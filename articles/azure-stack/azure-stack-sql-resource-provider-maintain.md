---
title: Mantenimiento de un proveedor de recursos de SQL en Azure Stack |Microsoft Docs
description: Aprenda cómo puede mantener el servicio de proveedor de recursos SQL en Azure Stack.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: ad899739dab1dc51d64368d2136ab87f73f6f3a0
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2018
ms.locfileid: "36300917"
---
# <a name="sql-resource-provider-maintenance-operations"></a>Operaciones de mantenimiento del proveedor de recursos de SQL

El proveedor de recursos de SQL se ejecuta en una máquina virtual bloqueada. Para habilitar las operaciones de mantenimiento, debe actualizar la seguridad de la máquina virtual. Para hacerlo con el principio de privilegio mínimo, puede usar el punto de conexión de [JEA (Just Enough Administration) de PowerShell](https://docs.microsoft.com/powershell/jea/overview) *DBAdapterMaintenance*. El paquete de instalación del proveedor de recursos incluye un script para esta operación.

## <a name="patching-and-updating"></a>Revisiones y actualizaciones

El proveedor de recursos de SQL no se suministra como parte de Azure Stack porque es un componente complementario. Microsoft proporciona las actualizaciones para el proveedor de recursos de SQL según sea necesario. Cuando se publica un adaptador de SQL actualizado, se proporciona un script para aplicar la actualización. Este script crea una nueva VM de proveedor de recursos, que migra el estado de la VM del proveedor antigua a la VM nueva. Para más información, consulte [Actualización del proveedor de recursos de SQL](azure-stack-sql-resource-provider-update.md).

### <a name="provider-virtual-machine"></a>Máquina virtual del proveedor

Dado que el proveedor de recursos se ejecuta en la máquina virtual de un *usuario*, debe aplicar las revisiones y actualizaciones necesarias cuando se publiquen. Puede usar los paquetes de actualización de Windows que se proporcionan como parte del ciclo de revisión y actualización para aplicar actualizaciones a la máquina virtual.

## <a name="backuprestoredisaster-recovery"></a>Copia de seguridad, restauración o recuperación ante desastres

 Dado que se trata de un componente complementarios, no se realiza una copia de seguridad del proveedor de recursos de SQL como parte de un proceso de recuperación ante desastres y continuidad empresarial (BCDR) de Azure Stack. Se proporcionarán scripts para las siguientes operaciones:

- Copia de seguridad de la información de estado (almacenada en una cuenta de almacenamiento de Azure Stack).
- Restauración del proveedor de recursos si se requiere una recuperación de la pila completa.

>[!NOTE]
>Si necesita realizar una recuperación, los servidores de base de datos deben recuperarse antes de restaurar el proveedor de recursos.

## <a name="updating-sql-credentials"></a>Actualización de las credenciales de SQL

Es responsable de crear y mantener cuentas sysadmin en los servidores SQL Server. El proveedor de recursos necesita una cuenta con estos privilegios para administrar las bases de datos para los usuarios, pero no necesita acceso a los datos de los usuarios. Si tiene que actualizar las contraseñas de sysadmin en los servidores SQL Server, puede utilizar la interfaz del administrador del proveedor de recursos para cambiar una contraseña almacenada. Estas contraseñas se almacenan en un almacén de claves en la instancia de Azure Stack.

Para modificar la configuración, seleccione **Examinar** &gt; **RECURSOS ADMINISTRATIVOS** &gt; **Servidores de hospedaje SQL** &gt; **Inicios de sesión SQL** y seleccione un nombre de usuario. El cambio se debe efectuar en la instancia de SQL en primer lugar (y en cualquier réplica, si es necesario). En **Configuración**, seleccione **Contraseña**.

![Actualización de la contraseña de administrador](./media/azure-stack-sql-rp-deploy/sqlrp-update-password.PNG)

## <a name="secrets-rotation"></a>Cambio de secretos

*Estas instrucciones se aplican solo a sistemas integrados de Azure Stack (versión 1804 y posteriores). No intente cambiar secretos en versiones de Azure Stack anteriores a 1804.*

Al usar los proveedores de recursos de SQL y MySQL con sistemas integrados de Azure Stack, puede cambiar los siguientes secretos de infraestructura (implementación):

- Certificado SSL externo [proporcionado durante la implementación](azure-stack-pki-certs.md).
- La contraseña de la cuenta de administrador local de la máquina virtual del proveedor de recursos proporcionada durante la implementación.
- Contraseña de usuario de diagnóstico del proveedor de recursos (dbadapterdiag).

### <a name="powershell-examples-for-rotating-secrets"></a>Ejemplos de PowerShell de cambio de secretos

**Cambio de todos los secretos al mismo tiempo.**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    –DiagnosticsUserPassword $passwd `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd  `
    -VMLocalCredential $localCreds
```

**Cambio de la contraseña de usuario de diagnóstico.**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    –DiagnosticsUserPassword  $passwd
```

**Cambio de la contraseña de la cuenta de administrador local de la máquina virtual.**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -VMLocalCredential $localCreds
```

**Cambio de la contraseña del certificado SSL.**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd
```

### <a name="secretrotationsqlproviderps1-parameters"></a>Parámetros de SecretRotationSQLProvider.ps1

|.|DESCRIPCIÓN|
|-----|-----|
|AzCredential|Credencial de la cuenta de administrador de servicio de Azure Stack.|
|CloudAdminCredential|Credencial de cuenta de dominio de administración en la nube de Azure Stack.|
|PrivilegedEndpoint|Punto de conexión con privilegios para acceder a Get-AzureStackStampInformation.|
|DiagnosticsUserPassword|Contraseña de la cuenta de usuario de diagnóstico.|
|VMLocalCredential|Cuenta de administrador local de la máquina virtual MySQLAdapter.|
|DefaultSSLCertificatePassword|Contraseña del certificado SSL predeterminado (*.pfx).|
|DependencyFilesLocalPath|Ruta de acceso local de los archivos de dependencia.|
|     |     |

### <a name="known-issues"></a>Problemas conocidos

**Problema**: Registros de cambio de secretos.<br>
Los registros para el cambio de secretos no se recopilan automáticamente si el script personalizado de cambio de secretos produce un error cuando se ejecuta.

**Solución alternativa**:<br>
Utilice el cmdlet Get-AzsDBAdapterLogs para recopilar todos los registros del proveedor de recursos, incluido AzureStack.DatabaseAdapter.SecretRotation.ps1_*.log, guardado en C:\Logs.

## <a name="update-the-virtual-machine-operating-system"></a>Actualización del sistema operativo de la máquina virtual

Use uno de los métodos siguientes para actualizar el sistema operativo de la máquina virtual.

- Instalar el paquete más reciente del proveedor de recursos mediante una imagen de Windows Server 2016 Core actualmente revisada.
- Instalar un paquete de Windows Update durante la instalación o actualización del proveedor de recursos.

## <a name="update-the-virtual-machine-windows-defender-definitions"></a>Actualización de las definiciones de Windows Defender de máquina virtual

Para actualizar las definiciones de Windows Defender:

1. Descargue la actualización de las definiciones de Windows Defender desde [esta página](https://www.microsoft.com/en-us/wdsi/definitions).

   En la página de actualización de definiciones, desplácese a "Manually download and install the definitions" (Descargar e instalar manualmente las definiciones). Descargue el archivo "Windows Defender Antivirus for Windows 10 and Windows 8.1" de 64 bits.

   De forma alternativa, use [este vínculo directo](https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64) para descargar o ejecutar el archivo fpam-fe.exe.

2. Cree una sesión de PowerShell en el punto de conexión de mantenimiento de la máquina virtual del adaptador del proveedor de recursos SQL.

3. Copie el archivo de actualización de definiciones en la máquina virtual mediante la sesión del punto de conexión de mantenimiento.

4. En la sesión de PowerShell de mantenimiento, ejecute el comando *Update-DBAdapterWindowsDefenderDefinitions*.

5. Después de instalar las definiciones, se recomienda eliminar el archivo de actualización de definiciones mediante el comando *Remove-ItemOnUserDrive*.

**Ejemplo de script de PowerShell para actualizar definiciones.**

Para actualizar las definiciones de Defender se puede editar y ejecutar el script siguiente. Reemplace los valores del script por los de su entorno.

```powershell
# Set credentials for local admin on the resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "<local admin user password>" -AsPlainText -Force
$vmLocalAdminUser = "<local admin user name>"
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential `
    ($vmLocalAdminUser, $vmLocalAdminPass)

# Provide the public IP address for the adapter VM.
$databaseRPMachine  = "<RP VM IP address>"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"

# Download the Windows Defender update definitions file from https://www.microsoft.com/en-us/wdsi/definitions.
Invoke-WebRequest -Uri 'https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64' `
    -Outfile $localPathToDefenderUpdate

# Create a session to the maintenance endpoint.
$session = New-PSSession -ComputerName $databaseRPMachine `
    -Credential $vmLocalAdminCreds -ConfigurationName DBAdapterMaintenance
# Copy the defender update file to the adapter virtual machine.
Copy-Item -ToSession $session -Path $localPathToDefenderUpdate `
     -Destination "User:\"
# Install the update definitions.
Invoke-Command -Session $session -ScriptBlock `
    {Update-AzSDBAdapterWindowsDefenderDefinition -DefinitionsUpdatePackageFile "User:\mpam-fe.exe"}
# Cleanup the definitions package file and session.
Invoke-Command -Session $session -ScriptBlock `
    {Remove-AzSItemOnUserDrive -ItemPath "User:\mpam-fe.exe"}
$session | Remove-PSSession
```

## <a name="collect-diagnostic-logs"></a>Recopilación de registros de diagnóstico

Para recopilar registros de la máquina virtual bloqueada, se puede usar el punto de conexión *DBAdapterDiagnostics* de JEA (Just Enough Administration) de PowerShell. Este punto de conexión proporciona los comandos siguientes:

- **Get-AzsDBAdapterLog**. Este comando crea un paquete ZIP con los registros de diagnóstico del proveedor de recursos y guarda el archivo en la unidad del usuario de la sesión. Este comando se puede ejecutar sin ningún parámetro y se recopilan los registros de las últimas cuatro horas.
- **Remove-AzsDBAdapterLog**. Este comando quita los paquetes de registros existentes en la máquina virtual del proveedor de recursos.

### <a name="endpoint-requirements-and-process"></a>Requisitos y proceso del punto de conexión

Cuando se instala o actualiza un proveedor de recursos, se crea la cuenta de usuario **dbadapterdiag**. Esta cuenta se usará para recopilar registros de diagnóstico.

>[!NOTE]
>La contraseña de la cuenta dbadapterdiag es la misma que se usa para el administrador local en la máquina virtual creada durante la implementación o actualización de un proveedor.

Para usar los comandos *DBAdapterDiagnostics*, cree una sesión remota de PowerShell para la máquina virtual del proveedor de recursos y ejecute el comando **Get-AzsDBAdapterLog**.

Establezca el intervalo de tiempo para la recopilación de registros mediante los parámetros **FromDate** y **ToDate**. Si no se especifica uno de estos parámetros (o ambos), se utilizan los valores predeterminados siguientes:

- FromDate es cuatro horas antes de la hora actual.
- ToDate es la hora actual.

**Ejemplo de script de PowerShell para recopilar registros.**

El siguiente script muestra cómo recopilar registros de diagnóstico de la máquina virtual del proveedor de recursos.

```powershell
# Create a new diagnostics endpoint session.
$databaseRPMachineIP = '<RP VM IP address>'
$diagnosticsUserName = 'dbadapterdiag'
$diagnosticsUserPassword = '<Enter Diagnostic password>'

$diagCreds = New-Object System.Management.Automation.PSCredential `
        ($diagnosticsUserName, (ConvertTo-SecureString -String $diagnosticsUserPassword -AsPlainText -Force))
$session = New-PSSession -ComputerName $databaseRPMachineIP -Credential $diagCreds `
        -ConfigurationName DBAdapterDiagnostics

# Sample that captures logs from the previous hour.
$fromDate = (Get-Date).AddHours(-1)
$dateNow = Get-Date
$sb = {param($d1,$d2) Get-AzSDBAdapterLog -FromDate $d1 -ToDate $d2}
$logs = Invoke-Command -Session $session -ScriptBlock $sb -ArgumentList $fromDate,$dateNow

# Copy the logs to the user drive.
$sourcePath = "User:\{0}" -f $logs
$destinationPackage = Join-Path -Path (Convert-Path '.') -ChildPath $logs
Copy-Item -FromSession $session -Path $sourcePath -Destination $destinationPackage

# Cleanup the logs.
$cleanup = Invoke-Command -Session $session -ScriptBlock {Remove- AzsDBAdapterLog }
# Close the session.
$session | Remove-PSSession
```

## <a name="next-steps"></a>Pasos siguientes

[Adición de servidores de hospedaje de SQL Server](azure-stack-sql-resource-provider-hosting-servers.md)
