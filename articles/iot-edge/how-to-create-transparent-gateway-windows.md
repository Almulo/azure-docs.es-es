---
title: Creación de una puerta de enlace transparente con Azure IoT Edge en Windows| Microsoft Docs
description: Uso de Azure IoT Edge para crear una puerta de enlace transparente que pueda procesar información para varios dispositivos.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 6/20/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: e9de037f886db7a48411959ef62e1e6687e54beb
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2018
ms.locfileid: "46984303"
---
# <a name="create-a-windows-iot-edge-device-that-acts-as-a-transparent-gateway"></a>Creación de un dispositivo IoT Edge de Windows que actúa como una puerta de enlace transparente

En este artículo se proporcionan instrucciones detalladas para usar un dispositivo de IoT Edge como una puerta de enlace transparente. Durante el resto de este artículo, el término *puerta de enlace de IoT Edge* hace referencia a un dispositivo de IoT Edge que se usa como una puerta de enlace transparente. Para información más detallada, consulte [Uso de un dispositivo IoT Edge como puerta de enlace][lnk-edge-as-gateway], que brinda información general conceptual.

>[!NOTE]
>Actualmente:
> * Si la puerta de enlace se desconecta desde IoT Hub, los dispositivos de nivel inferior no se pueden autenticar con la puerta de enlace.
> * Los dispositivos habilitados para Edge no pueden conectarse a puertas de enlace de IoT Edge. 
> * Los dispositivos de bajada no pueden usar la carga de archivos.

La parte más difícil de crear una puerta de enlace transparente es conectar de forma segura la puerta de enlace a los dispositivos de bajada. Azure IoT Edge le permite usar la infraestructura de PKI para configurar conexiones TLS seguras entre estos dispositivos. En este caso, vamos a permitir que un dispositivo de bajada se conecte a un dispositivo IoT Edge que actúa como puerta de enlace transparente.  Para mantener una seguridad razonable, el dispositivo de bajada debe confirmar la identidad del dispositivo de Edge, porque lo que quiere es que los dispositivos solo se conecten a sus puertas de enlace y no a una puerta de enlace posiblemente malintencionada.

Puede crear cualquier infraestructura de certificados que permita la confianza necesaria para la topología de la puerta de enlace de dispositivo. En este artículo, se da por hecho la misma configuración de certificado que usaría para habilitar la [seguridad de entidad de certificación X.509][lnk-iothub-x509] en IoT Hub, lo que implica un certificado de entidad de certificación X.509 asociado a una instancia de IoT Hub específica (la entidad de certificación del propietario de la instancia de IoT Hub) y una serie de certificados, firmados con esta entidad de certificación, instalados en los dispositivos de Edge.

![Instalación de la puerta de enlace][1]

La puerta de enlace presenta su certificado de entidad de certificación de Edge al dispositivo de bajada durante el inicio de la conexión. El dispositivo de bajada comprueba que el certificado de entidad de certificación del dispositivo de Edge está firmado con el certificado de entidad de certificación del propietario. Este proceso permite que el dispositivo de bajada confirme que la puerta de enlace procede de un origen de confianza.

Los pasos siguientes le guían por el proceso de crear los certificados e instalarlos en los lugares adecuados.

## <a name="prerequisites"></a>Requisitos previos
1.  [Instale el entorno de ejecución de Azure IoT Edge][lnk-install-windows-x64] en el dispositivo Windows que vaya a usar como puerta de enlace transparente.

