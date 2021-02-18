---
title: Loja de Consultas - Base de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve a funcionalidade De Loja de Consultas na Base de Dados Azure para PostgreSQL - Servidor Único.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/01/2020
ms.openlocfilehash: 1779df1c5f9baf2aa46ff809ecae9ec5e3cd7adb
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100581556"
---
# <a name="monitor-performance-with-the-query-store"></a>Monitorize o desempenho com a Loja de Consultas

**Aplica-se a:** Base de Dados Azure para PostgreSQL - Versões single server 9.6 e acima

A funcionalidade de Loja de Consultas na Base de Dados Azure para PostgreSQL fornece uma forma de acompanhar o desempenho da consulta ao longo do tempo. A Consulta Store simplifica a resolução de problemas de desempenho, ajudando-o a encontrar rapidamente as consultas mais longas e intensivas de recursos. A Loja de Consultas captura automaticamente um histórico de consultas e estatísticas de tempo de execução, e retém-nas para a sua revisão. Separa os dados por janelas de tempo para que possa ver padrões de utilização da base de dados. Os dados para todos os utilizadores, bases de dados e consultas são armazenados numa base de dados denominada **azure_sys** na Base de Dados Azure para a instância PostgreSQL.

> [!IMPORTANT]
> Não modifique a base **de dados azure_sys** ou os seus esquemas. Ao fazê-lo, impedirá que a Loja de Consultas e as funcionalidades de desempenho relacionadas funcionem corretamente.

## <a name="enabling-query-store"></a>Loja de Consultas habilitadora
A Loja de Consultas é uma funcionalidade de opt-in, pelo que não está ativa por padrão num servidor. A loja está ativada ou desativada globalmente para todas as bases de dados de um determinado servidor e não pode ser ligada ou desligada por base de dados.

### <a name="enable-query-store-using-the-azure-portal"></a>Ativar a Loja de Consultas utilizando o portal Azure
1. Inscreva-se no portal Azure e selecione a base de dados Azure para o servidor PostgreSQL.
2. Selecione **parâmetros** do servidor na secção **Definições** do menu.
3. Procure o `pg_qs.query_capture_mode` parâmetro.
4. Desa estação o valor `TOP` e **guarde.**

Para ativar as estatísticas de espera na sua Loja de Consultas: 
1. Procure o `pgms_wait_sampling.query_capture_mode` parâmetro.
1. Desa estação o valor `ALL` e **guarde.**


Em alternativa, pode definir estes parâmetros utilizando o Azure CLI.
```azurecli-interactive
az postgres server configuration set --name pg_qs.query_capture_mode --resource-group myresourcegroup --server mydemoserver --value TOP
az postgres server configuration set --name pgms_wait_sampling.query_capture_mode --resource-group myresourcegroup --server mydemoserver --value ALL
```

Aguarde 20 minutos para o primeiro lote de dados se manter na base de dados azure_sys.

## <a name="information-in-query-store"></a>Informação na Loja de Consultas
A Loja de Consultas tem duas lojas:
- Uma loja de estatísticas de tempo de execução para persistir na informação estatística de execução de consultas.
- Uma loja de estatísticas de espera para informações de estatísticas de espera persistentes.

Os cenários comuns para a utilização da Loja de Consultas incluem:
- Determinar o número de vezes que uma consulta foi executada numa janela de tempo
- Comparando o tempo médio de execução de uma consulta através das janelas do tempo para ver grandes deltas
- Identificando as consultas de execução mais longas nas últimas horas X
- Identificando as principais consultas N que estão à espera de recursos
- Compreender a natureza da espera para uma consulta particular

Para minimizar o uso do espaço, as estatísticas de execução em tempo de execução na loja de estatísticas de tempo de execução são agregadas sobre uma janela de tempo fixa e configurável. A informação nestas lojas é visível consultando as vistas da loja de consultas.

## <a name="access-query-store-information"></a>Acesso Informações da Loja de Consultas

Os dados da Loja de Consulta são armazenados na base de dados azure_sys no seu servidor Postgres. 

A seguinte consulta devolve informações sobre consultas na Loja de Consultas:
```sql
SELECT * FROM query_store.qs_view; 
``` 

Ou esta consulta para estatísticas de espera:
```sql
SELECT * FROM query_store.pgms_wait_sampling_view;
```

## <a name="finding-wait-queries"></a>Encontrar consultas de espera
Os tipos de eventos de espera combinam diferentes eventos de espera em baldes por semelhança. A Loja de Consultas fornece o tipo de evento de espera, o nome específico do evento de espera e a consulta em questão. Ser capaz de correlacionar esta informação de espera com as estatísticas de tempo de consulta significa que você pode obter uma compreensão mais profunda do que contribui para a consulta das características de desempenho.

