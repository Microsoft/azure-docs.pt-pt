---
title: Como monitorizar o Azure Cache de Redis | Documentos da Microsoft
description: Saiba como monitorizar o estado de funcionamento e o desempenho de seu Cache do Azure para instâncias de Redis
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 7e70b153-9c87-4290-85af-2228f31df118
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: yegu
ms.openlocfilehash: f41c14f664df8db809ad4c29c57d37c1124a0ff3
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56818549"
---
# <a name="how-to-monitor-azure-cache-for-redis"></a>Como monitorizar a Cache do Azure para Redis
A Cache do Azure para utilizações de Redis [do Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) para fornecer várias opções para a monitorização de instâncias de cache. Pode ver métricas, afixar gráficos de métricas ao Startboard, personalizar o intervalo de data e hora dos gráficos de monitorização, adicionar e remover as métricas de gráficos e definir alertas quando forem cumpridas determinadas condições. Essas ferramentas permitem-lhe monitorizar o estado de funcionamento da Cache do Azure para instâncias de Redis e ajudarão a gerenciar seus aplicativos de colocação em cache.

Métricas de Cache do Azure para instâncias de Redis são recolhidas a utilizar o Redis [INFO](https://redis.io/commands/info) comando aproximadamente duas vezes por minuto e automaticamente armazenadas durante 30 dias (consulte [exportar métricas da cache](#export-cache-metrics) para configurar um política de retenção diferentes) para que possam ser apresentados nos gráficos de métricas e avaliadas pelas regras de alerta. Para obter mais informações sobre os diferentes valores de informações utilizadas para cada métrica de cache, consulte [métricas disponíveis e intervalos de geração de relatórios](#available-metrics-and-reporting-intervals).

<a name="view-cache-metrics"></a>

Para ver métricas da cache, [navegue](cache-configure.md#configure-azure-cache-for-redis-settings) à sua instância de cache no [portal do Azure](https://portal.azure.com).  A Cache de Redis do Azure fornece alguns dos gráficos internos do sobre o **descrição geral** painel e o **métricas de Redis** painel. Cada gráfico pode ser personalizado ao adicionar ou remover as métricas e alterar o intervalo de relatórios.

![Métricas de Redis](./media/cache-how-to-monitor/redis-cache-redis-metrics-blade.png)

## <a name="view-pre-configured-metrics-charts"></a>Ver gráficos de métricas de pré-configurado

O **descrição geral** painel tem as seguintes pré-configurado a monitorização de gráficos.

* [Gráficos de monitorização](#monitoring-charts)
* [Gráficos de utilização](#usage-charts)

### <a name="monitoring-charts"></a>Gráficos de monitorização
O **monitorização** secção a **descrição geral** painel tem **atinge e erros de**, **obtém e define**, **ligações**, e **Total de comandos** gráficos.

![Gráficos de monitorização](./media/cache-how-to-monitor/redis-cache-monitoring-part.png)

### <a name="usage-charts"></a>Gráficos de utilização
O **utilização** secção a **descrição geral** painel tem **carga do servidor Redis**, **utilização da memória**, **largura de banda de rede**, e **utilização da CPU** gráficos e também apresenta os **escalão de preço** para a instância da cache.

![Gráficos de utilização](./media/cache-how-to-monitor/redis-cache-usage-part.png)

O **escalão de preço** apresenta os preços da cache de camada e podem ser utilizadas para [dimensionamento](cache-how-to-scale.md) cache para um escalão de preço diferente.

## <a name="view-metrics-with-azure-monitor"></a>Ver métricas com o Azure monitor
Para ver métricas de Redis e criem gráficos personalizados com o Azure Monitor, clique em **métricas** partir a **menu recursos**e personalizar o gráfico com as métricas desejadas, geração de relatórios de intervalo, o tipo de gráfico e muito mais.

![Métricas de Redis](./media/cache-how-to-monitor/redis-cache-monitor.png)

Para obter mais informações sobre como trabalhar com a métrica de utilizar o Azure Monitor, consulte [descrição geral das métricas no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

<a name="how-to-view-metrics-and-customize-chart"></a>
<a name="enable-cache-diagnostics"></a>
## <a name="export-cache-metrics"></a>Exportar métricas da cache
Por predefinição, são métricas de cache no Azure Monitor [armazenadas durante 30 dias](../azure-monitor/platform/data-collection.md#metrics) e, em seguida, eliminados. Para manter as suas métricas de cache durante mais de 30 dias, pode [designar uma conta de armazenamento](../azure-monitor/platform/archive-diagnostic-logs.md) e especifique um **retenção (dias)** política para as suas métricas de cache. 

Para configurar uma conta de armazenamento para as suas métricas de cache:

1. Clique em **diagnóstico** partir a **menu de recursos** no **a Cache de Redis do Azure** painel.
2. Clique em **no**.
3. Verifique **arquivo para uma conta de armazenamento**.
4. Selecione a conta de armazenamento para armazenar as métricas de cache.
5. Verifique os **1 minuto** caixa de verificação e especificar um **retenção (dias)** política. Se não pretender aplicar qualquer política de retenção e manter os dados para sempre, defina **retenção (dias)** ao **0**.
6. Clique em **Guardar**.

![Diagnóstico de redis](./media/cache-how-to-monitor/redis-cache-diagnostics.png)

>[!NOTE]
>Além de arquivamento de suas métricas de cache para o armazenamento, pode também [transmiti-los para um hub de eventos ou enviá-los para registos do Azure Monitor](../azure-monitor/platform/rest-api-walkthrough.md#retrieve-metric-values).
>
>

Para acessar as suas métricas, pode visualizá-las no portal do Azure, conforme descrito anteriormente neste artigo, e também pode acessá-los utilizando o [API de REST do Azure Monitor métricas](../azure-monitor/platform/stream-monitoring-data-event-hubs.md).

> [!NOTE]
> Se alterar contas de armazenamento, os dados na conta de armazenamento configurada anteriormente permanecem disponíveis para download, mas não é apresentada no portal do Azure.  
> 
> 

## <a name="available-metrics-and-reporting-intervals"></a>Métricas disponíveis e intervalos de geração de relatórios
Métricas de cache são relatadas usando-se vários intervalos de geração de relatórios, incluindo **última hora**, **hoje mesmo**, **semana passada**, e **personalizado**. O **métrica** painel para cada gráfico de métricas apresenta os valores de mínimos, máximo e médios para cada uma no gráfico, e algumas métricas de apresentam um total para o intervalo de relatórios. 

Cada métrica inclui duas versões. Uma métrica mede o desempenho para a cache completa e para as caches que utilizam [clustering](cache-how-to-premium-clustering.md), uma segunda versão da métrica que inclui `(Shard 0-9)` no desempenho de medidas de nome de uma única partição horizontal numa cache. Por exemplo, se tiver de uma cache de 4 partições horizontais, o `Cache Hits` é a quantidade total de acertos para todo o cache, e `Cache Hits (Shard 3)` é apenas as visitas para essa partição horizontal da cache.

> [!NOTE]
> Até mesmo quando o cache está ocioso, sem nenhum aplicativo de cliente ligado de Active Directory, poderá ver algumas atividades de cache, como clientes ligados, a utilização de memória e operações que está a ser executadas. Esta atividade é normal durante a operação de uma Cache do Azure para a instância de Redis.
> 
> 

| Métrica | Descrição |
| --- | --- |
| Acertos na Cache |O número de pesquisas de chave com êxito durante o intervalo de relatório especificado. Isso é mapeado para `keyspace_hits` partir do Redis [informações](https://redis.io/commands/info) comando. |
| Latência de cache (pré-visualização) | A latência da cache calculado com base desativar a latência de internós da cache. Esta métrica é medida em microssegundos e tem três dimensões: "Média", "Min" e "Max", que representam a latência média, mínimo e máxima do cache, respectivamente, durante o intervalo de relatório especificado. |
| Falhas na Cache |O número de pesquisas de chave com falhas durante o intervalo de relatório especificado. Isso é mapeado para `keyspace_misses` do comando Redis informações. Erros de cache não significa necessariamente que existe um problema com a cache. Por exemplo, ao usar o padrão cache-aside programação, um aplicativo procura primeiro na cache para um item. Se o item não estiver lá (falha de acerto na cache), o item é obtido a partir da base de dados e adicionado à cache para a próxima vez. Erros de cache são comportamento normal para o padrão cache-aside programação. Se o número de erros de cache é superior ao esperado, examine a lógica de aplicação que preenche e lê a partir da cache. Se os itens estão a ser expulsos da cache devido a pressão de memória, em seguida, pode haver alguns erros de cache, mas seria uma métrica melhor para monitorizar a pressão de memória `Used Memory` ou `Evicted Keys`. |
| Leitura da Cache |A quantidade de dados de leitura da cache em Megabytes por segundo (MB/s) durante o intervalo de relatório especificado. Este valor é derivado de placas de interface de rede que suportam a máquina virtual que aloja o cache e não é específico do Redis. **Este valor corresponde à largura de banda de rede utilizada por esta cache. Se pretender configurar alertas para limites de largura de banda de rede do lado do servidor, em seguida, criá-la utilizando esta `Cache Read` contador. Ver [esta tabela](cache-faq.md#cache-performance) para os limites de largura de banda observados para a cache de várias camadas e tamanhos de preços.** |
| Escrita na Cache |A quantidade de dados escritos na cache em Megabytes por segundo (MB/s) especificado durante o intervalo de relatórios. Este valor é derivado de placas de interface de rede que suportam a máquina virtual que aloja o cache e não é específico do Redis. Este valor corresponde à largura de banda de rede dos dados enviados para a cache do cliente. |
| Clientes Ligados |O número de ligações de cliente para a cache durante o intervalo de relatório especificado. Isso é mapeado para `connected_clients` do comando Redis informações. Uma vez a [limite de ligações](cache-configure.md#default-redis-server-configuration) é a falha de tentativas de ligação subsequentes atingiu aos será cache. Tenha em atenção que, mesmo se não existirem não existem aplicações de cliente do Active Directory, pode ainda haver algumas instâncias de clientes ligados devido aos processos internos e ligações. |
| CPU |A utilização da CPU da Cache do Azure para o servidor Redis como uma percentagem durante o intervalo de relatório especificado. Este valor é mapeado para o sistema operativo `\Processor(_Total)\% Processor Time` contador de desempenho. |
| Erros | Falhas específicas e problemas de desempenho que pode estar com a cache durante um intervalo de relatório especificado. Esta métrica tem oito dimensões que representam os tipos de erro diferente, mas poderia ter mais adicionado no futuro. Os tipos de erro representados agora são os seguintes: <br/><ul><li>**Ativação pós-falha** -se de uma cache faz a ativação pós-falha (promove subordinado a mestre)</li><li>**Falha de sistema** -se a cache inesperadamente falha em qualquer um de nós</li><li>**Correção de perda** – quando houver correção de perda em cache</li><li>**UnresponsiveClients** – quando os clientes são não leitura de dados do servidor rápido o bastante</li><li>**AOF** – quando existe um problema relacionado à persistência AOF</li><li>**RDB** – quando existe um problema relacionado à persistência RDB</li><li>**Importar** – quando existe um problema relacionado à importação RDB</li><li>**Exportar** – quando existe um problema relacionado à exportação da RDB da</li></ul> |
| Chaves Excluídas |O número de itens removidos da cache durante o intervalo de relatórios especificado devido ao `maxmemory` limite. Isso é mapeado para `evicted_keys` do comando Redis informações. |
| Chaves Expiradas |Expirou o número de itens da cache durante o intervalo de relatório especificado. Este valor é mapeado para `expired_keys` do comando Redis informações.|
| Obtém |O número de operações de get do cache durante o intervalo de relatório especificado. Este valor é a soma dos seguintes valores das informações de Redis, todos os comandos: `cmdstat_get`, `cmdstat_hget`, `cmdstat_hgetall`, `cmdstat_hmget`, `cmdstat_mget`, `cmdstat_getbit`, e `cmdstat_getrange`e é equivalente à soma de acertos de cache e falhas durante o intervalo de relatórios. |
| Operações por segundo | O número total de comandos processados por segundo pelo servidor de cache durante o intervalo de relatório especificado.  Este valor é mapeado para "instantaneous_ops_per_sec" do comando Redis informações. |
| Carga do Servidor Redis |A porcentagem de ciclos em que o servidor de Redis está ocupado a processar e não a aguardar inativo para mensagens. Se este contador atingir 100 significa que o servidor Redis atingiu um limite de desempenho e a CPU não é possível processar trabalhe qualquer o mais rápido. Se vir elevada carga do servidor Redis, em seguida, verá exceções de tempo limite no cliente. Neste caso deve considerar aumentar verticalmente ou particionar os dados em várias caches. |
| Conjuntos |O número de operações de conjunto para a cache durante o intervalo de relatório especificado. Este valor é a soma dos seguintes valores das informações de Redis, todos os comandos: `cmdstat_set`, `cmdstat_hset`, `cmdstat_hmset`, `cmdstat_hsetnx`, `cmdstat_lset`, `cmdstat_mset`, `cmdstat_msetnx`, `cmdstat_setbit`, `cmdstat_setex`, `cmdstat_setrange` , e `cmdstat_setnx`. |
| Chaves Totais  | O número máximo de chaves na cache durante o período de tempo de criação de relatórios anterior. Isso é mapeado para `keyspace` do comando Redis informações. Devido a uma limitação do sistema de métricas subjacente, para as caches com o clustering ativado, o Total de chaves devolve o número máximo de chaves de partição horizontal que tinha o número máximo de chaves durante o intervalo de relatórios.  |
| Total de Operações |O número total de comandos, processados pelo servidor de cache durante o intervalo de relatório especificado. Este valor é mapeado para `total_commands_processed` do comando Redis informações. Tenha em atenção que quando a Cache de Redis do Azure é usada unicamente para pub/sub não vai haver nenhum métricas para o `Cache Hits`, `Cache Misses`, `Gets`, ou `Sets`, mas haverá `Total Operations` métricas que refletem a utilização de cache para operações de pub/sub. |
| Memória Utilizada |A quantidade de memória de cache utilizada para pares de chave/valor na cache em MB durante o intervalo de relatório especificado. Este valor é mapeado para `used_memory` do comando Redis informações. Isso não inclui metadados ou fragmentação. |
| Percentagem de memória utilizada | O % do total de memória que está a ser utilizado durante o intervalo de relatório especificado.  Isso referencia o valor de "used_memory" do comando Redis informações para calcular a percentagem. |
| RSS de memória utilizada |A quantidade de memória de cache utilizada em MB durante o intervalo de relatórios especificado, incluindo a fragmentação e metadados. Este valor é mapeado para `used_memory_rss` do comando Redis informações. |

<a name="operations-and-alerts"></a>
## <a name="alerts"></a>Alertas
Pode configurar a receção de alertas com base em métricas e registos de atividades. O Azure Monitor permite-lhe configurar um alerta para fazer o seguinte quando é acionado:

* Enviar uma notificação por e-mail
* Chamar um webhook
* Invocar uma Aplicação Lógica do Azure

Para configurar regras de alerta para a sua cache, clique em **regras de alerta** partir do **menu recursos**.

![Monitorização](./media/cache-how-to-monitor/redis-cache-monitoring.png)

Para obter mais informações sobre como configurar e utilizar os alertas, consulte [descrição geral dos alertas](../monitoring-and-diagnostics/insights-alerts-portal.md).

## <a name="activity-logs"></a>Registos de Atividade
Registos de atividades fornecem informações sobre as operações que foram executadas em seu Cache do Azure para instâncias de Redis. Anteriormente era conhecido como "registos de auditoria" ou "registos operacionais". Utilizar registos de atividades, pode determinar o "o quê, quem e quando" para quaisquer operações (PUT, POST, DELETE) efetuadas na sua Cache do Azure para instâncias de Redis de escrita. 

> [!NOTE]
> Registos de Atividades não incluem operações de leitura (GET).
>
>

Para ver os registos de atividades para a sua cache, clique em **registos de atividades** partir a **menu recursos**.

Para obter mais informações sobre os registos de atividade, consulte [descrição geral do registo de atividades do Azure](../azure-monitor/platform/activity-logs-overview.md).











