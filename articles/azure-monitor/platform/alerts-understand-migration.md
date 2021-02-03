---
title: Compreender a migração para alertas do Monitor Azure
description: Entenda como funciona a migração dos alertas e resolve problemas.
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: yalavi
author: yalavi
ms.subservice: alerts
ms.openlocfilehash: e57b3dd31455db245103469874c517fe54479110
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526912"
---
# <a name="understand-migration-options-to-newer-alerts"></a>Compreender opções de migração para alertas mais recentes

Os alertas clássicos são [retirados](./monitoring-classic-retirement.md) para os utilizadores da nuvem pública, embora ainda em uso limitado para recursos que ainda não suportam os novos alertas. Em breve será anunciada uma nova data para os restantes alertas de migração, [nuvem do Governo Azure](../../azure-government/documentation-government-welcome.md)e [Azure China 21Vianet](https://docs.azure.cn/).

Este artigo explica como funciona a ferramenta de migração manual e de migração voluntária, que será usada para migrar as restantes regras de alerta. Também descreve remédios para alguns problemas comuns.

> [!IMPORTANT]
> Os alertas de registo de atividade (incluindo alertas de saúde do Serviço) e os alertas de log não são afetados pela migração. A migração aplica-se apenas às regras clássicas de alerta descritas [aqui.](monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform)

> [!NOTE]
> Se as suas regras clássicas de alerta forem inválidas, ou seja, se estiverem em métricas ou recursos [precotados](#classic-alert-rules-on-deprecated-metrics) que tenham sido eliminados, não serão migrados e não estarão disponíveis após o serviço ser retirado.

## <a name="manually-migrating-classic-alerts-to-newer-alerts"></a>Migrar manualmente alertas clássicos para alertas mais recentes

Os clientes interessados em migrar manualmente os seus restantes alertas já podem fazê-lo utilizando as seguintes secções. Estas secções também definem métricas que são aposentadas pelo fornecedor de recursos e que atualmente não podem ser migradas diretamente.

### <a name="guest-metrics-on-virtual-machines"></a>Métricas de hóspedes em máquinas virtuais

Antes de criar novos alertas métricos sobre as métricas dos hóspedes, as métricas dos hóspedes devem ser enviadas para a loja de métricas personalizadas do Azure Monitor. Siga estas instruções para permitir que o Monitor Azure se afunde em definições de diagnóstico:

- [Habilitando métricas de hóspedes para VMs Windows](collect-custom-metrics-guestos-resource-manager-vm.md)
- [Habilitando métricas de hóspedes para Linux VMs](collect-custom-metrics-linux-telegraf.md)

Depois destes passos, pode criar novos alertas métricos sobre as métricas dos hóspedes. E depois de criar novos alertas métricos, pode apagar alertas clássicos.

### <a name="storage-account-metrics"></a>Métricas da conta de armazenamento

Todos os alertas clássicos nas contas de armazenamento podem ser migrados, exceto alertas nestas métricas:

- PercentAuthorizationError
- PercentClientOtherError
- PercentNetworkError
- PercentServerOtherError
- PercentSuccess
- PercentThrottlingError
- PercentTimeoutError
- Anônimos
- SASThrottlingError
- ThrottlingError

As regras clássicas de alerta sobre as métricas por cento devem ser migradas com base no [mapeamento entre métricas antigas e novas de armazenamento](../../storage/common/storage-metrics-migration.md#metrics-mapping-between-old-metrics-and-new-metrics). Os limiares terão de ser modificados adequadamente, uma vez que a nova métrica disponível é absoluta.

As regras clássicas de alerta em AnonymousThrottlingError, SASThrottlingError e ThrottlingError devem ser divididas em dois novos alertas porque não existe uma métrica combinada que forneça a mesma funcionalidade. Os limiares terão de ser adaptados adequadamente.

### <a name="cosmos-db-metrics"></a>Métricas do Cosmos DB

Todos os alertas clássicos sobre as métricas do Cosmos DB podem ser migrados, exceto alertas nestas métricas:

- Pedidos médios por segundo
- Nível de Consistência
- Http 2xx
- Http 3xx
- Http 400
- Http 401
- Erro interno do servidor
- Max RUPM Consumido por Minuto
- Max RUs por segundo
- Pedidos falhados do Conde de Mongo
- Mongo elimina pedidos falhados
- Inserção de Mongo Pedidos Falhados
- Mongo Outros Pedidos Falhados
- Mongo Outro Pedido
- Outra Taxa de Pedido de Mongo
- Pedidos falhados da Mongo Query
- Pedidos falhados da Atualização de Mongo
- Latência de leitura observada
- Latência de escrita observada
- Disponibilidade de serviço
- Capacidade de Armazenamento
- Pedidos de Aceleração
- Total de Pedidos

Pedidos médios por Segundo, Nível de Consistência, Max RUPM Consumido por Minuto, Max RUs por segundo, Latência de Leitura Observada, Latência de Escrita Observada, Capacidade de Armazenamento não estão atualmente disponíveis no [novo sistema](metrics-supported.md#microsoftdocumentdbdatabaseaccounts).

Alertas sobre métricas de pedido como Http 2xx, Http 3xx, Http 400, Http 401, Internal Server Error, Service Availability, Throttled Requests e Total Requests não são migrados porque a forma como os pedidos são contados é diferente entre métricas clássicas e novas métricas. Os alertas sobre estes terão de ser recriados manualmente com limiares ajustados.

Os alertas sobre as métricas de Pedidos Falhados de Mongo devem ser divididos em múltiplos alertas porque não existe uma métrica combinada que forneça a mesma funcionalidade. Os limiares terão de ser adaptados adequadamente.

### <a name="classic-compute-metrics"></a>Métricas de computação clássicas

Quaisquer alertas sobre métricas de computação clássicas não serão migrados usando a ferramenta de migração, uma vez que os recursos de computação clássico ainda não são suportados com novos alertas. O suporte a novos alertas sobre estes tipos de recursos encontra-se atualmente em visualização pública e os clientes podem recriar novas regras de alerta equivalentes com base nas suas regras clássicas de alerta.

### <a name="classic-alert-rules-on-deprecated-metrics"></a>Regras clássicas de alerta sobre métricas precadas

Estas são regras clássicas de alerta sobre métricas que foram previamente apoiadas mas que acabaram por ser depreciadas. Uma pequena percentagem de clientes pode ter regras de alerta clássicas inválidas em tais métricas. Como estas regras de alerta são inválidas, não serão migradas.

| Tipo de recurso| Métricas preprecadas |
|-------------|----------------- |
| Microsoft.DBforMySQL/servidores | compute_consumption_percent, compute_limit |
| Microsoft.DBforPostgreSQL/servidores | compute_consumption_percent, compute_limit |
| Microsoft.Network/publicIPAddresses | incumprimento dotriggerrate |
| Microsoft.SQL/servidores/bases de dados | service_level_objetive, storage_limit, storage_used, dtu_consumption_percent, storage_used |
| Microsoft.Web/hostingEnvironments/multirolepools | conjunto de trabalhos de economia média |
| Microsoft.Web/hostingEnvironments/workerpools | bytesreceived, httpqueuelength |

## <a name="how-equivalent-new-alert-rules-and-action-groups-are-created"></a>Como são criadas novas regras de alerta e grupos de ação equivalentes

A ferramenta de migração converte as suas regras clássicas de alerta para novas regras de alerta e grupos de ação equivalentes. Para a maioria das regras clássicas de alerta, novas regras de alerta equivalentes estão na mesma métrica com as mesmas propriedades como `windowSize` e `aggregationType` . No entanto, existem algumas regras clássicas de alerta em métricas que têm uma métrica diferente, equivalente no novo sistema. Os seguintes princípios aplicam-se à migração de alertas clássicos, salvo especificação na secção abaixo:

- **Frequência**: Define com que frequência uma regra de alerta clássica ou nova verifica a circunstância. As `frequency` regras de alerta clássicas não eram configuráveis pelo utilizador e eram sempre de 5 minutos para todos os tipos de recursos, com exceção dos componentes da Application Insights para os quais era 1 min. Assim, a frequência das regras equivalentes também é definida para 5 min e 1 min respectivamente.
- **Tipo de agregação**: Define como a métrica é agregada sobre a janela de interesse. O `aggregationType` mesmo é o mesmo entre alertas clássicos e novos alertas para a maioria das métricas. Em alguns casos, uma vez que a métrica é diferente entre alertas clássicos e novos alertas, é usado o equivalente `aggregationType` ou o definido para a `primary Aggregation Type` métrica.
- **Unidades**: Propriedade da métrica em que o alerta é criado. Algumas métricas equivalentes têm unidades diferentes. O limiar é ajustado adequadamente conforme necessário. Por exemplo, se a métrica original tiver segundos como unidades mas a nova métrica equivalente tiver milliSegundos como unidades, o limiar original é multiplicado por 1000 para garantir o mesmo comportamento.
- **Tamanho da janela**: Define a janela sobre a qual os dados métricos são agregados para comparar com o limiar. Para valores padrão `windowSize` como 5mins, 15mins, 30mins, 1h, 3horas, 6 horas, 12 horas, 1 dia, não há alterações feitas para a regra de novo alerta equivalente. Para outros valores, o mais próximo `windowSize` é escolhido para ser usado. Para a maioria dos clientes, não há impacto com esta mudança. Para uma pequena percentagem de clientes, pode haver a necessidade de ajustar o limiar para obter exatamente o mesmo comportamento.

Nas secções seguintes, detalhamos as métricas que têm uma métrica diferente, equivalente no novo sistema. Qualquer métrica que permaneça a mesma para regras clássicas e novas de alerta não está listada. Pode encontrar uma lista de métricas suportadas no novo sistema [aqui.](metrics-supported.md)

### <a name="microsoftstorageaccountsservices"></a>Microsoft.StorageAccounts/serviços

Para serviços de conta de armazenamento como bolha, tabela, arquivo e fila, as seguintes métricas são mapeadas para métricas equivalentes como mostrado abaixo:

| Métrica em alertas clássicos | Métrica equivalente em novos alertas | Comentários|
|--------------------------|---------------------------------|---------|
| AnónimoAuthorizationError| Métrica de transações com dimensões "ResponseType"="AuthorizationError" e "Authentication" = "Anonymous"| |
| AnónimoClientOtherError | Métrica de transações com dimensões "ResponseType"="ClientOtherError" e "Autenticação" = "Anónimo" | |
| AnónimoClientTimeOutError| Métrica de transações com dimensões "ResponseType"="ClientTimeOutError" e "Autenticação" = "Anónimo" | |
| AnónimoNetworkError | Métrica de transações com dimensões "ResponseType"="NetworkError" e "Autenticação" = "Anónimo" | |
| AnónimoServerOtherError | Métrica de transações com dimensões "ResponseType"="ServerOtherError" e "Autenticação" = "Anónimo" | |
| AnónimoServerTimeOutError | Métrica de transações com dimensões "ResponseType"="ServerTimeOutError" e "Autenticação" = "Anónimo" | |
| AnónimoSuccess | Métrica de transações com dimensões "ResponseType"="Sucesso" e "Autenticação" = "Anónimo" | |
| AutorizaçãoError | Métrica de transações com dimensões "ResponseType"="AuthorizationError" | |
| AverageE2ELatency | SucessoE2ELatency | |
| AverageServerLatency | SucessoServerLatency | |
| Capacidade | Capacidade blob | Utilize `aggregationType` a 'média' em vez de 'last'. A métrica só se aplica aos serviços Blob |
| ClientOtherError | Métrica de transações com dimensões "ResponseType"="ClientOtherError"  | |
| ClientTimeoutError | Métrica de transações com dimensões "ResponseType"="ClientTimeOutError" | |
| Contagem de contentores | Contagem de contentores | Utilize `aggregationType` a 'média' em vez de 'last'. A métrica só se aplica aos serviços Blob |
| NetworkError | Métrica de transações com dimensões "ResponseType"="NetworkError" | |
| Conta de Objetos | BlobCount| Utilize `aggregationType` a 'média' em vez de 'last'. A métrica só se aplica aos serviços Blob |
| SASAuthorizationError | Métrica de transações com dimensões "ResponseType"="AuthorizationError" e "Authentication" = "SAS" | |
| SASClientOtherError | Métrica de transações com dimensões "ResponseType"="ClientOtherError" e "Autenticação" = "SAS" | |
| SASClientTimeOutError | Métrica de transações com dimensões "ResponseType"="ClientTimeOutError" e "Autenticação" = "SAS" | |
| SASNetworkError | Métrica de transações com dimensões "ResponseType"="NetworkError" e "Autenticação" = "SAS" | |
| SASServerOtherError | Métrica de transações com dimensões "ResponseType"="ServerOtherError" e "Autenticação" = "SAS" | |
| SASServerTimeOutError | Métrica de transações com dimensões "ResponseType"="ServerTimeOutError" e "Autenticação" = "SAS" | |
| SASSuccess | Métrica de transações com dimensões "ResponseType"="Sucesso" e "Autenticação" = "SAS" | |
| ServerOtherError | Métrica de transações com dimensões "ResponseType"="ServerOtherError" | |
| ServerTimeOutError | Métrica de transações com dimensões "ResponseType"="ServerTimeOutError"  | |
| Com êxito | Métrica de transações com dimensões "ResponseType"="Sucesso" | |
| TotalBillableRequests| Transações | |
| TotalEgress | Saída | |
| TotalIngress | Entrada | |
| TotalRequests | Transações | |

### <a name="microsoftinsightscomponents"></a>Microsoft.insights/componentes

Para insights de aplicação, as métricas equivalentes são como mostrado abaixo:

| Métrica em alertas clássicos | Métrica equivalente em novos alertas | Comentários|
|--------------------------|---------------------------------|---------|
| disponibilidade.disponibilidadeMétrico.valor | disponibilidadeResults/disponibilidadePercentage|   |
| disponibilidade.duraçãoMétrico.valor | disponibilidadeResults/duração| Multiplique o limiar original por 1000 como unidades para métrica clássica são em segundos e para um novo estão em milliSegundos.  |
| basicExceptionBrowser.count | exceções/navegador|  Utilize `aggregationType` 'contagem' em vez de 'soma'. |
| basicExceptionServer.count | exceções/servidor| Utilize `aggregationType` 'contagem' em vez de 'soma'.  |
| clientePerformance.clientProcess.value | browserEs/processamentoDuração| Multiplique o limiar original por 1000 como unidades para métrica clássica são em segundos e para um novo estão em milliSegundos.  |
| clientePerformance.networkConnection.value | browserEs/networkDuration|  Multiplique o limiar original por 1000 como unidades para métrica clássica são em segundos e para um novo estão em milliSegundos. |
| clientePerformance.receiveRequest.value | browserEs/receberDuration| Multiplique o limiar original por 1000 como unidades para métrica clássica são em segundos e para um novo estão em milliSegundos.  |
| clientePerformance.sendRequest.value | browserEs/sendDuration| Multiplique o limiar original por 1000 como unidades para métrica clássica são em segundos e para um novo estão em milliSegundos.  |
| clientePerformance.total.value | browserEs/totalDuration| Multiplique o limiar original por 1000 como unidades para métrica clássica são em segundos e para um novo estão em milliSegundos.  |
| performanceCounter.available_bytes.value | performanceCounters/memoryAvailableBytes|   |
| performanceCounter.io_data_bytes_per_sec.value | performanceCounters/processIOBytesPerSecond|   |
| performanceCounter.number_of_exceps_thrown_per_sec.value | performanceCounters/excepçõesPerSecond|   |
| performanceCounter.percentage_processor_time_normalized.value | performanceCounters/processCpuPercentage|   |
| performanceCounter.percentage_processor_time.value | performanceCounters/processCpuPercentage| O limiar terá de ser modificado adequadamente, uma vez que a métrica original foi em todos os núcleos e a nova métrica é normalizada para um núcleo. A ferramenta de migração não altera os limiares.  |
| performanceCounter.percentage_processor_total.value | performanceCounters/processadorEsPercentage|   |
| performanceCounter.process_private_bytes.value | performanceCounters/processPrivateBytes|   |
| performanceCounter.request_execution_time.value | performanceCounters/requestExecutionTime|   |
| performanceCounter.requests_in_application_queue.value | performanceCounters/requestsInQueue|   |
| performanceCounter.requests_per_sec.value | performanceCounters/requestsPerSecond|   |
| pedido.duração | pedidos/duração| Multiplique o limiar original por 1000 como unidades para métrica clássica são em segundos e para um novo estão em milliSegundos.  |
| pedido.taxa | pedidos/tarifa|   |
| requestFailed.count | pedidos/falhados| Utilize `aggregationType` 'contagem' em vez de 'soma'.   |
| view.count | pageVers/contagem| Utilize `aggregationType` 'contagem' em vez de 'soma'.   |

### <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/base de dadosAcontas

Para cosmos DB, as métricas equivalentes são como mostrado abaixo:

| Métrica em alertas clássicos | Métrica equivalente em novos alertas | Comentários|
|--------------------------|---------------------------------|---------|
| DisponíveisToragem     |DisponíveisToragem|   |
| Tamanho dos Dados | DataUsage| |
| Contagem de documentos | Contagem de documentos||
| Tamanho do índice | IndexUsage||
| Taxa de pedido de contagem de Mongo| MongoRequestCharge com dimensão "CommandName" = "contagem"||
| Taxa de pedido de contagem de Mongo | MongoRequestsCount com dimensão "CommandName" = "contagem"||
| Mongo Elimina Taxa de Pedido | MongoRequestCharge com dimensão "CommandName" = "excluir"||
| Tarifa de pedido de eliminação de Mongo | MongoRequestsCount com dimensão "CommandName" = "excluir"||
| Taxa de pedido de inserção de Mongo | MongoRequestCharge com dimensão "CommandName" = "inserir"||
| Taxa de pedido de inserção de Mongo | MongoRequestsCount com dimensão "CommandName" = "inserir"||
| Taxa de pedido de consulta de Mongo | MongoRequestCharge com dimensão "CommandName" = "Find"||
| Taxa de pedido de consulta de Mongo | MongoRequestsCount com dimensão "CommandName" = "find"||
| Taxa de pedido de atualização de Mongo | MongoRequestCharge com dimensão "CommandName" = "update"||
| Serviço Indisponível| ServiçoSAdisponibilidade||
| TotalRequestUnits | TotalRequestUnits||

### <a name="how-equivalent-action-groups-are-created"></a>Como grupos de ação equivalentes são criados

As regras clássicas de alerta tinham e-mail, webhook, aplicação lógica e ações de runbook ligadas à própria regra de alerta. As novas regras de alerta utilizam grupos de ação que podem ser reutilizados através de várias regras de alerta. A ferramenta de migração cria um grupo de ação único para as mesmas ações, independentemente de quantas regras de alerta estão a usar a ação. Os grupos de ação criados pela ferramenta de migração utilizam o formato de nomeação 'Migrated_AG*».

> [!NOTE]
> Os alertas clássicos enviaram e-mails localizados com base no local do administrador clássico quando usado para notificar funções de administrador clássico. Os novos e-mails de alerta são enviados através de Grupos de Ação e são apenas em inglês.

## <a name="rollout-phases"></a>Fases de implementação

A ferramenta de migração está a ser lançada por fases para os clientes que usam regras clássicas de alerta. Os proprietários de subscrição receberão um e-mail quando a subscrição estiver pronta para ser migrada utilizando a ferramenta.

> [!NOTE]
> Como a ferramenta está a ser lançada por fases, pode ver que algumas das suas subscrições ainda não estão prontas para serem migradas durante as fases iniciais.

A maioria das subscrições está atualmente marcada como pronta para a migração. Apenas as subscrições que têm alertas clássicos sobre os seguintes tipos de recursos ainda não estão prontas para a migração.

- Microsoft.classicCompute/domainNames/slots/roles
- Microsoft.insights/componentes

## <a name="who-can-trigger-the-migration"></a>Quem pode desencadear a migração?

Qualquer utilizador que tenha o papel incorporado de Monitoring Contributor ao nível da subscrição pode desencadear a migração. Os utilizadores que tenham um papel personalizado com as seguintes permissões também podem desencadear a migração:

- */ler
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*
- Microsoft.AlertsManagement/smartDetectorAlertRules/*

> [!NOTE]
> Além de ter permissões acima, a sua subscrição deve ainda ser registada no fornecedor de recursos Microsoft.AlertsManagement. Isto é necessário para migrar com sucesso alertas de anomalia de falha em Insights de Aplicação. 

## <a name="common-problems-and-remedies"></a>Problemas e remédios comuns

Depois de [desencadear a migração,](alerts-using-migration-tool.md)receberá e-mail nos endereços que forneceu para o notificar de que a migração está completa ou se é necessária alguma ação da sua parte. Esta secção descreve alguns problemas comuns e como lidar com eles.

### <a name="validation-failed"></a>Validação falhou

Devido a algumas alterações recentes às regras clássicas de alerta na sua subscrição, a subscrição não pode ser migrada. Este problema é temporário. Pode reiniciar a migração depois de o estado de migração voltar **pronto para a migração** em poucos dias.

### <a name="scope-lock-preventing-us-from-migrating-your-rules"></a>Bloqueio de âmbito que nos impede de migrar as suas regras

Como parte da migração, serão criados novos alertas métricos e novos grupos de ação, e então as regras clássicas de alerta serão eliminadas. No entanto, um bloqueio de âmbito pode impedir-nos de criar ou eliminar recursos. Dependendo do bloqueio de âmbito, algumas ou todas as regras não podiam ser migradas. Pode resolver este problema removendo o bloqueio de âmbito para a subscrição, grupo de recursos ou recursos, que está listado na [ferramenta de migração](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel), e desencadeando novamente a migração. O bloqueio de âmbito não pode ser desativado e deve ser removido durante o processo de migração. [Saiba mais sobre a gestão de fechaduras de âmbito.](../../azure-resource-manager/management/lock-resources.md#portal)

### <a name="policy-with-deny-effect-preventing-us-from-migrating-your-rules"></a>Política com efeito "Negar" impedindo-nos de migrar as suas regras

Como parte da migração, serão criados novos alertas métricos e novos grupos de ação, e então as regras clássicas de alerta serão eliminadas. No entanto, uma atribuição [da Política Azure](../../governance/policy/index.yml) pode impedir-nos de criar recursos. Dependendo da atribuição da apólice, algumas ou todas as regras não podiam ser migradas. As atribuições políticas que bloqueiam o processo estão listadas na [ferramenta de migração.](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel) Resolva este problema por qualquer um dos dois:

- Excluindo as subscrições, grupos de recursos ou recursos individuais durante a duração do processo de migração da atribuição de políticas. [Saiba mais sobre a gestão de âmbitos de exclusão de políticas.](../../governance/policy/tutorials/create-and-manage.md#remove-a-non-compliant-or-denied-resource-from-the-scope-with-an-exclusion)
- Desative o "Modo de Execução" para **desativar** a atribuição de apólices. [Saiba mais sobre a propriedade de aplicação de direitos de propriedade de aplicação de políticasMode](../../governance/policy/concepts/assignment-structure.md#enforcement-mode).
- Desconfiem de uma isenção de Política Azure (pré-visualização) sobre as subscrições, grupos de recursos ou recursos individuais para a atribuição de políticas. [Saiba mais sobre a estrutura de isenção da Política Azure.](../../governance/policy/concepts/exemption-structure.md)
- Remover ou alterar o efeito para 'deficientes', 'auditoria', 'append', ou 'modificar' (o que, por exemplo, pode resolver problemas relacionados com as etiquetas em falta). [Saiba mais sobre a gestão dos efeitos políticos.](../../governance/policy/concepts/definition-structure.md#policy-rule)

## <a name="next-steps"></a>Passos seguintes

- [Como utilizar a ferramenta de migração](alerts-using-migration-tool.md)
- [Preparar a migração](alerts-prepare-migration.md)
