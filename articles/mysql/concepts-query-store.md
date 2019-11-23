---
title: Repositório de Consultas no banco de dados do Azure para MySQL
description: Saiba mais sobre o recurso Repositório de Consultas no banco de dados do Azure para MySQL para ajudá-lo a acompanhar o desempenho ao longo do tempo.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: c8891fc96e3e511e4127b4e114a45b5a865cf8eb
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73603033"
---
# <a name="monitor-azure-database-for-mysql-performance-with-query-store"></a>Monitorar o desempenho do banco de dados do Azure para MySQL com o Repositório de Consultas

**Aplica-se a:** Banco de dados do Azure para MySQL 5,7

O recurso Repositório de Consultas no banco de dados do Azure para MySQL fornece uma maneira de controlar o desempenho de consulta ao longo do tempo. O Repositório de Consultas simplifica a solução de problemas de desempenho, ajudando você a encontrar rapidamente as consultas mais longas e com uso intensivo de recursos. Repositório de Consultas captura automaticamente um histórico de consultas e estatísticas de tempo de execução e as retém para sua análise. Ele separa os dados por janelas de tempo para que você possa ver os padrões de uso do banco de dados. Todos os usuários, bancos de dados e consultas são armazenados no banco de dados de esquema do **MySQL** na instância do banco de dados do Azure para MySQL.

## <a name="common-scenarios-for-using-query-store"></a>Cenários comuns para usar o Repositório de Consultas

O repositório de consultas pode ser usado em vários cenários, incluindo o seguinte:

- Detectando consultas regressadas
- Determinando o número de vezes que uma consulta foi executada em uma determinada janela de tempo
- Comparando o tempo médio de execução de uma consulta nas janelas de tempo para ver grandes deltas

## <a name="enabling-query-store"></a>Habilitando Repositório de Consultas

Repositório de Consultas é um recurso opcional, portanto, ele não está ativo por padrão em um servidor. O repositório de consultas é habilitado ou desabilitado globalmente para todos os bancos de dados em um determinado servidor e não pode ser ativado ou desativado por banco de dado.

### <a name="enable-query-store-using-the-azure-portal"></a>Habilitar Repositório de Consultas usando o portal do Azure

1. Entre no portal do Azure e selecione o servidor do banco de dados do Azure para MySQL.
1. Selecione **parâmetros de servidor** na seção **configurações** do menu.
1. Procure o parâmetro query_store_capture_mode.
1. Defina o valor como todos e **salve**.

Para habilitar as estatísticas de espera no seu Repositório de Consultas:

1. Procure o parâmetro query_store_wait_sampling_capture_mode.
1. Defina o valor como todos e **salve**.

Aguarde até 20 minutos para que o primeiro lote de dados persista no banco de dado MySQL.

## <a name="information-in-query-store"></a>Informações em Repositório de Consultas

Repositório de Consultas tem duas lojas:

- Um repositório de estatísticas de tempo de execução para manter as informações de estatísticas de execução de consulta.
- Um repositório de estatísticas de espera para manter informações de estatísticas de espera.

Para minimizar o uso de espaço, as estatísticas de execução de tempo de execução no repositório de estatísticas de tempo de execução são agregadas em uma janela de tempo fixa e configurável. As informações contidas nesses repositórios são visíveis consultando as exibições do repositório de consultas.

A consulta a seguir retorna informações sobre consultas no Repositório de Consultas:

```sql
SELECT * FROM mysql.query_store;
```

Ou esta consulta para estatísticas de espera:

```sql
SELECT * FROM mysql.query_store_wait_stats;
```

## <a name="finding-wait-queries"></a>Encontrando consultas de espera

> [!NOTE]
> As estatísticas de espera não devem ser habilitadas durante o horário de pico da carga de trabalho ou ser ativadas indefinidamente para cargas de trabalho confidenciais. <br>Para cargas de trabalho em execução com alta utilização de CPU ou em servidores configurados com menos vCores, tome cuidado ao habilitar estatísticas de espera. Ele não deve ser ativado indefinidamente. 

Os tipos de evento Wait combinam eventos de espera diferentes em buckets por similaridade. Repositório de Consultas fornece o tipo de evento Wait, o nome do evento de espera específico e a consulta em questão. Ser capaz de correlacionar essas informações de espera com as estatísticas de tempo de execução de consulta significa que você pode obter uma compreensão mais profunda do que contribui para consultar as características de desempenho.

Aqui estão alguns exemplos de como você pode obter mais informações sobre sua carga de trabalho usando as estatísticas de espera no Repositório de Consultas:

