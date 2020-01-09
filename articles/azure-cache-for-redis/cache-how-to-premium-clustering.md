---
title: Configurar clustering do Redis-cache Premium do Azure para Redis
description: Saiba como criar e gerenciar o clustering Redis para o cache do Azure da camada Premium para instâncias do Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 06/13/2018
ms.openlocfilehash: ddb44a064090a108f77d6a6f9a270fab8c55ec90
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75433437"
---
# <a name="how-to-configure-redis-clustering-for-a-premium-azure-cache-for-redis"></a>Como configurar o clustering do Redis para um cache Premium do Azure para Redis
O cache do Azure para Redis tem diferentes ofertas de cache, que fornecem flexibilidade na escolha do tamanho e dos recursos do cache, incluindo recursos da camada Premium, como clustering, persistência e suporte à rede virtual. Este artigo descreve como configurar o clustering em um cache do Azure Premium para a instância do Redis.

Para obter informações sobre outros recursos de cache Premium, consulte [introdução ao cache do Azure para a camada Premium do Redis](cache-premium-tier-intro.md).

## <a name="what-is-redis-cluster"></a>O que é o cluster Redis?
O cache do Azure para Redis oferece o cluster Redis conforme [implementado no Redis](https://redis.io/topics/cluster-tutorial). Com o cluster Redis, você obtém os seguintes benefícios: 

* A capacidade de dividir automaticamente seu conjunto de recursos entre vários nós. 
* A capacidade de continuar as operações quando um subconjunto de nós estiver apresentando falhas ou não conseguir se comunicar com o restante do cluster. 
* Mais taxa de transferência: a taxa de transferência aumenta linearmente à medida que você aumenta o número de fragmentos. 
* Mais tamanho de memória: aumenta linearmente à medida que você aumenta o número de fragmentos.  

O clustering não aumenta o número de conexões disponíveis para um cache clusterizado. Para obter mais informações sobre tamanho, taxa de transferência e largura de banda com caches Premium, consulte [qual cache do Azure para oferta de Redis e tamanho devo usar?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)

No Azure, o cluster Redis é oferecido como um modelo primário/de réplica, em que cada fragmento tem um par primário/de réplica com replicação em que a replicação é gerenciada pelo cache do Azure para o serviço Redis. 

## <a name="clustering"></a>Clustering
O clustering está habilitado no **novo cache do Azure para a folha Redis** durante a criação do cache. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

O clustering é configurado na folha do **cluster Redis** .

![Clustering][redis-cache-clustering]

Você pode ter até 10 fragmentos no cluster. Clique em **habilitado** e deslize o controle deslizante ou digite um número entre 1 e 10 para **contagem de fragmentos** e clique em **OK**.

Cada fragmento é um par de cache primário/de réplica gerenciado pelo Azure e o tamanho total do cache é calculado multiplicando o número de fragmentos pelo tamanho do cache selecionado no tipo de preço. 

![Clustering][redis-cache-clustering-selected]

Depois que o cache é criado, você se conecta a ele e o usa como um cache não clusterizado e o Redis distribui os dados em todos os fragmentos de cache. Se o diagnóstico estiver [habilitado](cache-how-to-monitor.md#enable-cache-diagnostics), as métricas serão capturadas separadamente para cada fragmento e poderão ser [exibidas](cache-how-to-monitor.md) na folha cache do Azure para Redis. 

> [!NOTE]
> 
> Há algumas diferenças mínimas necessárias no aplicativo cliente quando o clustering é configurado. Para obter mais informações, consulte [preciso fazer qualquer alteração no meu aplicativo cliente para usar o clustering?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 

Para obter um exemplo de código sobre como trabalhar com clustering com o cliente StackExchange. Redis, consulte a parte [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) do exemplo de [Olá, mundo](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) .

<a name="cluster-size"></a>

## <a name="change-the-cluster-size-on-a-running-premium-cache"></a>Alterar o tamanho do cluster em um cache Premium em execução
Para alterar o tamanho do cluster em um cache Premium em execução com clustering habilitado, clique em **tamanho do cluster Redis** no **menu de recursos**.

> [!NOTE]
> Embora o cache do Azure para a camada Premium do Redis tenha sido liberado para disponibilidade geral, o recurso de tamanho de cluster Redis está atualmente em visualização.
> 
> 

![Tamanho do cluster Redis][redis-cache-redis-cluster-size]

Para alterar o tamanho do cluster, use o controle deslizante ou digite um número entre 1 e 10 na caixa de texto **contagem de fragmentos** e clique em **OK** para salvar.

Aumentar o tamanho do cluster aumenta a taxa de transferência máxima e o tamanho do cache. O aumento do tamanho do cluster não aumenta o máximo. conexões disponíveis para clientes.

> [!NOTE]
> O dimensionamento de um cluster executa o comando [migrar](https://redis.io/commands/migrate) , que é um comando caro, portanto, para um impacto mínimo, considere a execução dessa operação fora do horário de pico. Durante o processo de migração, você verá um pico na carga do servidor. O dimensionamento de um cluster é um processo de execução demorada e a quantidade de tempo decorrido depende do número de chaves e do tamanho dos valores associados a essas chaves.
> 
> 

## <a name="clustering-faq"></a>Perguntas frequentes sobre clustering
A lista a seguir contém respostas para perguntas frequentes sobre o cache do Azure para clustering Redis.

* [É necessário fazer alterações no meu aplicativo cliente para usar o clustering?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
* [Como as chaves são distribuídas em um cluster?](#how-are-keys-distributed-in-a-cluster)
* [Qual é o maior tamanho de cache que posso criar?](#what-is-the-largest-cache-size-i-can-create)
* [Todos os clientes do Redis dão suporte ao clustering?](#do-all-redis-clients-support-clustering)
* [Como fazer conectar-se ao meu cache quando o clustering está habilitado?](#how-do-i-connect-to-my-cache-when-clustering-is-enabled)
* [Posso me conectar diretamente aos fragmentos individuais do meu cache?](#can-i-directly-connect-to-the-individual-shards-of-my-cache)
* [Posso configurar o clustering para um cache criado anteriormente?](#can-i-configure-clustering-for-a-previously-created-cache)
* [Posso configurar o clustering para um cache básico ou Standard?](#can-i-configure-clustering-for-a-basic-or-standard-cache)
* [Posso usar clustering com o estado de sessão do Redis ASP.NET e provedores de cache de saída?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)
* [Estou obtendo exceções de movimentação ao usar StackExchange. Redis e clustering, o que devo fazer?](#i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do)

### <a name="do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering"></a>É necessário fazer alterações no meu aplicativo cliente para usar o clustering?
* Quando o clustering está habilitado, somente o banco de dados 0 está disponível. Se o aplicativo cliente usar vários bancos de dados e tentar ler ou gravar em um banco de dados diferente de 0, a exceção a seguir será lançada. `Unhandled Exception: StackExchange.Redis.RedisConnectionException: ProtocolFailure on GET --->` `StackExchange.Redis.RedisCommandException: Multiple databases are not supported on this server; cannot switch to database: 6`
  
  Para obter mais informações, consulte [subconjunto implementado pela especificação de cluster Redis](https://redis.io/topics/cluster-spec#implemented-subset).
* Se você estiver usando [stackexchange. Redis](https://www.nuget.org/packages/StackExchange.Redis/), deverá usar o 1.0.481 ou posterior. Você se conecta ao cache usando os mesmos [pontos de extremidade, portas e chaves](cache-configure.md#properties) que você usa ao se conectar a um cache que não tem o clustering habilitado. A única diferença é que todas as leituras e gravações devem ser feitas no banco de dados 0.
  
  * Outros clientes podem ter requisitos diferentes. Veja [todos os clientes do Redis que dão suporte ao clustering?](#do-all-redis-clients-support-clustering)
* Se seu aplicativo usa várias operações de chave em lote em um único comando, todas as chaves devem estar localizadas no mesmo fragmento. Para localizar chaves no mesmo fragmento, consulte [como as chaves são distribuídas em um cluster?](#how-are-keys-distributed-in-a-cluster)
* Se você estiver usando o provedor de estado de sessão do Redis ASP.NET, deverá usar o 2.0.1 ou superior. Veja posso [usar o clustering com o estado de sessão do Redis ASP.net e os provedores de cache de saída?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)

### <a name="how-are-keys-distributed-in-a-cluster"></a>Como as chaves são distribuídas em um cluster?
De acordo com a documentação do [modelo de distribuição de chaves](https://redis.io/topics/cluster-spec#keys-distribution-model) Redis: o espaço de chave é dividido em slots de 16384. Cada chave é codificada em hash e atribuída a um desses slots, que são distribuídos entre os nós do cluster. Você pode configurar qual parte da chave é configurada em hash para garantir que várias chaves estejam localizadas no mesmo fragmento usando marcas de hash.

* Chaves com uma marca de hash – se qualquer parte da chave estiver entre `{` e `}`, somente essa parte da chave será codificada para fins de determinação do slot de hash de uma chave. Por exemplo, as três chaves a seguir estarão localizadas no mesmo fragmento: `{key}1`, `{key}2`e `{key}3`, já que apenas a parte `key` do nome tem hash. Para obter uma lista completa de especificações de marca de hash de chaves, consulte [marcas de hash de chaves](https://redis.io/topics/cluster-spec#keys-hash-tags).
* Chaves sem uma marca de hash-o nome de chave inteiro é usado para hash. Isso resulta em uma distribuição estatisticamente uniforme entre os fragmentos do cache.

Para obter o melhor desempenho e a taxa de transferência, é recomendável distribuir as chaves uniformemente. Se você estiver usando chaves com uma marca de hash, é responsabilidade do aplicativo garantir que as chaves sejam distribuídas uniformemente.

Para obter mais informações, consulte [modelo de distribuição de chaves](https://redis.io/topics/cluster-spec#keys-distribution-model), [fragmentação de dados de cluster Redis](https://redis.io/topics/cluster-tutorial#redis-cluster-data-sharding)e [marcas de hash de chaves](https://redis.io/topics/cluster-spec#keys-hash-tags).

Para obter um exemplo de código sobre como trabalhar com clustering e localizar chaves no mesmo fragmento com o cliente StackExchange. Redis, consulte a parte [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) do exemplo de [Olá, mundo](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) .

### <a name="what-is-the-largest-cache-size-i-can-create"></a>Qual é o maior tamanho de cache que posso criar?
O maior tamanho de cache Premium é de 120 GB. Você pode criar até 10 fragmentos, fornecendo um tamanho máximo de 1,2 TB GB. Se você precisar de um tamanho maior, poderá [solicitar mais](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase). Para obter mais informações, consulte [Azure cache for Redis Pricing](https://azure.microsoft.com/pricing/details/cache/).

### <a name="do-all-redis-clients-support-clustering"></a>Todos os clientes do Redis dão suporte ao clustering?
Nem todos os clientes dão suporte ao clustering Redis! Verifique a documentação da biblioteca que você está usando para verificar se você está usando uma biblioteca e uma versão que dão suporte a clustering. StackExchange. Redis é uma biblioteca que oferece suporte a clustering, em suas versões mais recentes. Para obter mais informações sobre outros clientes, consulte a seção [jogando com o cluster](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster) do [tutorial do cluster Redis](https://redis.io/topics/cluster-tutorial). 

O protocolo de clustering Redis requer que cada cliente se conecte a cada fragmento diretamente no modo de clustering e também defina novas respostas de erro, como ' MOVEd ' na ' CROSSSLOTS '. A tentativa de usar um cliente que não dá suporte ao clustering com um cache de modo de cluster pode resultar em muitas [exceções de redirecionamento movidas](https://redis.io/topics/cluster-spec#moved-redirection)ou apenas interromper seu aplicativo, se você estiver fazendo solicitações de várias chaves de slot.

> [!NOTE]
> Se você estiver usando o StackExchange. Redis como seu cliente, verifique se está usando a versão mais recente do [stackexchange. Redis](https://www.nuget.org/packages/StackExchange.Redis/) 1.0.481 ou posterior para que o clustering funcione corretamente. Se você tiver problemas com as exceções de movimentação, consulte [mover exceções](#move-exceptions) para obter mais informações.
>

### <a name="how-do-i-connect-to-my-cache-when-clustering-is-enabled"></a>Como fazer conectar-se ao meu cache quando o clustering está habilitado?
Você pode se conectar ao seu cache usando os mesmos [pontos de extremidade](cache-configure.md#properties), [portas](cache-configure.md#properties)e [chaves](cache-configure.md#access-keys) que você usa ao se conectar a um cache que não tem o clustering habilitado. O Redis gerencia o clustering no back-end para que você não precise gerenciá-lo do seu cliente.

### <a name="can-i-directly-connect-to-the-individual-shards-of-my-cache"></a>Posso me conectar diretamente aos fragmentos individuais do meu cache?
O protocolo de clustering requer que o cliente faça as conexões de fragmento corretas. Portanto, o cliente deve fazer isso corretamente para você. Dito isso, cada fragmento consiste em um par de cache primário/de réplica, coletivamente conhecido como uma instância de cache. Você pode se conectar a essas instâncias de cache usando o utilitário Redis-CLI na ramificação [instável](https://redis.io/download) do repositório Redis no github. Esta versão implementa o suporte básico quando iniciada com a opção `-c`. Para obter mais informações, consulte [jogando com o cluster](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster) em [https://redis.io](https://redis.io) no [tutorial do cluster Redis](https://redis.io/topics/cluster-tutorial).

Para não SSL, use os comandos a seguir.

    Redis-cli.exe –h <<cachename>> -p 13000 (to connect to instance 0)
    Redis-cli.exe –h <<cachename>> -p 13001 (to connect to instance 1)
    Redis-cli.exe –h <<cachename>> -p 13002 (to connect to instance 2)
    ...
    Redis-cli.exe –h <<cachename>> -p 1300N (to connect to instance N)

Para SSL, substitua `1300N` por `1500N`.

### <a name="can-i-configure-clustering-for-a-previously-created-cache"></a>Posso configurar o clustering para um cache criado anteriormente?
Sim. Primeiro, verifique se o cache é Premium, dimensionando se não for. Em seguida, você deve ser capaz de ver as opções de configuração de cluster, incluindo uma opção para habilitar o cluster. Você pode alterar o tamanho do cluster após a criação do cache ou depois de ter habilitado o clustering pela primeira vez.

   >[!IMPORTANT]
   >Não é possível desfazer a habilitação do clustering. E um cache com clustering habilitado e apenas um fragmento se comporta de *forma diferente* de um cache do mesmo tamanho *sem* clustering.

### <a name="can-i-configure-clustering-for-a-basic-or-standard-cache"></a>Posso configurar o clustering para um cache básico ou Standard?
O clustering só está disponível para caches Premium.

### <a name="can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers"></a>Posso usar clustering com o estado de sessão do Redis ASP.NET e provedores de cache de saída?
* **Provedor de cache de saída Redis** -nenhuma alteração necessária.
* **Provedor de estado de sessão do Redis** – para usar o clustering, você deve usar o [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 ou superior ou uma exceção é lançada. Essa é uma alteração significativa; para obter mais informações, consulte [v 2.0.0 de alteração significativa](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details).

<a name="move-exceptions"></a>

### <a name="i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do"></a>Estou obtendo exceções de movimentação ao usar StackExchange. Redis e clustering, o que devo fazer?
Se você estiver usando StackExchange. Redis e receber `MOVE` exceções ao usar o clustering, verifique se está usando [stackexchange. Redis 1.1.603](https://www.nuget.org/packages/StackExchange.Redis/) ou posterior. Para obter instruções sobre como configurar seus aplicativos .NET para usar o StackExchange. Redis, consulte [configurar os clientes de cache](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

## <a name="next-steps"></a>Passos seguintes
Saiba como usar mais recursos de cache Premium.

* [Introdução ao cache do Azure para a camada Premium do Redis](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-clustering]: ./media/cache-how-to-premium-clustering/redis-cache-clustering.png

[redis-cache-clustering-selected]: ./media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png

[redis-cache-redis-cluster-size]: ./media/cache-how-to-premium-clustering/redis-cache-redis-cluster-size.png
