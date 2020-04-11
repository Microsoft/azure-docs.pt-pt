---
title: Compreender ferramenta de migração para alertas do Monitor Azure
description: Entenda como funciona a ferramenta de migração de alertas e problemas de resolução de problemas.
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: yalavi
author: yalavi
ms.subservice: alerts
ms.openlocfilehash: d31c856e17348c23ad61130869af6ae440d3050d
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114311"
---
# <a name="understand-how-the-migration-tool-works"></a>Compreender como funciona a ferramenta de migração

Como [já foi anunciado,](monitoring-classic-retirement.md)os alertas clássicos no Monitor Do Azure estão a ser retirados até 31 de agosto de 2019 (foi originalmente 30 de junho de 2019). Uma ferramenta de migração está disponível no portal Azure para clientes que usam regras clássicas de alerta e que querem desencadear a migração por si mesmas.

Este artigo explica como funciona a ferramenta de migração voluntária. Também descreve remédios para alguns problemas comuns.

> [!NOTE]
> Devido ao atraso no lançamento da ferramenta de migração, a data de reforma para alertas clássicos migração foi alargada para 31 de agosto de [2019](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) a partir da data inicialmente anunciada de 30 de junho de 2019.

## <a name="classic-alert-rules-that-will-not-be-migrated"></a>Regras de alerta clássicas que não serão migradas

> [!IMPORTANT]
> Os alertas de registo de atividade (incluindo alertas de saúde de serviço) e alertas de registo não são afetados pela migração. A migração aplica-se apenas às regras clássicas de alerta [aqui](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform)descritas.

Embora a ferramenta possa migrar quase todas as [regras clássicas](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform)de alerta, existem algumas exceções. As seguintes regras de alerta não serão migradas utilizando a ferramenta (ou durante a migração automática a partir de setembro de 2019):

