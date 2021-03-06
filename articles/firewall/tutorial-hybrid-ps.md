---
title: Implementación y configuración de Azure Firewall en una red híbrida con Azure PowerShell
description: En este tutorial, aprenderá a implementar y configurar Azure Firewall mediante Azure Portal.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 919051a945d423a104b286e9c5703c5b749cf026
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2018
ms.locfileid: "46946466"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-in-a-hybrid-network-using-azure-powershell"></a>Tutorial: Implementación y configuración de Azure Firewall en una red híbrida con Azure PowerShell


En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Configuración del entorno de red
> * Configuración e implementación del firewall
> * Creación de las rutas
> * Creación de las máquinas virtuales
> * Probar el firewall

En este tutorial se crearán tres redes virtuales:
- **VNet-Hub**: el firewall está en esta red virtual.
- **VNet-Spoke**: la red virtual Spoke representa la carga de trabajo ubicada en Azure.
- **VNet-Onprem**: la red virtual Onprem representa una red local. En una implementación real, puede estar conectado por una conexión ExpressRoute o VPN. Para simplificar, este tutorial usa una conexión de puerta de enlace de VPN y una red virtual ubicada en Azure para representar una red local.

![Firewall en una red híbrida](media/tutorial-hybrid-ps/hybrid-network-firewall.png)

## <a name="key-requirements"></a>Requisitos principales

Hay tres requisitos clave para que este escenario funcione correctamente:

- Una ruta definida por el usuario en la subred de radio que apunte a la dirección IP de Azure Firewall como puerta de enlace predeterminada. La propagación de las rutas BGP debe **deshabilitarse** en esta tabla de rutas.
- Una ruta definida por el usuario en la subred de la puerta de enlace de concentrador debe apuntar a la dirección IP del firewall como próximo salto para las redes de radio.
- No se requiere ninguna ruta definida por el usuario en la subred de Azure Firewall, ya que obtiene las rutas de BGP.
- Asegúrese de establecer **AllowGatewayTransit** al emparejar VNet-Hub con VNet-Spoke y **UseRemoteGateways** al emparejar VNet-Spoke con VNet-Hub.

