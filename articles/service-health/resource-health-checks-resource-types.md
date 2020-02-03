---
title: Tipos de recursos com suporte por meio de Azure Resource Health | Microsoft Docs
description: Tipos de recursos com suporte por meio do Azure Resource Health
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: 3a61c8b90f4dc5e52a875488afb5374e3d6b7ce4
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717062"
---
# <a name="resource-types-and-health-checks-in-azure-resource-health"></a>Tipos de recursos e verificações de integridade no Azure Resource Health
Abaixo está uma lista completa de todas as verificações executadas por meio do Resource Health por tipos de recursos.

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers
|Verificações executadas|
|---|
|<ul><li>O servidor está em execução?</li><li>O servidor ficou sem memória?</li><li>O servidor está sendo inicializado?</li><li>O servidor está sendo recuperado?</li></ul>|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service
|Verificações executadas|
|---|
|<ul><li>O serviço de gerenciamento de API está em funcionamento?</li></ul>|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts
|Verificações executadas|
|---|
|<ul><li>A conta do lote está em execução?</li><li>A cota do pool foi excedida para esta conta do lote?</li></ul>|

## <a name="microsoftcacheredisredis"></a>Microsoft.CacheRedis/Redis
|Verificações executadas|
|---|
|<ul><li>Todos os nós de cache estão em funcionamento?</li><li>O cache pode ser alcançado de dentro do datacenter?</li><li>O cache atingiu o número máximo de conexões?</li><li> O cache esgotou sua memória disponível? </li><li>O cache está apresentando um grande número de falhas de página?</li><li>O cache está sob carga pesada?</li></ul>|

## <a name="microsoftcdnprofile"></a>Microsoft.CDN/profile
|Verificações executadas|
|---|
|<ul> <li>O portal complementar está acessível para operações de configuração da CDN?</li><li>Há problemas de entrega em andamento com os pontos de extremidade da CDN?</li><li>Os usuários podem alterar a configuração dos recursos da CDN?</li><li>As alterações de configuração estão se propagando na taxa esperada?</li><li>Os usuários podem gerenciar a configuração da CDN usando o portal do Azure, o PowerShell ou a API?</li> </ul>|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.classiccompute/virtualmachines
|Verificações executadas|
|---|
|<ul><li>O servidor host está em execução?</li><li>A inicialização do sistema operacional do host foi concluída?</li><li>O contêiner da máquina virtual foi provisionado e ligado?</li><li>Há conectividade de rede entre o host e a conta de armazenamento?</li><li>A inicialização do sistema operacional convidado foi concluída?</li><li>Há manutenção planejada em andamento?</li></ul>|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.cognitiveservices/accounts
|Verificações executadas|
|---|
|<ul><li>A conta pode ser alcançada de dentro do datacenter?</li><li>O provedor de recursos de serviços cognitivas está disponível?</li><li>O serviço cognitiva está disponível na região apropriada?</li><li>Operações de leitura podem ser executadas na conta de armazenamento que contém os metadados do recurso?</li><li>A cota de chamada à API foi atingida?</li><li>O limite de leitura de chamada à API foi atingido?</li></ul>|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.compute/virtualmachines
|Verificações executadas|
|---|
|<ul><li>O servidor que hospeda essa máquina virtual está em funcionamento?</li><li>A inicialização do sistema operacional do host foi concluída?</li><li>O contêiner da máquina virtual foi provisionado e ligado?</li><li>Há conectividade de rede entre o host e a conta de armazenamento?</li><li>A inicialização do sistema operacional convidado foi concluída?</li><li>Há manutenção planejada em andamento?</li></ul>|

## <a name="microsoftdatafactoryfactories"></a>Microsoft. datafactory/fábricas
|Verificações executadas|
|---|
|<ul><li>Há falhas de execução de pipeline?</li><li>O cluster está hospedando o Data Factory íntegro?</li></ul>|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.datalakeanalytics/accounts
|Verificações executadas|
|---|
|<ul><li>Os usuários já tiveram problemas ao enviar ou listar seus trabalhos de Data Lake Analytics?</li><li>Data Lake Analytics trabalhos não puderam ser concluídos devido a erros do sistema?</li></ul>|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.datalakestore/accounts
|Verificações executadas|
|---|
|<ul><li>Os usuários enfrentaram problemas ao carregar dados para Data Lake Store?</li><li>Os usuários enfrentavam problemas ao baixar dados de Data Lake Store?</li></ul>|

