---
title: 'Azure Security and Compliance Blueprint: análisis de datos para NIST SP 800-171'
description: 'Azure Security and Compliance Blueprint: análisis de datos para NIST SP 800-171'
services: security
author: jomolesk
ms.assetid: ca75d2a8-4e20-489b-9a9f-3a61e74b032d
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: b2c96509b2b8fd61c1814d5b4a015048eeb1807d
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/01/2018
ms.locfileid: "39392642"
---
# <a name="azure-security-and-compliance-blueprint---data-analytics-for-nist-sp-800-171"></a>Azure Security and Compliance Blueprint: análisis de datos para NIST SP 800-171

## <a name="overview"></a>Información general
En [NIST Special Publication 800-171](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) se proporcionan instrucciones para proteger la información sin clasificar controlada (CUI) que reside en sistemas de información y organizaciones no federales. NIST SP 800-171 establece 14 familias de requisitos de seguridad para proteger la confidencialidad de CUI.

Este documento de Azure Security and Compliance Blueprint proporciona instrucciones para ayudar a los clientes a implementar una arquitectura de análisis de datos de Azure que implementa un subconjunto de controles de NIST SP 800-171. Esta solución muestra las formas en que los clientes pueden cumplir con los requisitos específicos de seguridad y cumplimiento y sirve como base para que los clientes creen y configuren sus propias soluciones de análisis de datos en Azure.

Esta arquitectura de referencia, la guía de implementación asociada y el modelo de amenazas están diseñados como base para que los clientes puedan adaptarse a sus requisitos específicos y no deben usarse tal cual en un entorno de producción. La implementación de esta arquitectura sin modificación no es suficiente para satisfacer completamente los requisitos de NIST SP 800-171. Los clientes tienen la responsabilidad de realizar las evaluaciones de seguridad y cumplimiento adecuadas de cualquier solución compilada con esta arquitectura, ya que los requisitos pueden variar en función de las características de implementación de cada cliente.

## <a name="architecture-diagram-and-components"></a>Componentes y diagrama de la arquitectura
Esta solución proporciona una plataforma analítica sobre la cual los clientes pueden crear sus propias herramientas de análisis. La arquitectura de referencia esboza un caso de uso genérico en el que los clientes introducen datos ya sea a través de importaciones de datos masivos por parte del administrador de datos/SQL o mediante actualizaciones de datos operativos a través de un usuario operativo. Ambas series de tareas incorporan Azure Functions para importar datos en Azure SQL Database. El cliente debe configurar Azure Functions en Azure Portal para controlar las tareas de importación exclusivas de los requisitos de análisis propios del cliente.

Azure ofrece una variedad de servicios de informes y análisis al cliente; sin embargo, esta solución incorpora los servicios de Azure Machine Learning en conjunto con Azure SQL Database para examinar rápidamente los datos y entregar resultados más rápidos mediante un modelado más inteligente de los datos. Azure Machine Learning es una forma de aprendizaje automático destinada a aumentar la velocidad de las consultas mediante el descubrimiento de nuevas relaciones entre conjuntos de datos. Una vez que los datos se han entrenado mediante varias funciones estadísticas, se pueden sincronizar hasta siete grupos de consulta adicionales (ocho en total incluido el servidor del cliente) con los mismos modelos tabulares para distribuir la carga de trabajo de la consulta y reducir el tiempo de respuesta.

Para mejorar el análisis y los informes, Azure SQL Database puede configurarse con índices de almacén de columnas. Tanto Azure Machine Learning como Azure SQL Database se pueden escalar o reducir verticalmente o apagarse completamente en respuesta al uso del cliente. Todo el tráfico SQL se cifra con SSL mediante la inclusión de certificados autofirmados. Como procedimiento recomendado, Azure recomienda el uso de una entidad de certificación de confianza para mejorar la seguridad.

Cuando se cargan los datos en la instancia de Azure SQL Database y se entrenan con Azure Machine Learning, los digiere el usuario operativo y el administrador de datos/SQL con Power BI. Power BI muestra los datos de forma intuitiva y reúne la información a través de múltiples conjuntos de datos para obtener un mayor conocimiento. Su alto grado de adaptabilidad y fácil integración con Azure SQL Database asegura que los clientes puedan configurarlo para tratar una amplia gama de escenarios según sus necesidades de negocio.

