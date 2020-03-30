---
title: Como configurar o Cache Azure para redis
description: Compreenda a configuração padrão do Redis para Azure Cache para Redis e aprenda a configurar o seu Azure Cache para os casos Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 08/22/2017
ms.author: yegu
ms.openlocfilehash: f10be8efcd2d8e838b4b5f62310eb405f6ed0158
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278743"
---
# <a name="how-to-configure-azure-cache-for-redis"></a>Como configurar o Cache Azure para redis
Este tópico descreve as configurações disponíveis para os seus casos Azure Cache para Redis. Este tópico também cobre a configuração padrão do servidor Redis para os casos de Azure Cache para redis.

> [!NOTE]
> Para obter mais informações sobre configurar e utilizar funcionalidades de cache premium, consulte [como configurar a persistência,](cache-how-to-premium-persistence.md) [como configurar](cache-how-to-premium-clustering.md)o agrupamento e [como configurar o suporte à Rede Virtual](cache-how-to-premium-vnet.md).
>
>

## <a name="configure-azure-cache-for-redis-settings"></a>Configure Azure Cache para configurações redis
[!INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

As definições de Azure Cache para Redis são vistas e configuradas na **cache Azure para** lâmina Redis utilizando o Menu de **Recursos**.

![Cache Azure para definições de Redis](./media/cache-configure/redis-cache-settings.png)

Pode visualizar e configurar as seguintes definições utilizando o **Menu de Recursos**.

* [Descrição Geral](#overview)
* [Registo de atividades](#activity-log)
* [Controlo de acesso (IAM)](#access-control-iam)
* [Etiquetas](#tags)
* [Diagnosticar e resolver problemas](#diagnose-and-solve-problems)
* [Definições](#settings)
    * [Chaves de acesso](#access-keys)
    * [Definições avançadas](#advanced-settings)
    * [Azure Cache para Redis Advisor](#azure-cache-for-redis-advisor)
    * [Escala](#scale)
    * [Tamanho do cluster](#cluster-size)
    * [Persistência de dados](#redis-data-persistence)
    * [Atualizações agendadas](#schedule-updates)
    * [Georreplicação](#geo-replication)
    * [Rede Virtual](#virtual-network)
    * [Firewall](#firewall)
    * [Propriedades](#properties)
    * [Fechaduras](#locks)
    * [Script de automatização](#automation-script)
* Administração
    * [Importar dados](#importexport)
    * [Exportar dados](#importexport)
    * [Reiniciar](#reboot)
* [Monitorização](#monitoring)
    * [Métricas redis](#redis-metrics)
    * [Regras de alerta](#alert-rules)
    * [Diagnóstico](#diagnostics)
* Apoiar & configurações de resolução de problemas
    * [Saúde dos recursos](#resource-health)
    * [Novo pedido de apoio](#new-support-request)


## <a name="overview"></a>Descrição geral

**A visão geral** fornece-lhe informações básicas sobre o seu cache, tais como nome, portas, nível de preços e métricas de cache selecionadas.

### <a name="activity-log"></a>Registo de atividades

Clique no **registo de atividade** para visualizar as ações realizadas na sua cache. Também pode usar a filtragem para expandir esta vista para incluir outros recursos. Para obter mais informações sobre o trabalho com registos de auditoria, consulte [operações de auditoria com o Gestor de Recursos.](../azure-resource-manager/management/view-activity-logs.md) Para obter mais informações sobre a monitorização do Azure Cache para eventos Redis, consulte [Operações e alertas](cache-how-to-monitor.md#operations-and-alerts).

### <a name="access-control-iam"></a>Controlo de acesso (IAM)

A secção de controlo de **acesso (IAM)** fornece suporte para o controlo de acesso baseado em funções (RBAC) no portal Azure. Esta configuração ajuda as organizações a cumprir os seus requisitos de gestão de acesso de forma simples e precisa. Para mais informações, consulte [o controlo de acesso baseado em role no portal Azure](../role-based-access-control/role-assignments-portal.md).

### <a name="tags"></a>Etiquetas

A secção **Tags** ajuda-o a organizar os seus recursos. Para obter mais informações, veja [Utilizar etiquetas para organizar os recursos do Azure](../azure-resource-manager/management/tag-resources.md).


### <a name="diagnose-and-solve-problems"></a>Diagnosticar e resolver problemas

Clique em **Diagnosticar e resolver problemas** a serem fornecidos com questões e estratégias comuns para resolvê-los.



## <a name="settings"></a>Definições
A secção **Definições** permite-lhe aceder e configurar as seguintes definições para a sua cache.

* [Chaves de acesso](#access-keys)
* [Definições avançadas](#advanced-settings)
* [Azure Cache para Redis Advisor](#azure-cache-for-redis-advisor)
* [Escala](#scale)
* [Tamanho do cluster](#cluster-size)
* [Persistência de dados](#redis-data-persistence)
* [Atualizações agendadas](#schedule-updates)
* [Georreplicação](#geo-replication)
* [Rede Virtual](#virtual-network)
* [Firewall](#firewall)
* [Propriedades](#properties)
* [Fechaduras](#locks)
* [Script de automatização](#automation-script)



### <a name="access-keys"></a>Chaves de acesso
Clique em **teclas de acesso** para visualizar ou regenerar as teclas de acesso para a sua cache. Estas chaves são utilizadas pelos clientes que se ligam à sua cache.

![Cache Azure para Chaves de Acesso Redis](./media/cache-configure/redis-cache-manage-keys.png)

### <a name="advanced-settings"></a>Definições avançadas
As seguintes definições estão configuradas na lâmina de **definições Avançada.**

* [Portas de Acesso](#access-ports)
* [Políticas de memória](#memory-policies)
* [Notificações do espaço-chave (configurações avançadas)](#keyspace-notifications-advanced-settings)

#### <a name="access-ports"></a>Portas de Acesso
Por predefinição, o acesso não SSL está desativado para as novas caches. Para ativar a porta não-SSL, clique **em Não** **permitir o acesso apenas através do SSL** na lâmina de **definições Avançadas** e, em seguida, clique em **Guardar**.

> [!NOTE]
> O acesso sSL ao Azure Cache for Redis suporta tLS 1.0, 1.1 e 1.2 atualmente, mas as versões 1.0 e 1.1 estão a ser retiradas em breve.  Leia a nossa [página Remove TLS 1.0 e 1.1](cache-remove-tls-10-11.md) para mais detalhes.

![Cache Azure para portas de acesso Redis](./media/cache-configure/redis-cache-access-ports.png)

<a name="maxmemory-policy-and-maxmemory-reserved"></a>
#### <a name="memory-policies"></a>Políticas de memória
A **política Maxmemory,** **reservada à memória máxima**e as definições **reservadas** à memória máxima na lâmina de **configuração avançada** configuram as políticas de memória para a cache.

![Cache Azure para a Política Redis Maxmemory](./media/cache-configure/redis-cache-maxmemory-policy.png)

A política de **maxmemory** configura a política de despejo para o cache e permite-lhe escolher entre as seguintes políticas de despejo:

* `volatile-lru`- Esta é a política de despejo por defeito.
* `allkeys-lru`
* `volatile-random`
* `allkeys-random`
* `volatile-ttl`
* `noeviction`

Para obter `maxmemory` mais informações sobre políticas, consulte políticas de [despejo.](https://redis.io/topics/lru-cache#eviction-policies)

A definição **reservada à memória máxima** confunde a quantidade de memória, em MB, reservada para operações não cache, como a replicação durante a failover. Definir este valor permite-lhe ter uma experiência de servidor Redis mais consistente quando a sua carga varia. Este valor deve ser mais elevado para as cargas de trabalho que são pesadas. Quando a memória é reservada para tais operações, não está disponível para armazenamento de dados em cache.

A definição reservada à **memória máxima** confunde a quantidade de memória em MB reservada para acomodar para fragmentação da memória. Definir este valor permite-lhe ter uma experiência de servidor Redis mais consistente quando a cache está cheia ou perto da totalidade e a relação de fragmentação é elevada. Quando a memória é reservada para tais operações, não está disponível para armazenamento de dados em cache.

Uma coisa a considerar na escolha de um novo valor de reserva de memória **(reservado** à memória máxima ou reservado à **memória máxima**) é como esta mudança pode afetar uma cache que já está a funcionar com grandes quantidades de dados na si. Por exemplo, se tiver uma cache de 53 GB com 49 GB de dados, então mude o valor da reserva para 8 GB, esta alteração reduzirá a memória máxima disponível para o sistema para 45 GB. Se os `used_memory` seus `used_memory_rss` valores atuais ou os seus forem superiores ao novo `used_memory` limite `used_memory_rss` de 45 GB, então o sistema terá de despejar dados até ambos e estiver abaixo dos 45 GB. O despejo pode aumentar a carga do servidor e a fragmentação da memória. Para obter mais informações `used_memory` sobre `used_memory_rss`métricas de cache tais como e, ver [métricas disponíveis e intervalos](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)de reporte .

> [!IMPORTANT]
> As definições **reservadas** à memória máxima e **à máxima fragmentação** estão disponíveis apenas para caches Standard e Premium.
>
>

#### <a name="keyspace-notifications-advanced-settings"></a>Notificações do espaço-chave (configurações avançadas)
As notificações do espaço de teclado Redis estão configuradas na lâmina de **definições Avançada.** As notificações do Keyspace permitem que os clientes recebam notificações quando determinados eventos ocorrem.

![Cache Azure para Definições Avançadas Redis](./media/cache-configure/redis-cache-advanced-settings.png)

> [!IMPORTANT]
> As notificações do keyspace e a definição **de eventos espaço-chave notificada** só estão disponíveis para caches Standard e Premium.
>
>

Para mais informações, consulte [Redis Keyspace Notifications](https://redis.io/topics/notifications). Para obter o código da amostra, consulte o ficheiro [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) na amostra do [mundo Hello.](https://github.com/rustd/RedisSamples/tree/master/HelloWorld)


<a name="recommendations"></a>
## <a name="azure-cache-for-redis-advisor"></a>Azure Cache para Redis Advisor
A lâmina **Azure Cache for Redis Advisor** apresenta recomendações para a sua cache. Durante as operações normais, não são apresentadas recomendações.

![Recomendações](./media/cache-configure/redis-cache-no-recommendations.png)

Se ocorrerem quaisquer condições durante as operações da sua cache, tais como alta utilização da memória, largura de banda da rede ou carga do servidor, é apresentado um alerta no Cache Azure para a lâmina **Redis.**

![Recomendações](./media/cache-configure/redis-cache-recommendations-alert.png)

Mais informações podem ser encontradas na lâmina de **recomendações.**

![Recomendações](./media/cache-configure/redis-cache-recommendations.png)

Pode monitorizar estas métricas nos gráficos de [monitorização](cache-how-to-monitor.md#monitoring-charts) e nas [tabelas](cache-how-to-monitor.md#usage-charts) de utilização do Azure Cache para a lâmina **Redis.**

Cada nível de preços tem limites diferentes para ligações ao cliente, memória e largura de banda. Se o seu cache se aproximar da capacidade máxima destas métricas durante um período de tempo prolongado, é criada uma recomendação. Para obter mais informações sobre as métricas e limites revistos pela ferramenta **Recomendações,** consulte o quadro seguinte:

| Azure Cache para a métrica Redis | Mais informações |
| --- | --- |
| Utilização da largura de banda de rede |[Desempenho da cache - largura de banda disponível](cache-faq.md#cache-performance) |
| Clientes conectados |[Configuração do servidor Predefinido Redis - clientes max](#maxclients) |
| Carga do servidor |[Gráficos de utilização - Carga do Servidor Redis](cache-how-to-monitor.md#usage-charts) |
| Uso da memória |[Desempenho da cache - tamanho](cache-faq.md#cache-performance) |

Para atualizar a sua cache, clique **em Upgrade agora** para alterar o nível de preços e [escalar](#scale) a sua cache. Para obter mais informações sobre a escolha de um nível de preços, veja [o que azure cache para redis oferecendo e tamanho devo usar?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)


### <a name="scale"></a>Dimensionamento
Clique em **Escala** para visualizar ou alterar o nível de preços para a sua cache. Para obter mais informações sobre escala, consulte [Como escalar o Cache Azure para Redis](cache-how-to-scale.md).

![Azure Cache para o nível de preços redis](./media/cache-configure/pricing-tier.png)

<a name="cluster-size"></a>

### <a name="redis-cluster-size"></a>Tamanho do cluster redis
Clique no tamanho do **cluster** para alterar o tamanho do cluster para uma cache premium em execução com clustering ativado.

![Tamanho do cluster](./media/cache-configure/redis-cache-redis-cluster-size.png)

Para alterar o tamanho do cluster, utilize o slider ou escreva um número entre 1 e 10 na caixa de texto **de contagem de fragmentos** e clique em **OK** para guardar.

> [!IMPORTANT]
> O agrupamento Redis só está disponível para caches Premium. Para mais informações, consulte [Como configurar o agrupamento para um Cache Premium Azure para Redis](cache-how-to-premium-clustering.md).
>
>


### <a name="redis-data-persistence"></a>Persistência de dados de Redis
Clique na **persistência de Dados** para ativar, desativar ou configurar a persistência de dados para a sua cache premium. O Azure Cache for Redis oferece persistência redis usando a [persistência de RDB](cache-how-to-premium-persistence.md#configure-rdb-persistence) ou [persistência AOF](cache-how-to-premium-persistence.md#configure-aof-persistence).

Para mais informações, consulte [Como configurar a persistência de um Cache Premium Azure para Redis](cache-how-to-premium-persistence.md).


> [!IMPORTANT]
> A persistência de dados redis só está disponível para caches Premium.
>
>

### <a name="schedule-updates"></a>Agendar atualizações
A lâmina de **atualizações de Agenda** permite-lhe designar uma janela de manutenção para atualizações do servidor Redis para a sua cache.

> [!IMPORTANT]
> A janela de manutenção aplica-se apenas às atualizações do servidor Redis e não a quaisquer atualizações ou atualizações do Azure para o sistema operativo dos VMs que acolhem a cache.
>
>

![Agendar atualizações](./media/cache-configure/redis-schedule-updates.png)

Para especificar uma janela de manutenção, verifique os dias desejados e especifique a hora de início da janela de manutenção para cada dia e clique em **OK**. O tempo da janela de manutenção é na UTC.

> [!IMPORTANT]
> A funcionalidade de **atualizações do Programação** só está disponível para caches de nível Premium. Para mais informações e instruções, consulte [Azure Cache para administração Redis - Agendar atualizações](cache-administration.md#schedule-updates).
>
>

### <a name="geo-replication"></a>Georreplicação

A lâmina **de geo-replicação** fornece um mecanismo para ligar dois acabamentos Premium Azure Cache para instâncias Redis. Uma cache é designada como a cache ligada primária, e a outra como a cache secundária ligada. A cache ligada secundária torna-se apenas de leitura, e os dados escritos para a cache primária são replicados para a cache ligada secundária. Esta funcionalidade pode ser usada para replicar um cache em todas as regiões de Azure.

> [!IMPORTANT]
> **A geo-replicação** só está disponível para caches de nível Premium. Para mais informações e instruções, consulte [Como configurar a Geo-replicação para Azure Cache for Redis](cache-how-to-geo-replication.md).
>
>

### <a name="virtual-network"></a>Rede Virtual
A secção **Rede Virtual** permite-lhe configurar as definições de rede virtual para a sua cache. Para obter informações sobre a criação de um cache premium com suporte VNET e atualizar as suas definições, consulte como configurar o [Suporte de Rede Virtual para um Cache Premium Azure para Redis](cache-how-to-premium-vnet.md).

> [!IMPORTANT]
> As definições de rede virtual só estão disponíveis para caches premium que foram configurados com suporte VNET durante a criação de cache.
>
>

### <a name="firewall"></a>Firewall

A configuração das regras da firewall está disponível para todos os níveis Azure Cache para redis.

Clique em **Firewall** para visualizar e configurar as regras de firewall para cache.

![Firewall](./media/cache-configure/redis-firewall-rules.png)

Pode especificar as regras de firewall com um intervalo de endereçoIP inicial e final. Quando as regras de firewall são configuradas, apenas as ligações do cliente a partir das gamas de endereços IP especificadas podem ligar-se à cache. Quando uma regra de firewall é guardada, há um curto atraso antes que a regra seja eficaz. Este atraso é tipicamente inferior a um minuto.

> [!IMPORTANT]
> As ligações do Azure Cache para sistemas de monitorização Redis são sempre permitidas, mesmo que as regras de firewall estejam configuradas.
>
>

### <a name="properties"></a>Propriedades
Clique em **Propriedades** para ver informações sobre a sua cache, incluindo o ponto final de cache e portas.

![Cache Azure para propriedades redis](./media/cache-configure/redis-cache-properties.png)

### <a name="locks"></a>Bloqueios
A secção **Locks** permite-lhe bloquear uma subscrição, grupo de recursos ou recurso para evitar que outros utilizadores da sua organização apagam ou modifiquem acidentalmente recursos críticos. Para obter mais informações, consulte [Bloquear recursos com o Azure Resource Manager](../azure-resource-manager/management/lock-resources.md).

### <a name="automation-script"></a>Script de automatização

Clique no **script automation** para construir e exportar um modelo dos seus recursos implantados para futuras implementações. Para obter mais informações sobre o trabalho com modelos, consulte implementar recursos com modelos de [Gestor de Recursos Azure](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="administration-settings"></a>Configurações de administração
As definições na secção **Administração** permitem-lhe executar as seguintes tarefas administrativas para a sua cache.

![Administração](./media/cache-configure/redis-cache-administration.png)

* [Importar dados](#importexport)
* [Exportar dados](#importexport)
* [Reiniciar](#reboot)


### <a name="importexport"></a>Importação/Exportação
Import/Export é um Cache Azure para a operação de gestão de dados Redis, que permite importar e exportar dados na cache, importando e exportando um instantâneo Azure Cache for Redis Database (RDB) de uma cache premium para uma página blob numa conta de armazenamento Azure. A importação/exportação permite-lhe migrar entre diferentes cache sinuosos para casos Redis ou povoar a cache com dados antes da utilização.

A importação pode ser usada para trazer ficheiros RDB compatíveis com Redis de qualquer servidor Redis em qualquer nuvem ou ambiente, incluindo Redis em execução em Linux, Windows ou qualquer fornecedor de nuvem, como Amazon Web Services e outros. Importar dados é uma forma fácil de criar uma cache com dados pré-povoados. Durante o processo de importação, o Azure Cache for Redis carrega os ficheiros RDB do armazenamento Azure na memória e, em seguida, insere as teclas na cache.

A exportação permite-lhe exportar os dados armazenados em Azure Cache para Redis para ficheiros RDB compatíveis com Redis. Pode utilizar esta funcionalidade para mover dados de um Azure Cache para redis para outro ou para outro servidor Redis. Durante o processo de exportação, é criado um ficheiro temporário no VM que acolhe a caixa Azure para a instância do servidor Redis, e o ficheiro é enviado para a conta de armazenamento designada. Quando a operação de exportação termina com um estatuto de sucesso ou falha, o ficheiro temporário é suprimido.

> [!IMPORTANT]
> A importação/exportação só está disponível para caches de nível Premium. Para obter mais informações e instruções, consulte [os dados de importação e exportação em Azure Cache for Redis](cache-how-to-import-export-data.md).
>
>

### <a name="reboot"></a>Reiniciar
A lâmina **Reboot** permite reiniciar os nódosos da sua cache. Esta capacidade de reinicialização permite-lhe testar a sua aplicação de resiliência se houver uma falha de um nó de cache.

![Reiniciar](./media/cache-configure/redis-cache-reboot.png)

Se tiver uma cache premium com clustering ativado, pode selecionar quais os fragmentos da cache para reiniciar.

![Reiniciar](./media/cache-configure/redis-cache-reboot-cluster.png)

Para reiniciar um ou mais nós da sua cache, selecione os nós desejados e clique em **Reiniciar**. Se tiver uma cache premium com clustering ativado, selecione o fragmento(s) para reiniciar e, em seguida, clique em **Reiniciar**. Após alguns minutos, o nó(s) selecionado reinicia e volta a funcionar alguns minutos depois.

> [!IMPORTANT]
> O reboot está agora disponível para todos os níveis de preços. Para mais informações e instruções, consulte [Azure Cache para administração Redis - Reboot](cache-administration.md#reboot).
>
>


## <a name="monitoring"></a>Monitorização

A secção **de Monitorização** permite configurar diagnósticos e monitorização para o seu Azure Cache para Redis.
Para obter mais informações sobre o Azure Cache para monitorização e diagnóstico redis, consulte [Como monitorizar o Azure Cache for Redis](cache-how-to-monitor.md).

![Diagnóstico](./media/cache-configure/redis-cache-diagnostics.png)

* [Métricas redis](#redis-metrics)
* [Regras de alerta](#alert-rules)
* [Diagnóstico](#diagnostics)

### <a name="redis-metrics"></a>Métricas redis
Clique nas **métricas do Redis** para [ver as métricas](cache-how-to-monitor.md#view-cache-metrics) para a sua cache.

### <a name="alert-rules"></a>Regras de alerta

Clique nas regras de **alerta** para configurar alertas baseados em Azure Cache para métricas Redis. Para mais informações, consulte [Alertas](cache-how-to-monitor.md#alerts).

### <a name="diagnostics"></a>Diagnóstico

Por predefinição, as métricas de cache no Monitor Azure são [armazenadas durante 30 dias](../azure-monitor/platform/data-platform-metrics.md) e depois eliminadas. Para persistir as métricas de cache por mais de 30 dias, clique em **Diagnósticos** para [configurar a conta](cache-how-to-monitor.md#export-cache-metrics) de armazenamento utilizada para armazenar diagnósticos de cache.

>[!NOTE]
>Além de arquivar as suas métricas de cache para armazenamento, também pode [transmiti-las para um hub de eventos ou enviá-las para registos do Monitor Azure](../azure-monitor/platform/stream-monitoring-data-event-hubs.md).
>
>

## <a name="support--troubleshooting-settings"></a>Apoiar & configurações de resolução de problemas
As definições na secção **Suporte + resolução** de problemas fornecem-lhe opções para resolver problemas com a sua cache.

![Suporte e resolução de problemas](./media/cache-configure/redis-cache-support-troubleshooting.png)

* [Saúde dos recursos](#resource-health)
* [Novo pedido de apoio](#new-support-request)

### <a name="resource-health"></a>Estado de funcionamento de recursos
**A saúde** dos recursos observa o seu recurso e diz-lhe se está a funcionar como esperado. Para mais informações sobre o serviço de saúde azure Resource, consulte a [visão geral da saúde do Recurso Azure.](../resource-health/resource-health-overview.md)

> [!NOTE]
> Atualmente, a saúde dos recursos não pode reportar a saúde do Azure Cache para os casos Redis alojados numa rede virtual. Para mais informações, consulte [Todos os recursos de cache funcionam ao hospedar uma cache num VNET?](cache-how-to-premium-vnet.md#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)
>
>

### <a name="new-support-request"></a>Novo pedido de suporte
Clique em **Novo pedido** de suporte para abrir um pedido de suporte para a sua cache.





## <a name="default-redis-server-configuration"></a>Configuração do servidor Predefinido Redis
Os novos casos de Azure Cache para Redis estão configurados com os seguintes valores de configuração predefinidos do Redis:

> [!NOTE]
> As definições nesta secção não `StackExchange.Redis.IServer.ConfigSet` podem ser alteradas utilizando o método. Se este método for chamado com um dos comandos desta secção, é lançada uma exceção semelhante ao seguinte exemplo:  
>
> `StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
>
> Quaisquer valores configuráveis, como a **política de memória máxima,** são configuráveis através do portal Azure ou ferramentas de gestão de linha de comando, como o Azure CLI ou o PowerShell.
>
>

| Definição | Valor predefinido | Descrição |
| --- | --- | --- |
| `databases` |16 |O número padrão de bases de dados é de 16, mas pode configurar um número diferente com base no nível de preços. <sup>1</sup> A base de dados predefinida é DB 0, pode `connection.GetDatabase(dbid)` `dbid` selecionar uma `0` diferente `databases - 1`numa base por ligação utilizando um número entre e . |
| `maxclients` |Depende do nível<sup>de</sup> preços 2 |Este valor é o número máximo de clientes conectados permitidos ao mesmo tempo. Uma vez atingido o limite, a Redis fecha todas as novas ligações, devolvendo um erro de "número máximo de clientes atingidos". |
| `maxmemory-policy` |`volatile-lru` |A política de maxmemory é a definição `maxmemory` de como redis seleciona o que remover quando (o tamanho da oferta de cache que selecionou quando criou a cache) é alcançada. Com o Azure Cache para `volatile-lru`Redis, a definição padrão é, que remove as teclas com um conjunto de expiração utilizando um algoritmo LRU. Esta definição pode ser configurada no portal Azure. Para mais informações, consulte [as políticas de Memória.](#memory-policies) |
| `maxmemory-samples` |3 |Para salvar a memória, a LRU e os algoritmos Mínimos de TTL são algoritmos aproximados em vez de algoritmos precisos. Por padrão, redis verifica três chaves e escolhe a que foi usada menos recentemente. |
| `lua-time-limit` |5000 |Tempo máximo de execução de um guião lua em milissegundos. Se o tempo máximo de execução for atingido, redis regista que um script ainda está em execução após o tempo máximo permitido, e começa a responder a consultas com um erro. |
| `lua-event-limit` |500 |Tamanho máximo da fila do evento do guião. |
| `client-output-buffer-limit` `normalclient-output-buffer-limit` `pubsub` |0 0 0 32mb 8mb 60 |Os limites máximos de saída do cliente podem ser usados para forçar a desconexão de clientes que não estão a ler dados do servidor suficientemente rápido por alguma razão (uma razão comum é que um cliente Pub/Sub não pode consumir mensagens tão rapidamente quanto a editora as pode produzir). Para mais informações, consulte [https://redis.io/topics/clients](https://redis.io/topics/clients). |

<a name="databases"></a>
<sup>1</sup> O limite `databases` para é diferente para cada Cache Azure para o nível de preços Redis e pode ser definido na criação de cache. Se `databases` não for especificada qualquer definição durante a criação de cache, a predefinição é de 16.

* Caches básicos e standard
  * Cache C0 (250 MB) - até 16 bases de dados
  * Cache C1 (1 GB) - até 16 bases de dados
  * Cache C2 (2,5 GB) - até 16 bases de dados
  * Cache C3 (6 GB) - até 16 bases de dados
  * Cache C4 (13 GB) - até 32 bases de dados
  * Cache C5 (26 GB) - até 48 bases de dados
  * Cache C6 (53 GB) - até 64 bases de dados
* Caches premium
  * P1 (6 GB - 60 GB) - até 16 bases de dados
  * P2 (13 GB - 130 GB) - até 32 bases de dados
  * P3 (26 GB - 260 GB) - até 48 bases de dados
  * P4 (53 GB - 530 GB) - até 64 bases de dados
  * Todos os caches premium com cluster Redis ativados - O cluster `databases` Redis apenas suporta a utilização da base de dados 0, pelo que o limite para qualquer cache premium com cluster Redis ativado é efetivamente 1 e o comando [Select](https://redis.io/commands/select) não é permitido. Para mais informações, consulte [Se preciso de fazer alterações na aplicação do meu cliente para utilizar o clustering?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)

Para mais informações sobre bases de dados, veja o que são as [bases de dados redis?](cache-faq.md#what-are-redis-databases)

> [!NOTE]
> A `databases` definição só pode ser configurada durante a criação de cache e apenas utilizando powerShell, CLI ou outros clientes de gestão. Para um exemplo `databases` de configuração durante a criação de cache usando powerShell, consulte [New-AzRedisCache](cache-how-to-manage-redis-cache-powershell.md#databases).
>
>

<a name="maxclients"></a>
<sup>2</sup> `maxclients` é diferente para cada Cache Azure para o nível de preços Redis.

* Caches básicos e standard
  * Cache C0 (250 MB) - até 256 ligações
  * Cache C1 (1 GB) - até 1.000 ligações
  * Cache C2 (2,5 GB) - até 2.000 ligações
  * Cache C3 (6 GB) - até 5.000 ligações
  * Cache C4 (13 GB) - até 10.000 ligações
  * Cache C5 (26 GB) - até 15.000 ligações
  * Cache C6 (53 GB) - até 20.000 ligações
* Caches premium
  * P1 (6 GB - 60 GB) - até 7.500 ligações
  * P2 (13 GB - 130 GB) - até 15.000 ligações
  * P3 (26 GB - 260 GB) - até 30.000 ligações
  * P4 (53 GB - 530 GB) - até 40.000 ligações

> [!NOTE]
> Embora cada tamanho de cache permita *até* um certo número de ligações, cada ligação com Redis tem sobrecarga associada a ele. Um exemplo de tal sobrecarga seria cpU e uso de memória como resultado da encriptação TLS/SSL. O limite máximo de ligação para um dado tamanho de cache pressupõe uma cache levemente carregada. Se a carga a partir de ligação superior *mais* a carga das operações do cliente exceder a capacidade do sistema, a cache pode experimentar problemas de capacidade mesmo que não tenha excedido o limite de ligação para o tamanho atual do cache.
>
>



## <a name="redis-commands-not-supported-in-azure-cache-for-redis"></a>Comandos Redis não suportados em Azure Cache para redis
> [!IMPORTANT]
> Uma vez que a configuração e gestão do Azure Cache para os casos Redis é gerida pela Microsoft, os seguintes comandos são desativados. Se tentar invocá-los, recebe uma `"(error) ERR unknown command"`mensagem de erro semelhante a .
>
> * BGREWRITEAOF
> * BGSAVE
> * CONFIG
> * DEPURAR
> * MIGRAÇÃO
> * GUARDAR
> * ENCERRAMENTO
> * ESCRAVO
> * CLUSTER - Os comandos de escrita do cluster são desativados, mas os comandos cluster apenas de leitura são permitidos.
>
>

Para mais informações sobre os [https://redis.io/commands](https://redis.io/commands)comandos redis, consulte .

## <a name="redis-console"></a>Consola Redis
Pode emitir comandos de segurança para os casos do Seu Azure Cache para Redis utilizando a **Consola Redis,** que está disponível no portal Azure para todos os níveis de cache.

> [!IMPORTANT]
> - A Consola Redis não funciona com [o VNET.](cache-how-to-premium-vnet.md) Quando a sua cache faz parte de um VNET, apenas os clientes do VNET podem aceder à cache. Como a Consola Redis funciona no seu navegador local, que está fora do VNET, não pode ligar-se à sua cache.
> - Nem todos os comandos Redis são apoiados em Azure Cache para Redis. Para obter uma lista de comandos Redis que são desativados para Azure Cache for Redis, consulte os comandos redis anteriores [não suportados em Azure Cache para](#redis-commands-not-supported-in-azure-cache-for-redis) a secção Redis. Para mais informações sobre os [https://redis.io/commands](https://redis.io/commands)comandos redis, consulte .
>
>

Para aceder à Consola Redis, clique em **Consola** a partir do Cache Azure para a lâmina **Redis.**

![Consola Redis](./media/cache-configure/redis-console-menu.png)

Para emitir comandos contra a sua instância de cache, digite o comando desejado na consola.

![Consola Redis](./media/cache-configure/redis-console.png)


### <a name="using-the-redis-console-with-a-premium-clustered-cache"></a>Usando a Consola Redis com uma cache agrupada premium

Ao utilizar a Consola Redis com uma cache agrupada premium, pode emitir comandos para um único fragmento da cache. Para emitir um comando a um fragmento específico, ligue-se primeiro ao fragmento desejado clicando-o no picker de fragmentos.

![Consola Redis](./media/cache-configure/redis-console-premium-cluster.png)

Se tentar aceder a uma chave que é armazenada num fragmento diferente do fragmento conectado, recebe uma mensagem de erro semelhante à seguinte mensagem:

```
shard1>get myKey
(error) MOVED 866 13.90.202.154:13000 (shard 0)
```

No exemplo anterior, o fragmento 1 é `myKey` o fragmento selecionado, mas está `(shard 0)` localizado no fragmento 0, como indicado pela parte da mensagem de erro. Neste exemplo, para `myKey`aceder , selecione o fragmento 0 utilizando o picker de fragmentos e, em seguida, emita o comando desejado.


## <a name="move-your-cache-to-a-new-subscription"></a>Mova a sua cache para uma nova subscrição
Pode mover a sua cache para uma nova subscrição clicando em **Move**.

![Mova Azure Cache para Redis](./media/cache-configure/redis-cache-move.png)

Para obter informações sobre a mudança de recursos de um grupo de recursos para outro, e de uma subscrição para outra, consulte [mover recursos para um novo grupo de recursos ou subscrição](../azure-resource-manager/management/move-resource-group-and-subscription.md).

## <a name="next-steps"></a>Passos seguintes
* Para mais informações sobre trabalhar com os comandos redis, veja como posso executar os [comandos redis?](cache-faq.md#how-can-i-run-redis-commands)
