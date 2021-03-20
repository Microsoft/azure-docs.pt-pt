---
title: Cache Azure para redis gestão FAQs
description: Aprenda as respostas a perguntas comuns que o ajudam a gerir a Cache Azure para Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 08/06/2020
ms.openlocfilehash: 15c7ed4ca9d04e4bb314eea8b92bef749d2369b1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92537665"
---
# <a name="azure-cache-for-redis-management-faqs"></a>Cache Azure para redis gestão FAQs
Este artigo fornece respostas a perguntas comuns sobre como gerir a Cache Azure para Redis.

## <a name="common-questions-and-answers"></a>Perguntas e respostas comuns
Esta secção abrange as seguintes PERGUNTAS Frequentes:

* [Quando devo ativar a porta não-TLS/SSL para a ligação ao Redis?](#when-should-i-enable-the-non-tlsssl-port-for-connecting-to-redis)
* [Quais são as melhores práticas de produção?](#what-are-some-production-best-practices)
* [Quais são algumas das considerações ao usar comandos redis comuns?](#what-are-some-of-the-considerations-when-using-common-redis-commands)
* [Como posso comparar e testar o desempenho da minha cache?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [Detalhes importantes sobre o crescimento da ThreadPool](#important-details-about-threadpool-growth)
* [Ative o servidor GC para obter mais produção sobre o cliente ao utilizar o StackExchange.Redis](#enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis)
* [Considerações de desempenho em torno de ligações](#performance-considerations-around-connections)

### <a name="when-should-i-enable-the-non-tlsssl-port-for-connecting-to-redis"></a>Quando devo ativar a porta não-TLS/SSL para a ligação ao Redis?
O servidor Redis não suporta o TLS de forma nativa, mas o Azure Cache para Redis tem. Se estiver a ligar-se ao Azure Cache para Redis e o seu cliente apoiar o TLS, como o StackExchange.Redis, então deve utilizar o TLS.

>[!NOTE]
>A porta não-TLS é desativada por padrão para a nova Cache Azure para instâncias Redis. Se o seu cliente não suporta TLS, então deve ativar a porta não-TLS seguindo as instruções na secção portas [de acesso](cache-configure.md#access-ports) do [Configure uma cache em Cache Azure para artigo redis.](cache-configure.md)
>
>

As ferramentas vermelhas como `redis-cli` não funcionam com a porta TLS, mas pode utilizar um utilitário de modo a ligar as `stunnel` ferramentas de forma segura à porta TLS seguindo as instruções no anunciado ASP.NET sessão do fornecedor de estado para o post [de pré-visualização do Redis.](https://devblogs.microsoft.com/aspnet/announcing-asp-net-session-state-provider-for-redis-preview-release/)

Para obter instruções sobre o download das ferramentas Redis, veja como posso executar os [comandos Redis?](cache-development-faq.md#how-can-i-run-redis-commands)

### <a name="what-are-some-production-best-practices"></a>Quais são as melhores práticas de produção?
* [StackExchange.As melhores práticas de Redis](#stackexchangeredis-best-practices)
* [Configuração e conceitos](#configuration-and-concepts)
* [Teste de desempenho](#performance-testing)

#### <a name="stackexchangeredis-best-practices"></a>StackExchange.As melhores práticas de Redis
* `AbortConnect`Desaceguiar-se a falso e, em seguida, deixe o ConnectionMultiplexer voltar a ligar-se automaticamente. [Consulte aqui para mais detalhes.](https://gist.github.com/JonCole/36ba6f60c274e89014dd#file-se-redis-setabortconnecttofalse-md)
* Reutilizar o ConnectionMultiplexer - não crie um novo para cada pedido. O `Lazy<ConnectionMultiplexer>` padrão [mostrado aqui](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache) é recomendado.
* Redis funciona melhor com valores menores, por isso considere cortar dados maiores em várias teclas. [Nesta discussão redis,](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ)100 kb é considerado grande. Leia [este artigo](https://gist.github.com/JonCole/db0e90bedeb3fc4823c2#large-requestresponse-size) para um problema de exemplo que pode ser causado por grandes valores.
* Configure as [definições de ThreadPool](#important-details-about-threadpool-growth) para evitar intervalos de tempo.
* Utilize pelo menos o connectTimeout predefinido de 5 segundos. Este intervalo dá tempo suficiente ao StackExchange.Redis para restabelecer a ligação em caso de blip de rede.
* Esteja atento aos custos de desempenho associados às diferentes operações que está a executar. Por exemplo, o `KEYS` comando é uma operação O(n) e deve ser evitado. O [site redis.io](https://redis.io/commands/) tem detalhes sobre a complexidade do tempo para cada operação que suporta. Clique em cada comando para ver a complexidade de cada operação.

#### <a name="configuration-and-concepts"></a>Configuração e conceitos
* Utilize o Standard ou Premium Tier para sistemas de produção. O Escalão Básico é um sistema de nó único sem replicação de dados e sem SLA. Além disso, utilize pelo menos uma cache C1. As caches C0 são normalmente utilizadas para cenários simples dev/teste.
* Lembre-se que o Redis é uma loja de dados **na memória.** Leia [este artigo](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) para que esteja ciente de cenários onde a perda de dados pode ocorrer.
* Desenvolver o seu sistema de modo a que possa lidar com os blips de ligação [devido a remendos e falhas .](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md)

#### <a name="performance-testing"></a>Teste de desempenho
* Comece por usar `redis-benchmark.exe` para obter uma sensação de possível produção antes de escrever os seus próprios testes perf. Como `redis-benchmark` não suporta o TLS, tem de [ativar a porta Non-TLS através do portal Azure](cache-configure.md#access-ports) antes de realizar o teste. Por exemplo, veja [como posso comparar e testar o desempenho da minha cache?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* O VM cliente utilizado para testes deve estar na mesma região que o seu Azure Cache para a instância Redis.
* Recomendamos a utilização de Série VM Dv2 para o seu cliente, uma vez que têm melhor hardware e devem dar os melhores resultados.
* Certifique-se de que o seu VM do cliente que escolhe tem pelo menos a capacidade de computação e largura de banda como a cache que está a testar.
* Ativar o VRSS na máquina do cliente se estiver no Windows. [Consulte aqui para mais detalhes.](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn383582(v=ws.11))
* As instâncias de nível premium Redis têm melhor latência e produção de rede porque estão a executar hardware melhor tanto para CPU como para a Rede.

### <a name="what-are-some-of-the-considerations-when-using-common-redis-commands"></a>Quais são algumas das considerações ao usar comandos redis comuns?

* Evite usar certos comandos Redis que demoram muito tempo a ser concluídos, a menos que compreenda completamente o impacto destes comandos. Por exemplo, não executar o comando [KEYS](https://redis.io/commands/keys) em produção. Dependendo do número de chaves, pode levar muito tempo para voltar. Redis é um servidor de uma só linha e processa comandos um de cada vez. Se tiver outros comandos emitidos após o KEYS, estes não serão processados até que o Redis processe o comando KEYS. O [site redis.io](https://redis.io/commands/) tem detalhes sobre a complexidade do tempo para cada operação que suporta. Clique em cada comando para ver a complexidade de cada operação.
* Tamanhos das chaves - devo utilizar pequenas chaves/valores ou grandes chaves/valores? Depende do cenário. Se o seu cenário necessitar de teclas maiores, pode ajustar o ConnectionTimeout, depois tentar os valores e ajustar a sua lógica de repetição. Do ponto de vista do servidor Redis, valores mais pequenos dão um melhor desempenho.
* Estas considerações não significam que não se possa armazenar valores maiores em Redis; deve estar ciente das seguintes considerações. As latências serão mais altas. Se tiver um conjunto de dados que seja maior e um menor, pode utilizar várias instâncias ConnectionMultiplexer, cada uma configurada com um conjunto diferente de valores de tempo e retripamento, conforme descrito no anterior O que fazem as opções de [configuração StackExchange.Redis fazem secção.](cache-development-faq.md#what-do-the-stackexchangeredis-configuration-options-do)

### <a name="how-can-i-benchmark-and-test-the-performance-of-my-cache"></a>Como posso comparar e testar o desempenho da minha cache?
* [Ativar o diagnóstico da cache](cache-how-to-monitor.md#enable-cache-diagnostics) para poder [monitorizar](cache-how-to-monitor.md) o estado de funcionamento da cache. Pode ver as métricas no portal Azure e também pode [descarregá-las e revê-las](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) utilizando as ferramentas à sua escolha.
* Pode utilizar redis-benchmark.exe para carregar o seu servidor Redis.
* Certifique-se de que o cliente de teste de carga e o Cache Azure para Redis estão na mesma região.
* Utilize redis-cli.exe e monitorize a cache utilizando o comando INFO.
* Se a sua carga estiver a causar uma grande fragmentação de memória, deverá escalar até um tamanho de cache maior.
* Para obter instruções sobre o download das ferramentas Redis, veja como posso executar os [comandos Redis?](cache-development-faq.md#how-can-i-run-redis-commands)

Os seguintes comandos fornecem um exemplo de utilização redis-benchmark.exe. Para obter resultados precisos, execute estes comandos a partir de um VM na mesma região que o seu cache.

* Teste pipelined set solicita usando uma carga útil de 1k

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t SET -n 1000000 -d 1024 -P 50`
* Teste pipelined GET solicita usando uma carga útil de 1k.

>[!NOTE]
> Executar o teste SET mostrado acima primeiro para preencher cache
>

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t GET -n 1000000 -d 1024 -P 50`

### <a name="important-details-about-threadpool-growth"></a>Detalhes importantes sobre o crescimento da ThreadPool
O CLR ThreadPool tem dois tipos de fios - fios "Worker" e "I/O Completion Port" (IOCP).

* Os fios dos trabalhadores são usados para coisas como processar o `Task.Run(…)` , ou `ThreadPool.QueueUserWorkItem(…)` métodos. Estes fios também são utilizados por vários componentes no CLR quando o trabalho tem de acontecer num fio de fundo.
* Os fios do IOCP são utilizados quando acontece iO assíncronos, como quando lê a partir da rede.

A piscina de roscas fornece novos fios de trabalho ou fios de conclusão de E/S a pedido (sem qualquer estrangulamento) até atingir a definição "Mínimo" para cada tipo de fio. Por predefinição, o número mínimo de fios é definido para o número de processadores de um sistema.

Uma vez que o número de fios existentes (ocupados) atinja o número "mínimo" de fios, o ThreadPool acelerará a velocidade a que injeta novos fios num fio por 500 milissegundos. Normalmente, se o seu sistema tiver uma explosão de trabalho a precisar de um fio IOCP, irá processar isso rapidamente. No entanto, se a explosão de trabalho for mais do que a configuração "Mínima" configurada, haverá algum atraso no processamento de parte do trabalho, uma vez que o ThreadPool espera que uma de duas coisas aconteça.

* Um fio existente torna-se livre para processar o trabalho.
* Nenhum fio existente torna-se livre por 500 ms, por isso é criado um novo fio.

Basicamente, significa que quando o número de threads Busy é maior do que os threads Min, você provavelmente está pagando um atraso de 500 ms antes do tráfego de rede ser processado pela aplicação. Além disso, é importante notar que quando um fio existente permanece inativo por mais de 15 segundos (com base no que me lembro), será limpo e este ciclo de crescimento e encolhimento pode repetir-se.

Se olharmos para uma mensagem de erro de exemplo do StackExchange.Redis (construa 1.0.450 ou mais tarde), verá que agora imprime as estatísticas da ThreadPool (ver detalhes do IOCP e do WORKER abaixo).

```
System.TimeoutException: Timeout performing GET MyKey, inst: 2, mgr: Inactive,
queue: 6, qu: 0, qs: 6, qc: 0, wr: 0, wq: 0, in: 0, ar: 0,
IOCP: (Busy=6,Free=994,Min=4,Max=1000),
WORKER: (Busy=3,Free=997,Min=4,Max=1000)
```

No exemplo anterior, pode ver-se que para o fio DO IOCP existem seis fios ocupados e o sistema está configurado para permitir quatro fios mínimos. Neste caso, o cliente provavelmente teria visto dois atrasos de 500 ms, porque 6 > 4.

Note que StackExchange.Redis pode atingir intervalos se o crescimento de fios DE IOCP ou WORKER for acelerado.

#### <a name="recommendation"></a>Recomendação

Tendo em conta estas informações, recomendamos vivamente que os clientes estabeleçam o valor mínimo de configuração para fios IOCP e WORKER para algo maior do que o valor padrão. Não podemos dar orientações de tamanho único sobre o que deve ser este valor, porque o valor certo para uma aplicação será provavelmente demasiado alto ou baixo para outra aplicação. Esta definição também pode ter impacto no desempenho de outras partes de aplicações complicadas, pelo que cada cliente precisa de afinar esta definição às suas necessidades específicas. Um bom ponto de partida é 200 ou 300, depois testar e ajustar conforme necessário.

Como configurar este cenário:

* Recomendamos alterar esta definição programáticamente utilizando o método [ThreadPool.SetMinThreads (...)](/dotnet/api/system.threading.threadpool.setminthreads#System_Threading_ThreadPool_SetMinThreads_System_Int32_System_Int32_) em `global.asax.cs` . Por exemplo:

    ```csharp
    private readonly int minThreads = 200;
    void Application_Start(object sender, EventArgs e)
    {
        // Code that runs on application startup
        AreaRegistration.RegisterAllAreas();
        RouteConfig.RegisterRoutes(RouteTable.Routes);
        BundleConfig.RegisterBundles(BundleTable.Bundles);
        ThreadPool.SetMinThreads(minThreads, minThreads);
    }
    ```

    > [!NOTE]
    > O valor especificado por este método é uma configuração global, afetando toda a AppDomain. Por exemplo, se tiver uma máquina de 4 núcleos e quiser definir *minWorkerThreads* e *minIoThreads* a 50 por CPU durante o tempo de funcionação, utilizará **ThreadPool.SetMinThreads (200, 200)**.

* Também é possível especificar a definição de fios mínimos utilizando a [definição de configuração *minIoThreads* ou *minWorkerThreads*](/previous-versions/dotnet/netframework-4.0/7w2sway1(v=vs.100)) sob o `<processModel>` elemento de configuração em `Machine.config` , normalmente localizado em `%SystemRoot%\Microsoft.NET\Framework\[versionNumber]\CONFIG\` . **A definição do número de fios mínimos desta forma não é geralmente recomendada, porque é uma definição de todo o sistema.**

  > [!NOTE]
  > O valor especificado neste elemento de configuração é uma definição *por núcleo.* Por exemplo, se tiver uma máquina de 4 núcleos e quiser que a sua definição *de minIoThreads* seja de 200 em tempo de execução, utilizará `<processModel minIoThreads="50"/>` .
  >

### <a name="enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis"></a>Ative o servidor GC para obter mais produção sobre o cliente ao utilizar o StackExchange.Redis
Ativar o servidor GC pode otimizar o cliente e proporcionar um melhor desempenho e produção ao utilizar o StackExchange.Redis. Para obter mais informações sobre o servidor GC e como o habilitar, consulte os seguintes artigos:

* [Para ativar o servidor GC](/dotnet/framework/configure-apps/file-schema/runtime/gcserver-element)
* [Fundamentos da Coleção de Lixo](/dotnet/standard/garbage-collection/fundamentals)
* [Recolha e Performance do Lixo](/dotnet/standard/garbage-collection/performance)

### <a name="performance-considerations-around-connections"></a>Considerações de desempenho em torno de ligações

Cada nível de preços tem limites diferentes para ligações ao cliente, memória e largura de banda. Embora cada tamanho de cache permita *até* um certo número de ligações, cada ligação ao Redis tem sobrecarga associada a ele. Um exemplo de tal sobrecarga seria o uso de CPU e memória como resultado da encriptação TLS/SSL. O limite máximo de ligação para um dado tamanho de cache pressupõe uma cache levemente carregada. Se a carga da sobrecarga de ligação *mais* a carga das operações do cliente exceder a capacidade do sistema, a cache pode experimentar problemas de capacidade mesmo que não tenha excedido o limite de ligação para o tamanho atual da cache.

Para obter mais informações sobre os diferentes limites de ligações para cada nível, consulte [Azure Cache para preços de Redis](https://azure.microsoft.com/pricing/details/cache/). Para obter mais informações sobre ligações e outras configurações predefinicionam, consulte [a configuração do servidor Redis padrão](cache-configure.md#default-redis-server-configuration).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [outros Azure Cache para Redis FAQs](cache-faq.md).