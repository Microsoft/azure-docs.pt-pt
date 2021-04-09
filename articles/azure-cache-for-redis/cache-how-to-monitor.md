---
title: Monitor Azure Cache para Redis
description: Saiba como monitorizar a saúde e o desempenho do seu Azure Cache para instâncias Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 0ff11c9601fb55e27d8780185d77c177e9d9201b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100584633"
---
# <a name="monitor-azure-cache-for-redis"></a>Monitor Azure Cache para Redis

Azure Cache para Redis utiliza [o Azure Monitor](../azure-monitor/index.yml) para fornecer várias opções para monitorizar as suas instâncias de cache. Pode ver métricas, gráficos de métricas de pin para o Startboard, personalizar a data e a gama de horários dos gráficos de monitorização, adicionar e remover métricas dos gráficos e definir alertas quando certas condições são satisfeitas. Estas ferramentas permitem-lhe monitorizar a saúde do seu Azure Cache para instâncias Redis e ajudá-lo a gerir as suas aplicações de cache.

As métricas da Cache Azure para instâncias Redis são recolhidas utilizando o comando Redis [INFO](https://redis.io/commands/info) aproximadamente duas vezes por minuto e automaticamente armazenadas durante 30 dias (ver [métricas de cache de exportação](#export-cache-metrics) para configurar uma política de retenção diferente) para que possam ser apresentadas nos gráficos de métricas e avaliadas por regras de alerta. Para obter mais informações sobre os diferentes valores de INFO utilizados para cada métrica de cache, consulte [métricas disponíveis e intervalos de reporte](#available-metrics-and-reporting-intervals).

<a name="view-cache-metrics"></a>

Para ver as métricas de cache, [navegue](cache-configure.md#configure-azure-cache-for-redis-settings) para a sua cache no [portal Azure](https://portal.azure.com).  A azure Cache para Redis fornece alguns gráficos incorporados na lâmina **de visão geral** e na lâmina **de métricas Redis.** Cada gráfico pode ser personalizado adicionando ou removendo métricas e alterando o intervalo de reporte.

![Seis gráficos são mostrados. Um deles é Cache Hits e Cache Misses passado a hora.](./media/cache-how-to-monitor/redis-cache-redis-metrics-blade.png)

## <a name="view-pre-configured-metrics-charts"></a>Ver gráficos de métricas pré-configurados

A **lâmina de visão geral** tem os seguintes gráficos de monitorização pré-configurados.

* [Gráficos de monitorização](#monitoring-charts)
* [Gráficos de utilização](#usage-charts)

### <a name="monitoring-charts"></a>Gráficos de monitorização

A secção **de monitorização** na lâmina de **visão geral** tem **hits e misses,** **gets e sets,** **conexões** e **gráficos de comandos totais.**

![Gráficos de monitorização](./media/cache-how-to-monitor/redis-cache-monitoring-part.png)

### <a name="usage-charts"></a>Gráficos de utilização

A secção **de utilização** na lâmina **de visão geral** tem **redis Server Load**, Memory **Usage**, **Network Bandwidth** e **CPU Use** charts, e também apresenta o **nível de preços** para a instância cache.

![Gráficos de utilização](./media/cache-how-to-monitor/redis-cache-usage-part.png)

O **nível de preços** exibe o nível de preços da cache, e pode ser usado para [escalar](cache-how-to-scale.md) a cache para um nível de preços diferente.

## <a name="view-metrics-with-azure-monitor"></a>Ver métricas com monitor Azure

Para ver as métricas do Redis e criar gráficos personalizados utilizando o Azure Monitor, clique em **Métricas** a partir do **menu De recursos**, e personalize o seu gráfico usando as métricas desejadas, intervalo de reporte, tipo de gráfico e muito mais.

![No painel de navegação à esquerda dos contoso55, a Métrica é uma opção em Monitorização e destaca-se. Nas Métricas há uma lista de métricas. São selecionados os acessos de cache e as falhas da Cache.](./media/cache-how-to-monitor/redis-cache-monitor.png)

Para obter mais informações sobre o trabalho com as métricas utilizando o Azure Monitor, consulte [a visão geral das métricas no Microsoft Azure](../azure-monitor/data-platform.md).

<a name="how-to-view-metrics-and-customize-chart"></a>
<a name="enable-cache-diagnostics"></a>
## <a name="export-cache-metrics"></a>Métricas de cache de exportação

Por predefinição, as métricas de cache no Azure Monitor são [armazenadas durante 30 dias](../azure-monitor/essentials/data-platform-metrics.md) e depois eliminadas. Para persistir as suas métricas de cache por mais de 30 dias, pode [designar uma conta de armazenamento](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) e especificar uma política de **retenção (dias)** para as suas métricas de cache. 

Para configurar uma conta de armazenamento para as suas métricas de cache:

1. Na **cache Azure para redis,** no título **de monitorização,** selecione **Diagnósticos**.
2. **Selecione + Adicione a definição de diagnóstico**.
3. Diga as definições.
4. Verifique **o Arquivo numa conta de armazenamento.** Serão cobradas taxas normais de dados para armazenamento e transações quando enviar diagnósticos para uma conta de armazenamento.
4. Selecione **Configurar** para escolher a conta de armazenamento na qual guarde as métricas de cache.
5. Sob a **métrica** da cabeça da mesa, verifique a caixa ao lado dos itens de linha que pretende armazenar, como **a AllMetrics**. Especificar uma política **de retenção (dias).** A retenção máxima de dias que pode especificar é **de 365 dias.** No entanto, se quiser reter os dados das métricas para sempre, desembarate **a Retenção (dias)** para **0**.
6. Clique em **Guardar**.


![Diagnósticos de Redis](./media/cache-how-to-monitor/redis-cache-diagnostics.png)

>[!NOTE]
>Além de arquivar as suas métricas de cache para armazenamento, também pode [transmiti-las para um hub de Eventos ou enviá-las para registos do Azure Monitor](../azure-monitor/essentials/rest-api-walkthrough.md#retrieve-metric-values).
>

Para aceder às suas métricas, pode vê-las no portal Azure, como descrito anteriormente neste artigo, e também pode acessá-las utilizando a API de [Métricas do Monitor Azure.](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md)

> [!NOTE]
> Se alterar as contas de armazenamento, os dados na conta de armazenamento previamente configurada permanecem disponíveis para download, mas não são apresentados no portal Azure.  
> 

## <a name="available-metrics-and-reporting-intervals"></a>Métricas disponíveis e intervalos de reporte

As métricas de cache são reportadas usando vários intervalos de reporte, incluindo **última hora**, **hoje,** **semana passada**, e **personalizado**. A lâmina **métrica** de cada gráfico de métricas apresenta os valores médios, mínimos e máximos para cada métrica no gráfico, e algumas métricas apresentam um total para o intervalo de reporte. 

Cada métrica inclui duas versões. Uma métrica mede o desempenho de toda a cache, e para caches que usam [clustering](cache-how-to-premium-clustering.md), uma segunda versão da métrica que inclui `(Shard 0-9)` no nome mede o desempenho para um único fragmento em uma cache. Por exemplo, se uma cache tem quatro fragmentos, `Cache Hits` é o número total de acessos para toda a cache, e é `Cache Hits (Shard 3)` apenas o sucesso para o fragmento da cache.

> [!NOTE]
> Mesmo quando a cache está inativa sem aplicações de clientes ativos conectadas, poderá ver alguma atividade de cache, como clientes conectados, uso de memória e operações a serem realizadas. Esta atividade é normal durante o funcionamento de uma Cache Azure para a instância Redis.
> 
> 

| Metric | Descrição |
| --- | --- |
| Acertos na Cache |O número de exames de chave bem sucedidos durante o intervalo de reporte especificado. Este número mapeia para `keyspace_hits` a partir do comando REDIS [INFO.](https://redis.io/commands/info) |
| Latência cache (pré-visualização) | A latência da cache calculada com base na latência internada da cache. Esta métrica é medida em microsegundos, e tem três dimensões: `Avg` , e , que representam a `Min` `Max` média, mínima e a latência máxima da cache, respectivamente durante o intervalo de reporte especificado. |
| Falhas de Acerto na Cache |O número de exames de chave falhados durante o intervalo de reporte especificado. Este número mapeia para `keyspace_misses` a partir do comando REDIS INFO. Cache falha não significa necessariamente que há um problema com a cache. Por exemplo, ao utilizar o padrão de programação de cache-aside, uma aplicação fica em primeiro lugar na cache para um item. Se o item não estiver lá (cache falta), o item é recuperado da base de dados e adicionado à cache para a próxima vez. Falhas de cache são um comportamento normal para o padrão de programação de cache-aside. Se o número de falhas de cache for superior ao esperado, examine a lógica de aplicação que povoa e lê a partir da cache. Se os itens estiverem a ser despejados da cache devido à pressão da memória, então pode haver algumas falhas de cache, mas uma métrica melhor para monitorizar a pressão da memória seria `Used Memory` ou `Evicted Keys` . |
| Leitura da Cache |A quantidade de dados lidos a partir da cache em Megabytes por segundo (MB/s) durante o intervalo de reporte especificado. Este valor é derivado dos cartões de interface de rede que suportam a máquina virtual que hospeda a cache e não é específica do Redis. **Este valor corresponde à largura de banda de rede utilizada por esta cache. Se pretender configurar alertas para os limites de largura de banda do lado do servidor, crie-o utilizando este `Cache Read` contador. Consulte [esta tabela](cache-planning-faq.md#azure-cache-for-redis-performance) para ver os limites de largura de banda observados para vários níveis e tamanhos de preços de cache.** |
| Escrita na Cache |A quantidade de dados escritos na cache em Megabytes por segundo (MB/s) durante o intervalo de reporte especificado. Este valor é derivado dos cartões de interface de rede que suportam a máquina virtual que hospeda a cache e não é específica do Redis. Este valor corresponde à largura de banda de rede dos dados enviados para a cache do cliente. |
| Clientes Ligados |O número de ligações do cliente à cache durante o intervalo de reporte especificado. Este número mapeia para `connected_clients` a partir do comando REDIS INFO. Uma vez atingido o [limite de ligação,](cache-configure.md#default-redis-server-configuration) as tentativas de ligação subsequentes à cache falharão. Mesmo que não existam aplicações ativas de clientes, pode ainda haver alguns casos de clientes conectados devido a processos internos e conexões. |
| CPU |A utilização do CPU da Cache Azure para o servidor Redis em percentagem durante o intervalo de reporte especificado. Este valor mapeia para o contador de desempenho do sistema `\Processor(_Total)\% Processor Time` operativo. |
| Erros | Falhas específicas e problemas de desempenho que a cache pode estar a experimentar durante um intervalo de reporte especificado. Esta métrica tem oito dimensões que representam diferentes tipos de erro, mas poderia ter sido adicionada no futuro. Os tipos de erros agora representados são os seguintes: <br/><ul><li>**Failover** – quando uma cache falha (subordinado promove ao primário)</li><li>**Dataloss** – quando há perda de dados na cache</li><li>**Sem RespostaSAtenção** – quando os clientes não estão a ler dados do servidor suficientemente rápido</li><li>**AOF** – quando há um problema relacionado com a persistência da AOF</li><li>**RDB** – quando há um problema relacionado com a persistência do RDB</li><li>**Importação** – quando há uma questão relacionada com a Importação RDB</li><li>**Exportação** – quando há uma questão relacionada com a Exportação de RDB</li></ul> |
| Chaves Excluídas |O número de artigos despejados da cache durante o intervalo de reporte especificado devido ao `maxmemory` limite. Este número mapeia para `evicted_keys` a partir do comando REDIS INFO. |
| Chaves Expiradas |O número de itens expirou a partir da cache durante o intervalo de reporte especificado. Este valor mapeia para `expired_keys` a partir do comando REDIS INFO.|
| Obtenções |O número de operações de obtém da cache durante o intervalo de reporte especificado. Este valor é a soma dos seguintes valores do Redis INFO todos os `cmdstat_get` `cmdstat_hget` `cmdstat_hgetall` comandos: , , , , e é equivalente à soma de cache hits e `cmdstat_hmget` `cmdstat_mget` `cmdstat_getbit` `cmdstat_getrange` misses durante o intervalo de reporte. |
| Operações por Segundo | O número total de comandos processados por segundo pelo servidor cache durante o intervalo de reporte especificado.  Este valor mapeia para "instantaneous_ops_per_sec" a partir do comando Redis INFO. |
| Carga do servidor Redis |A percentagem de ciclos em que o servidor Redis está ocupado a processar e não está à espera de mensagens inativas. Se este contador chegar aos 100, significa que o servidor Redis atingiu um teto de desempenho e o CPU não pode processar o trabalho mais rápido. Se estiver a ver uma carga elevada do Redis Server, verá exceções no tempo limite no cliente. Neste caso, deve considerar aumentar ou dividir os seus dados em várias caches. |
| Conjuntos |O número de operações definidas na cache durante o intervalo de reporte especificado. Este valor é a soma dos seguintes valores do Redis INFO todos os comandos: `cmdstat_set` , , , , , `cmdstat_hset` e `cmdstat_hmset` `cmdstat_hsetnx` `cmdstat_lset` `cmdstat_mset` `cmdstat_msetnx` `cmdstat_setbit` `cmdstat_setex` `cmdstat_setrange` `cmdstat_setnx` . |
| Chaves totais  | O número máximo de chaves na cache durante o período de tempo de reporte anterior. Este número mapeia para `keyspace` a partir do comando REDIS INFO. Devido a uma limitação do sistema de métricas subjacente, para caches com agrupamento ativado, a Total Keys devolve o número máximo de chaves do fragmento que tinha o número máximo de chaves durante o intervalo de reporte.  |
| Total de Operações |O número total de comandos processados pelo servidor cache durante o intervalo de reporte especificado. Este valor mapeia para `total_commands_processed` a partir do comando REDIS INFO. Quando a Cache Azure para Redis é utilizada exclusivamente para pub/sub, não haverá métricas para `Cache Hits` , , ou , mas `Cache Misses` `Gets` `Sets` haverá `Total Operations` métricas que refletem o uso da cache para pub/sub operações. |
| Memória Utilizada |A quantidade de memória de cache utilizada para os pares chave/valor na cache em MB durante o intervalo de reporte especificado. Este valor mapeia para `used_memory` a partir do comando REDIS INFO. Este valor não inclui metadados ou fragmentação. |
| Percentagem de Memória Utilizada | A % da memória total que está a ser utilizada durante o intervalo de reporte especificado.  Este valor refere o `used_memory` valor do comando REDIS INFO para calcular a percentagem. |
| RSS de memória usada |A quantidade de memória de cache utilizada em MB durante o intervalo de reporte especificado, incluindo fragmentação e metadados. Este valor mapeia para `used_memory_rss` a partir do comando REDIS INFO. |

<a name="operations-and-alerts"></a>
## <a name="alerts"></a>Alertas

Pode configurar a receção de alertas com base em métricas e registos de atividades. O Azure Monitor permite-lhe configurar um alerta para fazer o seguinte quando é acionado:

* Enviar uma notificação por e-mail
* Chamar um webhook
* Invocar uma Aplicação Lógica do Azure

Para configurar as regras de alerta para o seu cache, clique nas **regras de alerta** a partir do **menu Recursos**.

![Monitorização](./media/cache-how-to-monitor/redis-cache-monitoring.png)

Para obter mais informações sobre configuração e utilização de Alertas, consulte [a visão geral dos alertas.](../azure-monitor/alerts/alerts-classic-portal.md)

## <a name="activity-logs"></a>Registos de Atividade
Os registos de atividade fornecem informações sobre as operações que foram realizadas na sua Cache Azure para instâncias Redis. Anteriormente era conhecido como "registos de auditoria" ou "registos operacionais". Utilizando registos de atividade, pode determinar o "o quê, quem e quando" para quaisquer operações de escrita (PUT, POST, DELETE) tomadas na sua Cache Azure para instâncias Redis. 

> [!NOTE]
> Os registos de atividade não incluem operações de leitura (GET).
>

Para ver os registos de atividade para o seu cache, clique em **registos** de atividade a partir do **menu Recursos.**

Para obter mais informações sobre os registos de atividades, consulte [a visão geral do Registo de Atividades Azure](../azure-monitor/essentials/platform-logs-overview.md).