Aqui estão alguns exemplos de como pode obter mais informações sobre a sua carga de trabalho utilizando as estatísticas de espera na Loja de Consultas:

| **Observação** | **Ação** |
|---|---|
|High Lock espera | Consulte os textos de consulta para as consultas afetadas e identifique as entidades-alvo. Procure na Loja de Consultas outras consultas que modifiquem a mesma entidade, que é executada com frequência e/ou tem alta duração. Depois de identificar estas consultas, considere alterar a lógica da aplicação para melhorar a conúnquidade, ou usar um nível de isolamento menos restritivo.|
| High Buffer IO espera | Encontre as consultas com um elevado número de leituras físicas na Loja de Consultas. Se combinarem as consultas com as altas esperas de IO, considere introduzir um índice na entidade subjacente, a fim de fazer procuras em vez de digitalizações. Isto minimizaria a despesa com o IO das consultas. Consulte as **Recomendações de Desempenho** do seu servidor no portal para ver se existem recomendações de índice para este servidor que otimizariam as consultas.|
| Alta Memória espera | Encontre as principais consultas de consumo de memória na Loja de Consultas. Estas consultas estão provavelmente a atrasar o progresso das consultas afetadas. Consulte as **Recomendações de Desempenho** do seu servidor no portal para ver se existem recomendações de índice que otimizem estas consultas.|

## <a name="configuration-options"></a>Opções de configuração
Quando a Loja de Consulta está ativada, guarda dados em janelas de agregação de 15 minutos, até 500 consultas distintas por janela. 

As seguintes opções estão disponíveis para configurar parâmetros da Loja de Consultas.

| **Parâmetro** | **Descrição** | **Predefinição** | **Intervalo**|
|---|---|---|---|
| pg_qs.consulta_capture_mode | Conjuntos que declarações são rastreadas. | nenhum | ninguém, em cima, todos os |
| pg_qs.max_consulta_text_length | Define o comprimento máximo de consulta que pode ser guardado. Consultas mais longas serão truncadas. | 6000 | 100 - 10K |
| pg_qs.retenção_period_in_days | Define o período de retenção. | 7 | 1 - 30 |
| pg_qs.track_utility | Define se os comandos de utilidade são rastreados | em | em, fora |

As seguintes opções aplicam-se especificamente às estatísticas de espera.

| **Parâmetro** | **Descrição** | **Predefinição** | **Intervalo**|
|---|---|---|---|
| pgms_wait_sampling.consulta_capture_mode | Conjuntos que declarações são rastreadas para estatísticas de espera. | nenhum | nenhum, todos os|
| Pgms_wait_sampling.history_period | Desa ajuste a frequência, em milissegundos, em que os eventos de espera são amostrados. | 100 | 1-600000 |

> [!NOTE] 
> **pg_qs.query_capture_mode** substitui **pgms_wait_sampling.query_capture_mode**. Se pg_qs.query_capture_mode for NONE, a definição pgms_wait_sampling.query_capture_mode não tem qualquer efeito.


Utilize o [portal Azure](howto-configure-server-parameters-using-portal.md) ou [O CLI Azure](howto-configure-server-parameters-using-cli.md) para obter ou definir um valor diferente para um parâmetro.

## <a name="views-and-functions"></a>Vistas e funções
Ver e gerir a Loja de Consultas utilizando as seguintes vistas e funções. Qualquer pessoa no papel público postgreSQL pode usar estas opiniões para ver os dados na Query Store. Estas opiniões só estão disponíveis na base **de dados azure_sys.**

As consultas são normalizadas olhando para a sua estrutura depois de remover literal e constantes. Se duas consultas forem idênticas, exceto para valores literais, terão o mesmo haxixe.

### <a name="query_storeqs_view"></a>query_store.qs_view
Esta vista devolve todos os dados na Loja de Consultas. Há uma linha para cada ID de base de dados distinto, ID do utilizador e ID de consulta. 

