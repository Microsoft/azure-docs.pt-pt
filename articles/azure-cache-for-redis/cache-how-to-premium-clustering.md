---
title: Configure Redis clustering - Premium Azure Cache for Redis
description: Aprenda a criar e gerir o clustering Redis para o seu premium nível Azure Cache para instâncias Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 06/13/2018
ms.openlocfilehash: 761c464730096eba36bc7c04227745cf362e5cc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278041"
---
# <a name="how-to-configure-redis-clustering-for-a-premium-azure-cache-for-redis"></a>Como configurar o agrupamento Redis para um Premium Azure Cache para Redis
O Azure Cache for Redis tem diferentes ofertas de cache, que proporcionam flexibilidade na escolha do tamanho e funcionalidades do cache, incluindo funcionalidades de nível Premium, como clustering, persistência e suporte de rede virtual. Este artigo descreve como configurar o agrupamento num azure cache premium para o caso Redis.

Para obter informações sobre outras funcionalidades de cache premium, consulte [Introdução ao Cache Azure para o nível Redis Premium](cache-premium-tier-intro.md).

## <a name="what-is-redis-cluster"></a>O que é o Aglomerado Redis?
Azure Cache for Redis oferece cluster Redis como [implementado em Redis](https://redis.io/topics/cluster-tutorial). Com o Redis Cluster, obtém-se os seguintes benefícios: 

* A capacidade de dividir automaticamente o seu conjunto de dados entre vários nódosos. 
* A capacidade de continuar as operações quando um subconjunto dos nós está a sofrer falhas ou é incapaz de comunicar com o resto do cluster. 
* Mais entrada: A entrada aumenta linearmente à medida que aumenta o número de fragmentos. 
* Mais tamanho de memória: Aumenta linearmente à medida que aumenta o número de fragmentos.  

O agrupamento não aumenta o número de ligações disponíveis para uma cache agrupada. Para mais informações sobre tamanho, entrada e largura de banda com caches premium, veja [o que azure cache para redis oferecendo e tamanho devo usar?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)

Em Azure, o cluster Redis é oferecido como um modelo primário/réplica onde cada fragmento tem um par primário/réplica com replicação onde a replicação é gerida por Azure Cache para o serviço Redis. 

## <a name="clustering"></a>Clustering
O agrupamento está ativado no **Novo Cache Azure para** a lâmina Redis durante a criação de cache. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

O agrupamento está configurado na lâmina **do Cluster Redis.**

![Clustering][redis-cache-clustering]

Pode ter até 10 cacos no aglomerado. Clique **ativado** e deslize o slider ou escreva um número entre 1 e 10 para a contagem de **Fragmentos** e **clique**OK .

Cada fragmento é um par de cache primário/réplica gerido por Azure, e o tamanho total da cache é calculado multiplicando o número de fragmentos pelo tamanho da cache selecionado no nível de preços. 

![Clustering][redis-cache-clustering-selected]

Assim que a cache é criada, liga-se a ela e usa-a como uma cache não agrupada, e o Redis distribui os dados pelos fragmentos da Cache. Se o diagnóstico estiver [ativado,](cache-how-to-monitor.md#enable-cache-diagnostics)as métricas são capturadas separadamente para cada fragmento e podem ser [vistas](cache-how-to-monitor.md) no Azure Cache para a lâmina Redis. 

> [!NOTE]
> 
> Existem algumas pequenas diferenças necessárias na sua aplicação de cliente quando o clustering é configurado. Para mais informações, consulte [Se preciso de fazer alterações na aplicação do meu cliente para utilizar o clustering?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 

Para obter um código de amostra no trabalho com o cliente StackExchange.Redis, consulte a [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) parte da amostra [Hello World.](https://github.com/rustd/RedisSamples/tree/master/HelloWorld)

<a name="cluster-size"></a>

## <a name="change-the-cluster-size-on-a-running-premium-cache"></a>Alterar o tamanho do cluster numa cache premium em execução
Para alterar o tamanho do cluster numa cache premium em execução com clustering ativado, clique no Tamanho do **Cluster** a partir do **menu Recurso**.

![Tamanho do cluster redis][redis-cache-redis-cluster-size]

Para alterar o tamanho do cluster, utilize o slider ou escreva um número entre 1 e 10 na caixa de texto **de contagem de fragmentos** e clique em **OK** para guardar.

Aumentar o tamanho do cluster aumenta a potência máxima e o tamanho da cache. Aumentar o tamanho do cluster não aumenta o máximo. conexões disponíveis para clientes.

> [!NOTE]
> A escalação de um cluster executa o comando [MIGRATE,](https://redis.io/commands/migrate) que é um comando caro, por isso, para o impacto mínimo, considere executar esta operação em horas não-pico. Durante o processo de migração, verá um pico na carga do servidor. A escalação de um cluster é um processo de longo curso e a quantidade de tempo decorrido depende do número de chaves e tamanho dos valores associados a essas teclas.
> 
> 

## <a name="clustering-faq"></a>FaQ de agrupamento
A lista que se segue contém respostas a perguntas comumente feitas sobre o agrupamento Azure Cache para redis.

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

### <a name="do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering"></a>Preciso de fazer alterações na aplicação do meu cliente para usar o agrupamento?
* Quando o agrupamento está ativado, apenas a base de dados 0 está disponível. Se a sua aplicação do cliente utilizar várias bases de dados e tentar ler ou escrever para uma base de dados diferente de 0, é lançada a seguinte exceção. `Unhandled Exception: StackExchange.Redis.RedisConnectionException: ProtocolFailure on GET --->` `StackExchange.Redis.RedisCommandException: Multiple databases are not supported on this server; cannot switch to database: 6`
  
  Para mais informações, consulte a Especificação do [Cluster Redis - Subconjunto implementado](https://redis.io/topics/cluster-spec#implemented-subset).
* Se estiver a utilizar [stackExchange.Redis,](https://www.nuget.org/packages/StackExchange.Redis/)deve utilizar 1.0.481 ou mais tarde. Liga-se à cache utilizando os [mesmos pontos finais, portas e teclas](cache-configure.md#properties) que utiliza quando se liga a uma cache que não tem agrupada ativada. A única diferença é que todos os comentários que sejam feitos devem ser feitos à base de dados 0.
  
  * Outros clientes podem ter requisitos diferentes. Ver [Todos os clientes redis apoiam o agrupamento?](#do-all-redis-clients-support-clustering)
* Se a sua aplicação utilizar múltiplas operações de chave num único comando, todas as teclas devem estar localizadas no mesmo fragmento. Para localizar as chaves no mesmo fragmento, vê [como as chaves são distribuídas num aglomerado?](#how-are-keys-distributed-in-a-cluster)
* Se estiver a utilizar o Redis ASP.NET fornecedor do Session State, deve utilizar 2.0.1 ou mais. Pode [usar o agrupamento com os fornecedores redis ASP.NET Session State e Output Caching?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)

### <a name="how-are-keys-distributed-in-a-cluster"></a>Como as chaves são distribuídas num aglomerado?
De acordo com a documentação do modelo de distribuição Redis [Keys:](https://redis.io/topics/cluster-spec#keys-distribution-model) O espaço-chave é dividido em 16384 slots. Cada chave é hashed e atribuída a uma destas ranhuras, que são distribuídas pelos nós do cluster. Pode configurar qual a parte da chave é hashed para garantir que várias teclas estão localizadas no mesmo fragmento usando hash tags.

* Chaves com uma etiqueta de hash - se `{` alguma `}`parte da chave estiver fechada e , apenas essa parte da chave é hashed para efeitos de determinação da ranhura hash de uma chave. Por exemplo, as seguintes 3 teclas estariam `{key}1` `{key}2`localizadas `{key}3` no `key` mesmo fragmento: , e uma vez que apenas a parte do nome é hashed. Para obter uma lista completa das especificações de hash tag de chaves, consulte [as etiquetas](https://redis.io/topics/cluster-spec#keys-hash-tags)de hash Keys .
* Chaves sem hash tag - todo o nome chave é usado para hashing. Isto resulta numa distribuição estatisticamente uniforme nos fragmentos da cache.

Para melhor desempenho e entrada, recomendamos distribuir as teclas uniformemente. Se estiver a utilizar chaves com uma etiqueta de hash, é da responsabilidade da aplicação garantir que as chaves são distribuídas uniformemente.

Para mais informações, consulte o modelo de [distribuição de Chaves,](https://redis.io/topics/cluster-spec#keys-distribution-model) [redis Cluster data sharding](https://redis.io/topics/cluster-tutorial#redis-cluster-data-sharding), e [Keys hash tags](https://redis.io/topics/cluster-spec#keys-hash-tags).

Para obter um código de amostra no trabalho com o agrupamento e localização de chaves no mesmo fragmento com o cliente StackExchange.Redis, consulte a [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) parte da amostra [Hello World.](https://github.com/rustd/RedisSamples/tree/master/HelloWorld)

### <a name="what-is-the-largest-cache-size-i-can-create"></a>Qual é o maior tamanho de cache que posso criar?
O maior tamanho de cache premium é de 120 GB. Pode criar até 10 fragmentos, dando-lhe um tamanho máximo de 1,2TB GB. Se precisar de um tamanho maior, pode [pedir mais.](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase) Para mais informações, consulte [Azure Cache para preços redis](https://azure.microsoft.com/pricing/details/cache/).

### <a name="do-all-redis-clients-support-clustering"></a>Todos os clientes redis apoiam o agrupamento?
Nem todos os clientes apoiam o agrupamento Redis! Verifique a documentação da biblioteca que está a usar, para verificar se está a utilizar uma biblioteca e versão que suportam o agrupamento. StackExchange.Redis é uma biblioteca que suporta o agrupamento, nas suas versões mais recentes. Para obter mais informações sobre outros clientes, consulte o [Jogo com a](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster) secção de cluster do tutorial do cluster [Redis](https://redis.io/topics/cluster-tutorial). 

O protocolo de clustering Redis exige que cada cliente se conectem diretamente a cada fragmento no modo de agrupamento, e também define novas respostas de erro, como 'MOVED' na 'CROSSSLOTS'. Tentar utilizar um cliente que não suporta o agrupamento com uma cache de modo cluster pode resultar em [muitas exceções de reorientação move- move,](https://redis.io/topics/cluster-spec#moved-redirection)ou apenas quebrar a sua aplicação, se estiver a fazer pedidos multi-chave de slot.

> [!NOTE]
> Se estiver a utilizar o StackExchange.Redis como cliente, certifique-se de que está a utilizar a versão mais recente do [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/) 1.0.481 ou mais tarde para o clustering funcionar corretamente. Se tiver algum problema com exceções de movimento, consulte [exceções](#move-exceptions) para mais informações.
>

### <a name="how-do-i-connect-to-my-cache-when-clustering-is-enabled"></a>Como posso ligar-me à minha cache quando o agrupamento está ativado?
Pode ligar-se à sua cache utilizando os [mesmos pontos finais,](cache-configure.md#properties) [portas](cache-configure.md#properties)e [chaves](cache-configure.md#access-keys) que utiliza quando se liga a uma cache que não tenha agrupada ativada. Redis gere o agrupamento na parte de trás para que não tenha que geri-lo do seu cliente.

### <a name="can-i-directly-connect-to-the-individual-shards-of-my-cache"></a>Posso ligar-me diretamente aos fragmentos individuais da minha cache?
O protocolo de agrupamento requer que o cliente faça as ligações corretas. Então o cliente deve fazer isto corretamente por si. Com isto dito, cada fragmento consiste num par de cache primário/réplica, coletivamente conhecido como um caso de cache. Pode ligar-se a estas instâncias de cache utilizando a utilidade redis-cli no ramo [instável](https://redis.io/download) do repositório Redis no GitHub. Esta versão implementa suporte básico `-c` quando iniciado com a switch. Para mais informações, consulte [https://redis.io](https://redis.io) Brincar com o [cluster](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster) no tutorial do cluster [Redis](https://redis.io/topics/cluster-tutorial).

Para não-ssl, utilize os seguintes comandos.

    Redis-cli.exe –h <<cachename>> -p 13000 (to connect to instance 0)
    Redis-cli.exe –h <<cachename>> -p 13001 (to connect to instance 1)
    Redis-cli.exe –h <<cachename>> -p 13002 (to connect to instance 2)
    ...
    Redis-cli.exe –h <<cachename>> -p 1300N (to connect to instance N)

Para ssl, `1300N` `1500N`substitua-o por .

### <a name="can-i-configure-clustering-for-a-previously-created-cache"></a>Posso configurar o agrupamento para uma cache previamente criada?
Sim. Primeiro certifique-se de que a sua cache é premium, escalando se não for. Em seguida, você deve ser capaz de ver as opções de configuração do cluster, incluindo uma opção para ativar o cluster. Pode alterar o tamanho do cluster após a criação da cache, ou depois de ter ativado o agrupamento pela primeira vez.

   >[!IMPORTANT]
   >Não se pode desfazer o agrupamento de habilitação. E uma cache com agrupamento habilitado e apenas um fragmento comporta-se *de forma diferente* de uma cache do mesmo tamanho *sem* agrupamento.

### <a name="can-i-configure-clustering-for-a-basic-or-standard-cache"></a>Posso configurar o agrupamento para uma cache básica ou padrão?
O agrupamento só está disponível para caches premium.

### <a name="can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers"></a>Posso usar o agrupamento com os fornecedores redis ASP.NET Session State e Output Caching?
* **Redis Output Cache fornecedor** - não são necessárias alterações.
* **Redis Session State provider** - para utilizar o agrupamento, você deve usar [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 ou superior ou uma exceção é lançada. Esta é uma mudança de rutura; para mais informações, consulte [v2.0.0 Detalhes de mudança de rutura](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details).

<a name="move-exceptions"></a>

### <a name="i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do"></a>Estou a receber exceções move quando usar StackExchange.Redis e clustering, o que devo fazer?
Se estiver a utilizar stackExchange.Redis e receber `MOVE` exceções ao utilizar o clustering, certifique-se de que está a utilizar [stackExchange.Redis 1.1.603](https://www.nuget.org/packages/StackExchange.Redis/) ou mais tarde. Para obter instruções sobre a configuração das suas aplicações .NET para utilizar StackExchange.Redis, consulte [Configurar os clientes cache](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

## <a name="next-steps"></a>Passos seguintes
Saiba como usar mais funcionalidades de cache premium.

* [Introdução ao Cache Azure para o nível Redis Premium](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-clustering]: ./media/cache-how-to-premium-clustering/redis-cache-clustering.png

[redis-cache-clustering-selected]: ./media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png

[redis-cache-redis-cluster-size]: ./media/cache-how-to-premium-clustering/redis-cache-redis-cluster-size.png
