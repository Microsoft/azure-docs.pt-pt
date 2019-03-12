---
title: Tipos de recursos no Azure Resource Health suportados | Documentos da Microsoft
description: Tipos de recurso suportados através do Azure Resource health
author: stephbaron
ms.author: stbaron
ms.topic: conceptual
ms.service: service-health
ms.date: 01/29/2019
ms.openlocfilehash: a8bc82a2717bfa6838b2331ef54ed8098422c223
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57770351"
---
# <a name="resource-types-and-health-checks-in-azure-resource-health"></a>Tipos de recursos e o estado de funcionamento verifica-se no estado de funcionamento de recursos do Azure
Segue-se uma lista completa de todas as verificações executadas por meio do Estado de funcionamento do recurso por tipos de recursos.

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers
|Verificações executadas|
|---|
|<ul><li>É o servidor em execução?</li><li>O servidor ficou sem memória?</li><li>O servidor está a iniciar cópia de segurança?</li><li>O servidor é recuperar?</li></ul>|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service
|Verificações executadas|
|---|
|<ul><li>É o serviço de gestão de Api a cópia de segurança e em execução?</li></ul>|

## <a name="microsoftcacheredisredis"></a>Microsoft.CacheRedis/Redis
|Verificações executadas|
|---|
|<ul><li>Estão todos os nós de Cache em execução?</li><li>A Cache jmccaffrey no Centro de dados?</li><li>A Cache atingiu o número máximo de ligações?</li><li> A cache esgotou a memória disponível? </li><li>É a Cache com um elevado número de falhas de página?</li><li>É a Cache com muita carga?</li></ul>|

## <a name="microsoftcdnprofile"></a>Microsoft.CDN/profile
|Verificações executadas|
|---|
|<ul> <li>O portal suplementar é acessível para operações de configuração da CDN?</li><li>Existem problemas de entrega contínua com os pontos finais CDN?</li><li>Os utilizadores podem alterar a configuração dos seus recursos CDN?</li><li>Alterações de configuração são propagar a tarifa esperado?</li><li>Os utilizadores podem gerir a configuração de CDN com o portal do Azure, PowerShell ou a API?</li> </ul>|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.classiccompute/virtualmachines
|Verificações executadas|
|---|
|<ul><li>É o servidor de anfitrião em execução?</li><li>A inicialização de SO de host concluída?</li><li>O contentor de máquina virtual é aprovisionado e com tecnologia de cópia de segurança?</li><li>Existe conectividade de rede entre o anfitrião e a conta de armazenamento?</li><li>O arranque de sistema operacional convidado concluída?</li><li>Existe a manutenção planeada em curso?</li></ul>|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.cognitiveservices/accounts
|Verificações executadas|
|---|
|<ul><li>A conta jmccaffrey no Centro de dados?</li><li>O fornecedor de recursos de serviços cognitivos está disponível?</li><li>O serviço cognitivos está disponível na região apropriada?</li><li>Pode ler operações ser executadas na conta de armazenamento que contém os metadados do recurso?</li><li>Foi atingido a quota de chamadas à API?</li><li>Foi atingido o limite de leitura de chamada de API?</li></ul>|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.compute/virtualmachines
|Verificações executadas|
|---|
|<ul><li>É o servidor que aloja esta máquina virtual, cópia de segurança e em execução?</li><li>A inicialização de SO de host concluída?</li><li>O contentor de máquina virtual é aprovisionado e com tecnologia de cópia de segurança?</li><li>Existe conectividade de rede entre o anfitrião e a conta de armazenamento?</li><li>O arranque de sistema operacional convidado concluída?</li><li>Existe a manutenção planeada em curso?</li></ul>|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.datalakeanalytics/accounts
|Verificações executadas|
|---|
|<ul><li>Tem problemas experientes utilizadores submeter ou listar seus trabalhos do Data Lake Analytics?</li><li>São tarefas de Data Lake Analytics não foi possível concluir a erros de sistema?</li></ul>|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.datalakestore/accounts
|Verificações executadas|
|---|
|<ul><li>Os utilizadores tiveram problemas carregar dados para o Data Lake Store?</li><li>Os utilizadores tiveram problemas de transferência de dados do Data Lake Store?</li></ul>|

## <a name="microsoftdevicesiothubs"></a>Microsoft.devices/iothubs

|Verificações executadas|
|---|
|<ul><li>É o hub IoT em execução?</li></ul>|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.documentdb/databaseAccounts
|Verificações executadas|
|---|
|<ul><li>Tem havido quaisquer solicitações de base de dados ou coleção não fornecidas por uma indisponibilidade de serviço do Azure Cosmos DB?</li><li>Tem havido quaisquer pedidos de documento não servidos por uma indisponibilidade de serviço do Azure Cosmos DB?</li></ul>|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults
|Verificações executadas|
|---|
|<ul><li>Pedidos para o Cofre de chaves falharem devido a problemas de plataforma do Cofre de chaves do Azure?</li><li>Pedidos para o Cofre de chaves estão a ser limitados devido a demasiados pedidos pelo cliente?</li></ul>|

