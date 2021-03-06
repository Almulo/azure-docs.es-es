---
title: Creación de Integration Runtime autohospedado en Azure Data Factory | Microsoft Docs
description: Aprenda a crear un entorno Integration Runtime autohospedado en Azure Data Factory, lo que permite a las factorías de datos tener acceso a almacenes de datos en una red privada.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: abnarain
ms.openlocfilehash: af7d4253b4e631295f8c04c96601e932595af9c1
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/05/2018
ms.locfileid: "43697771"
---
# <a name="how-to-create-and-configure-self-hosted-integration-runtime"></a>Creación y configuración de una instancia de Integration Runtime autohospedado
Integration Runtime es la infraestructura de proceso que usa Azure Data Factory para proporcionar capacidades de integración de datos en distintos entornos de red. Para más información acerca del entorno de ejecución de integración, consulte [Introducción a Integration Runtime](concepts-integration-runtime.md).

Un entorno de ejecución de integración autohospedado puede ejecutar las actividades de copia entre almacenes de datos en la nube y un almacén de datos en una red privada y distribuir las actividades de transformación entre recursos de proceso en una instancia de Azure Virtual Network o una red local. Integration Runtime autohospedado debe instalarse en un equipo local o en una máquina virtual dentro de una red privada.  

En este documento se muestra cómo crear y configurar una instancia de Integration Runtime autohospedado.

## <a name="high-level-steps-to-install-self-hosted-ir"></a>Pasos detallados para instalar Integration Runtime autohospedado
1. Cree un entorno Integration Runtime autohospedado. Puede usar la interfaz de usuario de ADF para crear el entorno de ejecución de integración autohospedado. Este es un ejemplo con PowerShell:

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $resouceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
2. Descargue e instale una instancia de Integration Runtime autohospedado (en el equipo local).
3. Recupere la clave de autenticación y registre Integration Runtime autohospedado con la clave. Este es un ejemplo con PowerShell:

    ```powershell
    Get-AzureRmDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resouceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime.  
    ```

## <a name="setting-up-self-hosted-ir-on-azure-vm-using-azure-resource-manager-template-automatation"></a>Configuración del entorno de ejecución de integración autohospedado en una máquina virtual de Azure mediante una plantilla de Azure Resource Manager (automatización)
Puede automatizar la configuración del entorno de ejecución de integración autohospedado en una máquina virtual de Azure mediante [esta plantilla de Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vms-with-selfhost-integration-runtime). Esto proporciona una manera fácil de tener un entorno de ejecución de integración autohospedado completamente funcional dentro de la red virtual de Azure con la característica de escalabilidad y alta disponibilidad (siempre y cuando establezca el número de nodos para que sea 2 o superior).

## <a name="command-flow-and-data-flow"></a>Flujo de comandos y flujo de datos
Cuando mueve los datos entre un entorno local y la nube, la actividad utiliza un entorno de ejecución de integración autohospedado para transferir los datos desde el origen de datos local a la nube, y viceversa.

A continuación se muestra el flujo de datos de alto nivel y el resumen de los pasos para copiar con Integration Runtime autohospedado:

![Información general de alto nivel](media\create-self-hosted-integration-runtime\high-level-overview.png)

