---
title: Repositório de Consultas no banco de dados do Azure para PostgreSQL-servidor único
description: Este artigo descreve o recurso Repositório de Consultas no banco de dados do Azure para PostgreSQL-servidor único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: deab527d44713bffed1f430ec283592d0e4232ee
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70764420"
---
# <a name="monitor-performance-with-the-query-store"></a>Monitorar o desempenho com o Repositório de Consultas

**Aplica-se a:** Banco de dados do Azure para PostgreSQL-versões de servidor único 9,6, 10, 11

O recurso Repositório de Consultas no banco de dados do Azure para PostgreSQL fornece uma maneira de controlar o desempenho da consulta ao longo do tempo. O Repositório de Consultas simplifica a solução de problemas de desempenho, ajudando você a encontrar rapidamente as consultas mais longas e com uso intensivo de recursos. Repositório de Consultas captura automaticamente um histórico de consultas e estatísticas de tempo de execução e as retém para sua análise. Ele separa os dados por janelas de tempo para que você possa ver os padrões de uso do banco de dados. Os dados para todos os usuários, bancos de dados e consultas é armazenado em um banco de dados chamado **azure_sys** na instância do banco de dados do Azure para PostgreSQL.

> [!IMPORTANT]
> Não modifique o banco de dados **azure_sys** ou seus esquemas. Isso impedirá que Repositório de Consultas e recursos de desempenho relacionados funcionem corretamente.

## <a name="enabling-query-store"></a>Habilitando Repositório de Consultas
Repositório de Consultas é um recurso opcional, portanto, ele não está ativo por padrão em um servidor. A loja é habilitada ou desabilitada globalmente para todos os bancos de dados em um determinado servidor e não pode ser ativada ou desativada por banco de dado.

### <a name="enable-query-store-using-the-azure-portal"></a>Habilitar Repositório de Consultas usando o portal do Azure
1. Entre no portal do Azure e selecione o servidor do banco de dados do Azure para PostgreSQL.
2. Selecione **parâmetros de servidor** na seção **configurações** do menu.
3. Procure o `pg_qs.query_capture_mode` parâmetro.
4. Defina o valor como `TOP` e **salve**.

Para habilitar as estatísticas de espera no seu Repositório de Consultas: 
1. Procure o `pgms_wait_sampling.query_capture_mode` parâmetro.
1. Defina o valor como `ALL` e **salve**.


Como alternativa, você pode definir esses parâmetros usando o CLI do Azure.
```azurecli-interactive
az postgres server configuration set --name pg_qs.query_capture_mode --resource-group myresourcegroup --server mydemoserver --value TOP
az postgres server configuration set --name pgms_wait_sampling.query_capture_mode --resource-group myresourcegroup --server mydemoserver --value ALL
```

Aguarde até 20 minutos para que o primeiro lote de dados persista no banco de dado azure_sys.

## <a name="information-in-query-store"></a>Informações em Repositório de Consultas
Repositório de Consultas tem duas lojas:
- Um repositório de estatísticas de tempo de execução para persistir as informações de estatísticas de execução de consulta.
- Um armazenamento de estatísticas de espera para manter informações de estatísticas de espera.

Os cenários comuns para usar o Repositório de Consultas incluem:
- Determinando o número de vezes que uma consulta foi executada em uma determinada janela de tempo
- Comparando o tempo médio de execução de uma consulta nas janelas de tempo para ver grandes deltas
- Identificando consultas de execução mais longa nas últimas X horas
- Identificando as N principais consultas que estão aguardando recursos
- Entendendo a natureza de espera para uma consulta específica

Para minimizar o uso de espaço, as estatísticas de execução de tempo de execução no repositório de estatísticas de tempo de execução são agregadas em uma janela de tempo fixa e configurável. As informações contidas nesses repositórios são visíveis consultando as exibições do repositório de consultas.

A consulta a seguir retorna informações sobre consultas no Repositório de Consultas:
```sql
SELECT * FROM query_store.qs_view; 
``` 

Ou esta consulta para estatísticas de espera:
```sql
SELECT * FROM query_store.pgms_wait_sampling_view;
```

## <a name="finding-wait-queries"></a>Encontrando consultas de espera
Os tipos de evento Wait combinam eventos de espera diferentes em buckets por similaridade. Repositório de Consultas fornece o tipo de evento Wait, o nome do evento de espera específico e a consulta em questão. Ser capaz de correlacionar essas informações de espera com as estatísticas de tempo de execução de consulta significa que você pode obter uma compreensão mais profunda do que contribui para consultar as características de desempenho.

Aqui estão alguns exemplos de como você pode obter mais informações sobre sua carga de trabalho usando as estatísticas de espera no Repositório de Consultas:

