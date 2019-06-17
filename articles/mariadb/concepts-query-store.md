---
title: Store de consulta na base de dados do Azure para MariaDB
description: Este artigo descreve a funcionalidade de Store de consulta na base de dados do Azure para MariaDB
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/12/2019
ms.openlocfilehash: 9016fa159e868f649901928cdf2dca2f08725e77
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079395"
---
# <a name="monitor-azure-database-for-mariadb-performance-with-query-store"></a>Monitorizar a base de dados do Azure para desempenho da MariaDB com Store de consulta

**Aplica-se a:**  base de dados do Azure para MariaDB 10.2

> [!NOTE]
> Consulta Store está em pré-visualização. Suporte para consulta Store no portal do Azure está a ser lançado e talvez ainda não estar disponível na sua região.

A funcionalidade de Store de consulta no banco de dados do Azure para Mariadb fornece uma maneira de controlar o desempenho das consultas ao longo do tempo. Consulta Store simplifica o desempenho de resolução de problemas, ajudando-o rapidamente encontrar as consultas de maior duração e muitos mais recursos. Consulta Store automaticamente captura um histórico das consultas e estatísticas de tempo de execução e mantém-los para revisão. Ela separa dados por intervalos de tempo para que possa ver padrões de utilização de bases de dados. Dados para todos os utilizadores, as bases de dados e consultas são armazenados no **mysql** base de dados de esquema na base de dados do Azure para MariaDB instância.

## <a name="common-scenarios-for-using-query-store"></a>Cenários comuns para utilizar o Query Store

Arquivo de consultas pode ser usado em vários cenários, incluindo o seguinte:

- Detetar consultas regredidas
- Determinar o número de vezes que uma consulta foi executada numa janela de tempo especificado
- Comparar o tempo médio de execução de uma consulta em intervalos de tempo para ver os deltas grandes
- Identificar as consultas de execução mais longa no passado X horas
- Identificação de consultas de N principais que estão a aguardar nos recursos
- Natureza de espera de compreensão de consulta
- Compreender as tendências de esperas de recursos e onde existe contenção de recursos

## <a name="enabling-query-store"></a>Ativar consulta Store

Consulta Store é um recurso opcional, para que ele não estará ativo por predefinição num servidor. O arquivo de consultas está ativado ou desativado globalmente para todas as bases de dados num determinado servidor e não pode ser ativado ou desativado por base de dados.

### <a name="enable-query-store-using-the-azure-portal"></a>Ativar Store de consulta com o portal do Azure

1. Inicie sessão no portal do Azure e selecione a sua base de dados do Azure para MariaDB server.
1. Selecione **parâmetros do servidor** no **definições** secção do menu.
1. Pesquisa para o parâmetro query_store_capture_mode.
1. Defina o valor a todas as e **guardar**.

Para ativar as estatísticas de espera no seu Store de consulta:

1. Pesquisa para o parâmetro query_store_wait_sampling_capture_mode.
1. Defina o valor a todas as e **guardar**.

Aguarde 20 minutos do primeiro lote de dados para se manter na base de dados mysql.

## <a name="information-in-query-store"></a>Informações em Store de consulta

Consulta Store tem dois arquivos:

- Armazenar uma estatísticas de tempo de execução para manter as informações de estatísticas de execução de consulta.
- Um arquivo de estatísticas de espera para manter informações de estatísticas de espera.

Para minimizar a utilização de espaço, as estatísticas de execução do tempo de execução no arquivo de estatísticas de tempo de execução são agregadas ao longo de uma janela de tempo fixo e configurável. As informações desses arquivos são visíveis ao consultar os modos de exibição do arquivo de consulta.

A consulta seguinte devolve informações sobre consultas no Query Store:

```sql
SELECT * FROM mysql.query_store;
```

Ou esta consulta para as estatísticas de espera:

```sql
SELECT * FROM mysql.query_store_wait_stats;
```

## <a name="finding-wait-queries"></a>A localizar consultas de espera

Tipos de eventos de espera combinam diferentes de espera eventos em buckets por semelhança. Consulta Store fornece o tipo de evento de espera, nome do evento específico de espera e a consulta em questão. A capacidade de correlacionar essas informações de espera com o tempo de execução de consulta estatísticas significa que pode obter um conhecimento mais aprofundado sobre o que contribui para características de desempenho de consulta.

Aqui estão alguns exemplos de como pode obter mais informações sobre sua carga de trabalho com as estatísticas de espera no Query Store:

