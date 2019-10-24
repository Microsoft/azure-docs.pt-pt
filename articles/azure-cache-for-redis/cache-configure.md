---
title: Como configurar o cache do Azure para Redis | Microsoft Docs
description: Entenda a configuração padrão do Redis para o cache do Azure para Redis e saiba como configurar seu cache do Azure para instâncias do Redis
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
ms.openlocfilehash: 6bc4b69122df7d29a611571a750229f47337015c
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756804"
---
# <a name="how-to-configure-azure-cache-for-redis"></a>Como configurar o cache do Azure para Redis
Este tópico descreve as configurações disponíveis para o cache do Azure para instâncias Redis. Este tópico também aborda a configuração padrão do servidor Redis para o cache do Azure para instâncias do Redis.

> [!NOTE]
> Para obter mais informações sobre como configurar e usar os recursos de cache Premium, consulte [como configurar a persistência](cache-how-to-premium-persistence.md), [como configurar o clustering](cache-how-to-premium-clustering.md)e [como configurar o suporte à rede virtual](cache-how-to-premium-vnet.md).
> 
> 

## <a name="configure-azure-cache-for-redis-settings"></a>Configurar o cache do Azure para configurações de Redis
[!INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

O cache do Azure para as configurações do Redis são exibidos e configurados na folha **cache do Azure para Redis** usando o **menu de recursos**.

![Cache do Azure para configurações de Redis](./media/cache-configure/redis-cache-settings.png)

Você pode exibir e definir as configurações a seguir usando o **menu de recursos**.

* [Descrição geral](#overview)
* [Registo de atividades](#activity-log)
* [Controle de acesso (IAM)](#access-control-iam)
* [Etiquetas](#tags)
* [Diagnosticar e resolver problemas](#diagnose-and-solve-problems)
* [Definições](#settings)
    * [Chaves de acesso](#access-keys)
    * [Configurações avançadas](#advanced-settings)
    * [Cache do Azure para o Redis Advisor](#azure-cache-for-redis-advisor)
    * [Dimensionar](#scale)
    * [Tamanho do cluster Redis](#cluster-size)
    * [Persistência de dados de Redis](#redis-data-persistence)
    * [Atualizações agendadas](#schedule-updates)
    * [Georreplicação](#geo-replication)
    * [Rede Virtual](#virtual-network)
    * [Firewall](#firewall)
    * [Properties](#properties)
    * [Bloquea](#locks)
    * [Script de automação](#automation-script)
* Administração
    * [Importar dados](#importexport)
    * [Exportar dados](#importexport)
    * [Reiniciar](#reboot)
* [Monitorização](#monitoring)
    * [Métricas de Redis](#redis-metrics)
    * [Regras de alerta](#alert-rules)
    * [Diagnóstico](#diagnostics)
* Suporte & configurações de solução de problemas
    * [Resource Health](#resource-health)
    * [Nova solicitação de suporte](#new-support-request)


## <a name="overview"></a>Visão geral

A **visão geral** fornece informações básicas sobre o cache, como nome, portas, tipo de preço e métricas de cache selecionadas.

### <a name="activity-log"></a>Registo de atividades

Clique em **log de atividades** para exibir as ações executadas no cache. Você também pode usar a filtragem para expandir essa exibição para incluir outros recursos. Para obter mais informações sobre como trabalhar com logs de auditoria, consulte [operações de auditoria com o Gerenciador de recursos](../azure-resource-manager/resource-group-audit.md). Para obter mais informações sobre como monitorar eventos do cache do Azure para Redis, consulte [operações e alertas](cache-how-to-monitor.md#operations-and-alerts).

### <a name="access-control-iam"></a>Controle de acesso (IAM)

A seção de **controle de acesso (iam)** fornece suporte para RBAC (controle de acesso baseado em função) no portal do Azure. Essa configuração ajuda as organizações a atender seus requisitos de gerenciamento de acesso de forma simples e precisa. Para obter mais informações, consulte [controle de acesso baseado em função no portal do Azure](../role-based-access-control/role-assignments-portal.md).

### <a name="tags"></a>Etiquetas

A seção de **marcas** ajuda você a organizar seus recursos. Para obter mais informações, veja [Utilizar etiquetas para organizar os recursos do Azure](../azure-resource-manager/resource-group-using-tags.md).


### <a name="diagnose-and-solve-problems"></a>Diagnosticar e resolver problemas

Clique em **diagnosticar e resolver problemas** a serem fornecidos com problemas e estratégias comuns para resolvê-los.



## <a name="settings"></a>Definições
A seção **configurações** permite que você acesse e defina as configurações a seguir para seu cache.

* [Chaves de acesso](#access-keys)
* [Configurações avançadas](#advanced-settings)
* [Cache do Azure para o Redis Advisor](#azure-cache-for-redis-advisor)
* [Dimensionar](#scale)
* [Tamanho do cluster Redis](#cluster-size)
* [Persistência de dados de Redis](#redis-data-persistence)
* [Atualizações agendadas](#schedule-updates)
* [Georreplicação](#geo-replication)
* [Rede Virtual](#virtual-network)
* [Firewall](#firewall)
* [Properties](#properties)
* [Bloquea](#locks)
* [Script de automação](#automation-script)



### <a name="access-keys"></a>Chaves de acesso
Clique em **chaves de acesso** para exibir ou regenerar as chaves de acesso para seu cache. Essas chaves são usadas pelos clientes que se conectam ao seu cache.

![Cache do Azure para chaves de acesso Redis](./media/cache-configure/redis-cache-manage-keys.png)

### <a name="advanced-settings"></a>Definições avançadas
As configurações a seguir são definidas na folha **Configurações avançadas** .

* [Portas de acesso](#access-ports)
* [Políticas de memória](#memory-policies)
* [Notificações de keyspace (configurações avançadas)](#keyspace-notifications-advanced-settings)

#### <a name="access-ports"></a>Portas de acesso
Por predefinição, o acesso não SSL está desativado para as novas caches. Para habilitar a porta não SSL, clique em **não** para **permitir acesso somente via SSL** na folha **Configurações avançadas** e, em seguida, clique em **salvar**.

> [!NOTE]
> O acesso SSL ao cache do Azure para Redis dá suporte a TLS 1,0 por padrão. A versão mínima com suporte do TLS pode ser elevada para TLS 1,2, se desejado, usando o menu suspenso de **versão mínima do TLS** na folha **Configurações avançadas** e, em seguida, clique em **salvar**.

![Cache do Azure para portas de acesso Redis](./media/cache-configure/redis-cache-access-ports.png)

<a name="maxmemory-policy-and-maxmemory-reserved"></a>
#### <a name="memory-policies"></a>Políticas de memória
As configurações de **política MaxMemory**, **MaxMemory-reservado**e **Maxfragmentationmemory reservado** na folha **Configurações avançadas** configuram as políticas de memória para o cache.

![Cache do Azure para a política Redis MaxMemory](./media/cache-configure/redis-cache-maxmemory-policy.png)

A **política MaxMemory** configura a política de remoção para o cache e permite que você escolha entre as seguintes políticas de remoção:

* `volatile-lru`-esta é a política de remoção padrão.
* `allkeys-lru`
* `volatile-random`
* `allkeys-random`
* `volatile-ttl`
* `noeviction`

Para obter mais informações sobre políticas de `maxmemory`, consulte [políticas de remoção](https://redis.io/topics/lru-cache#eviction-policies).

A configuração **MaxMemory-reserved** configura a quantidade de memória, em MB, reservada para operações que não são de cache, como a replicação durante o failover. Definir esse valor permite que você tenha uma experiência de servidor Redis mais consistente quando a carga varia. Esse valor deve ser definido como mais alto para cargas de trabalho que são pesadas de gravação. Quando a memória é reservada para essas operações, ela não está disponível para armazenamento de dados armazenados em cache.

A configuração **reservada maxfragmentationmemory** configura a quantidade de memória em MB reservada para acomodar a fragmentação de memória. Definir esse valor permite que você tenha uma experiência de servidor Redis mais consistente quando o cache está cheio ou próximo de completo e a taxa de fragmentação é alta. Quando a memória é reservada para essas operações, ela não está disponível para armazenamento de dados armazenados em cache.

Uma coisa a ser considerada ao escolher um novo valor de reserva de memória (**MaxMemory-reservado** ou **maxfragmentationmemory-reservado**) é como essa alteração pode afetar um cache que já está sendo executado com grandes quantidades de dados. Por exemplo, se você tiver um cache de 53 GB com 49 GB de dados, altere o valor de reserva para 8 GB, essa alteração removerá a memória máxima disponível para o sistema até 45 GB. Se o `used_memory` atual ou seus valores de `used_memory_rss` forem maiores do que o novo limite de 45 GB, o sistema terá que remover os dados até que `used_memory` e `used_memory_rss` estejam abaixo de 45 GB. A remoção pode aumentar a carga do servidor e a fragmentação da memória. Para obter mais informações sobre as métricas de cache, como `used_memory` e `used_memory_rss`, consulte [métricas disponíveis e intervalos de relatórios](cache-how-to-monitor.md#available-metrics-and-reporting-intervals).

> [!IMPORTANT]
> As configurações **MaxMemory-reserved** e **maxfragmentationmemory-reserved** estão disponíveis somente para caches Premium e Standard.
> 
> 

#### <a name="keyspace-notifications-advanced-settings"></a>Notificações de keyspace (configurações avançadas)
As notificações de keyspace do Redis são configuradas na folha **Configurações avançadas** . As notificações de keyspace permitem que os clientes recebam notificações quando determinados eventos ocorrerem.

![Cache do Azure para configurações avançadas do Redis](./media/cache-configure/redis-cache-advanced-settings.png)

> [!IMPORTANT]
> Notificações de keyspace e a configuração **notificar-keyspace-Events** só estão disponíveis para caches Standard e Premium.
> 
> 

Para obter mais informações, consulte [Redis keyspace Notifications](https://redis.io/topics/notifications). Para obter o código de exemplo, consulte o arquivo [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) no exemplo do [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) .


<a name="recommendations"></a>
## <a name="azure-cache-for-redis-advisor"></a>Cache do Azure para o Redis Advisor
O **cache do Azure para a folha do supervisor do Redis** exibe recomendações para seu cache. Durante as operações normais, nenhuma recomendação é exibida. 

![Recomendações](./media/cache-configure/redis-cache-no-recommendations.png)

Se qualquer condição ocorrer durante as operações do cache, como o uso de memória alta, a largura de banda de rede ou a carga do servidor, um alerta será exibido na folha **cache do Azure para Redis** .

![Recomendações](./media/cache-configure/redis-cache-recommendations-alert.png)

Mais informações podem ser encontradas na folha **recomendações** .

![Recomendações](./media/cache-configure/redis-cache-recommendations.png)

Você pode monitorar essas métricas nas seções [gráficos de monitoramento](cache-how-to-monitor.md#monitoring-charts) e [gráficos de uso](cache-how-to-monitor.md#usage-charts) da folha cache do **Azure para Redis** .

Cada tipo de preço tem limites diferentes para conexões de cliente, memória e largura de banda. Se o seu cache se aproximar da capacidade máxima para essas métricas em um período de tempo prolongado, uma recomendação será criada. Para obter mais informações sobre as métricas e os limites revisados pela ferramenta de **recomendações** , consulte a tabela a seguir:

| Cache do Azure para métrica Redis | Mais informações |
| --- | --- |
| Uso da largura de banda da rede |[Desempenho do cache-largura de banda disponível](cache-faq.md#cache-performance) |
| Clientes conectados |[Configuração padrão do servidor Redis-MaxClients](#maxclients) |
| Carga do servidor |[Gráficos de uso-carga do servidor Redis](cache-how-to-monitor.md#usage-charts) |
| Uso de memória |[Desempenho do cache-tamanho](cache-faq.md#cache-performance) |

Para atualizar o cache, clique em **Atualizar agora** para alterar o tipo de preço e [dimensionar](#scale) o cache. Para obter mais informações sobre como escolher um tipo de preço, consulte [qual o cache do Azure para oferta e tamanho do Redis devo usar?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)


### <a name="scale"></a>Escalabilidade
Clique em **dimensionar** para exibir ou alterar o tipo de preço do seu cache. Para obter mais informações sobre o dimensionamento, consulte [como dimensionar o cache do Azure para Redis](cache-how-to-scale.md).

![Cache do Azure para tipo de preço Redis](./media/cache-configure/pricing-tier.png)

<a name="cluster-size"></a>

### <a name="redis-cluster-size"></a>Tamanho do cluster Redis
Clique em **tamanho do cluster Redis (visualização)** para alterar o tamanho do cluster para um cache Premium em execução com clustering habilitado.

> [!NOTE]
> Observe que, embora o cache do Azure para a camada Premium do Redis tenha sido liberado para disponibilidade geral, o recurso de tamanho de cluster Redis está atualmente em visualização.
> 
> 

![Tamanho do cluster Redis](./media/cache-configure/redis-cache-redis-cluster-size.png)

Para alterar o tamanho do cluster, use o controle deslizante ou digite um número entre 1 e 10 na caixa de texto **contagem de fragmentos** e clique em **OK** para salvar.

> [!IMPORTANT]
> O clustering do Redis só está disponível para caches Premium. Para obter mais informações, consulte [como configurar o clustering para um cache Premium do Azure para Redis](cache-how-to-premium-clustering.md).
> 
> 


### <a name="redis-data-persistence"></a>Persistência de dados de Redis
Clique em **persistência de dados do Redis** para habilitar, desabilitar ou configurar a persistência de dados para o cache Premium. O cache do Azure para Redis oferece persistência de Redis usando persistência de [RDB](cache-how-to-premium-persistence.md#configure-rdb-persistence) ou [persistência de AOF](cache-how-to-premium-persistence.md#configure-aof-persistence).

Para obter mais informações, consulte [como configurar a persistência para um cache do Azure Premium para Redis](cache-how-to-premium-persistence.md).


> [!IMPORTANT]
> A persistência de dados do Redis só está disponível para caches Premium.
> 
> 

### <a name="schedule-updates"></a>Atualizações agendadas
A folha **agendar atualizações** permite designar uma janela de manutenção para atualizações do Redis Server para seu cache. 

> [!IMPORTANT]
> A janela de manutenção aplica-se somente a atualizações do servidor Redis e não a atualizações do Azure ou a atualizações do sistema operacional das VMs que hospedam o cache.
> 
> 

![Atualizações agendadas](./media/cache-configure/redis-schedule-updates.png)

Para especificar uma janela de manutenção, verifique os dias desejados e especifique a hora de início da janela de manutenção para cada dia e clique em **OK**. A hora da janela de manutenção está em UTC.

> [!IMPORTANT]
> A funcionalidade **agendar atualizações** só está disponível para caches da camada Premium. Para obter mais informações e instruções, consulte [cache do Azure para administração de Redis – agendar atualizações](cache-administration.md#schedule-updates).
> 
> 

### <a name="geo-replication"></a>Georreplicação

A folha **replicação geográfica** fornece um mecanismo para vincular duas camadas do cache do Azure de camada Premium para instâncias Redis. Um cache é designado como o cache vinculado primário e o outro como o cache vinculado secundário. O cache vinculado secundário torna-se somente leitura e os dados gravados no cache primário são replicados para o cache vinculado secundário. Essa funcionalidade pode ser usada para replicar um cache em regiões do Azure.

> [!IMPORTANT]
> **A replicação geográfica** só está disponível para caches da camada Premium. Para obter mais informações e instruções, consulte [como configurar a replicação geográfica para o cache do Azure para Redis](cache-how-to-geo-replication.md).
> 
> 

### <a name="virtual-network"></a>Rede Virtual
A seção **rede virtual** permite que você defina as configurações de rede virtual para seu cache. Para obter informações sobre como criar um cache Premium com suporte VNET e atualizar suas configurações, consulte [como configurar o suporte de rede virtual para um cache Premium do Azure para Redis](cache-how-to-premium-vnet.md).

> [!IMPORTANT]
> As configurações de rede virtual só estão disponíveis para caches Premium que foram configurados com suporte VNET durante a criação do cache. 
> 
> 

### <a name="firewall"></a>Firewall

A configuração de regras de firewall está disponível para todo o cache do Azure para camadas Redis.

Clique em **Firewall** para exibir e configurar as regras de firewall para o cache.

![Firewall](./media/cache-configure/redis-firewall-rules.png)

Você pode especificar regras de firewall com um intervalo de endereços IP inicial e final. Quando as regras de firewall são configuradas, somente as conexões de cliente dos intervalos de endereços IP especificados podem se conectar ao cache. Quando uma regra de firewall é salva, há um pequeno atraso antes que a regra seja efetiva. Esse atraso é normalmente menos de um minuto.

> [!IMPORTANT]
> As conexões do cache do Azure para sistemas de monitoramento Redis são sempre permitidas, mesmo se as regras de firewall estiverem configuradas.
> 
> 

### <a name="properties"></a>Propriedades
Clique em **Propriedades** para exibir informações sobre o cache, incluindo o ponto de extremidade e as portas do cache.

![Cache do Azure para propriedades Redis](./media/cache-configure/redis-cache-properties.png)

### <a name="locks"></a>Bloqueios
A seção de **bloqueios** permite que você bloqueie uma assinatura, grupo de recursos ou recurso para impedir que outros usuários em sua organização excluam ou modifiquem acidentalmente recursos críticos. Para obter mais informações, consulte [Bloquear recursos com o Azure Resource Manager](../azure-resource-manager/resource-group-lock-resources.md).

### <a name="automation-script"></a>Script de automação

Clique em **script de automação** para criar e exportar um modelo de seus recursos implantados para implantações futuras. Para obter mais informações sobre como trabalhar com modelos, consulte [implantar recursos com modelos de Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="administration-settings"></a>Configurações de administração
As configurações na seção **Administração** permitem que você execute as seguintes tarefas administrativas para seu cache. 

![Administração](./media/cache-configure/redis-cache-administration.png)

* [Importar dados](#importexport)
* [Exportar dados](#importexport)
* [Reiniciar](#reboot)


### <a name="importexport"></a>Importação/Exportação
A importação/exportação é um cache do Azure para a operação de gerenciamento de dados do Redis, que permite importar e exportar dados no cache importando e exportando um instantâneo do RDB (cache do Azure para o desbanco de Redis) de um cache Premium para um blob de páginas em uma conta de armazenamento do Azure. A importação/exportação permite que você migre entre diferentes cache do Azure para instâncias Redis ou preencha o cache com os dados antes de usá-los.

A importação pode ser usada para colocar arquivos de RDB compatíveis com Redis de qualquer servidor Redis em execução em qualquer nuvem ou ambiente, incluindo Redis em execução no Linux, Windows ou qualquer provedor de nuvem, como Amazon Web Services e outros. A importação de dados é uma maneira fácil de criar um cache com dados preenchidos previamente. Durante o processo de importação, o cache do Azure para Redis carrega os arquivos de RDB do armazenamento do Azure na memória e, em seguida, insere as chaves no cache.

A exportação permite que você exporte os dados armazenados no cache do Azure para Redis para arquivos RDB compatíveis com o Redis. Você pode usar esse recurso para mover dados de um cache do Azure para instância Redis para outro ou para outro servidor Redis. Durante o processo de exportação, um arquivo temporário é criado na VM que hospeda o cache do Azure para a instância do servidor Redis e o arquivo é carregado para a conta de armazenamento designada. Quando a operação de exportação for concluída com um status de êxito ou falha, o arquivo temporário será excluído.

> [!IMPORTANT]
> A importação/exportação só está disponível para caches da camada Premium. Para obter mais informações e instruções, consulte [importar e exportar dados no cache do Azure para Redis](cache-how-to-import-export-data.md).
> 
> 

### <a name="reboot"></a>Reiniciar
A folha **reinicializar** permite que você reinicialize os nós do cache. Essa capacidade de reinicialização permite testar seu aplicativo quanto à resiliência se houver uma falha de um nó de cache.

![Reiniciar](./media/cache-configure/redis-cache-reboot.png)

Se você tiver um cache Premium com clustering habilitado, poderá selecionar quais fragmentos do cache devem ser reinicializados.

![Reiniciar](./media/cache-configure/redis-cache-reboot-cluster.png)

Para reinicializar um ou mais nós do cache, selecione os nós desejados e clique em **reinicializar**. Se você tiver um cache Premium com clustering habilitado, selecione os fragmentos a serem reinicializados e clique em **reinicializar**. Após alguns minutos, os nós selecionados são reinicializados e ficam online novamente alguns minutos depois.

> [!IMPORTANT]
> A reinicialização agora está disponível para todos os tipos de preço. Para obter mais informações e instruções, consulte [Azure cache for Redis Administration-reboot](cache-administration.md#reboot).
> 
> 


## <a name="monitoring"></a>Monitorização

A seção **monitoramento** permite que você configure o diagnóstico e o monitoramento para o cache do Azure para Redis. Para obter mais informações sobre o cache do Azure para monitoramento e diagnóstico de Redis, consulte [como monitorar o cache do Azure para Redis](cache-how-to-monitor.md).

![Diagnósticos](./media/cache-configure/redis-cache-diagnostics.png)

* [Métricas de Redis](#redis-metrics)
* [Regras de alerta](#alert-rules)
* [Diagnóstico](#diagnostics)

### <a name="redis-metrics"></a>Métricas de Redis
Clique em **métricas de Redis** para [exibir as métricas](cache-how-to-monitor.md#view-cache-metrics) para o cache.

### <a name="alert-rules"></a>Regras de alerta

Clique em **regras de alerta** para configurar alertas com base no cache do Azure para métricas Redis. Para obter mais informações, consulte [alertas](cache-how-to-monitor.md#alerts).

### <a name="diagnostics"></a>Diagnósticos

Por padrão, as métricas de cache no Azure Monitor são [armazenadas por 30 dias](../azure-monitor/platform/data-platform-metrics.md) e, em seguida, excluídas. Para persistir suas métricas de cache por mais de 30 dias, clique em **diagnóstico** para [Configurar a conta de armazenamento](cache-how-to-monitor.md#export-cache-metrics) usada para armazenar o diagnóstico de cache.

>[!NOTE]
>Além de arquivar suas métricas de cache no armazenamento, você também pode [transmiti-las para um hub de eventos ou enviá-las para Azure monitor logs](../azure-monitor/platform/stream-monitoring-data-event-hubs.md).
>
>

## <a name="support--troubleshooting-settings"></a>Suporte & configurações de solução de problemas
As configurações na seção **suporte + solução de problemas** fornecem opções para resolver problemas com o cache.

![Suporte + solução de problemas](./media/cache-configure/redis-cache-support-troubleshooting.png)

* [Resource Health](#resource-health)
* [Nova solicitação de suporte](#new-support-request)

### <a name="resource-health"></a>Resource Health
O **Resource Health** inspeciona o recurso e informa se ele está sendo executado conforme o esperado. Para obter mais informações sobre o serviço de integridade de recursos do Azure, consulte [visão geral do Azure Resource Health](../resource-health/resource-health-overview.md).

> [!NOTE]
> No momento, o Resource Health não pode relatar a integridade do cache do Azure para instâncias Redis hospedadas em uma rede virtual. Para obter mais informações, consulte [todos os recursos de cache funcionam ao hospedar um cache em uma VNET?](cache-how-to-premium-vnet.md#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)
> 
> 

### <a name="new-support-request"></a>Novo pedido de suporte
Clique em **nova solicitação de suporte** para abrir uma solicitação de suporte para seu cache.





## <a name="default-redis-server-configuration"></a>Configuração padrão do servidor Redis
O novo cache do Azure para instâncias Redis são configurados com os seguintes valores de configuração padrão do Redis:

> [!NOTE]
> As configurações nesta seção não podem ser alteradas usando o método `StackExchange.Redis.IServer.ConfigSet`. Se esse método for chamado com um dos comandos nesta seção, uma exceção semelhante ao exemplo a seguir será gerada:  
> 
> `StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
> 
> Quaisquer valores configuráveis, como **Max-Memory-Policy**, são configuráveis por meio de portal do Azure ou ferramentas de gerenciamento de linha de comando, como CLI do Azure ou PowerShell.
> 
> 

| Definição | Valor predefinido | Descrição |
| --- | --- | --- |
| `databases` |16 |O número padrão de bancos de dados é 16, mas você pode configurar um número diferente com base no tipo de preço. <sup>1</sup> o banco de dados padrão é DB 0, você pode selecionar outro em uma base por conexão usando `connection.GetDatabase(dbid)` em que `dbid` é um número entre `0` e `databases - 1`. |
| `maxclients` |Depende do tipo de preço<sup>2</sup> |Esse valor é o número máximo de clientes conectados permitidos ao mesmo tempo. Depois que o limite for atingido, o Redis fechará todas as novas conexões, retornando um erro "número máximo de clientes atingidos". |
| `maxmemory-policy` |`volatile-lru` |A política MaxMemory é a configuração de como o Redis seleciona o que remover quando `maxmemory` (o tamanho da oferta de cache que você selecionou quando criou o cache) é atingido. Com o cache do Azure para Redis, a configuração padrão é `volatile-lru`, que remove as chaves com uma expiração definida usando um algoritmo LRU. Essa configuração pode ser definida no portal do Azure. Para obter mais informações, consulte [políticas de memória](#memory-policies). |
| `maxmemory-samples` |3 |Para economizar memória, os algoritmos de TTL mínimo e LRU são algoritmos aproximados em vez de algoritmos precisos. Por padrão, o Redis verifica três chaves e escolhe aquela que foi usada menos recentemente. |
| `lua-time-limit` |5000 |Tempo máximo de execução de um script de lua em milissegundos. Se o tempo de execução máximo for atingido, o Redis registrará em log que um script ainda está em execução após o tempo máximo permitido e começará a responder a consultas com um erro. |
| `lua-event-limit` |500 |Tamanho máximo da fila de eventos de script. |
| `client-output-buffer-limit` `normalclient-output-buffer-limit` `pubsub` |0 0 032mb 8MB 60 |Os limites de buffer de saída do cliente podem ser usados para forçar a desconexão de clientes que não estão lendo dados do servidor com rapidez suficiente por algum motivo (um motivo comum é que um cliente pub/sub não pode consumir mensagens tão rápido quanto o Publicador pode produzi-las). Para mais informações, consulte [https://redis.io/topics/clients](https://redis.io/topics/clients). |

<a name="databases"></a>
<sup>1</sup>o limite de `databases` é diferente para cada cache do Azure para o tipo de preço Redis e pode ser definido na criação do cache. Se nenhuma configuração de `databases` for especificada durante a criação do cache, o padrão será 16.

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
  * Todos os caches Premium com o cluster Redis habilitado-Redis cluster só dá suporte ao uso do banco de dados 0, portanto, o limite de `databases` para qualquer cache Premium com o cluster Redis habilitado é efetivamente 1 e o comando [Select](https://redis.io/commands/select) não é permitido. Para obter mais informações, consulte [preciso fazer qualquer alteração no meu aplicativo cliente para usar o clustering?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)

Para obter mais informações sobre bancos de dados, consulte [o que são bancos de dados Redis?](cache-faq.md#what-are-redis-databases)

> [!NOTE]
> A configuração `databases` pode ser configurada somente durante a criação do cache e somente com o PowerShell, a CLI ou outros clientes de gerenciamento. Para obter um exemplo de configuração de `databases` durante a criação do cache usando o PowerShell, consulte [New-AzRedisCache](cache-how-to-manage-redis-cache-powershell.md#databases).
> 
> 

<a name="maxclients"></a>
<sup>2</sup> `maxclients` é diferente para cada cache do Azure para o tipo de preço Redis.

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
> Embora cada tamanho de cache permita *até* um determinado número de conexões, cada conexão com Redis tem sobrecarga associada a ela. Um exemplo de tal sobrecarga seria o uso de CPU e memória como resultado da criptografia TLS/SSL. O limite máximo de conexões para um determinado tamanho de cache pressupõe um cache levemente carregado. Se a carga da sobrecarga de conexão *mais* a carga das operações do cliente exceder a capacidade para o sistema, o cache poderá ter problemas de capacidade mesmo que você não tenha excedido o limite de conexão para o tamanho do cache atual.
> 
> 



## <a name="redis-commands-not-supported-in-azure-cache-for-redis"></a>Comandos Redis sem suporte no cache do Azure para Redis
> [!IMPORTANT]
> Como a configuração e o gerenciamento do cache do Azure para instâncias Redis são gerenciados pela Microsoft, os comandos a seguir são desabilitados. Se você tentar chamá-los, receberá uma mensagem de erro semelhante a `"(error) ERR unknown command"`.
> 
> * BGREWRITEAOF
> * BGSAVE
> * CONFIGURAÇÃO
> * VERIFICAÇÃO
> * MIGRÁ
> * Guarde
> * DESLIGAR
> * SLAVEOF
> * CLUSTER-os comandos de gravação do cluster estão desabilitados, mas são permitidos comandos de cluster somente leitura.
> 
> 

Para obter mais informações sobre comandos Redis, consulte [https://redis.io/commands](https://redis.io/commands).

## <a name="redis-console"></a>Console do Redis
Você pode emitir comandos com segurança para o cache do Azure para instâncias Redis usando o **console do Redis**, que está disponível no portal do Azure para todas as camadas de cache.

> [!IMPORTANT]
> - O console do Redis não funciona com [VNET](cache-how-to-premium-vnet.md). Quando o cache faz parte de uma VNET, somente os clientes na VNET podem acessar o cache. Como o console do Redis é executado em seu navegador local, que está fora da VNET, ele não pode se conectar ao seu cache.
> - Nem todos os comandos Redis têm suporte no cache do Azure para Redis. Para obter uma lista de comandos do Redis que estão desabilitados para o cache do Azure para Redis, consulte a seção comandos anteriores do [Redis sem suporte no cache do Azure para Redis](#redis-commands-not-supported-in-azure-cache-for-redis) . Para obter mais informações sobre comandos Redis, consulte [https://redis.io/commands](https://redis.io/commands).
> 
> 

Para acessar o console do Redis, clique em **console** na folha **cache do Azure para Redis** .

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

No exemplo anterior, o fragmento 1 é o fragmento selecionado, mas `myKey` está localizado no fragmento 0, conforme indicado pela parte `(shard 0)` da mensagem de erro. Neste exemplo, para acessar `myKey`, selecione fragmento 0 usando o seletor de fragmentos e, em seguida, emita o comando desejado.


## <a name="move-your-cache-to-a-new-subscription"></a>Mover o cache para uma nova assinatura
Você pode mover o cache para uma nova assinatura clicando em **mover**.

![Mover o cache do Azure para Redis](./media/cache-configure/redis-cache-move.png)

Para obter informações sobre como mover recursos de um grupo de recursos para outro e de uma assinatura para outra, consulte [mover recursos para um novo grupo de recursos ou assinatura](../azure-resource-manager/resource-group-move-resources.md).

## <a name="next-steps"></a>Passos seguintes
* Para obter mais informações sobre como trabalhar com comandos Redis, consulte [como posso executar comandos do Redis?](cache-faq.md#how-can-i-run-redis-commands)

