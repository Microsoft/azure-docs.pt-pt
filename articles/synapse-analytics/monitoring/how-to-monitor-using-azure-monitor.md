---
title: Como monitorizar a Synapse Analytics usando o Azure Monitor
description: Saiba como monitorizar o seu espaço de trabalho Synapse Analytics utilizando métricas, alertas e registos do Monitor Azure
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: ac5b1df5ff246623b73dbc41d18216396e6ed7ef
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102549860"
---
# <a name="use-azure-monitor-with-your-azure-synapse-analytics-workspace"></a>Use o Azure Monitor com o seu espaço de trabalho Azure Synapse Analytics

As aplicações em nuvem são complexas e têm muitas partes móveis. Os monitores fornecem dados para ajudar a garantir que as suas aplicações permanecem em funcionamento em um estado saudável. Os monitores também ajudam a evitar potenciais problemas e a resolver problemas. Pode utilizar dados de monitorização para obter informações profundas sobre as suas aplicações. Este conhecimento ajuda-o a melhorar o desempenho da aplicação e a manutenção. Também ajuda a automatizar ações que de outra forma requerem intervenção manual.

O Azure Monitor fornece métricas de infraestrutura de nível base, alertas e registos para a maioria dos serviços Azure. Os registos de diagnóstico Azure são emitidos por um recurso e fornecem dados ricos e frequentes sobre o funcionamento desse recurso. A Azure Synapse Analytics pode escrever registos de diagnóstico no Azure Monitor.

Para mais informações, consulte [a visão geral do Azure Monitor](../../azure-monitor/overview.md).

## <a name="metrics"></a>Métricas

Com o Monitor, pode ganhar visibilidade no desempenho e saúde das suas cargas de trabalho Azure. O tipo mais importante de dados do Monitor é a métrica, que também é chamada de contador de desempenho. As métricas são emitidas pela maioria dos recursos Azure. O Monitor fornece várias formas de configurar e consumir estas métricas para monitorização e resolução de problemas.

Para aceder a estas métricas, preencha as instruções na [plataforma de dados Azure Monitor](../../azure-monitor/data-platform.md).

### <a name="workspace-level-metrics"></a>Métricas de nível de espaço de trabalho

Aqui estão algumas das métricas emitidas pelos espaços de trabalho:

| **Métrica**                           | **Categoria métrica, nome de exibição**                  | **Unidade** | **Tipos de agregação** | **Descrição**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| IntegraçãoActivityRunsEndidas         | Integração, Atividade corre métrica                     | de palavras    | Soma (padrão), Contagem                | O número total de operações que ocorreram/terminaram dentro de uma janela de 1 minuto. </br></br> Utilize a dimensão resultado desta métrica para filtrar por Estado final bem sucedido, falhado ou cancelado.|
| IntegraçãoPipelineRunsEnded         | Integração, Pipeline corre métrica                     | de palavras    | Soma (padrão), Contagem                | O número total de gasodutos que ocorreram/terminaram dentro de uma janela de 1 minuto. </br></br> Utilize a dimensão resultado desta métrica para filtrar por Estado final bem sucedido, falhado ou cancelado. |
| IntegraçãoTriggerRunsEnded          | Integração, Trigger executa métrica                      | de palavras    | Soma (padrão), Contagem                | O número total de disparos que ocorreram/terminaram dentro de uma janela de 1 minuto. </br></br> Utilize a dimensão resultado desta métrica para filtrar por Estado final bem sucedido, falhado ou cancelado. |
| BuiltinSqlPoolDataProcessedBytes     | Piscina SQL incorporada, Dados processados (bytes) | Byte | Soma (padrão) | Quantidade de dados processados pela piscina SQL sem servidor incorporada. |
| BuiltinSqlPoolLoginAttempts          | Piscina SQL incorporada, tentativas de login | de palavras | Soma (padrão) | Número de tentativas de login para a piscina SQL sem servidor incorporada. |
| BuiltinSqlPoolDataRequestsEnded      | Piscina SQL embutida, Pedidos terminados (bytes) | de palavras | Soma (padrão) | Número de pedidos SQL terminados para a piscina SQL sem servidor incorporada. </br></br> Utilize a dimensão resultado desta métrica para filtrar por estado final. |

### <a name="dedicated-sql-pool-metrics"></a>Métricas dedicadas da piscina SQL

Aqui estão algumas das métricas emitidas por piscinas SQL dedicadas:

