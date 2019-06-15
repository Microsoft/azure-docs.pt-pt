---
title: A Cache do Azure para exemplos de Redis | Documentos da Microsoft
description: Aprenda a utilizar a Cache do Azure para Redis
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 1f8d210c-ee09-4fe2-b63f-1e69246a27d8
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: multiple
ms.topic: article
ms.date: 01/23/2017
ms.author: yegu
ms.openlocfilehash: 73c771ab18d1cc2944298818c1cab90eb2f277ff
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60829808"
---
# <a name="azure-cache-for-redis-samples"></a>Exemplos da Cache do Azure para Redis
Este tópico fornece uma lista de Cache do Azure para exemplos de Redis, que abrange cenários, como ligar a uma cache, leitura e escrita de dados para e de uma cache e utilizar a Cache do ASP.NET do Azure para fornecedores de Redis. Alguns dos exemplos são projetos para download, e alguns fornecem orientações passo a passo e incluem fragmentos de código, mas não associar a um projeto que pode ser baixado.

## <a name="hello-world-samples"></a>Exemplos do Hello world
Os exemplos nesta secção mostram as noções básicas de ligar a uma Cache do Azure para a instância de Redis e ler e escrever dados na cache utilizando uma variedade de linguagens e clientes da Redis.

O [Olá, mundo](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) exemplo mostra como realizar várias operações de cache usando o [stackexchange. redis](https://github.com/StackExchange/StackExchange.Redis) cliente .NET.

Este exemplo mostra como:

* Utilizar várias opções de ligação
* Ler e escrever objetos de e para a cache a utilizar operações síncronas e assíncronas
* Utilizar comandos da Redis MGET/MSET para valores de retorno de chaves especificadas
* Efetuar operações transacionais de Redis
* Apresenta uma lista de trabalho com o Redis e conjuntos ordenados
* Objetos de .NET de Store usando JsonConvert Serializadores
* Utilizar conjuntos de Redis para implementar a marcação
* Trabalhar com o Cluster de Redis

Para obter mais informações, consulte a [stackexchange. redis](https://github.com/StackExchange/StackExchange.Redis) documentação no GitHub e para obter mais cenários de utilização Consulte a [StackExchange.Redis.Tests](https://github.com/StackExchange/StackExchange.Redis/tree/master/tests) testes de unidade.

[Como utilizar o Azure Cache de Redis com Python](cache-python-get-started.md) mostra como começar com Azure Cache de Redis com Python e o [redis-py](https://github.com/andymccurdy/redis-py) cliente.

[Trabalhar com objetos .NET na cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache) mostra-lhe uma forma para serializar objetos .NET, para que possa escrevê-las em e lê-los a partir de uma Cache do Azure para a instância de Redis. 

## <a name="use-azure-cache-for-redis-as-a-scale-out-backplane-for-aspnet-signalr"></a>Utilizar a Cache do Azure para Redis como uma ampliação Backplane para ASP.NET SignalR
O [utilizar a Cache do Azure para Redis como uma ampliação Backplane para ASP.NET SignalR](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane) exemplo demonstra como pode usar o Cache do Azure para Redis como uma backplane de SignalR. Para obter mais informações sobre backplane, consulte [aumento horizontal do SignalR com Redis](https://www.asp.net/signalr/overview/performance/scaleout-with-redis).

## <a name="azure-cache-for-redis-customer-query-sample"></a>Cache do Azure para o exemplo de consulta do cliente de Redis
Este exemplo demonstra o desempenho de compara entre Acessando dados através de uma cache e aceder aos dados do armazenamento de persistência. Este exemplo tem dois projetos.

* [Como a Cache de Redis do Azure pode melhorar o desempenho ao armazenar em cache de dados de demonstração](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
* [Efetuar o seeding do banco de dados e Cache para a demonstração](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## <a name="aspnet-session-state-and-output-caching"></a>Estado da sessão ASP.NET e o cache de saída
O [utilizar a Cache do Azure para Redis armazenar SessionState do ASP.NET e OutputCache](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching) exemplo demonstra como utilizar a Cache de Redis do Azure para armazenar a sessão do ASP.NET e Cache de saída com os fornecedores SessionState e OutputCache para Redis .

## <a name="manage-azure-cache-for-redis-with-maml"></a>Gerir a Cache do Azure para Redis com MAML
O [gerir a Cache do Azure para utilizar bibliotecas de gestão do Azure de Redis](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) exemplo demonstra como pode utilizar bibliotecas de gestão do Azure para gerir - (criar / atualizar / eliminar) a Cache. 

## <a name="custom-monitoring-sample"></a>Exemplo de monitorização de personalizado
O [acesso a Cache do Azure para dados de monitorização de Redis](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) exemplo demonstra como pode acessar dados de monitorização para a sua Cache do Azure para Redis fora do Portal do Azure.

## <a name="a-twitter-style-clone-written-using-php-and-redis"></a>Um clone de estilo do Twitter escrito usando o PHP e de Redis
O [Retwis](https://github.com/SyntaxC4-MSFT/retwis) exemplo é o Redis Hello World. É um clone de rede social mínimo do estilo do Twitter escrito usando o Redis e PHP com o [Predis](https://github.com/nrk/predis) cliente. O código-fonte foi concebido para ser muito simples e ao mesmo tempo para mostrar diferentes estruturas de dados de Redis.

## <a name="bandwidth-monitor"></a>Monitor de largura de banda
O [monitor de largura de banda](https://github.com/JonCole/SampleCode/tree/master/BandWidthMonitor) exemplo permite-lhe monitorizar a largura de banda utilizada no cliente. Para medir a largura de banda, execute o exemplo na máquina de cliente de cache, fazer chamadas para a cache e observe a largura de banda comunicada pelo exemplo de monitor de largura de banda.

