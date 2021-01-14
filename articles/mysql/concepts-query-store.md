---
title: Loja de Consultas - Base de Dados Azure para MySQL
description: Saiba mais sobre a funcionalidade Da Loja de Consultas na Base de Dados Azure para o MySQL para ajudá-lo a acompanhar o desempenho ao longo do tempo.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 5/12/2020
ms.openlocfilehash: 82482b260233994672e603c16fe8cf919c92337f
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201030"
---
# <a name="monitor-azure-database-for-mysql-performance-with-query-store"></a>Monitor Azure Database para desempenho do MySQL com a Loja de Consultas

**Aplica-se a:** Base de Dados Azure para MySQL 5.7, 8.0

A funcionalidade De Loja de Consultas na Base de Dados Azure para o MySQL fornece uma forma de acompanhar o desempenho da consulta ao longo do tempo. A Consulta Store simplifica a resolução de problemas de desempenho, ajudando-o a encontrar rapidamente as consultas mais longas e intensivas de recursos. A Loja de Consultas captura automaticamente um histórico de consultas e estatísticas de tempo de execução, e retém-nas para a sua revisão. Separa os dados por janelas de tempo para que possa ver padrões de utilização da base de dados. Os dados para todos os utilizadores, bases de dados e consultas são armazenados na base de **dados do esquema mysql** na Base de Dados Azure para o caso MySQL.

## <a name="common-scenarios-for-using-query-store"></a>Cenários comuns para a utilização da Loja de Consultas

A loja de consultas pode ser utilizada em vários cenários, incluindo os seguintes:

- Deteção de consultas regressas
- Determinar o número de vezes que uma consulta foi executada numa janela de tempo
- Comparando o tempo médio de execução de uma consulta através das janelas do tempo para ver grandes deltas

## <a name="enabling-query-store"></a>Loja de Consultas habilitadora

A Loja de Consultas é uma funcionalidade de opt-in, pelo que não está ativa por padrão num servidor. A loja de consultas está ativada ou desativada globalmente para todas as bases de dados de um determinado servidor e não pode ser ligada ou desligada por base de dados.

### <a name="enable-query-store-using-the-azure-portal"></a>Ativar a Loja de Consultas utilizando o portal Azure

1. Inscreva-se no portal Azure e selecione a base de dados Azure para o servidor MySQL.
1. Selecione **parâmetros** do servidor na secção **Definições** do menu.
1. Procure o parâmetro query_store_capture_mode.
1. Desa estação o valor para ALL e **Save**.

Para ativar as estatísticas de espera na sua Loja de Consultas:

1. Procure o parâmetro query_store_wait_sampling_capture_mode.
1. Desa estação o valor para ALL e **Save**.

Deixe até 20 minutos para que o primeiro lote de dados permaneça na base de dados mysql.

## <a name="information-in-query-store"></a>Informação na Loja de Consultas

A Loja de Consultas tem duas lojas:

- Uma loja de estatísticas de tempo de execução para persistir na informação estatística de execução de consultas.
- Uma loja de estatísticas de espera para informações de estatísticas de espera persistentes.

Para minimizar o uso do espaço, as estatísticas de execução em tempo de execução na loja de estatísticas de tempo de execução são agregadas sobre uma janela de tempo fixa e configurável. A informação nestas lojas é visível consultando as vistas da loja de consultas.

A seguinte consulta devolve informações sobre consultas na Loja de Consultas:

```sql
SELECT * FROM mysql.query_store;
```

Ou esta consulta para estatísticas de espera:

```sql
SELECT * FROM mysql.query_store_wait_stats;
```

## <a name="finding-wait-queries"></a>Encontrar consultas de espera

> [!NOTE]
> As estatísticas de espera não devem ser ativadas durante as horas de pico da carga de trabalho ou ser ligadas indefinidamente para cargas de trabalho sensíveis. <br>Para cargas de trabalho em execução com alta utilização do CPU ou em servidores configurados com vCores mais baixos, tenha cuidado ao permitir estatísticas de espera. Não deve ser ligado indefinidamente.

Os tipos de eventos de espera combinam diferentes eventos de espera em baldes por semelhança. A Loja de Consultas fornece o tipo de evento de espera, o nome específico do evento de espera e a consulta em questão. Ser capaz de correlacionar esta informação de espera com as estatísticas de tempo de consulta significa que você pode obter uma compreensão mais profunda do que contribui para a consulta das características de desempenho.

Aqui estão alguns exemplos de como pode obter mais informações sobre a sua carga de trabalho utilizando as estatísticas de espera na Loja de Consultas:

