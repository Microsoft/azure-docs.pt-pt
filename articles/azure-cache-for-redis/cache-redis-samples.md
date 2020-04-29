---
title: Exemplos da Cache do Azure para Redis
description: 'Aprenda a utilizar o Azure Cache para Redis com estas amostras de código: conectar-se a um cache, ler e escrever dados numa cache, ASP.NET Azure Cache para os fornecedores redis.'
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: sample
ms.date: 01/23/2017
ms.openlocfilehash: 8057a7db3d1a2db0e51ec15e4966ed0b31c84226
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "75433410"
---
# <a name="azure-cache-for-redis-samples"></a>Exemplos da Cache do Azure para Redis
Este tópico fornece uma lista de Azure Cache para amostras Redis, abrangendo cenários como a ligação a um cache, dados de leitura e escrita de e para uma cache, e usando o ASP.NET Azure Cache para os fornecedores Redis. Algumas das amostras são projetos transferíveis, e algumas fornecem orientação passo a passo e incluem códigos, mas não se ligam a um projeto transferível.

## <a name="hello-world-samples"></a>Olá amostras do mundo
As amostras desta secção mostram o básico de ligar-se a um Azure Cache para redis e ler e escrever dados à cache usando uma variedade de idiomas e clientes Redis.

A amostra [do mundo Hello](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) mostra como realizar várias operações de cache usando o cliente [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) .NET.

Esta amostra mostra como:

* Utilize várias opções de ligação
* Ler e escrever objetos de e para a cache usando operações sincronizadas e assíncronas
* Utilize comandos Redis MGET/MSET para devolver valores de teclas especificadas
* Realizar operações transacionais Redis
* Trabalhar com listas Redis e conjuntos classificados
* Armazenar objetos .NET utilizando serializadores JsonConvert
* Use conjuntos Redis para implementar a marcação
* Trabalhar com o Aglomerado Redis

Para mais informações, consulte a documentação [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) no GitHub e para mais cenários de utilização consulte os testes da unidade [StackExchange.Redis.Tests.](https://github.com/StackExchange/StackExchange.Redis/tree/master/tests)

[Como usar o Azure Cache para Redis com Python](cache-python-get-started.md) mostra como começar com o Azure Cache para redis usando Python e o cliente [redis-py.](https://github.com/andymccurdy/redis-py)

[Trabalhar com objetos .NET na cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache) mostra-lhe uma maneira de serializar objetos .NET para que possa escrevê-los e lê-los a partir de um Cache Azure para redis. 

## <a name="use-azure-cache-for-redis-as-a-scale-out-backplane-for-aspnet-signalr"></a>Use o Cache Azure para Redis como um backplane scale out para ASP.NET SignalR
O [Depósito Azure para Redis como um backplane scale out para ASP.NET](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane) a amostra SignalR demonstra como pode utilizar o Azure Cache para Redis como um backplane SignalR. Para mais informações sobre o backplane, consulte [SignalR Scaleout com Redis](https://www.asp.net/signalr/overview/performance/scaleout-with-redis).

## <a name="azure-cache-for-redis-customer-query-sample"></a>Azure Cache para amostra de consulta de cliente Redis
Esta amostra demonstra que compara o desempenho entre o acesso a dados a partir de uma cache e o acesso a dados a partir do armazenamento de persistência. Esta amostra tem dois projetos.

* [Demo como Azure Cache for Redis pode melhorar desempenho através de dados de Caching](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
* [Sementes a Base de Dados e Cache para a demonstração](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## <a name="aspnet-session-state-and-output-caching"></a>ASP.NET Sessão Estado e Caching de Saída
O [Depósito De Utilização Azure para redis para armazenar ASP.NET amostra SessionState e OutputCache](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching) demonstra como você usa O Cache Azure para Redis para armazenar ASP.NET Session e Output Cache utilizando os fornecedores SessionState e OutputCache para Redis.

## <a name="manage-azure-cache-for-redis-with-maml"></a>Gerir o Azure Cache para Redis com MAML
A cache [Manage Azure para Redis utilizando bibliotecas de gestão Azure](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) mostra como pode utilizar bibliotecas de gestão Azure para gerir - (Criar/ Atualizar/ eliminar) a sua cache. 

## <a name="custom-monitoring-sample"></a>Amostra de monitorização personalizada
A amostra de dados de Monitorização De [Acesso Azure Para Monitorização Redis](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) demonstra como pode aceder a dados de monitorização do seu Azure Cache for Redis fora do Portal Azure.

## <a name="a-twitter-style-clone-written-using-php-and-redis"></a>Um clone ao estilo do Twitter escrito usando PHP e Redis
A amostra [de Retwis](https://github.com/SyntaxC4-MSFT/retwis) é o Redis Hello World. É um clone de rede social ao estilo do Twitter escrito usando Redis e PHP usando o cliente [Predis.](https://github.com/nrk/predis) O código fonte foi concebido para ser muito simples e ao mesmo tempo para mostrar diferentes estruturas de dados redis.

## <a name="bandwidth-monitor"></a>Monitor de largura de banda
A amostra de [monitor de largura de banda](https://github.com/JonCole/SampleCode/tree/master/BandWidthMonitor) permite-lhe monitorizar a largura de banda utilizada no cliente. Para medir a largura de banda, faça a amostra na máquina do cliente cache, eprea chamadas para a cache e observe a largura de banda reportada pela amostra do monitor de largura de banda.