| **Métrica**                           | **Nome a apresentar**                  | **Unidade** | **Tipos de agregação** | **Descrição**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| DWULimit                            | Limite dwu                       | de palavras   | Max (padrão), Min, Avg | Tamanho configurado da piscina SQL |
| DWUUSED                             | DWU usado                        | de palavras   | Max (padrão), Min, Avg | Representa uma representação de alto nível de uso em toda a piscina SQL. Medido por limite DE DWU * percentagem de DWU |
| DWUUsedPercent                      | Percentagem utilizada pela DWU             | Percentagem | Max (padrão), Min, Avg | Representa uma representação de alto nível de uso em toda a piscina SQL. Medido pela tomada do máximo entre a percentagem de CPU e a percentagem de IO de dados |
| ConexõesBlockedByFirewall        | Ligações bloqueadas por firewall | de palavras   | Soma (padrão)   | Contagem de ligações bloqueadas pelas regras de firewall. Reveja as políticas de controlo de acesso para a sua piscina SQL e monitorize estas ligações se a contagem for elevada |
| AdaptiveCacheHitPercent             | Percentagem de impacto de cache adaptativo   | Percentagem | Max (padrão), Min, Avg | Mede a forma como as cargas de trabalho estão a utilizar a cache adaptativa. Utilize esta métrica com a métrica percentual de impacto da cache para determinar se deve escalar para capacidade adicional ou refazer cargas de trabalho para hidratar a cache |
| AdaptiveCacheUsedPercent            | Percentagem de cache adaptativa utilizada  | Percentagem | Max (padrão), Min, Avg | Mede a forma como as cargas de trabalho estão a utilizar a cache adaptativa. Utilize esta métrica com a cache utilizada métrica percentual para determinar se deve escalar para capacidade adicional ou refazer cargas de trabalho para hidratar a cache |
| LocalTempDBUsedPercent              | Percentagem de temporários locais utilizada    | Percentagem | Max (padrão), Min, Avg | Utilização temporária local em todos os nós de computação - valores são emitidos a cada cinco minutos |
| MemoryUsedPercent                   | Percentagem de memória usada          | Percentagem | Max (padrão), Min, Avg | Utilização da memória em todos os nós na piscina SQL |
| CPUPercent                          | Percentagem utilizada pelo CPU             | Percentagem | Max (padrão), Min, Avg | Utilização do CPU em todos os nós na piscina SQL |
| Ligações                         | Ligações                     | de palavras   | Soma (padrão)  | Contagem de entradas totais para a piscina SQL |
| ActiveQueries                      | Consultas ativas                 | de palavras   | Soma (padrão)   | As consultas ativas. Utilizando esta métrica não filtrada e não filtrada exibe todas as consultas ativas em execução no sistema |
| QueuedQueries                      | Consultas em fila                  | de palavras   | Soma (padrão)   | Contagem cumulativa de pedidos em fila após o limite máximo de concuência ter sido atingido |
| WLGActiveQueries                   | Consultas ativas do grupo de carga de trabalho   | de palavras   | Soma (padrão)   | As consultas ativas dentro do grupo de carga de trabalho. Utilizando esta métrica não filtrada e não filtrada exibe todas as consultas ativas em execução no sistema |
| WLGActiveQueriesTimeouts           | Tempo limites de consulta do grupo de carga de trabalho   | de palavras   | Soma (padrão)   | Consultas para o grupo de trabalho que tem cronometrado. Os intervalos de consulta reportados por esta métrica são apenas uma vez que a consulta tenha começado a executar (não inclui o tempo de espera devido ao bloqueio ou espera de recursos) |
| WLGQueuedQueries                   | Consultas em fila de grupos de carga   | de palavras   | Soma (padrão)   | Contagem cumulativa de pedidos em fila após o limite máximo de concuência ter sido atingido |
| WLGAllocationBySystemPercent        | Alocação de grupo de carga de trabalho por parte do sistema | Percentagem | Max (padrão), Min, Avg, Sum | A percentagem de alocação de recursos relativos a todo o sistema |
| WLGAllocationByEffectiveCapResourcePercent   | Alocação de grupo de carga de trabalho por percentagem máxima de recursos | Percentagem | Max (padrão), Min, Avg | Apresenta a percentagem de alocação de recursos em relação à percentagem efetiva de recursos de limite por grupo de carga de trabalho. Esta métrica fornece a utilização eficaz do grupo de carga de trabalho |
| WLGEffectiveCapResourcePercent      | Por cento efetivo do recurso cap  | Percentagem | Max (padrão), Min, Avg | A percentagem efetiva de recursos de capital para o grupo de trabalho. Se houver outros grupos de carga de trabalho com min_percentage_resource > 0, o effective_cap_percentage_resource é reduzido proporcionalmente |
| WLGEffectiveMinResourcePercent      | Por cento de recursos min eficazes  | Percentagem | Max (padrão), Min, Avg, Sum | A definição eficaz da percentagem de recursos min permitiu considerar o nível de serviço e as definições do grupo de carga de trabalho. O min_percentage_resource eficaz pode ser ajustado mais alto em níveis de serviço mais baixos |