| **Observação** | **Ação** |
|---|---|
|High Lock espera | Consulte os textos de consulta para as consultas afetadas e identifique as entidades-alvo. Procure na Loja de Consultas outras consultas que modifiquem a mesma entidade, que é executada com frequência e/ou tem alta duração. Depois de identificar estas consultas, considere alterar a lógica da aplicação para melhorar a conúnquidade, ou usar um nível de isolamento menos restritivo. |
|High Buffer IO espera | Encontre as consultas com um elevado número de leituras físicas na Loja de Consultas. Se combinarem as consultas com as altas esperas de IO, considere introduzir um índice na entidade subjacente, para fazer procuras em vez de digitalizações. Isto minimizaria a despesa com o IO das consultas. Consulte as **Recomendações de Desempenho** do seu servidor no portal para ver se existem recomendações de índice para este servidor que otimizariam as consultas. |
|Alta Memória espera | Encontre as principais consultas de consumo de memória na Loja de Consultas. Estas consultas estão provavelmente a atrasar o progresso das consultas afetadas. Consulte as **Recomendações de Desempenho** do seu servidor no portal para ver se existem recomendações de índice que otimizem estas consultas. |

## <a name="configuration-options"></a>Opções de configuração

Quando a Loja de Consulta está ativada, guarda dados em janelas de agregação de 15 minutos, até 500 consultas distintas por janela.

As seguintes opções estão disponíveis para configurar parâmetros da Loja de Consultas.

| **Parâmetro** | **Descrição** | **Predefinição** | **Intervalo** |
|---|---|---|---|
| query_store_capture_mode | Rode a função de loja de consultas ON/OFF com base no valor. Nota: Se performance_schema estiver desligado, ligue query_store_capture_mode ligará performance_schema e um subconjunto de instrumentos de esquema de desempenho necessários para esta função. | ALL | NENHUM, TODOS. |
| query_store_capture_interval | O intervalo de captura da loja de consulta em minutos. Permite especificar o intervalo em que as métricas de consulta são agregadas | 15 | 5 - 60 |
| query_store_capture_utility_queries | Ligar ou desligar para capturar todas as consultas de utilidade que estão executando no sistema. | NO | SIM, NÃO. |
| query_store_retention_period_in_days | Janela de tempo em dias para reter os dados na loja de consultas. | 7 | 1 - 30 |

As seguintes opções aplicam-se especificamente às estatísticas de espera.

| **Parâmetro** | **Descrição** | **Predefinição** | **Intervalo** |
|---|---|---|---|
| query_store_wait_sampling_capture_mode | Permite ligar / DESLIGAR as estatísticas de espera. | NENHUMA | NENHUM, TODOS. |
| query_store_wait_sampling_frequency | Altera a frequência da amostra de espera em segundos. 5 a 300 segundos. | 30 | 5-300 |

> [!NOTE]
> Atualmente **query_store_capture_mode** substitui esta configuração, o que significa que tanto **query_store_capture_mode** como **query_store_wait_sampling_capture_mode** têm de ser ativados a TODOS para que as estatísticas de espera funcionem. Se **query_store_capture_mode** for desligado, as estatísticas de espera também são desligadas, uma vez que as estatísticas de espera utilizam o performance_schema ativado, e o query_text capturado por loja de consultas.

Utilize o [portal Azure](howto-server-parameters.md) ou [O CLI Azure](howto-configure-server-parameters-using-cli.md) para obter ou definir um valor diferente para um parâmetro.

## <a name="views-and-functions"></a>Vistas e funções

