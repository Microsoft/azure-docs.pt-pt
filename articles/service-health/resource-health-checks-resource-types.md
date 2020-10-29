---
title: Tipos de Recursos Suportados através da Azure Resource Health Microsoft Docs
description: Tipos de Recursos Suportados através da Saúde dos Recursos Azure
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: 79f979b55366d29ca8cf22a1e65ebdcc557ef594
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92899488"
---
# <a name="resource-types-and-health-checks-in-azure-resource-health"></a>Tipos de recursos e verificações de saúde na saúde dos recursos Azure
Abaixo está uma lista completa de todos os cheques executados através da saúde de recursos por tipos de recursos.

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servidores
|Verificações executadas|
|---|
|<ul><li>O servidor está a funcionar?</li><li>O servidor está sem memória?</li><li>O servidor está a começar?</li><li>O servidor está a recuperar?</li></ul>|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service
|Verificações executadas|
|---|
|<ul><li>O serviço de gestão Api está a funcionar?</li></ul>|

## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/primavera
|Verificações executadas|
|---|
|<ul><li>A instância da Nuvem de primavera de Azure está disponível?</li></ul>|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batcontas ch/batch
|Verificações executadas|
|---|
|<ul><li>A conta do Batch está a funcionar?</li><li>A quota de piscina foi excedida para esta conta de lote?</li></ul>|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/Redis
|Verificações executadas|
|---|
|<ul><li>Todos os nós cache estão a funcionar?</li><li>A Cache pode ser alcançada a partir do centro de dados?</li><li>A Cache atingiu o número máximo de ligações?</li><li> A cache esgotou a memória disponível? </li><li>A Cache está a ter um elevado número de falhas de página?</li><li>O Cache está sob carga pesada?</li></ul>|

## <a name="microsoftcdnprofile"></a>Microsoft.CDN/perfil
|Verificações executadas|
|---|
|<ul> <li>O portal suplementar está acessível para operações de configuração cdn?</li><li>Existem problemas de entrega em curso com os pontos finais da CDN?</li><li>Os utilizadores podem alterar a configuração dos seus recursos CDN?</li><li>As alterações de configuração estão a propagar-se à taxa esperada?</li><li>Os utilizadores podem gerir a configuração do CDN utilizando o portal Azure, PowerShell ou a API?</li> </ul>|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.classiccompute/virtualmachines
|Verificações executadas|
|---|
|<ul><li>O servidor anfitrião está a funcionar?</li><li>O arranque do hospedeiro OS já foi concluído?</li><li>O recipiente da máquina virtual é a provisionado e alimentado?</li><li>Existe conectividade de rede entre o hospedeiro e a conta de armazenamento?</li><li>O arranque do so convidado terminou?</li><li>Há manutenção planeada?</li><li>O hardware do anfitrião está degradado e prevê-se que falhe em breve?</li></ul>|

## <a name="microsoftclassiccomputedomainnames"></a>Microsoft.classiccompute/domainnames
|Verificações executadas|
|---|
|<ul><li>A implantação de slots de produção é saudável em todas as instâncias de função?</li><li>O papel é saudável em todos os seus casos de VM?</li><li>Qual é o estado de saúde de cada VM dentro de um papel de um serviço de nuvem?</li><li>A alteração de estado em VM deveu-se à operação iniciada pela plataforma ou pelo cliente?</li><li>O arranque do so convidado terminou?</li><li>Há manutenção planeada?</li><li>O hardware do anfitrião está degradado e prevê-se que falhe em breve?</li><li>[Saiba mais](../cloud-services/resource-health-for-cloud-services.md) sobre Verificações Executadas</li></ul>|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.cognitiveservices/contas
|Verificações executadas|
|---|
|<ul><li>A conta pode ser alcançada a partir do centro de dados?</li><li>O Fornecedor de Recursos de Serviços Cognitivos está disponível?</li><li>O Serviço Cognitivo está disponível na região adequada?</li><li>As operações de leitura podem ser realizadas na conta de armazenamento que guarda os metadados de recursos?</li><li>A quota de chamada da API foi atingida?</li><li>O limite de leitura da chamada da API foi atingido?</li></ul>|

