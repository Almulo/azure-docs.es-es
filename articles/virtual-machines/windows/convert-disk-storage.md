---
title: Conversión de almacenamiento de Azure Managed Disks de estándar a premium, y viceversa | Microsoft Docs
description: Conversión de Azure Managed Disks de estándar a premium, y viceversa, mediante Azure PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: ramankum
manager: kavithag
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: ramankum
ms.openlocfilehash: 6cfbc458a4bd751b8b871501d19db641e3980767
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/12/2018
ms.locfileid: "44719331"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-and-vice-versa"></a>Conversión de almacenamiento de Azure Managed Disks de estándar a premium, y viceversa

Managed Disks ofrece tres opciones de almacenamiento: [SSD premium](../windows/premium-storage.md), SSD estándar (versión preliminar) y [HDD estándar](../windows/standard-storage.md). Permite cambiar fácilmente entre las opciones en función de sus necesidades de rendimiento, con el mínimo tiempo de inactividad. Esto no se admite para discos no administrados. Pero resulta muy sencillo realizar la [conversión a Managed Disks](convert-unmanaged-to-managed-disks.md) para cambiar fácilmente entre los tipos de discos.

En este artículo se muestra cómo convertir Managed Disks de estándar a premium, y viceversa, mediante Azure PowerShell. Si necesita instalarlas o actualizarlas, consulte [Install and configure Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-6.8.1) (Instalación y configuración de Azure PowerShell).

## <a name="before-you-begin"></a>Antes de empezar

* La conversión requiere reiniciar la máquina virtual, por lo que debe programar la migración del almacenamiento de discos durante una ventana de mantenimiento existente previamente. 
* Si usa discos no administrados, debe primero realizar la [conversión a Managed Disks](convert-unmanaged-to-managed-disks.md) para usar este artículo para cambiar entre las opciones de almacenamiento. 
* En este artículo se requiere la versión 6.0.0 del módulo de Azure PowerShell, u otra posterior. Ejecute ` Get-Module -ListAvailable AzureRM` para encontrar la versión. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-azurerm-ps). También tiene que ejecutar `Connect-AzureRmAccount` para crear una conexión con Azure.


## <a name="convert-all-the-managed-disks-of-a-vm-from-standard-to-premium-and-vice-versa"></a>Conversión de todos los discos Managed Disks de una máquina virtual de estándar a premium, y viceversa

En el ejemplo siguiente se muestra cómo cambiar todos los discos de una máquina virtual del almacenamiento estándar al premium. Para usar discos administrados premium, la máquina virtual debe usar un [Tamaño de máquina virtual](sizes.md) que admita Premium Storage. En este ejemplo también se pasa a un tamaño que admite Premium Storage.

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between StandardLRS and PremiumLRS based on your scenario
$storageType = 'Premium_LRS'

# Premium capable size
# Required only if converting storage from standard to premium
$size = 'Standard_DS2_v2'

# Stop and deallocate the VM before changing the size
Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force

$vm = Get-AzureRmVM -Name $vmName -resourceGroupName $rgName

# Change the VM size to a size that supports premium storage
# Skip this step if converting storage from premium to standard
$vm.HardwareProfile.VmSize = $size
Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzureRmDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.ManagedBy -eq $vm.Id)
    {
        $diskUpdateConfig = New-AzureRmDiskUpdateConfig –AccountType $storageType
        Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
        -DiskName $disk.Name
    }
}

Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```
## <a name="convert-a-managed-disk-from-standard-to-premium-and-vice-versa"></a>Conversión de un disco de Managed Disks de estándar a premium, y viceversa

Puede que, en el caso de la carga de trabajo de desarrollo y pruebas, prefiera tener una combinación de discos estándar y premium para reducir el costo. Para ello, puede actualizar a Premium Storage solo los discos que requieren mejor rendimiento. En el ejemplo siguiente se muestra cómo cambiar un solo disco de una máquina virtual del almacenamiento estándar al premium, y viceversa. Para usar discos administrados premium, la máquina virtual debe usar un [Tamaño de máquina virtual](sizes.md) que admita Premium Storage. En este ejemplo también se pasa a un tamaño que admite Premium Storage.

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between StandardLRS and PremiumLRS based on your scenario
$storageType = 'Premium_LRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzureRmDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzureRmResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzureRmVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzureRmVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Change the VM size to a size that supports premium storage
# Skip this step if converting storage from premium to standard
$vm.HardwareProfile.VmSize = $size
Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$diskUpdateConfig = New-AzureRmDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzureRmVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="convert-a-managed-disk-from-standard-hdd-to-standard-ssd-and-vice-versa"></a>Conversión de un disco administrado de HDD estándar a SSD estándar, y viceversa

En el ejemplo siguiente se muestra cómo cambiar un solo disco de una máquina virtual de HDD estándar a SSD estándar, y viceversa.

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and StandardSSD_LRS based on your scenario
$storageType = 'StandardSSD_LRS'

$disk = Get-AzureRmDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzureRmResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzureRmVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzureRmVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Update the storage type
$diskUpdateConfig = New-AzureRmDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzureRmVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="next-steps"></a>Pasos siguientes

Realice una copia de solo lectura de una máquina virtual mediante [instantáneas](snapshot-copy-managed-disk.md).

