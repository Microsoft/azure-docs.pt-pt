---
title: Entenda como a ferramenta de migração voluntária funciona para alertas de Azure Monitor
description: Entenda como funciona a ferramenta de migração de alertas e solucione problemas.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 5257724add570be480063ab776248a8fd1d944c7
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034758"
---
# <a name="understand-how-the-migration-tool-works"></a>Entenda como funciona a ferramenta de migração

Como [anunciado anteriormente](monitoring-classic-retirement.md), os alertas clássicos no Azure monitor estão sendo desativados até 31 de agosto de 2019 (foi originalmente 30 de junho de 2019). Uma ferramenta de migração está disponível no portal do Azure aos clientes que usam regras de alerta clássicas e que desejam disparar a migração por conta própria.

Este artigo explica como funciona a ferramenta de migração voluntária. Ele também descreve as soluções para alguns problemas comuns.

> [!NOTE]
> Devido ao atraso na distribuição da ferramenta de migração, a data de desativação da migração de alertas clássicos foi [prorrogada para 31 de agosto de 2019](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) da data de lançamento original de 30 de junho de 2019.

## <a name="classic-alert-rules-that-will-not-be-migrated"></a>Regras de alerta clássicas que não serão migradas

> [!IMPORTANT]
> Alertas do log de atividades (incluindo alertas de integridade do serviço) e alertas de log não são afetados pela migração. A migração se aplica somente às regras de alerta clássicas descritas [aqui](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform).

Embora a ferramenta possa migrar quase todas as [regras de alerta clássicas](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform), há algumas exceções. As seguintes regras de alerta não serão migradas usando a ferramenta (ou durante a migração automática a partir de setembro de 2019):