| **Observações** | **Ação** |
|---|---|
|Esperas de bloqueio altas | Verifique os textos de consulta para as consultas afetadas e identifique as entidades de destino. Examine Repositório de Consultas outras consultas que modificam a mesma entidade, que é executada com frequência e/ou têm alta duração. Depois de identificar essas consultas, considere alterar a lógica do aplicativo para melhorar a simultaneidade ou usar um nível de isolamento menos restritivo. |
|Esperas de e/s de buffer alto | Localize as consultas com um alto número de leituras físicas em Repositório de Consultas. Se eles corresponderem às consultas com altas esperas de e/s, considere a introdução de um índice na entidade subjacente, para fazer buscas em vez de verificações. Isso minimizaria a sobrecarga de e/s das consultas. Verifique as **recomendações de desempenho** para o servidor no portal para ver se há recomendações de índice para esse servidor que otimizaria as consultas. |
|Esperas de memória alta | Localize as principais consultas de consumo de memória no Repositório de Consultas. Essas consultas provavelmente estão atrasando o progresso das consultas afetadas. Verifique as **recomendações de desempenho** para seu servidor no portal para ver se há recomendações de índice que otimizarão essas consultas.|

## <a name="configuration-options"></a>Opções de configuração

Quando o Repositório de Consultas está habilitado, ele salva os dados em janelas de agregação de 15 minutos, até 500 consultas distintas por janela.

As opções a seguir estão disponíveis para configurar parâmetros de Repositório de Consultas.

| **Parâmetro** | **Descrição** | **Predefinição** | **Intervalo** |
|---|---|---|---|
| query_store_capture_mode | Ativar/desativar o recurso de repositório de consultas com base no valor. Observação: se performance_schema estiver desativado, ativar query_store_capture_mode ativará performance_schema e um subconjunto de instrumentos de esquema de desempenho necessário para esse recurso. | OS | NENHUM, TUDO |
| query_store_capture_interval | O intervalo de captura do repositório de consultas em minutos. Permite especificar o intervalo no qual as métricas de consulta são agregadas | 15 | 5 - 60 |
| query_store_capture_utility_queries | Ativar ou desativar o para capturar todas as consultas do utilitário que estão sendo executadas no sistema. | NO | SIM, NÃO |
| query_store_retention_period_in_days | Janela de tempo em dias para manter os dados no repositório de consultas. | 7 | 1 - 30 |

As opções a seguir se aplicam especificamente às estatísticas de espera.

| **Parâmetro** | **Descrição** | **Predefinição** | **Intervalo** |
|---|---|---|---|
| query_store_wait_sampling_capture_mode | Permite ativar/desativar as estatísticas de espera. | NONE | NENHUM, TUDO |
| query_store_wait_sampling_frequency | Altera a frequência de amostragem de espera em segundos. 5 a 300 segundos. | 30 | 5-300 |

> [!NOTE]
> Atualmente **query_store_capture_mode** substitui essa configuração, o que significa que **query_store_capture_mode** e **query_store_wait_sampling_capture_mode** precisam ser habilitados para que todas as estatísticas de espera funcionem. Se **query_store_capture_mode** for desativado, as estatísticas de espera serão desativadas, já que as estatísticas de espera utilizam o performance_schema habilitado e a query_text capturada pelo repositório de consultas.

Use o [portal do Azure](howto-server-parameters.md) ou [CLI do Azure](howto-configure-server-parameters-using-cli.md) para obter ou definir um valor diferente para um parâmetro.

## <a name="views-and-functions"></a>Exibições e funções