| **Observações** | **ação** |
|---|---|
|Esperas de bloqueio altas | Verifique os textos de consulta para as consultas afetadas e identifique as entidades de destino. Examine Repositório de Consultas outras consultas que modificam a mesma entidade, que é executada com frequência e/ou têm alta duração. Depois de identificar essas consultas, considere alterar a lógica do aplicativo para melhorar a simultaneidade ou usar um nível de isolamento menos restritivo.|
| Esperas de e/s de buffer alto | Localize as consultas com um alto número de leituras físicas em Repositório de Consultas. Se eles corresponderem às consultas com altas esperas de e/s, considere a introdução de um índice na entidade subjacente, para fazer buscas em vez de verificações. Isso minimizaria a sobrecarga de e/s das consultas. Verifique as **recomendações de desempenho** para o servidor no portal para ver se há recomendações de índice para esse servidor que otimizaria as consultas.|
| Esperas de memória alta | Localize as principais consultas de consumo de memória no Repositório de Consultas. Essas consultas provavelmente estão atrasando o progresso das consultas afetadas. Verifique as **recomendações de desempenho** para seu servidor no portal para ver se há recomendações de índice que otimizarão essas consultas.|

## <a name="configuration-options"></a>Opções de configuração
Quando o Repositório de Consultas está habilitado, ele salva os dados em janelas de agregação de 15 minutos, até 500 consultas distintas por janela. 

As opções a seguir estão disponíveis para configurar parâmetros de Repositório de Consultas.

| **Parâmetro** | **Descrição** | **Predefinição** | **Intervalo**|
|---|---|---|---|
| pg_qs.query_capture_mode | Define quais instruções são rastreadas. | nenhum | nenhum, superior, todos |
| pg_qs.max_query_text_length | Define o tamanho máximo da consulta que pode ser salvo. As consultas mais longas serão truncadas. | 6000 | 100-10K |
| pg_qs.retention_period_in_days | Define o período de retenção. | 7 | 1 - 30 |
| pg_qs.track_utility | Define se os comandos do utilitário são controlados | em | ativado, desativado |

As opções a seguir se aplicam especificamente às estatísticas de espera.

| **Parâmetro** | **Descrição** | **Predefinição** | **Intervalo**|
|---|---|---|---|
| pgms_wait_sampling.query_capture_mode | Define quais instruções são rastreadas para estatísticas de espera. | nenhum | nenhum, tudo|
| Pgms_wait_sampling.history_period | Defina a frequência, em milissegundos, na qual os eventos de espera são amostrados. | 100 | 1-600000 |

> [!NOTE] 
> **pg_qs. query_capture_mode** substitui **pgms_wait_sampling. query_capture_mode**. Se pg_qs. query_capture_mode for NONE, a configuração pgms_wait_sampling. query_capture_mode não terá efeito.


Use o [portal do Azure](howto-configure-server-parameters-using-portal.md) ou [CLI do Azure](howto-configure-server-parameters-using-cli.md) para obter ou definir um valor diferente para um parâmetro.

## <a name="views-and-functions"></a>Exibições e funções
Exiba e gerencie Repositório de Consultas usando as seguintes exibições e funções. Qualquer pessoa na função pública PostgreSQL pode usar essas exibições para ver os dados em Repositório de Consultas. Essas exibições estão disponíveis somente no banco de dados **azure_sys** .

As consultas são normalizadas examinando sua estrutura depois de remover literais e constantes. Se duas consultas forem idênticas, exceto valores literais, elas terão o mesmo hash.

### <a name="query_storeqs_view"></a>query_store.qs_view
Essa exibição retorna todos os dados em Repositório de Consultas. Há uma linha para cada ID de banco de dados, ID de usuário e ID de consulta distintos. 