|**Nome**   |**Tipo** | **Referências**  | **Descrição**|
|---|---|---|---|
|runtime_stats_entry_id |bigint | | ID da mesa de runtime_stats_entries|
|user_id    |oid    |pg_authid.oid  |OID do utilizador que executou a declaração|
|db_id  |oid    |pg_database.oid    |OID da base de dados em que a declaração foi executada|
|query_id   |bigint  || Código de haxixe interno, calculado a partir da árvore de parse da declaração|
|query_sql_text |Varchar(10000)  || Texto de uma declaração representativa. Diferentes consultas com a mesma estrutura estão agrupadas; este texto é o texto para a primeira das consultas no cluster.|
|plan_id    |bigint |   |ID do plano correspondente a esta consulta, ainda não disponível|
|start_time |carimbo de data/hora  ||  As consultas são agregadas por baldes de tempo - o tempo de um balde é de 15 minutos por defeito. Esta é a hora de início correspondente ao balde de tempo para esta entrada.|
|end_time   |carimbo de data/hora  ||  Tempo final correspondente ao balde de tempo para esta entrada.|
|chama  |bigint  || Número de vezes que a consulta executada|
|total_time |dupla precisão   ||  Tempo total de execução de consultas, em milissegundos|
|min_time   |dupla precisão   ||  Tempo mínimo de execução de consultas, em milissegundos|
|max_time   |dupla precisão   ||  Tempo máximo de execução de consultas, em milissegundos|
|mean_time  |dupla precisão   ||  Tempo médio de execução de consulta, em milissegundos|
|stddev_time|   dupla precisão    ||  Desvio padrão do tempo de execução de consultas, em milissegundos |
|linhas   |bigint ||  Número total de linhas recuperadas ou afetadas pela declaração|
|shared_blks_hit|   bigint  ||  Número total de cache de bloco partilhado pela declaração|
|shared_blks_read|  bigint  ||  Número total de blocos partilhados lidos pela declaração|
|shared_blks_dirtied|   bigint   || Número total de blocos partilhados sujos pela declaração |
|shared_blks_written|   bigint  ||  Número total de blocos partilhados escritos pela declaração|
|local_blks_hit|    bigint ||   Número total de cache de bloco local atinge pela declaração|
|local_blks_read|   bigint   || Número total de blocos locais lidos pela declaração|
|local_blks_dirtied|    bigint  ||  Número total de blocos locais sujos pela declaração|
|local_blks_written|    bigint  ||  Número total de blocos locais escritos pela declaração|
|temp_blks_read |bigint  || Número total de blocos temporários lidos pela declaração|
|temp_blks_written| bigint   || Número total de blocos temporários escritos pela declaração|
|blk_read_time  |dupla precisão    || Tempo total do tempo gasto blocos de leitura, em milissegundos (se track_io_timing estiver ativado, caso contrário zero)|
|blk_write_time |dupla precisão    || Tempo total a declaração gasta blocos de escrita, em milissegundos (se track_io_timing estiver ativado, caso contrário zero)|
    
### <a name="query_storequery_texts_view"></a>query_store.consulta_texts_view
Esta vista devolve dados de texto de consulta na Loja de Consultas. Há uma linha para cada query_text distinto.

| **Nome** | **Tipo** | **Descrição** |
|--|--|--|
| query_text_id | bigint | ID para a mesa query_texts |
| query_sql_text | Varchar(10000) | Texto de uma declaração representativa. Diferentes consultas com a mesma estrutura estão agrupadas; este texto é o texto para a primeira das consultas no cluster. |

### <a name="query_storepgms_wait_sampling_view"></a>query_store.pgms_wait_sampling_view
Esta vista devolve os dados dos eventos de espera na Loja de Consultas. Há uma linha para cada ID de base de dados distinto, ID do utilizador, identificação de consulta e evento.

| **Nome** | **Tipo** | **Referências** | **Descrição** |
|--|--|--|--|
| user_id | oid | pg_authid.oid | OID do utilizador que executou a declaração |
| db_id | oid | pg_database.oid | OID da base de dados em que a declaração foi executada |
| query_id | bigint |  | Código de haxixe interno, calculado a partir da árvore de parse da declaração |
| event_type | texto |  | O tipo de evento para o qual o backend está à espera |
| evento | texto |  | O nome do evento de espera se o backend está à espera |
| chama | Número inteiro |  | Número do mesmo evento capturado |

### <a name="functions"></a>Funções

Query_store.qs_reset() devolução do vazio

`qs_reset` descarta todas as estatísticas recolhidas até agora pela Query Store. Esta função só pode ser executada pela função de administração do servidor.

Query_store.staging_data_reset() devolução do vazio

`staging_data_reset` descarta todas as estatísticas recolhidas na memória pela Query Store (isto é, os dados na memória que ainda não foram lavados na base de dados). Esta função só pode ser executada pela função de administração do servidor.


## <a name="azure-monitor"></a>Azure Monitor
A base de dados Azure para PostgreSQL está integrada com [as definições de diagnóstico do Monitor Azure](../azure-monitor/essentials/diagnostic-settings.md). As definições de diagnóstico permitem-lhe enviar os seus registos postgres no formato JSON para [registos do Monitor Azure](../azure-monitor/logs/log-query-overview.md) para análise e alerta, Centros de Eventos para streaming e Armazenamento Azure para arquivamento.

>[!IMPORTANT]
> Esta funcionalidade de diagnóstico só está disponível nos níveis de preços otimizados para fins gerais e memória.

