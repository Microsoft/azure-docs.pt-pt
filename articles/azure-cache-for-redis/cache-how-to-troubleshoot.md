---
title: Como resolver problemas do Azure Cache de Redis | Documentos da Microsoft
description: Saiba como resolver problemas comuns com o Azure Cache de Redis.
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 928b9b9c-d64f-4252-884f-af7ba8309af6
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 03/15/2019
ms.author: yegu
ms.openlocfilehash: 66361871d365068a90a2eeab70d92adb6b246a83
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60830012"
---
# <a name="how-to-troubleshoot-azure-cache-for-redis"></a>Como resolver problemas de Cache do Azure para Redis

Este artigo ajuda a resolver problemas relacionados com diferentes categorias de problemas que pode ocorrer durante a conexão com a Cache do Azure para instâncias de Redis.

- [Resolução de problemas do lado do cliente](#client-side-troubleshooting) ajuda a identificar e resolver problemas na aplicação que liga à sua cache.
- [Resolução de problemas do lado do servidor](#server-side-troubleshooting) ajuda a identificar e resolver problemas que ocorrem na Cache do Azure para o lado de Redis.
- [Resolução de problemas de perda de dados](#data-loss-troubleshooting) ajuda a identificar e resolver incidentes, onde as chaves são esperadas mas não foi encontradas em seu cache.
- [Exceções de tempo limite do stackexchange. redis](#stackexchangeredis-timeout-exceptions) fornece documentação de orientação específica sobre resolução de problemas com a biblioteca stackexchange. redis.

> [!NOTE]
> Vários dos passos de resolução de problemas neste guia incluem instruções para executar comandos da Redis e monitorize várias métricas de desempenho. Para obter mais informações e instruções, consulte os artigos da [informações adicionais](#additional-information) secção.
>
>

## <a name="client-side-troubleshooting"></a>Resolução de problemas do lado do cliente

Esta secção aborda a resolução de problemas que ocorrem devido a uma condição na aplicação de cliente.

- [Pressão de memória no cliente](#memory-pressure-on-the-client)
- [Fluxo de tráfego](#burst-of-traffic)
- [Cliente de elevada utilização da CPU](#high-client-cpu-usage)
- [Largura de banda do lado do cliente excedida](#client-side-bandwidth-exceeded)
- [Tamanho grande de solicitação/resposta](#large-requestresponse-size)

### <a name="memory-pressure-on-the-client"></a>Pressão de memória no cliente

A pressão de memória na máquina cliente leva a todos os tipos de problemas de desempenho que podem atrasar o processamento das respostas da cache. Quando chega a pressão de memória, o sistema poderá página dados para o disco. Isso _a falha de página_ faz com que o sistema para significativamente mais lentos.

Para detetar a pressão de memória no cliente:

- Monitorizar a utilização de memória na máquina para certificar-se de que ele não excede a memória disponível.
- Monitorizar o cliente `Page Faults/Sec` contador de desempenho. Durante a operação normal, a maioria dos sistemas tem algumas falhas de página. Aumento de falhas de página correspondente com tempos limite de solicitação pode indicar pressão de memória.

Pressão de memória alta no cliente pode ser atenuado várias formas:

- Aprofunde nos seus padrões de utilização de memória para reduzir o consumo de memória no cliente.
- Atualize a sua VM de cliente para um tamanho maior com mais memória.

### <a name="burst-of-traffic"></a>Fluxo de tráfego

Aumentos repentinos de tráfego combinado com pobre `ThreadPool` definições podem resultar em atrasos no processamento de dados já enviados pelo servidor Redis, mas ainda não são consumidos no lado do cliente.

Monitor de como sua `ThreadPool` estatísticas mudam ao longo do tempo usando [um exemplo `ThreadPoolLogger` ](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs). Pode usar `TimeoutException` mensagens do stackexchange. redis, conforme mostrado abaixo continuar a investigar:

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0,
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

A exceção anterior, há vários problemas que são interessantes:

- Tenha em atenção que no `IOCP` secção e a `WORKER` secção tem um `Busy` valor maior que o `Min` valor. Essa diferença significa que seu `ThreadPool` definições precisam de ajuste.
- Também pode ver `in: 64221`. Este valor indica que os bytes 64,211 foram recebidos na camada de soquete do kernel do cliente, mas ainda não foi lida pela aplicação. Essa diferença normalmente significa que seu aplicativo (por exemplo, stackexchange. redis) não está lendo dados de rede tão rapidamente quanto o servidor é enviar para.

Pode [configurar sua `ThreadPool` definições](https://gist.github.com/JonCole/e65411214030f0d823cb) para se certificar de que seu pool de threads verticalmente rapidamente em períodos de rajada de cenários.

### <a name="high-client-cpu-usage"></a>Cliente de elevada utilização da CPU

Cliente de elevada utilização da CPU indica que o sistema não é possível acompanhar o trabalho que é solicitado a fazer. Apesar da cache enviada a resposta rapidamente, o cliente pode falhar ao processar a resposta de forma oportuna.

Monitorize a utilização da CPU do cliente em todo o sistema com métricas disponíveis no portal do Azure ou através de contadores de desempenho na máquina. Tenha cuidado para não monitorizar *processo* CPU porque um único processo pode ter baixa utilização da CPU, mas a CPU de todo o sistema pode ser elevada. Assista a picos de utilização da CPU que correspondem aos tempos limite. Elevada da CPU também pode fazer com que alta `in: XXX` valores na `TimeoutException` mensagens de erro, conforme descrito no [fluxo de tráfego](#burst-of-traffic) secção.

> [!NOTE]
> Stackexchange. redis 1.1.603 e mais tarde inclui a `local-cpu` métrica no `TimeoutException` mensagens de erro. Certifique-se estiver a utilizar a versão mais recente do [pacote NuGet do stackexchange. redis](https://www.nuget.org/packages/StackExchange.Redis/). Existem erros constantemente a ser corrigidos no código para tornar mais robusto para tempos limite, de modo é importante ter a versão mais recente.
>
>

Para mitigar o alto uso de CPU de um cliente:

- Investigar o que está a causar picos de CPU.
- Atualize o seu cliente para um tamanho maior de VM com mais capacidade de CPU.

### <a name="client-side-bandwidth-exceeded"></a>Largura de banda do lado do cliente excedida

Consoante a arquitetura de computadores cliente, podem ter limitações sobre quanta largura de banda de rede tem disponíveis. Se o cliente excede a largura de banda disponível ao sobrecarregar a capacidade de rede, em seguida, dados não são processados no lado do cliente tão rapidamente quanto o servidor está a enviar. Esta situação pode levar a tempos limite.

Monitorizar como a utilização de largura de banda mudar ao longo do tempo usando [um exemplo `BandwidthLogger` ](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs). Esse código pode não ser executada com êxito em alguns ambientes com permissões restritas (como web sites do Azure).

Para atenuar, reduzir o consumo de largura de banda de rede ou aumente o tamanho da VM para um com mais capacidade de rede do cliente.

### <a name="large-requestresponse-size"></a>Tamanho grande de solicitação/resposta

Uma solicitação/resposta grandes pode provocar tempos limite. Por exemplo, suponha que seu valor de tempo limite configurado no cliente é de 1 segundo. A aplicação solicite duas chaves (por exemplo, "A" e "B") ao mesmo tempo (usando a mesma conexão de rede física). A maioria dos clientes suportam o pedido "pipelining", em que ambos os pedidos "A" e "B" são enviados um após o outro sem esperar por suas respostas. O servidor envia as respostas de volta na mesma ordem. Se a resposta "A" for grande, pode comer a maior parte o tempo limite de solicitações posteriores.

No exemplo a seguir, o pedido 'A' e 'B' são enviadas rapidamente para o servidor. O servidor é iniciado, envia respostas 'A' e 'B' rapidamente. Devido a tempos de transferência de dados, resposta 'B' tem de aguardar por trás de resposta "A" tempo limite, mesmo que o servidor respondeu rapidamente.

    |-------- 1 Second Timeout (A)----------|
    |-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)

Este pedido/resposta é difícil de medir. Pode instrumentar o código do cliente para controlar os pedidos e respostas grandes.

Resoluções para tamanhos de resposta grandes são diversificadas mas incluem:

1. Otimize o seu aplicativo para um grande número de valores de pequenos, em vez de alguns valores grandes.
    - A solução preferencial é dividir os dados para os valores menores relacionados.
    - Consulte a postagem [o que é o intervalo de tamanho do valor ideal para redis? 100 KB é demasiado grande? ](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) para obter detalhes sobre por que valores menores são recomendadas.
1. Aumentar o tamanho da VM para obter capacidades de largura de banda superior
    - Mais largura de banda no cliente ou VM do servidor pode reduzir os tempos de transferência de dados de respostas maior.
    - Compare a utilização da rede atual em ambas as máquinas para os limites do seu tamanho atual de VMS. Mais largura de banda no apenas o servidor ou apenas no cliente pode não ser suficiente.
1. Aumente o número de objetos de conexão, a sua aplicação utiliza.
    - Utilize uma abordagem de rodízio para fazer pedidos ao longo de objetos de conexão diferentes.

## <a name="server-side-troubleshooting"></a>Resolução de problemas do lado do servidor

Esta secção aborda a resolução de problemas que ocorrem devido a uma condição no servidor de cache.

- [Pressão de memória no servidor](#memory-pressure-on-the-server)
- [Elevada utilização da CPU / carregamento do servidor](#high-cpu-usage--server-load)
- [Largura de banda do lado servidor excedida](#server-side-bandwidth-exceeded)

### <a name="memory-pressure-on-the-server"></a>Pressão de memória no servidor

A pressão de memória no lado do servidor nos leva a todos os tipos de problemas de desempenho que podem atrasar o processamento de pedidos. Quando chega a pressão de memória, o sistema poderá página dados para o disco. Isso _a falha de página_ faz com que o sistema para significativamente mais lentos. Existem várias causas possíveis deste pressão de memória:

- A cache é preenchida com dados perto de capacidade máxima.
- Redis está a ver a fragmentação de memória elevada. Essa fragmentação com mais freqüência é causada por armazenar objetos grandes, uma vez que o Redis é otimizado para objetos pequenos.

Redis expõe duas estatísticas através da [informações](https://redis.io/commands/info) comando que pode ajudá-lo a identificar este problema: "used_memory" e "used_memory_rss". Pode [ver estas métricas](cache-how-to-monitor.md#view-metrics-with-azure-monitor) com o portal.

Existem várias alterações possíveis, que pode fazer para ajudar a manter a utilização de memória em bom estado de funcionamento:

- [Configurar uma política de memória](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) e definir as horas de expiração em suas chaves. Esta política não pode ser suficiente se tiver a fragmentação.
- [Configura o valor maxmemory reservados](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) que é grande o suficiente para compensar a fragmentação de memória. Para obter mais informações, consulte o adicionais [considerações para as reservas de memória](#considerations-for-memory-reservations) abaixo.
- Divida os seus objetos em cache grandes em menores objetos relacionados.
- [Criar alertas](cache-how-to-monitor.md#alerts) em métricas, como memória utilizada para ser notificado de antecedência relativamente aos potenciais impactos.
- [Dimensionamento](cache-how-to-scale.md) para um tamanho maior de cache com mais capacidade de memória.

#### <a name="considerations-for-memory-reservations"></a>Considerações para as reservas de memória

A atualizar valores de reserva de memória, como maxmemory-reservado, pode afetar o desempenho da cache. Suponha que tenha uma cache de 53 GB, que é preenchida com 49 GB de dados. Alterar o valor de reserva para 8 GB remove máx. de memória disponível do sistema para 45 GB. Se _used_memory_ ou _used_memory_rss_ valores são maiores que 45 GB, o sistema pode expulsar dados até que ambas _used_memory_ e _used_memory_rss_ estão abaixo 45 GB. Expulsão pode aumentar a fragmentação de memória e de carga do servidor.

### <a name="high-cpu-usage--server-load"></a>Elevada utilização da CPU / carregamento do servidor

Uma carga do servidor de elevada ou a utilização da CPU significa que o servidor não consegue processar pedidos atempadamente. O servidor pode ser lenta responder e não é possível acompanhar taxas de pedidos.

[Monitorizar as métricas](cache-how-to-monitor.md#view-metrics-with-azure-monitor) , tais como carga de CPU ou de servidor. Assista a picos de utilização da CPU que correspondem aos tempos limite.

Existem várias alterações que pode fazer para reduzir a carga do servidor de elevada:

- Investigar o que está a causar picos de CPU, como em execução [comandos caros](#expensive-commands) ou página a falha devido a pressão de memória elevada.
- [Criar alertas](cache-how-to-monitor.md#alerts) em métricas, como a carga de CPU ou de servidor para ser notificado de antecedência relativamente aos potenciais impactos.
- [Dimensionamento](cache-how-to-scale.md) para um tamanho maior de cache com mais capacidade de CPU.

#### <a name="expensive-commands"></a>Comandos de caros

Nem todos os comandos da Redis são criados igualmente - alguns são mais dispendiosos do que outras pessoas. O [Redis comandos documentação](https://redis.io/commands) mostra a complexidade de tempo de cada comando. É recomendado que reveja os comandos que estiver executando em seu cache para compreender o impacto de desempenho desses comandos. Por exemplo, o [chaves](https://redis.io/commands/keys) comando, muitas vezes, é utilizado sem saber o que é uma operação (n). Pode evitar chaves utilizando [analisar](https://redis.io/commands/scan) reduzir a CPU picos.

Utilizar o [SLOWLOG](https://redis.io/commands/slowlog) de comando, pode medir comandos caros que está sendo executados no servidor.

### <a name="server-side-bandwidth-exceeded"></a>Largura de banda do lado do servidor foi excedida

Tamanhos de cache diferente de ter as capacidades de largura de banda de rede diferentes. Se o servidor excede a largura de banda disponível, em seguida, não ser enviados dados para o cliente o mais rapidamente. Pedidos de clientes foi o tempo limite porque o servidor não é possível enviar dados por push para o cliente rápido o bastante.

As métricas de "Cache de leitura" e "Cache de gravação" podem ser utilizadas para ver quanta largura de banda do lado do servidor está a ser utilizada. Pode [ver estas métricas](cache-how-to-monitor.md#view-metrics-with-azure-monitor) no portal.

Para mitigar situações em que a utilização de largura de banda de rede é próximo da capacidade máxima:

- Alterar o comportamento de chamada do cliente para reduzir a pedido de rede.
- [Criar alertas](cache-how-to-monitor.md#alerts) em métricas, como o cache de leitura ou escrita na cache para ser notificado de antecedência relativamente aos potenciais impactos.
- [Dimensionamento](cache-how-to-scale.md) para um tamanho maior de cache com mais capacidade de largura de banda de rede.

## <a name="data-loss-troubleshooting"></a>Resolução de problemas de perda de dados

Eu esperava para determinados dados estejam na minha Cache do Azure para a instância de Redis, mas ele não parece estar ali.

Ver [o que aconteceu aos meus dados no Redis?](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) para encontrar possíveis causas e resoluções.

## <a name="stackexchangeredis-timeout-exceptions"></a>Exceções de tempo limite do stackexchange. redis

Stackexchange. redis utiliza uma configuração de definição denominada `synctimeout` para operações síncronas, com um valor predefinido de 1000 ms. Se uma chamada síncrona não é concluída neste momento, o cliente stackexchange. redis emite um erro de tempo limite semelhante ao seguinte exemplo:

    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)

Esta mensagem de erro contém métricas que podem ajudar a indicar-lhe a causa e possíveis resolução do problema. A tabela seguinte contém detalhes sobre as métricas de mensagem de erro.

| Métrica de mensagem de erro | Detalhes |
| --- | --- |
| portamento da instalação |No último intervalo de tempo: 0 comandos foram emitidos |
| mgr |O Gestor de socket está fazendo `socket.select`, que significa que pede o sistema operacional para indicar um soquete que tem algo a fazer. O leitor não está ativamente ler a partir da rede porque ele não pensa que há algo a ver |
| fila |Existem 73 total de operações em curso |
| qu |6 das operações em curso estão na fila de mensagens e ainda não tiver sido escrito para a rede de saída |
| qs |67 das operações em curso foram enviados para o servidor, mas ainda não está disponível uma resposta. A resposta pode ser `Not yet sent by the server` ou `sent by the server but not yet processed by the client.` |
| qc |0 das operações em curso viu respostas, mas ainda não foi marcada como concluída porque eles estão aguardando o loop de conclusão |
| wR |Existe um escritor Active Directory (ou seja, que não estão a ser ignorados os 6 pedidos não enviados) bytes/activewriters |
| pol. |Não há nenhum leitores Active Directory e zero bytes estão disponíveis para ser continue a ler os bytes/activereaders NIC |

### <a name="steps-to-investigate"></a>Passos para investigar

1. Como melhor prática, certifique-se de que está a utilizar o seguinte padrão para ligar ao utilizar o cliente stackexchange. redis.

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

    Para obter mais informações, consulte [ligar à cache a utilizar o stackexchange. redis](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).

1. Certifique-se de que o servidor e a aplicação de cliente estão na mesma região no Azure. Por exemplo, poderá ser tirando tempos limite quando a cache está na região E.U.A. leste, mas o cliente está na região E.U.A. oeste e o pedido não é concluído dentro do `synctimeout` intervalo ou pode estar prontificando tempos limite quando estiver a depurar a partir do seu computador de desenvolvimento local. 

    É altamente recomendável que a cache e o cliente na mesma região do Azure. Se tiver um cenário que inclui as chamadas de entre várias regiões, deve definir os `synctimeout` intervalo para um valor maior do que o intervalo de 1000 ms predefinido, incluindo um `synctimeout` propriedade na cadeia de ligação. O exemplo seguinte mostra um trecho de uma cadeia de ligação do stackexchange. redis fornecida pelo Azure Cache de Redis com um `synctimeout` de 2000 ms.

        synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
1. Certifique-se estiver a utilizar a versão mais recente do [pacote NuGet do stackexchange. redis](https://www.nuget.org/packages/StackExchange.Redis/). Existem erros constantemente a ser corrigidos no código para tornar mais robusto para tempos limite, de modo é importante ter a versão mais recente.
1. Se os pedidos são ligados por limitações de largura de banda no servidor ou cliente, demora mais tempo para os mesmos para concluir e pode provocar tempos limite. Para ver se o tempo limite é devido a largura de banda de rede no servidor, consulte [largura de banda do lado do servidor excedida](#server-side-bandwidth-exceeded). Para ver se o tempo limite é devido a largura de banda de rede de cliente, consulte [largura de banda do lado do cliente excedida](#client-side-bandwidth-exceeded).
1. Se CPU vinculadas no servidor ou no cliente?

   - Verifique se estiver a obter acoplado por CPU no cliente. Elevada da CPU poderia fazer com que o pedido de não ser processadas no `synctimeout` intervalo e causa um pedido de tempo limite. Mover para um tamanho maior de cliente ou distribuir a carga pode ajudar a controlar este problema.
   - Verifique se estiver recebendo a CPU vinculada no servidor através da monitorização do `CPU` [métrica de desempenho da cache](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). Solicitações recebidas enquanto o Redis é vinculada à CPU podem causar esses pedidos para o tempo limite. Para resolver esta condição, pode distribuir a carga entre várias partições horizontais numa cache premium ou Atualize para um tamanho maior ou o escalão de preço. Para obter mais informações, consulte [excedido de largura de banda de lado do servidor](#server-side-bandwidth-exceeded).
1. Existem comandos a demorar muito tempo a processar no servidor? Comandos de execução longa, que estão a demorar muito tempo a processar no servidor de redis podem provocar tempos limite. Para obter mais informações sobre os comandos de execução longa, consulte [comandos caros](#expensive-commands). Pode ligar a sua Cache do Azure para a instância de Redis com o cliente redis-cli ou o [consola de Redis](cache-configure.md#redis-console). Em seguida, execute o [SLOWLOG](https://redis.io/commands/slowlog) comando para ver se existem pedidos mais lentamente do que o esperado. Servidor redis e stackexchange. redis estão otimizados para muitas solicitações pequenas em vez de menos solicitações grandes. Dividir os dados em segmentos mais pequenos, pode melhorar as coisas aqui.

    Para obter informações sobre a ligação ao ponto final SSL de seu cache redis-cli e túnel a utilizar, consulte a mensagem de blogue [anunciando ASP.NET sessão do fornecedor de estado para versão de pré-visualização de Redis](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).
1. Carga de servidor da Redis elevada pode provocar tempos limite. Pode monitorizar a carga do servidor através da monitorização do `Redis Server Load` [métrica de desempenho da cache](cache-how-to-monitor.md#available-metrics-and-reporting-intervals). Uma carga de servidor de 100 (valor máximo) significa que o servidor redis tem estado ocupado, com nenhum tempo de inatividade, processamento de pedidos. Para ver se determinadas solicitações estão ocupando toda a capacidade de servidor, execute o comando de SlowLog, conforme descrito no parágrafo anterior. Para obter mais informações, consulte utilização elevada da CPU / carregamento do servidor.
1. Houve qualquer outro evento no lado do cliente que pode ter causado um blip de rede? Os eventos comuns incluem: aumentar o número de instâncias de cliente ou para baixo, implantando uma nova versão do cliente ou o dimensionamento automático ativado. No nosso teste, Descobrimos que, dimensionamento automático ou o dimensionamento para cima/para baixo pode causar conectividade de rede de saída seja perdida durante vários segundos. Código de stackexchange. redis face a tais eventos e volta. Enquanto a restabelecer ligação, quaisquer pedidos na fila podem esgotar o tempo limite.
1. Houve um grande pedido anterior vários pequenos pedidos para a cache que excedeu o limite? O parâmetro `qs` no erro mensagem indica o número de pedidos foram enviado do cliente para o servidor, mas ainda não processou uma resposta. Este valor pode continuam a aumentar porque stackexchange. redis utiliza uma única ligação de TCP e só pode ler uma resposta ao mesmo tempo. Mesmo que a primeira operação excedeu o tempo limite, mudando mais dados sejam enviados para ou do servidor. Outros pedidos serão bloqueados até que o pedido de grandes dimensões é concluído e pode fazer com que os detalhes sobre o tempo. Uma solução é minimizar a possibilidade de tempos limite, garantindo que a cache é grande o suficiente para a sua carga de trabalho e a divisão de valores grandes em segmentos mais pequenos. Outra solução possível é utilizar um conjunto de `ConnectionMultiplexer` objetos no seu cliente e escolha menos carregado `ConnectionMultiplexer` ao enviar um pedido de novo. Carregamento em vários objetos de conexão deve impedir que um tempo limite único que faz com que outras solicitações também o tempo limite.
1. Se estiver a utilizar `RedisSessionStateProvider`, certifique-se de que definiu o tempo limite de repetição corretamente. `retryTimeoutInMilliseconds` deve ser superior a `operationTimeoutInMilliseconds`, caso contrário, não existem repetições ocorrem. No exemplo a seguir `retryTimeoutInMilliseconds` está definido como 3000. Para obter mais informações, consulte [fornecedor de estado de sessão do ASP.NET para a Cache de Redis do Azure](cache-aspnet-session-state-provider.md) e [como utilizar os parâmetros de configuração do fornecedor de estado de sessão e o fornecedor de Cache de saída](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration).

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

1. Verificar a utilização de memória no Cache do Azure para o servidor Redis por [monitoramento](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `Used Memory RSS` e `Used Memory`. Se uma política de expulsão estiver em vigor, o Redis começa expulsar chaves quando `Used_Memory` atinge o tamanho da cache. O ideal é que `Used Memory RSS` deve ser apenas ligeiramente superior `Used memory`. Uma grande diferença significa que existe a fragmentação de memória (interno ou externo). Quando `Used Memory RSS` é inferior a `Used Memory`, significa que parte da memória cache tem sido ativado pelo sistema operativo. Se essa troca ocorrer, pode esperar algumas latências significativas. Como Redis não tem controle sobre como as respetivas alocações são mapeadas para páginas de memória elevadas `Used Memory RSS` costuma ser o resultado de um pico na utilização da memória. Quando o servidor Redis libera memória, o alocador leva a memória, mas pode ou podem não oferecer a memória novamente no sistema. Pode haver uma discrepância entre o `Used Memory` consumo de memória e de valor, conforme comunicado pelo sistema operativo. Memória pode foram utilizada e lançada pela Redis, mas não especificado de volta ao sistema. Para ajudar a atenuar os problemas de memória, pode fazer os seguintes passos:

   - Atualize o cache para um tamanho maior para que não está a executar em relação a limitações de memória no sistema.
   - Definir as horas de expiração nas chaves para que os valores mais antigos são expulsos de forma proativa.
   - Monitor de `used_memory_rss` métrica da cache. Quando este valor se aproxima o tamanho da respetiva cache, o que provavelmente começar a ver problemas de desempenho. Distribua os dados por várias partições horizontais, se estiver a utilizar uma cache premium ou atualizar para um tamanho maior de cache.

   Para obter mais informações, consulte [pressão de memória no servidor](#memory-pressure-on-the-server).

## <a name="additional-information"></a>Informações adicionais

- [O Cache do Azure para o tamanho e oferta de Redis devo utilizar?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [Como efetuar testes de desempenho e testar o desempenho de minha cache?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [Como posso executar comandos da Redis?](cache-faq.md#how-can-i-run-redis-commands)
- [Como monitorizar a Cache do Azure para Redis](cache-how-to-monitor.md)