## <a name="microsoftcomputehostgroupshosts"></a>Microsoft.compute/hostgroups/hosts
|Verificações executadas|
|---|
|<ul><li>O anfitrião está a funcionar?</li><li>O hardware do anfitrião está degradado?</li><li>O anfitrião está fechado?</li><li>O serviço de hardware do anfitrião curou-se em hardware diferente?</li></ul>|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.compute/virtualmachines
|Verificações executadas|
|---|
|<ul><li>O servidor que está a montar esta máquina virtual está a funcionar?</li><li>O arranque do hospedeiro OS já foi concluído?</li><li>O recipiente da máquina virtual é a provisionado e alimentado?</li><li>Existe conectividade de rede entre o hospedeiro e a conta de armazenamento?</li><li>O arranque do so convidado terminou?</li><li>Há manutenção planeada?</li><li>O hardware do anfitrião está degradado e prevê-se que falhe em breve?</li></ul>|

## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters
|Verificações executadas|
|---|
|<ul><li>O aglomerado está a funcionar?</li><li>Os serviços essenciais estão disponíveis no cluster?</li><li>Todos os nós de agrupamento estão prontos?</li><li>O serviço principal é atual e válido?</li></ul>|

## <a name="microsoftdatafactoryfactories"></a>Microsoft.datafactory/fábricas
|Verificações executadas|
|---|
|<ul><li>Houve falhas no gasoduto?</li><li>O cluster que acolhe a Fábrica de Dados é saudável?</li></ul>|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.datalakeanalytics/accounts
|Verificações executadas|
|---|
|<ul><li>Os utilizadores tiveram problemas em submeter ou listar os seus empregos de Data Lake Analytics?</li><li>Os trabalhos da Data Lake Analytics não são capazes de ser concluídos devido a erros do sistema?</li></ul>|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.datalakestore/contas
|Verificações executadas|
|---|
|<ul><li>Os utilizadores tiveram problemas em enviar dados para a Data Lake Store?</li><li>Os utilizadores tiveram problemas em descarregar dados da Data Lake Store?</li></ul>|

## <a name="microsoftdatamigrationservices"></a>Microsoft.datamigration/services
|Verificações executadas|
|---|
|<ul><li>O serviço de migração da base de dados falhou na prestação?</li><li>O serviço de migração da base de dados parou devido à inatividade ou ao pedido do utilizador?</li></ul>|

## <a name="microsoftdatashareaccounts"></a>Microsoft.DataShare/contas
|Verificações executadas|
|---|
|<ul><li>A conta De partilha de dados está a funcionar?</li><li>O cluster que acolhe a Partilha de Dados está disponível?</li></ul>|

## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servidores
|Verificações executadas|
|---|
|<ul><li>O servidor está indisponível devido à manutenção?</li><li>O servidor está indisponível devido à reconfiguração?</li></ul>|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servidores
|Verificações executadas|
|---|
|<ul><li>O servidor está indisponível devido à manutenção?</li><li>O servidor está indisponível devido à reconfiguração?</li></ul>|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servidores
|Verificações executadas|
|---|
|<ul><li>O servidor está indisponível devido à manutenção?</li><li>O servidor está indisponível devido à reconfiguração?</li></ul>|

## <a name="microsoftdevicesiothubs"></a>Microsoft.devices/iothubs
|Verificações executadas|
|---|
|<ul><li>O centro de IoT está a funcionar?</li></ul>|

## <a name="microsoftdigitaltwinsdigitaltwinsinstances"></a>Microsoft.DigitalTwins/DigitalTwinsInstances
|Verificações executadas|
|---|
|<ul><li>A instância dos Gémeos Digitais Azure está a funcionar?</li></ul>|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.documentdb/base de dadosAcontas
|Verificações executadas|
|---|
|<ul><li>Houve alguma base de dados ou pedidos de recolha não servidos devido a uma indisponibilidade de serviço Azure Cosmos DB?</li><li>Houve algum pedido de documento não servido devido à indisponibilidade do serviço Azure Cosmos DB?</li></ul>|