## <a name="microsoftdatamigrationservices"></a>Microsoft.datamigration/services
|Verificações executadas|
|---|
|<ul><li>O serviço de migração de banco de dados falhou ao provisionar?</li><li>O serviço de migração de banco de dados parou devido à inatividade ou à solicitação do usuário?</li></ul>|

## <a name="microsoftdatashareaccounts"></a>Microsoft. DataShare/accounts
|Verificações executadas|
|---|
|<ul><li>A conta de compartilhamento de dados está em funcionamento?</li><li>O cluster está hospedando o compartilhamento de dados disponível?</li></ul>|

## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers
|Verificações executadas|
|---|
|<ul><li>O servidor está indisponível devido à manutenção?</li><li>O servidor está indisponível devido à reconfiguração?</li></ul>|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers
|Verificações executadas|
|---|
|<ul><li>O servidor está indisponível devido à manutenção?</li><li>O servidor está indisponível devido à reconfiguração?</li></ul>|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers
|Verificações executadas|
|---|
|<ul><li>O servidor está indisponível devido à manutenção?</li><li>O servidor está indisponível devido à reconfiguração?</li></ul>|

## <a name="microsoftdevicesiothubs"></a>Microsoft.devices/iothubs
|Verificações executadas|
|---|
|<ul><li>O Hub IoT está em funcionamento?</li></ul>|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.documentdb/databaseAccounts
|Verificações executadas|
|---|
|<ul><li>Alguma solicitação de banco de dados ou coleção não foi atendida devido a uma indisponibilidade de serviço Azure Cosmos DB?</li><li>Alguma solicitação de documento não foi atendida devido a uma indisponibilidade do serviço de Azure Cosmos DB?</li></ul>|

## <a name="microsofteventhubnamespaces"></a>Microsoft.eventhub/namespaces
|Verificações executadas|
|---|
|<ul><li>O namespace dos hubs de eventos está apresentando erros gerados pelo usuário?</li><li>O namespace dos hubs de eventos está sendo atualizado no momento?</li></ul>|

## <a name="microsofthdinsightclusters"></a>Microsoft.hdinsight/clusters
|Verificações executadas|
|---|
|<ul><li>Os serviços principais estão disponíveis no cluster HDInsight?</li><li>O cluster HDInsight pode acessar a chave para criptografia BYOK em repouso?</li></ul>|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults
|Verificações executadas|
|---|
|<ul><li>As solicitações para o cofre de chaves falharam devido a problemas da plataforma do Azure keyvault?</li><li>As solicitações para o cofre de chaves estão sendo limitadas devido a muitas solicitações feitas pelo cliente?</li></ul>|

## <a name="microsoftmachinelearningwebservices"></a>Microsoft.MachineLearning/webServices
|Verificações executadas|
|---|
|<ul><li>O serviço Web está funcionando?</li></ul>|

## <a name="microsoftmediamediaservices"></a>Microsoft. Media/mediaservices
|Verificações executadas|
|---|
|<ul><li>O serviço de mídia está em execução?</li></ul>|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.network/applicationgateways
|Verificações executadas|
|---|
|<ul><li>O desempenho do gateway de aplicativo foi degradado?</li><li>O gateway de aplicativo está disponível?</li></ul>|

## <a name="microsoftnetworkconnections"></a>Microsoft.network/connections
|Verificações executadas|
|---|
|<ul><li>O túnel VPN está conectado?</li><li>Há conflitos de configuração na conexão?</li><li>As chaves pré-compartilhadas estão configuradas corretamente?</li><li>O dispositivo VPN local está acessível?</li><li>Há incompatibilidades na política de segurança de IPSec/IKE?</li><li>A conexão VPN S2S foi provisionada corretamente ou está em um estado de falha?</li><li>A conexão VNET para VNET foi provisionada corretamente ou está em um estado de falha?</li></ul>|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.network/expressroutecircuits
|Verificações executadas|
|---|
|<ul><li>O circuito do ExpressRoute está íntegro?</li></ul>|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft.network/frontdoors
|Verificações executadas|
|---|
|<ul><li>Os back-ends de porta frontal estão respondendo com erros nas investigações de integridade?</li><li>As alterações de configuração estão atrasadas?</li></ul>|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft. Network/balancers
|Verificações executadas|
|---|
|<ul><li>Os pontos de extremidade de balanceamento de carga estão disponíveis?</li></ul>|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.network/virtualNetworkGateways
|Verificações executadas|
|---|
|<ul><li>O gateway de VPN é acessível pela Internet?</li><li>O gateway de VPN está no modo de espera?</li><li>O serviço VPN está em execução no gateway?</li></ul>|