Exiba e gerencie Repositório de Consultas usando as seguintes exibições e funções. Qualquer pessoa na [função pública selecionar privilégio](howto-create-users.md#how-to-create-additional-admin-users-in-azure-database-for-mysql) pode usar essas exibições para ver os dados em repositório de consultas. Essas exibições estão disponíveis somente no banco de dados **MySQL** .

As consultas são normalizadas examinando sua estrutura depois de remover literais e constantes. Se duas consultas forem idênticas, exceto valores literais, elas terão o mesmo hash.

### <a name="mysqlquery_store"></a>mysql.query_store

Essa exibição retorna todos os dados em Repositório de Consultas. Há uma linha para cada ID de banco de dados, ID de usuário e ID de consulta distintos.

| **Nome** | **Tipo de dados** | **IS_NULLABLE** | **Descrição** |
|---|---|---|---|
| `schema_name`| varchar(64) | NO | Nome do esquema |
| `query_id`| bigint(20) | NO| ID exclusiva gerada para a consulta específica, se a mesma consulta for executada em um esquema diferente, uma nova ID será gerada |
| `timestamp_id` | carimbo de data/hora| NO| Carimbo de data/hora em que a consulta é executada. Isso se baseia na configuração de query_store_interval|
| `query_digest_text`| longtext| NO| O texto de consulta normalizado após a remoção de todos os literais|
| `query_sample_text` | longtext| NO| Primeira aparência da consulta real com literais|
| `query_digest_truncated` | bit| Ok| Se o texto da consulta foi truncado. O valor será Sim se a consulta tiver mais de 1 KB|
| `execution_count` | bigint(20)| NO| O número de vezes que a consulta foi executada para esta ID de carimbo de data/hora durante o período de intervalo configurado|
| `warning_count` | bigint(20)| NO| Número de avisos que esta consulta gerou durante a|
| `error_count` | bigint(20)| NO| Número de erros que esta consulta gerou durante o intervalo|
| `sum_timer_wait` | double| Ok| Tempo de execução total desta consulta durante o intervalo|
| `avg_timer_wait` | double| Ok| Tempo médio de execução para esta consulta durante o intervalo|
| `min_timer_wait` | double| Ok| Tempo mínimo de execução para esta consulta|
| `max_timer_wait` | double| Ok| Tempo de execução máximo|
| `sum_lock_time` | bigint(20)| NO| Quantidade total de tempo gasto para todos os bloqueios para esta execução de consulta durante esta janela de tempo|
| `sum_rows_affected` | bigint(20)| NO| Número de linhas afetadas|
| `sum_rows_sent` | bigint(20)| NO| Número de linhas enviadas ao cliente|
| `sum_rows_examined` | bigint(20)| NO| Número de linhas examinadas|
| `sum_select_full_join` | bigint(20)| NO| Número de junções completas|
| `sum_select_scan` | bigint(20)| NO| Número de verificações selecionadas |
| `sum_sort_rows` | bigint(20)| NO| Número de linhas classificadas|
| `sum_no_index_used` | bigint(20)| NO| Número de vezes em que a consulta não usou nenhum índice|
| `sum_no_good_index_used` | bigint(20)| NO| Número de vezes em que o mecanismo de execução de consulta não usou índices bons|
| `sum_created_tmp_tables` | bigint(20)| NO| Número total de tabelas temporárias criadas|
| `sum_created_tmp_disk_tables` | bigint(20)| NO| Número total de tabelas temporárias criadas no disco (gera e/s)|
| `first_seen` | carimbo de data/hora| NO| A primeira ocorrência (UTC) da consulta durante a janela de agregação|
| `last_seen` | carimbo de data/hora| NO| A última ocorrência (UTC) da consulta durante esta janela de agregação|

### <a name="mysqlquery_store_wait_stats"></a>mysql.query_store_wait_stats

Essa exibição retorna dados de eventos de espera em Repositório de Consultas. Há uma linha para cada ID de banco de dados, ID de usuário, ID de consulta e evento distintos.

| **Nome**| **Tipo de dados** | **IS_NULLABLE** | **Descrição** |
|---|---|---|---|
| `interval_start` | carimbo de data/hora | NO| Início do intervalo (incremento de 15 minutos)|
| `interval_end` | carimbo de data/hora | NO| Fim do intervalo (incremento de 15 minutos)|
| `query_id` | bigint(20) | NO| ID exclusiva gerada na consulta normalizada (do repositório de consultas)|
| `query_digest_id` | varchar(32) | NO| O texto de consulta normalizado após a remoção de todos os literais (do repositório de consultas) |
| `query_digest_text` | longtext | NO| Primeira aparência da consulta real com literais (do repositório de consultas) |
| `event_type` | varchar(32) | NO| Categoria do evento de espera |
| `event_name` | varchar(128) | NO| Nome do evento de espera |
| `count_star` | bigint(20) | NO| Número de eventos de espera amostrados durante o intervalo para a consulta |
| `sum_timer_wait_ms` | double | NO| Tempo de espera total (em milissegundos) desta consulta durante o intervalo |

### <a name="functions"></a>Functions

| **Nome**| **Descrição** |
|---|---|
| `mysql.az_purge_querystore_data(TIMESTAMP)` | Limpa todos os dados do repositório de consultas antes do carimbo de data/hora fornecido |
| `mysql.az_procedure_purge_querystore_event(TIMESTAMP)` | Limpa todos os dados de evento de espera antes do carimbo de data/hora fornecido |
| `mysql.az_procedure_purge_recommendation(TIMESTAMP)` | Limpa as recomendações cuja expiração é anterior ao carimbo de data/hora especificado |

## <a name="limitations-and-known-issues"></a>Limitações e problemas conhecidos

- Se um servidor MySQL tiver o parâmetro `default_transaction_read_only` ativado, Repositório de Consultas não poderá capturar dados.
- Repositório de Consultas funcionalidade poderá ser interrompida se encontrar consultas longas de Unicode (\>= 6000 bytes).
- O período de retenção para estatísticas de espera é de 24 horas.
- Estatísticas de espera usa exemplo para capturar uma fração de eventos. A frequência pode ser modificada usando o parâmetro `query_store_wait_sampling_frequency`.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre as [informações de desempenho de consulta](concepts-query-performance-insight.md)
