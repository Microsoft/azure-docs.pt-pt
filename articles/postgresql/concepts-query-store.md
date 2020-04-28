---
title: Consulta Store - Base de Dados Azure para PostgreSQL - Servidor Único
description: Este artigo descreve a funcionalidade Query Store na Base de Dados Azure para PostgreSQL - Servidor Único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/14/2019
ms.openlocfilehash: ccc503e6718ee8f516920cfbea3ad86e7ed81d84
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74768270"
---
# <a name="monitor-performance-with-the-query-store"></a>Monitor desempenho com a Loja de Consultas

**Aplica-se a:** Base de Dados Azure para PostgreSQL - Versões Single Server 9.6, 10, 11

A funcionalidade Da Loja de Consultas na Base de Dados Azure para PostgreSQL fornece uma forma de acompanhar o desempenho da consulta ao longo do tempo. A Consulta Store simplifica a resolução de problemas de desempenho, ajudando-o rapidamente a encontrar as consultas de corrida mais longas e intensivas de recursos. A Consulta Store captura automaticamente um histórico de consultas e estatísticas de tempo de execução, e mantém-nas para a sua revisão. Separa os dados por janelas de tempo para que possa ver padrões de utilização da base de dados. Os dados para todos os utilizadores, bases de dados e consultas são armazenados numa base de dados chamada **azure_sys** na Base de Dados Azure para a instância PostgreSQL.

> [!IMPORTANT]
> Não forneça **os** azure_sys base de dados nem os seus esquemas. Ao fazê-lo, impedirá que a Query Store e as funcionalidades de desempenho relacionadas funcionem corretamente.

## <a name="enabling-query-store"></a>Loja de Consulta ativa
A Consulta Store é uma funcionalidade de opt-in, pelo que não está ativa por padrão num servidor. A loja está ativada ou desativada globalmente para todas as bases de dados de um determinado servidor e não pode ser ligada ou desligada por base de dados.

### <a name="enable-query-store-using-the-azure-portal"></a>Ativar a Loja de Consultas utilizando o portal Azure
1. Inscreva-se no portal Azure e selecione a sua Base de Dados Azure para o servidor PostgreSQL.
2. Selecione **Parâmetros** do servidor na secção **Definições** do menu.
3. Procura o `pg_qs.query_capture_mode` parâmetro.
4. Desloque `TOP` o valor e **poupe**.

Para ativar estatísticas de espera na sua Loja de Consulta: 
1. Procura o `pgms_wait_sampling.query_capture_mode` parâmetro.
1. Desloque `ALL` o valor e **poupe**.


Em alternativa, pode definir estes parâmetros utilizando o AZURE CLI.
```azurecli-interactive
az postgres server configuration set --name pg_qs.query_capture_mode --resource-group myresourcegroup --server mydemoserver --value TOP
az postgres server configuration set --name pgms_wait_sampling.query_capture_mode --resource-group myresourcegroup --server mydemoserver --value ALL
```

Aguarde 20 minutos para o primeiro lote de dados se manter na base de dados azure_sys.

## <a name="information-in-query-store"></a>Informação na Loja de Consulta
A Consulta Store tem duas lojas:
- Uma loja de estatísticas de tempo de execução para persistir as informações estatísticas de execução de consulta.
- Uma loja de estatísticas de espera para informações estatísticas de espera persistentes.

Os cenários comuns para a utilização da Loja de Consultas incluem:
- Determinar o número de vezes que uma consulta foi executada em uma determinada janela do tempo
- Comparando o tempo médio de execução de uma consulta através das janelas do tempo para ver grandes deltas
- Identificando as consultas de execução mais longas nas últimas x horas
- Identificação de consultas de topo n que estão à espera de recursos
- Compreender a natureza de esperar por uma consulta particular

Para minimizar o uso do espaço, as estatísticas de execução do tempo de execução na loja de estatísticas de tempo de execução são agregadas sobre uma janela de tempo fixa e configurável. A informação nestas lojas é visível consultando as vistas da loja de consulta.

## <a name="access-query-store-information"></a>Acesso informações da Consulta Store

Os dados da Consulta Store são armazenados na base de dados azure_sys no seu servidor Postgres. 

A seguinte consulta devolve informações sobre consultas na Consulta Store:
```sql
SELECT * FROM query_store.qs_view; 
``` 

Ou esta consulta de estatísticas de espera:
```sql
SELECT * FROM query_store.pgms_wait_sampling_view;
```

Também pode emitir dados da Consulta Store para registos de monitorização [do Azure](../azure-monitor/log-query/log-query-overview.md) para análise e alerta, Hubs de eventos para streaming e Armazenamento Azure para arquivamento. As categorias de registo para configurar são **QueryStoreRuntimeStatistics** e **QueryStoreWaitStatistics**. Para saber mais sobre a configuração, visite o artigo de definições de diagnóstico do [Azure Monitor.](../azure-monitor/platform/diagnostic-settings.md)