## <a name="microsoftnotificationhubsnamespace"></a>Microsoft.NotificationHubs/namespace
|Verificações executadas|
|---|
|<ul><li>As operações de tempo de execução, como o registro, a instalação ou o envio, podem ser executadas no namespace?</li></ul>|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.operationalinsights/workspaces
|Verificações executadas|
|---|
|<ul><li>Há atrasos de indexação para o espaço de trabalho?</li></ul>|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/Capacities
|Verificações executadas|
|---|
|<ul><li>O recurso de capacidade está em funcionamento?</li><li>Todas as cargas de trabalho estão em funcionamento?</li></ul>|

## <a name="microsoftpowerbiworkspacecollections"></a>Microsoft.PowerBI/workspaceCollections
|Verificações executadas|
|---|
|<ul><li>O sistema operacional do host está funcionando?</li><li>O workspacecollection pode ser acessado de fora do datacenter?</li><li>O provedor de recursos de Power BI está disponível?</li><li>O serviço Power BI está disponível na região apropriada?</li></ul>|

## <a name="microsoftsearchsearchservices"></a>Microsoft.search/searchServices
|Verificações executadas|
|---|
|<ul><li>As operações de diagnóstico podem ser executadas no cluster?</li></ul>|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces
|Verificações executadas|
|---|
|<ul><li>Os clientes estão enfrentando erros de barramento de serviço gerados pelo usuário?</li><li>Os usuários estão enfrentando um aumento em erros transitórios devido a uma atualização de namespace do barramento de serviço?</li></ul>|

## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft. SQL/managedInstances/bancos de dados
|Verificações executadas|
|---|
|<ul><li>O banco de dados está em execução?</li></ul>|

## <a name="microsoftsqlserverdatabases"></a>Microsoft. SQL/Server/bancos de dados
|Verificações executadas|
|---|
|<ul><li>Há logons no banco de dados?</li></ul>|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts
|Verificações executadas|
|---|
|<ul><li>As solicitações para ler dados da conta de armazenamento falharam devido a problemas da plataforma de armazenamento do Azure?</li><li>As solicitações para gravar dados na conta de armazenamento falham devido a problemas da plataforma de armazenamento do Azure?</li><li>O cluster de armazenamento em que a conta de armazenamento reside não está disponível?</li></ul>|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs
|Verificações executadas|
|---|
|<ul><li>Todos os hosts em que o trabalho está sendo executado e em execução?</li><li>O trabalho não pôde ser iniciado?</li><li>Há atualizações em tempo de execução em andamento?</li><li>O trabalho está em um estado esperado (por exemplo, em execução ou parado pelo cliente)?</li><li>O trabalho encontrou exceções de memória insuficiente?</li><li>Há atualizações de computação agendadas em andamento?</li><li>O Gerenciador de execução (plano de controle) está disponível?</li></ul>|

## <a name="microsoftwebserverfarms"></a>Microsoft.web/serverFarms
|Verificações executadas|
|---|
|<ul><li>O servidor host está em execução?</li><li>Serviços de Informações da Internet está em execução?</li><li>O balanceador de carga está em execução?</li><li>O plano do serviço de aplicativo pode ser acessado de dentro do datacenter?</li><li>A conta de armazenamento está hospedando o conteúdo dos sites para o serverFarm disponível?</li></ul>|

## <a name="microsoftwebsites"></a>Microsoft.web/sites
|Verificações executadas|
|---|
|<ul><li>O servidor host está em execução?</li><li>O servidor de informações da Internet está em execução?</li><li>O balanceador de carga está em execução?</li><li>O aplicativo Web pode ser acessado de dentro do datacenter?</li><li>A conta de armazenamento está hospedando o conteúdo do site disponível?</li></ul>|

## <a name="next-steps"></a>Próximos Passos
-  Consulte a introdução ao painel de saúde do [serviço Azure](service-health-overview.md) e [introdução à Azure Resource Health](resource-health-overview.md) para saber mais sobre eles. 
-  [Perguntas frequentes sobre a Saúde dos Recursos Azure](resource-health-faq.md)
- Configure alertas para que você seja notificado sobre problemas de integridade. Para mais informações, consulte [Configurar Alertas para eventos de saúde de serviço.](../azure-monitor/platform/alerts-activity-log-service-notifications.md) 