Consulte la sección [Creación de rutas](#create-routes) en este tutorial para ver cómo se crean estas rutas.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

## <a name="declare-the-variables"></a>Declaración de las variables
En el ejemplo siguiente se declaran las variables con los valores para este tutorial. En la mayoría de los casos, deberá reemplazar los valores por los suyos propios. No obstante, puede usar estas variables si está practicando los pasos para familiarizarse con este tipo de configuración. Si es necesario, modifique las variables y después cópielas y péguelas en la consola de PowerShell.

```azurepowershell
$RG1 = "FW-Hybrid-Test"
$Location1 = "East US"

# Variables for the firewall hub VNet

$VNetnameHub = "VNet-hub"
$SNnameHub = "AzureFirewallSubnet"
$VNetHubPrefix = "10.5.0.0/16"
$SNHubPrefix = "10.5.0.0/24"
$SNGWHubPrefix = "10.5.1.0/24"
$GWHubName = "GW-hub"
$GWHubpipName = "VNet-hub-GW-pip"
$GWIPconfNameHub = "GW-ipconf-hub"
$ConnectionNameHub = "hub-to-Onprem"

# Variables for the spoke VNet

$VnetNameSpoke = "VNet-Spoke"
$SNnameSpoke = "SN-Workload"
$VNetSpokePrefix = "10.6.0.0/16"
$SNSpokePrefix = "10.6.0.0/24"
$SNSpokeGWPrefix = "10.6.1.0/24"

# Variables for the OnPrem VNet

$VNetnameOnprem = "Vnet-Onprem"
$SNNameOnprem = "SN-Corp"
$VNetOnpremPrefix = "192.168.0.0/16"
$SNOnpremPrefix = "192.168.1.0/24"
$SNGWOnpremPrefix = "192.168.2.0/24"
$GWOnpremName = "GW-Onprem"
$GWIPconfNameOnprem = "GW-ipconf-Onprem"
$ConnectionNameOnprem = "Onprem-to-hub"
$GWOnprempipName = "VNet-Onprem-GW-pip"

$SNnameGW = "GatewaySubnet"
```

## <a name="create-a-resource-group"></a>Crear un grupo de recursos
Cree un grupo de recursos en el que se incluirán todos los recursos necesarios para este tutorial:

```azurepowershell
  New-AzureRmResourceGroup -Name $RG1 -Location $Location1
  ```

## <a name="create-and-configure-the-firewall-hub-vnet"></a>Creación y configuración de la red virtual del concentrador de firewall

Defina las subredes que se incluirán en la red virtual:

```azurepowershell
$FWsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameHub -AddressPrefix $SNHubPrefix
$GWsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWHubPrefix
```

Ahora, cree la red virtual del concentrador de firewall:

```azurepowershell
$VNetHub = New-AzureRmVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetHubPrefix -Subnet $FWsub,$GWsub
```
Solicite que se asigne una dirección IP pública a la puerta de enlace de VPN que creará para la red virtual. Observe que *AllocationMethod* es **Dynamic**. No puede especificar la dirección IP que desea usar. Se asigna dinámicamente a la puerta de enlace de VPN. 

  ```azurepowershell
  $gwpip1 = New-AzureRmPublicIpAddress -Name $GWHubpipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```
## <a name="create-and-configure-the-spoke-vnet"></a>Creación y configuración de la red virtual de radio

Defina las subredes que se incluirán en la red virtual de radio:

```azurepowershell
$Spokesub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameSpoke -AddressPrefix $SNSpokePrefix
$GWsubSpoke = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNSpokeGWPrefix
```

Cree la red virtual de radio:

```azurepowershell
$VNetSpoke = New-AzureRmVirtualNetwork -Name $VnetNameSpoke -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetSpokePrefix -Subnet $Spokesub,$GWsubSpoke
```

## <a name="configure-and-deploy-the-firewall"></a>Configuración e implementación del firewall

Ahora, implemente el firewall en la red virtual de concentrador.

```azurepowershell
# Get a Public IP for the firewall
$FWpip = New-AzureRmPublicIpAddress -Name "fw-pip" -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Static -Sku Standard
# Create the firewall
$Azfw = New-AzureRmFirewall -Name AzFW01 -ResourceGroupName $RG1 -Location $Location1 -VirtualNetworkName $VNetnameHub -PublicIpName fw-pip

#Save the firewall private IP address for future use

$AzfwPrivateIP = $Azfw.IpConfigurations.privateipaddress
$AzfwPrivateIP

```

### <a name="configure-network-rules"></a>Configuración de reglas de red

```azurepowershell
$Rule1 = New-AzureRmFirewallNetworkRule -Name "AllowWeb" -Protocol TCP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort 80
$Rule2 = New-AzureRmFirewallNetworkRule -Name "AllowPing" -Protocol ICMP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort *
$Rule3 = New-AzureRmFirewallNetworkRule -Name "AllowRDP" -Protocol TCP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort 3389

$NetRuleCollection = New-AzureRmFirewallNetworkRuleCollection -Name RCNet01 -Priority 100 `
   -Rule $Rule1,$Rule2,$Rule3 -ActionType "Allow"
$Azfw.NetworkRuleCollections = $NetRuleCollection
Set-AzureRmFirewall -AzureFirewall $Azfw
```
### <a name="configure-an-application-rule"></a>Configuración de una regla de aplicación

```azurepowershell
$Rule4 = New-AzureRmFirewallApplicationRule -Name "AllowBing" -Protocol "Http:80","Https:443" `
   -SourceAddress $SNOnpremPrefix -TargetFqdn "bing.com"

$AppRuleCollection = New-AzureRmFirewallApplicationRuleCollection -Name RCApp01 -Priority 100 `
   -Rule $Rule4 -ActionType "Allow"
$Azfw.ApplicationRuleCollections = $AppRuleCollection
Set-AzureRmFirewall -AzureFirewall $Azfw

```

## <a name="create-and-connect-the-vpn-gateways"></a>Creación y conexión de las puertas de enlace de VPN

Las redes virtuales de concentrador y local se conectan mediante una puerta de enlace de VPN.

### <a name="create-a-vpn-gateway-for-the-hub-vnet"></a>Creación de una puerta de enlace de VPN para la red virtual de concentrador

Establezca la configuración de la puerta de enlace de VPN. La configuración de la puerta de enlace de VPN define la subred y la dirección IP pública.

  ```azurepowershell
  $vnet1 = Get-AzureRmVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1
  $subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
  $gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfNameHub `
  -Subnet $subnet1 -PublicIpAddress $gwpip1
  ```

Ahora, cree la puerta de enlace de VPN para la red virtual de concentrador. Las configuraciones VNet a VNet requieren un VpnType RouteBased. La creación de una puerta de enlace de VPN suele tardar 45 minutos o más, según la SKU de la puerta de enlace de VPN seleccionada.

```azurepowershell
New-AzureRmVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```

### <a name="create-a-vpn-gateway-for-the-onprem-vnet"></a>Creación de una puerta de enlace de VPN para la red virtual local

Establezca la configuración de la puerta de enlace de VPN. La configuración de la puerta de enlace de VPN define la subred y la dirección IP pública.

  ```azurepowershell
$vnet2 = Get-AzureRmVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1
$subnet2 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gwipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfNameOnprem `
  -Subnet $subnet2 -PublicIpAddress $gwOnprempip
  ```

Ahora, cree la puerta de enlace de VPN para la red virtual local. Las configuraciones VNet a VNet requieren un VpnType RouteBased. La creación de una puerta de enlace de VPN suele tardar 45 minutos o más, según la SKU de la puerta de enlace de VPN seleccionada.

```azurepowershell
New-AzureRmVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf2 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```
### <a name="create-the-vpn-connections"></a>Creación de las conexiones
Ahora puede crear las conexiones de VPN entre las puertas de enlace de concentrador y local.

#### <a name="get-the-vpn-gateways"></a>Obtención de las puertas de enlace de VPN
```azurepowershell
$vnetHubgw = Get-AzureRmVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1
$vnetOnpremgw = Get-AzureRmVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1
```

#### <a name="create-the-connections"></a>Crear las conexiones

En este paso, cree la conexión desde la red virtual de concentrador a la red virtual local. Verá una clave compartida a la que se hace referencia en los ejemplos. Puede utilizar sus propios valores para la clave compartida. Lo importante es que la clave compartida coincida en ambas conexiones. Se tardará unos momentos en terminar de crear la conexión.

```azurepowershell
New-AzureRmVirtualNetworkGatewayConnection -Name $ConnectionNameHub -ResourceGroupName $RG1 `
-VirtualNetworkGateway1 $vnetHubgw -VirtualNetworkGateway2 $vnetOnpremgw -Location $Location1 `
-ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
```
Cree la conexión entre la red virtual local y la red virtual de concentrador. Este paso es similar al anterior, excepto que se crea una conexión desde la red virtual local a la red virtual de concentrador. Asegúrese de que coincidan las claves compartidas. Después de unos minutos, se habrá establecido la conexión.

  ```azurepowershell
  New-AzureRmVirtualNetworkGatewayConnection -Name $ConnectionNameOnprem -ResourceGroupName $RG1 `
  -VirtualNetworkGateway1 $vnetOnpremgw -VirtualNetworkGateway2 $vnetHubgw -Location $Location1 `
  -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
  ```
#### <a name="verify-the-connection"></a>Comprobación de la conexión
 
Para comprobar que la conexión se realizó correctamente, use el cmdlet *Get-AzureRmVirtualNetworkGatewayConnection*, con o sin *-Debug*. Puede usar el siguiente ejemplo de cmdlet, configurando los valores para que coincidan con los tuyos. Cuando se le pida, seleccione **A** para ejecutar **Todo**. En el ejemplo, *-Name* es el nombre de la conexión que desea probar.

```azurepowershell
Get-AzureRmVirtualNetworkGatewayConnection -Name $ConnectionNameHub -ResourceGroupName $RG1
```

Cuando el cmdlet termine, consulte los valores. En el ejemplo siguiente, el estado de conexión aparece como *Connected* (Conectado) y pueden verse los bytes de entrada y salida.

```
"connectionStatus": "Connected",
"ingressBytesTransferred": 33509044,
"egressBytesTransferred": 4142431
```

## <a name="create-and-configure-the-onprem-vnet"></a>Creación y configuración de la red virtual local

Defina las subredes que se incluirán en la red virtual:

```azurepowershell
$Onpremsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNNameOnprem -AddressPrefix $SNOnpremPrefix
$GWOnpremsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWOnpremPrefix
```

Ahora, cree la red virtual local:

```azurepowershell
$VNetOnprem = New-AzureRmVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetOnpremPrefix -Subnet $Onpremsub,$GWOnpremsub
```
Solicite que se asigne una dirección IP pública a la puerta de enlace que creará para la red virtual. Observe que *AllocationMethod* es **Dynamic**. No puede especificar la dirección IP que desea usar. Se asigna dinámicamente a la puerta de enlace. 

  ```azurepowershell
  $gwOnprempip = New-AzureRmPublicIpAddress -Name $GWOnprempipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```

## <a name="peer-the-hub-and-spoke-vnets"></a>Emparejamiento de las redes virtuales de concentrador y de radio

Ahora, empareje las redes virtuales de concentrador y de radio.

```azurepowershell
# Peer hub to spoke
Add-AzureRmVirtualNetworkPeering -Name HubtoSpoke -VirtualNetwork $VNetHub -RemoteVirtualNetworkId $VNetSpoke.Id -AllowGatewayTransit

# Peer spoke to hub
Add-AzureRmVirtualNetworkPeering -Name SpoketoHub -VirtualNetwork $VNetSpoke -RemoteVirtualNetworkId $VNetHub.Id -AllowForwardedTraffic -UseRemoteGateways
```
## <a name="create-routes"></a>Creación de rutas

A continuación, cree un par de rutas: 
- Una ruta desde la subred de puerta de enlace de concentrador a la subred de radio mediante la dirección IP del firewall.
- Una ruta predeterminada desde la subred de radio mediante la dirección IP del firewall.

```azurepowershell
#Create a route table
$routeTableHubSpoke = New-AzureRmRouteTable `
  -Name 'UDR-Hub-Spoke' `
  -ResourceGroupName $RG1 `
  -location $Location1

#Create a route
Get-AzureRmRouteTable `
  -ResourceGroupName $RG1 `
  -Name UDR-Hub-Spoke `
  | Add-AzureRmRouteConfig `
  -Name "ToSpoke" `
  -AddressPrefix $VNetSpokePrefix `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzureRmRouteTable

#Associate the route table to the subnet

Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $VNetHub `
  -Name $SNnameGW `
  -AddressPrefix $SNGWHubPrefix `
  -RouteTable $routeTableHubSpoke | `
Set-AzureRmVirtualNetwork

#Now create the default route

#Create a table, with BGP route propagation disabled
$routeTableSpokeDG = New-AzureRmRouteTable `
  -Name 'UDR-DG' `
  -ResourceGroupName $RG1 `
  -location $Location1 `
  -DisableBgpRoutePropagation

#Create a route
Get-AzureRmRouteTable `
  -ResourceGroupName $RG1 `
  -Name UDR-DG `
  | Add-AzureRmRouteConfig `
  -Name "ToSpoke" `
  -AddressPrefix 0.0.0.0/0 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzureRmRouteTable

#Associate the route table to the subnet

Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $VNetSpoke `
  -Name $SNnameSpoke `
  -AddressPrefix $SNSpokePrefix `
  -RouteTable $routeTableSpokeDG | `
Set-AzureRmVirtualNetwork
```
## <a name="create-virtual-machines"></a>Creación de máquinas virtuales

Ahora, cree las máquinas virtuales de local y de cargas de trabajo de radio, y colóquelas en las subredes adecuadas.

### <a name="create-the-workload-virtual-machine"></a>Creación de la máquina virtual de cargas de trabajo
Cree una máquina virtual en la red virtual de radio, que ejecute IIS, sin ninguna dirección IP pública y que permita que se haga ping en ella.
Cuando se le solicite, escriba el nombre de usuario y la contraseña de la máquina virtual.

```azurepowershell
# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name Allow-RDP  -Protocol Tcp `
  -Direction Inbound -Priority 200 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix $SNSpokePrefix -DestinationPortRange 3389 -Access Allow
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name Allow-web  -Protocol Tcp `
  -Direction Inbound -Priority 202 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix $SNSpokePrefix -DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $RG1 -Location $Location1 -Name NSG-Spoke02 -SecurityRules $nsgRuleRDP,$nsgRuleWeb

#Create the NIC
$NIC = New-AzureRmNetworkInterface -Name spoke-01 -ResourceGroupName $RG1 -Location $Location1 -SubnetId $VnetSpoke.Subnets[0].Id -NetworkSecurityGroupId $nsg.Id

#Define the virtual machine
$VirtualMachine = New-AzureRmVMConfig -VMName VM-Spoke-01 -VMSize "Standard_DS2"
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName Spoke-01 -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2016-Datacenter' -Version latest

#Create the virtual machine
New-AzureRmVM -ResourceGroupName $RG1 -Location $Location1 -VM $VirtualMachine -Verbose

#Install IIS on the VM
Set-AzureRmVMExtension `
    -ResourceGroupName $RG1 `
    -ExtensionName IIS `
    -VMName VM-Spoke-01 `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server"}' `
    -Location $Location1

#Create a host firewall rule to allow ping in
Set-AzureRmVMExtension `
    -ResourceGroupName $RG1 `
    -ExtensionName IIS `
    -VMName VM-Spoke-01 `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4"}' `
    -Location $Location1
```

### <a name="create-the-onprem-virtual-machine"></a>Creación de la máquina virtual local
Se trata de una máquina virtual sencilla con una dirección IP pública a la que se puede conectar mediante Escritorio remoto. Desde allí, puede conectarse al servidor local a través del firewall. Cuando se le solicite, escriba el nombre de usuario y la contraseña de la máquina virtual.
```azurepowershell
New-AzureRmVm `
    -ResourceGroupName $RG1 `
    -Name "VM-Onprem" `
    -Location $Location1 `
    -VirtualNetworkName $VNetnameOnprem `
    -SubnetName $SNNameOnprem `
    -OpenPorts 3389 `
    -Size "Standard_DS2"
```

## <a name="test-the-firewall"></a>Probar el firewall
En primer lugar, obtenga y anote la dirección IP privada de la máquina virtual **VM-spoke-01**.

```azurepowershell
$NIC.IpConfigurations.privateipaddress
```

1. En Azure Portal, conéctese a la máquina virtual **VM-Onprem**.
2. Abra un símbolo del sistema de Windows PowerShell en **VM-Onprem** y haga ping a la dirección IP privada de **VM-spoke-01**.

   Obtendrá una respuesta.
1. Abra un explorador web en **VM-Onprem** y vaya a http://\<VM-spoke-01 private IP\>.

   Verá la página predeterminada de Internet Information Services.

3. En **VM-Onprem**, abra un escritorio remoto a **VM-spoke-01** en la dirección IP privada.

   Se realizará la conexión y podrá iniciar sesión con el nombre de usuario y la contraseña elegidos.

Con ello, ha comprobado que funcionan las reglas de firewall:

- Puede hacer ping al servidor en la red virtual de radio.
- Puede examinar el servidor web en la red virtual de radio.
- Puede conectarse al servidor en la red virtual de radio mediante RDP.

A continuación, cambie la acción de recopilación de la regla de red del firewall a **Deny** (Denegar) para comprobar que las reglas del firewall funcionan según lo previsto. Ejecute el siguiente script para cambiar la acción de recopilación de la regla a **Deny** (Denegar).

```azurepowershell
$rcNet = $azfw.GetNetworkRuleCollectionByName("RCNet01")
$rcNet.action.type = "Deny"

$rcApp = $azfw.GetApplicationRuleCollectionByName("RCApp01")
$rcApp.action.type = "Deny"

Set-AzureRmFirewall -AzureFirewall $azfw
```
Ahora, ejecute de nuevo las pruebas. Esta vez, todas producirán un error. Cierre los escritorios remotos existentes antes de probar las reglas modificadas.

## <a name="clean-up-resources"></a>Limpieza de recursos

Puede conservar los recursos relacionados con el firewall para el siguiente tutorial o, si ya no los necesita, eliminar el grupo de recursos **FW-Hybrid-Test** para eliminarlos todos.


## <a name="next-steps"></a>Pasos siguientes

En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * Configuración del entorno de red
> * Configuración e implementación del firewall
> * Creación de las rutas
> * Creación de las máquinas virtuales
> * Probar el firewall

A continuación, puede supervisar los registros de Azure Firewall.

> [!div class="nextstepaction"]
> [Tutorial: Supervisión de los registros de Azure Firewall](./tutorial-diagnostics.md)
