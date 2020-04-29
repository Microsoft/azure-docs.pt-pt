---
title: Tipos de recursos suportados através da Saúde dos Recursos Azure [ Microsoft Docs
description: Tipos de recursos suportados através da saúde dos recursos azure
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: 5cc80147730fdc97b1181690f6e70fc538d4afcc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80478900"
---
# <a name="resource-types-and-health-checks-in-azure-resource-health"></a>Tipos de recursos e verificações de saúde na saúde dos recursos do Azure
Abaixo está uma lista completa de todos os controlos executados através da saúde dos recursos por tipos de recursos.

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servidores
|Cheques Executados|
|---|
|<ul><li>O servidor está a funcionar?</li><li>O servidor está sem memória?</li><li>O servidor está a ligar?</li><li>O servidor está a recuperar?</li></ul>|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service
|Cheques Executados|
|---|
|<ul><li>O serviço de Gestão Api está a funcionar?</li></ul>|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts
|Cheques Executados|
|---|
|<ul><li>A conta do Lote está a funcionar?</li><li>A quota da piscina foi ultrapassada para esta conta de lote?</li></ul>|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/Redis
|Cheques Executados|
|---|
|<ul><li>Todos os nós cache estão a funcionar?</li><li>O Cache pode ser alcançado a partir do centro de dados?</li><li>O Cache atingiu o número máximo de ligações?</li><li> A cache esgotou a sua memória disponível? </li><li>O Cache está a passar por um elevado número de falhas de página?</li><li>O Cache está sob carga pesada?</li></ul>|

## <a name="microsoftcdnprofile"></a>Microsoft.CDN/perfil
|Cheques Executados|
|---|
|<ul> <li>O portal suplementar é acessível para operações de configuração de CDN?</li><li>Existem problemas de entrega em curso com os pontos finais da CDN?</li><li>Os utilizadores podem alterar a configuração dos seus recursos CDN?</li><li>As alterações de configuração estão a propagar-se ao ritmo esperado?</li><li>Os utilizadores podem gerir a configuração CDN utilizando o portal Azure, PowerShell ou a API?</li> </ul>|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.classiccompute/virtualmachines
|Cheques Executados|
|---|
|<ul><li>O servidor do hospedeiro está a funcionar?</li><li>O arranque do osso hospedeiro já está concluído?</li><li>O contentor virtual está aprovisionado e alimentado?</li><li>Existe conectividade de rede entre o hospedeiro e a conta de armazenamento?</li><li>O arranque do soo convidado já terminou?</li><li>Há manutenção planeada em curso?</li><li>O hardware do hospedeiro está degradado e previsto que falhe em breve?</li></ul>|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.cognitiveservices/accounts
|Cheques Executados|
|---|
|<ul><li>A conta pode ser alcançada a partir do centro de dados?</li><li>O Fornecedor de Recursos Cognitivos está disponível?</li><li>O Serviço Cognitivo está disponível na região apropriada?</li><li>As operações de leitura podem ser realizadas na conta de armazenamento que detém os metadados de recursos?</li><li>A quota de chamada da API foi atingida?</li><li>O limite de leitura da chamada da API já foi atingido?</li></ul>|

## <a name="microsoftcomputehostgroupshosts"></a>Microsoft.compute/hostgroups/hosts
|Cheques Executados|
|---|
|<ul><li>É o anfitrião em funcionamento</li><li>O hardware do hospedeiro está degradado?</li><li>O negócio do anfitrião está localizado?</li><li>O serviço de hardware do anfitrião curou-se para diferentes hardware?</li></ul>|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.compute/virtualmachines
|Cheques Executados|
|---|
|<ul><li>O servidor está a alojar esta máquina virtual a funcionar?</li><li>O arranque do osso hospedeiro já está concluído?</li><li>O contentor virtual está aprovisionado e alimentado?</li><li>Existe conectividade de rede entre o hospedeiro e a conta de armazenamento?</li><li>O arranque do soo convidado já terminou?</li><li>Há manutenção planeada em curso?</li><li>O hardware do hospedeiro está degradado e previsto que falhe em breve?</li></ul>|

## <a name="microsoftdatafactoryfactories"></a>Microsoft.datafactory/fábricas
|Cheques Executados|
|---|
|<ul><li>Houve falhas na execução do gasoduto?</li><li>O cluster que acolhe a Fábrica de Dados é saudável?</li></ul>|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.datalakeanalytics/contas
|Cheques Executados|
|---|
|<ul><li>Os utilizadores já tiveram problemas em submeter ou listar os seus trabalhos de Data Lake Analytics?</li><li>Os trabalhos da Data Lake Analytics não conseguem ser concluídos devido a erros do sistema?</li></ul>|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.datalakestore/contas
|Cheques Executados|
|---|
|<ul><li>Os utilizadores já tiveram problemas em enviar dados para data lake store?</li><li>Os utilizadores já tiveram problemas em descarregar dados da Data Lake Store?</li></ul>|

## <a name="microsoftdatamigrationservices"></a>Microsoft.datamigration/services
|Cheques Executados|
|---|
|<ul><li>O serviço de migração da base de dados não foi prestado?</li><li>O serviço de migração da base de dados parou devido a inatividade ou pedido do utilizador?</li></ul>|

## <a name="microsoftdatashareaccounts"></a>Microsoft.DataShare/contas
|Cheques Executados|
|---|
|<ul><li>A conta Data Share está a funcionar?</li><li>O cluster que acolhe a Partilha de Dados está disponível?</li></ul>|

## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servidores
|Cheques Executados|
|---|
|<ul><li>O servidor não está disponível devido à manutenção?</li><li>O servidor não está disponível devido à reconfiguração?</li></ul>|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servidores
|Cheques Executados|
|---|
|<ul><li>O servidor não está disponível devido à manutenção?</li><li>O servidor não está disponível devido à reconfiguração?</li></ul>|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servidores
|Cheques Executados|
|---|
|<ul><li>O servidor não está disponível devido à manutenção?</li><li>O servidor não está disponível devido à reconfiguração?</li></ul>|

## <a name="microsoftdevicesiothubs"></a>Microsoft.devices/iothubs
|Cheques Executados|
|---|
|<ul><li>O centro de IoT está a funcionar?</li></ul>|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.documentdb/databaseAccounts
|Cheques Executados|
|---|
|<ul><li>Houve algum pedido de base de dados ou recolha não servido devido a uma indisponibilidade de serviço Azure Cosmos DB?</li><li>Houve algum pedido de documento não servido devido a uma indisponibilidade de serviço Azure Cosmos DB?</li></ul>|

## <a name="microsofteventhubnamespaces"></a>Microsoft.eventhub/espaços de nome
|Cheques Executados|
|---|
|<ul><li>O espaço de nome do Event Hubs está a experimentar erros gerados pelo utilizador?</li><li>O espaço de nome saque do Event Hubs está atualmente a ser atualizado?</li></ul>|

## <a name="microsofthdinsightclusters"></a>Microsoft.hdinsight/clusters
|Cheques Executados|
|---|
|<ul><li>Os serviços centrais estão disponíveis no cluster HDInsight?</li><li>O cluster HDInsight pode aceder à chave para encriptação BYOK em repouso?</li></ul>|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/cofres
|Cheques Executados|
|---|
|<ul><li>Os pedidos para o cofre chave falham devido a problemas na plataforma Azure KeyVault?</li><li>Os pedidos para o cofre chave estão a ser estrangulados devido a muitos pedidos feitos pelo cliente?</li></ul>|

## <a name="microsoftmachinelearningwebservices"></a>Microsoft.MachineLearning/webServices
|Cheques Executados|
|---|
|<ul><li>O serviço web está a funcionar?</li></ul>|

## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices
|Cheques Executados|
|---|
|<ul><li>O serviço de mídia está a funcionar?</li></ul>|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.network/applicationgateways
|Cheques Executados|
|---|
|<ul><li>O desempenho do Gateway de Aplicação está degradado?</li><li>O Gateway de Aplicação está disponível?</li></ul>|

## <a name="microsoftnetworkconnections"></a>Microsoft.network/connections
|Cheques Executados|
|---|
|<ul><li>O túnel VPN está ligado?</li><li>Há conflitos de configuração na ligação?</li><li>As chaves pré-partilhadas estão devidamente configuradas?</li><li>O dispositivo VPN no local é acessível?</li><li>Existem incompatibilidades na política de segurança IPSec/IKE?</li><li>A ligação VPN S2S está devidamente aprovisionada ou em estado falhado?</li><li>A ligação VNET-to-VNET está devidamente aprovisionada ou em estado falhado?</li></ul>|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.network/expressroutecircuits
|Cheques Executados|
|---|
|<ul><li>O circuito ExpressRoute é saudável?</li></ul>|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft.network/frontdoors
|Cheques Executados|
|---|
|<ul><li>Os backends da Porta da Frente estão a responder com erros às sondas de saúde?</li><li>As alterações de configuração estão atrasadas?</li></ul>|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.network/LoadBalancers
|Cheques Executados|
|---|
|<ul><li>Os pontos finais de equilíbrio de carga estão disponíveis?</li></ul>|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.network/virtualNetworkGateways
|Cheques Executados|
|---|
|<ul><li>O gateway VPN é acessível a partir da internet?</li><li>O VPN Gateway está em modo de espera?</li><li>O serviço VPN está a funcionar na porta de entrada?</li></ul>|

## <a name="microsoftnotificationhubsnamespace"></a>Microsoft.NotificationHubs/espaço de nome
|Cheques Executados|
|---|
|<ul><li>Podem ser realizadas operações de tempo de execução como registo, instalação ou envio no espaço de nome?</li></ul>|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.operationalinsights/espaços de trabalho
|Cheques Executados|
|---|
|<ul><li>Há atrasos de indexação para o espaço de trabalho?</li></ul>|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/Capacities
|Cheques Executados|
|---|
|<ul><li>O recurso de capacidade está a funcionar?</li><li>Todas as cargas de trabalho estão a funcionar?</li></ul>|

## <a name="microsoftpowerbiworkspacecollections"></a>Microsoft.PowerBI/workspaceCollections
|Cheques Executados|
|---|
|<ul><li>O hospedeiro está a funcionar?</li><li>O espaço de trabalhoCollection é acessível de fora do datacenter?</li><li>O Power BI Resource Provider está disponível?</li><li>O Serviço Power BI está disponível na região apropriada?</li></ul>|

## <a name="microsoftsearchsearchservices"></a>Microsoft.search/searchServices
|Cheques Executados|
|---|
|<ul><li>As operações de diagnóstico podem ser realizadas no cluster?</li></ul>|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/espaços de nome
|Cheques Executados|
|---|
|<ul><li>Os clientes que experimentam erros gerados pelo utilizador service bus?</li><li>Os utilizadores estão a sofrer um aumento de erros transitórios devido a uma atualização do espaço de nome do Service Bus?</li></ul>|

## <a name="microsoftservicefabricclusters"></a>Microsoft.ServiceFabric/clusters
|Cheques Executados|
|---|
|<ul><li>O cluster service Fabric está a funcionar?</li><li>O cluster Service Fabric pode ser gerido através do Gestor de Recursos Azure?</li></ul>|

## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft.SQL/managedInstances/bases de dados
|Cheques Executados|
|---|
|<ul><li>A base de dados está a funcionar?</li></ul>|

## <a name="microsoftsqlserverdatabases"></a>Microsoft.SQL/Server/bases de dados
|Cheques Executados|
|---|
|<ul><li>Houve registos na base de dados?</li></ul>|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts
|Cheques Executados|
|---|
|<ul><li>Os pedidos para ler dados da conta de Armazenamento estão a falhar devido a problemas na plataforma de armazenamento do Azure?</li><li>Os pedidos para escrever dados para a conta de Armazenamento estão a falhar devido a problemas na plataforma de armazenamento do Azure?</li><li>O cluster de armazenamento onde a conta de armazenamento reside indisponível?</li></ul>|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs
|Cheques Executados|
|---|
|<ul><li>Todos os anfitriões onde o trabalho está a funcionar?</li><li>O trabalho não podia começar?</li><li>Há atualizações em curso?</li><li>O trabalho está num estado esperado (por exemplo, correr ou parar pelo cliente)?</li><li>O trabalho foi encontrado fora das exceções à memória?</li><li>Há atualizações de cálculo agendadas em curso?</li><li>O Gestor de Execução (plano de controlo) está disponível?</li></ul>|

## <a name="microsoftwebserverfarms"></a>Microsoft.web/serverFarms
|Cheques Executados|
|---|
|<ul><li>O servidor do hospedeiro está a funcionar?</li><li>Os Serviços de Informação da Internet estão a funcionar?</li><li>O equilibrador de carga está a funcionar?</li><li>O Plano de Serviço de Aplicações pode ser alcançado a partir do centro de dados?</li><li>A conta de armazenamento está disponível para o conteúdo dos sites para o servidorFarm??</li></ul>|

## <a name="microsoftwebsites"></a>Microsoft.web/sites
|Cheques Executados|
|---|
|<ul><li>O servidor do hospedeiro está a funcionar?</li><li>O servidor de Informação de Internet está a funcionar?</li><li>O equilibrador de carga está a funcionar?</li><li>A Web App pode ser contactada a partir do centro de dados?</li><li>A conta de armazenamento está disponível?</li></ul>|

## <a name="next-steps"></a>Passos Seguintes
-  Consulte a introdução ao painel de saúde do [serviço Azure](service-health-overview.md) e [introdução à Azure Resource Health](resource-health-overview.md) para saber mais sobre eles. 
-  [Perguntas frequentes sobre a Saúde dos Recursos Azure](resource-health-faq.md)
- Instale alertas para que seja notificado de problemas de saúde. Para mais informações, consulte [Configurar Alertas para eventos de saúde de serviço.](../azure-monitor/platform/alerts-activity-log-service-notifications.md) 
