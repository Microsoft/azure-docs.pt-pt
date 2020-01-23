---
title: Como monitorar o cache do Azure para Redis
description: Saiba como monitorar a integridade e o desempenho de seu cache do Azure para instâncias de Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 07/13/2017
ms.openlocfilehash: 86cbeddba699e89ce1127dbac72dac81dcc41449
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76547494"
---
# <a name="how-to-monitor-azure-cache-for-redis"></a>Como monitorar o cache do Azure para Redis

O cache do Azure para Redis usa [Azure monitor](../azure-monitor/index.yml) para fornecer várias opções para monitorar suas instâncias de cache. Você pode exibir as métricas, fixar os gráficos de métricas no quadro inicial, personalizar o intervalo de data e hora dos gráficos de monitoramento, adicionar e remover métricas dos gráficos e definir alertas quando determinadas condições forem atendidas. Essas ferramentas permitem monitorar a integridade do cache do Azure para instâncias Redis e ajudá-lo a gerenciar seus aplicativos de cache.

As métricas para o cache do Azure para instâncias Redis são coletadas usando o comando Redis [info](https://redis.io/commands/info) aproximadamente duas vezes por minuto e automaticamente armazenadas por 30 dias (consulte [Exportar métricas de cache](#export-cache-metrics) para configurar uma política de retenção diferente) para que elas possam ser exibidas nos gráficos de métricas e avaliadas pelas regras de alerta. Para obter mais informações sobre os diferentes valores de informações usados para cada métrica de cache, consulte [métricas disponíveis e intervalos de relatório](#available-metrics-and-reporting-intervals).

<a name="view-cache-metrics"></a>

Para exibir as métricas de cache, [navegue](cache-configure.md#configure-azure-cache-for-redis-settings) até sua instância de cache no [portal do Azure](https://portal.azure.com).  O cache do Azure para Redis fornece alguns gráficos internos na folha **visão geral** e na folha **métricas do Redis** . Cada gráfico pode ser personalizado adicionando ou removendo métricas e alterando o intervalo de relatórios.

![Métricas de Redis](./media/cache-how-to-monitor/redis-cache-redis-metrics-blade.png)

## <a name="view-pre-configured-metrics-charts"></a>Exibir gráficos de métricas pré-configurados

A folha de **visão geral** tem os seguintes gráficos de monitoramento pré-configurados.

* [Gráficos de monitoramento](#monitoring-charts)
* [Gráficos de uso](#usage-charts)

### <a name="monitoring-charts"></a>Gráficos de monitoramento

A seção **monitoramento** na folha **visão geral** tem os gráficos **ocorrências e erros**, **gets e sets**, **conexões**e **total de comandos** .

![Gráficos de monitoramento](./media/cache-how-to-monitor/redis-cache-monitoring-part.png)

### <a name="usage-charts"></a>Gráficos de uso

A seção **uso** na folha **visão geral** tem a **carga do servidor Redis**, o **uso de memória**, a largura de banda da **rede**e os gráficos de **uso da CPU** , além de exibir o **tipo de preço** para a instância de cache.

![Gráficos de uso](./media/cache-how-to-monitor/redis-cache-usage-part.png)

O **tipo de preço** exibe o tipo de preço de cache e pode ser usado para [dimensionar](cache-how-to-scale.md) o cache para um tipo de preço diferente.

## <a name="view-metrics-with-azure-monitor"></a>Exibir métricas com o Azure monitor

Para exibir as métricas do Redis e criar gráficos personalizados usando Azure Monitor, clique em **métricas** no **menu de recursos**e personalize o gráfico usando as métricas desejadas, o intervalo de relatórios, o tipo de gráfico e muito mais.

![Métricas de Redis](./media/cache-how-to-monitor/redis-cache-monitor.png)

Para obter mais informações sobre como trabalhar com métricas usando Azure Monitor, consulte [visão geral das métricas no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

<a name="how-to-view-metrics-and-customize-chart"></a>
<a name="enable-cache-diagnostics"></a>
## <a name="export-cache-metrics"></a>Exportar métricas de cache

Por padrão, as métricas de cache no Azure Monitor são [armazenadas por 30 dias](../azure-monitor/platform/data-platform-metrics.md) e, em seguida, excluídas. Para persistir suas métricas de cache por mais de 30 dias, você pode [designar uma conta de armazenamento](../azure-monitor/platform/archive-diagnostic-logs.md) e especificar uma política de **retenção (dias)** para suas métricas de cache. 

Para configurar uma conta de armazenamento para suas métricas de cache:

1. Na página **cache do Azure para Redis** , no cabeçalho **monitoramento** , selecione **diagnóstico**.
2. Selecione **+ Adicionar configuração de diagnóstico**.
3. Nomeie as configurações.
4. Verifique **o arquivo morto em uma conta de armazenamento**. Você será cobrado pelas taxas de dados normais para armazenamento e transações quando enviar diagnósticos para uma conta de armazenamento.
4. Selecione **Configurar** para escolher a conta de armazenamento na qual armazenar as métricas de cache.
5. Na **métrica**título da tabela, marque a caixa de seleção ao lado dos itens de linha que você deseja armazenar, como **biométricas**. Especifique uma política de **retenção (dias)** . A retenção máxima de dias que você pode especificar é de **365 dias**. No entanto, se você quiser manter os dados de métricas para sempre, defina a **retenção (dias)** como **0**.
6. Clique em **Guardar**.


![Diagnóstico de Redis](./media/cache-how-to-monitor/redis-cache-diagnostics.png)

>[!NOTE]
>Além de arquivar suas métricas de cache no armazenamento, você também pode [transmiti-las para um hub de eventos ou enviá-las para Azure monitor logs](../azure-monitor/platform/rest-api-walkthrough.md#retrieve-metric-values).
>

Para acessar suas métricas, você pode exibi-las no portal do Azure conforme descrito anteriormente neste artigo e também pode acessá-las usando a [API REST de métricas Azure monitor](../azure-monitor/platform/stream-monitoring-data-event-hubs.md).

> [!NOTE]
> Se você alterar as contas de armazenamento, os dados na conta de armazenamento configurada anteriormente permanecerão disponíveis para download, mas não serão exibidos no portal do Azure.  
> 

## <a name="available-metrics-and-reporting-intervals"></a>Métricas e intervalos de relatórios disponíveis

As métricas de cache são relatadas usando vários intervalos de relatório, incluindo a **última hora**, **hoje**, **semana passada**e **personalizado**. A folha **métrica** para cada gráfico de métricas exibe os valores médio, mínimo e máximo para cada métrica no gráfico e algumas métricas exibem um total para o intervalo de relatório. 

Cada métrica inclui duas versões. Uma métrica mede o desempenho de todo o cache e, para caches que usam [clustering](cache-how-to-premium-clustering.md), uma segunda versão da métrica que inclui `(Shard 0-9)` no nome mede o desempenho de um único fragmento em um cache. Por exemplo, se um cache tiver quatro fragmentos, `Cache Hits` será o número total de ocorrências para todo o cache e `Cache Hits (Shard 3)` será apenas as ocorrências para esse fragmento do cache.

> [!NOTE]
> Mesmo quando o cache está ocioso sem aplicativos cliente ativos conectados, você pode ver alguma atividade de cache, como clientes conectados, uso de memória e operações que estão sendo executadas. Essa atividade é normal durante a operação de um cache do Azure para a instância Redis.
> 
> 

| Métrica | Descrição |
| --- | --- |
| Acertos do cache |O número de pesquisas de chave com êxito durante o intervalo de relatório especificado. Esse número é mapeado para `keyspace_hits` do comando [info](https://redis.io/commands/info) do Redis. |
| Latência de cache (visualização) | A latência do cache calculada com base na latência entre nós do cache. Essa métrica é medida em microssegundos e tem três dimensões: `Avg`, `Min`e `Max`, que representam a latência média, mínima e máxima do cache, respectivamente durante o intervalo de relatório especificado. |
| Erros de cache |O número de pesquisas de chave com falha durante o intervalo de relatório especificado. Esse número é mapeado para `keyspace_misses` do comando INFO do Redis. Os erros de cache não significam necessariamente que há um problema com o cache. Por exemplo, ao usar o padrão de programação colado em cache, um aplicativo procura primeiro no cache para um item. Se o item não estiver lá (erro de cache), o item será recuperado do banco de dados e adicionado ao cache para a próxima vez. Os erros de cache são um comportamento normal para o padrão de programação colado em cache. Se o número de erros de cache for maior do que o esperado, examine a lógica do aplicativo que popula e lê do cache. Se os itens estiverem sendo removidos do cache devido à pressão de memória, pode haver alguns erros de cache, mas uma métrica melhor para monitorar a pressão de memória seria `Used Memory` ou `Evicted Keys`. |
| Leitura de cache |A quantidade de dados lidos do cache em megabytes por segundo (MB/s) durante o intervalo de relatório especificado. Esse valor é derivado das placas de interface de rede que dão suporte à máquina virtual que hospeda o cache e não é Redis específico. **Esse valor corresponde à largura de banda de rede usada por esse cache. Se você quiser configurar alertas para limites de largura de banda de rede do lado do servidor, crie-os usando este `Cache Read` contador. Consulte [esta tabela](cache-faq.md#cache-performance) para ver os limites de largura de banda observados para vários tamanhos e tipos de preço de cache.** |
| Gravação de cache |A quantidade de dados gravados no cache, em megabytes por segundo (MB/s) durante o intervalo de relatório especificado. Esse valor é derivado das placas de interface de rede que dão suporte à máquina virtual que hospeda o cache e não é Redis específico. Esse valor corresponde à largura de banda de rede dos dados enviados para o cache do cliente. |
| Clientes Ligados |O número de conexões de cliente com o cache durante o intervalo de relatório especificado. Esse número é mapeado para `connected_clients` do comando INFO do Redis. Depois que o [limite de conexão](cache-configure.md#default-redis-server-configuration) for atingido, as tentativas de conexão subsequentes para o cache falharão. Mesmo que não haja nenhum aplicativo cliente ativo, ainda pode haver algumas instâncias de clientes conectados devido a conexões e processos internos. |
| CPU |A utilização da CPU do cache do Azure para o servidor Redis como uma porcentagem durante o intervalo de relatório especificado. Esse valor é mapeado para o contador de desempenho `\Processor(_Total)\% Processor Time` do sistema operacional. |
| Erros | Falhas específicas e problemas de desempenho que o cache pode estar enfrentando durante um intervalo de relatório especificado. Essa métrica tem oito dimensões que representam tipos de erro diferentes, mas pode ter mais adicionado no futuro. Os tipos de erro representados agora são os seguintes: <br/><ul><li>**Failover** – quando um cache executa failover (subordinado elevado ao mestre)</li><li>**Perda** de dados – quando há perda de dado no cache</li><li>**UnresponsiveClients** – quando os clientes não estão lendo dados do servidor com rapidez suficiente</li><li>**AoF** – quando há um problema relacionado à persistência do AoF</li><li>**RDB** – quando há um problema relacionado à persistência de RDB</li><li>**Importar** – quando há um problema relacionado à importação de RDB</li><li>**Exportar** – quando há um problema relacionado à exportação de RDB</li></ul> |
| Chaves removidas |O número de itens removidos do cache durante o intervalo de relatório especificado devido ao limite de `maxmemory`. Esse número é mapeado para `evicted_keys` do comando INFO do Redis. |
| Chaves expiradas |O número de itens expirados do cache durante o intervalo de relatório especificado. Esse valor é mapeado para `expired_keys` do comando INFO do Redis.|
| Visível |O número de operações Get do cache durante o intervalo de relatório especificado. Esse valor é a soma dos seguintes valores do comando Redis INFO All: `cmdstat_get`, `cmdstat_hget`, `cmdstat_hgetall`, `cmdstat_hmget`, `cmdstat_mget`, `cmdstat_getbit`e `cmdstat_getrange`e é equivalente à soma de acertos de cache e erros durante o intervalo de relatório. |
| Operações por segundo | O número total de comandos processados por segundo pelo servidor de cache durante o intervalo de relatório especificado.  Esse valor é mapeado para "instantaneous_ops_per_sec" do comando Redis INFO. |
| Carga do servidor Redis |A porcentagem de ciclos em que o servidor Redis está ocupado processando e não aguardando ocioso pelas mensagens. Se esse contador atingir 100, significa que o servidor Redis atingiu um teto de desempenho e a CPU não pode processar o trabalho mais rapidamente. Se você estiver vendo alta carga do servidor Redis, verá exceções de tempo limite no cliente. Nesse caso, você deve considerar a expansão ou o particionamento de seus dados em vários caches. |
| Conjuntos |O número de operações de conjunto para o cache durante o intervalo de relatório especificado. Esse valor é a soma dos seguintes valores do comando Redis INFO All: `cmdstat_set`, `cmdstat_hset`, `cmdstat_hmset`, `cmdstat_hsetnx`, `cmdstat_lset`, `cmdstat_mset`, `cmdstat_msetnx`, `cmdstat_setbit`, `cmdstat_setex`, `cmdstat_setrange`e `cmdstat_setnx`. |
| Total de chaves  | O número máximo de chaves no cache durante o período de tempo de relatório anterior. Esse número é mapeado para `keyspace` do comando INFO do Redis. Devido a uma limitação do sistema de métricas subjacente, para caches com clustering habilitado, o total de chaves retorna o número máximo de chaves do fragmento que tinham o número máximo de chaves durante o intervalo de relatório.  |
| Total de operações |O número total de comandos processados pelo servidor de cache durante o intervalo de relatório especificado. Esse valor é mapeado para `total_commands_processed` do comando INFO do Redis. Quando o cache do Azure para Redis é usado apenas para pub/sub, não haverá métricas para `Cache Hits`, `Cache Misses`, `Gets`ou `Sets`, mas haverá `Total Operations` métricas que refletem o uso de cache para operações pub/sub. |
| Memória usada |A quantidade de memória de cache usada para pares de chave/valor no cache em MB durante o intervalo de relatório especificado. Esse valor é mapeado para `used_memory` do comando INFO do Redis. Esse valor não inclui metadados ou fragmentação. |
| Porcentagem de memória usada | O% da memória total que está sendo usada durante o intervalo de relatório especificado.  Esse valor faz referência ao valor `used_memory` do comando INFO do Redis para calcular a porcentagem. |
| Memória RSS usada |A quantidade de memória de cache usada em MB durante o intervalo de relatório especificado, incluindo fragmentação e metadados. Esse valor é mapeado para `used_memory_rss` do comando INFO do Redis. |

<a name="operations-and-alerts"></a>
## <a name="alerts"></a>Alertas

Pode configurar a receção de alertas com base em métricas e registos de atividades. O Azure Monitor permite-lhe configurar um alerta para fazer o seguinte quando é acionado:

* Enviar uma notificação por e-mail
* Chamar um webhook
* Invocar uma Aplicação Lógica do Azure

Para configurar regras de alerta para seu cache, clique em **regras de alerta** no **menu de recursos**.

![Monitorização](./media/cache-how-to-monitor/redis-cache-monitoring.png)

Para obter mais informações sobre como configurar e usar alertas, consulte [visão geral de alertas](../monitoring-and-diagnostics/insights-alerts-portal.md).

## <a name="activity-logs"></a>Registos de Atividade
Os logs de atividade fornecem informações sobre as operações que foram realizadas no cache do Azure para instâncias Redis. Anteriormente, era conhecido como "logs de auditoria" ou "logs operacionais". Usando os logs de atividade, você pode determinar "o que, quem e quando" para qualquer operação de gravação (PUT, POST, DELETE) realizada no cache do Azure para instâncias Redis. 

> [!NOTE]
> Os logs de atividade não incluem operações de leitura (GET).
>

Para exibir os logs de atividade do seu cache, clique em **logs de atividade** no **menu de recursos**.

Para obter mais informações sobre logs de atividade, consulte [visão geral do log de atividades do Azure](../azure-monitor/platform/platform-logs-overview.md).