## <a name="microsoftnetworkconnections"></a>Microsoft.network/connections
|Verificações executadas|
|---|
|<ul><li>O túnel VPN está ligado?</li><li>Existem conflitos de configuração na ligação?</li><li>As chaves pré-partilhadas estão corretamente configuradas?</li><li>Dispositivo VPN no local é acessível?</li><li>Existem incompatibilidades na política de segurança de IPSec/IKE?</li><li>É a ligação S2S VPN corretamente aprovisionado ou num Estado de falha?</li><li>É a ligação de VNET a VNET corretamente aprovisionado ou num Estado de falha?</li></ul>|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.network/virtualNetworkGateways
|Verificações executadas|
|---|
|<ul><li>O gateway de VPN é acessível a partir da internet?</li><li>É o Gateway de VPN no modo de reserva dinâmica?</li><li>O serviço VPN em execução no gateway?</li></ul>|

## <a name="microsoftnotificationhubsnamespace"></a>Microsoft.NotificationHubs/namespace
|Verificações executadas|
|---|
|<ul><li> Podem ser executadas operações de tempo de execução, como o registo, a instalação ou a enviar no espaço de nomes?</li></ul>|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/Capacities
|Verificações executadas|
|---|
|<ul><li>É o recurso de capacidade em execução?</li><li>Estão todas as cargas de trabalho em execução?</li></ul>|

## <a name="microsoftpowerbiworkspacecollections"></a>Microsoft.PowerBI/workspaceCollections
|Verificações executadas|
|---|
|<ul><li>É o sistema operacional host em execução?</li><li>O workspaceCollection é acessível a partir de fora do datacenter?</li><li>O fornecedor de recursos do Power BI está disponível?</li><li>O serviço Power BI está disponível na região apropriada?</li></ul>|

## <a name="microsoftsearchsearchservices"></a>Microsoft.search/searchServices
|Verificações executadas|
|---|
|<ul><li>Operações de diagnóstico podem ser executadas no cluster?</li></ul>|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces
|Verificações executadas|
|---|
|<ul><li>Os clientes ocorrerem erros de barramento de serviço gerados pelo utilizador?</li><li>Utilizadores que está a ter um aumento do número de erros devido a uma atualização do espaço de nomes do Service Bus?</li></ul>|

## <a name="microsoftsqlserverdatabase"></a>Microsoft.SQL/Server/database
|Verificações executadas|
|---|
|<ul><li> Tem havido inícios de sessão para a base de dados?</li></ul>|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts
|Verificações executadas|
|---|
|<ul><li>São pedidos para ler dados da conta de armazenamento falhar devido a problemas de plataforma de armazenamento do Azure?</li><li>São pedidos para escrever dados para a conta de armazenamento falhar devido a problemas de plataforma de armazenamento do Azure?</li><li>O cluster de armazenamento onde reside a conta de armazenamento não está disponível?</li></ul>|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs
|Verificações executadas|
|---|
|<ul><li>São todos os anfitriões em que a tarefa é executar a cópia de segurança e em execução?</li><li>A tarefa não foi possível iniciar?</li><li>Existem atualizações de tempo de execução em curso?</li><li>É o trabalho num Estado inesperado (por exemplo, em execução ou parado pelo cliente)?</li><li>A tarefa encontrou as exceções de memória?</li><li>Existem atualizações de computação agendados em curso?</li><li>O Gestor de execução (plano de controlo) está disponível?</li></ul>|

## <a name="microsoftwebserverfarms"></a>Microsoft.web/serverFarms
|Verificações executadas|
|---|
|<ul><li>É o servidor de anfitrião em execução?</li><li>Serviços de informação Internet está em execução?</li><li>O Balanceador de carga está em execução?</li><li>O plano do serviço de aplicações jmccaffrey no Centro de dados?</li><li>A conta de armazenamento está a alojar o conteúdo de sites para o farm de servidores disponível??</li></ul>|

## <a name="microsoftwebsites"></a>Microsoft.web/sites
|Verificações executadas|
|---|
|<ul><li>É o servidor de anfitrião em execução?</li><li>Internet Information server está em execução?</li><li>O Balanceador de carga está em execução?</li><li>A aplicação Web jmccaffrey no Centro de dados?</li><li>A conta de armazenamento está a alojar o conteúdo de site disponível?</li></ul>|

# <a name="next-steps"></a>Próximos Passos
-  Ver [introdução ao dashboard do Azure Service Health](service-health-overview.md) e [introdução ao Azure Resource Health](resource-health-overview.md) para saber mais sobre eles. 
-  [Perguntas mais frequentes sobre o Azure Resource Health](resource-health-faq.md)
- Configure alertas para que seja notificado de problemas de estado de funcionamento. Para obter mais informações, consulte [configurar alertas para eventos de estado de funcionamento do serviço](../azure-monitor/platform/alerts-activity-log-service-notifications.md). 
