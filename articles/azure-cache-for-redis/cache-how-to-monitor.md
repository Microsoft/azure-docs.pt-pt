---
title: Como monitorizar o Azure Cache para redis
description: Saiba como monitorizar a saúde e o desempenho do seu Azure Cache para os casos redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 07/13/2017
ms.openlocfilehash: 86cbeddba699e89ce1127dbac72dac81dcc41449
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76547494"
---
# <a name="how-to-monitor-azure-cache-for-redis"></a>Como monitorizar o Azure Cache para redis

O Azure Cache for Redis utiliza o [Monitor Azure](../azure-monitor/index.yml) para fornecer várias opções para monitorizar as suas instâncias de cache. Pode visualizar métricas, gráficos de métricas de pin os gráficos para a Startboard, personalizar a gama de data e hora dos gráficos de monitorização, adicionar e remover métricas dos gráficos, e definir alertas quando determinadas condições são satisfeitas. Estas ferramentas permitem-lhe monitorizar a saúde do seu Azure Cache para os casos Redis e ajudá-lo a gerir as suas aplicações de cache.

As métricas para os casos de Azure Cache para Redis são recolhidas utilizando o comando Redis [INFO](https://redis.io/commands/info) aproximadamente duas vezes por minuto e armazenadas automaticamente durante 30 dias (ver métricas de [cache de exportação](#export-cache-metrics) para configurar uma política de retenção diferente) para que possam ser exibidas nos gráficos de métricas e avaliadas pelas regras de alerta. Para obter mais informações sobre os diferentes valores de INFO utilizados para cada métrica de cache, consulte [métricas disponíveis e intervalos](#available-metrics-and-reporting-intervals)de reporte disponíveis.

<a name="view-cache-metrics"></a>

Para ver as métricas de cache, [navegue](cache-configure.md#configure-azure-cache-for-redis-settings) até à sua cache no [portal Azure](https://portal.azure.com).  O Azure Cache for Redis fornece algumas tabelas incorporadas na lâmina **de visão geral** e na lâmina de **métricaredis.** Cada gráfico pode ser personalizado adicionando ou removendo métricas e alterando o intervalo de reporte.

![Métricas redis](./media/cache-how-to-monitor/redis-cache-redis-metrics-blade.png)

## <a name="view-pre-configured-metrics-charts"></a>Ver gráficos de métricas pré-configurados

A lâmina **de visão geral** tem os seguintes gráficos de monitorização pré-configurados.

* [Gráficos de monitorização](#monitoring-charts)
* [Gráficos de utilização](#usage-charts)

### <a name="monitoring-charts"></a>Gráficos de monitorização

A secção **de monitorização** na lâmina **de visão geral** tem gráficos de Acesso e **Misses,** **Gets and Sets,** **Connections**e **Total Commands.**

![Gráficos de monitorização](./media/cache-how-to-monitor/redis-cache-monitoring-part.png)

### <a name="usage-charts"></a>Gráficos de utilização

A secção **de utilização** na lâmina **de visão geral** tem carga do servidor **Redis,** **utilização da memória,** **largura de banda de rede**e gráficos de utilização do **CPU,** e também apresenta o **nível** de preços para a instância de cache.

![Gráficos de utilização](./media/cache-how-to-monitor/redis-cache-usage-part.png)

O **nível** de preços apresenta o nível de preços da cache e pode ser usado para [escalar](cache-how-to-scale.md) a cache para um nível de preços diferente.

## <a name="view-metrics-with-azure-monitor"></a>Ver métricas com monitor Azure

Para ver as métricas redis e criar gráficos personalizados utilizando o Monitor Azure, clique em **Métricas** do **menu Derecursos**e personalize o seu gráfico utilizando as métricas desejadas, intervalo de reporte, tipo de gráfico e muito mais.

![Métricas redis](./media/cache-how-to-monitor/redis-cache-monitor.png)

Para obter mais informações sobre o trabalho com métricas utilizando o Monitor Azure, consulte a [visão geral das métricas no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

<a name="how-to-view-metrics-and-customize-chart"></a>
<a name="enable-cache-diagnostics"></a>
## <a name="export-cache-metrics"></a>Métricas de cache de exportação

Por predefinição, as métricas de cache no Monitor Azure são [armazenadas durante 30 dias](../azure-monitor/platform/data-platform-metrics.md) e depois eliminadas. Para persistir as métricas de cache por mais de 30 dias, pode [designar uma conta](../azure-monitor/platform/archive-diagnostic-logs.md) de armazenamento e especificar uma política de **retenção (dias)** para as suas métricas de cache. 

Para configurar uma conta de armazenamento para as métricas de cache:

1. Na página **Azure Cache for Redis,** sob a rubrica **Monitoring,** selecione **Diagnósticos**.
2. Selecione **+ Adicione a definição de diagnóstico**.
3. Diga as definições.
4. Consulte o Arquivo numa conta de **armazenamento.** Ser-lhe-ão cobradas taxas de dados normais para armazenamento e transações quando enviar diagnósticos para uma conta de armazenamento.
4. **Selecione Configure** para escolher a conta de armazenamento para armazenar as métricas de cache.
5. Sob a **métrica**da cabeça de mesa, verifique a caixa ao lado dos itens de linha que pretende armazenar, como a **AllMetrics**. Especifique uma política de **retenção (dias).** A retenção máxima de dias que pode especificar é de **365 dias**. No entanto, se quiser reter os dados métricos para sempre, detete a **Retenção (dias)** para **0**.
6. Clique em **Guardar**.


![Diagnósticos redis](./media/cache-how-to-monitor/redis-cache-diagnostics.png)

>[!NOTE]
>Além de arquivar as suas métricas de cache para armazenamento, também pode [transmiti-las para um hub de eventos ou enviá-las para registos do Monitor Azure](../azure-monitor/platform/rest-api-walkthrough.md#retrieve-metric-values).
>

Para aceder às suas métricas, pode vê-las no portal Azure como descrito anteriormente neste artigo, e também pode acessá-las através da [API REST Métricas do Monitor Azure](../azure-monitor/platform/stream-monitoring-data-event-hubs.md).

> [!NOTE]
> Se alterar as contas de armazenamento, os dados da conta de armazenamento previamente configurado permanecem disponíveis para download, mas não são apresentados no portal Azure.  
> 

## <a name="available-metrics-and-reporting-intervals"></a>Métricas disponíveis e intervalos de reporte

As métricas de cache são relatadas usando vários intervalos de reporte, incluindo **hora passada**, **hoje,** **semana passada**, e **costume**. A lâmina **métrica** para cada gráfico de métricas apresenta os valores médios, mínimos e máximos para cada métrica no gráfico, e algumas métricas apresentam um total para o intervalo de reporte. 

Cada métrica inclui duas versões. Uma métrica mede o desempenho de toda a cache, e para caches `(Shard 0-9)` que usam [agrupamentos](cache-how-to-premium-clustering.md), uma segunda versão da métrica que inclui no nome mede o desempenho de um único fragmento em uma cache. Por exemplo, se uma cache `Cache Hits` tem quatro fragmentos, é o `Cache Hits (Shard 3)` número total de acessos para toda a cache, e são apenas os sucessos para aquele fragmento da cache.

> [!NOTE]
> Mesmo quando a cache está inativa sem aplicações ativas ligadas do cliente, pode ver alguma atividade de cache, como clientes conectados, uso de memória e operações a serem realizadas. Esta atividade é normal durante o funcionamento de um Cache Azure para redis.
> 
> 

| Métrica | Descrição |
| --- | --- |
| Cache Hits |O número de principais descuidos bem sucedidos durante o intervalo de reporte especificado. Este número `keyspace_hits` mapeia para o comando Redis [INFO.](https://redis.io/commands/info) |
| Latência cache (pré-visualização) | A latência da cache calculada com base na latência interna da cache. Esta métrica é medida em microsegundos `Avg`e `Min`tem `Max`três dimensões: e , que representam a latência média, mínima e máxima da cache, respectivamente durante o intervalo de reporte especificado. |
| Cache Misses |O número de principais descuidos falhados durante o intervalo de reporte especificado. Este número `keyspace_misses` mapeia para o comando Redis INFO. Cache falha não significa necessariamente que há um problema com a cache. Por exemplo, ao utilizar o padrão de programação à parte da cache, uma aplicação olha primeiro para a cache para um item. Se o item não estiver lá (cache miss), o item é recuperado da base de dados e adicionado à cache para a próxima vez. Falhas de cache são comportamento normal para o padrão de programação à parte da cache. Se o número de cache saem mais do que o esperado, examine a lógica de aplicação que povoa e lê a partir da cache. Se os itens estiverem a ser despejados da cache devido à pressão da memória, então pode `Used Memory` haver `Evicted Keys`algumas falhas de cache, mas uma métrica melhor para monitorizar a pressão da memória seria ou . |
| Leitura de cache |A quantidade de dados lidos a partir da cache em Megabytes por segundo (MB/s) durante o intervalo de reporte especificado. Este valor é derivado dos cartões de interface de rede que suportam a máquina virtual que acolhe a cache e não é específico redis. **Este valor corresponde à largura de banda da rede utilizada por esta cache. Se pretender configurar alertas para os limites de largura de `Cache Read` banda da rede do lado do servidor, crie-os utilizando este contador. Consulte [esta tabela](cache-faq.md#cache-performance) para obter os limites de largura de banda observados para vários níveis e tamanhos de preços de cache.** |
| Cache Write |A quantidade de dados escritos à cache em Megabytes por segundo (MB/s) durante o intervalo de reporte especificado. Este valor é derivado dos cartões de interface de rede que suportam a máquina virtual que acolhe a cache e não é específico redis. Este valor corresponde à largura de banda da rede de dados enviados para a cache do cliente. |
| Clientes Ligados |O número de ligações do cliente à cache durante o intervalo de reporte especificado. Este número `connected_clients` mapeia para o comando Redis INFO. Uma vez atingido o limite de [ligação,](cache-configure.md#default-redis-server-configuration) as tentativas de ligação subsequentes à cache falharão. Mesmo que não existam aplicações ativas do cliente, ainda pode haver alguns casos de clientes conectados devido a processos internos e conexões. |
| CPU |A utilização do CPU do Azure Cache para o servidor Redis em percentagem durante o intervalo de reporte especificado. Este valor mapeia para o contador de desempenho do sistema `\Processor(_Total)\% Processor Time` operativo. |
| Erros | Falhas específicas e problemas de desempenho que a cache pode estar experimentando durante um intervalo de reporte especificado. Esta métrica tem oito dimensões que representam diferentes tipos de erros, mas poderia ter sido mais adicionada no futuro. Os tipos de erro representados agora são os seguintes: <br/><ul><li>**Failover** – quando uma cache falha (subordinada promove a mestre)</li><li>**Dataloss** – quando há perda de dados na cache</li><li>**Clientes sem resposta** – quando os clientes não estão a ler dados do servidor suficientemente rápido</li><li>**AOF** – quando há um problema relacionado com a persistência da AOF</li><li>**RDB** – quando há um problema relacionado com a persistência do RDB</li><li>**Importação** – quando existe uma questão relacionada com a importação de RDB</li><li>**Exportação** – quando há uma questão relacionada com a exportação de RDB</li></ul> |
| Chaves despejadas |O número de artigos despejados da cache durante o `maxmemory` intervalo de reporte especificado devido ao limite. Este número `evicted_keys` mapeia para o comando Redis INFO. |
| Chaves expiradas |O número de itens expirou a partir da cache durante o intervalo de reporte especificado. Este valor `expired_keys` mapeia para o comando Redis INFO.|
| Fica |O número de operações de obter a partir da cache durante o intervalo de reporte especificado. Este valor é a soma dos seguintes valores `cmdstat_get`do `cmdstat_hget` `cmdstat_hgetall`Redis INFO todos os comandos: `cmdstat_hmget`, , , `cmdstat_mget`, `cmdstat_getbit`e , e `cmdstat_getrange`é equivalente à soma de cache hits e falhas durante o intervalo de reporte. |
| Operações por Segundo | O número total de comandos processados por segundo pelo servidor cache durante o intervalo de reporte especificado.  Este valor mapeia para "instantaneous_ops_per_sec" do comando Redis INFO. |
| Carga do servidor Redis |A percentagem de ciclos em que o servidor Redis está ocupado a processar e não está à espera de mensagens inativas. Se este contador chegar aos 100, significa que o servidor Redis atingiu um teto de desempenho e o CPU não pode processar o trabalho mais rápido. Se estiver a ver uma carga de servidor redis alta, então verá exceções no tempo de saída no cliente. Neste caso, deve considerar a escala ou a divisão dos seus dados em vários caches. |
| Conjuntos |O número de operações definidas para a cache durante o intervalo de reporte especificado. Este valor é a soma dos seguintes valores `cmdstat_set`da `cmdstat_hset` `cmdstat_hmset`Redis INFO `cmdstat_setbit`todos `cmdstat_setex` `cmdstat_setrange`os `cmdstat_setnx`comandos: , `cmdstat_hsetnx`, `cmdstat_lset`, `cmdstat_mset`, , `cmdstat_msetnx`, , , , e . |
| Chaves totais  | O número máximo de teclas na cache durante o período de reporte anterior. Este número `keyspace` mapeia para o comando Redis INFO. Devido a uma limitação do sistema de métricas subjacentes, para caches com clustering ativado, a Total Keys devolve o número máximo de teclas do fragmento que tinham o número máximo de teclas durante o intervalo de reporte.  |
| Total de Operações |O número total de comandos processados pelo servidor cache durante o intervalo de reporte especificado. Este valor `total_commands_processed` mapeia para o comando Redis INFO. Quando o Azure Cache for Redis for utilizado exclusivamente `Cache Hits`para `Cache Misses` `Gets`pub/sub, não haverá métricas para, ou `Sets`, mas haverá `Total Operations` métricas que refletem o uso da cache para operações de pub/sub. |
| Memória Usada |A quantidade de memória de cache utilizada para os pares chave/valor na cache em MB durante o intervalo de reporte especificado. Este valor `used_memory` mapeia para o comando Redis INFO. Este valor não inclui metadados ou fragmentação. |
| Percentagem de memória usada | A % da memória total que está a ser utilizada durante o intervalo de reporte especificado.  Este valor refere `used_memory` o valor do comando Redis INFO para calcular a percentagem. |
| RSS de memória usada |A quantidade de memória de cache utilizada em MB durante o intervalo de reporte especificado, incluindo fragmentação e metadados. Este valor `used_memory_rss` mapeia para o comando Redis INFO. |

<a name="operations-and-alerts"></a>
## <a name="alerts"></a>Alertas

Pode configurar a receção de alertas com base em métricas e registos de atividades. O Azure Monitor permite-lhe configurar um alerta para fazer o seguinte quando é acionado:

* Enviar uma notificação por e-mail
* Chamar um webhook
* Invocar uma Aplicação Lógica do Azure

Para configurar as regras de alerta para a sua cache, clique em regras de **alerta** a partir do **menu Recurso**.

![Monitorização](./media/cache-how-to-monitor/redis-cache-monitoring.png)

Para mais informações sobre configurar e utilizar Alertas, consulte a [visão geral dos Alertas](../monitoring-and-diagnostics/insights-alerts-portal.md).

## <a name="activity-logs"></a>Registos de Atividade
Os registos de atividade fornecem informações sobre as operações que foram realizadas no seu Cache Azure para os casos Redis. Anteriormente era conhecido como "registos de auditoria" ou "registos operacionais". Utilizando registos de atividade, pode determinar o "o quê, quem e quando" para quaisquer operações de escrita (PUT, POST, DELETE) tomadas no seu Cache Azure para casos Redis. 

> [!NOTE]
> Os registos de atividade não incluem operações de leitura (GET).
>

Para visualizar os registos de atividade para a sua cache, clique nos **registos** de atividade do **menu Recurso**.

Para mais informações sobre os registos de Atividade, consulte a [visão geral do Registo de Atividades do Azure](../azure-monitor/platform/platform-logs-overview.md).