### <a name="apache-spark-pool-metrics"></a>Métricas da piscina de Faíscas Apache

Aqui estão algumas das métricas emitidas pelas piscinas Apache Spark:

| **Métrica**                           | **Categoria métrica, nome de exibição**                  | **Unidade** | **Tipos de agregação** | **Descrição**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| BigDataPoolApplicaçõesEndidas  | Candidaturas de Apache Spark terminadas  | de palavras | Soma (padrão) | Número de aplicações de piscina Apache Spark encerradas |
| BigDataPoolAllocatedCores     | Número de vCores atribuídos à piscina Apache Spark                 | de palavras | Max (padrão), Min, Avg | VCores atribuídos para uma Piscina de Faíscas Apache |
| BigDataPoolAllocatedMemory    | Quantidade de memória (GB) atribuída à piscina Apache Spark            | de palavras | Max (padrão), Min, Avg | Memória atribuída para o Apache Spark Pool (GB) |
| BigDataPoolApplicaçõesAtivas | Aplicações Apache Spark ativas | de palavras | Max (padrão), Min, Avg | Número de aplicações ativas da piscina Apache Spark |

## <a name="alerts"></a>Alertas

Inscreva-se no portal Azure e selecione   >  **Alertas de Monitorização** para criar alertas.

### <a name="create-alerts"></a>Criar alertas

1. Selecione **+ nova regra de alerta** para criar um novo alerta.

1. Defina a **condição de alerta** para especificar quando o alerta deve disparar.

    > [!NOTE]
    > Certifique-se de que seleciona **All** in the Filter pela lista de down-down do tipo de **recurso.**

1. Defina os detalhes do **alerta** para especificar mais sobre como o alerta deve ser configurado.

1. Defina o **grupo de ação** para determinar quem deve receber os alertas (e como).

## <a name="logs"></a>Registos

### <a name="workspace-level-logs"></a>Troncos de nível de espaço de trabalho

Aqui estão os registos emitidos pela Azure Synapse Analytics espaços de trabalho:

| Log Analytics nome da tabela | Nome da categoria de registo                 | Description |
|-------------------------------|-------------------------------------------------|-------------|
| SynapseGatewayApiReques     | GatewayApiRequests             | Pedidos de Azure Synapse gateway API. |
| Operações De SinapseRbac         | Operações De SinapseRbac          | Operações de controlo de acesso baseado em funções (SRBAC) da Azure Synapse. |

### <a name="dedicated-sql-pool-logs"></a>Troncos de piscina SQL dedicados

Aqui estão os registos emitidos por piscinas SQL dedicadas:

| Log Analytics nome da tabela        | Nome da categoria de registo             | Description |
|----------------------|--------------------------------------|-------------|
| SinapseSqlPoolExecRequests  | ExecRequests | Informações sobre pedidos/consultas SQL numa piscina SQL dedicada a Azure Synapse.
| SynapseSqlPoolDmsWorkers    | DmsWorkers   | Informação sobre trabalhadores que completam passos DMS numa piscina SQL dedicada a Azure Synapse.
| SinapseSqlPoolRequestSteps  | PedidoSteps | Informações sobre passos de pedido que compõem um dado pedido/consulta SQL em uma piscina SQL dedicada Azure Synapse.
| SinapseSqlPoolSqlRequests   | SqlRequests  | Informação sobre distribuição de consultas dos passos de pedidos/consultas SQL numa piscina SQL dedicada a Azure Synapse.
| SynapseSqlPoolWaits         | Espera        | Informações sobre os estados de espera encontrados durante a execução de um pedido/consulta SQL numa piscina SQL dedicada a Azure Synapse, incluindo fechaduras e esperas nas filas de transmissão.

Para obter mais informações sobre estes registos, consulte as seguintes informações:
- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?view=azure-sqldw-latest&preserve-view=true)

### <a name="apache-spark-pool-log"></a>Log de piscina de Faísca Apache Spark

Aqui está o registo emitido pelas piscinas Apache Spark:

| Log Analytics nome da tabela               | Nome da categoria de registo              | Description                 |
|-----------------------------|---------------------------------------|-----------------------------|
| SinapseBigDataPoolApplicações Acabou | BigDataPoolAppsEnded | Informação sobre aplicações terminadas da Apache Spark |

### <a name="diagnostic-settings"></a>Definições de diagnóstico

