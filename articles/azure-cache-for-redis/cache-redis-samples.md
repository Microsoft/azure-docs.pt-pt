---
title: Exemplos da Cache do Azure para Redis
description: 'Saiba como utilizar o Azure Cache para Redis com estas amostras de código: ligar a uma cache, ler e escrever dados numa cache, ASP.NET Azure Cache para fornecedores de Redis.'
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: devx-track-dotnet
ms.topic: sample
ms.date: 01/23/2017
ms.openlocfilehash: 553850173f463a05b13768eb3b9e17703bfa2886
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88212287"
---
# <a name="azure-cache-for-redis-samples"></a>Exemplos da Cache do Azure para Redis
Este tópico fornece uma lista de Cache Azure para amostras redis, cobrindo cenários como ligar a uma cache, ler e escrever dados de e para uma cache, e usar o ASP.NET Cache Azure para fornecedores de Redis. Algumas das amostras são projetos transferíveis, e algumas fornecem orientação passo a passo e incluem snippets de código, mas não se ligam a um projeto transferível.

## <a name="hello-world-samples"></a>Olá amostras do mundo
As amostras desta secção mostram o básico da ligação a uma Cache Azure para a instância Redis e ler e escrever dados para a cache usando uma variedade de idiomas e clientes Redis.

A amostra [do mundo Hello](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) mostra como realizar várias operações de cache utilizando o cliente [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) .NET.

Esta amostra mostra como:

* Use várias opções de conexão
* Leia e escreva objetos de e para a cache usando operações sincronizadas e assíncronos
* Utilize comandos Redis MGET/MSET para devolver valores das teclas especificadas
* Realizar operações transacionais da Redis
* Trabalhar com listas redis e conjuntos classificados
* Armazenar objetos .NET utilizando serializers JsonConvert
* Use conjuntos redis para implementar a marcação
* Trabalhar com o Redis Cluster

Para obter mais informações, consulte a documentação [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) no GitHub e para mais cenários de utilização consulte os testes da unidade [StackExchange.Redis.Tests.](https://github.com/StackExchange/StackExchange.Redis/tree/master/tests)

[Como usar a Cache Azure para Redis com Python](cache-python-get-started.md) mostra como começar com Azure Cache para Redis usando Python e o cliente [redis-py.](https://github.com/andymccurdy/redis-py)

[O trabalho com objetos .NET na cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache) mostra-lhe uma maneira de serializar objetos .NET para que possa escrevê-los e lê-los a partir de uma cache Azure para a instância Redis. 

## <a name="use-azure-cache-for-redis-as-a-scale-out-backplane-for-aspnet-signalr"></a>Use a cache Azure para redis como um backplane de escala para ASP.NET SignalR
A [cache de Azure use para redis como um backplane de escala para ASP.NET](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane) amostra signalR demonstra como você pode usar Azure Cache para Redis como um backplane SignalR. Para obter mais informações sobre o backplane, consulte [SignalR Scaleout com Redis](https://www.asp.net/signalr/overview/performance/scaleout-with-redis).

## <a name="azure-cache-for-redis-customer-query-sample"></a>Cache Azure para amostra de consulta de clientes Redis
Esta amostra demonstra comparar o desempenho entre aceder a dados a partir de uma cache e aceder a dados do armazenamento de persistência. Esta amostra tem dois projetos.

* [Demo como Azure Cache para Redis pode melhorar o desempenho através de caching dados](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
* [Semeia a Base de Dados e Cache para a demonstração](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## <a name="aspnet-session-state-and-output-caching"></a>ASP.NET Estado da sessão e caching de saída
A [cache de Azure use para redis para armazenar ASP.NET sessãoState e a amostra OutputCache](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching) demonstra como você usa Azure Cache para Redis para armazenar ASP.NET Sessão e Cache de saída usando os fornecedores SessionState e OutputCache para Redis.

## <a name="manage-azure-cache-for-redis-with-maml"></a>Gerir a cache Azure para Redis com MAML
A [cache de gestão de Azure para redis utilizando a amostra de Bibliotecas de Gestão Azure](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) demonstra como pode utilizar as Bibliotecas de Gestão Azure para gerir - (Criar/ Atualizar/ apagar) a sua Cache. 

## <a name="custom-monitoring-sample"></a>Amostra de monitorização personalizada
A [amostra de dados access Azure Monitoring](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) demonstra como pode aceder aos dados de monitorização da sua Cache Azure para Redis fora do Portal Azure.

## <a name="a-twitter-style-clone-written-using-php-and-redis"></a>Um clone ao estilo do Twitter escrito usando PHP e Redis
A amostra [de Retwis](https://github.com/SyntaxC4-MSFT/retwis) é o Redis Hello World. É um clone de rede social de estilo twitter mínimo escrito usando Redis e PHP usando o cliente [Predis.](https://github.com/nrk/predis) O código fonte foi concebido para ser muito simples e, ao mesmo tempo, para mostrar diferentes estruturas de dados redis.

## <a name="bandwidth-monitor"></a>Monitor de largura de banda
A amostra [do monitor de largura de](https://github.com/JonCole/SampleCode/tree/master/BandWidthMonitor) banda permite-lhe monitorizar a largura de banda utilizada no cliente. Para medir a largura de banda, faça a amostra na máquina do cliente cache, faça chamadas para a cache e observe a largura de banda relatada pela amostra do monitor de largura de banda.