Toda la solución se basa en el servicio Azure Storage, que los clientes configuran desde Azure Portal. Azure Storage cifra todos los datos con Storage Service Encryption para mantener la confidencialidad de los datos en reposo. El almacenamiento con redundancia geográfica garantiza que un evento adverso en el centro de datos principal del cliente no tendrá como resultado una pérdida de datos, ya que una segunda copia se almacenará en una ubicación separada a cientos de kilómetros de distancia.

Para mejorar la seguridad, todos los recursos de esta solución se administran como un grupo de recursos mediante Azure Resource Manager. El control de acceso basado en rol de Azure Active Directory se utiliza para controlar el acceso a los recursos implementados, incluidas las claves en Azure Key Vault. El mantenimiento del sistema se supervisa mediante Azure Security Center y Azure Monitor. Los clientes configuran ambos servicios de monitorización para capturar registros y mostrar el estado del sistema en un único panel de fácil navegación.

Azure SQL Database se administra comúnmente mediante SQL Server Management Studio, que se ejecuta desde una máquina local configurada para acceder a Azure SQL Database mediante una conexión segura VPN o ExpressRoute. **Microsoft recomienda configurar una conexión VPN o ExpressRoute para la administración y la importación de datos en el grupo de recursos**.

![Análisis de datos para NIST SP 800-171: diagrama de arquitectura de referencia](images/nist171-analytics-architecture.png "Análisis de datos para NIST SP 800-171: diagrama de arquitectura de referencia")