1. El desarrollador de datos crea un entorno de ejecución de integración autohospedado dentro de una factoría de datos de Azure mediante un cmdlet de PowerShell. Actualmente, Azure Portal no admite esta característica.
2. Un desarrollador de datos crea un servicio vinculado para un almacén de datos local mediante la especificación de la instancia de Integration Runtime autohospedado que debe usar para conectarse a los almacenes de datos. Como parte de la configuración del servicio vinculado, el desarrollador de datos usa la aplicación Credential Manager para especificar las credenciales y los tipos de autenticación. El cuadro de diálogo de la aplicación Credential Manager se comunica con el almacén de datos para probar la conexión y el entorno Integration Runtime autohospedado para guardar las credenciales.
   - El nodo de Integration Runtime autohospedado cifra la credencial mediante la interfaz de programación de aplicaciones de protección de datos de Windows (DPAPI) y lo guarda localmente. Si se establecen varios nodos para la alta disponibilidad, las credenciales se sincronizan de nuevo en otros nodos. Cada nodo las cifra mediante DPAPI y las almacena localmente. La sincronización de credenciales es transparente para el desarrollador de datos y se controla por parte de Integration Runtime autohospedado.    
   - El servicio Data Factory se comunica con la instancia de Integration Runtime autohospedado para la programación y administración de trabajos a través de un **canal de control** que usa una cola de Azure Service Bus compartida. Cuando es necesario ejecutar un trabajo de actividad, Data Factory pone en cola la solicitud junto con cualquier información de credenciales (en caso de que las credenciales no estén ya almacenadas en el entorno Integration Runtime autohospedado). La instancia de Integration Runtime autohospedado inicia el trabajo después de sondear la cola.
   - El entorno Integration Runtime autohospedado copia datos de un almacén local a un almacenamiento en nube, o viceversa, en función de cómo esté configurada la actividad de copia en la canalización de datos. En este paso, el entorno Integration Runtime autohospedado se comunica directamente con servicios de almacenamiento basados en la nube, como Azure Blob Storage, a través de un canal seguro (HTTPS).

## <a name="considerations-for-using-self-hosted-ir"></a>Consideraciones a la hora de usar Integration Runtime autohospedado

- Es posible utilizar un solo entorno de Integration Runtime autohospedado para varios orígenes de datos locales. Sin embargo, **estará asociado a una única factoría de datos de Azure** y no podrá compartirse con otra factoría de datos.
- Puede tener **solo una instancia del entorno Integration Runtime autohospedado** instalada en un equipo. Supongamos que tiene dos factorías de datos que necesitan acceder a orígenes de datos locales: tendrá que instalar un entorno Integration Runtime autohospedado en dos equipos locales. En otras palabras, un entorno Integration Runtime autohospedado está asociado a una factoría de datos determinada.
- El **entorno Integration Runtime autohospedado no tiene que estar en la misma máquina que el origen de datos**. Sin embargo, cuanto más se acerque el entorno Integration Runtime autohospedado al origen de datos, menos tiempo necesitará para conectarse a este. Le recomendamos que instale el entorno Integration Runtime autohospedado en una máquina diferente de la que hospeda el origen de datos local. Si el entorno Integration Runtime autohospedado y el origen de datos están en equipos diferentes, el entorno Integration Runtime autohospedado no compite por los recursos con el origen de datos.
- Puede tener **varios entornos Integration Runtime autohospedados en diferentes máquinas conectándose al mismo origen de datos local**. Por ejemplo, puede que tenga dos entornos Integration Runtime autohospedado que atienden a dos factorías de datos pero el mismo origen de datos local está registrado con las dos factorías de datos.
- Si ya tiene una puerta de enlace instalada en el equipo que atiende a un escenario de **Power BI**, instale un **entorno Integration Runtime autohospedado independiente para Azure Data Factory** en otra máquina.
- El entorno de ejecución de integración autohospedado debe usarse para admitir la integración de datos dentro de Azure Virtual Network.
- Considere el origen de datos como uno de tipo local (que está detrás de un firewall), aunque utilice **ExpressRoute**. Use el entorno de ejecución de integración autohospedado para establecer la conectividad entre el servicio y el origen de datos.
- Debe utilizar el entorno de ejecución de integración autohospedado incluso si el almacén de datos está en la nube en una **máquina virtual IaaS de Azure**.
- Las tareas pueden generar error en un entorno de ejecución de integración autohospedado instalada en un equipo Windows Server en el que está habilitado el cifrado compatible con FIPS. Para solucionar este problema, deshabilite el cifrado compatible con FIPS en el servidor. Para deshabilitar el cifrado compatible con FIPS, cambie el valor del Registro siguiente de 1 (habilitado) a 0 (deshabilitado): `HKLM\System\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy\Enabled`.

## <a name="prerequisites"></a>requisitos previos

