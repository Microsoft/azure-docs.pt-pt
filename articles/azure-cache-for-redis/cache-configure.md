---
title: Como configurar cache Azure para Redis
description: Compreenda a configuração padrão do Redis para Azure Cache para Redis e aprenda a configurar a sua Cache Azure para instâncias Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 08/22/2017
ms.author: yegu
ms.openlocfilehash: f0d0742994b14f692c2aea9130edc73d779cff52
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92544771"
---
# <a name="how-to-configure-azure-cache-for-redis"></a>Como configurar cache Azure para Redis
Este tópico descreve as configurações disponíveis para o seu Azure Cache para instâncias Redis. Este tópico também cobre a configuração padrão do servidor Redis para Azure Cache para instâncias Redis.

> [!NOTE]
> Para obter mais informações sobre configurar e utilizar funcionalidades de cache premium, consulte [Como configurar a persistência,](cache-how-to-premium-persistence.md) [Como configurar o agrupamento](cache-how-to-premium-clustering.md)e como [configurar o suporte à Rede Virtual.](cache-how-to-premium-vnet.md)
>
>

## <a name="configure-azure-cache-for-redis-settings"></a>Cache Azure configurado para configurações de Redis
[!INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

A cache azul para as definições de Redis são visualizadas e configuradas na **Cache Azure para** lâmina Redis utilizando o **Menu de Recursos** .

![Cache Azure para Definições de Redis](./media/cache-configure/redis-cache-settings.png)

Pode visualizar e configurar as seguintes definições utilizando o **Menu de Recursos.**

* [Descrição geral](#overview)
* [Registo de atividades](#activity-log)
* [Controlo de acesso (IAM)](#access-control-iam)
* [Etiquetas](#tags)
* [Diagnosticar e resolver problemas](#diagnose-and-solve-problems)
* [Definições](#settings)
    * [Chaves de acesso](#access-keys)
    * [Definições avançadas](#advanced-settings)
    * [Cache Azure para Redis Advisor](#azure-cache-for-redis-advisor)
    * [Dimensionamento](#scale)
    * [Tamanho do cluster](#cluster-size)
    * [Persistência de dados](#redis-data-persistence)
    * [Agendar atualizações](#schedule-updates)
    * [Georreplicação](#geo-replication)
    * [Rede Virtual](#virtual-network)
    * [Firewall](#firewall)
    * [Propriedades](#properties)
    * [Bloqueios](#locks)
    * [Script de automatização](#automation-script)
* Administração
    * [Importar dados](#importexport)
    * [Exportar dados](#importexport)
    * [Reiniciar](#reboot)
* [Monitorização](#monitoring)
    * [Métricas redis](#redis-metrics)
    * [Regras de alerta](#alert-rules)
    * [Diagnóstico](#diagnostics)
* Suporte & configurações de resolução de problemas
    * [Estado de funcionamento dos recursos](#resource-health)
    * [Novo pedido de apoio](#new-support-request)


## <a name="overview"></a>Descrição geral

**A visão geral** fornece-lhe informações básicas sobre o seu cache, tais como nome, portas, nível de preços e métricas de cache selecionadas.

### <a name="activity-log"></a>Registo de atividades

Clique **em Iniciarções de atividades** para ver as ações realizadas no seu cache. Também pode utilizar a filtragem para expandir esta vista para incluir outros recursos. Para obter mais informações sobre o trabalho com os registos de auditoria, consulte [as operações de Auditoria com o Gestor de Recursos.](../azure-resource-manager/management/view-activity-logs.md) Para obter mais informações sobre a monitorização da Cache Azure para eventos Redis, consulte [Operações e alertas.](cache-how-to-monitor.md#operations-and-alerts)

### <a name="access-control-iam"></a>Controlo de acesso (IAM)

A secção **de controlo de acesso (IAM)** fornece suporte para o controlo de acesso baseado em funções Azure (Azure RBAC) no portal Azure. Esta configuração ajuda as organizações a cumprir os seus requisitos de gestão de acesso de forma simples e precisa. Para obter mais informações, consulte [o controlo de acesso baseado em funções Azure no portal Azure](../role-based-access-control/role-assignments-portal.md).

### <a name="tags"></a>Etiquetas

A secção **Tags** ajuda-o a organizar os seus recursos. Para obter mais informações, veja [Utilizar etiquetas para organizar os recursos do Azure](../azure-resource-manager/management/tag-resources.md).


### <a name="diagnose-and-solve-problems"></a>Diagnosticar e resolver problemas

Clique **em diagnosticar e resolver problemas** a fornecer com questões e estratégias comuns para resolvê-los.



## <a name="settings"></a>Definições
A secção **Definições** permite-lhe aceder e configurar as seguintes definições para o seu cache.

* [Chaves de acesso](#access-keys)
* [Definições avançadas](#advanced-settings)
* [Cache Azure para Redis Advisor](#azure-cache-for-redis-advisor)
* [Dimensionamento](#scale)
* [Tamanho do cluster](#cluster-size)
* [Persistência de dados](#redis-data-persistence)
* [Agendar atualizações](#schedule-updates)
* [Georreplicação](#geo-replication)
* [Rede Virtual](#virtual-network)
* [Firewall](#firewall)
* [Propriedades](#properties)
* [Bloqueios](#locks)
* [Script de automatização](#automation-script)



### <a name="access-keys"></a>Chaves de acesso
Clique **nas teclas de acesso** para visualizar ou regenerar as teclas de acesso para o seu cache. Estas chaves são usadas pelos clientes que se ligam ao seu cache.

![Cache Azure para Chaves de Acesso Redis](./media/cache-configure/redis-cache-manage-keys.png)

### <a name="advanced-settings"></a>Definições avançadas
As seguintes definições são configuradas na lâmina **de regulação avançada.**

* [Portos de Acesso](#access-ports)
* [Políticas de memória](#memory-policies)
* [Notificações do Espaço-Chave (definições avançadas)](#keyspace-notifications-advanced-settings)

#### <a name="access-ports"></a>Portos de Acesso
Por predefinição, o acesso não-TLS/SSL é desativado para novos caches. Para ativar a porta não TLS, clique em **No** for **Allow access apenas via SSL** na lâmina **de definições Avançadas** e, em seguida, clique em **Guardar** .

> [!NOTE]
> O acesso do TLS ao Azure Cache para Redis suporta TLS 1.0, 1.1 e 1.2 atualmente, mas as versões 1.0 e 1.1 estão a ser retiradas em breve.  Leia a nossa [página Remove TLS 1.0 e 1.1](cache-remove-tls-10-11.md) para mais detalhes.

![Cache Azure para portas de acesso Redis](./media/cache-configure/redis-cache-access-ports.png)

<a name="maxmemory-policy-and-maxmemory-reserved"></a>
#### <a name="memory-policies"></a>Políticas de memória
A **política Maxmemory** , **reservada ao maxmemory** , e as definições **reservadas ao maxfragmentationmemory** na lâmina **de configurações avançadas** configuram as políticas de memória para a cache.

![Cache Azure para a Política De Redis Maxmemory](./media/cache-configure/redis-cache-maxmemory-policy.png)

**A política maxmemory** configura a política de despejo para a cache e permite-lhe escolher entre as seguintes políticas de despejo:

* `volatile-lru` - Esta é a política de despejo padrão.
* `allkeys-lru`
* `volatile-random`
* `allkeys-random`
* `volatile-ttl`
* `noeviction`

Para obter mais informações sobre `maxmemory` políticas, consulte [as políticas de despejo.](https://redis.io/topics/lru-cache#eviction-policies)

A definição **reservada ao maxmemory** configura a quantidade de memória, em MB por exemplo num cluster, que é reservada para operações não-cache, como a replicação durante o failover. Definir este valor permite-lhe ter uma experiência de servidor Redis mais consistente quando a sua carga varia. Este valor deve ser mais elevado para cargas de trabalho que são escritas pesadas. Quando a memória é reservada para tais operações, não está disponível para armazenamento de dados em cache.

A **definição reservada ao maxfragmentationmemory** configura a quantidade de memória, em MB por exemplo num cluster, que é reservada para acomodar para fragmentação da memória. Definir este valor permite-lhe ter uma experiência de servidor Redis mais consistente quando a cache está cheia ou perto de completa e a relação de fragmentação é elevada. Quando a memória é reservada para tais operações, não está disponível para armazenamento de dados em cache.

Uma coisa a ter em conta na escolha de um novo valor de reserva de memória ( **reservado ao maxmemory** ou **maxfragment-reserved** ) é como esta mudança pode afetar uma cache que já está a funcionar com grandes quantidades de dados no mesmo. Por exemplo, se tiver uma cache de 53 GB com 49 GB de dados, em seguida, altere o valor da reserva para 8 GB, esta alteração deixará a memória máxima disponível para o sistema para 45 GB. Se a sua corrente `used_memory` ou os seus `used_memory_rss` valores forem superiores ao novo limite de 45 GB, então o sistema terá de despejar dados até ambos `used_memory` e ficar `used_memory_rss` abaixo dos 45 GB. O despejo pode aumentar a carga do servidor e a fragmentação da memória. Para obter mais informações sobre métricas de cache, tais como `used_memory` `used_memory_rss` e, consulte [métricas disponíveis e intervalos de reporte](cache-how-to-monitor.md#available-metrics-and-reporting-intervals).

> [!IMPORTANT]
> As definições **reservadas ao maxmemory** e **maxfragmentationm-reservedmemory-reserved** estão disponíveis apenas para caches Standard e Premium.
>
>

#### <a name="keyspace-notifications-advanced-settings"></a>Notificações do Espaço-Chave (definições avançadas)
As notificações do espaço-chave redis estão configuradas na lâmina **de definições avançadas.** As notificações keyspace permitem que os clientes recebam notificações quando determinados eventos ocorrem.

![Cache Azure para Redis Advanced Settings](./media/cache-configure/redis-cache-advanced-settings.png)

> [!IMPORTANT]
> As notificações keyspace e a definição **de eventos de espaço-chave de notificação** só estão disponíveis para caches Standard e Premium.
>
>

Para mais informações, consulte [as notificações do Espaço-Chave Redis.](https://redis.io/topics/notifications) Para obter o código de amostra, consulte o ficheiro [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) na amostra [do mundo Hello.](https://github.com/rustd/RedisSamples/tree/master/HelloWorld)


<a name="recommendations"></a>
## <a name="azure-cache-for-redis-advisor"></a>Cache Azure para Redis Advisor
A **cache Azure para a** lâmina Redis Advisor apresenta recomendações para o seu cache. Durante as operações normais, não são apresentadas recomendações.

![Screenshot que mostra onde as recomendações são apresentadas.](./media/cache-configure/redis-cache-no-recommendations.png)

Se ocorrerem condições durante as operações do seu cache, tais como utilização de alta memória, largura de banda de rede ou carga do servidor, é apresentado um alerta na **Cache Azure para** a lâmina Redis.

![Screenshot que mostra onde os alertas são apresentados na cache Azure para a secção Redis.](./media/cache-configure/redis-cache-recommendations-alert.png)

Mais informações podem ser encontradas na lâmina **de recomendações.**

![Recomendações](./media/cache-configure/redis-cache-recommendations.png)

Pode monitorizar estas métricas nas [tabelas de monitorização](cache-how-to-monitor.md#monitoring-charts) e nas secções de gráficos de [utilização](cache-how-to-monitor.md#usage-charts) da **Cache Azure para lâmina Redis.**

Cada nível de preços tem limites diferentes para ligações ao cliente, memória e largura de banda. Se a sua cache se aproximar da capacidade máxima destas métricas durante um período de tempo prolongado, é criada uma recomendação. Para obter mais informações sobre as métricas e limites revistos pela ferramenta **Recomendações,** consulte o seguinte quadro:

| Cache Azure para métrica Redis | Mais informações |
| --- | --- |
| Utilização da largura de banda de rede |[Desempenho da cache - largura de banda disponível](cache-planning-faq.md#azure-cache-for-redis-performance) |
| Clientes conectados |[Configuração padrão do servidor Redis - maxclients](#maxclients) |
| Carga do servidor |[Gráficos de utilização - Redis Server Load](cache-how-to-monitor.md#usage-charts) |
| Utilização de memória |[Desempenho da cache - tamanho](cache-planning-faq.md#azure-cache-for-redis-performance) |

Para atualizar o seu cache, clique em **Atualizar agora** para alterar o nível de preços e [escalar](#scale) o seu cache. Para obter mais informações sobre a escolha de um nível de preços, consulte [escolher o nível certo](cache-overview.md#choosing-the-right-tier)


### <a name="scale"></a>Escala
Clique **em Escala** para ver ou alterar o nível de preços para o seu cache. Para obter mais informações sobre a escala, consulte [Como Escalar Cache Azure para Redis](cache-how-to-scale.md).

![Cache Azure para o nível de preços do Redis](./media/cache-configure/pricing-tier.png)

<a name="cluster-size"></a>

### <a name="redis-cluster-size"></a>Tamanho do aglomerado de redis
Clique no Tamanho do **Cluster** para alterar o tamanho do cluster para uma cache premium de corrida com clustering ativado.

![Tamanho do cluster](./media/cache-configure/redis-cache-redis-cluster-size.png)

Para alterar o tamanho do cluster, utilize o slider ou escreva um número entre 1 e 10 na caixa de texto **de contagem de fragmentos** e clique **em OK** para guardar.

> [!IMPORTANT]
> O agrupamento Redis só está disponível para caches Premium. Para obter mais informações, consulte [Como configurar o agrupamento para uma Cache Premium Azure para Redis.](cache-how-to-premium-clustering.md)
>
>


### <a name="redis-data-persistence"></a>Persistência de dados de Redis
Clique **em Persistência de Dados** para ativar, desativar ou configurar a persistência de dados para a sua cache premium. Azure Cache for Redis oferece persistência redis usando a persistência de RDB ou a persistência da AOF.

Para obter mais informações, consulte [Como configurar a persistência para uma Cache Premium Azure para Redis](cache-how-to-premium-persistence.md).


> [!IMPORTANT]
> A persistência de dados redis só está disponível para caches Premium.
>
>

### <a name="schedule-updates"></a>Agendar atualizações
A lâmina **de atualização do Programa** permite-lhe designar uma janela de manutenção para atualizações do servidor Redis para o seu cache.

> [!IMPORTANT]
> A janela de manutenção aplica-se apenas às atualizações do servidor Redis e não a quaisquer atualizações ou atualizações do Azure ao sistema operativo dos VMs que acolhem a cache.
>
>

![Agendar atualizações](./media/cache-configure/redis-schedule-updates.png)

Para especificar uma janela de manutenção, verifique os dias desejados e especifique a hora de início da janela de manutenção para cada dia e clique **em OK** . O tempo da janela de manutenção está na UTC.

> [!IMPORTANT]
> A funcionalidade **de atualização do Schedule** só está disponível para caches de nível Premium. Para obter mais informações e instruções, consulte [Azure Cache para administração Redis - Agendar atualizações](cache-administration.md#schedule-updates).
>
>

### <a name="geo-replication"></a>Georreplicação

A lâmina **de geo-replicação** fornece um mecanismo para ligar dois Cache Azure de nível Premium para instâncias Redis. Uma cache é designada como a cache principal ligada, e a outra como a cache ligada secundária. A cache ligada secundária torna-se apenas de leitura, e os dados escritos na cache primária são replicados para a cache ligada secundária. Esta funcionalidade pode ser usada para replicar uma cache em todas as regiões de Azure.

> [!IMPORTANT]
> **A geo-replicação** só está disponível para caches de nível Premium. Para obter mais informações e instruções, consulte [Como configurar a geo-replicação para Azure Cache para Redis](cache-how-to-geo-replication.md).
>
>

### <a name="virtual-network"></a>Rede Virtual
A secção **Rede Virtual** permite-lhe configurar as definições de rede virtual para o seu cache. Para obter informações sobre a criação de uma cache premium com suporte VNET e atualizar as suas definições, consulte [Como configurar o Suporte de Rede Virtual para uma Cache Premium Azure para Redis](cache-how-to-premium-vnet.md).

> [!IMPORTANT]
> As definições de rede virtuais só estão disponíveis para caches premium que foram configurados com suporte VNET durante a criação de cache.
>
>

### <a name="firewall"></a>Firewall

A configuração das regras de firewall está disponível para todos os níveis Azure Cache para os níveis Redis.

Clique em **Firewall** para ver e configurar as regras de firewall para cache.

![Firewall](./media/cache-configure/redis-firewall-rules.png)

Pode especificar as regras de firewall com um intervalo de endereço IP inicial e final. Quando as regras de firewall são configuradas, apenas as ligações do cliente a partir das gamas de endereços IP especificadas podem ligar-se à cache. Quando uma regra de firewall é guardada, há um curto atraso antes da regra ser eficaz. Este atraso é tipicamente inferior a um minuto.

> [!IMPORTANT]
> As ligações da Cache Azure para sistemas de monitorização Redis são sempre permitidas, mesmo que as regras de firewall estejam configuradas.
>
>

### <a name="properties"></a>Propriedades
Clique **em Propriedades** para ver informações sobre o seu cache, incluindo o ponto final de cache e portas.

![Cache Azure para Propriedades Redis](./media/cache-configure/redis-cache-properties.png)

### <a name="locks"></a>Bloqueios
A secção **Fechaduras** permite-lhe bloquear uma subscrição, grupo de recursos ou recurso para evitar que outros utilizadores da sua organização apaguem ou modifiquem acidentalmente recursos críticos. Para obter mais informações, consulte [Bloquear recursos com o Azure Resource Manager](../azure-resource-manager/management/lock-resources.md).

### <a name="automation-script"></a>Script de automatização

Clique **no script de automação** para construir e exportar um modelo dos seus recursos implantados para futuras implementações. Para obter mais informações sobre o trabalho com os modelos, consulte [implementar recursos com modelos de Gestor de Recursos Azure](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="administration-settings"></a>Definições de administração
As definições na secção **Administração** permitem-lhe executar as seguintes tarefas administrativas para o seu cache.

![Administração](./media/cache-configure/redis-cache-administration.png)

* [Importar dados](#importexport)
* [Exportar dados](#importexport)
* [Reiniciar](#reboot)


### <a name="importexport"></a>Importação/Exportação
Import/Export é uma cache Azure para a operação de gestão de dados Redis, que permite importar e exportar dados da cache importando e exportando uma cache Azure para Redis Database (RDB) de uma cache premium para uma bolha de página numa Conta de Armazenamento Azure. A Importação/Exportação permite-lhe migrar entre diferentes Cache Azure para instâncias Redis ou povoar a cache com dados antes de usar.

A importação pode ser usada para trazer ficheiros RDB compatíveis com Redis de qualquer servidor Redis em qualquer nuvem ou ambiente, incluindo Redis em execução em Linux, Windows ou qualquer fornecedor de nuvem, como Amazon Web Services e outros. Importar dados é uma maneira fácil de criar uma cache com dados pré-povoados. Durante o processo de importação, a Azure Cache for Redis carrega os ficheiros RDB do armazenamento Azure na memória e, em seguida, insere as chaves na cache.

A exportação permite exportar os dados armazenados em Cache Azure para Redis para ficheiros RDB compatíveis com Redis. Pode utilizar esta funcionalidade para mover dados de um Cache Azure para redis para outro ou para outro servidor Redis. Durante o processo de exportação, é criado um ficheiro temporário no VM que acolhe a Cache Azure para a instância do servidor Redis, e o ficheiro é enviado para a conta de armazenamento designada. Quando a operação de exportação estiver concluída com um estado de sucesso ou de insucesso, o ficheiro temporário é suprimido.

> [!IMPORTANT]
> A importação/exportação só está disponível para caches de nível Premium. Para obter mais informações e instruções, consulte [os dados de Importação e Exportação em Cache Azure para Redis](cache-how-to-import-export-data.md).
>
>

### <a name="reboot"></a>Reiniciar
A lâmina **Reboot** permite-lhe reiniciar os nós da sua cache. Esta capacidade de reinicialização permite-lhe testar a sua aplicação de resiliência se houver uma falha de um nó de cache.

![Reiniciar](./media/cache-configure/redis-cache-reboot.png)

Se tiver uma cache premium com clustering ativado, pode selecionar quais os fragmentos da cache para reiniciar.

![Screenshot que mostra onde selecionar quais os fragmentos da cache para reiniciar.](./media/cache-configure/redis-cache-reboot-cluster.png)

Para reiniciar um ou mais nós da sua cache, selecione os nós desejados e clique em **Reboot** . Se tiver uma cache premium com clustering ativado, selecione o fragmento para reiniciar e, em seguida, clique em **Reboot** . Após alguns minutos, o (s) nó(s) selecionado reinicia e volta a estar on-line alguns minutos depois.

> [!IMPORTANT]
> Reboot está agora disponível para todos os níveis de preços. Para obter mais informações e instruções, consulte [Azure Cache para administração Redis - Reboot](cache-administration.md#reboot).
>
>


## <a name="monitoring"></a>Monitorização

A secção **de Monitorização** permite-lhe configurar diagnósticos e monitorização para o seu Cache Azure para Redis.
Para obter mais informações sobre a cache de Azure para monitorização e diagnóstico de Redis, consulte [Como monitorizar a Cache Azure para Redis](cache-how-to-monitor.md).

![Diagnóstico](./media/cache-configure/redis-cache-diagnostics.png)

* [Métricas redis](#redis-metrics)
* [Regras de alerta](#alert-rules)
* [Diagnóstico](#diagnostics)

### <a name="redis-metrics"></a>Métricas redis
Clique nas **métricas redis** para [ver as métricas](cache-how-to-monitor.md#view-cache-metrics) para o seu cache.

### <a name="alert-rules"></a>Regras de alerta

Clique **em regras de alerta** para configurar alertas com base em Cache Azure para métricas Redis. Para mais informações, consulte [Alertas.](cache-how-to-monitor.md#alerts)

### <a name="diagnostics"></a>Diagnóstico

Por predefinição, as métricas de cache no Azure Monitor são [armazenadas durante 30 dias](../azure-monitor/platform/data-platform-metrics.md) e depois eliminadas. Para persistir as suas métricas de cache por mais de 30 dias, clique em **Diagnósticos** para [configurar a conta de armazenamento](cache-how-to-monitor.md#export-cache-metrics) utilizada para armazenar diagnósticos de cache.

>[!NOTE]
>Além de arquivar as suas métricas de cache para armazenamento, também pode [transmiti-las para um hub de Eventos ou enviá-las para registos do Azure Monitor](../azure-monitor/platform/stream-monitoring-data-event-hubs.md).
>
>

## <a name="support--troubleshooting-settings"></a>Suporte & configurações de resolução de problemas
As definições na secção **De Resolução de Problemas De suporte +** fornecem-lhe opções para resolver problemas com o seu cache.

![Suporte e resolução de problemas](./media/cache-configure/redis-cache-support-troubleshooting.png)

* [Estado de funcionamento dos recursos](#resource-health)
* [Novo pedido de apoio](#new-support-request)

### <a name="resource-health"></a>Estado de funcionamento de recursos
**A saúde dos recursos** observa o seu recurso e diz-lhe se está a funcionar como esperado. Para obter mais informações sobre o serviço de saúde Azure Resource, consulte [a visão geral da saúde do Recurso Azure.](../service-health/resource-health-overview.md)

> [!NOTE]
> Atualmente, a saúde dos recursos não é capaz de reportar sobre a saúde de Azure Cache para os casos de Redis hospedados numa rede virtual. Para obter mais informações, consulte [Todas as funcionalidades de cache funcionam ao hospedar uma cache num VNET?](cache-how-to-premium-vnet.md#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)
>
>

### <a name="new-support-request"></a>Novo pedido de suporte
Clique em **Novo pedido de suporte** para abrir um pedido de suporte para o seu cache.





## <a name="default-redis-server-configuration"></a>Configuração do servidor Redis padrão
A nova cache Azure para instâncias Redis são configuradas com os seguintes valores de configuração padrão do Redis:

> [!NOTE]
> As definições desta secção não podem ser alteradas utilizando o `StackExchange.Redis.IServer.ConfigSet` método. Se este método for chamado com um dos comandos desta secção, é lançada uma exceção semelhante ao seguinte exemplo:  
>
> `StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
>
> Quaisquer valores configuráveis, como **a política de memória máxima,** são configuráveis através do portal Azure ou de ferramentas de gestão de linhas de comando, como o Azure CLI ou o PowerShell.
>
>

| Definição | Valor predefinido | Descrição |
| --- | --- | --- |
| `databases` |16 |O número predefinido das bases de dados é de 16, mas pode configurar um número diferente com base no nível de preços. <sup>1</sup> A base de dados predefinitiva é DB 0, pode selecionar uma diferente numa base por ligação, utilizando `connection.GetDatabase(dbid)` onde está um número entre e `dbid` `0` `databases - 1` . |
| `maxclients` |Depende do nível de preços<sup>2</sup> |Este valor é o número máximo de clientes conectados permitido ao mesmo tempo. Uma vez atingido o limite, a Redis fecha todas as novas ligações, devolvendo um erro de "número máximo de clientes atingidos". |
| `maxmemory-policy` |`volatile-lru` |A política maxmemory é a definição de como o Redis seleciona o que remover quando `maxmemory` (o tamanho da oferta de cache que selecionou quando criou a cache) é alcançado. Com a Cache Azure para Redis, a definição predefinida é `volatile-lru` , que remove as teclas com um conjunto de expiração usando um algoritmo LRU. Esta definição pode ser configurada no portal Azure. Para mais informações, consulte [as políticas de Memória.](#memory-policies) |
| `maxmemory-samples` |3 |Para guardar a memória, os algoritmos LRU e TTL mínimos são algoritmos aproximados em vez de algoritmos precisos. Por defeito, o Redis verifica três chaves e escolhe a que foi usada menos recentemente. |
| `lua-time-limit` |5000 |Tempo de execução máxima de um guião lua em milissegundos. Se o tempo máximo de execução for atingido, o Redis regista que um script ainda está em execução após o tempo máximo permitido, e começa a responder a perguntas com um erro. |
| `lua-event-limit` |500 |Tamanho máximo da fila do evento do script. |
| `client-output-buffer-limit` `normalclient-output-buffer-limit` `pubsub` |0 0 032mb 8mb 60 |Os limites do tampão de saída do cliente podem ser usados para forçar a desconexão de clientes que não estão a ler dados do servidor com rapidez suficiente por alguma razão (uma razão comum é que um cliente Pub/Sub não pode consumir mensagens tão rápido quanto o editor pode produzi-los). Para obter mais informações, veja [https://redis.io/topics/clients](https://redis.io/topics/clients). |

<a name="databases"></a>
<sup>1</sup> O limite `databases` para é diferente para cada Cache Azure para o nível de preços redis e pode ser definido na criação de cache. Se não `databases` for especificada qualquer definição durante a criação da cache, o padrão é 16.

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
  * Todos os caches premium com cluster Redis ativados - O cluster Redis só suporta a utilização da base de dados 0, pelo que o `databases` limite para qualquer cache premium com cluster Redis ativado é efetivamente 1 e o comando [Select](https://redis.io/commands/select) não é permitido. Para mais informações, consulte [Preciso de fazer alterações à aplicação do meu cliente para utilizar o clustering?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)

Para obter mais informações sobre bases de dados, veja [o que são as bases de dados do Redis?](cache-development-faq.md#what-are-redis-databases)

> [!NOTE]
> A `databases` definição só pode ser configurada durante a criação de cache e apenas utilizando PowerShell, CLI ou outros clientes de gestão. Para um exemplo de configuração durante a `databases` criação de cache usando PowerShell, consulte [New-AzRedisCache](cache-how-to-manage-redis-cache-powershell.md#databases).
>
>

<a name="maxclients"></a>
<sup>2</sup> `maxclients` é diferente para cada Cache Azure para o nível de preços redis.

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
> Embora cada tamanho de cache permita *até* um certo número de ligações, cada ligação ao Redis tem sobrecarga associada a ele. Um exemplo de tal sobrecarga seria o uso de CPU e memória como resultado da encriptação TLS/SSL. O limite máximo de ligação para um dado tamanho de cache pressupõe uma cache levemente carregada. Se a carga da sobrecarga de ligação *mais* a carga das operações do cliente exceder a capacidade do sistema, a cache pode experimentar problemas de capacidade mesmo que não tenha excedido o limite de ligação para o tamanho atual da cache.
>
>



## <a name="redis-commands-not-supported-in-azure-cache-for-redis"></a>Comandos Redis não apoiados em Cache Azure para Redis
> [!IMPORTANT]
> Como a configuração e gestão do Azure Cache para instâncias Redis é gerida pela Microsoft, os seguintes comandos são desativados. Se tentar invocá-los, recebe uma mensagem de erro semelhante a `"(error) ERR unknown command"` .
>
> * BGREWRITEAOF
> * BGSAVE
> * CONFIG
> * DEPURAR
> * MIGRAR
> * GUARDAR
> * ENCERRAMENTO
> * SLAVEOF
> * CLUSTER - Os comandos de escrita de cluster são desativados, mas os comandos de cluster apenas de leitura são permitidos.
>
>

Para mais informações sobre os comandos Redis, [https://redis.io/commands](https://redis.io/commands) consulte.

## <a name="redis-console"></a>Consola Redis
Pode emitir comandos de forma segura para o seu Azure Cache para instâncias Redis utilizando a **Consola Redis** , que está disponível no portal Azure para todos os níveis de cache.

> [!IMPORTANT]
> - A Consola Redis não funciona com [o VNET.](cache-how-to-premium-vnet.md) Quando a sua cache faz parte de um VNET, apenas os clientes do VNET podem aceder à cache. Como a Consola Redis funciona no seu navegador local, que está fora do VNET, não consegue ligar-se ao seu cache.
> - Nem todos os comandos Redis são suportados em Azure Cache para Redis. Para obter uma lista de comandos Redis que são desativados para Azure Cache para Redis, consulte os comandos Redis anteriores [não suportados na Cache Azure para](#redis-commands-not-supported-in-azure-cache-for-redis) a secção Redis. Para mais informações sobre os comandos Redis, [https://redis.io/commands](https://redis.io/commands) consulte.
>
>

Para aceder à Consola Redis, clique em **Consola** a partir da **Cache Azure para lâmina Redis.**

![Screenshot que realça o botão Consola.](./media/cache-configure/redis-console-menu.png)

Para emitir comandos contra a sua instância de cache, digite o comando desejado na consola.

![O screenshot thas mostra a Consola Redis com o comando de entrada e os resultados.](./media/cache-configure/redis-console.png)


### <a name="using-the-redis-console-with-a-premium-clustered-cache"></a>Utilização da Consola Redis com uma cache agrupada premium

Ao utilizar a Consola Redis com uma cache agrupada premium, pode emitir comandos a um único fragmento da cache. Para emitir um comando a um fragmento específico, ligue-se primeiro ao fragmento desejado clicando-o no apanhador de fragmentos.

![Consola Redis](./media/cache-configure/redis-console-premium-cluster.png)

Se tentar aceder a uma chave que é armazenada num fragmento diferente do fragmento ligado, receberá uma mensagem de erro semelhante à seguinte mensagem:

```
shard1>get myKey
(error) MOVED 866 13.90.202.154:13000 (shard 0)
```

No exemplo anterior, o fragmento 1 é o fragmento selecionado, mas `myKey` está localizado em fragmento 0, como indicado pela parte da `(shard 0)` mensagem de erro. Neste exemplo, para aceder `myKey` , selecione shard 0 utilizando o apanhador de fragmentos e, em seguida, emite o comando pretendido.


## <a name="move-your-cache-to-a-new-subscription"></a>Mude o seu cache para uma nova subscrição
Pode mover o seu cache para uma nova subscrição clicando em **Move** .

![Mover cache Azure para Redis](./media/cache-configure/redis-cache-move.png)

Para obter informações sobre a deslocação de recursos de um grupo de recursos para outro, e de uma subscrição para outra, consulte [mover recursos para novo grupo de recursos ou subscrição](../azure-resource-manager/management/move-resource-group-and-subscription.md).

## <a name="next-steps"></a>Passos seguintes
* Para mais informações sobre trabalhar com os comandos redis, veja [como posso comandar redis?](cache-development-faq.md#how-can-i-run-redis-commands)