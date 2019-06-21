---
title: Compreender como a ferramenta de migração voluntária funciona para os alertas do Azure Monitor
description: Compreender como funciona a ferramenta de migração de alertas e resolução de problemas.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 015000388c5629dbd8ed8833931a809ebd738bd6
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295536"
---
# <a name="understand-how-the-migration-tool-works"></a>Compreender como funciona a ferramenta de migração

Como [anunciado anteriormente](monitoring-classic-retirement.md), alertas Clássicos no Monitor do Azure estão a ser retiradas até 31 de Agosto de 2019 (foi originalmente 30 de Junho de 2019). Uma ferramenta de migração está disponível no portal do Azure para os clientes que utilizam regras de alerta clássicas e que desejam acionar a migração propriamente ditas.

Este artigo explica como funciona a ferramenta de migração voluntária. Ele também descreve as soluções para alguns problemas comuns.

> [!NOTE]
> Devido a atraso no faseada da ferramenta de migração, tem sido a data de retirada para a migração de alertas clássicos [estendido para 31 de Agosto de 2019](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) a contar da data originalmente anunciado de 30 de Junho de 2019.

## <a name="classic-alert-rules-that-will-not-be-migrated"></a>Regras de alerta clássicas que não serão migradas

> [!IMPORTANT]
> Alertas de registo de atividade (incluindo alertas de estado de funcionamento de serviço) e alertas de registo não são afetadas pela migração. A migração só se aplica a regras de alerta clássicas descritas [aqui](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform).

Embora a ferramenta pode migrar quase todos os [regras de alerta clássicas](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform), existem algumas exceções. As seguintes regras de alerta não serão migradas utilizando a ferramenta (ou durante a migração automática a partir de Setembro de 2019):

