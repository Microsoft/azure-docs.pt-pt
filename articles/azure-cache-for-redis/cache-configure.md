---
title: Como configurar o cache do Azure para Redis
description: Entenda a configuração padrão do Redis para o cache do Azure para Redis e saiba como configurar seu cache do Azure para instâncias do Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 08/22/2017
ms.author: yegu
ms.openlocfilehash: f10be8efcd2d8e838b4b5f62310eb405f6ed0158
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76714631"
---
# <a name="how-to-configure-azure-cache-for-redis"></a>Como configurar o cache do Azure para Redis
Este tópico descreve as configurações disponíveis para o cache do Azure para instâncias Redis. Este tópico também aborda a configuração padrão do servidor Redis para o cache do Azure para instâncias do Redis.

> [!NOTE]
> Para obter mais informações sobre configurar e utilizar funcionalidades de cache premium, consulte [como configurar a persistência,](cache-how-to-premium-persistence.md) [como configurar](cache-how-to-premium-clustering.md)o agrupamento e [como configurar o suporte à Rede Virtual](cache-how-to-premium-vnet.md).
>
>

## <a name="configure-azure-cache-for-redis-settings"></a>Configurar o cache do Azure para configurações de Redis
[!INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

As definições de Azure Cache para Redis são vistas e configuradas na **cache Azure para** lâmina Redis utilizando o Menu de **Recursos**.

![Cache do Azure para configurações de Redis](./media/cache-configure/redis-cache-settings.png)

Pode visualizar e configurar as seguintes definições utilizando o **Menu de Recursos**.

* [Descrição geral](#overview)
* [Registo de atividades](#activity-log)
* [Controlo de acesso (IAM)](#access-control-iam)
* [Etiquetas](#tags)
* [Diagnosticar e resolver problemas](#diagnose-and-solve-problems)
* [Definições](#settings)
    * [Chaves de acesso](#access-keys)
    * [Configurações avançadas](#advanced-settings)
    * [Azure Cache para Redis Advisor](#azure-cache-for-redis-advisor)
    * [Dimensionar](#scale)
    * [Tamanho do cluster](#cluster-size)
    * [Persistência de dados](#redis-data-persistence)
    * [Atualizações agendadas](#schedule-updates)
    * [Georreplicação](#geo-replication)
    * [Rede Virtual](#virtual-network)
    * [Firewall](#firewall)
    * [Propriedades](#properties)
    * [Fechaduras](#locks)
    * [Roteiro de automação](#automation-script)
* Administração
    * [Importar dados](#importexport)
    * [Exportar dados](#importexport)
    * [Reiniciar](#reboot)
* [Monitorização](#monitoring)
    * [Métricas redis](#redis-metrics)
    * [Regras de alerta](#alert-rules)
    * [Diagnóstico](#diagnostics)
* Suporte & configurações de solução de problemas
    * [Saúde dos recursos](#resource-health)
    * [Novo pedido de apoio](#new-support-request)


## <a name="overview"></a>Descrição geral

**A visão geral** fornece-lhe informações básicas sobre o seu cache, tais como nome, portas, nível de preços e métricas de cache selecionadas.

### <a name="activity-log"></a>Registo de atividades

Clique no **registo de atividade** para visualizar as ações realizadas na sua cache. Você também pode usar a filtragem para expandir essa exibição para incluir outros recursos. Para obter mais informações sobre o trabalho com registos de auditoria, consulte [operações de auditoria com o Gestor de Recursos.](../azure-resource-manager/management/view-activity-logs.md) Para obter mais informações sobre a monitorização do Azure Cache para eventos Redis, consulte [Operações e alertas](cache-how-to-monitor.md#operations-and-alerts).

### <a name="access-control-iam"></a>Controlo de acesso (IAM)

A secção de controlo de **acesso (IAM)** fornece suporte para o controlo de acesso baseado em funções (RBAC) no portal Azure. Essa configuração ajuda as organizações a atender seus requisitos de gerenciamento de acesso de forma simples e precisa. Para mais informações, consulte [o controlo de acesso baseado em role no portal Azure](../role-based-access-control/role-assignments-portal.md).

### <a name="tags"></a>Etiquetas

A secção **Tags** ajuda-o a organizar os seus recursos. Para obter mais informações, veja [Utilizar etiquetas para organizar os recursos do Azure](../azure-resource-manager/management/tag-resources.md).


### <a name="diagnose-and-solve-problems"></a>Diagnosticar e resolver problemas

Clique em **Diagnosticar e resolver problemas** a serem fornecidos com questões e estratégias comuns para resolvê-los.



## <a name="settings"></a>Definições
A secção **Definições** permite-lhe aceder e configurar as seguintes definições para a sua cache.

* [Chaves de acesso](#access-keys)
* [Configurações avançadas](#advanced-settings)
* [Azure Cache para Redis Advisor](#azure-cache-for-redis-advisor)
* [Dimensionar](#scale)
* [Tamanho do cluster](#cluster-size)
* [Persistência de dados](#redis-data-persistence)
* [Atualizações agendadas](#schedule-updates)
* [Georreplicação](#geo-replication)
* [Rede Virtual](#virtual-network)
* [Firewall](#firewall)
* [Propriedades](#properties)
* [Fechaduras](#locks)
* [Roteiro de automação](#automation-script)



### <a name="access-keys"></a>Chaves de acesso
Clique em **teclas de acesso** para visualizar ou regenerar as teclas de acesso para a sua cache. Essas chaves são usadas pelos clientes que se conectam ao seu cache.

![Cache do Azure para chaves de acesso Redis](./media/cache-configure/redis-cache-manage-keys.png)

### <a name="advanced-settings"></a>Definições avançadas
As seguintes definições estão configuradas na lâmina de **definições Avançada.**

* [Portas de Acesso](#access-ports)
* [Políticas de memória](#memory-policies)
* [Notificações do espaço-chave (configurações avançadas)](#keyspace-notifications-advanced-settings)

#### <a name="access-ports"></a>Portas de acesso
Por predefinição, o acesso não SSL está desativado para as novas caches. Para ativar a porta não-SSL, clique **em Não** **permitir o acesso apenas através do SSL** na lâmina de **definições Avançadas** e, em seguida, clique em **Guardar**.

> [!NOTE]
> O acesso SSL ao cache do Azure para Redis dá suporte a TLS 1,0, 1,1 e 1,2 no momento, mas as versões 1,0 e 1,1 estão sendo desativadas em breve.  Leia a nossa [página Remove TLS 1.0 e 1.1](cache-remove-tls-10-11.md) para mais detalhes.

![Cache do Azure para portas de acesso Redis](./media/cache-configure/redis-cache-access-ports.png)

<a name="maxmemory-policy-and-maxmemory-reserved"></a>
#### <a name="memory-policies"></a>Políticas de memória
A **política Maxmemory,** **reservada à memória máxima**e as definições **reservadas** à memória máxima na lâmina de **configuração avançada** configuram as políticas de memória para a cache.

![Cache do Azure para a política Redis MaxMemory](./media/cache-configure/redis-cache-maxmemory-policy.png)

A política de **maxmemory** configura a política de despejo para o cache e permite-lhe escolher entre as seguintes políticas de despejo:

* `volatile-lru` - Esta é a política de despejo por defeito.
* `allkeys-lru`
* `volatile-random`
* `allkeys-random`
* `volatile-ttl`
* `noeviction`

Para obter mais informações sobre as políticas `maxmemory`, consulte as políticas de [despejo.](https://redis.io/topics/lru-cache#eviction-policies)

A definição **reservada à memória máxima** confunde a quantidade de memória, em MB, reservada para operações não cache, como a replicação durante a failover. Definir esse valor permite que você tenha uma experiência de servidor Redis mais consistente quando a carga varia. Esse valor deve ser definido como mais alto para cargas de trabalho que são pesadas de gravação. Quando a memória é reservada para essas operações, ela não está disponível para armazenamento de dados armazenados em cache.

A definição reservada à **memória máxima** confunde a quantidade de memória em MB reservada para acomodar para fragmentação da memória. Definir esse valor permite que você tenha uma experiência de servidor Redis mais consistente quando o cache está cheio ou próximo de completo e a taxa de fragmentação é alta. Quando a memória é reservada para essas operações, ela não está disponível para armazenamento de dados armazenados em cache.

Uma coisa a considerar na escolha de um novo valor de reserva de memória **(reservado** à memória máxima ou reservado à **memória máxima**) é como esta mudança pode afetar uma cache que já está a funcionar com grandes quantidades de dados na si. Por exemplo, se você tiver um cache de 53 GB com 49 GB de dados, altere o valor de reserva para 8 GB, essa alteração removerá a memória máxima disponível para o sistema até 45 GB. Se os seus `used_memory` atuais ou os seus valores `used_memory_rss` forem superiores ao novo limite de 45 GB, então o sistema terá de despejar dados até que tanto `used_memory` como `used_memory_rss` estejam abaixo dos 45 GB. A remoção pode aumentar a carga do servidor e a fragmentação da memória. Para obter mais informações sobre métricas de cache, como `used_memory` e `used_memory_rss`, consulte [métricas disponíveis e intervalos de reporte.](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)

> [!IMPORTANT]
> As definições **reservadas** à memória máxima e **à máxima fragmentação** estão disponíveis apenas para caches Standard e Premium.
>
>

#### <a name="keyspace-notifications-advanced-settings"></a>Notificações de keyspace (configurações avançadas)
As notificações do espaço de teclado Redis estão configuradas na lâmina de **definições Avançada.** As notificações de keyspace permitem que os clientes recebam notificações quando determinados eventos ocorrerem.

![Cache do Azure para configurações avançadas do Redis](./media/cache-configure/redis-cache-advanced-settings.png)

> [!IMPORTANT]
> As notificações do keyspace e a definição **de eventos espaço-chave notificada** só estão disponíveis para caches Standard e Premium.
>
>

Para mais informações, consulte [Redis Keyspace Notifications](https://redis.io/topics/notifications). Para obter o código da amostra, consulte o ficheiro [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) na amostra do [mundo Hello.](https://github.com/rustd/RedisSamples/tree/master/HelloWorld)


<a name="recommendations"></a>
## <a name="azure-cache-for-redis-advisor"></a>Cache do Azure para o Redis Advisor
A lâmina **Azure Cache for Redis Advisor** apresenta recomendações para a sua cache. Durante as operações normais, nenhuma recomendação é exibida.

![Recomendações](./media/cache-configure/redis-cache-no-recommendations.png)

Se ocorrerem quaisquer condições durante as operações da sua cache, tais como alta utilização da memória, largura de banda da rede ou carga do servidor, é apresentado um alerta no Cache Azure para a lâmina **Redis.**

![Recomendações](./media/cache-configure/redis-cache-recommendations-alert.png)

Mais informações podem ser encontradas na lâmina de **recomendações.**

![Recomendações](./media/cache-configure/redis-cache-recommendations.png)

Pode monitorizar estas métricas nos gráficos de [monitorização](cache-how-to-monitor.md#monitoring-charts) e nas [tabelas](cache-how-to-monitor.md#usage-charts) de utilização do Azure Cache para a lâmina **Redis.**

Cada tipo de preço tem limites diferentes para conexões de cliente, memória e largura de banda. Se o seu cache se aproximar da capacidade máxima para essas métricas em um período de tempo prolongado, uma recomendação será criada. Para obter mais informações sobre as métricas e limites revistos pela ferramenta **Recomendações,** consulte o quadro seguinte:

| Cache do Azure para métrica Redis | Mais informações |
| --- | --- |
| Utilização da largura de banda da rede |[Desempenho da cache - largura de banda disponível](cache-faq.md#cache-performance) |
| Clientes conectados |[Configuração do servidor Predefinido Redis - clientes max](#maxclients) |
| Carga do servidor |[Gráficos de utilização - Carga do Servidor Redis](cache-how-to-monitor.md#usage-charts) |
| Uso de memória |[Desempenho da cache - tamanho](cache-faq.md#cache-performance) |

Para atualizar a sua cache, clique **em Upgrade agora** para alterar o nível de preços e [escalar](#scale) a sua cache. Para obter mais informações sobre a escolha de um nível de preços, veja [o que azure cache para redis oferecendo e tamanho devo usar?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)


### <a name="scale"></a>Escala
Clique em **Escala** para visualizar ou alterar o nível de preços para a sua cache. Para obter mais informações sobre escala, consulte [Como escalar o Cache Azure para Redis](cache-how-to-scale.md).

![Cache do Azure para tipo de preço Redis](./media/cache-configure/pricing-tier.png)

<a name="cluster-size"></a>

### <a name="redis-cluster-size"></a>Tamanho do cluster Redis
Clique no tamanho do **cluster** para alterar o tamanho do cluster para uma cache premium em execução com clustering ativado.

![Tamanho do cluster](./media/cache-configure/redis-cache-redis-cluster-size.png)

Para alterar o tamanho do cluster, utilize o slider ou escreva um número entre 1 e 10 na caixa de texto **de contagem de fragmentos** e clique em **OK** para guardar.

> [!IMPORTANT]
> O clustering do Redis só está disponível para caches Premium. Para mais informações, consulte [Como configurar o agrupamento para um Cache Premium Azure para Redis](cache-how-to-premium-clustering.md).
>
>


### <a name="redis-data-persistence"></a>Redis persistência de dados
Clique na **persistência de Dados** para ativar, desativar ou configurar a persistência de dados para a sua cache premium. O Azure Cache for Redis oferece persistência redis usando a [persistência de RDB](cache-how-to-premium-persistence.md#configure-rdb-persistence) ou [persistência AOF](cache-how-to-premium-persistence.md#configure-aof-persistence).

Para mais informações, consulte [Como configurar a persistência de um Cache Premium Azure para Redis](cache-how-to-premium-persistence.md).


> [!IMPORTANT]
> A persistência de dados do Redis só está disponível para caches Premium.
>
>

### <a name="schedule-updates"></a>Agendar atualizações
A lâmina de **atualizações de Agenda** permite-lhe designar uma janela de manutenção para atualizações do servidor Redis para a sua cache.

> [!IMPORTANT]
> A janela de manutenção aplica-se somente a atualizações do servidor Redis e não a atualizações do Azure ou a atualizações do sistema operacional das VMs que hospedam o cache.
>
>

![Agendar atualizações](./media/cache-configure/redis-schedule-updates.png)

Para especificar uma janela de manutenção, verifique os dias desejados e especifique a hora de início da janela de manutenção para cada dia e clique em **OK**. A hora da janela de manutenção está em UTC.

> [!IMPORTANT]
> A funcionalidade de **atualizações do Programação** só está disponível para caches de nível Premium. Para mais informações e instruções, consulte [Azure Cache para administração Redis - Agendar atualizações](cache-administration.md#schedule-updates).
>
>

### <a name="geo-replication"></a>Georreplicação

A lâmina **de geo-replicação** fornece um mecanismo para ligar dois acabamentos Premium Azure Cache para instâncias Redis. Um cache é designado como o cache vinculado primário e o outro como o cache vinculado secundário. O cache vinculado secundário torna-se somente leitura e os dados gravados no cache primário são replicados para o cache vinculado secundário. Essa funcionalidade pode ser usada para replicar um cache em regiões do Azure.

> [!IMPORTANT]
> **A geo-replicação** só está disponível para caches de nível Premium. Para mais informações e instruções, consulte [Como configurar a Geo-replicação para Azure Cache for Redis](cache-how-to-geo-replication.md).
>
>

### <a name="virtual-network"></a>Rede virtual
A secção **Rede Virtual** permite-lhe configurar as definições de rede virtual para a sua cache. Para obter informações sobre a criação de um cache premium com suporte VNET e atualizar as suas definições, consulte como configurar o [Suporte de Rede Virtual para um Cache Premium Azure para Redis](cache-how-to-premium-vnet.md).

> [!IMPORTANT]
> As configurações de rede virtual só estão disponíveis para caches Premium que foram configurados com suporte VNET durante a criação do cache.
>
>

### <a name="firewall"></a>Firewall

A configuração de regras de firewall está disponível para todo o cache do Azure para camadas Redis.

Clique em **Firewall** para visualizar e configurar as regras de firewall para cache.

![Firewall](./media/cache-configure/redis-firewall-rules.png)

Você pode especificar regras de firewall com um intervalo de endereços IP inicial e final. Quando as regras de firewall são configuradas, somente as conexões de cliente dos intervalos de endereços IP especificados podem se conectar ao cache. Quando uma regra de firewall é salva, há um pequeno atraso antes que a regra seja efetiva. Esse atraso é normalmente menos de um minuto.

> [!IMPORTANT]
> As conexões do cache do Azure para sistemas de monitoramento Redis são sempre permitidas, mesmo se as regras de firewall estiverem configuradas.
>
>

### <a name="properties"></a>Propriedades
Clique em **Propriedades** para ver informações sobre a sua cache, incluindo o ponto final de cache e portas.

![Cache do Azure para propriedades Redis](./media/cache-configure/redis-cache-properties.png)

### <a name="locks"></a>Bloqueios
A secção **Locks** permite-lhe bloquear uma subscrição, grupo de recursos ou recurso para evitar que outros utilizadores da sua organização apagam ou modifiquem acidentalmente recursos críticos. Para obter mais informações, consulte [Bloquear recursos com o Azure Resource Manager](../azure-resource-manager/management/lock-resources.md).

### <a name="automation-script"></a>Roteiro de automação

Clique no **script automation** para construir e exportar um modelo dos seus recursos implantados para futuras implementações. Para obter mais informações sobre o trabalho com modelos, consulte implementar recursos com modelos de [Gestor de Recursos Azure](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="administration-settings"></a>Configurações de administração
As definições na secção **Administração** permitem-lhe executar as seguintes tarefas administrativas para a sua cache.

![Administração](./media/cache-configure/redis-cache-administration.png)

* [Importar dados](#importexport)
* [Exportar dados](#importexport)
* [Reiniciar](#reboot)


### <a name="importexport"></a>Importação/Exportação
A importação/exportação é um cache do Azure para a operação de gerenciamento de dados do Redis, que permite importar e exportar dados no cache importando e exportando um instantâneo do RDB (cache do Azure para o desbanco de Redis) de um cache Premium para um blob de páginas em uma conta de armazenamento do Azure. A importação/exportação permite que você migre entre diferentes cache do Azure para instâncias Redis ou preencha o cache com os dados antes de usá-los.

A importação pode ser usada para colocar arquivos de RDB compatíveis com Redis de qualquer servidor Redis em execução em qualquer nuvem ou ambiente, incluindo Redis em execução no Linux, Windows ou qualquer provedor de nuvem, como Amazon Web Services e outros. A importação de dados é uma maneira fácil de criar um cache com dados preenchidos previamente. Durante o processo de importação, o cache do Azure para Redis carrega os arquivos de RDB do armazenamento do Azure na memória e, em seguida, insere as chaves no cache.

A exportação permite que você exporte os dados armazenados no cache do Azure para Redis para arquivos RDB compatíveis com o Redis. Você pode usar esse recurso para mover dados de um cache do Azure para instância Redis para outro ou para outro servidor Redis. Durante o processo de exportação, um arquivo temporário é criado na VM que hospeda o cache do Azure para a instância do servidor Redis e o arquivo é carregado para a conta de armazenamento designada. Quando a operação de exportação for concluída com um status de êxito ou falha, o arquivo temporário será excluído.

> [!IMPORTANT]
> A importação/exportação só está disponível para caches da camada Premium. Para obter mais informações e instruções, consulte [os dados de importação e exportação em Azure Cache for Redis](cache-how-to-import-export-data.md).
>
>

### <a name="reboot"></a>Reiniciar
A lâmina **Reboot** permite reiniciar os nódosos da sua cache. Essa capacidade de reinicialização permite testar seu aplicativo quanto à resiliência se houver uma falha de um nó de cache.

![Reiniciar](./media/cache-configure/redis-cache-reboot.png)

Se você tiver um cache Premium com clustering habilitado, poderá selecionar quais fragmentos do cache devem ser reinicializados.

![Reiniciar](./media/cache-configure/redis-cache-reboot-cluster.png)

Para reiniciar um ou mais nós da sua cache, selecione os nós desejados e clique em **Reiniciar**. Se tiver uma cache premium com clustering ativado, selecione o fragmento(s) para reiniciar e, em seguida, clique em **Reiniciar**. Após alguns minutos, os nós selecionados são reinicializados e ficam online novamente alguns minutos depois.

> [!IMPORTANT]
> A reinicialização agora está disponível para todos os tipos de preço. Para mais informações e instruções, consulte [Azure Cache para administração Redis - Reboot](cache-administration.md#reboot).
>
>


## <a name="monitoring"></a>Monitorização

A secção **de Monitorização** permite configurar diagnósticos e monitorização para o seu Azure Cache para Redis.
Para obter mais informações sobre o Azure Cache para monitorização e diagnóstico redis, consulte [Como monitorizar o Azure Cache for Redis](cache-how-to-monitor.md).

![Diagnóstico](./media/cache-configure/redis-cache-diagnostics.png)

* [Métricas redis](#redis-metrics)
* [Regras de alerta](#alert-rules)
* [Diagnóstico](#diagnostics)

### <a name="redis-metrics"></a>Métricas de Redis
Clique nas **métricas do Redis** para [ver as métricas](cache-how-to-monitor.md#view-cache-metrics) para a sua cache.

### <a name="alert-rules"></a>Regras de alertas

Clique nas regras de **alerta** para configurar alertas baseados em Azure Cache para métricas Redis. Para mais informações, consulte [Alertas](cache-how-to-monitor.md#alerts).

### <a name="diagnostics"></a>Diagnóstico

Por predefinição, as métricas de cache no Monitor Azure são [armazenadas durante 30 dias](../azure-monitor/platform/data-platform-metrics.md) e depois eliminadas. Para persistir as métricas de cache por mais de 30 dias, clique em **Diagnósticos** para [configurar a conta](cache-how-to-monitor.md#export-cache-metrics) de armazenamento utilizada para armazenar diagnósticos de cache.

>[!NOTE]
>Além de arquivar as suas métricas de cache para armazenamento, também pode [transmiti-las para um hub de eventos ou enviá-las para registos do Monitor Azure](../azure-monitor/platform/stream-monitoring-data-event-hubs.md).
>
>

## <a name="support--troubleshooting-settings"></a>Suporte & configurações de solução de problemas
As definições na secção **Suporte + resolução** de problemas fornecem-lhe opções para resolver problemas com a sua cache.

![Suporte + resolução de problemas](./media/cache-configure/redis-cache-support-troubleshooting.png)

* [Saúde dos recursos](#resource-health)
* [Novo pedido de apoio](#new-support-request)

### <a name="resource-health"></a>Estado de funcionamento de recursos
**A saúde** dos recursos observa o seu recurso e diz-lhe se está a funcionar como esperado. Para mais informações sobre o serviço de saúde azure Resource, consulte a [visão geral da saúde do Recurso Azure.](../resource-health/resource-health-overview.md)

> [!NOTE]
> No momento, o Resource Health não pode relatar a integridade do cache do Azure para instâncias Redis hospedadas em uma rede virtual. Para mais informações, consulte [Todos os recursos de cache funcionam ao hospedar uma cache num VNET?](cache-how-to-premium-vnet.md#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)
>
>

### <a name="new-support-request"></a>Novo pedido de suporte
Clique em **Novo pedido** de suporte para abrir um pedido de suporte para a sua cache.





## <a name="default-redis-server-configuration"></a>Configuração padrão do servidor Redis
O novo cache do Azure para instâncias Redis são configurados com os seguintes valores de configuração padrão do Redis:

> [!NOTE]
> As definições desta secção não podem ser alteradas utilizando o método `StackExchange.Redis.IServer.ConfigSet`. Se esse método for chamado com um dos comandos nesta seção, uma exceção semelhante ao exemplo a seguir será gerada:  
>
> `StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
>
> Quaisquer valores configuráveis, como a **política de memória máxima,** são configuráveis através do portal Azure ou ferramentas de gestão de linha de comando, como o Azure CLI ou o PowerShell.
>
>

| Definição | Valor predefinido | Descrição |
| --- | --- | --- |
| `databases` |16 |O número padrão de bases de dados é de 16, mas pode configurar um número diferente com base no nível de preços. <sup>1</sup> A base de dados predefinida é DB 0, pode selecionar uma diferente numa base por ligação utilizando `connection.GetDatabase(dbid)` em que `dbid` é um número entre `0` e `databases - 1`. |
| `maxclients` |Depende do nível<sup>de</sup> preços 2 |Esse valor é o número máximo de clientes conectados permitidos ao mesmo tempo. Depois que o limite for atingido, o Redis fechará todas as novas conexões, retornando um erro "número máximo de clientes atingidos". |
| `maxmemory-policy` |`volatile-lru` |A política de maxmemory é a definição de como redis seleciona o que remover quando `maxmemory` (o tamanho da oferta de cache que selecionou quando criou a cache) é alcançada. Com o Azure Cache para Redis, a definição padrão é `volatile-lru`, que remove as teclas com um conjunto de expiração utilizando um algoritmo LRU. Essa configuração pode ser definida no portal do Azure. Para mais informações, consulte [as políticas de Memória.](#memory-policies) |
| `maxmemory-samples` |3 |Para economizar memória, os algoritmos de TTL mínimo e LRU são algoritmos aproximados em vez de algoritmos precisos. Por padrão, o Redis verifica três chaves e escolhe aquela que foi usada menos recentemente. |
| `lua-time-limit` |5,000 |Tempo máximo de execução de um script de lua em milissegundos. Se o tempo de execução máximo for atingido, o Redis registrará em log que um script ainda está em execução após o tempo máximo permitido e começará a responder a consultas com um erro. |
| `lua-event-limit` |500 |Tamanho máximo da fila de eventos de script. |
| `client-output-buffer-limit` `normalclient-output-buffer-limit` `pubsub` |0 0 032mb 8mb 60 |Os limites de buffer de saída do cliente podem ser usados para forçar a desconexão de clientes que não estão lendo dados do servidor com rapidez suficiente por algum motivo (um motivo comum é que um cliente pub/sub não pode consumir mensagens tão rápido quanto o Publicador pode produzi-las). Para mais informações, consulte [https://redis.io/topics/clients](https://redis.io/topics/clients). |

<a name="databases"></a><sup>
1</sup>O limite para `databases` é diferente para cada Azure Cache para o nível de preços Redis e pode ser definido na criação de cache. Se não for especificada a definição `databases` durante a criação do cache, o padrão é de 16.

* Caches básico e Standard
  * Cache de C0 (250 MB)-até 16 bancos de dados
  * Cache C1 (1 GB)-até 16 bancos de dados
  * Cache C2 (2,5 GB)-até 16 bancos de dados
  * Cache C3 (6 GB)-até 16 bancos de dados
  * Cache C4 (13 GB)-até 32 bancos de dados
  * Cache C5 (26 GB)-até 48 bancos de dados
  * Cache C6 (53 GB)-até 64 bancos de dados
* Caches Premium
  * P1 (6 GB-60 GB)-até 16 bancos de dados
  * P2 (13 GB-130 GB)-até 32 bancos de dados
  * P3 (26 GB-260 GB)-até 48 bancos de dados
  * P4 (53 GB-530 GB)-até 64 bancos de dados
  * Todos os caches premium com cluster Redis ativados - O cluster Redis apenas suporta a utilização da base de dados 0, pelo que o limite de `databases` para qualquer cache premium com cluster Redis ativado é efetivamente 1 e o comando [Select](https://redis.io/commands/select) não é permitido. Para mais informações, consulte [Se preciso de fazer alterações na aplicação do meu cliente para utilizar o clustering?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)

Para mais informações sobre bases de dados, veja o que são as [bases de dados redis?](cache-faq.md#what-are-redis-databases)

> [!NOTE]
> A definição `databases` só pode ser configurada durante a criação de cache e apenas utilizando powerShell, CLI ou outros clientes de gestão. Para um exemplo de configuração `databases` durante a criação de cache usando powerShell, consulte [New-AzRedisCache](cache-how-to-manage-redis-cache-powershell.md#databases).
>
>

<a name="maxclients"></a>
<sup>2</sup>`maxclients` é diferente para cada Cache Azure para o nível de preços Redis.

* Caches básico e Standard
  * Cache de C0 (250 MB)-até 256 conexões
  * Cache C1 (1 GB)-até 1.000 conexões
  * Cache de C2 (2,5 GB)-até 2.000 conexões
  * Cache C3 (6 GB)-até 5.000 conexões
  * Cache C4 (13 GB)-até 10.000 conexões
  * Cache C5 (26 GB)-até 15.000 conexões
  * Cache C6 (53 GB)-até 20.000 conexões
* Caches Premium
  * P1 (6 GB-60 GB)-até 7.500 conexões
  * P2 (13 GB-130 GB)-até 15.000 conexões
  * P3 (26 GB-260 GB)-até 30.000 conexões
  * P4 (53 GB-530 GB)-até 40.000 conexões

> [!NOTE]
> Embora cada tamanho de cache permita *até* um certo número de ligações, cada ligação com Redis tem sobrecarga associada a ele. Um exemplo de tal sobrecarga seria o uso de CPU e memória como resultado da criptografia TLS/SSL. O limite máximo de conexões para um determinado tamanho de cache pressupõe um cache levemente carregado. Se a carga a partir de ligação superior *mais* a carga das operações do cliente exceder a capacidade do sistema, a cache pode experimentar problemas de capacidade mesmo que não tenha excedido o limite de ligação para o tamanho atual do cache.
>
>



## <a name="redis-commands-not-supported-in-azure-cache-for-redis"></a>Comandos Redis sem suporte no cache do Azure para Redis
> [!IMPORTANT]
> Como a configuração e o gerenciamento do cache do Azure para instâncias Redis são gerenciados pela Microsoft, os comandos a seguir são desabilitados. Se tentar invocá-los, recebe uma mensagem de erro semelhante à `"(error) ERR unknown command"`.
>
> * BGREWRITEAOF
> * BGSAVE
> * CONFIGURAÇÃO
> * DEPURADO
> * MIGRÁ
> * SALVAR
> * DESLIGAR
> * SLAVEOF
> * CLUSTER-os comandos de gravação do cluster estão desabilitados, mas são permitidos comandos de cluster somente leitura.
>
>

Para mais informações sobre os comandos redis, consulte [https://redis.io/commands](https://redis.io/commands).

## <a name="redis-console"></a>Console do Redis
Pode emitir comandos de segurança para os casos do Seu Azure Cache para Redis utilizando a **Consola Redis,** que está disponível no portal Azure para todos os níveis de cache.

> [!IMPORTANT]
> - A Consola Redis não funciona com [o VNET.](cache-how-to-premium-vnet.md) Quando o cache faz parte de uma VNET, somente os clientes na VNET podem acessar o cache. Como o console do Redis é executado em seu navegador local, que está fora da VNET, ele não pode se conectar ao seu cache.
> - Nem todos os comandos Redis têm suporte no cache do Azure para Redis. Para obter uma lista de comandos Redis que são desativados para Azure Cache for Redis, consulte os comandos redis anteriores [não suportados em Azure Cache para](#redis-commands-not-supported-in-azure-cache-for-redis) a secção Redis. Para mais informações sobre os comandos redis, consulte [https://redis.io/commands](https://redis.io/commands).
>
>

Para aceder à Consola Redis, clique em **Consola** a partir do Cache Azure para a lâmina **Redis.**

![Console do Redis](./media/cache-configure/redis-console-menu.png)

Para emitir comandos em sua instância de cache, digite o comando desejado no console.

![Console do Redis](./media/cache-configure/redis-console.png)


### <a name="using-the-redis-console-with-a-premium-clustered-cache"></a>Usando o console do Redis com um cache de cluster Premium

Ao usar o console do Redis com um cache do cluster Premium, você pode emitir comandos para um único fragmento do cache. Para emitir um comando para um fragmento específico, primeiro conecte-se ao fragmento desejado clicando nele no seletor de fragmentos.

![Console do Redis](./media/cache-configure/redis-console-premium-cluster.png)

Se você tentar acessar uma chave que está armazenada em um fragmento diferente do fragmento conectado, você receberá uma mensagem de erro semelhante à mensagem a seguir:

```
shard1>get myKey
(error) MOVED 866 13.90.202.154:13000 (shard 0)
```

No exemplo anterior, o fragmento 1 é o fragmento selecionado, mas `myKey` está localizado no fragmento 0, como indicado pela parte `(shard 0)` da mensagem de erro. Neste exemplo, para aceder a `myKey`, selecione o fragmento 0 utilizando o picker de fragmentos e, em seguida, emita o comando desejado.


## <a name="move-your-cache-to-a-new-subscription"></a>Mover o cache para uma nova assinatura
Pode mover a sua cache para uma nova subscrição clicando em **Move**.

![Mover o cache do Azure para Redis](./media/cache-configure/redis-cache-move.png)

Para obter informações sobre a mudança de recursos de um grupo de recursos para outro, e de uma subscrição para outra, consulte [mover recursos para um novo grupo de recursos ou subscrição](../azure-resource-manager/management/move-resource-group-and-subscription.md).

## <a name="next-steps"></a>Passos Seguintes
* Para mais informações sobre trabalhar com os comandos redis, veja como posso executar os [comandos redis?](cache-faq.md#how-can-i-run-redis-commands)