1. Obtenga OpenSSL para Windows. Existen muchas maneras de instalar OpenSSL:

   >[!NOTE]
   >Si ya ha instalado OpenSSL en el dispositivo Windows, puede omitir este paso, pero asegúrese de que `openssl.exe` está disponible en su variable de entorno `%PATH%`.

   * Descargue e instale cualquier [archivo binario de OpenSSL de terceros](https://wiki.openssl.org/index.php/Binaries), por ejemplo, de [este proyecto en SourceForge](https://sourceforge.net/projects/openssl/).
   
   * Descargue el código fuente de OpenSSL y compile los archivos binarios en su máquina por sí mismo o a través de [vcpkg](https://github.com/Microsoft/vcpkg). Las instrucciones que aparecen a continuación usan vcpkg para descargar el código fuente, compilar e instalar OpenSSL en el equipo Windows en pasos muy fáciles de realizar.

      1. Vaya al directorio donde quiera instalar vcpkg. De aquí en adelante se hará referencia a él como $VCPKGDIR. Siga las instrucciones para descargar e instalar [vcpkg](https://github.com/Microsoft/vcpkg).
   
      1. Después de instalar vcpkg, desde un símbolo del sistema de Powershell, ejecute el siguiente comando para instalar el paquete de OpenSSL para Windows x64. Este proceso suele tardar aproximadamente 5 minutos en completarse.

         ```PowerShell
         .\vcpkg install openssl:x64-windows
         ```
      1. Agregue `$VCPKGDIR\installed\x64-windows\tools\openssl` a su variable de entorno `PATH` para que el archivo `openssl.exe` esté disponible para la invocación.

1. Vaya al directorio en el que quiere trabajar. De aquí en adelante haremos referencia a él como $WRKDIR.  Todos los archivos se crearán en este directorio.
   
   cd $WRKDIR

1.  Obtenga los scripts para generar los certificados del entorno de no producción necesarios con el siguiente comando. Estos scripts ayudan a crear los certificados necesarios para configurar una puerta de enlace transparente.

      ```PowerShell
      git clone https://github.com/Azure/azure-iot-sdk-c.git
      ```

1. Copie los archivos de configuración y de script en el directorio de trabajo. Además, establezca la variable env OPENSSL_CONF para usar el archivo de configuración openssl_root_ca.cnf.

   ```PowerShell
   copy azure-iot-sdk-c\tools\CACertificates\*.cnf .
   copy azure-iot-sdk-c\tools\CACertificates\ca-certs.ps1 .
   $env:OPENSSL_CONF = "$PWD\openssl_root_ca.cnf"
   ```

1. Habilite PowerShell para ejecutar los scripts con el siguiente comando.

   ```PowerShell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted
   ```

1. Lleve las funciones, usadas por los scripts, al espacio de nombres global de PowerShell mediante scripts prefijados con punto con el siguiente comando.
   
   ```PowerShell
   . .\ca-certs.ps1
   ```

1. Compruebe que OpenSSL se ha instalado correctamente y asegúrese de que no haya conflictos de nombres con los certificados existentes, mediante la ejecución del siguiente comando. Si hay problemas, el script debe describir cómo corregirlos en el sistema.

   ```PowerShell
   Test-CACertsPrerequisites
   ```

## <a name="certificate-creation"></a>Creación de certificados
1.  Cree el certificado de entidad de certificación de propietario y un certificado intermedio. Estos se colocan en `$WRKDIR`.

      ```PowerShell
      New-CACertsCertChain rsa
      ```

1.  Cree el certificado de entidad de certificación de dispositivo de Edge y una clave privada con el siguiente comando.

   >[!NOTE]
   > **NO** use un nombre que sea igual que el nombre de host DNS de la puerta de enlace. Si lo hace, la certificación del cliente con estos certificados generará un error.

   ```PowerShell
   New-CACertsEdgeDevice "<gateway device name>"
   ```

## <a name="certificate-chain-creation"></a>Creación de la cadena de certificado
Cree una cadena de certificado a partir del certificado de entidad de certificación de propietario, el certificado intermedio y el certificado de entidad de certificación de dispositivo de Edge, con el siguiente comando. Al colocarlo en un archivo de cadena, puede instalarlo fácilmente en el dispositivo de Edge que actúa como puerta de enlace transparente.

   ```PowerShell
   Write-CACertsCertificatesForEdgeDevice "<gateway device name>"
   ```

   La salida de la ejecución del script son los certificados y la clave siguientes:
   * `$WRKDIR\certs\new-edge-device.*`
   * `$WRKDIR\private\new-edge-device.key.pem`
   * `$WRKDIR\certs\azure-iot-test-only.root.ca.cert.pem`

## <a name="installation-on-the-gateway"></a>Instalación en la puerta de enlace
1.  Copie los archivos siguientes de $WRKDIR en cualquier parte de su dispositivo de Edge; haremos referencia a esto como $CERTDIR. Si ha generado los certificados en el dispositivo de Edge, omita este paso.

   * Certificado de entidad de certificación de dispositivo: `$WRKDIR\certs\new-edge-device-full-chain.cert.pem`
   * Clave privada de entidad de certificación de dispositivo: `$WRKDIR\private\new-edge-device.key.pem`
   * Entidad de certificación de propietario: `$WRKDIR\certs\azure-iot-test-only.root.ca.cert.pem`

2.  Establezca las propiedades `certificate` del archivo YAML de configuración del demonio de seguridad en la ruta de acceso donde colocó los archivos de certificado y clave.

```yaml
certificates:
  device_ca_cert: "$CERTDIR\\certs\\new-edge-device-full-chain.cert.pem"
  device_ca_pk: "$CERTDIR\\private\\new-edge-device.key.pem"
  trusted_ca_certs: "$CERTDIR\\certs\\azure-iot-test-only.root.ca.cert.pem"
```
## <a name="deploy-edgehub-to-the-gateway"></a>Implementación de EdgeHub en la puerta de enlace
Una de las funcionalidades clave de Azure IoT Edge es que puede implementar módulos en dispositivos de IoT Edge desde la nube. En esta sección tiene que crear una implementación aparentemente vacía; sin embargo, Edge Hub se agrega automáticamente a todas las implementaciones incluso si no existe ningún otro módulo. Edge Hub es el único módulo que necesita en un dispositivo de Edge para que actúe como una puerta de enlace transparente, así que es suficiente con crear una implementación vacía. 
1. En Azure Portal, navegue hasta el centro de IoT.
2. Vaya a **IoT Edge** y seleccione el dispositivo IoT Edge que quiere usar como puerta de enlace.
3. Seleccione **Set modules** (Establecer módulos).
4. Seleccione **Next** (Siguiente).
5. En el paso **Specify routes** (Especificar rutas), debe tener una ruta predeterminada que envíe todos los mensajes de todos los módulos a IoT Hub. Si no es así, agregue el código siguiente y, a continuación, seleccione **Next** (Siguiente).
   ```JSON
   {
       "routes": {
           "route": "FROM /* INTO $upstream"
       }
   }
   ```
6. En el paso de revisión de plantilla, seleccione **Submit** (Enviar).

## <a name="installation-on-the-downstream-device"></a>Instalación en el dispositivo de bajada
Un dispositivo de nivel inferior puede ser cualquier aplicación que usa el [SDK de dispositivo IoT de Azure][lnk-devicesdk], como la aplicación sencilla describa en [Conexión del dispositivo en IoT Hub con .NET][lnk-iothub-getstarted]. Una aplicación de dispositivo de bajada tiene que confiar en el certificado de la **entidad de certificación de propietario** para validar las conexiones TLS en los dispositivos de puerta de enlace. Normalmente, este paso puede realizarse de dos maneras: en el nivel del sistema operativo o, para determinados idiomas, en el nivel de aplicación.

### <a name="os-level"></a>Nivel de sistema operativo
La instalación de este certificado en el almacén de certificados de sistema operativo permitirá que todas las aplicaciones usen el certificado de entidad de certificación de propietario como certificado de confianza.

* Ubuntu: este es un ejemplo de cómo instalar un certificado de entidad de certificación en un host de Ubuntu.

   ```cmd
   sudo cp $CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem  /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
   sudo update-ca-certificates
   ```
 
    Verá un mensaje que dice "Updating certificates in /etc/ssl/certs... 1 added, 0 removed; done." (Actualizando certificados en /etc/ssl/certs...1 agregado, 0 quitados, listo)

* Windows: este es un ejemplo de cómo instalar un certificado de entidad de certificación en un host de Windows.
  * En el menú de inicio, escriba "Administrar certificados de equipo". Esto debería iniciar una utilidad denominada `certlm`.
  * Navegue hasta Certificados - Equipo Local --> Certificado raíz de confianza --> Certificados --> Haga clic con el botón derecho --> Todas las tareas --> Importar para iniciar el Asistente para importar certificados.
  * Siga los pasos tal como se indica e importe el archivo de certificado $CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem.
  * Cuando haya completado, verá el mensaje "Importación correcta".

### <a name="application-level"></a>Nivel de aplicación
En aplicaciones. NET, puede agregar el siguiente fragmento de código para confiar en un certificado en formato PEM. Inicialice la variable `certPath` con `$CERTDIR\certs\azure-iot-test-only.root.ca.cert.pem`.

   ```
   using System.Security.Cryptography.X509Certificates;

   ...

   X509Store store = new X509Store(StoreName.Root, StoreLocation.CurrentUser);
   store.Open(OpenFlags.ReadWrite);
   store.Add(new X509Certificate2(X509Certificate2.CreateFromCertFile(certPath)));
   store.Close();
   ```

## <a name="connect-the-downstream-device-to-the-gateway"></a>Conexión del dispositivo de bajada a la puerta de enlace
Debe inicializar el SDK del dispositivo de IoT Hub con una cadena de conexión que haga referencia al nombre de host del dispositivo de puerta de enlace. Esto se hace anexando la propiedad `GatewayHostName` a la cadena de conexión del dispositivo. Por ejemplo, esto es una cadena de conexión de dispositivo de ejemplo para un dispositivo, al que se anexa la propiedad `GatewayHostName`:

   ```
   HostName=yourHub.azure-devices.net;DeviceId=yourDevice;SharedAccessKey=XXXYYYZZZ=;GatewayHostName=mygateway.contoso.com
   ```

   >[!NOTE]
   >Este es un comando de ejemplo que comprueba que todo se ha configurado correctamente. Verá un mensaje que dice que la verificación es correcta.
   >
   >openssl s_client -connect mygateway.contoso.com:8883 -CAfile $CERTDIR/certs/azure-iot-test-only.root.ca.cert.pem -showcerts

## <a name="routing-messages-from-downstream-devices"></a>Enrutamiento de mensajes desde dispositivos de bajada
El entorno de ejecución de Azure IoT Edge puede enrutar los mensajes enviados desde dispositivos de bajada igual que los mensajes enviados por los módulos. Esto le permite realizar análisis en un módulo que se ejecuta en la puerta de enlace antes de enviar datos a la nube. La siguiente ruta se usaría para enviar mensajes desde un dispositivo de bajada llamado `sensor` a un módulo llamado `ai_insights`.

   ```json
   { "routes":{ "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" } }
   ```

Para más información sobre el enrutamiento de mensajes, consulte el [artículo de composición de los módulos][lnk-module-composition].

[!INCLUDE [](../../includes/iot-edge-extended-offline-preview.md)]

## <a name="next-steps"></a>Pasos siguientes
[Descripción de los requisitos y las herramientas para desarrollar módulos de IoT Edge][lnk-module-dev].

<!-- Images -->
[1]: ./media/how-to-create-transparent-gateway/gateway-setup.png

<!-- Links -->
[lnk-install-windows-x64]: ./how-to-install-iot-edge-windows-with-windows.md
[lnk-module-composition]: ./module-composition.md
[lnk-devicesdk]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
[lnk-edge-as-gateway]: ./iot-edge-as-gateway.md
[lnk-module-dev]: module-development.md
[lnk-iothub-getstarted]: ../iot-hub/quickstart-send-telemetry-dotnet.md
[lnk-iothub-x509]: ../iot-hub/iot-hub-x509ca-overview.md
[lnk-iothub-secure-deployment]: ../iot-hub/iot-hub-security-deployment.md
[lnk-iothub-tokens]: ../iot-hub/iot-hub-devguide-security.md#security-tokens
[lnk-iothub-throttles-quotas]: ../iot-hub/iot-hub-devguide-quotas-throttling.md
[lnk-iothub-devicetwins]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-iothub-c2d]: ../iot-hub/iot-hub-devguide-messages-c2d.md
[lnk-ca-scripts]: https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md
[lnk-modbus-module]: https://github.com/Azure/iot-edge-modbus
