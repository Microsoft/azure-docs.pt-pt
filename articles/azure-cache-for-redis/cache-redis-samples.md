---
title: Exemplos da Cache do Azure para Redis
description: 'Saiba como usar o cache do Azure para Redis com estes exemplos de código: conectando a um cache, lendo e gravando dados em um cache, ASP.NET do cache do Azure para provedores de Redis.'
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: sample
ms.date: 01/23/2017
ms.openlocfilehash: 8057a7db3d1a2db0e51ec15e4966ed0b31c84226
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75433410"
---
# <a name="azure-cache-for-redis-samples"></a>Exemplos da Cache do Azure para Redis
Este tópico fornece uma lista de exemplos do cache do Azure para Redis, abrangendo cenários como conectar-se a um cache, ler e gravar dados de e para um cache e usar o cache do Azure ASP.NET para provedores de Redis. Alguns dos exemplos são projetos que podem ser baixados e alguns fornecem orientações passo a passo e incluem trechos de código, mas não são vinculados a um projeto que pode ser baixado.

## <a name="hello-world-samples"></a>Exemplos do Hello World
Os exemplos nesta seção mostram as noções básicas de como se conectar a um cache do Azure para a instância Redis e ler e gravar dados no cache usando uma variedade de linguagens e clientes Redis.

O exemplo [Olá, mundo](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) mostra como executar várias operações de cache usando o cliente .NET [stackexchange. Redis](https://github.com/StackExchange/StackExchange.Redis) .

Este exemplo mostra como:

* Usar várias opções de conexão
* Ler e gravar objetos de e para o cache usando operações síncronas e assíncronas
* Usar comandos Redis MGET/MSET para retornar valores de chaves especificadas
* Executar operações transacionais Redis
* Trabalhar com listas de Redis e conjuntos classificados
* Armazenar objetos .NET usando serializadores JsonConvert
* Usar conjuntos de Redis para implementar a marcação
* Trabalhar com o cluster Redis

Para obter mais informações, consulte a documentação do [stackexchange. Redis](https://github.com/StackExchange/StackExchange.Redis) no GitHub e para obter mais cenários de uso, consulte os testes de unidade [stackexchange. Redis. Tests](https://github.com/StackExchange/StackExchange.Redis/tree/master/tests) .

[Como usar o cache do Azure para Redis com Python](cache-python-get-started.md) mostra como começar a usar o cache do Azure para Redis usando Python e o cliente [Redis-py](https://github.com/andymccurdy/redis-py) .

[Trabalhar com objetos .net no cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache) mostra uma maneira de serializar objetos .net para que você possa gravá-los e lê-los de um cache do Azure para instância Redis. 

## <a name="use-azure-cache-for-redis-as-a-scale-out-backplane-for-aspnet-signalr"></a>Usar o cache do Azure para Redis como um backplane de expansão para o Signalr ASP.NET
O exemplo de [uso do cache do Azure para Redis como um backplane de expansão para ASP.net signalr](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane) demonstra como você pode usar o cache do Azure para Redis como um backplane do signalr. Para obter mais informações sobre o backplane, consulte [signaler ScaleOut com Redis](https://www.asp.net/signalr/overview/performance/scaleout-with-redis).

## <a name="azure-cache-for-redis-customer-query-sample"></a>Cache do Azure para exemplo de consulta de cliente Redis
Este exemplo demonstra como comparar o desempenho entre o acesso a dados de um cache e o acesso a dados do armazenamento de persistência. Este exemplo tem dois projetos.

* [Demonstração de como o cache do Azure para Redis pode melhorar o desempenho armazenando dados em cache](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
* [Propagar o banco de dados e o cache para a demonstração](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## <a name="aspnet-session-state-and-output-caching"></a>Estado de sessão ASP.NET e cache de saída
O exemplo [usar o cache do Azure para Redis para armazenar o ASP.net SessionState e OutputCache](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching) demonstra como usar o cache do Azure para Redis para armazenar a sessão ASP.net e o cache de saída usando os provedores SessionState e OutputCache para Redis.

## <a name="manage-azure-cache-for-redis-with-maml"></a>Gerenciar o cache do Azure para Redis com MAML
O exemplo [gerenciar o cache do Azure para Redis usando bibliotecas de gerenciamento do Azure](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) demonstra como você pode usar as bibliotecas de gerenciamento do Azure para gerenciar (criar/atualizar/excluir) seu cache. 

## <a name="custom-monitoring-sample"></a>Exemplo de monitoramento personalizado
O exemplo de [dados de monitoramento do cache do Azure para Redis](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) demonstra como você pode acessar dados de monitoramento para o cache do Azure para Redis fora do portal do Azure.

## <a name="a-twitter-style-clone-written-using-php-and-redis"></a>Um clone no estilo do Twitter escrito usando PHP e Redis
O exemplo [Retwis](https://github.com/SyntaxC4-MSFT/retwis) é o Redis Olá, mundo. É um clone de rede social mínimo no estilo do Twitter escrito usando Redis e PHP usando o cliente [Predis](https://github.com/nrk/predis) . O código-fonte foi projetado para ser muito simples e ao mesmo tempo para mostrar diferentes estruturas de dados Redis.

## <a name="bandwidth-monitor"></a>Monitor de largura de banda
O exemplo de [Monitor de largura de banda](https://github.com/JonCole/SampleCode/tree/master/BandWidthMonitor) permite monitorar a largura de banda usada no cliente. Para medir a largura de banda, execute o exemplo no computador cliente de cache, faça chamadas para o cache e observe a largura de banda relatada pelo exemplo de monitor de largura de banda.
