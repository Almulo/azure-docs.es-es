---
title: Uso de PowerShell para cambiar el tamaño de una máquina virtual Windows en Azure | Microsoft Docs
description: Cambie el tamaño de una máquina virtual Windows creada con el modelo de implementación de Resource Manager utilizando Azure PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 057ff274-6dad-415e-891c-58f8eea9ed78
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: cynthn
ms.openlocfilehash: 8854f694ce067aaa80a159430a9f48440bcdd95a
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/04/2018
ms.locfileid: "34701877"
---
# <a name="resize-a-windows-vm"></a>Cambio de tamaño de una máquina virtual Windows

En este artículo se muestra cómo mover una máquina virtual a otro [tamaño de máquina virtual](sizes.md) diferente con Azure PowerShell.

Después de crear una máquina virtual, puede escalarla o reducirla verticalmente cambiando su tamaño. En algunos casos, hay que desasignarla antes. Esto puede suceder si el nuevo tamaño no está disponible en el clúster de hardware que hospeda la actualmente la máquina virtual.

Si la máquina virtual usa Premium Storage, asegúrese de elegir una versión **s** del tamaño para obtener compatibilidad con este nivel de almacenamiento. Por ejemplo, elija Standard_E4**s**_v3 en lugar de Standard_E4_v3.

## <a name="resize-a-windows-vm-not-in-an-availability-set"></a>Cambio de tamaño de una máquina virtual Windows que no está en un conjunto de disponibilidad

Establezca algunas variables. Reemplace los valores por su propia información.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Muestre los tamaños de máquina virtual que están disponibles en el clúster de hardware donde se hospeda la máquina virtual. 
   
```powershell
Get-AzureRmVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Si se muestra el tamaño deseado, ejecute los comandos siguientes para cambiar el tamaño de la máquina virtual. Si el tamaño deseado no aparece, vaya al paso 3.
   
```powershell
$vm = Get-AzureRmVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMsize>"
Update-AzureRmVM -VM $vm -ResourceGroupName $resourceGroup
```

Si el tamaño deseado no se muestra, ejecute los siguientes comandos para desasignar la máquina virtual, cambiar su tamaño y reiniciarla. Sustituya **<newVMsize>** por el tamaño que desee.
   
```powershell
Stop-AzureRmVM -ResourceGroupName $resourceGroup -Name $vmName -Force
$vm = Get-AzureRmVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMSize>"
Update-AzureRmVM -VM $vm -ResourceGroupName $resourceGroup
Start-AzureRmVM -ResourceGroupName $resourceGroup -Name $vmName
```

> [!WARNING]
> Al desasignar la máquina virtual, se liberan todas las direcciones IP dinámicas asignadas a ella. Esto no afecta a los discos del SO y de datos. 
> 
> 

## <a name="resize-a-windows-vm-in-an-availability-set"></a>Cambio de tamaño de una máquina virtual Windows que está en un conjunto de disponibilidad

Si el nuevo tamaño de una máquina virtual de un conjunto de disponibilidad no está disponible en el clúster de hardware que hospeda la máquina virtual, habrá que desasignar todas las máquinas virtuales del conjunto de disponibilidad para cambiar el tamaño de la máquina virtual. También tendrá que actualizar el tamaño de otras máquinas virtuales del conjunto de disponibilidad después de cambiar el tamaño de una máquina virtual. Para cambiar el tamaño de una máquina virtual de un conjunto de disponibilidad, siga estos pasos.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Muestre los tamaños de máquina virtual que están disponibles en el clúster de hardware donde se hospeda la máquina virtual. 
   
```powershell
Get-AzureRmVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Si se muestra el tamaño deseado, ejecute el comando siguiente para cambiar el tamaño de la máquina virtual. Si no aparece, vaya a la sección siguiente.
   
```powershell
$vm = Get-AzureRmVM -ResourceGroupName $resourceGroup -VMName $vmName 
$vm.HardwareProfile.VmSize = "<newVmSize>"
Update-AzureRmVM -VM $vm -ResourceGroupName $resourceGroup
```
    
Si el tamaño deseado no aparece, continúe con los pasos siguientes para desasignar todas las máquinas virtuales del conjunto de disponibilidad, cambiar su tamaño y reiniciarlas.

Detenga todas las máquinas virtuales del conjunto de disponibilidad.
   
```powershell
$as = Get-AzureRmAvailabilitySet -ResourceGroupName $resourceGroup
$vmIds = $as.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzureRmVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 
```

Cambie el tamaño de todas las máquinas virtuales del conjunto de disponibilidad y reinícielas.
   
```powershell
$newSize = "<newVmSize>"
$as = Get-AzureRmAvailabilitySet -ResourceGroupName $resourceGroup
$vmIds = $as.VirtualMachinesReferences
  foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    $vm = Get-AzureRmVM -ResourceGroupName $resourceGroup -Name $vmName
    $vm.HardwareProfile.VmSize = $newSize
    Update-AzureRmVM -ResourceGroupName $resourceGroup -VM $vm
    Start-AzureRmVM -ResourceGroupName $resourceGroup -Name $vmName
    }
```

## <a name="next-steps"></a>Pasos siguientes

Para obtener una mayor escalabilidad, ejecute varias instancias de VM y escálelas horizontalmente. Para obtener más información, consulte el artículo sobre [escalado automático de máquinas Linux en un conjunto de escalado de máquinas virtuales](../../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md).

