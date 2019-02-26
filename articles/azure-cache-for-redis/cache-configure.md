---
title: Como configurar o Azure Cache de Redis | Documentos da Microsoft
description: Compreender a configuração de Redis predefinida para a Cache do Azure para Redis e saiba como configurar a sua Cache do Azure para instâncias de Redis
services: cache
documentationcenter: na
author: yegu-ms
manager: jhubbard
editor: tysonn
ms.assetid: d0bf2e1f-6a26-4e62-85ba-d82b35fc5aa6
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 08/22/2017
ms.author: yegu
ms.openlocfilehash: 5e4fc40a9b6cfc945670bcd497d4b13af61f2f2f
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56817767"
---
# <a name="how-to-configure-azure-cache-for-redis"></a>Como configurar a Cache do Azure para Redis
Este tópico descreve as configurações disponíveis para a sua Cache do Azure para instâncias de Redis. Este tópico também inclui a configuração de servidor da Redis predefinida, para a Cache do Azure para instâncias de Redis.

> [!NOTE]
> Para obter mais informações sobre como configurar e utilizar as funcionalidades de cache premium, consulte [como configurar a persistência](cache-how-to-premium-persistence.md), [como configurar o clustering](cache-how-to-premium-clustering.md), e [como configurar o suporte de rede Virtual ](cache-how-to-premium-vnet.md).
> 
> 

