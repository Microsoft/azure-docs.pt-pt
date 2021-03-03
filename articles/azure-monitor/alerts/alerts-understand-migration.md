---
title: Compreender a migração para alertas do Monitor Azure
description: Entenda como funciona a migração dos alertas e resolve problemas.
ms.topic: conceptual
ms.date: 02/14/2021
ms.author: yalavi
author: yalavi
ms.subservice: alerts
ms.openlocfilehash: fdac8015cf87ffa0a25a8558668329a8cd82327f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101737193"
---
# <a name="understand-migration-options-to-newer-alerts"></a>Compreender opções de migração para alertas mais recentes

Os alertas clássicos são [retirados](./monitoring-classic-retirement.md) para utilizadores de nuvem pública, embora ainda em uso limitado até **31 de maio de 2021**. Os alertas clássicos para a nuvem do Governo Azure e para o Azure China 21Vianet vão reformar-se a **29 de fevereiro de 2024**.

Este artigo explica como funciona a ferramenta de migração manual e de migração voluntária, que será usada para migrar as restantes regras de alerta. Também descreve soluções para alguns problemas comuns.

> [!IMPORTANT]
> Os alertas de registo de atividade (incluindo alertas de saúde do Serviço) e os alertas de log não são afetados pela migração. A migração aplica-se apenas às regras clássicas de alerta descritas [aqui.](./monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform)