- Regras de alerta clássicas em métricas de convidado de máquina virtual (Windows e Linux). Consulte a [orientações para recriar essas regras de alerta no novos alertas de métricas](#guest-metrics-on-virtual-machines) mais adiante neste artigo.
- Regras de alerta clássicas em métricas de armazenamento clássicas. Consulte a [documentação de orientação para monitorizar as suas contas de armazenamento clássicas](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/).
- Regras de alerta clássicas em algumas métricas de conta de armazenamento. Ver [detalhes](#storage-account-metrics) mais adiante neste artigo.
- Regras de alerta clássicas em algumas métricas de Cosmos DB. Detalhes serão adicionados numa atualização futura.

Se a sua subscrição tiver tais regras clássicas, tem de migrá-los manualmente. Uma vez que não é possível fornecer uma migração automática, qualquer alertas de métricas existentes, clássicas desses tipos irão continuar a funcionar até de 2020 de Junho. Esta extensão dá-lhe tempo para mover para novos alertas. No entanto, não existem novos alertas clássicos podem ser criadas depois de Agosto de 2019.

> [!NOTE]
> Além das exceções listadas acima, se as regras do alerta clássicas são inválidas ou seja, estão na [preterido métricas](#classic-alert-rules-on-deprecated-metrics) ou recursos que tiverem sido eliminados, não serão migrados durante a migração voluntária. Serão eliminadas quaisquer tais inválidas clássicas regras de alerta quando ocorre a migração automática.

### <a name="guest-metrics-on-virtual-machines"></a>Métricas de convidado em máquinas virtuais

Antes de poder criar novos alertas de métricas em métricas de convidado, as métricas de convidado têm de ser enviadas para o arquivo de métricas personalizadas do Azure Monitor. Siga estas instruções para ativar o sink do Azure Monitor nas definições de diagnóstico:

- [Ativar as métricas de convidado para VMs do Windows](collect-custom-metrics-guestos-resource-manager-vm.md)
- [Ativar as métricas de convidado para VMs do Linux](collect-custom-metrics-linux-telegraf.md)

Depois de concluído estes passos, pode criar novos alertas de métricas em métricas de convidado. E, depois de criar novos alertas de métricas, pode eliminar alertas clássicos.

### <a name="storage-account-metrics"></a>Métricas da conta de armazenamento

Todos os alertas clássicos em contas de armazenamento podem ser migrados, exceto os alertas nestas métricas:

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

Alerta clássica de regras em percentagem métricas devem ser migradas com base na [o mapeamento entre as métricas de armazenamento antigos e novos](https://docs.microsoft.com/azure/storage/common/storage-metrics-migration#metrics-mapping-between-old-metrics-and-new-metrics). Limiares precisará ser modificado adequadamente porque a nova métrica disponível é absoluto.

Regras de alerta clássicas em AnonymousThrottlingError, SASThrottlingError e ThrottlingError têm de ser divididas em dois novos alertas porque não existe nenhuma métrica combinada que fornece a mesma funcionalidade. Limiares tem de ser adaptada adequadamente.

### <a name="classic-alert-rules-on-deprecated-metrics"></a>Regras de alerta clássicas em métricas preteridas

Estes são regras de alerta clássicas nas métricas que eram anteriormente suportadas, mas, eventualmente, foram preteridas. Uma pequena porcentagem de cliente pode ter regras de alerta clássicas inválidas em tais métricas. Uma vez que estas regras de alerta são inválidas, não serão migrados.

| Tipo de recurso| Metric(s) preterido |
|-------------|----------------- |
| Microsoft.DBforMySQL/servers | compute_consumption_percent, compute_limit |
| Microsoft.DBforPostgreSQL/servers | compute_consumption_percent, compute_limit |
| Microsoft.Network/publicIPAddresses | defaultddostriggerrate |
| Microsoft.SQL/servers/databases | service_level_objective, storage_limit, storage_used, limitação, dtu_consumption_percent, storage_used |
| Microsoft.Web/hostingEnvironments/multirolepools | averagememoryworkingset |
| Microsoft.Web/hostingEnvironments/workerpools | bytesreceived, httpqueuelength |

## <a name="how-equivalent-new-alert-rules-and-action-groups-are-created"></a>Como são criados equivalentes novas regras de alerta e grupos de ação

A ferramenta de migração converte as regras do alerta clássicas equivalentes novas regras de alerta e grupos de ação. Para a maioria das regras de alerta clássica, equivalentes novas regras de alerta estão na mesma métrica com as mesmas propriedades como `windowSize` e `aggregationType`. No entanto, existem algumas regras são nas métricas que têm uma métrica diferente, equivalente no novo sistema de alerta clássica. Os seguintes princípios aplicam-se à migração de alertas clássicos, a menos que especificado na secção abaixo:

- **Frequência**: Define a frequência com que uma regra de alerta clássica ou nova verifica para a condição. O `frequency` nas regras de alerta clássicas não era configurável pelo utilizador e era sempre 5 minutos para todos os tipos de recursos, exceto os componentes do Application Insights para o qual era 1 min. Então, frequência de regras equivalentes também está definida como 5 min e 1 min, respetivamente.
- **Tipo de agregação**: Define a forma como a métrica é agregada ao longo da janela de interesse. O `aggregationType` também é o mesmo entre alertas clássicos e os novos alertas para a maioria das métricas. Em alguns casos, uma vez que a métrica é diferente entre alertas clássicos e os novos alertas, equivalentes `aggregationType` ou o `primary Aggregation Type` definido para a métrica é utilizada.
- **Unidades**: Propriedade da métrica em que o alerta foi criado. Algumas métricas equivalentes tem unidades diferentes. O limiar é ajustado apropriadamente, conforme necessário. Por exemplo, se a métrica original tem segundos como unidades, mas tem de métrica de nova equivalente milissegundos como unidades, o limiar original é multiplicado por 1000 para garantir que o mesmo comportamento.
- **Tamanho da janela**: Define ao longo do que métrica os dados são agregados para comparar com o limiar da janela. Para o standard `windowSize` valores como 5mins, 15mins, 30 minutos, 1 hora, 3 horas, 6 horas, 12 horas, 1 dia, há nenhuma alteração feita para equivalente nova regra de alerta. Para outros valores, o mais aproximado `windowSize` é escolhida a ser utilizado. Para a maioria dos clientes, não é afetado com esta alteração. Para uma pequena porcentagem de clientes, poderá ser necessário ajustar o limiar para obter um comportamento exatamente igual.

Nas seções a seguir, vamos detalhar as métricas que tem uma métrica diferente, equivalente no novo sistema. Qualquer métrica que permanece o mesmo clássicas e novas regras de alerta não está listada. Pode encontrar uma lista de métricas suportadas no novo sistema [aqui](metrics-supported.md).

### <a name="microsoftstorageaccountsservices"></a>Microsoft.StorageAccounts/services

Para os serviços de conta de armazenamento, como BLOBs, tabela, ficheiros e filas, as métricas seguintes são mapeadas para métricas equivalentes conforme mostrado abaixo:

| Métrica em alertas clássicos | Métrica equivalente em novos alertas | Comentários|
|--------------------------|---------------------------------|---------|
| AnonymousAuthorizationError| Métrica de transações com dimensões "ResponseType" = "AuthorizationError" e "Autenticação" = "Anónimo"| |
| AnonymousClientOtherError | Métrica de transações com dimensões "ResponseType" = "ClientOtherError" e "Autenticação" = "Anónimo" | |
| AnonymousClientTimeOutError| Métrica de transações com dimensões "ResponseType" = "ClientTimeOutError" e "Autenticação" = "Anónimo" | |
| AnonymousNetworkError | Métrica de transações com dimensões "ResponseType" = "NetworkError" e "Autenticação" = "Anónimo" | |
| AnonymousServerOtherError | Métrica de transações com dimensões "ResponseType" = "ServerOtherError" e "Autenticação" = "Anónimo" | |
| AnonymousServerTimeOutError | Métrica de transações com dimensões "ResponseType" = "ServerTimeOutError" e "Autenticação" = "Anónimo" | |
| AnonymousSuccess | Métrica de transações com dimensões "ResponseType" = "Êxito" e "Autenticação" = "Anónimo" | |
| AuthorizationError | Métrica de transações com dimensões "ResponseType" = "AuthorizationError" | |
| Apresentam uma AverageE2ELatency | SuccessE2ELatency | |
| AverageServerLatency | SuccessServerLatency | |
| Capacidade | BlobCapacity | Utilize `aggregationType` "média" em vez de "último". Métrica só se aplica aos serviços de Blob |
| ClientOtherError | Métrica de transações com dimensões "ResponseType" = "ClientOtherError"  | |
| ClientTimeoutError | Métrica de transações com dimensões "ResponseType" = "ClientTimeOutError" | |
| ContainerCount | ContainerCount | Utilize `aggregationType` "média" em vez de "último". Métrica só se aplica aos serviços de Blob |
| NetworkError | Métrica de transações com dimensões "ResponseType" = "NetworkError" | |
| ObjectCount | BlobCount| Utilize `aggregationType` "média" em vez de "último". Métrica só se aplica aos serviços de Blob |
| SASAuthorizationError | Métrica de transações com dimensões "ResponseType" = "AuthorizationError" e "Autenticação" = "SAS" | |
| SASClientOtherError | Métrica de transações com dimensões "ResponseType" = "ClientOtherError" e "Autenticação" = "SAS" | |
| SASClientTimeOutError | Métrica de transações com dimensões "ResponseType" = "ClientTimeOutError" e "Autenticação" = "SAS" | |
| SASNetworkError | Métrica de transações com dimensões "ResponseType" = "NetworkError" e "Autenticação" = "SAS" | |
| SASServerOtherError | Métrica de transações com dimensões "ResponseType" = "ServerOtherError" e "Autenticação" = "SAS" | |
| SASServerTimeOutError | Métrica de transações com dimensões "ResponseType" = "ServerTimeOutError" e "Autenticação" = "SAS" | |
| SASSuccess | Métrica de transações com dimensões "ResponseType" = "Êxito" e "Autenticação" = "SAS" | |
| ServerOtherError | Métrica de transações com dimensões "ResponseType" = "ServerOtherError" | |
| ServerTimeOutError | Métrica de transações com dimensões "ResponseType" = "ServerTimeOutError"  | |
| Êxito | Métrica de transações com dimensões "ResponseType" = "Êxito" | |
| TotalBillableRequests| Transações | |
| TotalEgress | Saída | |
| TotalIngress | Entrada | |
| TotalRequests | Transações | |

### <a name="microsoftinsightscomponents"></a>Microsoft.insights/components

Para o Application Insights, o equivalentes métricas são conforme mostrado abaixo:

| Métrica em alertas clássicos | Métrica equivalente em novos alertas | Comentários|
|--------------------------|---------------------------------|---------|
| availability.availabilityMetric.value | availabilityResults/availabilityPercentage|   |
| availability.durationMetric.value | availabilityResults/duration| Multiplique o limiar original de 1000 unidades para a métrica clássica são em segundos e para a nova um são em milissegundos.  |
| basicExceptionBrowser.count | exceptions/browser|  Utilize `aggregationType` "count" em vez de "soma". |
| basicExceptionServer.count | exceptions/server| Utilize `aggregationType` "count" em vez de "soma".  |
| clientPerformance.clientProcess.value | browserTimings/processingDuration| Multiplique o limiar original de 1000 unidades para a métrica clássica são em segundos e para a nova um são em milissegundos.  |
| clientPerformance.networkConnection.value | browserTimings/networkDuration|  Multiplique o limiar original de 1000 unidades para a métrica clássica são em segundos e para a nova um são em milissegundos. |
| clientPerformance.receiveRequest.value | browserTimings/receiveDuration| Multiplique o limiar original de 1000 unidades para a métrica clássica são em segundos e para a nova um são em milissegundos.  |
| clientPerformance.sendRequest.value | browserTimings/sendDuration| Multiplique o limiar original de 1000 unidades para a métrica clássica são em segundos e para a nova um são em milissegundos.  |
| clientPerformance.total.value | browserTimings/totalDuration| Multiplique o limiar original de 1000 unidades para a métrica clássica são em segundos e para a nova um são em milissegundos.  |
| performanceCounter.available_bytes.value | performanceCounters/memoryAvailableBytes|   |
| performanceCounter.io_data_bytes_per_sec.value | performanceCounters/processIOBytesPerSecond|   |
| performanceCounter.number_of_exceps_thrown_per_sec.value | performanceCounters/exceptionsPerSecond|   |
| performanceCounter.percentage_processor_time_normalized.value | performanceCounters/processCpuPercentage|   |
| performanceCounter.percentage_processor_time.value | performanceCounters/processCpuPercentage| Limiar tem de ser modificado adequadamente como métrica original foi em todos os núcleos e novas métricas são normalizadas para um núcleo. Ferramenta de migração não altera os limiares.  |
| performanceCounter.percentage_processor_total.value | performanceCounters/processorCpuPercentage|   |
| performanceCounter.process_private_bytes.value | performanceCounters/processPrivateBytes|   |
| performanceCounter.request_execution_time.value | performanceCounters/requestExecutionTime|   |
| performanceCounter.requests_in_application_queue.value | performanceCounters/requestsInQueue|   |
| performanceCounter.requests_per_sec.value | performanceCounters/requestsPerSecond|   |
| request.duration | requests/duration| Multiplique o limiar original de 1000 unidades para a métrica clássica são em segundos e para a nova um são em milissegundos.  |
| request.rate | taxa de pedidos /|   |
| requestFailed.count | requests/failed| Utilize `aggregationType` "count" em vez de "soma".   |
| view.count | pageViews/count| Utilize `aggregationType` "count" em vez de "soma".   |

### <a name="how-equivalent-action-groups-are-created"></a>Como são criados grupos de ação equivalente

Ações de aplicação e o runbook de lógica associadas ao alerta de alerta clássica tinham de regras de e-mail, webhook, regra em si. Novas regras de alerta utilizam grupos de ação que podem ser reutilizados em várias regras de alerta. A ferramenta de migração cria o grupo de ação única para as mesmas ações, independentemente de quantos regras de alerta estiver a utilizar a ação. Grupos de ação criados pela ferramenta de migração, utilize o formato de nomenclatura 'Migrated_AG *'.

## <a name="rollout-phases"></a>Fases de implementação

A ferramenta de migração está a implementar em fases para os clientes que utilizam regras de alerta clássicas. Proprietários de subscrições irão receber um e-mail quando a subscrição está pronta para ser migrada utilizando a ferramenta.

> [!NOTE]
> Uma vez que a ferramenta está a ser implementada em fases, poderá ver que algumas das suas subscrições ainda não estão prontas a serem migrados durante as fases iniciais.

A maioria das subscrições atualmente é marcada como prontos para a migração. Apenas as subscrições que têm alertas clássicos nos seguintes tipos de recursos são ainda que não está prontas para migração.

- Microsoft.classicCompute/domainNames/slots/roles
- Microsoft.documentDB/databases
- Microsoft.insights/components

## <a name="who-can-trigger-the-migration"></a>Quem pode acionar a migração?

Qualquer utilizador que tenha a função incorporada de Contribuidor de monitorização ao nível da subscrição pode acionar a migração. Os utilizadores que têm uma função personalizada com as seguintes permissões também podem acionar a migração:

- \* / leitura
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*

> [!NOTE]
> Além de ter acima permissões, a sua subscrição além disso deve ser registada no fornecedor de recursos de Microsoft.AlertsManagement. Isto é necessário para migrar com êxito os alertas de anomalias de falha no Application Insights. 

## <a name="common-problems-and-remedies"></a>Problemas e soluções comuns

Depois de [acionar a migração](alerts-using-migration-tool.md), irá receber um e-mail nos endereços que indicou para notificá-lo que a migração estiver concluída, ou se é necessária qualquer ação do utilizador. Esta secção descreve alguns problemas comuns e como lidar com eles.

### <a name="validation-failed"></a>Falha na validação

Devido a algumas alterações recentes para regras de alerta clássicas na sua subscrição, não é possível migrar a subscrição. Esse problema é temporário. Pode reiniciar a migração, depois do Estado de migração Retroceder **pronto para a migração** em alguns dias.

### <a name="policy-or-scope-lock-preventing-us-from-migrating-your-rules"></a>Impedir-nos de migrar as suas regras de bloqueio de política ou âmbito

Como parte da migração, serão criados novos alertas de métricas e novos grupos de ação e, em seguida, as regras de alerta clássicas serão eliminadas. No entanto, existe uma política ou o âmbito de bloqueio impedir-nos de criação de recursos. Consoante o bloqueio de política ou âmbito, não foi possível migrar algumas ou todas as regras. Pode resolver este problema ao remover o bloqueio de âmbito ou a política temporariamente e acionar novamente a migração.

## <a name="next-steps"></a>Passos Seguintes

- [Como utilizar a ferramenta de migração](alerts-using-migration-tool.md)
- [Preparar para a migração](alerts-prepare-migration.md)