## <a name="configure-azure-cache-for-redis-settings"></a>Configurar a Cache do Azure para as definições de Redis
[!INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

A Cache do Azure para as definições do Redis são exibidas e configurado no **a Cache de Redis do Azure** através do painel a **Menu recursos**.

![Cache do Azure para as definições de Redis](./media/cache-configure/redis-cache-settings.png)

Pode ver e configurar as seguintes definições utilizando a **Menu de recursos**.

* [Descrição geral](#overview)
* [Registo de atividades](#activity-log)
* [Controlo de acesso (IAM)](#access-control-iam)
* [Etiquetas](#tags)
* [Diagnosticar e resolver problemas](#diagnose-and-solve-problems)
* [Definições](#settings)
    * [Chaves de acesso](#access-keys)
    * [Definições avançadas](#advanced-settings)
    * [Cache do Azure para o Assistente de Redis](#azure-cache-for-redis-advisor)
    * [Dimensionamento](#scale)
    * [Tamanho do cluster redis](#cluster-size)
    * [Persistência de dados de Redis](#redis-data-persistence)
    * [Atualizações agendadas](#schedule-updates)
    * [Georreplicação](#geo-replication)
    * [Rede Virtual](#virtual-network)
    * [Firewall](#firewall)
    * [Propriedades](#properties)
    * [Bloqueios](#locks)
    * [Script de automação](#automation-script)
* Administração
    * [Importar dados](#importexport)
    * [Exportar dados](#importexport)
    * [Reiniciar](#reboot)
* [Monitorização](#monitoring)
    * [Métricas de redis](#redis-metrics)
    * [Regras de alerta](#alert-rules)
    * [Diagnóstico](#diagnostics)
* Suporte e as definições de resolução de problemas
    * [Estado de funcionamento do recurso](#resource-health)
    * [Novo pedido de suporte](#new-support-request)


## <a name="overview"></a>Descrição geral

**Descrição geral** fornece a com informações básicas sobre a cache, como o nome, as portas, métricas de cache selecionada e escalão de preço.

### <a name="activity-log"></a>Registo de atividades

Clique em **registo de atividades** para ver as ações executadas no seu cache. Também pode utilizar a filtragem para expandir esta vista para incluir outros recursos. Para obter mais informações sobre como trabalhar com registos de auditoria, consulte [auditar operações com o Resource Manager](../azure-resource-manager/resource-group-audit.md). Para obter mais informações sobre como monitorizar a Cache do Azure para eventos de Redis, consulte [operações e alertas](cache-how-to-monitor.md#operations-and-alerts).

### <a name="access-control-iam"></a>Controlo de acesso (IAM)

O **controlo de acesso (IAM)** secção fornece suporte para o controlo de acesso baseado em funções (RBAC) no portal do Azure. Esta configuração ajuda as organizações a cumprir os respetivos requisitos de gestão de acesso simples e com precisão. Para obter mais informações, consulte [controlo de acesso baseado em funções no portal do Azure](../role-based-access-control/role-assignments-portal.md).

### <a name="tags"></a>Etiquetas

O **etiquetas** secção ajuda-o a organizar os recursos. Para obter mais informações, consulte [utilizar etiquetas para organizar os recursos do Azure](../azure-resource-manager/resource-group-using-tags.md).


### <a name="diagnose-and-solve-problems"></a>Diagnosticar e resolver problemas

Clique em **diagnosticar e resolver problemas** para ser fornecido com problemas comuns e estratégias para solucioná-los.



## <a name="settings"></a>Definições
O **definições** secção permite-lhe aceder e configure as seguintes definições para a sua cache.

* [Chaves de acesso](#access-keys)
* [Definições avançadas](#advanced-settings)
* [Cache do Azure para o Assistente de Redis](#azure-cache-for-redis-advisor)
* [Dimensionamento](#scale)
* [Tamanho do cluster redis](#cluster-size)
* [Persistência de dados de Redis](#redis-data-persistence)
* [Atualizações agendadas](#schedule-updates)
* [Georreplicação](#geo-replication)
* [Rede Virtual](#virtual-network)
* [Firewall](#firewall)
* [Propriedades](#properties)
* [Bloqueios](#locks)
* [Script de automação](#automation-script)



### <a name="access-keys"></a>Chaves de acesso
Clique em **chaves de acesso** para ver ou regenerar as chaves de acesso para a sua cache. Estas chaves são utilizadas pelos clientes do que se liga à sua cache.

![Cache do Azure para as chaves de acesso de Redis](./media/cache-configure/redis-cache-manage-keys.png)

### <a name="advanced-settings"></a>Definições avançadas
As seguintes definições são configuradas no **definições avançadas** painel.

* [Portas de acesso](#access-ports)
* [Políticas de memória](#memory-policies)
* [Notificações Keyspace (definições avançadas)](#keyspace-notifications-advanced-settings)

#### <a name="access-ports"></a>Portas de Acesso
Por predefinição, o acesso não SSL está desativado para as novas caches. Para ativar a porta não SSL, clique em **não** para **permitir acesso apenas via SSL** sobre o **definições avançadas** painel e clique em **guardar**.

> [!NOTE]
> Acesso SSL para a Cache do Azure para o Redis suporta o TLS 1.0 por predefinição. A versão mínima suportada do TLS pode ser aumentada até TLS 1.2, se assim o desejar, utilizando o **versão do TLS mínima** menu pendente a **definições avançadas** painel e clique em **guardar**.

![Cache do Azure para portas de acesso de Redis](./media/cache-configure/redis-cache-access-ports.png)

<a name="maxmemory-policy-and-maxmemory-reserved"></a>
#### <a name="memory-policies"></a>Políticas de memória
O **política de Maxmemory**, **reservados maxmemory**, e **reservados maxfragmentationmemory** definições no **definições avançadas** painel Configurar as políticas de memória para a cache.

![Cache do Azure para a política de Maxmemory de Redis](./media/cache-configure/redis-cache-maxmemory-policy.png)

**Política de Maxmemory** configura a política de expulsão da cache e permite-lhe escolher entre as seguintes políticas de expulsão:

* `volatile-lru` -Essa é a política de expulsão de predefinição.
* `allkeys-lru`
* `volatile-random`
* `allkeys-random`
* `volatile-ttl`
* `noeviction`

Para obter mais informações sobre `maxmemory` políticas, consulte [políticas de remoção](https://redis.io/topics/lru-cache#eviction-policies).

O **reservados maxmemory** definição configura a quantidade de memória, em MB, o que está reservado para operações de fora de cache, como replicação durante a ativação pós-falha. A definição deste valor permite-lhe ter uma experiência mais consistente de servidor Redis, quando a carga varia. Este valor deve ser definido como superior para cargas de trabalho que são pesadas de escrita. Quando a memória é reservada para essas operações, ele não está disponível para armazenamento de dados em cache.

O **reservados maxfragmentationmemory** definição configura a quantidade de memória em MB que está reservado para se prepararem para a fragmentação de memória. Definir este valor permite que tenha uma experiência mais consistente de servidor Redis, quando o cache estiver cheia ou próximas completo e a fragmentação a proporção é elevada. Quando a memória é reservada para essas operações, ele não está disponível para armazenamento de dados em cache.

Uma coisa a considerar ao escolher um novo valor de reserva de memória (**reservados maxmemory** ou **reservados maxfragmentationmemory**) é a forma como esta alteração pode afetar um cache que já está em execução com grandes quantidades de dados no mesmo. Por exemplo, se tenha uma cache de 53 GB com 49 GB de dados, em seguida, altere o valor de reserva para 8 GB, esta alteração irá diminuir a memória máxima disponível para o sistema para baixo até 45 GB. Se qualquer um dos seus atual `used_memory` ou o seu `used_memory_rss` valores são maiores que o novo limite de 45 GB, em seguida, o sistema terá de expulsar dados até que ambas `used_memory` e `used_memory_rss` estão abaixo 45 GB. Expulsão pode aumentar a fragmentação de memória e de carga do servidor. Para obter mais informações sobre as métricas de cache, como `used_memory` e `used_memory_rss`, consulte [métricas disponíveis e intervalos de geração de relatórios](cache-how-to-monitor.md#available-metrics-and-reporting-intervals).

> [!IMPORTANT]
> O **reservados maxmemory** e **reservados maxfragmentationmemory** definições só estão disponíveis para o Standard e Premium coloca em cache.
> 
> 

#### <a name="keyspace-notifications-advanced-settings"></a>Notificações Keyspace (definições avançadas)
Redis keyspace notificações estão configuradas no **definições avançadas** painel. As notificações Keyspace permitem aos clientes receber notificações quando ocorrem determinados eventos.

![Definições avançadas do que a Cache de Redis do Azure](./media/cache-configure/redis-cache-advanced-settings.png)

> [!IMPORTANT]
> Notificações Keyspace e a **notificar-keyspace-eventos** definição só estão disponíveis para as caches Standard e Premium.
> 
> 

Para obter mais informações, consulte [Redis notificações de Keyspace](https://redis.io/topics/notifications). Para o código de exemplo, consulte a [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) de ficheiros a [Olá, mundo](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) exemplo.


<a name="recommendations"></a>
## <a name="azure-cache-for-redis-advisor"></a>Cache do Azure para o Assistente de Redis
O **Cache do Azure para o Assistente de Redis** painel apresenta recomendações para a sua cache. Durante as operações normais, não existem recomendações são apresentadas. 

![Recomendações](./media/cache-configure/redis-cache-no-recommendations.png)

Se ocorrerem quaisquer condições durante as operações da cache, como utilização elevada da memória, largura de banda de rede ou de carga do servidor, é apresentado um alerta no **a Cache de Redis do Azure** painel.

![Recomendações](./media/cache-configure/redis-cache-recommendations-alert.png)

Informações adicionais podem ser encontradas no **recomendações** painel.

![Recomendações](./media/cache-configure/redis-cache-recommendations.png)

Pode monitorizar estas métricas na [gráficos de monitorização](cache-how-to-monitor.md#monitoring-charts) e [gráficos de utilização](cache-how-to-monitor.md#usage-charts) seções do **a Cache de Redis do Azure** painel.

Cada escalão de preço tem limites diferentes para ligações de cliente, memória e largura de banda. Se a sua cache se aproxima do capacidade máxima para estas métricas através de um período de tempo prolongado, é criada uma recomendação. Para obter mais informações sobre as métricas e os limites revistos pela **recomendações** ferramenta, consulte a tabela seguinte:

| Cache do Azure para a métrica de Redis | Mais informações |
| --- | --- |
| Utilização de largura de banda de rede |[Desempenho de cache - largura de banda disponível](cache-faq.md#cache-performance) |
| Clientes ligados |[Configuração do servidor predefinido Redis - maxclients](#maxclients) |
| Carga do servidor |[Gráficos de utilização - carga do servidor Redis](cache-how-to-monitor.md#usage-charts) |
| Utilização de memória |[Desempenho de cache - tamanho](cache-faq.md#cache-performance) |

Para atualizar a cache, clique em **atualizar agora** para alterar o escalão de preço e [dimensionamento](#scale) seu cache. Para obter mais informações sobre como escolher um escalão de preço, consulte [que a Cache do Azure para o tamanho e oferta de Redis devo utilizar?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)


### <a name="scale"></a>Escala
Clique em **dimensionamento** para ver ou alterar o escalão de preço para a sua cache. Para obter mais informações sobre o dimensionamento, veja [como dimensionar a cache do Azure para Redis](cache-how-to-scale.md).

![Cache do Azure para o escalão de preço de Redis](./media/cache-configure/pricing-tier.png)

<a name="cluster-size"></a>

### <a name="redis-cluster-size"></a>Tamanho do Cluster redis
Clique em **tamanho de Cluster de Redis (pré-visualização)** para alterar o tamanho de cluster para uma execução cache premium com clustering ativado.

> [!NOTE]
> Tenha em atenção que, enquanto a Cache do Azure para o escalão Premium de Redis foi lançada para disponibilidade geral, a funcionalidade de tamanho de Cluster de Redis está atualmente em pré-visualização.
> 
> 

![Tamanho do cluster redis](./media/cache-configure/redis-cache-redis-cluster-size.png)

Para alterar o tamanho do cluster, utilize o controlo de deslize ou insira um número entre 1 e 10 a **contagem de partições horizontais** caixa de texto e clique em **OK** para guardar.

> [!IMPORTANT]
> O clustering de redis só está disponível para as Premium caches. Para obter mais informações, consulte [como configurar o clustering para uma Cache do Azure Premium para Redis](cache-how-to-premium-clustering.md).
> 
> 


### <a name="redis-data-persistence"></a>Persistência de dados de Redis
Clique em **persistência de dados de Redis** para ativar, desativar ou configurar a persistência de dados para a sua cache premium. A Cache de Redis do Azure oferece a persistência de Redis através de [persistência RDB](cache-how-to-premium-persistence.md#configure-rdb-persistence) ou [persistência AOF](cache-how-to-premium-persistence.md#configure-aof-persistence).

Para obter mais informações, consulte [como configurar a persistência para uma Cache do Azure Premium para Redis](cache-how-to-premium-persistence.md).


> [!IMPORTANT]
> Persistência de dados de redis só está disponível para as Premium caches.
> 
> 

### <a name="schedule-updates"></a>Agendar atualizações
O **agendar atualizações** painel permite-lhe designar uma janela de manutenção para atualizações do servidor para a sua cache de Redis. 

> [!IMPORTANT]
> A janela de manutenção só se aplica a Redis atualizações do servidor e não para qualquer serviço de atualizações ou atualiza para o sistema operativo das VMs que alojam a cache.
> 
> 

![Agendar atualizações](./media/cache-configure/redis-schedule-updates.png)

Para especificar uma janela de manutenção, verifique os dias desejados e especifique a hora de início da janela de manutenção para cada dia e clique em **OK**. O tempo de janela de manutenção é em UTC.

> [!IMPORTANT]
> O **agendar atualizações** funcionalidade só está disponível para as caches de escalão Premium. Para obter mais informações e instruções, consulte [Cache do Azure para a administração de Redis - agendar atualizações](cache-administration.md#schedule-updates).
> 
> 

### <a name="geo-replication"></a>Georreplicação

O **georreplicação** painel fornece um mecanismo para ligar duas escalão Premium da Cache do Azure para instâncias de Redis. Um cache é designado como o cache ligada primário e o outro como o cache secundário ligada. Torna-se a cache de ligado secundária só de leitura e escritos na cache primária de dados são replicados para o cache secundário ligada. Esta funcionalidade pode ser utilizada para replicar uma cache entre regiões do Azure.

> [!IMPORTANT]
> **Replicação geográfica** só está disponível para as caches de escalão Premium. Para obter mais informações e instruções, consulte [como configurar a georreplicação para a Cache do Azure para Redis](cache-how-to-geo-replication.md).
> 
> 

### <a name="virtual-network"></a>Rede Virtual
O **rede Virtual** secção permite-lhe configurar as definições de rede virtual para a sua cache. Para obter informações sobre como criar uma cache premium com VNET suporte e atualizar as definições, consulte [como configurar o suporte de rede Virtual para uma Cache do Azure Premium para Redis](cache-how-to-premium-vnet.md).

> [!IMPORTANT]
> Definições de rede virtual só estão disponíveis para as caches premium que foram configuradas com suporte VNET durante a criação da cache. 
> 
> 

### <a name="firewall"></a>Firewall

Configuração de regras de firewall está disponível para todo o Cache do Azure para os escalões de Redis.

Clique em **Firewall** para ver e configurar regras de firewall para a cache.

![Firewall](./media/cache-configure/redis-firewall-rules.png)

Pode especificar regras de firewall com um intervalo de endereços IP de início e de fim. Quando as regras de firewall são configuradas, podem ligar apenas ligações de cliente a partir dos intervalos de endereços IP especificados para a cache. Quando uma regra de firewall é guardada, há um pequeno atraso antes da regra está em vigor. Este atraso é, normalmente, menos de um minuto.

> [!IMPORTANT]
> Ligações a partir de Cache do Azure para sistemas de monitorização de Redis sempre são permitidas, mesmo que as regras de firewall estão configuradas.
> 
> 

### <a name="properties"></a>Propriedades
Clique em **propriedades** para ver informações sobre a cache, incluindo o ponto final da cache e as portas.

![Cache do Azure para as propriedades de Redis](./media/cache-configure/redis-cache-properties.png)

### <a name="locks"></a>Bloqueios
O **bloqueia** secção permite-lhe bloquear uma subscrição, grupo de recursos ou recursos para impedir que outros utilizadores na sua organização acidentalmente eliminem ou modifiquem recursos críticos. Para obter mais informações, consulte [Bloquear recursos com o Azure Resource Manager](../azure-resource-manager/resource-group-lock-resources.md).

### <a name="automation-script"></a>Script de automatização

Clique em **script de automação** para criar e exportar um modelo de seus recursos implementados para as futuras Implantações. Para obter mais informações sobre como trabalhar com modelos, consulte [implementar recursos com modelos Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="administration-settings"></a>Definições de administração
As definições no **administração** secção permitem-lhe efetuar as seguintes tarefas administrativas para a sua cache. 

![Administração](./media/cache-configure/redis-cache-administration.png)

* [Importar dados](#importexport)
* [Exportar dados](#importexport)
* [Reiniciar](#reboot)


### <a name="importexport"></a>Importação/Exportação
Importar/exportar é uma Cache do Azure para a operação de gestão dados de Redis, que permite-lhe importar e exportar dados na cache por importar e exportar uma Cache do Azure para o instantâneo de base de dados de Redis (RDB) de uma cache premium para um blob de páginas numa conta de armazenamento do Azure. Importar/exportar permite-lhe migrar entre diferentes a Cache do Azure para instâncias de Redis ou povoar a cache com os dados antes de utilização.

Importação pode ser utilizada para juntar o ficheiros RDB compatíveis de Redis a partir de qualquer servidor Redis em execução em qualquer cloud ou o ambiente, incluindo o Redis em execução no Linux, Windows ou qualquer fornecedor de cloud, como Amazon Web Services e outras pessoas. A importação de dados é uma forma fácil de criar uma cache com dados preenchidos previamente. Durante o processo de importação, a Cache de Redis do Azure carrega os ficheiros RDB do armazenamento do Azure na memória e, em seguida, insere as chaves para a cache.

Exportação permite-lhe exportar os dados armazenados na Cache do Azure para ficheiros RDB compatíveis do Redis para Redis. Pode utilizar esta funcionalidade para mover dados de um Cache do Azure para a instância de Redis para outro ou para outro servidor de Redis. Durante o processo de exportação, um ficheiro temporário é criado na VM que aloja a Cache do Azure para a instância de servidor Redis e o ficheiro é carregado para a conta de armazenamento designada. Quando a operação de exportação for concluída com o estado de sucesso ou falha, o ficheiro temporário é eliminado.

> [!IMPORTANT]
> Importar/exportar só está disponível para as caches de escalão Premium. Para obter mais informações e instruções, consulte [importar e exportar dados na Cache do Azure para Redis](cache-how-to-import-export-data.md).
> 
> 

### <a name="reboot"></a>Reiniciar
O **reiniciar** painel permite-lhe reiniciar os nós da cache. Esta capacidade de reinício permite-lhe testar a sua aplicação para resiliência se ocorrer uma falha de um nó de cache.

![Reiniciar](./media/cache-configure/redis-cache-reboot.png)

Se tiver uma cache premium com clustering ativado, pode selecionar qual shards da cache para reiniciar o computador.

![Reiniciar](./media/cache-configure/redis-cache-reboot-cluster.png)

Para reiniciar uma ou mais nós da cache, selecione os nós pretendido e clique em **reiniciar**. Se tiver uma cache premium com clustering ativado, selecione o shards a reiniciar e, em seguida, clique em **reiniciar**. Após alguns minutos, o reinício do nó selecionado (s) e são novamente online alguns minutos mais tarde.

> [!IMPORTANT]
> O reinício está agora disponível para todos os escalões de preço. Para obter mais informações e instruções, consulte [reinicialização de Cache do Azure para a administração de Redis -](cache-administration.md#reboot).
> 
> 


## <a name="monitoring"></a>Monitorização

O **monitorização** secção permite-lhe configurar o diagnóstico e monitorização para a sua Cache do Azure para Redis. Para obter mais informações sobre a Cache do Azure para Redis monitorização e diagnóstico, consulte [como monitorizar a Cache do Azure para Redis](cache-how-to-monitor.md).

![Diagnóstico](./media/cache-configure/redis-cache-diagnostics.png)

* [Métricas de redis](#redis-metrics)
* [Regras de alerta](#alert-rules)
* [Diagnóstico](#diagnostics)

### <a name="redis-metrics"></a>Métricas de Redis
Clique em **métricas de Redis** ao [ver métricas](cache-how-to-monitor.md#view-cache-metrics) para a sua cache.

### <a name="alert-rules"></a>Regras de alerta

Clique em **regras de alerta** para configurar alertas com base na Cache do Azure para as métricas de Redis. Para obter mais informações, consulte [alertas](cache-how-to-monitor.md#alerts).

### <a name="diagnostics"></a>Diagnóstico

Por predefinição, são métricas de cache no Azure Monitor [armazenadas durante 30 dias](../azure-monitor/platform/data-collection.md#metrics) e, em seguida, eliminados. Para manter as suas métricas de cache durante mais de 30 dias, clique em **diagnóstico** ao [configurar a conta de armazenamento](cache-how-to-monitor.md#export-cache-metrics) utilizada para armazenar o diagnóstico da cache.

>[!NOTE]
>Além de arquivamento de suas métricas de cache para o armazenamento, pode também [transmiti-los para um hub de eventos ou enviá-los para registos do Azure Monitor](../azure-monitor/platform/stream-monitoring-data-event-hubs.md).
>
>

## <a name="support--troubleshooting-settings"></a>Suporte e as definições de resolução de problemas
As definições no **suporte + resolução de problemas** secção fornecer-lhe opções para resolver problemas com a cache.

![Suporte e resolução de problemas](./media/cache-configure/redis-cache-support-troubleshooting.png)

* [Estado de funcionamento do recurso](#resource-health)
* [Novo pedido de suporte](#new-support-request)

### <a name="resource-health"></a>Estado de funcionamento de recursos
**Estado de funcionamento do recurso** monitoriza o recurso e informa se está a funcionar conforme esperado. Para obter mais informações sobre o serviço de estado de funcionamento de recursos do Azure, consulte [descrição geral do Estado de funcionamento de recursos do Azure](../resource-health/resource-health-overview.md).

> [!NOTE]
> Estado de funcionamento do recurso está atualmente não é possível gerar relatórios sobre o estado de funcionamento de Cache do Azure para instâncias de Redis alojado numa rede virtual. Para obter mais informações, consulte [todas as funcionalidades de cache funcionam ao hospedar um cache numa VNET?](cache-how-to-premium-vnet.md#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)
> 
> 

### <a name="new-support-request"></a>Novo pedido de suporte
Clique em **novo pedido de suporte** para abrir um pedido de suporte para a sua cache.





## <a name="default-redis-server-configuration"></a>Configuração predefinida do servidor Redis
Nova Cache do Azure para instâncias de Redis estão configurados com os seguintes valores de configuração do Redis padrão:

> [!NOTE]
> Não não possível alterar as definições nesta secção usando o `StackExchange.Redis.IServer.ConfigSet` método. Se esse método é chamado com um dos comandos nesta secção, é emitida uma exceção semelhante ao seguinte exemplo:  
> 
> `StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
> 
> Os valores que são configuráveis, como **política de memória máxima**, são configuráveis através do portal do Azure ou ferramentas de gestão da linha de comandos, como a CLI do Azure ou o PowerShell.
> 
> 

| Definição | Valor predefinido | Descrição |
| --- | --- | --- |
| `databases` |16 |O número predefinido de bases de dados é 16, mas pode configurar um número diferente com base no escalão de preço. <sup>1</sup> base de dados predefinida é DB 0, pode selecionar um diferente numa base por ligação a utilizar `connection.GetDatabase(dbid)` onde `dbid` é um número entre `0` e `databases - 1`. |
| `maxclients` |Depende do escalão de preço<sup>2</sup> |Este valor é o número máximo de clientes ligados permitido ao mesmo tempo. Assim que o limite for atingido o Redis fecha todas as ligações novas, devolver um erro de "atingido o número máximo de clientes". |
| `maxmemory-policy` |`volatile-lru` |Política de Maxmemory destina-se a definição de como o Redis seleciona o que deve remover quando `maxmemory` (o tamanho da cache que selecionou quando criou o cache da oferta) for atingido. Com a Cache de Redis do Azure é a predefinição `volatile-lru`, que remove as chaves com uma conjunto com um algoritmo LRU de expiração. Esta definição pode ser configurada no portal do Azure. Para obter mais informações, consulte [políticas de memória](#memory-policies). |
| `maxmemory-samples` |3 |Para poupar memória, LRU e algoritmos TTL mínimo são aproximados algoritmos em vez de algoritmos precisos. Por predefinição de Redis verificações de três chaves e escolhe aquele que foi utilizado menos recentemente. |
| `lua-time-limit` |5.000 |Tempo de execução máximo de um script de Lua em milissegundos. Se o tempo de execução máximo for atingido, Redis e registos de que um script ainda está em execução após o tempo máximo permitido é iniciado responder a consultas com um erro. |
| `lua-event-limit` |500 |Tamanho máximo da fila de eventos de script. |
| `client-output-buffer-limit` `normalclient-output-buffer-limit` `pubsub` |0 0 032mb 8mb 60 |Os limites de memória intermédia de saída do cliente podem ser utilizados para forçar a desativação de clientes que não são leitura de dados do servidor rápido o suficiente por algum motivo (uma razão comum é que um cliente de Pub/Sub não é possível consumir mensagens o mais rápidas, como o publicador pode produzi-los). Para mais informações, consulte [https://redis.io/topics/clients](https://redis.io/topics/clients). |

<a name="databases"></a>
<sup>1</sup>o limite para `databases` é diferente para cada Cache do Azure para o escalão de preço de Redis e pode ser definido durante a criação de cache. Se não `databases` definição é especificada durante a criação de cache, a predefinição é 16.

* Básico e Standard caches
  * C0 (250 MB) de cache - até 16 bases de dados
  * C1 cache (1 GB) - até 16 bases de dados
  * C2 cache (2,5 GB) - até 16 bases de dados
  * C3 cache (6 GB) - até 16 bases de dados
  * C4 cache (13 GB) - até 32 bases de dados
  * C5 cache (26 GB) - até 48 bases de dados
  * C6 cache (53 GB) - até 64 bases de dados
* Enquanto as caches Premium
  * P1 (6 GB a 60 GB) - até 16 bases de dados
  * P2 (13 GB 130 GB) - até 32 bases de dados
  * P3 (26 GB 260 GB) - até 48 bases de dados
  * P4 (53 GB a 530 GB) - até 64 bases de dados
  * Todas as caches premium com o cluster de Redis habilitado - o cluster de Redis suporta apenas a utilização da base de dados 0 então, o `databases` limitar para qualquer cache premium com o cluster de Redis ativados com eficiência é 1 e o [selecione](https://redis.io/commands/select) comando não é permitido. Para obter mais informações, consulte [é necessário efetuar quaisquer alterações à minha aplicação de cliente para utilizar o clustering?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)

Para obter mais informações sobre bases de dados, consulte [quais são as bases de dados Redis?](cache-faq.md#what-are-redis-databases)

> [!NOTE]
> O `databases` definição pode ser configurada apenas durante a criação de cache e apenas com o PowerShell, CLI ou outros clientes de gestão. Para obter um exemplo de configuração `databases` durante a criação de cache com o PowerShell, consulte [New-AzRedisCache](cache-howto-manage-redis-cache-powershell.md#databases).
> 
> 

<a name="maxclients"></a>
<sup>2</sup> `maxclients` é diferente para cada Cache do Azure para o escalão de preço de Redis.

* Básico e Standard caches
  * C0 (250 MB) de cache - até 256 ligações
  * C1 cache (1 GB) - até 1000 ligações
  * C2 cache (2,5 GB) - até 2.000 ligações
  * C3 cache (6 GB) - até 5000 ligações
  * C4 cache (13 GB) - até 10.000 conexões
  * C5 cache (26 GB) - até 15 000 ligações
  * C6 cache (53 GB) - até 20.000 ligações
* Enquanto as caches Premium
  * P1 (6 GB a 60 GB) - até 7.500 ligações
  * P2 (13 GB 130 GB) - até 15 000 ligações
  * P3 (26 GB 260 GB) - até 30 000 ligações
  * P4 (53 GB a 530 GB) - até 40.000 ligações

> [!NOTE]
> Embora cada tamanho da cache permite *até* um determinado número de ligações, cada ligação ao Redis sobrecarga associada a ele. Um exemplo de tal sobrecarga seria a utilização de CPU e memória como resultado de encriptação de TLS/SSL. O limite máximo de ligação para um tamanho de cache determinada parte do princípio de uma cache de ânimo leve carregá-lo. Se carregar de sobrecarga de ligação *plus* carga de operações do cliente excede a capacidade para o sistema, a cache pode ter problemas de capacidade, mesmo que não excedeu o limite de ligação para o tamanho atual da cache.
> 
> 



## <a name="redis-commands-not-supported-in-azure-cache-for-redis"></a>Redis comandos não suportados na Cache do Azure para Redis
> [!IMPORTANT]
> Porque a configuração e gestão de Cache do Azure para instâncias de Redis é gerida pela Microsoft, os seguintes comandos estão desativados. Se tentar invocá-los, receberá uma mensagem de erro semelhante a `"(error) ERR unknown command"`.
> 
> * BGREWRITEAOF
> * BGSAVE
> * CONFIG
> * DEPURAR
> * MIGRAR
> * GUARDAR
> * ENCERRAMENTO
> * SLAVEOF
> * CLUSTER - escrita comandos estão desativados, mas o Cluster só de leitura são permitidos comandos do Cluster.
> 
> 

Para obter mais informações sobre os comandos da Redis, consulte [ https://redis.io/commands ](https://redis.io/commands).

## <a name="redis-console"></a>A consola do redis
Com segurança pode emitir comandos para a Cache do Azure para instâncias de Redis com o **consola de Redis**, que está disponível no portal do Azure para todos os escalões de cache.

> [!IMPORTANT]
> - A consola de Redis não funciona com [VNET](cache-how-to-premium-vnet.md). Quando a sua cache faz parte de uma VNET, apenas os clientes na VNET podem aceder à cache. Porque a consola de Redis é executado no seu navegador local, o que está fora da VNET, não é possível ligar à cache.
> - Nem todos os comandos da Redis são suportados na Cache do Azure para Redis. Para obter uma lista de comandos de Redis que estão desativados para a Cache do Azure para Redis, consulte o anterior [Redis comandos não suportados na Cache do Azure para Redis](#redis-commands-not-supported-in-azure-cache-for-redis) secção. Para obter mais informações sobre os comandos da Redis, consulte [ https://redis.io/commands ](https://redis.io/commands).
> 
> 

Para aceder à consola Redis, clique em **consola** partir do **a Cache de Redis do Azure** painel.

![A consola do redis](./media/cache-configure/redis-console-menu.png)

Para emitir comandos em sua instância da cache, escreva o comando pretendido na consola.

![A consola do redis](./media/cache-configure/redis-console.png)


### <a name="using-the-redis-console-with-a-premium-clustered-cache"></a>Utilizando a consola de Redis com uma cache premium em cluster

Quando utilizar a consola de Redis com um premium, a cache em cluster, pode emitir comandos para uma única partição horizontal da cache. Para emitir um comando para uma partição horizontal específica, liga pela primeira vez para a partição horizontal pretendida ao clicar no mesmo o Seletor de partição horizontal.

![A consola do redis](./media/cache-configure/redis-console-premium-cluster.png)

Se tentar acessar uma chave armazenada numa partição horizontal diferente do que a partição horizontal ligada, receberá uma mensagem de erro parecida com a seguinte mensagem:

```
shard1>get myKey
(error) MOVED 866 13.90.202.154:13000 (shard 0)
```

No exemplo anterior, a partição horizontal 1 é a partição horizontal selecionado, mas `myKey` está localizado na partição horizontal 0, conforme indicado pelo `(shard 0)` parte da mensagem de erro. Neste exemplo, para aceder a `myKey`, selecione a partição horizontal 0 com o Seletor de partição horizontal e, em seguida, emita o comando pretendido.


## <a name="move-your-cache-to-a-new-subscription"></a>Mover a sua cache para uma nova subscrição
Pode mover o cache para uma nova subscrição ao clicar em **mover**.

![Mover o Cache do Azure para Redis](./media/cache-configure/redis-cache-move.png)

Para informações sobre como mover os recursos de um grupo de recursos para outro e de uma subscrição para outro, consulte [mover recursos para um novo grupo de recursos ou subscrição](../azure-resource-manager/resource-group-move-resources.md).

## <a name="next-steps"></a>Passos Seguintes
* Para obter mais informações sobre como trabalhar com os comandos da Redis, consulte [como executar comandos da Redis?](cache-faq.md#how-can-i-run-redis-commands)

