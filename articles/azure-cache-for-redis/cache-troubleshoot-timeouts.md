---
title: Resolver problemas de limites de tempo da Cache do Azure para Redis
description: Aprenda a resolver problemas comuns de tempo limite com Azure Cache para Redis, como patching de servidor redis e exceções de tempo de StackExchange.Redis.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 10/18/2019
ms.openlocfilehash: bf8b20dadd2fcd78657aa6877e796b645332dd94
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88213450"
---
# <a name="troubleshoot-azure-cache-for-redis-timeouts"></a>Resolver problemas de limites de tempo da Cache do Azure para Redis

Esta secção discute problemas de resolução de tempo que ocorrem quando se ligam a Azure Cache para Redis.

- [Patching do servidor Redis](#redis-server-patching)
- [StackExchange.Redis exceções no tempo limite](#stackexchangeredis-timeout-exceptions)

> [!NOTE]
> Várias das etapas de resolução de problemas neste guia incluem instruções para executar comandos Redis e monitorizar várias métricas de desempenho. Para obter mais informações e instruções, consulte os artigos na secção [informações adicionais.](#additional-information)
>

## <a name="redis-server-patching"></a>Patching do servidor Redis

O Azure Cache para Redis atualiza regularmente o seu software de servidor como parte da funcionalidade de serviço gerida que fornece. Esta atividade [de remendação](cache-failover.md) ocorre em grande parte atrás da cena. Durante as falhas quando os nós do servidor Redis estão a ser remendados, os clientes Redis ligados a estes nós podem experimentar intervalos temporários à medida que as ligações são trocadas entre estes nós. Veja [como é que uma falha afeta a minha aplicação de cliente](cache-failover.md#how-does-a-failover-affect-my-client-application) para obter mais informações sobre os efeitos secundários que a correção pode ter na sua aplicação e como pode melhorar o seu manuseamento de eventos de remendação.

## <a name="stackexchangeredis-timeout-exceptions"></a>StackExchange.Redis exceções no tempo limite

StackExchange.Redis utiliza uma definição de configuração nomeada `synctimeout` para operações sincronizadas com um valor padrão de 5000 ms. Se uma chamada sincronizada não for concluída neste tempo, o cliente StackExchange.Redis lança um erro de tempo limite semelhante ao seguinte exemplo:

```output
    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)
```

Esta mensagem de erro contém métricas que podem ajudar a indicar-lhe a causa e possível resolução do problema. A tabela seguinte contém detalhes sobre as métricas da mensagem de erro.

| Métrica da mensagem de erro | Detalhes |
| --- | --- |
| inst |Na última fatia: 0 comandos foram emitidos |
| mgr |O gestor da tomada está a `socket.select` fazer, o que significa que está a pedir ao SO que indique uma tomada que tem algo a fazer. O leitor não está a ler ativamente da rede porque acha que não há nada a fazer. |
| fila |Existem 73 operações totais em curso |
| qu |6 das operações em curso estão na fila não solicitada e ainda não foram escritas para a rede de saída |
| qs |67 das operações em curso foram enviadas para o servidor, mas ainda não há uma resposta disponível. A resposta pode ser `Not yet sent by the server` ou `sent by the server but not yet processed by the client.` |
| qc |0 das operações em curso viram respostas, mas ainda não foram marcadas como completas porque estão à espera do ciclo de conclusão |
| wr |Há um escritor ativo (o que significa que os 6 pedidos não solicitados não estão a ser ignorados) bytes/activewriters |
| para dentro |Não há leitores ativos e zero bytes estão disponíveis para serem lidos nos bytes/activereaders do NIC |

Pode utilizar os seguintes passos para investigar possíveis causas de raiz.

1. Como uma boa prática, certifique-se de que está a usar o seguinte padrão para se conectar ao utilizar o cliente StackExchange.Redis.

    ```csharp
    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

    });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }
    ```

    Para obter mais informações, consulte [Connect to the cache usando StackExchange.Redis](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).

1. Certifique-se de que o seu servidor e a aplicação do cliente estão na mesma região em Azure. Por exemplo, pode estar a receber intervalos quando o seu cache está no Leste dos EUA, mas o cliente está no Oeste dos EUA e o pedido não está completo dentro do `synctimeout` intervalo ou pode estar a receber intervalos de tempo quando estiver a depurar da sua máquina de desenvolvimento local. 

    É altamente recomendado ter a cache e no cliente na mesma região de Azure. Se tiver um cenário que inclua chamadas de região cruzada, deve definir o `synctimeout` intervalo para um valor superior ao intervalo padrão de 5000 ms, incluindo uma propriedade na cadeia de `synctimeout` ligação. O exemplo a seguir mostra um corte de uma cadeia de conexão para StackExchange.Redis fornecido por Azure Cache para Redis com um `synctimeout` de 2000 ms.

    ```output
    synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
    ```

1. Certifique-se de que utiliza a versão mais recente do [pacote StackExchange.Redis NuGet](https://www.nuget.org/packages/StackExchange.Redis/). Existem bugs constantemente a ser corrigidos no código para torná-lo mais robusto para os intervalos de tempo, pelo que ter a versão mais recente é importante.
1. Se os seus pedidos estiverem ligados por limitações de largura de banda no servidor ou cliente, demora mais tempo a completar e pode causar intervalos de tempo. Para ver se o seu tempo limite é devido à largura de banda da rede no servidor, consulte a [limitação da largura de banda do lado do Servidor](cache-troubleshoot-server.md#server-side-bandwidth-limitation). Para ver se o seu tempo limite é devido à largura de banda da rede de clientes, consulte [a limitação da largura de banda do lado do Cliente](cache-troubleshoot-client.md#client-side-bandwidth-limitation).
1. Está a ser ligado ao CPU no servidor ou no cliente?

   - Verifique se está a ser vinculado pela CPU ao seu cliente. O CPU elevado pode fazer com que o pedido não seja processado dentro do `synctimeout` intervalo e causar um pedido de tempo. Mudar para um tamanho de cliente maior ou distribuir a carga pode ajudar a controlar este problema.
   - Verifique se está a ficar com CPU ligado ao servidor, monitorizando a [métrica](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)de desempenho da cache do CPU . Os pedidos que chegam enquanto o Redis está ligado ao CPU podem fazer com que esses pedidos se esfumem. Para resolver esta condição, pode distribuir a carga por vários fragmentos numa cache premium, ou fazer upgrade para um tamanho maior ou nível de preços. Para obter mais informações, consulte [a limitação da largura de banda do lado do Servidor](cache-troubleshoot-server.md#server-side-bandwidth-limitation).
1. Há comandos que demoram muito tempo a processar no servidor? Comandos de longa duração que estão a demorar muito tempo a processar no redis-servidor podem causar intervalos de tempo. Para obter mais informações sobre comandos de longa duração, consulte [comandos de longa duração](cache-troubleshoot-server.md#long-running-commands). Pode ligar-se à sua Cache Azure para a instância Redis utilizando o cliente redis-cli ou a [Consola Redis](cache-configure.md#redis-console). Em seguida, executar o comando [SLOWLOG](https://redis.io/commands/slowlog) para ver se há pedidos mais lentos do que o esperado. Redis Server e StackExchange.Redis são otimizados para muitos pedidos pequenos em vez de menos pedidos grandes. Dividir os seus dados em pedaços menores pode melhorar as coisas aqui.

    Para obter informações sobre a ligação ao ponto final TLS/SSL da sua cache utilizando redis-cli e atordoado, consulte o post de blog [Anunciando ASP.NET Session State Provider para o lançamento de pré-visualização de Redis](https://devblogs.microsoft.com/aspnet/announcing-asp-net-session-state-provider-for-redis-preview-release/).
1. A alta carga do servidor Redis pode causar intervalos de tempo. Pode monitorizar a carga do servidor monitorizando a métrica de desempenho da `Redis Server Load` [cache](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). Uma carga de servidor de 100 (valor máximo) significa que o servidor redis tem estado ocupado, sem tempo de marcha lenta, solicitações de processamento. Para ver se determinados pedidos estão a ocupar toda a capacidade do servidor, execute o comando SlowLog, conforme descrito no parágrafo anterior. Para obter mais informações, consulte o uso do CPU alto / Carga do servidor.
1. Houve algum outro evento do lado do cliente que possa ter causado um blip na rede? Os eventos comuns incluem: aumentar o número de casos de cliente para cima ou para baixo, implementar uma nova versão do cliente, ou autoescala ativada. Nos nossos testes, descobrimos que a escala automática ou a escala para cima/para baixo podem fazer com que a conectividade da rede de saída se perca durante vários segundos. O código StackExchange.Redis é resistente a tais eventos e reconecta-se. Ao reconectar-se, quaisquer pedidos na fila podem ser esgotados.
1. Houve um grande pedido anterior a vários pequenos pedidos para a cache que esgotaram o tempo? O parâmetro `qs` na mensagem de erro indica-lhe quantos pedidos foram enviados do cliente para o servidor, mas não processou uma resposta. Este valor pode continuar a crescer porque o StackExchange.Redis utiliza uma única ligação TCP e só consegue ler uma resposta de cada vez. Mesmo que a primeira operação tenha sido cronometrada, não impede que mais dados sejam enviados para ou a partir do servidor. Outros pedidos serão bloqueados até que o grande pedido esteja concluído e pode causar intervalos de tempo. Uma solução é minimizar a possibilidade de intervalos, garantindo que o seu cache é suficientemente grande para a sua carga de trabalho e dividindo grandes valores em pedaços menores. Outra solução possível é utilizar uma piscina de `ConnectionMultiplexer` objetos no seu cliente e escolher o menos carregado `ConnectionMultiplexer` ao enviar um novo pedido. O carregamento através de vários objetos de ligação deve evitar que um único intervalo de tempo cause outros pedidos também para esgotar.
1. Se estiver a `RedisSessionStateProvider` utilizar, certifique-se de que definiu corretamente o tempo de reesma. `retryTimeoutInMilliseconds` deve ser mais alto do `operationTimeoutInMilliseconds` que, caso contrário não ocorrem recaíções. No exemplo a seguir `retryTimeoutInMilliseconds` está definido para 3000. Para obter mais informações, consulte [ASP.NET Session State Provider for Azure Cache for Redis](cache-aspnet-session-state-provider.md) e Como utilizar os [parâmetros de configuração do Fornecedor de Estado de Sessão e do Fornecedor de Cache de Saída](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration).

    ```xml
    <add
      name="AFRedisCacheSessionStateProvider"
      type="Microsoft.Web.Redis.RedisSessionStateProvider"
      host="enbwcache.redis.cache.windows.net"
      port="6380"
      accessKey="…"
      ssl="true"
      databaseId="0"
      applicationName="AFRedisCacheSessionState"
      connectionTimeoutInMilliseconds = "5000"
      operationTimeoutInMilliseconds = "1000"
      retryTimeoutInMilliseconds="3000" />
    ```

1. Verifique a utilização da memória no cache Azure para o servidor Redis [através da monitorização](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `Used Memory RSS` e `Used Memory` . Se houver uma política de despejo, o Redis começa a despejar chaves quando `Used_Memory` atinge o tamanho da cache. Idealmente, `Used Memory RSS` deve ser apenas ligeiramente mais alto do que `Used memory` . Uma grande diferença significa que há fragmentação da memória (interna ou externa). Quando `Used Memory RSS` for inferior a , significa que parte da memória do cache foi `Used Memory` trocada pelo sistema operativo. Se esta troca ocorrer, pode esperar algumas latências significativas. Como o Redis não tem controlo sobre como as suas alocações são mapeadas para páginas de memória, alta `Used Memory RSS` é muitas vezes o resultado de um pico no uso da memória. Quando o servidor Redis liberta a memória, o alocador tira a memória, mas pode ou não devolver a memória ao sistema. Pode haver uma discrepância entre o valor e o `Used Memory` consumo de memória conforme relatado pelo sistema operativo. A memória pode ter sido usada e libertada por Redis, mas não é reemedificado ao sistema. Para ajudar a mitigar problemas de memória, pode fazer os seguintes passos:

   - Atualize a cache para um tamanho maior para que não esteja a correr contra as limitações de memória no sistema.
   - Desajei os tempos de validade nas teclas de modo a que os valores mais antigos sejam despejados proactivamente.
   - Monitorize a métrica da `used_memory_rss` cache. Quando este valor se aproxima do tamanho da cache, é provável que comece a ver problemas de desempenho. Distribua os dados através de vários fragmentos se estiver a usar uma cache premium, ou fazer upgrade para um tamanho de cache maior.

   Para obter mais informações, consulte [a pressão de memória no servidor Redis](cache-troubleshoot-server.md#memory-pressure-on-redis-server).

## <a name="additional-information"></a>Informações adicionais

- [Resolver problemas do lado do cliente da Cache do Azure para Redis](cache-troubleshoot-client.md)
- [Resolver problemas do lado do servidor da Cache do Azure para Redis](cache-troubleshoot-server.md)
- [Como posso comparar e testar o desempenho da minha cache?](cache-management-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [Como monitorizar a Cache do Azure para Redis](cache-how-to-monitor.md)