> [!NOTE]
> Se as suas regras clássicas de alerta forem inválidas, ou seja, se estiverem em métricas ou recursos [precotados](#classic-alert-rules-on-deprecated-metrics) que tenham sido eliminados, não serão migrados e não estarão disponíveis após o serviço ser retirado.

## <a name="manually-migrating-classic-alerts-to-newer-alerts"></a>Migrar manualmente alertas clássicos para alertas mais recentes

Os clientes interessados em migrar manualmente os seus restantes alertas já podem fazê-lo utilizando as seguintes secções. Também inclui métricas que são aposentadas e, portanto, não podem ser migradas diretamente.

### <a name="guest-metrics-on-virtual-machines"></a>Métricas de hóspedes em máquinas virtuais

Antes de criar novos alertas métricos sobre as métricas dos hóspedes, as métricas dos hóspedes devem ser enviadas para a loja de registos Azure Monitor. Siga estas instruções para criar alertas:

- [Habilitando a recolha de métricas de hóspedes para registar análises](../agents/agent-data-sources.md)
- [Criação de alertas de registo no Azure Monitor](./alerts-log.md)

Há mais opções para recolher métricas de hóspedes e alertá-las, [aprender mais.](../agents/agents-overview.md)

### <a name="storage-and-classic-storage-account-metrics"></a>Métricas de conta de armazenamento e armazenamento clássico

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

As regras clássicas de alerta em AnonymousThrottlingError, SASThrottlingError e ThrottlingError devem ser divididas em dois novos alertas porque não há nenhuma métrica combinada que forneça a mesma funcionalidade. Os limiares terão de ser adaptados adequadamente.

### <a name="cosmos-db-metrics"></a>Métricas do Cosmos DB

Todos os alertas clássicos sobre as métricas do Cosmos DB podem ser migrados, exceto alertas nestas métricas:

- Pedidos médios por segundo
- Nível de Consistência
- Http 2xx
- Http 3xx
- Max RUPM Consumido por Minuto
- Max RUs por segundo
- Mongo Outro Pedido
- Outra Taxa de Pedido de Mongo
- Latência de leitura observada
- Latência de escrita observada
- Disponibilidade de serviço
- Capacidade de Armazenamento

Pedidos médios por segundo, nível de consistência, Max RUPM Consumido por minuto, Max RUs por segundo, Latência de Leitura Observada, Latência de Escrita Observada e Capacidade de Armazenamento não estão atualmente disponíveis no [novo sistema](../essentials/metrics-supported.md#microsoftdocumentdbdatabaseaccounts).

Alertas sobre métricas de pedido como Http 2xx, Http 3xx e Disponibilidade de Serviço não são migrados porque a forma como os pedidos são contados é diferente entre métricas clássicas e novas métricas. Os alertas nestas métricas terão de ser recriados manualmente com limiares ajustados.

### <a name="classic-alert-rules-on-deprecated-metrics"></a>Regras clássicas de alerta sobre métricas precadas

Seguem-se as regras clássicas de alerta sobre métricas que foram previamente suportadas, mas que acabaram por ser depreciadas. Uma pequena percentagem de clientes pode ter regras de alerta clássicas inválidas em tais métricas. Como estas regras de alerta são inválidas, não serão migradas.

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

- **Frequência**: Define com que frequência uma regra de alerta clássica ou nova verifica a circunstância. As `frequency` regras de alerta clássicas não eram configuráveis pelo utilizador e eram sempre de 5 minutos para todos os tipos de recursos. A frequência das regras equivalentes também está definida para 5 min.
- **Tipo de agregação**: Define como a métrica é agregada sobre a janela de interesse. O `aggregationType` mesmo é o mesmo entre alertas clássicos e novos alertas para a maioria das métricas. Em alguns casos, uma vez que a métrica é diferente entre alertas clássicos e novos alertas, é usado o equivalente `aggregationType` ou o definido para a `primary Aggregation Type` métrica.
- **Unidades**: Propriedade da métrica em que o alerta é criado. Algumas métricas equivalentes têm unidades diferentes. O limiar é ajustado adequadamente conforme necessário. Por exemplo, se a métrica original tiver segundos como unidades mas a nova métrica equivalente tiver milissegundos como unidades, o limiar original é multiplicado por 1000 para garantir o mesmo comportamento.
- **Tamanho da janela**: Define a janela sobre a qual os dados métricos são agregados para comparar com o limiar. Para valores padrão `windowSize` como 5 minutos, 15 minutos, 30 minutos, 1 hora, 3 horas, 6 horas, 12 horas, 1 dia, não há alteração feita para a regra equivalente de novo alerta. Para outros valores, o mais próximo `windowSize` é usado. Para a maioria dos clientes, não há efeito com esta mudança. Para uma pequena percentagem de clientes, pode haver a necessidade de ajustar o limiar para obter exatamente o mesmo comportamento.

Nas secções seguintes, detalhamos as métricas que têm uma métrica diferente, equivalente no novo sistema. Qualquer métrica que permaneça a mesma para regras clássicas e novas de alerta não está listada. Pode encontrar uma lista de métricas suportadas no novo sistema [aqui.](../essentials/metrics-supported.md)

### <a name="microsoftstoragestorageaccounts-and-microsoftclassicstoragestorageaccounts"></a>Microsoft.Storage/storageAcounts e Microsoft.ClassicStorage/storageAcounts

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

### <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/base de dadosAcontas

Para cosmos DB, as métricas equivalentes são como mostrado abaixo:

| Métrica em alertas clássicos | Métrica equivalente em novos alertas | Comentários|
|--------------------------|---------------------------------|---------|
| DisponíveisToragem | DisponíveisToragem||
| Tamanho dos Dados | DataUsage| |
| Contagem de documentos | Contagem de documentos||
| Tamanho do índice | IndexUsage||
| Serviço Indisponível | ServiçoSAdisponibilidade||
| TotalRequestUnits | TotalRequestUnits||
| Pedidos de Aceleração | TotalRequests com dimensão "StatusCode" = "429"| O tipo de agregação 'média' é corrigido para 'Count'|
| Erros Internos de Servidor | TotalRequests com dimensão "StatusCode" = "500"}| O tipo de agregação 'média' é corrigido para 'Count'|
| Http 401 | TotalRequests com dimensão "StatusCode" = "401"| O tipo de agregação 'média' é corrigido para 'Count'|
| Http 400 | TotalRequests com dimensão "StatusCode" = "400"| O tipo de agregação 'média' é corrigido para 'Count'|
| Total de Pedidos | TotalRequests| O tipo de agregação 'Max' é corrigido para 'Count'|
| Taxa de pedido de contagem de Mongo| MongoRequestCharge com dimensão "CommandName" = "contagem"||
| Taxa de pedido de contagem de Mongo | MongoRequestsCount com dimensão "CommandName" = "contagem"||
| Mongo Elimina Taxa de Pedido | MongoRequestCharge com dimensão "CommandName" = "excluir"||
| Tarifa de pedido de eliminação de Mongo | MongoRequestsCount com dimensão "CommandName" = "excluir"||
| Taxa de pedido de inserção de Mongo | MongoRequestCharge com dimensão "CommandName" = "inserir"||
| Taxa de pedido de inserção de Mongo | MongoRequestsCount com dimensão "CommandName" = "inserir"||
| Taxa de pedido de consulta de Mongo | MongoRequestCharge com dimensão "CommandName" = "Find"||
| Taxa de pedido de consulta de Mongo | MongoRequestsCount com dimensão "CommandName" = "find"||
| Taxa de pedido de atualização de Mongo | MongoRequestCharge com dimensão "CommandName" = "update"||
| Inserção de Mongo Pedidos Falhados | MongoRequestCount com dimensões "CommandName" = "inserir" e "Status" = "falhado"| O tipo de agregação 'média' é corrigido para 'Count'|
| Pedidos falhados da Mongo Query | MongoRequestCount com dimensões "CommandName" = "consulta" e "Estado" = "falhado"| O tipo de agregação 'média' é corrigido para 'Count'|
| Pedidos falhados do Conde de Mongo | MongoRequestCount com dimensões "CommandName" = "count" e "Status" = "failed"| O tipo de agregação 'média' é corrigido para 'Count'|
| Pedidos falhados da Atualização de Mongo | MongoRequestCount com dimensões "CommandName" = "update" e "Status" = "failed"| O tipo de agregação 'média' é corrigido para 'Count'|
| Mongo Outros Pedidos Falhados | MongoRequestCount com dimensões "CommandName" = "outro" e "Status" = "falhado"| O tipo de agregação 'média' é corrigido para 'Count'|
| Mongo elimina pedidos falhados | MongoRequestCount com dimensões "CommandName" = "delete" e "Status" = "falhado"| O tipo de agregação 'média' é corrigido para 'Count'|

### <a name="how-equivalent-action-groups-are-created"></a>Como grupos de ação equivalentes são criados

As regras clássicas de alerta tinham e-mail, webhook, app lógica e ações de runbook ligadas à própria regra de alerta. As novas regras de alerta utilizam grupos de ação que podem ser reutilizados através de várias regras de alerta. A ferramenta de migração cria um grupo de ação único para as mesmas ações, não importa quantas regras de alerta estejam a usar a ação. Os grupos de ação criados pela ferramenta de migração utilizam o formato de nomeação 'Migrated_AG*».

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

Como parte da migração, serão criados novos alertas métricos e novos grupos de ação, e então as regras clássicas de alerta serão eliminadas. No entanto, um bloqueio de âmbito pode impedir-nos de criar ou eliminar recursos. Dependendo da fechadura de âmbito, algumas ou todas as regras não podiam ser migradas. Pode resolver este problema removendo o bloqueio de âmbito para a subscrição, grupo de recursos ou recursos, que está listado na [ferramenta de migração](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel), e desencadeando novamente a migração. O bloqueio de âmbito não pode ser desativado e deve ser removido durante o processo de migração. [Saiba mais sobre a gestão de fechaduras de âmbito.](../../azure-resource-manager/management/lock-resources.md#portal)

### <a name="policy-with-deny-effect-preventing-us-from-migrating-your-rules"></a>Política com efeito "Negar" impedindo-nos de migrar as suas regras

Como parte da migração, serão criados novos alertas métricos e novos grupos de ação, e então as regras clássicas de alerta serão eliminadas. No entanto, uma atribuição [da Política Azure](../../governance/policy/index.yml) pode impedir-nos de criar recursos. Dependendo da atribuição da apólice, algumas ou todas as regras não podiam ser migradas. As atribuições políticas que bloqueiam o processo estão listadas na [ferramenta de migração.](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel) Resolva este problema por qualquer um dos dois:

- Excluindo as subscrições, grupos de recursos ou recursos individuais durante o processo de migração da atribuição de políticas. [Saiba mais sobre a gestão de âmbitos de exclusão de políticas.](../../governance/policy/tutorials/create-and-manage.md#remove-a-non-compliant-or-denied-resource-from-the-scope-with-an-exclusion)
- Desative o "Modo de Execução" para **desativar** a atribuição de apólices. [Saiba mais sobre a propriedade de aplicação de direitos de propriedade de aplicação de políticasMode](../../governance/policy/concepts/assignment-structure.md#enforcement-mode).
- Desconfiem de uma isenção de Política Azure (pré-visualização) sobre as subscrições, grupos de recursos ou recursos individuais para a atribuição de políticas. [Saiba mais sobre a estrutura de isenção da Política Azure.](../../governance/policy/concepts/exemption-structure.md)
- Remover ou alterar o efeito para 'deficientes', 'auditoria', 'append', ou 'modificar' (o que, por exemplo, pode resolver problemas relacionados com as etiquetas em falta). [Saiba mais sobre a gestão dos efeitos políticos.](../../governance/policy/concepts/definition-structure.md#policy-rule)

## <a name="next-steps"></a>Passos seguintes

- [Como utilizar a ferramenta de migração](alerts-using-migration-tool.md)
- [Preparar a migração](alerts-prepare-migration.md)