- Regras clássicas de alerta sobre métricas de hóspedes de máquinavirtual (tanto Windows como Linux). Consulte as [orientações para recriar tais regras de alerta em novos alertas métricos](#guest-metrics-on-virtual-machines) mais tarde neste artigo.
- Regras clássicas de alerta sobre métricas de armazenamento clássicas. Consulte as [orientações para monitorizar as suas contas de armazenamento clássicas](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/).
- Regras clássicas de alerta em algumas métricas de conta de armazenamento. Veja mais tarde neste artigo [os detalhes.](#storage-account-metrics)
- Regras clássicas de alerta em algumas métricas do Cosmos DB. Veja mais tarde neste artigo [os detalhes.](#cosmos-db-metrics)
- Regras clássicas de alerta em todas as máquinas virtuais clássicas e métricas de serviços na nuvem (Microsoft.ClassicCompute/virtualMachines e Microsoft.ClassicCompute/domainNames/slots/roles). Veja mais tarde neste artigo [os detalhes.](#classic-compute-metrics)

Se a sua subscrição tiver regras clássicas, deve emigrar manualmente. Como não podemos fornecer uma migração automática, quaisquer alertas métricos clássicos e existentes deste tipo continuarão a funcionar até junho de 2020. Esta extensão dá-lhe tempo para passar para novos alertas. Pode também continuar a criar novos alertas clássicos sobre as exceções acima listadas até junho de 2020. No entanto, para tudo o resto, não podem ser criados novos alertas clássicos após agosto de 2019.

> [!NOTE]
> Além das exceções acima enumeradas, se as suas regras clássicas de alerta forem inválidas, ou seja, estão em [métricas ou](#classic-alert-rules-on-deprecated-metrics) recursos depreciados que foram eliminados, não serão migrados e não estarão disponíveis após a retirada do serviço.

### <a name="guest-metrics-on-virtual-machines"></a>Métricas de hóspedes em máquinas virtuais

Antes de poder criar novos alertas métricos sobre as métricas dos hóspedes, as métricas dos hóspedes devem ser enviadas para a loja de métricas personalizadas Do Monitor Azure. Siga estas instruções para ativar o lavatório do Monitor Azure em definições de diagnóstico:

- [Ativar métricas de hóspedes para VMs windows](collect-custom-metrics-guestos-resource-manager-vm.md)
- [Habilitar as métricas dos hóspedes para VMs Linux](collect-custom-metrics-linux-telegraf.md)

Depois destes passos serem feitos, pode criar novos alertas métricos sobre as métricas dos hóspedes. E depois de criar novos alertas métricos, pode eliminar alertas clássicos.

### <a name="storage-account-metrics"></a>Métricas da conta de armazenamento

Todos os alertas clássicos sobre contas de armazenamento podem ser migrados, exceto alertas sobre estas métricas:

- Erro de percentauthorization
- PercentclientOtherError
- Erro de Rede Por Percent
- PercentServerOtherError
- PercentSuccess
- PercentThrottlingError
- PercentTimeoutError
- Erro De Throttling Anónimo
- SASThrottlingError
- ThrottlingError

As regras clássicas de alerta sobre métricas por cento devem ser migradas com base [no mapeamento entre métricas de armazenamento antigas e novas.](https://docs.microsoft.com/azure/storage/common/storage-metrics-migration#metrics-mapping-between-old-metrics-and-new-metrics) Os limiares terão de ser modificados adequadamente porque a nova métrica disponível é absoluta.

As regras clássicas de alerta em AnonymousThrottlingError, SASThrottlingError e ThrottlingError devem ser divididas em dois novos alertas porque não existe uma métrica combinada que forneça a mesma funcionalidade. Os limiares terão de ser adaptados adequadamente.

### <a name="cosmos-db-metrics"></a>Métricas do Cosmos DB

Todos os alertas clássicos sobre as métricas de Db cosmos podem ser migrados, exceto alertas sobre estas métricas:

- Pedidos médios por Segundo
- Nível de Consistência
- Http 2xx
- Http 3xx
- Http 400
- Http 401
- Erro interno do servidor
- Max RUPM consumido por minuto
- Max Rus por segundo
- Pedidos falhados do Conde de Mongo
- Mongo eliminar pedidos falhados
- Inserção de Mongo Pedidos Falhados
- Mongo Outros Pedidos Falhados
- Mongo Outros Pedidos
- Taxa de Pedido de Mongo
- Pedidos falhados da consulta de Mongo
- Pedidos falhados da atualização do Mongo
- Latência de leitura observada
- Latência de Escrita Observada
- Disponibilidade de Serviço
- Capacidade de Armazenamento
- Pedidos estrangulados
- Total de Pedidos

Pedidos médios por segundo, Nível de Consistência, Max RUPM consumido por minuto, Max RUs por segundo, Latência de Leitura Observada, Latência de Escrita Observada, Capacidade de Armazenamento não estão atualmente disponíveis no [novo sistema.](metrics-supported.md#microsoftdocumentdbdatabaseaccounts)

Alertas sobre métricas de pedidos como Http 2xx, Http 3xx, Http 400, Http 401, Erro de Servidor Interno, Disponibilidade de Serviço, Pedidos Throttled e Pedidos Totais não são migrados porque a forma como os pedidos são contados é diferente entre métricas clássicas e novas métricas. Os alertas sobre estes terão de ser recriados manualmente com os limiares ajustados.

Os alertas sobre as métricas de Pedidos Falhados de Mongo devem ser divididos em múltiplos alertas porque não existe uma métrica combinada que forneça a mesma funcionalidade. Os limiares terão de ser adaptados adequadamente.

### <a name="classic-compute-metrics"></a>Métricas de computação clássicas

Quaisquer alertas sobre métricas clássicas de cálculo não serão migrados usando a ferramenta de migração, uma vez que os recursos computacionais clássicos ainda não são suportados com novos alertas. No futuro, será adicionado apoio a novos alertas sobre estes tipos de recursos. Uma vez que isso esteja disponível, os clientes devem recriar novas regras de alerta equivalentes com base nas suas regras clássicas de alerta antes de junho de 2020.

### <a name="classic-alert-rules-on-deprecated-metrics"></a>Regras clássicas de alerta sobre métricas depreciadas

Estas são regras clássicas de alerta sobre métricas que foram previamente apoiadas mas que acabaram por ser depreciadas. Uma pequena percentagem de cliente pode ter inválidas regras de alerta clássicos sobre tais métricas. Como estas regras de alerta são inválidas, não serão migradas.

| Tipo de recurso| Métricas depreciadas |
|-------------|----------------- |
| Microsoft.DBforMySQL/servidores | compute_consumption_percent, compute_limit |
| Microsoft.DBforPostgreSQL/servidores | compute_consumption_percent, compute_limit |
| Microsoft.Network/publicIPAddresss | defaultddostriggerrate |
| Microsoft.SQL/servidores/bases de dados | service_level_objetive, storage_limit, storage_used, estrangulando, dtu_consumption_percent, storage_used |
| Microsoft.Web/hostingEnvironments/multirolepools | conjunto de trabalho de memória média |
| Microsoft.Web/hostingEnvironments/workerpools | bytesreceived, httpqueuelength |

## <a name="how-equivalent-new-alert-rules-and-action-groups-are-created"></a>Como são criadas novas regras de alerta equivalentes e grupos de ação

A ferramenta de migração converte as suas regras clássicas de alerta para novas regras de alerta equivalentes e grupos de ação. Para a maioria das regras clássicas de alerta, novas regras `windowSize` `aggregationType`de alerta equivalentes estão na mesma métrica com as mesmas propriedades como e . No entanto, existem algumas regras clássicas de alerta sobre métricas que têm uma métrica diferente e equivalente no novo sistema. Os seguintes princípios aplicam-se à migração de alertas clássicos, salvo especificação na secção abaixo:

- **Frequência**: Define com que frequência uma regra de alerta clássica ou nova verifica a circunstância. As `frequency` regras clássicas de alerta não eram configuráveis pelo utilizador e eram sempre 5 minutos para todos os tipos de recursos, exceto componentes de Application Insights para os quais era de 1 min. Assim, a frequência de regras equivalentes também está definida para 5 min e 1 min respectivamente.
- **Tipo de agregação**: Define como a métrica é agregada sobre a janela de interesse. O `aggregationType` é também o mesmo entre alertas clássicos e novos alertas para a maioria das métricas. Em alguns casos, uma vez que a métrica `aggregationType` é `primary Aggregation Type` diferente entre alertas clássicos e novos alertas, equivalente ou definido para a métrica é usado.
- **Unidades**: Propriedade da métrica em que o alerta é criado. Algumas métricas equivalentes têm unidades diferentes. O limiar é ajustado adequadamente conforme necessário. Por exemplo, se a métrica original tiver segundos como unidades, mas uma nova métrica equivalente tem milissegundos como unidades, o limiar original é multiplicado por 1000 para garantir o mesmo comportamento.
- **Tamanho da janela**: Define a janela sobre a qual os dados métricos são agregados para comparar com o limiar. Para `windowSize` valores padrão como 5mins, 15mins, 30mins, 1hora, 3horas, 6 horas, 12 horas, 1 dia, não há alteração para nova regra de alerta equivalente. Para outros valores, `windowSize` o mais próximo é escolhido para ser usado. Para a maioria dos clientes, não há impacto com esta mudança. Para uma pequena percentagem de clientes, pode haver a necessidade de ajustar o limiar para obter exatamente o mesmo comportamento.

Nas seguintes secções, detalhamos as métricas que têm uma métrica diferente e equivalente no novo sistema. Qualquer métrica que permaneça a mesma para regras clássicas e novas de alerta não está listada. Pode encontrar aqui uma lista de métricas suportadas no novo [sistema.](metrics-supported.md)

### <a name="microsoftstorageaccountsservices"></a>Microsoft.StorageAccounts/services

Para serviços de conta de armazenamento como blob, mesa, arquivo e fila, as seguintes métricas são mapeadas para métricas equivalentes, como mostrado abaixo:

| Métrica em alertas clássicos | Métrica equivalente em novos alertas | Comentários|
|--------------------------|---------------------------------|---------|
| Erro de Autorização Anónima| Métrica de transações com dimensões "ResponseType="AuthorizationError" e "Autenticação" = "Anónimo"| |
| Erro de Cliente Anónimo | Métrica de transações com dimensões "ResponseType="ClientOtherError" e "Autenticação" = "Anónimo" | |
| Erro de timeoutdo cliente anónimo| Métrica de transações com dimensões "ResponseType"="ClientTimeOutError" e "Autenticação" = "Anónimo" | |
| Erro de Rede Anónimo | Métrica de transações com dimensões "ResponseType="Error de rede" e "Autenticação" = "Anónimo" | |
| Erro de outro servidor anónimo | Métrica de transações com dimensões "ResponseType="ServerOtherError" e "Autenticação" = "Anónimo" | |
| Erro de timeoutdo do servidor anónimo | Métrica de transações com dimensões "ResponseType"="ServerTimeOutError" e "Autenticação" = "Anónimo" | |
| Sucesso Anónimo | Métrica de transações com dimensões "ResponseType="Sucesso" e "Autenticação" = "Anónimo" | |
| Erro de Autorização | Métrica de transações com dimensões "ResponseType="AuthorizationError" | |
| AverageE2ELatency | SuccessE2ELatency | |
| AverageServerLatency | SuccessServerLatency | |
| Capacidade | BlobCapacity | Use `aggregationType` "médio" em vez de "último". A métrica aplica-se apenas aos serviços Blob |
| Erro clientotherError | Métrica de transações com dimensões "ResponseType="ClientOtherError"  | |
| Error Timeout do cliente | Métrica de transações com dimensões "ResponseType="ClientTimeOutError" | |
| Contagem de contentores | Contagem de contentores | Use `aggregationType` "médio" em vez de "último". A métrica aplica-se apenas aos serviços Blob |
| NetworkError | Métrica de transações com dimensões "ResponseType="Error networkerror" | |
| Contagem de Objetos | BlobCount| Use `aggregationType` "médio" em vez de "último". A métrica aplica-se apenas aos serviços Blob |
| Erro de Autorização sas | Métrica de transações com dimensões "ResponseType="AuthorizationError" e "Autenticação" = "SAS" | |
| Erro SASClientOtherError | Métrica de transações com dimensões "ResponseType="ClientOtherError" e "Autenticação" = "SAS" | |
| Erro de timeouterror SASClientTime | Métrica de transações com dimensões "ResponseType"="ClientTimeOutError" e "Autenticação" = "SAS" | |
| Erro sasNetwork | Métrica de transações com dimensões "ResponseType="Error de rede" e "Autenticação" = "SAS" | |
| Erro SASServerOtherError | Métrica de transações com dimensões "ResponseType="ServerOtherError" e "Autenticação" = "SAS" | |
| Erro de timeouterror sASServer | Métrica de transações com dimensões "ResponseType"="ServerTimeOutError" e "Autenticação" = "SAS" | |
| SASSuccess | Métrica de transações com dimensões "ResponseType="Success" e "Autenticação" = "SAS" | |
| Error serverOtherError | Métrica de transações com dimensões "ResponseType="ServerOtherError" | |
| Error timeout do servidor | Métrica de transações com dimensões "ResponseType="ServerTimeOutError"  | |
| Êxito | Métrica de transações com dimensões "ResponseType="Sucesso" | |
| TotalBillableRequests| Transações | |
| TotalEgress | Saída | |
| TotalIngress | Entrada | |
| TotalRequests | Transações | |

### <a name="microsoftinsightscomponents"></a>Microsoft.insights/componentes

Para insights de aplicação, as métricas equivalentes são como mostrado abaixo:

| Métrica em alertas clássicos | Métrica equivalente em novos alertas | Comentários|
|--------------------------|---------------------------------|---------|
| disponibilidade.disponibilidadeMetric.value | disponibilidadeResultados/disponibilidadePercentage|   |
| disponibilidade.duraÇãoMetric.value | disponibilidadeResultados/duração| Multiplique o limiar original em 1000, uma vez que as unidades para métricaclássica estão em segundos e para uma nova estão em milissegundos.  |
| basicExceptionBrowser.count | exceções/navegador|  Use `aggregationType` "contar" em vez de "soma". |
| basicExceptionServer.count | exceções/servidor| Use `aggregationType` "contar" em vez de "soma".  |
| clientePerformance.clienteProcess.value | navegadorTimings/processamentoDuração| Multiplique o limiar original em 1000, uma vez que as unidades para métricaclássica estão em segundos e para uma nova estão em milissegundos.  |
| clientePerformance.networkConnection.value | navegadorTimings/redeDura|  Multiplique o limiar original em 1000, uma vez que as unidades para métricaclássica estão em segundos e para uma nova estão em milissegundos. |
| clientePerformance.receiveRequest.value | navegadorTimings/recepçãoDura| Multiplique o limiar original em 1000, uma vez que as unidades para métricaclássica estão em segundos e para uma nova estão em milissegundos.  |
| clientePerformance.sendRequest.value | navegadorTimings/sendDura| Multiplique o limiar original em 1000, uma vez que as unidades para métricaclássica estão em segundos e para uma nova estão em milissegundos.  |
| clientePerformance.total.valor | browserTimings/totalDuração| Multiplique o limiar original em 1000, uma vez que as unidades para métricaclássica estão em segundos e para uma nova estão em milissegundos.  |
| performanceCounter.available_bytes.value | desempenhoCounters/memoryAvailableBytes|   |
| performanceCounter.io_data_bytes_per_sec.value | performanceCounters/processIOBytesPerSecond|   |
| performanceCounter.number_of_exceps_thrown_per_sec.value | desempenhoCounters/excepçõesPerSecond|   |
| performanceCounter.percentage_processor_time_normalized.value | performanceCounters/processCpuPercentage|   |
| performanceCounter.percentage_processor_time.value | performanceCounters/processCpuPercentage| O limiar terá de ser devidamente modificado, uma vez que a métrica original foi em todos os núcleos e a nova métrica é normalizada para um núcleo. A ferramenta de migração não muda os limiares.  |
| performanceCounter.percentage_processor_total.value | desempenhoCounters/processadorCpuPercentage|   |
| performanceCounter.process_private_bytes.value | performanceCounters/processPrivateBytes|   |
| performanceCounter.request_execution_time.value | performanceCounters/requestExecutionTime|   |
| performanceCounter.requests_in_application_queue.value | performanceCounters/requestsInQueue|   |
| performanceCounter.requests_per_sec.value | performanceCounters/requestsPerSecond|   |
| pedido.duração | pedidos/duração| Multiplique o limiar original em 1000, uma vez que as unidades para métricaclássica estão em segundos e para uma nova estão em milissegundos.  |
| pedido.taxa | pedidos/taxa|   |
| pedidoFailed.count | pedidos/falhado| Use `aggregationType` "contar" em vez de "soma".   |
| vista.contar | pageViews/count| Use `aggregationType` "contar" em vez de "soma".   |

### <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

Para cosmos DB, as métricas equivalentes são como mostrado abaixo:

| Métrica em alertas clássicos | Métrica equivalente em novos alertas | Comentários|
|--------------------------|---------------------------------|---------|
| Armazenamento Disponível     |Armazenamento Disponível|   |
| Tamanho dos dados | Utilização de dados| |
| Contagem de Documentos | Contagem de documentos||
| Tamanho do índice | Utilização de índices||
| Taxa de pedido de contagem de Mongo| MongoRequestCharge com dimensão "Nome de comando" = "contagem"||
| Taxa de pedido de contagem de Mongo | MongoRequestsCount com dimensão "Nome de comando" = "contagem"||
| Mongo Excluir Taxa de Pedido | MongoRequestCharge com dimensão "Nome de comando" = "excluir"||
| Taxa de pedido de exclusão de Mongo | MongoRequestsCount com dimensão "Nome de comando" = "excluir"||
| Taxa de pedido de inserção de Mongo | MongoRequestCharge com dimensão "Nome de comando" = "inserir"||
| Taxa de pedido de inserção de Mongo | MongoRequestsCount com dimensão "Nome de comando" = "inserir"||
| Taxa de pedido de consulta de Mongo | MongoRequestCharge com dimensão "Nome de comando" = "encontrar"||
| Taxa de pedido de consulta de Mongo | MongoRequestsCount com dimensão "Nome de comando" = "encontrar"||
| Taxa de pedido de atualização de Mongo | MongoRequestCharge com dimensão "Nome de comando" = "atualização"||
| Serviço Indisponível| ServiçoDisponibilidade||
| TotaldeUnidades de Pedidos | TotaldeUnidades de Pedidos||

### <a name="how-equivalent-action-groups-are-created"></a>Como são criados grupos de ação equivalentes

As regras clássicas de alerta tinham e-mail, webhook, aplicação lógica e ações de livro de corridas ligadas à regra de alerta em si. As novas regras de alerta utilizam grupos de ação que podem ser reutilizados através de várias regras de alerta. A ferramenta de migração cria um único grupo de ação para as mesmas ações, independentemente de quantas regras de alerta estão a usar a ação. Os grupos de ação criados pela ferramenta de migração utilizam o formato de nomeação 'Migrated_AG*'.

> [!NOTE]
> Os alertas clássicos enviaram e-mails localizados com base na localização do administrador clássico quando usados para notificar funções clássicas de administrador. Novos e-mails de alerta são enviados através de Action Groups e são apenas em inglês.

## <a name="rollout-phases"></a>Fases de implementação

A ferramenta de migração está a desenrolar-se por fases para os clientes que usam regras clássicas de alerta. Os proprietários de subscrições receberão um e-mail quando a subscrição estiver pronta para ser migrada utilizando a ferramenta.

> [!NOTE]
> Como a ferramenta está a ser lançada por fases, pode ver que algumas das suas subscrições ainda não estão prontas para serem migradas durante as fases iniciais.

A maioria das subscrições estão atualmente marcadas como prontas para a migração. Apenas as subscrições que têm alertas clássicos sobre os seguintes tipos de recursos ainda não estão prontas para a migração.

- Microsoft.classicCompute/domainNames/slots/roles
- Microsoft.insights/componentes

## <a name="who-can-trigger-the-migration"></a>Quem pode desencadear a migração?

Qualquer utilizador que tenha o papel integrado de Colaborador de Monitorização ao nível da subscrição pode desencadear a migração. Os utilizadores que tenham uma função personalizada com as seguintes permissões também podem desencadear a migração:

- */ler
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*
- Microsoft.AlertsManagement/smartDetectorAlertRules/*

> [!NOTE]
> Além de ter permissões acima, a sua subscrição deve ser registada adicionalmente no Microsoft.AlertsManagement fornecedor de recursos. Isto é necessário para migrar com sucesso alertas de Anomalia de Falha em Insights de Aplicação. 

## <a name="common-problems-and-remedies"></a>Problemas e remédios comuns

Depois de [desencadear a migração,](alerts-using-migration-tool.md)receberá e-mail nos endereços que forneceu para notificá-lo de que a migração está completa ou se for necessária qualquer ação por si. Esta secção descreve alguns problemas comuns e como lidar com eles.

### <a name="validation-failed"></a>Validação falhada

Devido a algumas alterações recentes às regras clássicas de alerta na sua subscrição, a subscrição não pode ser migrada. Este problema é temporário. Pode reiniciar a migração depois de o estatuto de migração voltar pronto para a **migração** em poucos dias.

### <a name="scope-lock-preventing-us-from-migrating-your-rules"></a>Bloqueio de âmbito que nos impede de migrar as suas regras

Como parte da migração, serão criados novos alertas métricos e novos grupos de ação, e depois serão eliminadas as regras clássicas de alerta. No entanto, um bloqueio de âmbito pode impedir-nos de criar ou eliminar recursos. Dependendo do bloqueio de âmbito, algumas ou todas as regras não podiam ser migradas. Pode resolver este problema removendo o bloqueio de âmbito para a subscrição, grupo de recursos ou recurso, que está listado na ferramenta de [migração,](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel)e desencadeando novamente a migração. O bloqueio de âmbito não pode ser desativado e deve ser removido durante o processo de migração. [Saiba mais sobre a gestão](../../azure-resource-manager/management/lock-resources.md#portal)de fechaduras de âmbito.

### <a name="policy-with-deny-effect-preventing-us-from-migrating-your-rules"></a>Política com efeito 'Negar' impedindo-nos de migrar as suas regras

Como parte da migração, serão criados novos alertas métricos e novos grupos de ação, e depois serão eliminadas as regras clássicas de alerta. No entanto, uma política pode impedir-nos de criar recursos. Dependendo da política, algumas ou todas as regras não podiam ser migradas. As políticas que estão a bloquear o processo constam da ferramenta de [migração.](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel) Resolver este problema por qualquer um dos dois:

- Excluindo as subscrições ou grupos de recursos durante a duração do processo de migração a partir da atribuição de políticas. [Saiba mais sobre a gestão](../../governance/policy/tutorials/create-and-manage.md#exempt-a-non-compliant-or-denied-resource-using-exclusion)do âmbito de exclusão de políticas.
- Remoção ou alteração do efeito para "auditoria" ou "apêndice" (que, por exemplo, pode resolver questões relacionadas com as etiquetas em falta). [Saiba mais sobre como gerir o efeito das políticas.](../../governance/policy/concepts/definition-structure.md#policy-rule)

## <a name="next-steps"></a>Passos seguintes

- [Como utilizar a ferramenta de migração](alerts-using-migration-tool.md)
- [Preparar para a migração](alerts-prepare-migration.md)