Utilize definições de diagnóstico para configurar registos de diagnóstico para recursos não computatados. As definições para um controlo de recursos têm as seguintes características:

* Especificam para onde são enviados registos de diagnóstico. Exemplos incluem uma conta de armazenamento Azure, um centro de eventos Azure ou registos monitor.
* Especificam quais as categorias de registos enviadas.
* Especificam quanto tempo cada categoria de registo deve ser mantida numa conta de armazenamento.
* Uma retenção de zero dias significa que os registos são mantidos para sempre. Caso contrário, o valor pode ser de qualquer número de dias de 1 a 2.147.483.647.
* Se as políticas de retenção forem definidas, mas armazenar registos numa conta de armazenamento é desativado, as políticas de retenção não têm efeito. Por exemplo, esta condição pode acontecer quando apenas são selecionadas as opções de Centros de Eventos ou de Registos do Monitor.
* As políticas de retenção são aplicadas por dia. A fronteira entre os dias ocorre à meia-noite Tempo Universal Coordenado (UTC). Ao fim de um dia, os registos de dias que estão para além da política de retenção são eliminados. Por exemplo, se tiver uma política de retenção de um dia, no início de hoje os registos de antes de ontem são apagados.

Com as definições de diagnóstico do Azure Monitor, pode encaminhar registos de diagnóstico para análise para vários alvos diferentes.

* **Conta de armazenamento**: Guarde os seus registos de diagnóstico numa conta de armazenamento para auditoria ou inspeção manual. Pode utilizar as definições de diagnóstico para especificar o tempo de retenção em dias.
* **Centro de Eventos**: Transmita os registos para Azure Event Hubs. Os registos tornam-se entradas para uma solução de análise de serviço de parceiro/personalizado como o Power BI.
* **Log Analytics workspace**: Analise os registos com Log Analytics. A integração do Azure Synapse com o Log Analytics é útil nos seguintes cenários:
  * Você quer escrever consultas complexas sobre um rico conjunto de métricas que são publicadas pela Azure Synapse para Log Analytics. Pode criar alertas personalizados nestas consultas através do Azure Monitor.
  * Quer monitorizar através dos espaços de trabalho. Pode encaminhar dados de vários espaços de trabalho para um único espaço de trabalho log analytics.

Também pode utilizar uma conta de armazenamento ou espaço de nomes do Event Hub que não esteja na subscrição do recurso que emite registos. O utilizador que configurar a definição deve ter acesso adequado ao controlo de acesso baseado em funções (Azure RBAC) a ambas as subscrições.

#### <a name="configure-diagnostic-settings"></a>Configurar configurações de diagnóstico

Crie ou adicione configurações de diagnóstico para o seu espaço de trabalho, piscina SQL dedicada ou piscina Apache Spark.

1. No portal, vá ao Monitor. Selecione   >  **definições de diagnóstico**.

1. Selecione o espaço de trabalho Synapse, piscina SQL dedicada ou piscina Apache Spark para a qual pretende criar uma definição de diagnóstico.

1. Se não existirem definições de diagnóstico no espaço de trabalho selecionado, é solicitado que crie uma definição. **Selecione Ligue os diagnósticos**.

   Se houver definições de diagnóstico existentes no espaço de trabalho, verá uma lista de definições já configuradas no recurso. Selecione **Adicionar definição de diagnóstico**.

1. Dê um nome à sua definição, selecione **Enviar para registar análises** e, em seguida, selecione um espaço de trabalho no **log analytics**.

    > [!NOTE]
    > Como uma tabela de registoS Azure não pode ter mais de 500 colunas, **recomendamos vivamente** que selecione _o modo Específico de Recursos_. Para mais informações, consulte [a referência AzureDiagnostics Logs](/azure-monitor/reference/tables/azurediagnostics#additionalfields-column).

1. Selecione **Guardar**.

Após alguns momentos, a nova definição aparece na sua lista de configurações para o seu espaço de trabalho, piscina SQL dedicada ou piscina Apache Spark. Os registos de diagnóstico são transmitidos para esse espaço de trabalho assim que novos dados de eventos são gerados. Podem decorrer até 15 minutos entre quando um evento é emitido e quando aparece no Log Analytics.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a monitorização do gasoduto, consulte o gasoduto Monitor que funciona no artigo [do Synapse Studio.](how-to-monitor-pipeline-runs.md) 

Para obter mais informações sobre a monitorização das aplicações apache Spark, consulte as aplicações Monitor Apache Spark no artigo [do Synapse Studio.](apache-spark-applications.md)

Para obter mais informações sobre a monitorização dos pedidos do SQL, consulte os pedidos do Monitor SQL no artigo [do Synapse Studio.](how-to-monitor-sql-requests.md)