Esta solución usa los siguientes servicios de Azure. Se puede encontrar más información en la sección [Arquitectura de implementación](#deployment-architecture).

- Application Insights
- Azure Active Directory
- Azure Data Catalog
- Azure Disk Encryption
- Azure Event Grid
- Azure Functions
- Azure Key Vault
- Azure Machine Learning
- Azure Monitor
- Azure Security Center
- Azure SQL Database
- Azure Storage
- Azure Log Analytics
- Azure Virtual Network
    - (1) /16 redes
    - (2) /24 redes
    - (2) Grupos de seguridad de red
- Panel de Power BI

## <a name="deployment-architecture"></a>Arquitectura de implementación
En la siguiente sección se detallan los elementos de desarrollo e implementación.

**Azure Event Grid**: [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview) permite a los clientes crear fácilmente aplicaciones con arquitecturas basadas en eventos. Los usuarios seleccionan el recurso de Azure al que les gustaría suscribirse y asignan el controlador de eventos o el punto de conexión de webhook para enviar el evento. Los clientes pueden proteger los puntos de conexión de webhook mediante la incorporación de parámetros de consulta a la URL de webhook al crear una suscripción a eventos. Azure Event Grid solo admite puntos de conexión de webhook HTTPS. Azure Event Grid permite a los clientes controlar el nivel de acceso dado a distintos usuarios para realizar diversas operaciones de administración, como enumerar las suscripciones a eventos, crear otras nuevas y generar claves. Event Grid utiliza el control de acceso basado en rol de Azure.

**Azure Functions**: [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) es un servicio de proceso sin servidor que permite ejecutar código a petición sin necesidad de aprovisionar ni administrar explícitamente la infraestructura. Use Azure Functions para ejecutar un script o un fragmento de código en respuesta a diversos eventos.

**Azure Machine Learning**: [Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/preview/) es una técnica de ciencia de datos que permite a los equipos utilizar datos existentes para prever tendencias, resultados y comportamientos futuros.

**Azure Data Catalog**: [Data Catalog](https://docs.microsoft.com/azure/data-catalog/data-catalog-what-is-data-catalog) facilita que los usuarios que administran los datos puedan detectar y comprender los orígenes de datos. En los orígenes de datos comunes se pueden registrar, etiquetar y buscar datos. Los datos permanecen en la ubicación existente, pero se agrega una copia de sus metadatos a Data Catalog, junto con una referencia a la ubicación del origen de datos. Los metadatos también se indexan no solo para que todos los orígenes de datos se puedan detectar fácilmente a través de la búsqueda, sino también para que los usuarios que los detecten puedan comprenderlos.

### <a name="virtual-network"></a>Virtual network
La arquitectura de referencia define una red virtual privada con un espacio de direcciones de 10.0.0.0/16.

**Grupos de seguridad de red**: los [grupos de seguridad de red](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) contienen listas de control de acceso que permiten o deniegan el tráfico en una red virtual. Los grupos de seguridad de red pueden usarse para proteger el tráfico en una subred o un nivel de máquina virtual individual. Existen los siguientes grupos de seguridad de red:
  - Un grupo de seguridad de red para Active Directory
  - Un grupo de seguridad de red para la carga de trabajo

Cada uno de los grupos de seguridad de red tiene puertos y protocolos específicos abiertos para que la solución pueda funcionar de forma segura y correcta. Además, las siguientes opciones de configuración están habilitadas para cada grupo de seguridad de red:
  - Los [eventos y registros de diagnóstico](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) están habilitados y se almacenan en la cuenta de almacenamiento.
  - Log Analytics está conectado a los [diagnósticos del grupo de seguridad de red](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json).

**Subredes**: cada subred está asociada a su grupo de seguridad de red correspondiente.

### <a name="data-in-transit"></a>Datos en tránsito
De manera predeterminada, Azure cifra todas las comunicaciones hacia y desde los centros de datos de Azure. Todas las transacciones a Azure Storage mediante Azure Portal se realizan mediante HTTPS.

### <a name="data-at-rest"></a>Datos en reposo

La arquitectura protege los datos en reposo mediante el cifrado, la auditoría de base de datos y otras medidas.

**Azure Storage**: para cumplir con los requisitos de los datos en reposo cifrados, [Azure Storage](https://azure.microsoft.com/services/storage/) usa [Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption).  Esto ayuda a proteger y salvaguardar los datos en apoyo de los compromisos de seguridad y requisitos de cumplimiento de la organización definidos por la directiva NIST SP 800-171.

**Azure Disk Encryption**: [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) aprovecha la función de BitLocker de Windows para proporcionar el cifrado del volumen de discos de datos. La solución se integra con Azure Key Vault para ayudar a controlar y administrar las claves de cifrado del disco.

**Azure SQL Database**: la instancia de Azure SQL Database usa las siguientes medidas de seguridad de base de datos:
-   La [autenticación y autorización de AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) permite la administración de identidades de usuarios de bases de datos y otros servicios de Microsoft en una ubicación central.
-   La [auditoría de bases de datos SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started) realiza un seguimiento de eventos de bases de datos y los escribe en un registro de auditoría de una cuenta de almacenamiento de Azure.
-   Azure SQL Database está configurado para usar el [cifrado de datos transparente](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), que realiza el cifrado y descifrado en tiempo real de la base de datos, las copias de seguridad asociadas y los archivos de registro de transacciones, para proteger la información en reposo. El cifrado de datos transparente garantiza que los datos almacenados no han estado sujetos a acceso no autorizado.
-   Las [reglas de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impiden el acceso a los servidores de bases de datos hasta que se otorgan los permisos adecuados. Asimismo, otorgan acceso a las bases de datos según la dirección IP de origen de cada solicitud.
-   La [detección de amenazas de SQL](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) permite detectar y responder ante posibles amenazas a medida que se producen, mediante el envío de alertas de seguridad para actividades sospechosas en la base de datos, posibles puntos vulnerables, ataques por inyección de código SQL y patrones anómalos de acceso a la base de datos.
-   Las [columnas cifradas](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) garantizan que los datos confidenciales nunca aparezcan como texto no cifrado en el sistema de base de datos. Después de habilitar el cifrado de datos, solo las aplicaciones cliente o los servidores de aplicaciones con acceso a las claves pueden acceder a los datos de texto no cifrado.
- El [enmascaramiento dinámico de datos de SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limita la exposición de los datos confidenciales al enmascarar los datos para usuarios o aplicaciones sin privilegios. El enmascaramiento dinámico de datos puede detectar de forma automática datos potencialmente confidenciales y sugerir las máscaras adecuadas que se pueden aplicar. Esto contribuye a reducir el acceso de forma que los datos confidenciales no salen de la base de datos por medio de acceso no autorizado. Los clientes son responsables de ajustar la configuración del enmascaramiento dinámico de datos para cumplir con el esquema de la base de datos.

### <a name="identity-management"></a>Administración de identidades
Las siguientes tecnologías proporcionan funcionalidades de administración del acceso a datos confidenciales en el entorno de Azure:
-   [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) es el directorio multiinquilino basado en la nube y el servicio de administración de identidades de Microsoft. Todos los usuarios de la solución se crean en Azure Active Directory, incluidos los usuarios que acceden a Azure SQL Database.
-   La autenticación para acceder a la aplicación se realiza con Azure Active Directory. Para obtener más información, consulte [Integración de aplicaciones con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications). Además, el cifrado de columnas de la base de datos usa Azure Active Directory para autenticar la aplicación en Azure SQL Database. Para más información, consulte cómo [proteger los datos confidenciales en SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   El [control de acceso basado en rol de Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) permite a los administradores especificar permisos de acceso bien definidos para conceder solo el nivel de acceso que los usuarios necesitan para realizar su trabajo. En lugar de dar a cada usuario permisos ilimitados para los recursos de Azure, los administradores pueden permitir solo ciertas acciones para acceder a los datos. El acceso a la suscripción está limitado al administrador de la suscripción.
- [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-getting-started) permite a los clientes minimizar el número de usuarios con acceso a determinada información, como los datos.  Los administradores pueden usar Azure Active Directory Privileged Identity Management para detectar, restringir y supervisar las identidades con privilegios y su acceso a los recursos. Esta funcionalidad también puede utilizarse para aplicar un acceso administrativo a petición, Just-in-Time, cuando sea necesario.
-   [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) detecta posibles puntos vulnerables que afectan a las identidades de una organización, configura respuestas automatizadas si surgen acciones sospechosas relacionadas con esas identidades, investiga incidentes sospechosos y toma las medidas oportunas para resolverlos.

### <a name="security"></a>Seguridad
**Administración de secretos**: la solución utiliza [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para la administración de claves y secretos. Azure Key Vault ayuda a proteger claves criptográficas y secretos usados por servicios y aplicaciones en la nube. Las siguientes funcionalidades de Azure Key Vault ayudan a los clientes a proteger los datos:
- Se configuran directivas de acceso avanzadas según las necesidades.
- Se definen directivas de acceso a Key Vault con los permisos mínimos requeridos para las claves y los secretos.
- Todas las claves y los secretos en Key Vault tienen fechas de expiración.
- Todas las claves de Key Vault están protegidas por módulos de seguridad de hardware especializados. El tipo de clave es una clave RSA de 2048 bits protegida por módulos de seguridad de hardware.
- A todos los usuarios y entidades se les otorgan los permisos mínimos necesarios mediante el control de acceso basado en rol.
- Los registros de diagnóstico de Key Vault están habilitados con un período de retención de al menos 365 días.
- Las operaciones criptográficas permitidas para las claves están restringidas únicamente a las requeridas.

**Azure Security Center**: con [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), los clientes pueden aplicar y administrar de forma centralizada las directivas de seguridad entre cargas de trabajo, limitar la exposición a amenazas y detectar y responder a ataques. Además, Azure Security Center accede a las configuraciones existentes de los servicios de Azure para proporcionar recomendaciones de configuración y servicio que ayuden a mejorar la postura de seguridad y a proteger los datos.

Azure Security Center usa una variedad de funcionalidades de detección para alertar a los clientes de posibles ataques contra sus entornos. Estas alertas contienen información útil acerca de lo que desencadenó la alerta, los recursos objetivo y el origen del ataque. Azure Security Center cuenta con un conjunto de [alertas de seguridad predefinidas](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), que se desencadenan cuando tiene lugar una amenaza o actividad sospechosa. Las [reglas de alertas personalizadas](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) de Azure Security Center permiten a los clientes definir nuevas alertas de seguridad basadas en los datos ya recopilados en el entorno.

Azure Security Center proporciona alertas de seguridad e incidentes clasificados por orden de prioridad, lo que facilita a los clientes la detección y solución de posibles problemas de seguridad. Se genera un [informe de inteligencia de amenazas](https://docs.microsoft.com/azure/security-center/security-center-threat-report) por cada amenaza detectada para ayudar a los equipos de respuesta a incidentes a investigar y corregir las amenazas.

### <a name="logging-and-auditing"></a>Registro y auditoría

Los servicios de Azure proporcionan un registro completo de la actividad de usuario y del sistema, así como de mantenimiento del sistema:
- **Registros de actividad:** [los registros de actividad](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) proporcionan información detallada sobre las operaciones realizadas en los recursos de la suscripción. Los registros de actividad pueden ayudar a determinar el iniciador de una operación, el momento en que se produce y el estado.
- **Registros de diagnóstico:** [los registros de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) incluyen todos los registros emitidos por todos los recursos. Estos registros incluyen registros del sistema de eventos de Windows, registros de Azure Storage, registros de auditoría de Key Vault, y registros de firewall y acceso a Application Gateway. Todos los registros de diagnóstico se escriben en una cuenta de almacenamiento de Azure centralizada y cifrada para su archivado. El usuario puede configurar la retención hasta 730 días para cumplir los requisitos de retención específicos de una organización.

**Log Analytics**: esos registros se consolidan en [Log Analytics](https://azure.microsoft.com/services/log-analytics/) para el procesamiento, el almacenamiento y la creación de informes de panel. Una vez recopilados, los datos se organizan en tablas independientes para cada tipo de datos dentro de las áreas de trabajo de Operations Management Suite, lo que permite que todos los datos se puedan analizar conjuntamente con independencia de su origen. Además, Azure Security Center se integra con Log Analytics, lo que permite a los clientes usar consultas de Log Analytics para acceder a sus datos de eventos de seguridad y combinarlos con datos de otros servicios.

Las siguientes [soluciones de administración](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) de Log Analytics se incluyen como parte de esta arquitectura:
-   [Active Directory Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): la solución Active Directory Health Check evalúa el riesgo y el estado de mantenimiento de los entornos de servidor a intervalos regulares y proporciona una lista clasificada por orden de prioridad de recomendaciones específicas para la infraestructura de servidor implementada.
- [SQL Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): la solución SQL Health Check evalúa el riesgo y el estado de los entornos de servidor a intervalos regulares y proporciona a los clientes una lista prioritaria de recomendaciones específicas para la infraestructura de servidor implementada.
- [Agent Health](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): la solución Agent Health notifica el número de agentes implementados y su distribución geográfica, así como el número de agentes que no responden y el de agentes que envían datos operativos.
-   [Activity Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): la solución Activity Log Analytics ayuda a los clientes a analizar los registros de actividad de Azure de todas las suscripciones de Azure para un cliente.

**Azure Automation**: [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) almacena, ejecuta y administra runbooks. En esta solución, los runbooks ayudan a recopilar registros de Azure SQL Database. La solución [Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking) de Automation permite a los clientes identificar fácilmente los cambios en el entorno.

**Azure Monitor**: [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ayuda a los usuarios a realizar un seguimiento del rendimiento, mantener la seguridad e identificar tendencias y permite a las organizaciones auditar, crear alertas y archivar datos, incluido el seguimiento de las llamadas a API en sus recursos de Azure.

**Application Insights**: [Application Insights](https://docs.microsoft.com/azure/application-insights/) es un servicio de Application Performance Management extensible para desarrolladores web en varias plataformas. Detecta anomalías de rendimiento e incluye herramientas de análisis eficaces que ayudan a diagnosticar problemas y comprender lo que hacen realmente los usuarios con la aplicación. Está diseñado para ayudar a los usuarios a mejorar continuamente el rendimiento y la facilidad de uso.

## <a name="threat-model"></a>Modelo de amenazas

El diagrama de flujo de datos de esta arquitectura de referencia está disponible para su [descarga](https://aka.ms/nist171-analytics-tm) y se encuentra a continuación. El modelo puede ayudar a los clientes a comprender los puntos de riesgo potencial de la infraestructura del sistema al realizar modificaciones.

![Análisis de datos para NIST SP 800-171: modelo de amenazas](images/nist171-analytics-threat-model.png "Análisis de datos para NIST SP 800-171: modelo de amenazas")

## <a name="compliance-documentation"></a>Documentación de cumplimiento
En [Azure Security and Compliance Blueprint: matriz de responsabilidades del cliente para NIST SP 800-171](https://aka.ms/nist171-crm) muestra todos los controles de seguridad que exige NIST SP 800-171. En esta matriz se detalla si la implementación de cada objetivo es responsabilidad de Microsoft, del cliente o de ambos.

En [Azure Security and Compliance Blueprint: matriz de implementación de control de análisis de datos para NIST SP 800-171](https://aka.ms/nist171-analytics-cim) se ofrece información sobre qué controles de NIST SP 800-171 se abordan en la arquitectura de análisis de datos; se incluyen descripciones detalladas de cómo la implementación satisface los requisitos de cada control tratado.


## <a name="guidance-and-recommendations"></a>Instrucciones y recomendaciones
### <a name="vpn-and-expressroute"></a>VPN y ExpressRoute
Debe configurarse un túnel VPN seguro o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) para establecer una conexión segura a los recursos implementados como parte de esta arquitectura de referencia de análisis de datos. Al configurar una VPN o ExpressRoute adecuadamente, los clientes pueden agregar una capa de protección para los datos en tránsito.

Mediante la implementación de un túnel VPN seguro con Azure, se puede crear una conexión privada virtual entre una red local y una red virtual de Azure. Esta conexión tiene lugar a través de Internet y permite a los clientes colocar con seguridad la información en un "túnel" dentro de un vínculo cifrado entre la red del cliente y Azure. La VPN de sitio a sitio es una tecnología segura y madura que empresas de todos los tamaños han implementado durante décadas. Se utiliza [el modo de túnel IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) en esta opción como mecanismo de cifrado.

Dado que el tráfico dentro del túnel VPN atraviesa Internet con una VPN de sitio a sitio, Microsoft ofrece otra opción de conexión aún más segura. Azure ExpressRoute es un vínculo de WAN dedicado entre Azure y una ubicación local o un proveedor de hospedaje de Exchange. Como las conexiones de ExpressRoute no se realizan a través de una conexión a Internet, ofrecen más confiabilidad, más velocidad, menor latencia y mayor seguridad que las conexiones a Internet normales. Además, dado que se trata de una conexión directa del proveedor de telecomunicaciones del cliente, los datos no viajan a través de Internet y, por lo tanto, no están expuestos a ella.

Hay [disponibles](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid) procedimientos recomendados para implementar una red híbrida segura que extienda una red local a Azure.

### <a name="extract-transform-load-process"></a>Proceso de extracción, transformación y carga
[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) puede cargar datos en Azure SQL Database sin necesidad de una herramienta independiente para extraer, transformar, cargar o importar. PolyBase permite el acceso a los datos mediante consultas T-SQL. Con PolyBase se puede usar la inteligencia empresarial y la pila de análisis de Microsoft, así como herramientas de terceros compatibles con SQL Server.

### <a name="azure-active-directory-setup"></a>Configuración de Azure Active Directory
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) es esencial para la administración de la implementación y la concesión de acceso a las personas que interactúan con el entorno. Se puede integrar una instancia de Windows Server Active Directory con Azure Active Directory en [cuatro clics](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-get-started-express). Los clientes también pueden enlazar la infraestructura de Active Directory implementada (controladores de dominio) con una instancia de Azure Active Directory existente al convertir la infraestructura de Active Directory implementada en un subdominio de un bosque de Azure Active Directory.

## <a name="disclaimer"></a>Renuncia de responsabilidades

 - Este documento es meramente informativo. MICROSOFT NO EFECTÚA NINGÚN TIPO DE GARANTÍA, YA SEA EXPRESA, IMPLÍCITA O ESTATUTARIA, SOBRE LA INFORMACIÓN INCLUIDA EN EL PRESENTE DOCUMENTO. Este documento se proporciona "tal cual". La información y las opiniones expresadas en este documento, como las direcciones URL y otras referencias a sitios web de Internet, pueden cambiar sin previo aviso. Los clientes que lean este documento se atienen a las consecuencias de usarlo.
 - Este documento no proporciona a los clientes ningún derecho legal sobre ninguna propiedad intelectual de ningún producto o solución de Microsoft.
 - Los clientes pueden copiar y usar este documento con fines internos y de referencia.
 - Algunas de las recomendaciones de este documento pueden provocar un aumento del uso de datos, de la red o de los recursos de procesos en Azure, lo que podría incrementar los costos de las licencias o las suscripciones de Azure.
 - Esta arquitectura está diseñada para servir como base para que los clientes puedan ajustarse a sus requisitos específicos y no debe usarse tal cual en un entorno de producción.
 - Este documento se ha desarrollado como referencia y no debe usarse para definir todos los medios por los que un cliente puede cumplir normas y requisitos de cumplimiento específicos. Los clientes deben buscar apoyo legal de su organización sobre las implementaciones de cliente aprobadas.