- Las versiones de **sistema operativo** compatibles son Windows 7 Service Pack 1, Windows 8.1, Windows 10, Windows Server 2008 R2 SP1, Windows Server 2012, Windows Server 2012 R2 y Windows Server 2016. No se admite la instalación del entorno de ejecución de integración autohospedado en un **controlador de dominio**.
- Es necesario **.NET Framework 4.6.1 o posterior**. Si está instalando el entorno de ejecución de integración autohospedado en una máquina con Windows 7, instale .NET Framework 4.6.1 o posterior. Consulte [Requisitos de sistema de .NET Framework](/dotnet/framework/get-started/system-requirements) para más información.
- La **configuración** recomendada de la máquina del entorno de ejecución de integración autohospedado es de al menos 2 GHz, 4 núcleos, 8 GB de RAM y disco de 80 GB.
- Si la máquina host está en hibernación, el entorno de ejecución de integración autohospedado no responde a las solicitudes de datos. Por tanto, configure un plan de energía adecuado en el equipo antes de instalar el entorno Integration Runtime autohospedado. Si la máquina está configurada para hibernar, se mostrará un mensaje durante la instalación del entorno Integration Runtime autohospedado.
- Debe ser administrador del equipo para instalar y configurar correctamente el entorno de ejecución de integración autohospedado.
- Puesto que las ejecuciones de la actividad de copia suceden con una frecuencia determinada, el uso de recursos (CPU, memoria) en la máquina también sigue el mismo patrón, con horas pico y tiempos de inactividad. El uso de recursos también depende en gran medida de la cantidad de datos que se mueven. Cuando hay varios trabajos de copia en curso, puede ver que el uso de los recursos aumenta durante las horas pico.