| **Observação** | **ação** |
|---|---|
|Esperas de bloqueio elevado | Verifique os textos de consulta para consultas afetadas e identificar as entidades de destino. Procure no Query Store outras consultas modificar a mesma entidade, o que é executada com frequência e/ou tem alta duração. Depois de identificar estas consultas, considere alterar a lógica do aplicativo para aprimorar a simultaneidade ou utilizar um nível de isolamento menos restritivo. |
|Esperas de alto de memória intermédia de e/s | Determinar as consultas com um elevado número de leituras físicas na Store de consulta. Se coincidirem as consultas com alta esperas de e/s, considere a introdução de um índice na entidade subjacente, fazer buscas em vez de análises. Isso seria minimizar a sobrecarga de e/s das consultas. Verifique os **recomendações de desempenho** para o seu servidor no portal para ver se existem recomendações de índice para este servidor que seria otimizar as consultas. |
|Esperas de memória elevadas | Encontre a consultas no Query Store de consumo de memória superior. Estas consultas são provavelmente atrasando ainda mais o progresso das consultas afetadas. Verifique os **recomendações de desempenho** para o seu servidor no portal para ver se existem recomendações de índice que seriam a otimizar estas consultas.|

## <a name="configuration-options"></a>Opções de configuração

Quando a consulta Store está ativada guarda os dados no windows de agregação de 15 minutos, até 500 consultas distintas por janela.

As seguintes opções estão disponíveis para configurar parâmetros de consulta Store.

| **Parâmetro** | **Descrição** | **Predefinição** | **Range** |
|---|---|---|---|
| query_store_capture_mode | Ative a funcionalidade de arquivo de consulta ON/OFF com base no valor. Nota: Se performance_schema estiver desligado, ativando query_store_capture_mode irá ativar performance_schema e um subconjunto de instrumentos de esquema de desempenho necessário para esta funcionalidade. | TODOS OS | NONE, TUDO |
| query_store_capture_interval | O arquivo de consultas capturar intervalo em minutos. Permite especificar o intervalo no qual as métricas de consulta são agregadas | 15 | 5 - 60 |
| query_store_capture_utility_queries | Desativar ou ativar ou DESATIVAR a capturar todas as consultas de utilitário que está em execução no sistema. | NO | SIM, NÃO |
| query_store_retention_period_in_days | Janela de tempo em dias para manter os dados no arquivo de consultas. | 7 | 1 - 30 |

As opções seguintes aplicam-se especificamente a estatísticas de espera.

| **Parâmetro** | **Descrição** | **Predefinição** | **Range** |
|---|---|---|---|
| query_store_wait_sampling_capture_mode | Permite ativar ON / DESATIVADO as estatísticas de espera. | NENHUM | NONE, TUDO |
| query_store_wait_sampling_frequency | Frequência de Alters da amostragem de espera em segundos. 5 e 300 segundos. | 30 | 5-300 |

> [!NOTE]
> Atualmente **query_store_capture_mode** prevalece sobre esta configuração, que significa que ambos **query_store_capture_mode** e **query_store_wait_sampling_capture_mode** tem de estar ativada para todos os para as estatísticas de espera trabalhar. Se **query_store_capture_mode** está desativado, em seguida, as estatísticas de espera é desativada, bem, uma vez que as estatísticas de espera utiliza performance_schema ativada e o query_text capturado pelo arquivo de consultas.

Utilize o [portal do Azure](howto-server-parameters.md) para obter ou definir um valor diferente para um parâmetro.

## <a name="views-and-functions"></a>Funções e exibições

