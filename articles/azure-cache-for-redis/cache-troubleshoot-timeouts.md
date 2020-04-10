---
title: Resolver problemas de limites de tempo da Cache do Azure para Redis
description: Saiba como resolver problemas de tempo comum com o Azure Cache para Redis, tais como patching de servidor redis e exceções de tempo de timeout StackExchange.Redis.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.openlocfilehash: 4301a55e3f5ea5b445ef1540ee59d1b5c28ca0ed
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010822"
---
# <a name="troubleshoot-azure-cache-for-redis-timeouts"></a>Resolver problemas de limites de tempo da Cache do Azure para Redis

Esta secção discute problemas de resolução de tempo de resolução de problemas que ocorrem quando se conecta a Azure Cache for Redis.

- [Remendar servidor Redis](#redis-server-patching)
- [StackExchange.Redis exceções ao intervalo](#stackexchangeredis-timeout-exceptions)

> [!NOTE]
> Várias das etapas de resolução de problemas neste guia incluem instruções para executar comandos Redis e monitorizar várias métricas de desempenho. Para mais informações e instruções, consulte os artigos na secção [informação adicional.](#additional-information)
>

## <a name="redis-server-patching"></a>Remendar servidor Redis

O Azure Cache para redis atualiza regularmente o seu software de servidor como parte da funcionalidade de serviço gerida que fornece. Esta atividade de [remendos](cache-failover.md) ocorre em grande parte por detrás do local. Durante as falhas quando os nós do servidor Redis estão a ser remendados, os clientes Redis ligados a estes nós podem experimentar intervalos temporários à medida que as ligações são trocadas entre estes nós. Veja [como uma falha afeta o meu pedido](cache-failover.md#how-does-a-failover-affect-my-client-application) de cliente para obter mais informações sobre quais os efeitos colaterais que a correção pode ter na sua aplicação e como pode melhorar o seu tratamento de eventos de remendos.

## <a name="stackexchangeredis-timeout-exceptions"></a>StackExchange.Redis exceções ao intervalo

StackExchange.Redis usa uma `synctimeout` definição de configuração nomeada para operações sincronizadas com um valor padrão de 1000 ms. Se uma chamada sincronizada não estiver concluída neste período, o cliente StackExchange.Redis lança um erro de tempo semelhante ao seguinte exemplo:

    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)

Esta mensagem de erro contém métricas que podem ajudar a apontá-lo para a causa e possível resolução do problema. A tabela seguinte contém detalhes sobre as métricas da mensagem de erro.

| Métrica da mensagem de erro | Detalhes |
| --- | --- |
| inst |Na última fatia: 0 comandos foram emitidos |
| mgr |O gerente da `socket.select`tomada está a fazer, o que significa que está a pedir ao SO que indique uma tomada que tenha algo a fazer. O leitor não está a ler ativamente da rede porque acha que não há nada a fazer. |
| fila |Há 73 operações totais em curso |
| qu |6 das operações em curso estão na fila não enviada e ainda não foram escritas para a rede de saída |
| qs |67 das operações em curso foram enviadas para o servidor, mas uma resposta ainda não está disponível. A resposta `Not yet sent by the server` pode ser ou`sent by the server but not yet processed by the client.` |
| qc |0 das operações em curso viram respostas, mas ainda não foram marcadas como completas porque estão à espera do ciclo de conclusão |
| wr |Há um escritor ativo (o que significa que os 6 pedidos não enviados não estão a ser ignorados) bytes/activewriters |
| in |Não existem leitores ativos e zero bytes estão disponíveis para serem lidos nos bytes/leitores ativos da NIC |

Pode usar os seguintes passos para investigar possíveis causas de raiz.

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

    Para mais informações, consulte [Connect to the cache utilizando StackExchange.Redis](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).

1. Certifique-se de que o seu servidor e a aplicação do cliente estão na mesma região do Azure. Por exemplo, você pode estar recebendo intervalos quando o seu cache está no Leste dos `synctimeout` EUA, mas o cliente está no Oeste dos EUA e o pedido não completa dentro do intervalo ou você pode estar recebendo intervalos quando você está depurando da sua máquina de desenvolvimento local. 

    É altamente recomendado ter a cache e no cliente na mesma região de Azure. Se tiver um cenário que inclua chamadas `synctimeout` de região cruzada, deve definir o intervalo para um `synctimeout` valor superior ao intervalo padrão de 1000 ms, incluindo uma propriedade na cadeia de ligação. O exemplo seguinte mostra um corte de uma corda de ligação para StackExchange.Redis fornecida por Azure Cache para Redis com uma `synctimeout` de 2000 ms.

        synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
1. Certifique-se de que utiliza a versão mais recente do [pacote StackExchange.Redis NuGet](https://www.nuget.org/packages/StackExchange.Redis/). Existem bugs constantemente a ser corrigidos no código para torná-lo mais robusto para os intervalos de tempo, pelo que ter a versão mais recente é importante.
1. Se os seus pedidos estiverem vinculados por limitações de largura de banda no servidor ou cliente, demora mais tempo a completar e pode causar intervalos. Para ver se o seu tempo limite é devido à largura de banda da rede no servidor, consulte a [limitação da largura de banda do lado do Servidor](cache-troubleshoot-server.md#server-side-bandwidth-limitation). Para ver se o seu tempo limite é devido à largura de banda da rede do cliente, consulte a [limitação da largura de banda do lado do Cliente](cache-troubleshoot-client.md#client-side-bandwidth-limitation).
1. Está saturado no servidor ou no cliente?

   - Verifique se está a ser vinculado pela CPU ao seu cliente. A Alta CPU pode fazer com que `synctimeout` o pedido não seja processado dentro do intervalo e causar um pedido de tempo. Mudar para um tamanho maior do cliente ou distribuir a carga pode ajudar a controlar este problema.
   - Verifique se está a ficar com cpU ligado ao servidor, monitorizando a métrica de desempenho da [cache](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)CPU . Os pedidos que chegam enquanto o Redis está vinculado à CPU podem fazer com que esses pedidos se estem. Para resolver esta condição, pode distribuir a carga por vários fragmentos numa cache premium, ou fazer upgrade para um tamanho maior ou um nível de preços. Para obter mais informações, consulte a [limitação da largura de banda do lado do Servidor](cache-troubleshoot-server.md#server-side-bandwidth-limitation).
1. Há comandos que levam muito tempo a processar o servidor? Comandos de longa duração que estão a demorar muito tempo a processar no servidor redis podem causar intervalos. Para obter mais informações sobre comandos de longa duração, consulte [comandos de longa duração](cache-troubleshoot-server.md#long-running-commands). Pode ligar-se ao seu Azure Cache para a instância Redis utilizando o cliente redis-cli ou a [Consola Redis](cache-configure.md#redis-console). Em seguida, executar o comando [SLOWLOG](https://redis.io/commands/slowlog) para ver se há pedidos mais lentos do que o esperado. Redis Server e StackExchange.Redis estão otimizados para muitos pequenos pedidos em vez de menos grandes pedidos. Dividir os seus dados em pedaços menores pode melhorar as coisas aqui.

    Para obter informações sobre a ligação ao ponto final TLS/SSL da sua cache utilizando redis-cli e stunnel, consulte a publicação de blog anunciando ASP.NET Session State Provider para o lançamento de [pré-visualização redis](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).
1. A carga do servidor Redis alta pode causar intervalos. Pode monitorizar a carga do `Redis Server Load` servidor monitorizando a métrica de desempenho da [cache](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). Uma carga de servidor de 100 (valor máximo) significa que o servidor redis tem estado ocupado, sem tempo de inatividade, solicitações de processamento. Para ver se determinados pedidos estão a assumir toda a capacidade do servidor, execute o comando SlowLog, conforme descrito no parágrafo anterior. Para mais informações, consulte o uso de CPU elevado / Carga do Servidor.
1. Houve algum outro evento do lado do cliente que pudesse ter causado um blip de rede? Os eventos comuns incluem: escalar o número de casos de clientes para cima ou para baixo, implementar uma nova versão do cliente, ou escala automática ativada. Nos nossos testes, descobrimos que a escala automática ou a escala para cima/para baixo pode fazer com que a conectividade da rede de saída se perca durante vários segundos. StackExchange.Redis code é resiliente a tais eventos e reconecta-se. Durante a religação, quaisquer pedidos na fila podem esgotar-se.
1. Houve um grande pedido anterior a vários pequenos pedidos para a cache que tinha esgotado? O parâmetro `qs` na mensagem de erro diz-lhe quantos pedidos foram enviados do cliente para o servidor, mas não processou uma resposta. Este valor pode continuar a crescer porque stackExchange.Redis usa uma única ligação TCP e só consegue ler uma resposta de cada vez. Mesmo que a primeira operação tenha sido cronometrada, não impede que mais dados sejam enviados de ou para o servidor. Outros pedidos serão bloqueados até que o grande pedido esteja terminado e possam causar intervalos de tempo. Uma solução é minimizar a possibilidade de intervalos, garantindo que a sua cache é suficientemente grande para a sua carga de trabalho e dividindo grandes valores em pedaços menores. Outra solução possível é `ConnectionMultiplexer` usar um conjunto de objetos no seu cliente, e escolher o menos carregado `ConnectionMultiplexer` ao enviar um novo pedido. Carregar vários objetos de ligação deve evitar que um único intervalo de tempo provoque que outros pedidos também se estem.
1. Se estiver a `RedisSessionStateProvider`utilizar, certifique-se de que definiu corretamente o tempo de reparação. `retryTimeoutInMilliseconds`deve ser `operationTimeoutInMilliseconds`maior do que, caso contrário não ocorrerem repetições. No exemplo `retryTimeoutInMilliseconds` seguinte é fixado para 3000. Para mais informações, consulte [ASP.NET Session State Provider for Azure Cache for Redis](cache-aspnet-session-state-provider.md) e Como utilizar os [parâmetros de configuração do Fornecedor de Estado de Session e fornecedor de cache](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration)de saída .

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

1. Verifique a utilização da memória no Azure `Used Memory`Cache para o servidor Redis através da [monitorização](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `Used Memory RSS` e . Se houver uma política de despejo, redis `Used_Memory` começa a despejar chaves quando atinge o tamanho da cache. Idealmente, `Used Memory RSS` deve ser apenas `Used memory`ligeiramente mais alto que . Uma grande diferença significa que há fragmentação da memória (interna ou externa). Quando `Used Memory RSS` é `Used Memory`inferior a, significa que parte da memória da cache foi trocada pelo sistema operativo. Se esta troca ocorrer, pode esperar algumas tardios significativas. Como o Redis não tem controlo sobre como as suas dotações são mapeadas para páginas de memória, o alto `Used Memory RSS` é muitas vezes o resultado de um pico no uso da memória. Quando o servidor Redis liberta a memória, o alocósero tira a memória, mas pode ou não devolver a memória ao sistema. Pode haver uma discrepância `Used Memory` entre o valor e o consumo de memória, conforme relatado pelo sistema operativo. A memória pode ter sido usada e libertada por Redis, mas não devolta ao sistema. Para ajudar a mitigar problemas de memória, pode fazer os seguintes passos:

   - Atualize a cache para um tamanho maior para que não esteja a correr contra limitações de memória no sistema.
   - Detete os prazos de validade nas teclas de modo a que os valores mais antigos sejam despejados proactivamente.
   - Monitorize `used_memory_rss` a métrica da cache. Quando este valor se aproxima do tamanho da sua cache, é provável que comece a ver problemas de desempenho. Distribua os dados por vários fragmentos se estiver a usar uma cache premium ou a atualizar para um tamanho de cache maior.

   Para mais informações, consulte [a pressão da memória no servidor Redis](cache-troubleshoot-server.md#memory-pressure-on-redis-server).

## <a name="additional-information"></a>Informações adicionais

- [Resolver problemas do lado do cliente da Cache do Azure para Redis](cache-troubleshoot-client.md)
- [Resolver problemas do lado do servidor da Cache do Azure para Redis](cache-troubleshoot-server.md)
- [Como posso comparar e testar o desempenho da minha cache?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [Como monitorizar o Azure Cache para redis](cache-how-to-monitor.md)
