---
title: Configurar clustering do Redis-cache Premium do Azure para Redis
description: Saiba como criar e gerenciar o clustering Redis para o cache do Azure da camada Premium para instâncias do Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 06/13/2018
ms.openlocfilehash: 761c464730096eba36bc7c04227745cf362e5cc6
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76714460"
---
# <a name="how-to-configure-redis-clustering-for-a-premium-azure-cache-for-redis"></a>Como configurar o clustering do Redis para um cache Premium do Azure para Redis
O cache do Azure para Redis tem diferentes ofertas de cache, que fornecem flexibilidade na escolha do tamanho e dos recursos do cache, incluindo recursos da camada Premium, como clustering, persistência e suporte à rede virtual. Este artigo descreve como configurar o clustering em um cache do Azure Premium para a instância do Redis.

Para obter informações sobre outras funcionalidades de cache premium, consulte [Introdução ao Cache Azure para o nível Redis Premium](cache-premium-tier-intro.md).

## <a name="what-is-redis-cluster"></a>O que é o cluster Redis?
Azure Cache for Redis oferece cluster Redis como [implementado em Redis](https://redis.io/topics/cluster-tutorial). Com o cluster Redis, você obtém os seguintes benefícios: 

* A capacidade de dividir automaticamente seu conjunto de recursos entre vários nós. 
* A capacidade de continuar as operações quando um subconjunto de nós estiver apresentando falhas ou não conseguir se comunicar com o restante do cluster. 
* Mais taxa de transferência: a taxa de transferência aumenta linearmente à medida que você aumenta o número de fragmentos. 
* Mais tamanho de memória: aumenta linearmente à medida que você aumenta o número de fragmentos.  

O clustering não aumenta o número de conexões disponíveis para um cache clusterizado. Para mais informações sobre tamanho, entrada e largura de banda com caches premium, veja [o que azure cache para redis oferecendo e tamanho devo usar?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)

No Azure, o cluster Redis é oferecido como um modelo primário/de réplica, em que cada fragmento tem um par primário/de réplica com replicação em que a replicação é gerenciada pelo cache do Azure para o serviço Redis. 

## <a name="clustering"></a>Clustering
O agrupamento está ativado no **Novo Cache Azure para** a lâmina Redis durante a criação de cache. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

O agrupamento está configurado na lâmina **do Cluster Redis.**

![Clustering][redis-cache-clustering]

Você pode ter até 10 fragmentos no cluster. Clique **ativado** e deslize o slider ou escreva um número entre 1 e 10 para a contagem de **Fragmentos** e **clique**OK .

Cada fragmento é um par de cache primário/de réplica gerenciado pelo Azure e o tamanho total do cache é calculado multiplicando o número de fragmentos pelo tamanho do cache selecionado no tipo de preço. 

![Clustering][redis-cache-clustering-selected]

Depois que o cache é criado, você se conecta a ele e o usa como um cache não clusterizado e o Redis distribui os dados em todos os fragmentos de cache. Se o diagnóstico estiver [ativado,](cache-how-to-monitor.md#enable-cache-diagnostics)as métricas são capturadas separadamente para cada fragmento e podem ser [vistas](cache-how-to-monitor.md) no Azure Cache para a lâmina Redis. 

> [!NOTE]
> 
> Há algumas diferenças mínimas necessárias no aplicativo cliente quando o clustering é configurado. Para mais informações, consulte [Se preciso de fazer alterações na aplicação do meu cliente para utilizar o clustering?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 

Para obter um código de amostra no trabalho com o cliente StackExchange.Redis, consulte a [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) parte da amostra [Hello World.](https://github.com/rustd/RedisSamples/tree/master/HelloWorld)

<a name="cluster-size"></a>

## <a name="change-the-cluster-size-on-a-running-premium-cache"></a>Alterar o tamanho do cluster em um cache Premium em execução
Para alterar o tamanho do cluster numa cache premium em execução com clustering ativado, clique no Tamanho do **Cluster** a partir do **menu Recurso**.

![Tamanho do cluster Redis][redis-cache-redis-cluster-size]

Para alterar o tamanho do cluster, utilize o slider ou escreva um número entre 1 e 10 na caixa de texto **de contagem de fragmentos** e clique em **OK** para guardar.

Aumentar o tamanho do cluster aumenta a taxa de transferência máxima e o tamanho do cache. O aumento do tamanho do cluster não aumenta o máximo. conexões disponíveis para clientes.

> [!NOTE]
> A escalação de um cluster executa o comando [MIGRATE,](https://redis.io/commands/migrate) que é um comando caro, por isso, para o impacto mínimo, considere executar esta operação em horas não-pico. Durante o processo de migração, você verá um pico na carga do servidor. O dimensionamento de um cluster é um processo de execução demorada e a quantidade de tempo decorrido depende do número de chaves e do tamanho dos valores associados a essas chaves.
> 
> 

## <a name="clustering-faq"></a>Perguntas frequentes sobre clustering
A lista a seguir contém respostas para perguntas frequentes sobre o cache do Azure para clustering Redis.

* [Preciso de fazer alterações na aplicação do meu cliente para usar o agrupamento?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
* [Como as chaves são distribuídas num aglomerado?](#how-are-keys-distributed-in-a-cluster)
* [Qual é o maior tamanho de cache que posso criar?](#what-is-the-largest-cache-size-i-can-create)
* [Todos os clientes redis apoiam o agrupamento?](#do-all-redis-clients-support-clustering)
* [Como posso ligar-me à minha cache quando o agrupamento está ativado?](#how-do-i-connect-to-my-cache-when-clustering-is-enabled)
* [Posso ligar-me diretamente aos fragmentos individuais da minha cache?](#can-i-directly-connect-to-the-individual-shards-of-my-cache)
* [Posso configurar o agrupamento para uma cache previamente criada?](#can-i-configure-clustering-for-a-previously-created-cache)
* [Posso configurar o agrupamento para uma cache básica ou padrão?](#can-i-configure-clustering-for-a-basic-or-standard-cache)
* [Posso usar o agrupamento com os fornecedores redis ASP.NET Session State e Output Caching?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)
* [Estou a receber exceções move quando usar StackExchange.Redis e clustering, o que devo fazer?](#i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do)

### <a name="do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering"></a>É necessário fazer alterações no meu aplicativo cliente para usar o clustering?
* Quando o clustering está habilitado, somente o banco de dados 0 está disponível. Se o aplicativo cliente usar vários bancos de dados e tentar ler ou gravar em um banco de dados diferente de 0, a exceção a seguir será lançada. `Unhandled Exception: StackExchange.Redis.RedisConnectionException: ProtocolFailure on GET --->` `StackExchange.Redis.RedisCommandException: Multiple databases are not supported on this server; cannot switch to database: 6`
  
  Para mais informações, consulte a Especificação do [Cluster Redis - Subconjunto implementado](https://redis.io/topics/cluster-spec#implemented-subset).
* Se estiver a utilizar [stackExchange.Redis,](https://www.nuget.org/packages/StackExchange.Redis/)deve utilizar 1.0.481 ou mais tarde. Liga-se à cache utilizando os [mesmos pontos finais, portas e teclas](cache-configure.md#properties) que utiliza quando se liga a uma cache que não tem agrupada ativada. A única diferença é que todas as leituras e gravações devem ser feitas no banco de dados 0.
  
  * Outros clientes podem ter requisitos diferentes. Ver [Todos os clientes redis apoiam o agrupamento?](#do-all-redis-clients-support-clustering)
* Se seu aplicativo usa várias operações de chave em lote em um único comando, todas as chaves devem estar localizadas no mesmo fragmento. Para localizar as chaves no mesmo fragmento, vê [como as chaves são distribuídas num aglomerado?](#how-are-keys-distributed-in-a-cluster)
* Se você estiver usando o provedor de estado de sessão do Redis ASP.NET, deverá usar o 2.0.1 ou superior. Pode [usar o agrupamento com os fornecedores redis ASP.NET Session State e Output Caching?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)

### <a name="how-are-keys-distributed-in-a-cluster"></a>Como as chaves são distribuídas em um cluster?
De acordo com a documentação do modelo de distribuição Redis [Keys:](https://redis.io/topics/cluster-spec#keys-distribution-model) O espaço-chave é dividido em 16384 slots. Cada chave é codificada em hash e atribuída a um desses slots, que são distribuídos entre os nós do cluster. Você pode configurar qual parte da chave é configurada em hash para garantir que várias chaves estejam localizadas no mesmo fragmento usando marcas de hash.

* Chaves com uma etiqueta de hash - se alguma parte da chave estiver fechada em `{` e `}`- apenas essa parte da chave é hashed para efeitos de determinação da ranhura de hash de uma chave. Por exemplo, as seguintes 3 teclas estariam localizadas no mesmo fragmento: `{key}1`, `{key}2`e `{key}3`, uma vez que apenas a parte `key` do nome é hashed. Para obter uma lista completa das especificações de hash tag de chaves, consulte [as etiquetas](https://redis.io/topics/cluster-spec#keys-hash-tags)de hash Keys .
* Chaves sem uma marca de hash-o nome de chave inteiro é usado para hash. Isso resulta em uma distribuição estatisticamente uniforme entre os fragmentos do cache.

Para obter o melhor desempenho e a taxa de transferência, é recomendável distribuir as chaves uniformemente. Se você estiver usando chaves com uma marca de hash, é responsabilidade do aplicativo garantir que as chaves sejam distribuídas uniformemente.

Para mais informações, consulte o modelo de [distribuição de Chaves,](https://redis.io/topics/cluster-spec#keys-distribution-model) [redis Cluster data sharding](https://redis.io/topics/cluster-tutorial#redis-cluster-data-sharding), e [Keys hash tags](https://redis.io/topics/cluster-spec#keys-hash-tags).

Para obter um código de amostra no trabalho com o agrupamento e localização de chaves no mesmo fragmento com o cliente StackExchange.Redis, consulte a [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) parte da amostra [Hello World.](https://github.com/rustd/RedisSamples/tree/master/HelloWorld)

### <a name="what-is-the-largest-cache-size-i-can-create"></a>Qual é o maior tamanho de cache que posso criar?
O maior tamanho de cache Premium é de 120 GB. Você pode criar até 10 fragmentos, fornecendo um tamanho máximo de 1,2 TB GB. Se precisar de um tamanho maior, pode [pedir mais.](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase) Para mais informações, consulte [Azure Cache para preços redis](https://azure.microsoft.com/pricing/details/cache/).

### <a name="do-all-redis-clients-support-clustering"></a>Todos os clientes do Redis dão suporte ao clustering?
Nem todos os clientes dão suporte ao clustering Redis! Verifique a documentação da biblioteca que você está usando para verificar se você está usando uma biblioteca e uma versão que dão suporte a clustering. StackExchange. Redis é uma biblioteca que oferece suporte a clustering, em suas versões mais recentes. Para obter mais informações sobre outros clientes, consulte o [Jogo com a](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster) secção de cluster do tutorial do cluster [Redis](https://redis.io/topics/cluster-tutorial). 

O protocolo de clustering Redis requer que cada cliente se conecte a cada fragmento diretamente no modo de clustering e também defina novas respostas de erro, como ' MOVEd ' na ' CROSSSLOTS '. Tentar utilizar um cliente que não suporta o agrupamento com uma cache de modo cluster pode resultar em [muitas exceções de reorientação move- move,](https://redis.io/topics/cluster-spec#moved-redirection)ou apenas quebrar a sua aplicação, se estiver a fazer pedidos multi-chave de slot.

> [!NOTE]
> Se estiver a utilizar o StackExchange.Redis como cliente, certifique-se de que está a utilizar a versão mais recente do [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/) 1.0.481 ou mais tarde para o clustering funcionar corretamente. Se tiver algum problema com exceções de movimento, consulte [exceções](#move-exceptions) para mais informações.
>

### <a name="how-do-i-connect-to-my-cache-when-clustering-is-enabled"></a>Como fazer conectar-se ao meu cache quando o clustering está habilitado?
Pode ligar-se à sua cache utilizando os [mesmos pontos finais,](cache-configure.md#properties) [portas](cache-configure.md#properties)e [chaves](cache-configure.md#access-keys) que utiliza quando se liga a uma cache que não tenha agrupada ativada. O Redis gerencia o clustering no back-end para que você não precise gerenciá-lo do seu cliente.

### <a name="can-i-directly-connect-to-the-individual-shards-of-my-cache"></a>Posso me conectar diretamente aos fragmentos individuais do meu cache?
O protocolo de clustering requer que o cliente faça as conexões de fragmento corretas. Portanto, o cliente deve fazer isso corretamente para você. Dito isso, cada fragmento consiste em um par de cache primário/de réplica, coletivamente conhecido como uma instância de cache. Pode ligar-se a estas instâncias de cache utilizando a utilidade redis-cli no ramo [instável](https://redis.io/download) do repositório Redis no GitHub. Esta versão implementa suporte básico quando iniciado com o interruptor `-c`. Para mais informações, consulte [Brincar com o cluster](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster) no [https://redis.io](https://redis.io) no tutorial do [cluster Redis.](https://redis.io/topics/cluster-tutorial)

Para não SSL, use os comandos a seguir.

    Redis-cli.exe –h <<cachename>> -p 13000 (to connect to instance 0)
    Redis-cli.exe –h <<cachename>> -p 13001 (to connect to instance 1)
    Redis-cli.exe –h <<cachename>> -p 13002 (to connect to instance 2)
    ...
    Redis-cli.exe –h <<cachename>> -p 1300N (to connect to instance N)

Para ssl, substitua `1300N` por `1500N`.

### <a name="can-i-configure-clustering-for-a-previously-created-cache"></a>Posso configurar o clustering para um cache criado anteriormente?
Sim. Primeiro, verifique se o cache é Premium, dimensionando se não for. Em seguida, você deve ser capaz de ver as opções de configuração de cluster, incluindo uma opção para habilitar o cluster. Você pode alterar o tamanho do cluster após a criação do cache ou depois de ter habilitado o clustering pela primeira vez.

   >[!IMPORTANT]
   >Não é possível desfazer a habilitação do clustering. E uma cache com agrupamento habilitado e apenas um fragmento comporta-se *de forma diferente* de uma cache do mesmo tamanho *sem* agrupamento.

### <a name="can-i-configure-clustering-for-a-basic-or-standard-cache"></a>Posso configurar o clustering para um cache básico ou Standard?
O clustering só está disponível para caches Premium.

### <a name="can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers"></a>Posso usar clustering com o estado de sessão do Redis ASP.NET e provedores de cache de saída?
* **Redis Output Cache fornecedor** - não são necessárias alterações.
* **Redis Session State provider** - para utilizar o agrupamento, você deve usar [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 ou superior ou uma exceção é lançada. Esta é uma mudança de rutura; para mais informações, consulte [v2.0.0 Detalhes de mudança de rutura](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details).

<a name="move-exceptions"></a>

### <a name="i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do"></a>Estou obtendo exceções de movimentação ao usar StackExchange. Redis e clustering, o que devo fazer?
Se estiver a utilizar stackExchange.Redis e receber `MOVE` exceções ao utilizar o clustering, certifique-se de que está a utilizar [stackExchange.Redis 1.1.603](https://www.nuget.org/packages/StackExchange.Redis/) ou mais tarde. Para obter instruções sobre a configuração das suas aplicações .NET para utilizar StackExchange.Redis, consulte [Configurar os clientes cache](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

## <a name="next-steps"></a>Passos Seguintes
Saiba como usar mais recursos de cache Premium.

* [Introdução ao Cache Azure para o nível Redis Premium](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-clustering]: ./media/cache-how-to-premium-clustering/redis-cache-clustering.png

[redis-cache-clustering-selected]: ./media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png

[redis-cache-redis-cluster-size]: ./media/cache-how-to-premium-clustering/redis-cache-redis-cluster-size.png