## <a name="microsofteventhubnamespaces"></a>Microsoft.eventhub/namespaces
|Verificações executadas|
|---|
|<ul><li>O espaço de nomes do Event Hubs está a sofrer erros gerados pelo utilizador?</li><li>O espaço de nomes do Event Hubs está atualmente a ser atualizado?</li></ul>|

## <a name="microsofthdinsightclusters"></a>Microsoft.hdinsight/clusters
|Verificações executadas|
|---|
|<ul><li>Os serviços essenciais estão disponíveis no cluster HDInsight?</li><li>O cluster HDInsight pode aceder à chave para a encriptação BYOK em repouso?</li></ul>|

## <a name="microsoftiotcentraliotapps"></a>Microsoft.IoTCentral/IoTApps
|Verificações executadas|
|---|
|<ul><li>A Aplicação Central IoT está disponível?</li></ul>|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/cofres
|Verificações executadas|
|---|
|<ul><li>Os pedidos de falha no cofre de chaves estão a falhar devido a problemas na plataforma Azure KeyVault?</li><li>Os pedidos para o cofre chave estão a ser estrangulados devido a muitos pedidos feitos pelo cliente?</li></ul>|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/clusters
|Verificações executadas|
|---|
|<ul><li>O cluster está a ter baixas taxas de sucesso de ingestão?</li><li>O aglomerado está a sofrer de alta latência de ingestão?</li><li>O cluster está a sofrer um elevado número de falhas de consulta?</li></ul>|

## <a name="microsoftmachinelearningwebservices"></a>Microsoft.MachineLearning/webServices
|Verificações executadas|
|---|
|<ul><li>O serviço web está a funcionar?</li></ul>|

## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices
|Verificações executadas|
|---|
|<ul><li>O serviço de comunicação está a funcionar?</li></ul>|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.network/applicationgateways
|Verificações executadas|
|---|
|<ul><li>A performance do Gateway de Aplicação está degradada?</li><li>O Gateway de Aplicações está disponível?</li></ul>|

## <a name="microsoftnetworkconnections"></a>Microsoft.network/conexões
|Verificações executadas|
|---|
|<ul><li>O túnel VPN está ligado?</li><li>Existem conflitos de configuração na ligação?</li><li>As chaves pré-partilhadas estão devidamente configuradas?</li><li>O dispositivo VPN no local é alcançável?</li><li>Existem incompatibilidades na política de segurança IPSec/IKE?</li><li>A ligação S2S VPN está devidamente prevista ou num estado falhado?</li><li>A ligação VNET-vNET está devidamente prevista ou num estado falhado?</li></ul>|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.network/expressroutecircuits
|Verificações executadas|
|---|
|<ul><li>O circuito ExpressRoute é saudável?</li></ul>|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft.network/frontdoors
|Verificações executadas|
|---|
|<ul><li>Os backends front door estão a responder com erros nas sondas de saúde?</li><li>As alterações de configuração estão atrasadas?</li></ul>|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.network/LoadBalancers
|Verificações executadas|
|---|
|<ul><li>Os pontos finais de equilíbrio de carga estão disponíveis?</li></ul>|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.network/trafficmanagerprofils
|Verificações executadas|
|---|
|<ul><li>Há algum problema com o perfil do Gestor de Tráfego?</li></ul>|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.network/virtualNetworkGateways
|Verificações executadas|
|---|
|<ul><li>O gateway VPN é acessível a partir da internet?</li><li>O Gateway VPN está em modo de espera?</li><li>O serviço VPN está a funcionar no portal?</li></ul>|

