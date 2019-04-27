---
title: Como configurar o clustering de Redis para uma Cache do Azure Premium para Redis | Documentos da Microsoft
description: Saiba como criar e gerir o clustering de Redis para o escalão Premium Cache do Azure para instâncias de Redis
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 62208eec-52ae-4713-b077-62659fd844ab
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 06/13/2018
ms.author: yegu
ms.openlocfilehash: 602d77f3d4e8ed10c2c964462bc2dc21240cef5c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60541391"
---
# <a name="how-to-configure-redis-clustering-for-a-premium-azure-cache-for-redis"></a>Como configurar o clustering de Redis para uma Cache do Azure Premium para Redis
A Cache de Redis do Azure tem ofertas de cache diferente, que fornecem flexibilidade na escolha de tamanho de cache e funcionalidades, incluindo as funcionalidades do escalão Premium, tais como clustering, persistência e suporte de rede virtual. Este artigo descreve como configurar o clustering num Cache do Azure de premium para a instância de Redis.

Para obter informações sobre outras funcionalidades de cache premium, consulte [introdução à Cache do Azure para o escalão Premium da Redis](cache-premium-tier-intro.md).

## <a name="what-is-redis-cluster"></a>O que é o Cluster de Redis?
A Cache de Redis do Azure oferece o cluster de Redis como [implementados no Redis](https://redis.io/topics/cluster-tutorial). Com o Cluster de Redis, obtém os seguintes benefícios: 

* A capacidade de dividir automaticamente o seu conjunto de dados entre vários nós. 
* A capacidade continue operações quando um subconjunto de nós está com falhas ou não é possível comunicar com o restante do cluster. 
* Maior débito: Débito aumenta linearmente à medida que aumenta o número de partições horizontais. 
* Tamanho de memória mais: Aumenta linearmente à medida que aumenta o número de partições horizontais.  

Clustering não aumenta o número de ligações disponíveis para uma cache em cluster. Para obter mais informações sobre o tamanho, débito e largura de banda com premium caches, consulte [que a Cache do Azure para o tamanho e oferta de Redis devo utilizar?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)

No Azure, o cluster de Redis é oferecido como um modelo de primário/réplica em que cada partição horizontal tem um par de primário/réplica com a replicação em que a replicação é gerida pelo Cache do Azure para o serviço de Redis. 

## <a name="clustering"></a>Clustering
Clustering é ativado no **nova Cache do Azure para Redis** painel durante a criação da cache. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Clustering é configurado no **Cluster de Redis** painel.

![Clustering][redis-cache-clustering]

Pode ter até 10 shards no cluster. Clique em **Enabled** e deslize o controlo de deslize ou escreva um número entre 1 e 10 para **contagem de Shards** e clique em **OK**.

Cada partição horizontal é um par de cache primário/réplica gerenciado pelo Azure e o tamanho total da cache é calculado ao multiplicar o número de partições horizontais pelo tamanho da cache selecionado no escalão de preço. 

![Clustering][redis-cache-clustering-selected]

Depois de criada a cache se conectar a ela e utilize-lo apenas como uma cache não-clusterizados e Redis distribui os dados em toda as partições horizontais de Cache. Se for de diagnóstico [ativada](cache-how-to-monitor.md#enable-cache-diagnostics), as métricas são capturadas em separado para cada partição horizontal e pode ser [visualizado](cache-how-to-monitor.md) na Cache do Azure para o painel de Redis. 

> [!NOTE]
> 
> Existem algumas pequenas diferenças na aplicação de cliente é necessárias quando o cluster está configurado. Para obter mais informações, consulte [é necessário efetuar quaisquer alterações à minha aplicação de cliente para utilizar o clustering?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 

Para o código de exemplo sobre como trabalhar com o clustering com o cliente stackexchange. redis, consulte a [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) parte a [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) exemplo.

<a name="cluster-size"></a>

## <a name="change-the-cluster-size-on-a-running-premium-cache"></a>Alterar o tamanho do cluster numa execução premium cache
Para alterar o tamanho de cluster numa execução cache premium com clustering ativado, clique em **tamanho do Cluster Redis** partir a **menu recursos**.

> [!NOTE]
> Enquanto a Cache do Azure para o escalão Premium de Redis foi lançada para disponibilidade geral, a funcionalidade de tamanho de Cluster de Redis está atualmente em pré-visualização.
> 
> 

![Tamanho do cluster redis][redis-cache-redis-cluster-size]

Para alterar o tamanho do cluster, utilize o controlo de deslize ou insira um número entre 1 e 10 a **contagem de partições horizontais** caixa de texto e clique em **OK** para guardar.

Aumentar o tamanho de cluster aumenta o débito máximo e o tamanho da cache. Aumentar o tamanho de cluster não aumenta o máximo. ligações disponíveis para clientes.

> [!NOTE]
> Dimensionar um cluster executa o [MIGRAR](https://redis.io/commands/migrate) comando, que é um comando Caro, então, para um impacto mínimo, considere executar esta operação durante o horário de pico. Durante o processo de migração, verá um pico de carga do servidor. Dimensionar um cluster é uma longa execução de processo e a quantidade de tempo que demora depende o número de chaves e o tamanho dos valores associados a essas chaves.
> 
> 

## <a name="clustering-faq"></a>FAQ sobre clustering
A lista seguinte contém respostas para perguntas freqüentes sobre a Cache do Azure para o clustering de Redis.

* [É necessário efetuar quaisquer alterações à minha aplicação de cliente para utilizar o clustering?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
* [Como as chaves são distribuídas num cluster?](#how-are-keys-distributed-in-a-cluster)
* [O que é o maior tamanho de cache, que posso criar?](#what-is-the-largest-cache-size-i-can-create)
* [Todos os clientes da Redis suportam clustering?](#do-all-redis-clients-support-clustering)
* [Como posso ligar a minha cache quando o cluster está ativado?](#how-do-i-connect-to-my-cache-when-clustering-is-enabled)
* [Diretamente ligar para as partições horizontais individuais da minha cache?](#can-i-directly-connect-to-the-individual-shards-of-my-cache)
* [Posso configurar o clustering para uma cache criada anteriormente?](#can-i-configure-clustering-for-a-previously-created-cache)
* [Posso configurar o clustering para uma cache básica ou padrão?](#can-i-configure-clustering-for-a-basic-or-standard-cache)
* [Posso utilizar o clustering com os fornecedores de estado de sessão do ASP.NET de Redis e a cache de saída?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)
* [Estou a receber exceções de movimentação ao utilizar o stackexchange. redis e clustering, o que devo fazer?](#i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do)

### <a name="do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering"></a>É necessário efetuar quaisquer alterações à minha aplicação de cliente para utilizar o clustering?
* Quando o clustering é ativado, apenas base de dados 0 está disponível. Se a aplicação cliente utiliza várias bases de dados e tentar ler ou escrever para uma base de dados diferente de 0, a seguinte exceção é lançada. `Unhandled Exception: StackExchange.Redis.RedisConnectionException: ProtocolFailure on GET --->` `StackExchange.Redis.RedisCommandException: Multiple databases are not supported on this server; cannot switch to database: 6`
  
  Para obter mais informações, consulte [especificação do Cluster Redis - subconjunto implementada](https://redis.io/topics/cluster-spec#implemented-subset).
* Se estiver a utilizar [stackexchange. redis](https://www.nuget.org/packages/StackExchange.Redis/), tem de utilizar 1.0.481 ou posterior. Ligar à cache com o mesmo [pontos de extremidade, portas e as chaves](cache-configure.md#properties) que utilizar ao ligar a uma cache não tem o clustering ativado. A única diferença é que todas as leituras e gravações devem ser feitas para a base de dados 0.
  
  * Outros clientes podem ter requisitos diferentes. Consulte [fazerem todos os clientes de Redis suportar clustering?](#do-all-redis-clients-support-clustering)
* Se o aplicativo usar várias operações-chave em lote num único comando, todas as chaves têm de estar localizadas na mesma partição horizontal. Para localizar as chaves na mesma partição horizontal, consulte [como as chaves são distribuídas num cluster?](#how-are-keys-distributed-in-a-cluster)
* Se estiver a utilizar o fornecedor de estado de sessão do ASP.NET de Redis tem de utilizar 2.0.1 ou superior. Consulte [posso utilizar o clustering com os fornecedores de estado de sessão do ASP.NET de Redis e a cache de saída?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)

### <a name="how-are-keys-distributed-in-a-cluster"></a>Como as chaves são distribuídas num cluster?
Pelo Redis [modelo de distribuição de chaves](https://redis.io/topics/cluster-spec#keys-distribution-model) documentação: O espaço de chave é dividido em blocos de 16384. Cada chave é protegido por hash e atribuído a uma destas entradas sem incorrer, que são distribuídas por nós do cluster. Pode configurar que parte da chave é protegido por hash para garantir que as várias chaves estão localizadas na mesma partição horizontal com etiquetas de hash.

* As chaves com uma marca de hash - se a qualquer parte da chave está entre `{` e `}`, apenas essa parte da chave é protegido por hash para efeitos de determinar a ranhura de hash de uma chave. Por exemplo, as seguintes chaves de 3 seriam estar localizadas na mesma partição horizontal: `{key}1`, `{key}2`, e `{key}3` uma vez que apenas o `key` parte do nome é protegido por hash. Para obter uma lista completa das especificações de marca de hash de chaves, consulte [chaves de hash etiquetas](https://redis.io/topics/cluster-spec#keys-hash-tags).
* Chaves sem uma marca de hash - o nome completo da chave é utilizada para codificar. Isso resulta numa ponto de vista estatístico igualar distribuição entre as partições horizontais da cache.

Para obter melhor desempenho e débito, recomendamos que distribuir as chaves de forma uniforme. Se estiver a utilizar chaves com uma marca de hash é responsabilidade do aplicativo para garantir que as chaves são distribuídas uniformemente.

Para obter mais informações, consulte [modelo de distribuição de chaves](https://redis.io/topics/cluster-spec#keys-distribution-model), [fragmentação de dados do Cluster de Redis](https://redis.io/topics/cluster-tutorial#redis-cluster-data-sharding), e [chaves de hash etiquetas](https://redis.io/topics/cluster-spec#keys-hash-tags).

Para o código de exemplo sobre como trabalhar com o clustering e localizar as chaves na mesma partição horizontal com o cliente stackexchange. redis, consulte a [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) parte a [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) exemplo.

### <a name="what-is-the-largest-cache-size-i-can-create"></a>O que é o maior tamanho de cache, que posso criar?
O maior tamanho de cache premium é 53 GB. Pode criar até 10 shards, dando-lhe um tamanho máximo de 530 GB. Se precisar de um tamanho maior, pode [pedir mais](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase). Para obter mais informações, consulte [do Azure na Cache de Redis preços](https://azure.microsoft.com/pricing/details/cache/).

### <a name="do-all-redis-clients-support-clustering"></a>Todos os clientes da Redis suportam clustering?
Atualmente, não todo o suporte de clientes Redis clustering. Stackexchange. redis é aquele que suporte a ele. Para obter mais informações sobre outros clientes, consulte a [Brincando com o cluster](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster) secção a [tutorial do cluster Redis](https://redis.io/topics/cluster-tutorial). 

O protocolo de clustering de Redis requer que cada cliente ligar a cada partição horizontal diretamente no modo de clustering. A tentar utilizar um cliente que não suporta o clustering deverá resultar em muita [foram MOVIDOS redirecionamento exceções](https://redis.io/topics/cluster-spec#moved-redirection).

> [!NOTE]
> Se estiver a utilizar o stackexchange. redis como seu cliente, certifique-se de que está a utilizar a versão mais recente do [stackexchange. redis](https://www.nuget.org/packages/StackExchange.Redis/) 1.0.481 ou posterior para o clustering para funcionar corretamente. Se tiver quaisquer problemas com exceções de movimentação, veja [mover exceções](#move-exceptions) para obter mais informações.
> 
> 

### <a name="how-do-i-connect-to-my-cache-when-clustering-is-enabled"></a>Como posso ligar a minha cache quando o cluster está ativado?
Pode ligar a sua cache com o mesmo [pontos de extremidade](cache-configure.md#properties), [portas](cache-configure.md#properties), e [chaves](cache-configure.md#access-keys) que utilizar ao ligar a uma cache não tem o clustering ativado. Redis gere o clustering back-end, para que não tenha para geri-la a partir do seu cliente.

### <a name="can-i-directly-connect-to-the-individual-shards-of-my-cache"></a>Diretamente ligar para as partições horizontais individuais da minha cache?
O protocolo de clustering exige que o cliente tornar as ligações de partição horizontal correto. Portanto, o cliente deve fazer corretamente por si. Dito isso, cada partição horizontal é composta por um par de cache primário/réplica, coletivamente conhecido como uma instância de cache. Pode ligar a estas instâncias de cache usando o utilitário de cli de redis no [instável](https://redis.io/download) ramo do repositório de Redis no GitHub. Esta versão implementa quando iniciado com o suporte básico a `-c` mudar. Para obter mais informações, consulte [Brincando com o cluster](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster) nos [ https://redis.io ](https://redis.io) no [tutorial do cluster Redis](https://redis.io/topics/cluster-tutorial).

Para não ssl, utilize os seguintes comandos.

    Redis-cli.exe –h <<cachename>> -p 13000 (to connect to instance 0)
    Redis-cli.exe –h <<cachename>> -p 13001 (to connect to instance 1)
    Redis-cli.exe –h <<cachename>> -p 13002 (to connect to instance 2)
    ...
    Redis-cli.exe –h <<cachename>> -p 1300N (to connect to instance N)

Para ssl, substitua `1300N` com `1500N`.

### <a name="can-i-configure-clustering-for-a-previously-created-cache"></a>Posso configurar o clustering para uma cache criada anteriormente?
Atualmente só pode ativar o clustering quando cria uma cache. Pode alterar o tamanho do cluster depois da cache é criada, mas não é possível adicionar o clustering para uma cache premium ou remover o clustering de uma cache premium depois de criar a cache. Uma cache premium com clustering ativado e apenas uma partição é diferente de uma cache premium do mesmo tamanho com sem clustering.

### <a name="can-i-configure-clustering-for-a-basic-or-standard-cache"></a>Posso configurar o clustering para uma cache básica ou padrão?
Clustering só está disponível para as premium caches.

### <a name="can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers"></a>Posso utilizar o clustering com os fornecedores de estado de sessão do ASP.NET de Redis e a cache de saída?
* **Fornecedor de Cache de saída redis** -sem necessidade de alterações.
* **Fornecedor de estado de sessão de redis** - para utilizar o clustering, tem de utilizar [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 ou superior, ou uma exceção é lançada. Esta é uma alteração de última hora; Para obter mais informações, consulte [v2.0.0 detalhes de alteração de última hora](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details).

<a name="move-exceptions"></a>

### <a name="i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do"></a>Estou a receber exceções de movimentação ao utilizar o stackexchange. redis e clustering, o que devo fazer?
Se estiver a utilizar o stackexchange. redis e receber `MOVE` exceções quando utilizar o clustering, certifique-se de que está a utilizar [stackexchange. redis 1.1.603](https://www.nuget.org/packages/StackExchange.Redis/) ou posterior. Para obter instruções sobre como configurar as suas aplicações de .NET para utilizar stackexchange. redis, veja [configurar os clientes de cache](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

## <a name="next-steps"></a>Passos Seguintes
Saiba como utilizar as funcionalidades de cache premium mais.

* [Introdução à Cache do Azure para o escalão Premium da Redis](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-clustering]: ./media/cache-how-to-premium-clustering/redis-cache-clustering.png

[redis-cache-clustering-selected]: ./media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png

[redis-cache-redis-cluster-size]: ./media/cache-how-to-premium-clustering/redis-cache-redis-cluster-size.png