## <a name="installation-best-practices"></a>Procedimientos recomendados de instalación
El entorno de ejecución de integración autohospedado puede instalarse mediante la descarga de un paquete de instalación MSI desde el [Centro de descarga de Microsoft](https://www.microsoft.com/download/details.aspx?id=39717). Consulte el artículo [Movimiento de datos entre orígenes locales y la nube con Data Management Gateway](tutorial-hybrid-copy-powershell.md) para obtener instrucciones detalladas.

- Configure el plan de energía en el equipo host para el entorno Integration Runtime autohospedado, de forma que el equipo no hiberne. Si la máquina host está en hibernación, el entorno Integration Runtime autohospedado se pone en modo sin conexión.
- Realice regularmente una copia de las credenciales asociadas con el entorno Integration Runtime autohospedado.

## <a name="install-and-register-self-hosted-ir-from-download-center"></a>Instalación y registro del entorno Integration Runtime autohospedado desde el Centro de descarga

1. Vaya a la [página de descarga del entorno de ejecución de integración autohospedado](https://www.microsoft.com/download/details.aspx?id=39717).
2. Haga clic en **Descargar**, seleccione la versión apropiada (**32 bits** o **64 bits**) y haga clic en **Siguiente**.
3. Ejecute el **MSI** directamente o guárdelo en el disco duro para ejecutarlo más adelante.
4. En la página **principal**, seleccione un **idioma** y haga clic en **Siguiente**.
5. **Acepte** el contrato de licencia para el usuario final y haga clic en **Siguiente**.
6. Seleccione **carpeta** para instalar el entorno de ejecución de integración autohospedado y haga clic en **Siguiente**.
7. En la página **Preparado para instalar...**, haga clic en **Instalar**.
8. Haga clic en **Finalizar** para completar la instalación.
9. Obtención de la clave de autenticación con Azure PowerShell. Ejemplo de PowerShell para recuperar la clave de autenticación:

    ```powershell
    Get-AzureRmDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resouceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```
11. En la página **Register Integration Runtime (Self-hosted)** (Registrar Integration Runtime autohospedado) del Administrador de configuración de Integration Runtime de Microsoft que se ejecuta en su máquina, realice estos pasos:
    1. Pegue la **clave de autenticación** en el área de texto.
    2. Si lo desea, haga clic en **Show authentication key** (Mostrar clave de autenticación) para ver el texto de la clave.
    3. Haga clic en **Registrar**.


## <a name="high-availability-and-scalability"></a>Alta disponibilidad y escalabilidad
Un entorno de ejecución de integración autohospedado puede asociarse a varias máquinas locales. Estas máquinas se llaman nodos. Puede tener hasta cuatro nodos asociados con un entorno de ejecución de integración autohospedado. Las ventajas de tener varios nodos (máquinas locales con la puerta de enlace instalada) para una puerta de enlace lógica son:
1. Mayor disponibilidad del entorno de ejecución de integración autohospedado para que ya no sea el único punto de error de la solución de Big Data o la integración de datos en la nube con Azure Data Factory, garantizando la continuidad con un máximo de 4 nodos.
2. Rendimiento mejorado durante el movimiento de datos entre almacenes de datos locales y en la nube. Obtenga más información sobre las [comparaciones de rendimiento](copy-activity-performance.md).

Para asociar varios nodos, basta con instalar el software Integration Runtime (autohospedado) desde el [Centro de descarga](https://www.microsoft.com/download/details.aspx?id=39717) y registrarlo con las claves de autenticación obtenidas con el cmdlet New-AzureRmDataFactoryV2IntegrationRuntimeKey, como se describe en el [tutorial](tutorial-hybrid-copy-powershell.md).

> [!NOTE]
> No es necesario crear un nuevo entorno de ejecución de integración autohospedado para asociar cada nodo. Puede instalar el entorno de ejecución de integración autohospedado en otra máquina y registrarlo con la misma clave de autenticación. 

> [!NOTE]
> Antes de agregar otro nodo para **High Availability and Scalability**, asegúrese de que la opción **"Remote access to intranet"** (Acceso remoto a la intranet) está **habilitada** en el primer nodo (Microsoft Integration Runtime Configuration Manager -> Configuración -> Remote access to intranet [Acceso remoto a la intranet]). 

### <a name="scale-considerations"></a>Consideraciones de escala

#### <a name="scale-out"></a>Escalado horizontal

Cuando la **memoria disponible en el entorno de ejecución de integración autohospedado es baja** y el **uso de CPU es alto**, agregar un nuevo nodo ayuda a escalar horizontalmente la carga en las máquinas. Si se producen errores en las actividades debido a tiempos de espera excedidos o a un nodo del entorno de ejecución de integración autohospedado sin conexión, será útil agregar un nodo a la puerta de enlace.

#### <a name="scale-up"></a>Escalado vertical

Cuando la memoria disponible y la CPU no se utilizan correctamente, pero la ejecución de trabajos simultáneos está alcanzando el límite, debe escalar verticalmente aumentando el número de trabajos simultáneos que se pueden ejecutar en un nodo. También puede escalar verticalmente cuando se agota el tiempo de espera de las actividades porque el entorno de ejecución de integración autohospedado está sobrecargado. Como se muestra en la siguiente imagen, puede aumentar la capacidad máxima para un nodo.  

![](media\create-self-hosted-integration-runtime\scale-up-self-hosted-IR.png)

### <a name="tlsssl-certificate-requirements"></a>Requisitos del certificado TLS/SSL

Estos son los requisitos para el certificado TLS/SSL que se usa para proteger las comunicaciones entre los nodos del entorno de ejecución de integración:

- El certificado debe ser un certificado de confianza pública X509 v3. Se recomienda que utilice certificados emitidos por una entidad de certificación pública (CA).
- Todos los nodos del entorno de ejecución de integración deben confiar en este certificado.
- Se admiten certificados comodín. Si el nombre FQDN es **node1.domain.contoso.com**, puede utilizar ***.domain.contoso.com** como nombre del firmante del certificado.
- No se recomienda usar certificados de SAN, ya que solo se utilizará el último elemento de los nombres alternativos del firmante y los demás se ignorarán debido a la limitación actual. Por ejemplo, tiene un certificado de SAN cuyos SAN son **node1.domain.contoso.com** y **node2.domain.contoso.com**, pero solo puede usar este certificado en el equipo cuyo FQDN es **node2.domain.contoso.com**.
- Se admite cualquier tamaño de clave compatible con Windows Server 2012 R2 para los certificados SSL.
- El certificado que usa claves CNG no es compatible.  

## <a name="sharing-the-self-hosted-integration-runtime-ir-with-multiple-data-factories"></a>Uso compartido del entorno de ejecución de integración autohospedado con varias factorías de datos.

Puede volver a usar una infraestructura de entorno de ejecución de integración autohospedado que ya haya configurado en una factoría de datos. Esto le permite crear un **entorno de ejecución de integración autohospedado vinculado** en una factoría de datos diferente haciendo referencia a otro entorno de ejecución de integración autohospedado ya existente (compartido).

#### <a name="terminologies"></a>**Terminología**

- **Entorno de ejecución de integración compartido**: el entorno de ejecución de integración autohospedado original que se ejecuta en una infraestructura física.  
- **Entorno de ejecución de integración vinculado**: el entorno de ejecución de integración que hace referencia a otro entorno compartido. Este es un entorno de ejecución de integración lógico que usa la infraestructura de otro entorno de ejecución de integración autohospedado (compartido).

#### <a name="high-level-steps-for-creating-a-linked-self-hosted-ir"></a>Pasos de alto nivel para la creación de un entorno de ejecución de integración autohospedado vinculado

En el entorno de ejecución de integración autohospedado que se va a compartir:

1. Conceda permiso a la instancia de Data Factory en la que le gustaría crear el entorno de ejecución de integración vinculado. 

   ![](media\create-self-hosted-integration-runtime\grant-permissions-IR-sharing.png)

   ![](media\create-self-hosted-integration-runtime\3_rbac_permissions.png)

2. Anote el **identificador de recurso** del entorno de ejecución de integración autohospedado que se va a compartir.

   ![](media\create-self-hosted-integration-runtime\4_ResourceID_self-hostedIR.png)

En la instancia de Data Factory a la que se concedieron los permisos:

3. Cree un nuevo entorno de ejecución de integración autohospedado (vinculado) y escriba el **identificador de recurso** anterior.

   ![](media\create-self-hosted-integration-runtime\6_create-linkedIR_2.png)

   ![](media\create-self-hosted-integration-runtime\6_create-linkedIR_3.png)

#### <a name="monitoring"></a>Supervisión 

- **IR compartido**

  ![](media\create-self-hosted-integration-runtime\Contoso-shared-IR.png)

  ![](media\create-self-hosted-integration-runtime\contoso-shared-ir-monitoring.png)

- **IR vinculado**

  ![](media\create-self-hosted-integration-runtime\Contoso-linked-ir.png)

  ![](media\create-self-hosted-integration-runtime\Contoso-linked-ir-monitoring.png)

#### <a name="known-limitations-of-self-hosted-ir-sharing"></a>Limitaciones conocidas del uso compartido de un entorno de ejecución de integración autohospedado

1. El número predeterminado de entornos de ejecución de integración que se puede crear en un único entorno de ejecución de integración autohospedado es **20**. Si necesita más, póngase en contacto con el soporte técnico. 

2. En la factoría de datos en la que se va a crear el entorno de ejecución de integración debe tener una instancia de MSI ([Managed Service Identity](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)). De forma predeterminada, las factorías de datos que se crean en el portal de Ibiza o mediante los cmdlets de PowerShell incluyen una MSI creada implícitamente. Sin embargo, en algunos casos, cuando la factoría de datos se crea mediante una plantilla de Azure Resource Manager o un SDK, la propiedad "**identidad**" **se debe establecer** explícitamente para asegurarse de que Azure Resource Manager crea una factoría de datos que contiene una MSI. 

3. La versión del entorno de ejecución de integración autohospedado debe ser igual o superior a la versión 3.8.xxxx.xx. [Descargue la versión más reciente](https://www.microsoft.com/download/details.aspx?id=39717) del entorno de ejecución de integración autohospedado

4. En la factoría de datos en la que se va a crear el entorno de ejecución de integración debe tener una instancia de MSI ([Managed Service Identity](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)). De forma predeterminada, las factorías de datos que se crean en el portal de Ibiza o mediante los cmdlets de PowerShell incluyen una MSI ([identidad de servicio administrada](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)).
cread implícitamente. Sin embargo, en el caso de las factorías de datos creadas con una plantilla de Azure Resource Manager (ARM) o un SDK, es necesario establecer la propiedad "Identity" para asegurarse de que se crea una MSI.

5. La versión de ADF SDK para .Net que admite esta característica es la versión >= 1.1.0

6. La versión de Azure PowerShell que admite esta característica es >= 6.6.0 (AzureRM.DataFactoryV2 >= 0.5.7)

7. Para conceder permiso, el usuario necesitará el rol "Propietario" o el rol "Propietario" heredado en la instancia de Data Factory donde exista el IR compartido. 

  > [!NOTE]
  > Esta característica solo está disponible en Azure Data Factory versión 2 

## <a name="system-tray-icons-notifications"></a>Notificaciones/iconos de la bandeja del sistema

Si mueve el cursor sobre el mensaje de notificación o el icono en la bandeja del sistema, verá detalles sobre el estado del entorno de ejecución de integración autohospedado.

![Notificaciones de la bandeja del sistema](media\create-self-hosted-integration-runtime\system-tray-notifications.png)

## <a name="ports-and-firewall"></a>Puertos y firewall
Existen dos firewalls que tiene que tener en cuenta: el **firewall corporativo**, que se ejecuta en el enrutador central de la organización, y **Firewall de Windows**, configurado como demonio en la máquina local en la que está instalado el entorno de ejecución de integración autohospedado.

![Firewall](media\create-self-hosted-integration-runtime\firewall.png)

En el **firewall corporativo**, debe configurar los siguientes dominios y puertos de salida:

Nombres de dominio | Puertos | DESCRIPCIÓN
------------ | ----- | ------------
*.servicebus.windows.net | 443 | Usado para la comunicación con el back-end del servicio de movimiento de datos
*.core.windows.net | 443 | Usado para la copia de almacenamiento provisional que usa el blob de Azure (si está configurado)
*.frontend.clouddatahub.net | 443 | Usado para la comunicación con el back-end del servicio de movimiento de datos
download.Microsoft.com | 443 | Usado para descargar las actualizaciones

En el nivel de **Firewall de Windows** (nivel de máquina), normalmente se habilitan estos puertos de salida. De lo contrario, puede configurar los puertos y dominios según corresponda en el equipo del entorno de ejecución de integración autohospedado.

> [!NOTE]
> En función del origen y los receptores, quizá deba agregar a la lista de permitidos del firewall corporativo o de Windows más dominios y puertos de salida.
>
> Para algunas bases de datos en la nube (por ejemplo: Azure SQL Database, Azure Data Lake, etc.), puede que necesite agregar a la lista de permitidos de la configuración del firewall la dirección IP de la máquina del entorno de ejecución de integración autohospedado.

### <a name="copy-data-from-a-source-to-a-sink"></a>Copia de datos desde un origen a un receptor
Asegúrese de que las reglas del firewall estén habilitadas correctamente en el firewall corporativo, en el Firewall de Windows de la máquina del entorno Integration Runtime autohospedado y en el propio almacén de datos. De este modo, el entorno de ejecución de integración autohospedado podrá conectarse al origen y al receptor correctamente. Habilite las reglas de cada almacén de datos que participe en la operación de copia.

Por ejemplo, para copiar información desde **un almacén de datos local a un receptor de Azure SQL Database o uno de Azure SQL Data Warehouse**, debe realizar los siguientes pasos:

- Permita la comunicación **TCP** saliente en el puerto **1433** para el Firewall de Windows y el corporativo.
- Establezca la configuración de firewall del servidor SQL de Azure para agregar la dirección IP de la máquina del entorno de ejecución de integración autohospedado a la lista de IP permitidas.

> [!NOTE]
> Si el firewall no permite el puerto de salida 1433, el entorno de ejecución de integración autohospedado no podrá tener acceso directamente a Azure SQL. En este caso, puede usar la [copia de almacenamiento provisional](copy-activity-performance.md) en SQL Azure Database y SQL Azure DW. En este escenario, requeriría solo HTTPS (puerto 443) para el movimiento de datos.


## <a name="proxy-server-considerations"></a>Consideraciones acerca del servidor proxy
Si en su entorno de red corporativo se usa un servidor proxy para acceder a Internet, configure el entorno de ejecución de integración autohospedado para utilizar la configuración de proxy adecuada. Puede establecer el proxy durante la fase de registro inicial.

![Especificación del servidor proxy](media\create-self-hosted-integration-runtime\specify-proxy.png)

El entorno Integration Runtime autohospedado utiliza el servidor proxy para conectarse al servicio en la nube. Haga clic en el vínculo Cambiar durante la configuración inicial. Verá el cuadro de diálogo de configuración de proxy.

![Establecimiento de proxy](media\create-self-hosted-integration-runtime\set-http-proxy.png)

Hay tres opciones de configuración:

- **No utilizar proxy**: el entorno de ejecución de integración autohospedado no usa expresamente ningún proxy para conectarse a servicios en la nube.
- **Usar proxy del sistema**: el entorno Integration Runtime autohospedado utiliza la configuración de proxy de diahost.exe.config y diawp.exe.config. Si no hay ningún proxy configurado en diahost.exe.config y diawp.exe.config, el entorno Integration Runtime autohospedado se conecta al servicio en la nube directamente sin pasar por el proxy.
- **Usar proxy personalizado**: establezca la configuración del proxy HTTP que se utilizará para el entorno de ejecución de integración autohospedado, en lugar de usar las configuraciones de diahost.exe.config y diawp.exe.config. La dirección y el puerto son obligatorios. El nombre de usuario y la contraseña son opcionales según la configuración de autenticación del proxy. Todas las configuraciones se cifran con DPAPI de Windows en el entorno de ejecución de integración autohospedado y se almacenan localmente en el equipo.

El servicio host de Integration Runtime se reinicia automáticamente después de guardar la configuración de proxy actualizada.

Cuando el entorno Integration Runtime autohospedado se haya registrado correctamente, si quiere ver o actualizar la configuración de proxy, utilice el Administrador de configuración de Integration Runtime.

1. Inicie el **Administrador de configuración de Microsoft Integration Runtime**.
   - Cambie a la pestaña **Configuración** .
   - Haga clic en el vínculo **Cambiar** de la sección **Proxy HTTP** para iniciar el cuadro de diálogo **Establecer proxy HTTP**.
   - Tras hacer clic en el botón **Siguiente** , verá un cuadro de diálogo de advertencia que solicita permiso para guardar la configuración de proxy y reiniciar el servicio host de Integration Runtime.

Puede ver y actualizar el proxy HTTP mediante la herramienta Administrador de configuración.

![Visualización de proxy](media\create-self-hosted-integration-runtime\view-proxy.png)

> [!NOTE]
> Si configura un servidor proxy con la autenticación NTLM, el servicio host de Integration Runtime se ejecutará con la cuenta de dominio. Si más adelante cambia la contraseña de dicha cuenta de dominio, no olvide actualizar la configuración del servicio y reiniciarlo en consecuencia. Debido a este requisito, se recomienda usar una cuenta de dominio específica para acceder al servidor proxy, que no requiere actualizar la contraseña con frecuencia.

### <a name="configure-proxy-server-settings"></a>Configuración de un servidor proxy

Si selecciona la opción **Usar proxy del sistema** para el proxy HTTP, el entorno de ejecución de integración autohospedado utilizará la configuración de proxy de diahost.exe.config y diawp.exe.config. Si no hay ningún proxy especificado en diahost.exe.config y diawp.exe.config, el entorno de ejecución de integración autohospedado se conecta al servicio en la nube directamente sin pasar por el proxy. En el procedimiento siguiente se proporcionan instrucciones para actualizar el archivo diahost.exe.config.

1. En el Explorador de archivos, cree una copia segura de C:\Archivos de programa\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config para realizar una copia de seguridad del archivo original.
2. Inicie Notepad.exe como administrador y abra el archivo de texto C:\Program Files\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config. Encontrará la etiqueta predeterminada para system.net como se muestra en el siguiente código:

    ```xml
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```
    Después, puede agregar los detalles del servidor proxy tal y como se muestran en el ejemplo siguiente:

    ```xml
    <system.net>
        <defaultProxy enabled="true">
              <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    Se permiten propiedades adicionales dentro de la etiqueta proxy para especificar la configuración requerida, como scriptLocation. Consulte [proxy (Elemento, Configuración de red)](https://msdn.microsoft.com/library/sa91de1e.aspx) para ver la sintaxis.

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
3. Guarde el archivo de configuración en la ubicación original; después, reinicie el servicio host de Integration Runtime autohospedado para aplicar los cambios. Para hacerlo, use el applet Servicios del Panel de control o, en el **Administrador de configuración de Integration Runtime**, haga clic en el botón **Detener servicio** y, luego, en **Iniciar servicio**. Si el servicio no se inicia, probablemente sea debido a que se ha agregado una sintaxis de etiqueta XML incorrecta en el archivo de configuración de aplicación que se ha modificado.

> [!IMPORTANT]
> No olvide actualizar ambos, diahost.exe.config y diawp.exe.config.

Además de estos puntos anteriores, también tiene que asegurarse de que Microsoft Azure se encuentra en la lista blanca de su compañía. Se puede descargar una lista de direcciones IP válidas de Microsoft Azure en el [Centro de descarga de Microsoft](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>Posibles síntomas de problemas relacionados con el firewall y el servidor proxy
Si se producen errores como los siguientes, es probable que se deban a una configuración incorrecta del servidor proxy o del firewall, que impide que el entorno de ejecución de integración autohospedado se conecte a Data Factory para autenticarse. Consulte la sección anterior para garantizar que el firewall y el servidor proxy están configurados correctamente.

1. Al intentar registrar el entorno de ejecución de integración autohospedado, recibe el siguiente error: "No se pudo registrar este nodo de Integration Runtime. Confirme que la clave de autenticación sea válida y que se esté ejecutando el servicio host de Integration Runtime en este equipo. "
   - Al abrir el Administrador de configuración de Integration Runtime, ve el estado "**Desconectado**" o "**Conectando**". Cuando se consultan los registros de eventos de Windows, en “Visor de eventos” > “Registros de aplicaciones y servicios” > “Microsoft Integration Runtime” aparecen mensajes de error como el siguiente:

    ```
    Unable to connect to the remote server
    A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
    ```

### <a name="enable-remote-access-from-intranet"></a>Activación del acceso remoto desde la intranet  
Si utiliza **PowerShell** o la aplicación **Administrador de credenciales** para cifrar credenciales desde otra máquina (de la red) diferente de la que tiene instalado el entorno de ejecución de integración autohospedado, la opción **"Remote Access from Intranet"** (Acceso remoto desde la intranet) debería estar habilitada. Si ejecuta **PowerShell** o la aplicación **Administrador de credenciales** para cifrar credenciales desde la misma máquina en la que está instalado el entorno de ejecución de integración autohospedado, es posible que la opción **"Remote Access from Intranet"** (Acceso remoto desde la intranet) no esté habilitada.

La opción "Remote Access from Intranet" (Acceso remoto desde la intranet) debe estar **habilitada** antes de agregar otro nodo para **High Availability and Scalability**.  

Durante la instalación del entorno de ejecución de integración autohospedado (versión 3.3.xxxx.x en adelante), de forma predeterminada, el entorno de ejecución de integración autohospedado deshabilita la opción **"Remote Access from Intranet"** (Acceso remoto desde la intranet) en la máquina de dicho entorno.

Si usa un firewall de terceros, puede abrir manualmente el puerto 8060 (o el puerto configurado por el usuario). Si se presenta un problema de firewall durante la instalación del entorno de ejecución de integración autohospedado, puede probar a usar el comando siguiente para instalarlo sin configurar el firewall.

```
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
```
> [!NOTE]
> La aplicación **Administrador de credenciales** aún no está disponible para cifrar credenciales en ADFv2. Esta compatibilidad se agregará más adelante.  

Si decide no abrir el puerto 8060 en la máquina del entorno de ejecución de integración autohospedado, use otros mecanismos que no sean la aplicación de **Configuración de credenciales** para configurar las credenciales del almacén de datos. Por ejemplo, puede usar el cmdlet de PowerShell New-AzureRmDataFactoryV2LinkedServiceEncryptCredential. Consulte la sección Configuración de credenciales y seguridad para obtener más información sobre cómo configurar las credenciales del almacén de datos.


## <a name="next-steps"></a>Pasos siguientes
Vea el tutorial siguiente para obtener instrucciones detalladas: [Tutorial: copy on-premises data to cloud](tutorial-hybrid-copy-powershell.md) (Tutorial: copia de datos locales en la nube).