### <a name="configure-diagnostic-settings"></a>Configurar configurações de diagnóstico
Pode ativar as definições de diagnóstico para o seu servidor Postgres utilizando o portal Azure, CLI, REST API e PowerShell. As categorias de registo a configurar são **QueryStoreRuntimeStatistics** e **QueryStoreWaitStatistics**. 

Para ativar registos de recursos utilizando o portal Azure:

1. No portal, aceda a Definições de Diagnóstico no menu de navegação do seu servidor Postgres.
2. Selecione Adicionar Definição de Diagnóstico.
3. Diga o nome desta definição.
4. Selecione o seu ponto final preferido (conta de armazenamento, centro de eventos, análise de registo).
5. Selecione os tipos de registo **QueryStoreRuntimeStatistics** e **QueryStoreWaitStatistics**.
6. Guarde a sua configuração.

Para ativar esta definição utilizando a PowerShell, CLI ou REST API, visite o [artigo de definições de diagnóstico](../azure-monitor/essentials/diagnostic-settings.md).

### <a name="json-log-format"></a>Formato de log JSON
As tabelas seguintes descrevem os campos para os dois tipos de troncos. Dependendo do ponto final de saída que escolher, os campos incluídos e a ordem em que aparecem podem variar.

#### <a name="querystoreruntimestatistics"></a>QueryStoreRuntimeStatistics
|**Campo** | **Descrição** |
|---|---|
| TempoGerado [UTC] | Carimbo de tempo quando o registo foi gravado na UTC |
| ResourceId | URI de recurso Azure do servidor Postgres |
| Categoria | `QueryStoreRuntimeStatistics` |
| OperationName | `QueryStoreRuntimeStatisticsEvent` |
| LogicalServerName_s | Nome do servidor postgres | 
| runtime_stats_entry_id_s | ID da mesa de runtime_stats_entries |
| user_id_s | OID do utilizador que executou a declaração |
| db_id_s | OID da base de dados em que a declaração foi executada |
| query_id_s | Código de haxixe interno, calculado a partir da árvore de parse da declaração |
| end_time_s | Tempo final correspondente ao balde de tempo para esta entrada |
| calls_s | Número de vezes que a consulta executada |
| total_time_s | Tempo total de execução de consultas, em milissegundos |
| min_time_s | Tempo mínimo de execução de consultas, em milissegundos |
| max_time_s | Tempo máximo de execução de consultas, em milissegundos |
| mean_time_s | Tempo médio de execução de consulta, em milissegundos |
| ResourceGroup | O grupo de recursos | 
| SubscriptionId | O seu ID de subscrição |
| ResourceProvider | `Microsoft.DBForPostgreSQL` | 
| Recurso | Nome do servidor postgres |
| ResourceType | `Servers` | 


#### <a name="querystorewaitstatistics"></a>QueryStoreWaitStatistics
|**Campo** | **Descrição** |
|---|---|
| TempoGerado [UTC] | Carimbo de tempo quando o registo foi gravado na UTC |
| ResourceId | URI de recurso Azure do servidor Postgres |
| Categoria | `QueryStoreWaitStatistics` |
| OperationName | `QueryStoreWaitEvent` |
| user_id_s | OID do utilizador que executou a declaração |
| db_id_s | OID da base de dados em que a declaração foi executada |
| query_id_s | Código de haxixe interno da consulta |
| calls_s | Número do mesmo evento capturado |
| event_type_s | O tipo de evento para o qual o backend está à espera |
| event_s | O nome do evento de espera se o backend está à espera |
| start_time_t | Hora de início do evento |
| end_time_s | Tempo de fim do evento | 
| LogicalServerName_s | Nome do servidor postgres | 
| ResourceGroup | O grupo de recursos | 
| SubscriptionId | O seu ID de subscrição |
| ResourceProvider | `Microsoft.DBForPostgreSQL` | 
| Recurso | Nome do servidor postgres |
| ResourceType | `Servers` | 

## <a name="limitations-and-known-issues"></a>Problemas e limitações conhecidos
- Se um servidor PostgreSQL tiver o parâmetro default_transaction_read_only ligado, a Loja de Consultas não pode capturar dados.
- A funcionalidade de Consulta Store pode ser interrompida se encontrar longas consultas unicode (>= 6000 bytes).
- [Leia réplicas](concepts-read-replicas.md) replicam dados da Loja de Consultas a partir do servidor primário. Isto significa que a Loja de Consultas de uma réplica de uma réplica não fornece estatísticas sobre consultas executadas na réplica de leitura.


## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [cenários onde a Loja de Consultas pode ser especialmente útil.](concepts-query-store-scenarios.md)
- Saiba mais sobre [as melhores práticas para a utilização da Loja de Consultas.](concepts-query-store-best-practices.md)