|**Name**   |**Tipo** | **Referências**  | **Descrição**|
|---|---|---|---|
|runtime_stats_entry_id |bigint | | ID da tabela runtime_stats_entries|
|user_id    |oid    |pg_authid.oid  |OID do usuário que executou a instrução|
|db_id  |oid    |pg_database.oid    |OID do banco de dados no qual a instrução foi executada|
|query_id   |bigint  || Código hash interno, computado da árvore de análise da instrução|
|query_sql_text |Varchar(10000)  || Texto de uma instrução representativa. Consultas diferentes com a mesma estrutura são agrupadas; Esse texto é o texto para a primeira das consultas no cluster.|
|plan_id    |bigint |   |ID do plano correspondente a esta consulta, ainda não disponível|
|start_time |timestamp  ||  As consultas são agregadas por buckets de tempo – o período de tempo de um Bucket é de 15 minutos por padrão. Esta é a hora de início correspondente ao Bucket de tempo para esta entrada.|
|end_time   |timestamp  ||  Hora de término correspondente ao Bucket de tempo para esta entrada.|
|chamadas  |bigint  || Número de vezes que a consulta foi executada|
|total_time |precisão dupla   ||  Tempo total de execução da consulta, em milissegundos|
|min_time   |precisão dupla   ||  Tempo mínimo de execução da consulta, em milissegundos|
|max_time   |precisão dupla   ||  Tempo máximo de execução de consulta, em milissegundos|
|mean_time  |precisão dupla   ||  Tempo médio de execução da consulta, em milissegundos|
|stddev_time|   precisão dupla    ||  Desvio padrão do tempo de execução da consulta, em milissegundos |
|linhas   |bigint ||  Número total de linhas recuperadas ou afetadas pela instrução|
|shared_blks_hit|   bigint  ||  Número total de acertos de cache de bloco compartilhado pela instrução|
|shared_blks_read|  bigint  ||  Número total de blocos compartilhados lidos pela instrução|
|shared_blks_dirtied|   bigint   || Número total de blocos compartilhados sujos pela instrução |
|shared_blks_written|   bigint  ||  Número total de blocos compartilhados gravados pela instrução|
|local_blks_hit|    bigint ||   Número total de acertos do cache de blocos locais pela instrução|
|local_blks_read|   bigint   || Número total de blocos locais lidos pela instrução|
|local_blks_dirtied|    bigint  ||  Número total de blocos locais sujos pela instrução|
|local_blks_written|    bigint  ||  Número total de blocos locais gravados pela instrução|
|temp_blks_read |bigint  || Número total de blocos temporários lidos pela instrução|
|temp_blks_written| bigint   || Número total de blocos temporários gravados pela instrução|
|blk_read_time  |precisão dupla    || Tempo total que a instrução gastou em blocos de leitura, em milissegundos (se track_io_timing estiver habilitada, caso contrário, zero)|
|blk_write_time |precisão dupla    || Tempo total que a instrução gastou para gravar blocos, em milissegundos (se track_io_timing estiver habilitada, caso contrário, zero)|
    
### <a name="query_storequery_texts_view"></a>query_store.query_texts_view
Esta exibição retorna dados de texto de consulta em Repositório de Consultas. Há uma linha para cada query_text distinta.

|**Name**|  **Tipo**|   **Descrição**|
|---|---|---|
|query_text_id  |bigint     |ID da tabela query_texts|
|query_sql_text |Varchar(10000)     |Texto de uma instrução representativa. Consultas diferentes com a mesma estrutura são agrupadas; Esse texto é o texto para a primeira das consultas no cluster.|

### <a name="query_storepgms_wait_sampling_view"></a>query_store.pgms_wait_sampling_view
Essa exibição retorna dados de eventos de espera em Repositório de Consultas. Há uma linha para cada ID de banco de dados, ID de usuário, ID de consulta e evento distintos.

|**Name**|  **Tipo**|   **Referências**| **Descrição**|
|---|---|---|---|
|user_id    |oid    |pg_authid.oid  |OID do usuário que executou a instrução|
|db_id  |oid    |pg_database.oid    |OID do banco de dados no qual a instrução foi executada|
|query_id   |bigint     ||Código hash interno, computado da árvore de análise da instrução|
|event_type |text       ||O tipo de evento para o qual o back-end está aguardando|
|event  |text       ||O nome do evento de espera se o back-end estiver aguardando atualmente|
|chamadas  |Integer        ||Número do mesmo evento capturado|


### <a name="functions"></a>Functions
Query_store. qs_reset () retorna void

`qs_reset` Descarta todas as estatísticas coletadas até agora por Repositório de Consultas. Essa função só pode ser executada pela função de administrador do servidor.

Query_store. staging_data_reset () retorna void

`staging_data_reset` Descarta todas as estatísticas coletadas na memória por Repositório de Consultas (ou seja, os dados na memória que ainda não foram liberados para o banco de dados). Essa função só pode ser executada pela função de administrador do servidor.

## <a name="limitations-and-known-issues"></a>Limitações e problemas conhecidos
- Se um servidor PostgreSQL tiver o parâmetro default_transaction_read_only ativado, Repositório de Consultas não poderá capturar dados.
- Repositório de Consultas funcionalidade poderá ser interrompida se encontrar consultas longas de Unicode (> = 6000 bytes).
- As [réplicas de leitura](concepts-read-replicas.md) replicam dados de repositório de consultas do servidor mestre. Isso significa que a Repositório de Consultas de uma réplica de leitura não fornece estatísticas sobre as consultas executadas na réplica de leitura.


## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [cenários em que repositório de consultas pode ser especialmente útil](concepts-query-store-scenarios.md).
- Saiba mais sobre [as práticas recomendadas para usar repositório de consultas](concepts-query-store-best-practices.md).