Ver e gerir a Loja de Consultas utilizando as seguintes vistas e funções. Qualquer pessoa no [papel público de privilégio selecionado](howto-create-users.md#to-create-more-admin-users-in-azure-database-for-mysql) pode usar estas opiniões para ver os dados na Loja de Consultas. Estas vistas só estão disponíveis na base de **dados mysql.**

As consultas são normalizadas olhando para a sua estrutura depois de remover literal e constantes. Se duas consultas forem idênticas, exceto para valores literais, terão o mesmo haxixe.

### <a name="mysqlquery_store"></a>mysql.query_store

Esta vista devolve todos os dados na Loja de Consultas. Há uma linha para cada ID de base de dados distinto, ID do utilizador e ID de consulta.

| **Nome** | **Tipo de Dados** | **IS_NULLABLE** | **Descrição** |
|---|---|---|---|
| `schema_name`| varchar(64) | NO | Nome do esquema |
| `query_id`| bigint(20) | NO| ID único gerado para a consulta específica, se a mesma consulta executar em esquema diferente, um novo ID será gerado |
| `timestamp_id` | carimbo de data/hora| NO| Tempotam em que a consulta é executada. Isto baseia-se na configuração query_store_interval|
| `query_digest_text`| texto longo| NO| O texto de consulta normalizado depois de remover todos os literais|
| `query_sample_text` | texto longo| NO| Primeira aparição da consulta real com literais|
| `query_digest_truncated` | bit| SIM| Se o texto de consulta foi truncado. Valor será Sim se a consulta for maior que 1 KB|
| `execution_count` | bigint(20)| NO| O número de vezes que a consulta foi executada para este ID de tempotamp / durante o período de intervalo configurado|
| `warning_count` | bigint(20)| NO| Número de advertências esta consulta gerada durante o interno|
| `error_count` | bigint(20)| NO| Número de erros gerados nesta consulta durante o intervalo|
| `sum_timer_wait` | double| SIM| Tempo total de execução desta consulta durante o intervalo|
| `avg_timer_wait` | double| SIM| Tempo médio de execução para esta consulta durante o intervalo|
| `min_timer_wait` | double| SIM| Tempo mínimo de execução para esta consulta|
| `max_timer_wait` | double| SIM| Tempo máximo de execução|
| `sum_lock_time` | bigint(20)| NO| Quantidade total de tempo gasto para todas as fechaduras para esta execução de consulta durante esta janela de tempo|
| `sum_rows_affected` | bigint(20)| NO| Número de linhas afetadas|
| `sum_rows_sent` | bigint(20)| NO| Número de linhas enviadas ao cliente|
| `sum_rows_examined` | bigint(20)| NO| Número de linhas examinadas|
| `sum_select_full_join` | bigint(20)| NO| Número de juntas completas|
| `sum_select_scan` | bigint(20)| NO| Número de digitalizações selecionadas |
| `sum_sort_rows` | bigint(20)| NO| Número de linhas ordenadas|
| `sum_no_index_used` | bigint(20)| NO| Número de vezes em que a consulta não usou quaisquer índices|
| `sum_no_good_index_used` | bigint(20)| NO| Número de vezes em que o motor de execução de consulta não usou quaisquer bons índices|
| `sum_created_tmp_tables` | bigint(20)| NO| Número total de tabelas temporárias criadas|
| `sum_created_tmp_disk_tables` | bigint(20)| NO| Número total de tabelas temporárias criadas em disco (gera E/O)|
| `first_seen` | carimbo de data/hora| NO| A primeira ocorrência (UTC) da consulta durante a janela de agregação|
| `last_seen` | carimbo de data/hora| NO| A última ocorrência (UTC) da consulta durante esta janela de agregação|

### <a name="mysqlquery_store_wait_stats"></a>mysql.query_store_wait_stats

Esta vista devolve os dados dos eventos de espera na Loja de Consultas. Há uma linha para cada ID de base de dados distinto, ID do utilizador, identificação de consulta e evento.

| **Nome**| **Tipo de Dados** | **IS_NULLABLE** | **Descrição** |
|---|---|---|---|
| `interval_start` | carimbo de data/hora | NO| Início do intervalo (incremento de 15 minutos)|
| `interval_end` | carimbo de data/hora | NO| Fim do intervalo (incremento de 15 minutos)|
| `query_id` | bigint(20) | NO| ID único gerado na consulta normalizada (da loja de consultas)|
| `query_digest_id` | varchar(32) | NO| O texto de consulta normalizado após a remoção de todos os literais (da loja de consultas) |
| `query_digest_text` | texto longo | NO| Primeira aparição da consulta real com literal (da loja de consultas) |
| `event_type` | varchar(32) | NO| Categoria do evento de espera |
| `event_name` | varchar(128) | NO| Nome do evento de espera |
| `count_star` | bigint(20) | NO| Número de eventos de espera amostrados durante o intervalo para a consulta |
| `sum_timer_wait_ms` | double | NO| Tempo total de espera (em milissegundos) desta consulta durante o intervalo |

### <a name="functions"></a>Funções

| **Nome**| **Descrição** |
|---|---|
| `mysql.az_purge_querystore_data(TIMESTAMP)` | Purga todos os dados da loja de consultas antes do carimbo de tempo dado |
| `mysql.az_procedure_purge_querystore_event(TIMESTAMP)` | Purga todos os dados do evento de espera antes do carimbo de tempo dado |
| `mysql.az_procedure_purge_recommendation(TIMESTAMP)` | Purga recomendações cuja expiração é antes do prazo dado |

## <a name="limitations-and-known-issues"></a>Problemas e limitações conhecidos

- Se um servidor MySQL tiver o parâmetro `default_transaction_read_only` ligado, a Loja de Consultas não pode capturar dados.
- A funcionalidade da Loja de Consulta pode ser interrompida se encontrar longas consultas unicode \> (= 6000 bytes).
- O período de retenção das estatísticas de espera é de 24 horas.
- As estatísticas de espera usam a amostra para capturar uma fração de eventos. A frequência pode ser modificada utilizando o parâmetro `query_store_wait_sampling_frequency` .

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [consultas de performance insights](concepts-query-performance-insight.md)