- Regras de alerta clássicas em métricas de convidado de máquina virtual (Windows e Linux). Consulte as [diretrizes para recriar essas regras de alerta em novos alertas de métrica](#guest-metrics-on-virtual-machines) mais adiante neste artigo.
- Regras de alerta clássicas em métricas de armazenamento clássico. Consulte as [diretrizes para monitorar suas contas de armazenamento clássicas](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/).
- Regras de alerta clássicas em algumas métricas de conta de armazenamento. Consulte os [detalhes](#storage-account-metrics) mais adiante neste artigo.
- Regras de alerta clássicas em algumas métricas de Cosmos DB. Consulte os [detalhes](#cosmos-db-metrics) mais adiante neste artigo.
- Regras de alerta clássicas em todas as máquinas virtuais clássicas e métricas de serviços de nuvem (Microsoft. ClassicCompute/virtualMachines e Microsoft. ClassicCompute/nome_do_domínio/Slots/funções). Consulte os [detalhes](#classic-compute-metrics) mais adiante neste artigo.

Se sua assinatura tiver essas regras clássicas, você deverá migrá-las manualmente. Como não podemos fornecer uma migração automática, todos os alertas de métrica clássicos existentes desses tipos continuarão funcionando até 2020 de junho. Essa extensão dá tempo para passar para novos alertas. No entanto, nenhum novo alerta clássico pode ser criado após 2019 de agosto.

> [!NOTE]
> Além das exceções listadas acima, se suas regras de alerta clássicas forem inválidas [](#classic-alert-rules-on-deprecated-metrics) , ou seja, se estiverem em métricas preteridas ou recursos que foram excluídos, elas não serão migradas durante a migração voluntária. Quaisquer regras de alerta clássica inválidas serão excluídas quando ocorrer a migração automática.

### <a name="guest-metrics-on-virtual-machines"></a>Métricas de convidado em máquinas virtuais

Antes de criar novos alertas de métrica em métricas de convidado, as métricas de convidado devem ser enviadas para o Azure Monitor repositório de métricas personalizado. Siga estas instruções para habilitar o coletor de Azure Monitor nas configurações de diagnóstico:

- [Habilitando métricas de convidado para VMs do Windows](collect-custom-metrics-guestos-resource-manager-vm.md)
- [Habilitando métricas de convidado para VMs Linux](collect-custom-metrics-linux-telegraf.md)

Depois que essas etapas forem concluídas, você poderá criar novos alertas de métricas em métricas de convidado. E, depois de criar novos alertas de métrica, você pode excluir alertas clássicos.

### <a name="storage-account-metrics"></a>Métricas da conta de armazenamento

Todos os alertas clássicos em contas de armazenamento podem ser migrados, exceto alertas nessas métricas:

- PercentAuthorizationError
- PercentClientOtherError
- PercentNetworkError
- PercentServerOtherError
- PercentSuccess
- PercentThrottlingError
- PercentTimeoutError
- AnonymousThrottlingError
- SASThrottlingError
- ThrottlingError

As regras de alerta clássicas em métricas de porcentagem devem ser migradas com base no [mapeamento entre métricas de armazenamento antigas e novas](https://docs.microsoft.com/azure/storage/common/storage-metrics-migration#metrics-mapping-between-old-metrics-and-new-metrics). Os limites precisarão ser modificados adequadamente, pois a nova métrica disponível é absoluta.

As regras de alerta clássicas em AnonymousThrottlingError, SASThrottlingError e ThrottlingError devem ser divididas em dois novos alertas, pois não há métricas combinadas que forneçam a mesma funcionalidade. Os limites precisarão ser adaptados adequadamente.

### <a name="cosmos-db-metrics"></a>Métricas do Cosmos DB

Todos os alertas clássicos em métricas de Cosmos DB podem ser migrados, exceto alertas nessas métricas:

- Média de solicitações por segundo
- Nível de Consistência
- Http 2xx
- Http 3xx
- Http 400
- Http 401
- Erro Interno do Servidor
- Máximo de RUPM consumidos por minuto
- Máximo de RUs por segundo
- Solicitações de falha na contagem de Mongo
- Solicitações de exclusão com falha do Mongo
- Solicitações de inserção com falha do Mongo
- Mongo outras solicitações com falha
- Mongo outro encargo de solicitação
- Mongo de outra taxa de solicitação
- Solicitações de consulta Mongo com falha
- Solicitações de falha na atualização do Mongo
- Latência de leitura observada
- Latência de gravação observada
- Disponibilidade do serviço
- Capacidade de Armazenamento
- Solicitações limitadas
- Total de Pedidos

Média de solicitações por segundo, nível de consistência, máximo de RUPM consumidas por minuto, máximo de RUs por segundo, latência de leitura observada, latência de gravação observada, a capacidade de armazenamento não está disponível no momento no [novo sistema](metrics-supported.md#microsoftdocumentdbdatabaseaccounts).

Alertas sobre métricas de solicitação como http 2xx, http 3xx, http 400, http 401, erro interno do servidor, disponibilidade do serviço, solicitações limitadas e total de solicitações não são migrados porque a maneira como as solicitações são contadas é diferente entre métricas clássicas e novas métricas. Os alertas sobre eles deverão ser recriados manualmente com limites ajustados.

Alertas em Mongo as métricas de solicitações com falha devem ser divididos em vários alertas porque não há métricas combinadas que forneçam a mesma funcionalidade. Os limites precisarão ser adaptados adequadamente.

### <a name="classic-compute-metrics"></a>Métricas de computação clássicas

Quaisquer alertas sobre métricas de computação clássicas não serão migrados usando a ferramenta de migração, já que os recursos de computação clássicos ainda não têm suporte com novos alertas. O suporte para novos alertas nesses tipos de recursos será adicionado no futuro. Uma vez disponível, os clientes devem recriar novas regras de alerta equivalentes com base em suas regras de alerta clássicas antes de junho de 2020.

### <a name="classic-alert-rules-on-deprecated-metrics"></a>Regras de alerta clássicas sobre métricas preteridas

Essas são regras de alerta clássicas sobre métricas que eram anteriormente suportadas, mas que foram eventualmente preteridas. Uma pequena porcentagem do cliente pode ter regras de alerta clássicas inválidas nessas métricas. Como essas regras de alerta são inválidas, elas não serão migradas.

| Tipo de recurso| Métrica (s) preteridas |
|-------------|----------------- |
| Microsoft.DBforMySQL/servers | compute_consumption_percent, compute_limit |
| Microsoft.DBforPostgreSQL/servers | compute_consumption_percent, compute_limit |
| Microsoft.Network/publicIPAddresses | defaultddostriggerrate |
| Microsoft.SQL/servers/databases | service_level_objective, storage_limit, storage_used, throttling, dtu_consumption_percent, storage_used |
| Microsoft.Web/hostingEnvironments/multirolepools | averagememoryworkingset |
| Microsoft.Web/hostingEnvironments/workerpools | bytesreceived, httpqueuelength |

## <a name="how-equivalent-new-alert-rules-and-action-groups-are-created"></a>Como as novas regras de alerta e os grupos de ação equivalentes são criados

A ferramenta de migração converte suas regras de alerta clássicas para novas regras de alerta e grupos de ação equivalentes. Para as regras de alerta mais clássicas, as novas regras de alerta equivalentes estão na mesma métrica com `windowSize` as `aggregationType`mesmas propriedades, como e. No entanto, há algumas regras de alerta clássicas em métricas que têm uma métrica diferente e equivalente no novo sistema. Os princípios a seguir se aplicam à migração de alertas clássicos, a menos que especificado na seção abaixo:

- **Frequência**: Define com que frequência uma regra de alerta clássica ou nova verifica a condição. As `frequency` regras de alerta clássicas não eram configuráveis pelo usuário e foram sempre 5 minutos para todos os tipos de recursos, exceto Application insights componentes para os quais era 1 minuto. Portanto, a frequência das regras equivalentes também é definida como 5 min e 1 min, respectivamente.
- **Tipo**de agregação: Define como a métrica é agregada na janela de interesse. O `aggregationType` também é o mesmo entre alertas clássicos e novos alertas para a maioria das métricas. Em alguns casos, como a métrica é diferente entre alertas clássicos e novos alertas, o `aggregationType` equivalente ou `primary Aggregation Type` o definido para a métrica é usado.
- **Unidades**: Propriedade da métrica na qual o alerta é criado. Algumas métricas equivalentes têm unidades diferentes. O limite é ajustado adequadamente conforme necessário. Por exemplo, se a métrica original tiver segundos como unidades, mas a nova métrica equivalente tiver milissegundos como unidades, o limite original será multiplicado por 1000 para garantir o mesmo comportamento.
- **Tamanho da janela**: Define a janela sobre a qual os dados de métrica são agregados para comparar com o limite. Para valores `windowSize` padrão como 5mins, 15mins, 30mins, 1hour, 3Hours, 6 horas, 12 horas, 1 dia, não há nenhuma alteração feita para a nova regra de alerta equivalente. Para outros valores, o mais próximo `windowSize` é escolhido para ser usado. Para a maioria dos clientes, não há nenhum impacto com essa alteração. Para um pequeno percentual de clientes, pode haver a necessidade de ajustar o limite para obter exatamente o mesmo comportamento.

Nas seções a seguir, detalharemos as métricas que têm uma métrica diferente e equivalente no novo sistema. Qualquer métrica que permanece a mesma para as regras de alerta clássicas e novas não está listada. Você pode encontrar uma lista de métricas com suporte no novo sistema [aqui](metrics-supported.md).

### <a name="microsoftstorageaccountsservices"></a>Microsoft.StorageAccounts/services

Para serviços de conta de armazenamento como BLOB, tabela, arquivo e fila, as métricas a seguir são mapeadas para métricas equivalentes, conforme mostrado abaixo:

| Métrica em alertas clássicos | Métrica equivalente em novos alertas | Comentários|
|--------------------------|---------------------------------|---------|
| AnonymousAuthorizationError| Métrica de transações com dimensões "ResponseType" = "AuthorizationError" e "Authentication" = "anônimo"| |
| AnonymousClientOtherError | Métrica de transações com dimensões "ResponseType" = "ClientOtherError" e "Authentication" = "anônimo" | |
| AnonymousClientTimeOutError| Métrica de transações com dimensões "ResponseType" = "ClientTimeOutError" e "Authentication" = "anônimo" | |
| AnonymousNetworkError | Métrica de transações com dimensões "ResponseType" = "NetworkError" e "Authentication" = "anônimo" | |
| AnonymousServerOtherError | Métrica de transações com dimensões "ResponseType" = "ServerOtherError" e "Authentication" = "anônimo" | |
| AnonymousServerTimeOutError | Métrica de transações com dimensões "ResponseType" = "ServerTimeOutError" e "Authentication" = "anônimo" | |
| AnonymousSuccess | Métrica de transações com dimensões "ResponseType" = "êxito" e "autenticação" = "anônimo" | |
| AuthorizationError | Métrica de transações com dimensões "ResponseType" = "AuthorizationError" | |
| AverageE2ELatency | SuccessE2ELatency | |
| AverageServerLatency | SuccessServerLatency | |
| Capacidade | BlobCapacity | Use `aggregationType` ' Average ' em vez de ' Last '. A métrica se aplica somente aos serviços de BLOB |
| ClientOtherError | Métrica de transações com dimensões "ResponseType" = "ClientOtherError"  | |
| ClientTimeoutError | Métrica de transações com dimensões "ResponseType" = "ClientTimeOutError" | |
| ContainerCount | ContainerCount | Use `aggregationType` ' Average ' em vez de ' Last '. A métrica se aplica somente aos serviços de BLOB |
| NetworkError | Métrica de transações com dimensões "ResponseType" = "NetworkError" | |
| ObjectCount | BlobCount| Use `aggregationType` ' Average ' em vez de ' Last '. A métrica se aplica somente aos serviços de BLOB |
| SASAuthorizationError | Métrica de transações com dimensões "ResponseType" = "AuthorizationError" e "Authentication" = "SAS" | |
| SASClientOtherError | Métrica de transações com dimensões "ResponseType" = "ClientOtherError" e "Authentication" = "SAS" | |
| SASClientTimeOutError | Métrica de transações com dimensões "ResponseType" = "ClientTimeOutError" e "Authentication" = "SAS" | |
| SASNetworkError | Métrica de transações com dimensões "ResponseType" = "NetworkError" e "Authentication" = "SAS" | |
| SASServerOtherError | Métrica de transações com dimensões "ResponseType" = "ServerOtherError" e "Authentication" = "SAS" | |
| SASServerTimeOutError | Métrica de transações com dimensões "ResponseType" = "ServerTimeOutError" e "Authentication" = "SAS" | |
| SASSuccess | Métrica de transações com dimensões "ResponseType" = "êxito" e "autenticação" = "SAS" | |
| ServerOtherError | Métrica de transações com dimensões "ResponseType" = "ServerOtherError" | |
| ServerTimeOutError | Métrica de transações com dimensões "ResponseType" = "ServerTimeOutError"  | |
| Êxito | Métrica de transações com dimensões "ResponseType" = "êxito" | |
| TotalBillableRequests| Transações | |
| TotalEgress | Saída | |
| TotalIngress | Entrada | |
| TotalRequests | Transações | |

### <a name="microsoftinsightscomponents"></a>Microsoft.insights/components

Por Application Insights, as métricas equivalentes são mostradas abaixo:

| Métrica em alertas clássicos | Métrica equivalente em novos alertas | Comentários|
|--------------------------|---------------------------------|---------|
| disponibilidade. availabilityMetric. Value | availabilityResults/availabilityPercentage|   |
| availability.durationMetric.value | availabilityResults/duration| Multiplique o limite original por 1000, pois as unidades para métrica clássica estão em segundos e para uma nova em milissegundos.  |
| basicExceptionBrowser.count | exceções/navegador|  Use `aggregationType` ' count ' em vez de ' Sum '. |
| basicExceptionServer.count | exceções/servidor| Use `aggregationType` ' count ' em vez de ' Sum '.  |
| clientPerformance.clientProcess.value | browserTimings/processingDuration| Multiplique o limite original por 1000, pois as unidades para métrica clássica estão em segundos e para uma nova em milissegundos.  |
| clientPerformance.networkConnection.value | browserTimings/networkDuration|  Multiplique o limite original por 1000, pois as unidades para métrica clássica estão em segundos e para uma nova em milissegundos. |
| clientPerformance.receiveRequest.value | browserTimings/receiveDuration| Multiplique o limite original por 1000, pois as unidades para métrica clássica estão em segundos e para uma nova em milissegundos.  |
| clientPerformance.sendRequest.value | browserTimings/sendDuration| Multiplique o limite original por 1000, pois as unidades para métrica clássica estão em segundos e para uma nova em milissegundos.  |
| clientPerformance.total.value | browserTimings/totalDuration| Multiplique o limite original por 1000, pois as unidades para métrica clássica estão em segundos e para uma nova em milissegundos.  |
| performanceCounter. available_bytes. Value | performanceCounters/memoryAvailableBytes|   |
| performanceCounter. Io _data_bytes_per_sec. Value | performanceCounters/processIOBytesPerSecond|   |
| performanceCounter.number_of_exceps_thrown_per_sec.value | performanceCounters/exceptionsPerSecond|   |
| performanceCounter.percentage_processor_time_normalized.value | performanceCounters/processCpuPercentage|   |
| performanceCounter.percentage_processor_time.value | performanceCounters/processCpuPercentage| O limite precisará ser modificado adequadamente, pois a métrica original foi feita em todos os núcleos e a nova métrica é normalizada para um núcleo. A ferramenta de migração não altera os limites.  |
| performanceCounter.percentage_processor_total.value | performanceCounters/processorCpuPercentage|   |
| performanceCounter. process_private_bytes. Value | performanceCounters/processPrivateBytes|   |
| performanceCounter.request_execution_time.value | performanceCounters/requestExecutionTime|   |
| performanceCounter.requests_in_application_queue.value | performanceCounters/requestsInQueue|   |
| performanceCounter.requests_per_sec.value | performanceCounters/requestsPerSecond|   |
| request.duration | solicitações/duração| Multiplique o limite original por 1000, pois as unidades para métrica clássica estão em segundos e para uma nova em milissegundos.  |
| solicitação. taxa | solicitações/taxa|   |
| requestFailed.count | solicitações/com falha| Use `aggregationType` ' count ' em vez de ' Sum '.   |
| Exibir. contagem | pageViews/contagem| Use `aggregationType` ' count ' em vez de ' Sum '.   |

### <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

Por Cosmos DB, as métricas equivalentes são mostradas abaixo:

| Métrica em alertas clássicos | Métrica equivalente em novos alertas | Comentários|
|--------------------------|---------------------------------|---------|
| AvailableStorage     |AvailableStorage|   |
| Tamanho dos Dados | DataUsage| |
| Contagem de documentos | DocumentCount||
| Tamanho do Índice | IndexUsage||
| Encargo de solicitação de contagem de Mongo| MongoRequestCharge com a dimensão "CommandName" = "Count"||
| Taxa de solicitação de contagem de Mongo | MongoRequestsCount com a dimensão "CommandName" = "Count"||
| Mongo o encargo da solicitação de exclusão | MongoRequestCharge com a dimensão "CommandName" = "Delete"||
| Taxa de solicitação de exclusão de Mongo | MongoRequestsCount com a dimensão "CommandName" = "Delete"||
| Cobrança de solicitação de inserção de Mongo | MongoRequestCharge com a dimensão "CommandName" = "Insert"||
| Taxa de solicitação de inserção de Mongo | MongoRequestsCount com a dimensão "CommandName" = "Insert"||
| Encargo de solicitação de consulta do Mongo | MongoRequestCharge com a dimensão "CommandName" = "Find"||
| Taxa de solicitação de consulta do Mongo | MongoRequestsCount com a dimensão "CommandName" = "Find"||
| Encargo de solicitação de atualização do Mongo | MongoRequestCharge com a dimensão "CommandName" = "Update"||
| Serviço Não Disponível| Indisponibilidade||
| TotalRequestUnits | TotalRequestUnits||

### <a name="how-equivalent-action-groups-are-created"></a>Como os grupos de ação equivalentes são criados

As regras de alerta clássicas tinham email, webhook, aplicativo lógico e ações de runbook vinculadas à própria regra de alerta. As novas regras de alerta usam grupos de ação que podem ser reutilizados em várias regras de alerta. A ferramenta de migração cria um grupo de ação único para as mesmas ações, independentemente de quantas regras de alerta estão usando a ação. Os grupos de ação criados pela ferramenta de migração usam o formato de nomenclatura ' Migrated_AG * '.

> [!NOTE]
> Os alertas clássicos enviaram emails localizados com base na localidade do administrador clássico quando usados para notificar funções de administrador clássicas. Os novos emails de alerta são enviados por meio de grupos de ação e são apenas em inglês.

## <a name="rollout-phases"></a>Fases de distribuição

A ferramenta de migração é distribuída em fases para clientes que usam regras de alerta clássicas. Os proprietários da assinatura receberão um email quando a assinatura estiver pronta para ser migrada usando a ferramenta.

> [!NOTE]
> Como a ferramenta está sendo distribuída em fases, você pode ver que algumas de suas assinaturas ainda não estão prontas para serem migradas durante as primeiras fases.

A maioria das assinaturas está marcada atualmente como pronta para migração. Somente as assinaturas que têm alertas clássicos nos tipos de recursos a seguir ainda não estão prontas para migração.

- Microsoft.classicCompute/domainNames/slots/roles
- Microsoft.insights/components

## <a name="who-can-trigger-the-migration"></a>Quem pode disparar a migração?

Qualquer usuário que tenha a função interna de colaborador de monitoramento no nível de assinatura pode disparar a migração. Os usuários que têm uma função personalizada com as seguintes permissões também podem disparar a migração:

- */read
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*
- Microsoft. AlertsManagement/smartDetectorAlertRules/*

> [!NOTE]
> Além de ter permissões acima, sua assinatura também deve ser registrada com o provedor de recursos Microsoft. AlertsManagement. Isso é necessário para migrar com êxito os alertas de anomalias de falha no Application Insights. 

## <a name="common-problems-and-remedies"></a>Problemas comuns e soluções

Depois de [disparar a migração](alerts-using-migration-tool.md), você receberá um email nos endereços fornecidos para notificá-lo de que a migração foi concluída ou se alguma ação for necessária. Esta seção descreve alguns problemas comuns e como lidar com eles.

### <a name="validation-failed"></a>Falha na validação

Devido a algumas alterações recentes nas regras de alerta clássicas em sua assinatura, a assinatura não pode ser migrada. Esse problema é temporário. Você pode reiniciar a migração após o status de migração voltar a ficar **pronto para migração** em alguns dias.

### <a name="scope-lock-preventing-us-from-migrating-your-rules"></a>Bloqueio de escopo nos impedindo de migrar suas regras

Como parte da migração, novos alertas de métrica e novos grupos de ação serão criados e as regras de alerta clássicas serão excluídas. No entanto, um bloqueio de escopo pode impedi-lo de criar ou excluir recursos. Dependendo do bloqueio de escopo, algumas ou todas as regras não puderam ser migradas. Você pode resolver esse problema removendo o bloqueio de escopo para a assinatura, o grupo de recursos ou o recurso, que está listado na [ferramenta de migração](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)e disparando a migração novamente. O bloqueio de escopo não pode ser desabilitado e deve ser removido durante o processo de migração. [Saiba mais sobre como gerenciar bloqueios de escopo](../../azure-resource-manager/resource-group-lock-resources.md#portal).

### <a name="policy-with-deny-effect-preventing-us-from-migrating-your-rules"></a>Política com o efeito ' negar ' nos impedindo de migrar suas regras

Como parte da migração, novos alertas de métrica e novos grupos de ação serão criados e as regras de alerta clássicas serão excluídas. No entanto, uma política pode impedi-lo de criar recursos. Dependendo da política, algumas ou todas as regras não puderam ser migradas. As políticas que estão bloqueando o processo são listadas na [ferramenta de migração](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel). Resolva esse problema:

- Excluindo as assinaturas ou grupos de recursos para a duração do processo de migração da atribuição de política. [Saiba mais sobre como gerenciar políticas escopo de exclusão](../../governance/policy/tutorials/create-and-manage.md#exempt-a-non-compliant-or-denied-resource-using-exclusion).
- Remover ou alterar o efeito para ' auditoria ' ou ' acrescentar ' (que, por exemplo, pode resolver problemas relacionados a marcas ausentes). [Saiba mais sobre o efeito de gerenciamento de políticas](../../governance/policy/concepts/definition-structure.md#policy-rule).

## <a name="next-steps"></a>Passos Seguintes

- [Como utilizar a ferramenta de migração](alerts-using-migration-tool.md)
- [Preparar para a migração](alerts-prepare-migration.md)
