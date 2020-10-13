---
title: Configurar o agrupamento Redis - Premium Azure Cache para Redis
description: Saiba como criar e gerir o agrupamento Redis para o seu Azure Cache de nível Premium para instâncias Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 06/13/2018
ms.openlocfilehash: d37aa275a07586738bf7416cee6611bdc8284df3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88004764"
---
# <a name="how-to-configure-redis-clustering-for-a-premium-azure-cache-for-redis"></a>Como configurar o agrupamento Redis para um Premium Azure Cache para Redis
O Azure Cache para Redis tem diferentes ofertas de cache, que proporcionam flexibilidade na escolha do tamanho e funcionalidades da cache, incluindo características de nível Premium, tais como clustering, persistência e suporte de rede virtual. Este artigo descreve como configurar o agrupamento numa cache Azure premium para o exemplo de Redis.

## <a name="what-is-redis-cluster"></a>O que é Redis Cluster?
Azure Cache para Redis oferece cluster Redis como [implementado em Redis](https://redis.io/topics/cluster-tutorial). Com o Redis Cluster, obtém os seguintes benefícios: 

* A capacidade de dividir automaticamente o conjunto de dados entre vários nós. 
* A capacidade de continuar as operações quando um subconjunto dos nós está a sofrer falhas ou não consegue comunicar com o resto do cluster. 
* Mais produção: A produção aumenta linearmente à medida que aumenta o número de fragmentos. 
* Mais tamanho da memória: Aumenta linearmente à medida que aumenta o número de fragmentos.  

O agrupamento não aumenta o número de ligações disponíveis para uma cache agrupada. Para obter mais informações sobre tamanho, produção e largura de banda com caches premium, consulte [Escolher o nível certo](cache-overview.md#choosing-the-right-tier)

Em Azure, o cluster Redis é oferecido como um modelo primário/réplica onde cada fragmento tem um par primário/réplica com replicação onde a replicação é gerida por Azure Cache para o serviço Redis. 

## <a name="clustering"></a>Clustering
O agrupamento está ativado na **Cache New Azure para lâmina Redis** durante a criação da cache. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

O agrupamento está configurado na lâmina do **Redis Cluster.**

![Clustering][redis-cache-clustering]

Pode ter até 10 fragmentos no aglomerado. Clique **em Ativado** e deslize o slider ou escreva um número entre 1 e 10 para **a contagem de fragmentos** e clique **em OK**.

Cada fragmento é um par de cache primário/réplica gerido por Azure, e o tamanho total da cache é calculado multiplicando o número de fragmentos pelo tamanho da cache selecionado no nível de preços. 

![Clustering][redis-cache-clustering-selected]

Uma vez criada a cache, liga-se a ele e utiliza-se como uma cache não agrupada, e o Redis distribui os dados através dos fragmentos de Cache. Se os diagnósticos [estiverem ativados,](cache-how-to-monitor.md#enable-cache-diagnostics)as métricas são capturadas separadamente para cada fragmento e podem ser [vistas](cache-how-to-monitor.md) na Cache Azure para lâmina Redis. 

> [!NOTE]
> 
> Existem algumas pequenas diferenças necessárias na sua aplicação de cliente quando o clustering é configurado. Para mais informações, consulte [Preciso de fazer alterações à aplicação do meu cliente para utilizar o clustering?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 

Para obter o código de amostra sobre o trabalho de agrupamento com o cliente StackExchange.Redis, consulte a [parte clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) da amostra [Hello World.](https://github.com/rustd/RedisSamples/tree/master/HelloWorld)

<a name="cluster-size"></a>

## <a name="change-the-cluster-size-on-a-running-premium-cache"></a>Mude o tamanho do cluster numa cache premium de corrida
Para alterar o tamanho do cluster numa cache premium de execução com o clustering ativado, clique no Tamanho do **Cluster** a partir do **menu Recursos**.

![Tamanho do cluster Redis][redis-cache-redis-cluster-size]

Para alterar o tamanho do cluster, utilize o slider ou escreva um número entre 1 e 10 na caixa de texto **de contagem de fragmentos** e clique **em OK** para guardar.

Aumentar o tamanho do cluster aumenta a potência máxima e o tamanho da cache. Aumentar o tamanho do cluster não aumenta as ligações máximas disponíveis para os clientes.

> [!NOTE]
> Escalar um cluster executa o comando [MIGRATE,](https://redis.io/commands/migrate) que é um comando caro, por isso, para o mínimo impacto, considere executar esta operação durante horas não-pico. Durante o processo de migração, verá um pico na carga do servidor. Escalar um cluster é um processo de execução longo e o tempo de duração dependerá do número de chaves e do tamanho dos valores associados a essas teclas.
> 
> 

## <a name="clustering-faq"></a>FaQ de agrupamento
A lista que se segue contém respostas para perguntas comumente feitas sobre Azure Cache para o agrupamento Redis.

* [Preciso de fazer alguma alteração na aplicação do meu cliente para usar o agrupamento?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
* [Como as chaves são distribuídas num aglomerado?](#how-are-keys-distributed-in-a-cluster)
* [Qual é o maior tamanho de cache que posso criar?](#what-is-the-largest-cache-size-i-can-create)
* [Todos os clientes redis suportam o agrupamento?](#do-all-redis-clients-support-clustering)
* [Como posso ligar-me à minha cache quando o agrupamento está ativado?](#how-do-i-connect-to-my-cache-when-clustering-is-enabled)
* [Posso ligar-me diretamente aos fragmentos individuais da minha cache?](#can-i-directly-connect-to-the-individual-shards-of-my-cache)
* [Posso configurar o agrupamento para uma cache previamente criada?](#can-i-configure-clustering-for-a-previously-created-cache)
* [Posso configurar o agrupamento para uma cache básica ou padrão?](#can-i-configure-clustering-for-a-basic-or-standard-cache)
* [Posso utilizar o agrupamento com os fornecedores redis ASP.NET Session State e output caching?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)
* [Estou a receber exceções ao usar StackExchange.Redis e agrupamento, o que devo fazer?](#i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do)

### <a name="do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering"></a>Preciso de fazer alguma alteração na aplicação do meu cliente para usar o agrupamento?
* Quando o agrupamento está ativado, apenas a base de dados 0 está disponível. Se a sua aplicação de clientes utilizar várias bases de dados e tentar ler ou escrever para uma base de dados diferente de 0, a seguinte exceção é lançada. `Unhandled Exception: StackExchange.Redis.RedisConnectionException: ProtocolFailure on GET --->` `StackExchange.Redis.RedisCommandException: Multiple databases are not supported on this server; cannot switch to database: 6`
  
  Para obter mais informações, consulte [a Especificação do Cluster Redis - Subconjunto implementado.](https://redis.io/topics/cluster-spec#implemented-subset)
* Se estiver a utilizar [o StackExchange.Redis,](https://www.nuget.org/packages/StackExchange.Redis/)tem de utilizar 1.0.481 ou mais tarde. Ligue-se à cache utilizando os [mesmos pontos finais, portas e chaves](cache-configure.md#properties) que utiliza ao ligar a uma cache que não tem o agrupamento ativado. A única diferença é que todas as leituras e escritos devem ser feitos na base de dados 0.
  
  Outros clientes podem ter requisitos diferentes. [Vê todos os clientes redis suportam agrupamento?](#do-all-redis-clients-support-clustering)
* Se a sua aplicação utilizar várias operações de chave num único comando, todas as chaves devem estar localizadas no mesmo fragmento. Para localizar as chaves no mesmo fragmento, vê [como as chaves são distribuídas num aglomerado?](#how-are-keys-distributed-in-a-cluster)
* Se estiver a utilizar o fornecedor Redis ASP.NET Session State, deve utilizar 2.0.1 ou superior. Ver [Posso usar o agrupamento com os fornecedores redis ASP.NET Session State e Output Caching?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)

### <a name="how-are-keys-distributed-in-a-cluster"></a>Como as chaves são distribuídas num aglomerado?
De acordo com a documentação do modelo de distribuição Redis [Keys:](https://redis.io/topics/cluster-spec#keys-distribution-model) O espaço chave é dividido em 16384 slots. Cada chave é hashed e atribuído a uma destas ranhuras, que são distribuídas pelos nós do cluster. Pode configurar qual parte da chave é hashed para garantir que várias teclas estão localizadas no mesmo fragmento usando hash tags.

* Chaves com uma etiqueta de haxixe - se alguma parte da chave estiver fechada `{` e `}` , apenas essa parte da chave é hashed para efeitos de determinação da ranhura de haxixe de uma chave. Por exemplo, as seguintes 3 teclas estariam localizadas no mesmo fragmento: `{key}1` , e uma vez que `{key}2` `{key}3` apenas a parte do nome é `key` hashed. Para obter uma lista completa das especificações de etiquetas de hash de teclas, consulte [as etiquetas de hash keys](https://redis.io/topics/cluster-spec#keys-hash-tags).
* Chaves sem hash tag - todo o nome chave é usado para hashing. Isto resulta numa distribuição estatisticamente uniforme através dos fragmentos da cache.

Para melhor desempenho e produção, recomendamos distribuir as chaves uniformemente. Se estiver a utilizar as teclas com uma etiqueta de haxixe, é da responsabilidade da aplicação garantir que as teclas são distribuídas uniformemente.

Para obter mais informações, consulte [o modelo de distribuição keys,](https://redis.io/topics/cluster-spec#keys-distribution-model)o fragmento de [dados do Redis Cluster](https://redis.io/topics/cluster-tutorial#redis-cluster-data-sharding)e as [etiquetas de hash keys](https://redis.io/topics/cluster-spec#keys-hash-tags).

Para obter o código de amostra sobre o trabalho com as chaves de agrupamento e localização no mesmo fragmento com o cliente StackExchange.Redis, consulte a parte [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) da amostra [Hello World.](https://github.com/rustd/RedisSamples/tree/master/HelloWorld)

### <a name="what-is-the-largest-cache-size-i-can-create"></a>Qual é o maior tamanho de cache que posso criar?
O maior tamanho de cache premium é de 120 GB. Pode criar até 10 fragmentos dando-lhe um tamanho máximo de 1,2TB GB. Se precisar de um tamanho maior, pode [solicitar mais.](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase) Para mais informações, consulte [a Cache Azure para redis pricing](https://azure.microsoft.com/pricing/details/cache/).

### <a name="do-all-redis-clients-support-clustering"></a>Todos os clientes redis suportam o agrupamento?
Nem todos os clientes apoiam o agrupamento Redis! Verifique a documentação da biblioteca que está a usar, para verificar se está a utilizar uma biblioteca e versão que suportam o agrupamento. StackExchange.Redis é uma biblioteca que suporta o agrupamento, nas suas versões mais recentes. Para obter mais informações sobre outros clientes, consulte a secção [de Jogo com a](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster) secção de cluster do tutorial do cluster [Redis.](https://redis.io/topics/cluster-tutorial) 

O protocolo de clustering Redis requer que cada cliente se conecte a cada fragmento diretamente no modo de agrupamento, e também define novas respostas de erro, como 'MOVE' na 'CROSSSLOTS'. Tentar utilizar um cliente que não suporte o agrupamento com uma cache de modo cluster pode resultar em [muitas exceções](https://redis.io/topics/cluster-spec#moved-redirection)de redirecionamento MOVE , ou apenas quebrar a sua aplicação, se estiver a fazer pedidos multi-chave de slot cruzada.

> [!NOTE]
> Se estiver a utilizar o StackExchange.Redis como cliente, certifique-se de que está a utilizar a versão mais recente do [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/) 1.0.481 ou mais tarde para que o agrupamento funcione corretamente. Se tiver algum problema com exceções de movimento, consulte [as exceções para](#move-exceptions) mais informações.
>

### <a name="how-do-i-connect-to-my-cache-when-clustering-is-enabled"></a>Como posso ligar-me à minha cache quando o agrupamento está ativado?
Pode ligar-se ao seu cache utilizando os [mesmos pontos finais,](cache-configure.md#properties) [portas](cache-configure.md#properties)e [chaves](cache-configure.md#access-keys) que utiliza ao ligar a uma cache que não tem o agrupamento ativado. O Redis gere o agrupamento no backend para não teres de o gerir a partir do teu cliente.

### <a name="can-i-directly-connect-to-the-individual-shards-of-my-cache"></a>Posso ligar-me diretamente aos fragmentos individuais da minha cache?
O protocolo de agrupamento requer que o cliente faça as ligações de fragmentos corretas. Então o cliente deve fazer isto corretamente por si. Dito isto, cada fragmento consiste de um par de cache primário/réplica, coletivamente conhecido como um cache instance. Pode ligar-se a estas instâncias de cache usando o utilitário redis-cli no ramo [instável](https://redis.io/download) do repositório Redis no GitHub. Esta versão implementa suporte básico quando começa com o `-c` interruptor. Para obter mais informações, consulte ["Jogar com o cluster"](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster) [https://redis.io](https://redis.io) no tutorial do cluster [Redis.](https://redis.io/topics/cluster-tutorial)

Para não-TLS, utilize os seguintes comandos.

```bash
Redis-cli.exe –h <<cachename>> -p 13000 (to connect to instance 0)
Redis-cli.exe –h <<cachename>> -p 13001 (to connect to instance 1)
Redis-cli.exe –h <<cachename>> -p 13002 (to connect to instance 2)
...
Redis-cli.exe –h <<cachename>> -p 1300N (to connect to instance N)
```

Para TLS, `1300N` substitua- por `1500N` .

### <a name="can-i-configure-clustering-for-a-previously-created-cache"></a>Posso configurar o agrupamento para uma cache previamente criada?
Sim. Primeiro, certifique-se de que o seu cache é premium, escalando se não for. Em seguida, deverá ser capaz de ver as opções de configuração do cluster, incluindo uma opção para ativar o cluster. Pode alterar o tamanho do cluster após a criação da cache, ou depois de ter ativado o agrupamento pela primeira vez.

   >[!IMPORTANT]
   >Não se pode desfazer o agrupamento. E uma cache com agrupamento ativado e apenas um fragmento comporta-se de *forma diferente* de uma cache do mesmo tamanho *sem* agrupamento.

### <a name="can-i-configure-clustering-for-a-basic-or-standard-cache"></a>Posso configurar o agrupamento para uma cache básica ou padrão?
O agrupamento só está disponível para caches premium.

### <a name="can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers"></a>Posso utilizar o agrupamento com os fornecedores redis ASP.NET Session State e output caching?
* **Reensitado fornecedor de Cache de Saída Redis** - não são necessárias alterações.
* **Redis Session State provider** - para utilizar o clustering, você deve usar [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 ou superior ou uma exceção é lançada. Esta é uma mudança de rutura; para obter mais informações, consulte [v2.0.0 Breaking Change Details](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details).

<a name="move-exceptions"></a>

### <a name="i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do"></a>Estou a receber exceções ao usar StackExchange.Redis e agrupamento, o que devo fazer?
Se estiver a utilizar o StackExchange.Redis e receber `MOVE` exceções ao utilizar o clustering, certifique-se de que está a utilizar [o StackExchange.Redis 1.1.603](https://www.nuget.org/packages/StackExchange.Redis/) ou mais tarde. Para obter instruções sobre a configuração das suas aplicações .NET para utilizar o StackExchange.Redis, consulte [configurar os clientes cache](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre o Azure Cache para funcionalidades redis.

* [Cache Azure para os níveis de serviço Redis Premium](cache-overview.md#service-tiers)

<!-- IMAGES -->

[redis-cache-clustering]: ./media/cache-how-to-premium-clustering/redis-cache-clustering.png

[redis-cache-clustering-selected]: ./media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png

[redis-cache-redis-cluster-size]: ./media/cache-how-to-premium-clustering/redis-cache-redis-cluster-size.png