## <a name="finding-wait-queries"></a>Encontrar consultas de espera
Os tipos de eventos de espera combinam diferentes eventos de espera em baldes por semelhança. A Consulta Store fornece o tipo de evento de espera, nome específico do evento de espera e a consulta em questão. Ser capaz de correlacionar esta informação de espera com as estatísticas de tempo de execução da consulta significa que você pode obter uma compreensão mais profunda do que contribui para a consulta características de desempenho.

Aqui estão alguns exemplos de como pode obter mais informações sobre a sua carga de trabalho usando as estatísticas de espera na Consulta Store:

| **Observação** | **Ação** |
|---|---|
|High Lock espera | Consulte os textos de consulta para as consultas afetadas e identifique as entidades-alvo. Procure na Consulta Store para outras consultas que modifiquem a mesma entidade, que é executada com frequência e/ou tem alta duração. Depois de identificar estas consultas, considere alterar a lógica da aplicação para melhorar a conmoedação, ou usar um nível de isolamento menos restritivo.|
| Alto Tampão IO espera | Encontre as consultas com um elevado número de leituras físicas na Loja de Consultas. Se corresponderem às consultas com altas esperas de IO, considere introduzir um índice sobre a entidade subjacente, a fim de fazer procura em vez de digitalização. Isto minimizaria a sobrecarga das consultas. Consulte as **Recomendações de Desempenho** do seu servidor no portal para ver se existem recomendações de índice para este servidor que otimizariam as consultas.|
| Alta Memória espera | Encontre as principais consultas de consumo de memória na Loja de Consultas. Estas consultas estão provavelmente a atrasar o progresso das consultas afetadas. Consulte as **Recomendações de Desempenho** do seu servidor no portal para ver se existem recomendações de índice que otimizariam estas consultas.|

## <a name="configuration-options"></a>Opções de configuração
Quando a Consulta Store está ativada, guarda dados em janelas de agregação de 15 minutos, até 500 consultas distintas por janela. 

As seguintes opções estão disponíveis para configurar os parâmetros da Consulta Store.

| **Parâmetro** | **Descrição** | **Padrão** | **Alcance**|
|---|---|---|---|
| pg_qs.query_capture_mode | Conjuntos que declarações são rastreadas. | nenhuma | nenhum, no topo, todos os |
| pg_qs.max_query_text_length | Define o comprimento máximo de consulta que pode ser guardado. Consultas mais longas serão truncadas. | 6000 | 100 - 10K |
| pg_qs.retention_period_in_days | Define o período de retenção. | 7 | 1 - 30 |
| pg_qs.track_utility | Define se os comandos de utilidade são rastreados | em | on, off |

As seguintes opções aplicam-se especificamente às estatísticas de espera.

| **Parâmetro** | **Descrição** | **Padrão** | **Alcance**|
|---|---|---|---|
| pgms_wait_sampling.query_capture_mode | Conjuntos que declarações são rastreadas para estatísticas de espera. | nenhuma | nenhum, todos|
| Pgms_wait_sampling.history_period | Desloque a frequência em milissegundos, em que os eventos de espera são amostrados. | 100 | 1-600000 |

> [!NOTE] 
> **pg_qs.query_capture_mode** substitui **pgms_wait_sampling.query_capture_mode.** Se pg_qs.query_capture_mode for NENHUMA, a definição pgms_wait_sampling.query_capture_mode não tem efeito.


Utilize o [portal Azure](howto-configure-server-parameters-using-portal.md) ou [o Azure CLI](howto-configure-server-parameters-using-cli.md) para obter ou definir um valor diferente para um parâmetro.

## <a name="views-and-functions"></a>Vistas e funções
Ver e gerir a Consulta Store utilizando as seguintes vistas e funções. Qualquer pessoa no papel público do PostgreSQL pode usar estas vistas para ver os dados na Consulta Store. Estas opiniões só estão disponíveis na base de dados **azure_sys.**

As consultas são normalizadas olhando para a sua estrutura após a remoção de literals e constantes. Se duas consultas forem idênticas, exceto valores literais, terão o mesmo hash.

### <a name="query_storeqs_view"></a>query_store.qs_view
Esta vista devolve todos os dados da Consulta Store. Há uma linha para cada id de base de dados distinta, identificação do utilizador e identificação de consulta. 