Ver e gerir Store de consulta com as seguintes vistas e funções. Qualquer pessoa na [função pública de privilégio selecione](howto-create-users.md#create-additional-admin-users) pode utilizar estas vistas para ver os dados na consulta Store. Estas vistas só estão disponíveis na **mysql** base de dados.

Consultas são normalizadas examinando sua estrutura após a remoção de literais e constantes. Se duas consultas são idênticas, exceto para valores literais, eles terão o mesmo hash.

### <a name="mysqlquerystore"></a>mysql.query_store

Esta vista devolve todos os dados na consulta Store. Há uma linha para cada base de dados distinto ID, o ID de utilizador e o ID de consulta.

| **Nome** | **Tipo de dados** | **IS_NULLABLE** | **Descrição** |
|---|---|---|---|
| `schema_name`| varchar(64) | NO | Nome do esquema |
| `query_id`| bigint(20) | NO| ID exclusivo gerado para a consulta específica, se a mesma consulta é executada no esquema diferente, um novo ID será gerado |
| `timestamp_id` | timestamp| NO| Timestamp em que a consulta é executada. Isso se baseia na configuração query_store_interval|
| `query_digest_text`| longtext| NO| O texto da consulta normalizado depois de remover todos os literais|
| `query_sample_text` | longtext| NO| Primeiro aspeto da consulta real com literais|
| `query_digest_truncated` | bit| SIM| Se o texto da consulta foi truncado. Valor será Sim, se a consulta tiver mais de 1 KB|
| `execution_count` | bigint(20)| NO| O número de vezes que a consulta foi executada para este ID de timestamp / durante o período de intervalo configurado|
| `warning_count` | bigint(20)| NO| Número de avisos esta consulta gerados durante o interno|
| `error_count` | bigint(20)| NO| Número de erros esta consulta gerada durante o intervalo|
| `sum_timer_wait` | double| SIM| Total de tempo de execução desta consulta durante o intervalo|
| `avg_timer_wait` | double| SIM| Tempo médio de execução para esta consulta durante o intervalo|
| `min_timer_wait` | double| SIM| Tempo de execução mínimo para esta consulta|
| `max_timer_wait` | double| SIM| Tempo de execução máximo|
| `sum_lock_time` | bigint(20)| NO| Quantidade total de tempo gasto para todos os bloqueios para esta execução da consulta durante este período de tempo|
| `sum_rows_affected` | bigint(20)| NO| Número de linhas afetadas|
| `sum_rows_sent` | bigint(20)| NO| Número de linhas enviado ao cliente|
| `sum_rows_examined` | bigint(20)| NO| Número de linhas examinado|
| `sum_select_full_join` | bigint(20)| NO| Número de joins completos|
| `sum_select_scan` | bigint(20)| NO| Número de análises, selecionadas |
| `sum_sort_rows` | bigint(20)| NO| Número de linhas ordenadas|
| `sum_no_index_used` | bigint(20)| NO| Número de vezes que quando a consulta não utilizou os índices|
| `sum_no_good_index_used` | bigint(20)| NO| Número de vezes que quando o motor de execução de consulta não utilizou boa índices|
| `sum_created_tmp_tables` | bigint(20)| NO| Número total de tabelas temporárias criadas|
| `sum_created_tmp_disk_tables` | bigint(20)| NO| Número total de tabelas temporárias criadas no disco (gera e/s)|
| `first_seen` | timestamp| NO| A primeira ocorrência (UTC) da consulta durante a janela de agregação|
| `last_seen` | timestamp| NO| A última ocorrência (UTC) da consulta durante esta janela de agregação|

### <a name="mysqlquerystorewaitstats"></a>mysql.query_store_wait_stats

Esta vista devolve os dados de eventos na consulta Store de espera. Há uma linha para cada ID de base de dados distintos, ID de utilizador, ID de consulta e eventos.

| **Nome**| **Tipo de dados** | **IS_NULLABLE** | **Descrição** |
|---|---|---|---|
| `interval_start` | timestamp | NO| Início do intervalo (incremento de 15 minutos)|
| `interval_end` | timestamp | NO| Fim do intervalo (incremento de 15 minutos)|
| `query_id` | bigint(20) | NO| ID exclusivo gerado na consulta normalizado (a partir do arquivo de consultas)|
| `query_digest_id` | varchar(32) | NO| O texto da consulta normalizado depois de remover todos os literais (a partir do arquivo de consultas) |
| `query_digest_text` | longtext | NO| Primeiro aspeto da consulta real com literais (a partir do arquivo de consultas) |
| `event_type` | varchar(32) | NO| Categoria do evento de espera |
| `event_name` | varchar(128) | NO| Nome do evento de espera |
| `count_star` | bigint(20) | NO| Número de eventos de espera amostragem durante o intervalo para a consulta |
| `sum_timer_wait_ms` | double | NO| Tempo de espera total (em milissegundos) desta consulta durante o intervalo |

### <a name="functions"></a>Funções

| **Nome**| **Descrição** |
|---|---|
| `mysql.az_purge_querystore_data(TIMESTAMP)` | Remove todos os dados de arquivo de consulta antes do carimbo de hora especificado |
| `mysql.az_procedure_purge_querystore_event(TIMESTAMP)` | Esperar de limpezas de todos os dados de eventos antes do carimbo de hora especificado |
| `mysql.az_procedure_purge_recommendation(TIMESTAMP)` | Recomendações de limpezas de cuja expiração é antes do carimbo de hora especificado |

## <a name="limitations-and-known-issues"></a>Limitações e problemas conhecidos

- Se um servidor de MariaDB tem o parâmetro `default_transaction_read_only` , Store de consulta não é possível capturar os dados.
- Funcionalidade de consulta Store pode ser interrompida se encontrar consultas de Unicode longas (\>= 6000 bytes).
- O período de retenção para as estatísticas de espera é de 24 horas.
- As estatísticas de espera utiliza uma fração dos eventos de captura de te de exemplo. A frequência pode ser modificada utilizando o parâmetro `query_store_wait_sampling_frequency`.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [informações de desempenho de consulta](concepts-query-performance-insight.md)