## <a name="microsoftnotificationhubsnamespace"></a>Microsoft.NotificationHubs/namespace
|Verificações executadas|
|---|
|<ul><li>As operações de tempo de execução como registo, instalação ou envio podem ser realizadas no espaço de nomes?</li></ul>|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.operationalinsights/workspaces
|Verificações executadas|
|---|
|<ul><li>Há atrasos de indexação para o espaço de trabalho?</li></ul>|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/Capacities
|Verificações executadas|
|---|
|<ul><li>O recurso de capacidade está a funcionar?</li><li>Todas as cargas de trabalho estão a funcionar?</li></ul>

## <a name="microsoftsearchsearchservices"></a>Microsoft.search/searchServices
|Verificações executadas|
|---|
|<ul><li>As operações de diagnóstico podem ser realizadas no cluster?</li></ul>|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces
|Verificações executadas|
|---|
|<ul><li>Os clientes que experimentam erros gerados pelo utilizador service bus?</li><li>Os utilizadores estão a sofrer um aumento de erros transitórios devido a uma atualização do espaço de nomes do Service Bus?</li></ul>|

## <a name="microsoftservicefabricclusters"></a>Microsoft.ServiceFabric/clusters
|Verificações executadas|
|---|
|<ul><li>O aglomerado de tecido de serviço está a funcionar?</li><li>O cluster de Tecido de Serviço pode ser gerido através do Azure Resource Manager?</li></ul>|

## <a name="microsoftsqlmanagedinstancesdatabases"></a>Microsoft.SQL/managedInstances/databases
|Verificações executadas|
|---|
|<ul><li>A base de dados está a funcionar?</li></ul>|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.SQL/servidores/bases de dados
|Verificações executadas|
|---|
|<ul><li>Tem havido logins na base de dados?</li></ul>|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAcontas
|Verificações executadas|
|---|
|<ul><li>Os pedidos de leitura de dados da conta de Armazenamento estão a falhar devido a problemas na plataforma de armazenamento Azure?</li><li>Os pedidos para escrever dados na conta de Armazenamento estão a falhar devido a problemas na plataforma de armazenamento Azure?</li><li>O cluster de armazenamento onde reside a conta de Armazenamento não está disponível?</li></ul>|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs
|Verificações executadas|
|---|
|<ul><li>Todos os anfitriões estão a executar e a funcionar?</li><li>O trabalho não pôde começar?</li><li>Existem atualizações de tempo de execução em curso?</li><li>O trabalho está num estado esperado (por exemplo, a correr ou parado pelo cliente)?</li><li>O trabalho encontrou exceções de memória?</li><li>Existem atualizações de computação programadas em curso?</li><li>O Gestor de Execução (plano de controlo) está disponível?</li></ul>|

## <a name="microsoftwebserverfarms"></a>Microsoft.web/serverFarms
|Verificações executadas|
|---|
|<ul><li>O servidor anfitrião está a funcionar?</li><li>Os Serviços de Informação da Internet estão a funcionar?</li><li>O equilibrador de carga está a funcionar?</li><li>O Plano de Serviço de Aplicações pode ser alcançado a partir do centro de dados?</li><li>A conta de armazenamento que hospeda o conteúdo dos sites para o servidorFarm está disponível??</li></ul>|

## <a name="microsoftwebsites"></a>Microsoft.web/sites
|Verificações executadas|
|---|
|<ul><li>O servidor anfitrião está a funcionar?</li><li>O servidor de informação da Internet está a funcionar?</li><li>O equilibrador de carga está a funcionar?</li><li>A Web App pode ser alcançada a partir do centro de dados?</li><li>A conta de armazenamento que hospeda o conteúdo do site está disponível?</li></ul>|

## <a name="next-steps"></a>Passos Seguintes
-  Consulte [o painel de saúde do serviço Azure](service-health-overview.md) e a [Introdução à Azure Resource Health](resource-health-overview.md) para saber mais sobre eles. 
-  [Perguntas frequentes sobre Azure Resource Health](resource-health-faq.md)
- Adasse alertas para que você é notificado de problemas de saúde. Para mais informações, consulte alertas de configuração [para eventos de saúde de serviço.](./alerts-activity-log-service-notifications-portal.md)