|**Nome**   |**Tipo** | **Referências**  | **Descrição**|
|---|---|---|---|
|runtime_stats_entry_id |bigint | | ID da mesa runtime_stats_entries|
|user_id    |oid    |pg_authid.oid  |OID do utilizador que executou a declaração|
|db_id  |oid    |pg_database.oid    |OID da base de dados em que a declaração foi executada|
|query_id   |bigint  || Código de hash interno, calculado a partir da árvore de parse da declaração|
|query_sql_text |Varchar(10000)  || Texto de uma declaração representativa. Consultas diferentes com a mesma estrutura estão agrupadas; este texto é o texto para a primeira das consultas no cluster.|
|plan_id    |bigint |   |ID do plano correspondente a esta consulta, ainda não disponível|
|start_time |carimbo de data/hora  ||  As consultas são agregadas por baldes de tempo - o tempo de um balde é de 15 minutos por padrão. Esta é a hora de início correspondente ao balde de tempo para esta entrada.|
|end_time   |carimbo de data/hora  ||  Tempo de fim correspondente ao balde de tempo para esta entrada.|
|chamadas  |bigint  || Número de vezes a consulta executada|
|total_time |dupla precisão   ||  Tempo total de execução de consulta, em milissegundos|
|min_time   |dupla precisão   ||  Tempo mínimo de execução de consulta, em milissegundos|
|max_time   |dupla precisão   ||  Tempo máximo de execução de consulta, em milissegundos|
|mean_time  |dupla precisão   ||  Tempo de execução de consulta média, em milissegundos|
|stddev_time|   dupla precisão    ||  Desvio padrão do tempo de execução da consulta, em milissegundos |
|linhas   |bigint ||  Número total de linhas recuperadas ou afetadas pela declaração|
|shared_blks_hit|   bigint  ||  Número total de bloqueios de blocos partilhados pela declaração|
|shared_blks_read|  bigint  ||  Número total de blocos partilhados lidos pelo comunicado|
|shared_blks_dirtied|   bigint   || Número total de blocos partilhados sujos pela declaração |
|shared_blks_written|   bigint  ||  Número total de blocos partilhados escritos pela declaração|
|local_blks_hit|    bigint ||   Número total de bloqueios de blocos locais atingidos pelo comunicado|
|local_blks_read|   bigint   || Número total de blocos locais lidos pelo comunicado|
|local_blks_dirtied|    bigint  ||  Número total de blocos locais sujos pela declaração|
|local_blks_written|    bigint  ||  Número total de blocos locais escritos pela declaração|
|temp_blks_read |bigint  || Número total de blocos temporários lidos pelo comunicado|
|temp_blks_written| bigint   || Número total de blocos temporários escritos pela declaração|
|blk_read_time  |dupla precisão    || O tempo total da declaração gasta a ler blocos, em milissegundos (se track_io_timing estiver ativado, caso contrário zero)|
|blk_write_time |dupla precisão    || O tempo total da declaração gasta a escrever blocos, em milissegundos (se track_io_timing estiver ativado, caso contrário zero)|
    
### <a name="query_storequery_texts_view"></a>query_store.query_texts_view
Esta visualização devolve dados de texto consultados na Consulta Store. Há uma fila para cada query_text distinto.

|**Nome**|  **Tipo**|   **Descrição**|
|---|---|---|
|query_text_id  |bigint     |ID para a mesa query_texts|
|query_sql_text |Varchar(10000)     |Texto de uma declaração representativa. Consultas diferentes com a mesma estrutura estão agrupadas; este texto é o texto para a primeira das consultas no cluster.|

### <a name="query_storepgms_wait_sampling_view"></a>query_store.pgms_wait_sampling_view
Esta vista devolve dados de eventos de espera na Loja de Consultas. Há uma linha para cada id de base de dados distinta, ID do utilizador, id consulta e evento.

|**Nome**|  **Tipo**|   **Referências**| **Descrição**|
|---|---|---|---|
|user_id    |oid    |pg_authid.oid  |OID do utilizador que executou a declaração|
|db_id  |oid    |pg_database.oid    |OID da base de dados em que a declaração foi executada|
|query_id   |bigint     ||Código de hash interno, calculado a partir da árvore de parse da declaração|
|event_type |texto       ||O tipo de evento para o qual o backend está esperando|
|evento  |texto       ||O nome do evento de espera se o backend está atualmente à espera|
|chamadas  |Número inteiro        ||Número do mesmo evento capturado|


### <a name="functions"></a>Funções
Query_store.qs_reset devoluções anuladas

`qs_reset` descarta todas as estatísticas recolhidas até agora pela Consulta Store. Esta função só pode ser executada pela função de administrador do servidor.

Query_store.staging_data_reset() devoluções anuladas

`staging_data_reset` descarta todas as estatísticas recolhidas na memória pela Consulta Store (isto é, os dados na memória que ainda não foram lavados na base de dados). Esta função só pode ser executada pela função de administrador do servidor.

## <a name="limitations-and-known-issues"></a>Limitações e problemas conhecidos
- Se um servidor PostgreSQL tiver o parâmetro default_transaction_read_only ligado, a Consulta Store não pode capturar dados.
- A funcionalidade da Consulta Store pode ser interrompida se encontrar longas consultas Unicode (>= 6000 bytes).
- [Leia réplicas](concepts-read-replicas.md) replicar dados da Consulta Store a partir do servidor principal. Isto significa que a Loja de Consulta de uma réplica de leitura não fornece estatísticas sobre consultas executadas na réplica da leitura.


## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [cenários onde a Consulta Store pode ser especialmente útil](concepts-query-store-scenarios.md).
- Saiba mais sobre [as melhores práticas para utilizar a Query Store.](concepts-query-store-best-practices.md)
