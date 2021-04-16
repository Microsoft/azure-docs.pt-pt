---
title: Loja de Consultas - Base de Dados Azure para PostgreSQL - Flex Server
description: Este artigo descreve a funcionalidade De Loja de Consultas na Base de Dados Azure para PostgreSQL - Flex Server.
author: ssen-msft
ms.author: ssen
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: 6ab2ea6f6544bcf561e92f00b5afee693393c157
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559207"
---
# <a name="monitor-performance-with-query-store"></a>Monitorize o Desempenho com a Loja de Consultas
**Aplica-se a:** Base de Dados Azure para pós-SQL - Flex Server versões 11 e superior

A funcionalidade de Loja de Consultas na Base de Dados Azure para PostgreSQL fornece uma forma de acompanhar o desempenho da consulta ao longo do tempo. A Loja de Consultas simplifica a resolução de problemas de desempenho, ajudando-o a encontrar rapidamente as consultas mais longas e intensivas de recursos. A Loja de Consultas captura automaticamente um histórico de consultas e estatísticas de tempo de execução, e retém-nas para a sua revisão. Corta os dados com o tempo para que possa ver padrões de uso temporal. Os dados para todos os utilizadores, bases de dados e consultas são armazenados numa base de dados denominada **azure_sys** na Base de Dados Azure para o caso PostgreSQL.

> [!IMPORTANT]
> Não modifique a base **de dados azure_sys** ou o seu esquema. Ao fazê-lo, impedirá que a Loja de Consultas e as funcionalidades de desempenho relacionadas funcionem corretamente.
## <a name="enabling-query-store"></a>Loja de Consultas habilitadora
A Loja de Consultas é uma funcionalidade de opt-in, pelo que não é ativada por padrão num servidor. A loja de consultas está ativada ou desativada globalmente para todas as bases de dados de um determinado servidor e não pode ser ligada ou desligada por base de dados.
### <a name="enable-query-store-using-the-azure-portal"></a>Ativar a Loja de Consultas utilizando o portal Azure
1. Inscreva-se no portal Azure e selecione a base de dados Azure para o servidor PostgreSQL.
2. Selecione **parâmetros** do servidor na secção **Definições** do menu.
3. Procure o `pg_qs.query_capture_mode` parâmetro.
4. Desa estade o valor para `TOP` ou `ALL` e **economize**.
Aguarde 20 minutos para o primeiro lote de dados se manter na base de dados azure_sys.
## <a name="information-in-query-store"></a>Informação na Loja de Consultas
A Loja de Consultas tem uma loja:
- Uma loja de estatísticas de tempo de execução para persistir na informação estatística de execução de consultas.

Os cenários comuns para a utilização da Loja de Consultas incluem:
- Determinar o número de vezes que uma consulta foi executada numa janela de tempo
- Comparando o tempo médio de execução de uma consulta através das janelas do tempo para ver grandes deltas
- Identificando as consultas de execução mais longas nas últimas horas Para minimizar o uso do espaço, as estatísticas de execução na loja de estatísticas de tempo de execução são agregadas sobre uma janela de tempo fixa e configurável. As informações nestas lojas podem ser consultadas com vistas.
## <a name="access-query-store-information"></a>Acesso Informações da Loja de Consultas
Os dados da Loja de Consulta são armazenados na base de dados azure_sys no seu servidor Postgres. A seguinte consulta devolve informações sobre consultas na Loja de Consultas:
```sql
SELECT * FROM query_store.qs_view; 
``` 

## <a name="configuration-options"></a>Opções de configuração
Quando a Loja de Consulta está ativada, guarda dados em janelas de agregação de 15 minutos, até 500 consultas distintas por janela. As seguintes opções estão disponíveis para configurar parâmetros da Loja de Consultas.

| **Parâmetro** | **Descrição** | **Predefinição** | **Intervalo**|
|---|---|---|---|
| pg_qs.consulta_capture_mode | Conjuntos que declarações são rastreadas. | nenhum | ninguém, em cima, todos os |
| pg_qs.max_consulta_text_length | Define o comprimento máximo de consulta que pode ser guardado. Consultas mais longas serão truncadas. | 6000 | 100 - 10K |
| pg_qs.retenção_period_in_days | Define o período de retenção. | 7 | 1 - 30 |
| pg_qs.track_utility | Define se os comandos de utilidade são rastreados | em | em, fora |

Utilize o [portal Azure](howto-configure-server-parameters-using-portal.md) para obter ou definir um valor diferente para um parâmetro.

## <a name="views-and-functions"></a>Vistas e funções
Ver e gerir a Loja de Consultas utilizando as seguintes vistas e funções. Qualquer pessoa no papel público postgreSQL pode usar estas opiniões para ver os dados na Query Store. Estas opiniões só estão disponíveis na base **de dados azure_sys.**
As consultas são normalizadas olhando para a sua estrutura depois de remover literal e constantes. Se duas consultas forem idênticas, exceto para valores literais, terão a mesma consulta.
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

### <a name="functions"></a>Funções
`qs_reset` descarta todas as estatísticas recolhidas até agora pela Query Store. Esta função só pode ser executada pela função de administração do servidor.

`staging_data_reset` descarta todas as estatísticas recolhidas na memória pela Query Store (isto é, os dados na memória que ainda não foram lavados na base de dados). Esta função só pode ser executada pela função de administração do servidor.

## <a name="limitations-and-known-issues"></a>Problemas e limitações conhecidos
- Se um servidor PostgreSQL tiver o parâmetro default_transaction_read_only ligado, a Query Store não capturará quaisquer dados.
## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [cenários onde a Loja de Consultas pode ser especialmente útil.](concepts-query-store-scenarios.md)
- Saiba mais sobre [as melhores práticas para a utilização da Loja de Consultas.](concepts-query-store-best-practices.md)
