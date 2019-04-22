---
title: A Cache de FAQ de Redis do Azure | Documentos da Microsoft
description: Aprender as respostas às perguntas mais comuns, padrões e práticas recomendadas para a Cache do Azure para Redis
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: c2c52b7d-b2d1-433a-b635-c20180e5cab2
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: yegu
ms.openlocfilehash: 65e8553969aa92848b1c4496724a7b7754b5d659
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58895601"
---
# <a name="azure-cache-for-redis-faq"></a>FAQ da Cache do Azure para Redis
Obtenha as respostas a perguntas comuns, padrões e práticas recomendadas para a Cache do Azure para Redis.

## <a name="what-if-my-question-isnt-answered-here"></a>E se minha pergunta não respondida aqui?
Se a sua pergunta não está listada aqui, informe-nos e vamos ajudar a encontrar uma resposta.

* Pode postar uma pergunta nos comentários no final nestas perguntas frequentes e interagir com a equipe de Cache do Azure e outros membros da Comunidade sobre este artigo.
* Para chegar a um vasto público, pode postar uma pergunta sobre o [fórum MSDN do Azure Cache](https://social.msdn.microsoft.com/forums/azure/home?forum=azurecache) e interagir com a equipe de Cache do Azure e outros membros da Comunidade.
* Se quiser fazer um pedido de funcionalidade, pode enviar os pedidos e idéias para [do Azure na Cache de Redis User Voice](https://feedback.azure.com/forums/169382-cache).
* Também pode enviar um e-mail para nós através de [comentários externos do Azure Cache](mailto:azurecache@microsoft.com).

## <a name="azure-cache-for-redis-basics"></a>Cache do Azure para o básico de Redis
As perguntas frequentes a esta secção abrangem algumas noções básicas de Cache do Azure para Redis.

* [O que é a Cache de Redis do Azure?](#what-is-azure-cache-for-redis)
* [Como posso começar a utilizar com a Cache do Azure para Redis?](#how-can-i-get-started-with-azure-cache-for-redis)

As seguintes FAQs abrangem os conceitos básicos e perguntas sobre o Azure Cache de Redis e são respondidas numa das outras seções de perguntas frequentes.

* [O Cache do Azure para o tamanho e oferta de Redis devo utilizar?](#what-azure-cache-for-redis-offering-and-size-should-i-use)
* [O Cache do Azure para clientes da Redis devo utilizar?](#what-azure-cache-for-redis-clients-can-i-use)
* [Existe um emulador local para a Cache do Azure para Redis?](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [Como posso monitorizar o estado de funcionamento e desempenho da minha cache?](#how-do-i-monitor-the-health-and-performance-of-my-cache)

## <a name="planning-faqs"></a>FAQs de planeamento
* [O Cache do Azure para o tamanho e oferta de Redis devo utilizar?](#what-azure-cache-for-redis-offering-and-size-should-i-use)
* [Cache do Azure para desempenho de Redis](#azure-cache-for-redis-performance)
* [Em que região de localizar minha cache?](#in-what-region-should-i-locate-my-cache)
* [Como é faturada de Cache do Azure para Redis?](#how-am-i-billed-for-azure-cache-for-redis)
* [Pode utilizar a Cache do Azure para Redis com o Azure Government Cloud, Azure China Cloud ou Microsoft Azure Alemanha?](#can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany)

## <a name="development-faqs"></a>FAQs de desenvolvimento
* [O que faz as opções de configuração stackexchange. redis?](#what-do-the-stackexchangeredis-configuration-options-do)
* [O Cache do Azure para clientes da Redis devo utilizar?](#what-azure-cache-for-redis-clients-can-i-use)
* [Existe um emulador local para a Cache do Azure para Redis?](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [Como posso executar comandos da Redis?](#how-can-i-run-redis-commands)
* [Por que não a Cache de Redis do Azure tem uma referência de biblioteca de classe do MSDN, como alguns dos outros serviços do Azure?](#why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services)
* [Posso utilizar a Cache do Azure para Redis como uma cache de sessão do PHP?](#can-i-use-azure-cache-for-redis-as-a-php-session-cache)
* [Quais são as bases de dados Redis?](#what-are-redis-databases)

## <a name="security-faqs"></a>FAQs sobre a segurança
* [Quando deve a ativar a porta não SSL para conectar a Redis?](#when-should-i-enable-the-non-ssl-port-for-connecting-to-redis)

## <a name="production-faqs"></a>FAQs de produção
* [Quais são algumas práticas recomendadas de produção?](#what-are-some-production-best-practices)
* [Quais são algumas das considerações ao utilizar comandos da Redis comuns?](#what-are-some-of-the-considerations-when-using-common-redis-commands)
* [Como efetuar testes de desempenho e testar o desempenho de minha cache?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [Detalhes importantes sobre o crescimento de ThreadPool](#important-details-about-threadpool-growth)
* [Ativar o GC de servidor obter mais débito no cliente, ao utilizar o stackexchange. redis](#enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis)
* [Considerações de desempenho em torno de ligações](#performance-considerations-around-connections)

## <a name="monitoring-and-troubleshooting-faqs"></a>Monitorização e resolução de problemas de perguntas frequentes
As perguntas frequentes nesta secção sobre monitorização e resolução de problemas de perguntas comuns. Para obter mais informações sobre a monitorização e resolução de problemas de Cache para instâncias de Redis do Azure, consulte [como monitorizar a Cache do Azure para Redis](cache-how-to-monitor.md) e [como resolver problemas de Cache do Azure para Redis](cache-how-to-troubleshoot.md).

* [Como posso monitorizar o estado de funcionamento e desempenho da minha cache?](#how-do-i-monitor-the-health-and-performance-of-my-cache)
* [Por que estou a ver tempos limite?](#why-am-i-seeing-timeouts)
* [Por que motivo o meu cliente foi desligado da cache?](#why-was-my-client-disconnected-from-the-cache)

## <a name="prior-cache-offering-faqs"></a>FAQs sobre a oferta de Cache anterior
* [Qual das ofertas da Cache do Azure é melhor para mim?](#which-azure-cache-offering-is-right-for-me)

### <a name="what-is-azure-cache-for-redis"></a>O que é a Cache de Redis do Azure?
A Cache de Redis do Azure baseia-se o software de código aberto popular [Redis](https://redis.io/). Ele fornece acesso a uma Cache de Azure segura, dedicada para Redis, gerida pela Microsoft e acessível a partir de qualquer aplicação no Azure. Para obter uma descrição mais detalhada, consulte a [a Cache de Redis do Azure](https://azure.microsoft.com/services/cache/) página de produto no Azure.com.

### <a name="how-can-i-get-started-with-azure-cache-for-redis"></a>Como posso começar a utilizar com a Cache do Azure para Redis?
Existem várias formas, que pode começar a utilizar a Cache do Azure para Redis.

* Pode consultar um dos nossos tutoriais disponíveis para [.NET](cache-dotnet-how-to-use-azure-redis-cache.md), [ASP.NET](cache-web-app-howto.md), [Java](cache-java-get-started.md), [node. js](cache-nodejs-get-started.md), e [Python](cache-python-get-started.md).
* Pode assistir [como criar alto desempenho aplicativos usando o Cache do Microsoft Azure para Redis](https://azure.microsoft.com/documentation/videos/how-to-build-high-performance-apps-using-microsoft-azure-cache/).
* Pode consultar a documentação do cliente para os clientes que correspondam a linguagem de desenvolvimento do seu projeto para ver como utilizar o Redis. Existem muitos clientes de Redis que podem ser utilizados com o Azure Cache de Redis. Para obter uma lista de clientes Redis, consulte [ https://redis.io/clients ](https://redis.io/clients).

Se ainda não tiver uma conta do Azure, pode:

* [Criar uma conta do Azure gratuitamente](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero). Receberá créditos que podem ser utilizados para experimentar os serviços pagos do Azure. Mesmo depois de gastar todos os créditos, pode manter a conta e utilizar os serviços e recursos gratuitos do Azure.
* [Ativar os benefícios de subscritor do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). A sua subscrição do MSDN dá-lhe créditos todos os meses que pode utilizar em serviços pagos do Azure.

<a name="cache-size"></a>

### <a name="what-azure-cache-for-redis-offering-and-size-should-i-use"></a>O Cache do Azure para o tamanho e oferta de Redis devo utilizar?
Cada Cache do Azure para a oferta de Redis fornece diferentes níveis de **tamanho**, **largura de banda**, **elevada disponibilidade**, e **SLA** opções.

Seguem-se as considerações para escolher uma oferta de Cache.

* **Memória**: Os escalões básico e Standard oferecem 250 MB – 53 GB. O escalão Premium oferece até 530 GB. Para obter mais informações, consulte [do Azure na Cache de Redis preços](https://azure.microsoft.com/pricing/details/cache/).
* **Desempenho de rede**: Se tiver uma carga de trabalho que precisa de débito elevado, o escalão Premium oferece mais largura de banda em comparação comparada a Standard ou Basic. Também dentro de cada camada, caches de tamanho maior tem mais largura de banda por causa da VM subjacente que aloja a cache. Consulte a [seguinte tabela](#cache-performance) para obter mais informações.
* **Débito**: O escalão Premium oferece o débito máximo de disponível. Se o cliente ou servidor de cache atingir os limites de largura de banda, poderá receber tempos limite no lado do cliente. Para obter mais informações, consulte a tabela seguinte.
* **Elevada disponibilidade/SLA**: A Cache de Redis do Azure garante que uma cache Standard/Premium está disponível, pelo menos, 99,9% do tempo. Para saber mais sobre o nosso SLA, consulte [do Azure na Cache de Redis preços](https://azure.microsoft.com/support/legal/sla/cache/v1_0/). O SLA cobre apenas a conectividade aos pontos finais do Cache. O SLA não engloba a proteção contra perda de dados. Recomendamos que utilize a funcionalidade de persistência de dados de Redis no escalão Premium para aumentar a resiliência contra a perda de dados.
* **Persistência de dados de redis**: O escalão Premium permite-lhe manter os dados de cache numa conta de armazenamento do Azure. Em cache básico/Standard, todos os dados são armazenados apenas na memória. Se existirem problemas de infraestrutura subjacentes lá podem ser a potencial perda de dados. Recomendamos que utilize a funcionalidade de persistência de dados de Redis no escalão Premium para aumentar a resiliência contra a perda de dados. A Cache de Redis do Azure oferece RDB e AOF (brevemente) as opções de persistência de Redis. Para obter mais informações, consulte [como configurar a persistência para uma Cache do Azure Premium para Redis](cache-how-to-premium-persistence.md).
* **Cluster de redis**: Criar caches superiores a 53 GB ou dividir os dados em vários nós de Redis, pode utilizar o clustering de Redis, que está disponível no escalão Premium. Cada nó é composta por um par de cache primário/réplica para elevada disponibilidade. Para obter mais informações, consulte [como configurar o clustering para uma Cache do Azure Premium para Redis](cache-how-to-premium-clustering.md).
* **Avançada de isolamento de segurança e rede**: Implementação de rede Virtual (VNET) do Azure fornece maior segurança e isolamento para a sua Cache do Azure para Redis, bem como sub-redes, políticas de controlo de acesso e outras funcionalidades adicionais para restringem o acesso. Para obter mais informações, consulte [como configurar o suporte de rede Virtual para uma Cache do Azure Premium para Redis](cache-how-to-premium-vnet.md).
* **Configurar Redis**: Nos escalões Standard e Premium, pode configurar Redis para notificações Keyspace.
* **Número máximo de ligações de cliente**: O escalão Premium oferece o número máximo de clientes que pode ligar-se ao Redis, com um número superior de ligações para as caches de tamanho maior. Clustering não aumenta o número de ligações disponíveis para uma cache em cluster. Para obter mais informações, consulte [Cache do Azure para Redis preços](https://azure.microsoft.com/pricing/details/cache/).
* **Dedicados ao núcleo do servidor Redis**: No escalão Premium, todos os tamanhos de cache tem um núcleo dedicada para Redis. Nos escalões básico/Standard, o tamanho de C1 e acima têm um núcleo dedicada para o servidor Redis.
* **O redis é um único thread** , de modo a ter mais de dois núcleos não fornece benefícios adicionais sobre ter apenas dois núcleos, mas os tamanhos de VM maiores têm normalmente mais largura de banda que tamanhos mais pequenos. Se o cliente ou servidor de cache atingir os limites de largura de banda, em seguida, receber tempos limite no lado do cliente.
* **Melhorias de desempenho**: Caches no escalão Premium são implementadas em hardware com processadores mais rápidos, fornecendo um melhor desempenho em comparação com o escalão básico ou Standard. As Caches de escalão Premium têm um débito superior e latências mais baixas.

<a name="cache-performance"></a>

### <a name="azure-cache-for-redis-performance"></a>Cache do Azure para desempenho de Redis
A tabela seguinte mostra os valores de largura de banda máxima observados enquanto testa vários tamanhos de Standard e Premium coloca em cache usando `redis-benchmark.exe` partir de uma VM de IaaS em relação à Cache do Azure para o ponto final de Redis. Para o débito SSL, o redis-parâmetro de comparação é utilizado com túnel para ligar à Cache do Azure para o ponto final de Redis.

>[!NOTE] 
>Estes valores não são garantidos e não existe nenhum SLA para estes números, mas deve ser típico. Deve carregar testar seu próprio aplicativo para determinar o tamanho de cache adequada para a sua aplicação.
>Esses números podem ser alterados conforme periodicamente publicamos resultados mais recentes.
>

Esta tabela, preste as seguintes conclusões:

* Taxa de transferência para as caches que têm o mesmo tamanho é maior no escalão Premium em comparação com o escalão Standard. Por exemplo, com uma Cache de 6 GB, o débito de P1 é 180,000 RPS em comparação com a 100.000 para C3.
* Com o clustering de Redis, débito aumenta linearmente, à medida que aumenta o número de shards (nós) no cluster. Por exemplo, se criar um cluster de P4 de 10 shards, em seguida, a taxa de transferência disponível é 400 000 * 10 = 4 milhões RPS.
* Débito para tamanhos de chave maiores é superior no escalão Premium em comparação com o escalão Standard.

| Escalão de preço | Tamanho | Núcleos de CPU | Largura de banda disponível | Tamanho do valor de 1 KB | Tamanho do valor de 1 KB |
| --- | --- | --- | --- | --- | --- |
| **Tamanhos de cache padrão** | | |**Megabits por segundo (Mb/s) / Megabytes por segundo (MB/s)** |**Pedidos por segundo (RPS) não SSL** |**Pedidos por segundo (RPS) SSL** |
| C0 |250 MB |Partilhado |100 / 12.5 |15 000 |7.500 |
| C1 |1 GB |1 |500 / 62.5 |38,000 |20,720 |
| C2 |2,5 GB |2 |500 / 62.5 |41,000 |37,000 |
| C3 |6 GB |4 |1000 / 125 |100 000 |90,000 |
| C4 |13 GB |2 |500 / 62.5 |60,000 |55,000 |
| C5 |26 GB |4 |1,000 / 125 |102,000 |93,000 |
| C6 |53 GB |8 |2,000 / 250 |126,000 |120,000 |
| **Tamanhos de cache Premium** | |**Núcleos de CPU por shard** | **Megabits por segundo (Mb/s) / Megabytes por segundo (MB/s)** |**Pedidos por segundo (RPS) não SSL, por shard** |**Pedidos por segundo (RPS) SSL, por shard** |
| P1 |6 GB |2 |1,500 / 187.5 |180,000 |172,000 |
| P2 |13 GB |4 |3,000 / 375 |350,000 |341,000 |
| P3 |26 GB |4 |3,000 / 375 |350,000 |341,000 |
| P4 |53 GB |8 |6,000 / 750 |400,000 |373,000 |

Para obter instruções sobre como configurar o túnel ou baixar as ferramentas de Redis, tal como `redis-benchmark.exe`, consulte a [como executar comandos da Redis?](#cache-commands) secção.

<a name="cache-region"></a>

### <a name="in-what-region-should-i-locate-my-cache"></a>Em que região de localizar minha cache?
Para obter o melhor desempenho e latência mais baixa, localize a sua Cache do Azure para o Redis na mesma região que a aplicação de cliente de cache.

<a name="cache-billing"></a>

### <a name="how-am-i-billed-for-azure-cache-for-redis"></a>Como é faturada de Cache do Azure para Redis?
Cache do Azure para obter preços de Redis está [aqui](https://azure.microsoft.com/pricing/details/cache/). A página de preços apresenta uma lista de preços como uma tarifa por hora. Caches são faturadas numa base por minuto desde o momento em que a cache é criada até o momento em que um cache é eliminada. Não existe nenhuma opção para parar ou pausar a faturação de uma cache.

### <a name="can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany"></a>Pode utilizar a Cache do Azure para Redis com o Azure Government Cloud, Azure China Cloud ou Microsoft Azure Alemanha?
Sim, a Cache de Redis do Azure está disponível no Azure Government Cloud, Azure China Cloud e no Microsoft Azure Alemanha. Os URLs para aceder e gerir a Cache do Azure para Redis são diferentes nessas nuvens em comparação com a Cloud pública do Azure. 

| Nuvem   | Sufixo DNS para Redis            |
|---------|---------------------------------|
| Público  | *.redis.cache.windows.net       |
| Governo dos EUA  | *.redis.cache.usgovcloudapi.net |
| Alemanha | *.redis.cache.cloudapi.de       |
| China   | *.redis.cache.chinacloudapi.cn  |

Para obter mais informações sobre considerações ao utilizar o Azure Cache de Redis com outras clouds, consulte os links a seguir.

- [Bases de dados do Azure Government - a Cache de Redis do Azure](../azure-government/documentation-government-services-database.md#azure-cache-for-redis)
- [O Azure na China Cloud - a Cache de Redis do Azure](https://www.azure.cn/home/features/redis-cache/)
- [Microsoft Azure Alemanha](https://azure.microsoft.com/overview/clouds/germany/)

Para obter informações sobre como utilizar o Azure Cache de Redis com o PowerShell no Azure Government Cloud, Azure China Cloud e no Microsoft Azure Alemanha, consulte [como ligar a outras clouds - Cache do Azure para Redis PowerShell](cache-howto-manage-redis-cache-powershell.md#how-to-connect-to-other-clouds).

<a name="cache-configuration"></a>

### <a name="what-do-the-stackexchangeredis-configuration-options-do"></a>O que faz as opções de configuração stackexchange. redis?
Stackexchange. redis tem muitas opções. Esta secção aborda algumas das configurações comuns. Para obter mais informações sobre as opções de stackexchange. redis, consulte [configuração stackexchange. redis](https://stackexchange.github.io/StackExchange.Redis/Configuration).

| ConfigurationOptions | Descrição | Recomendação |
| --- | --- | --- |
| AbortOnConnectFail |Quando definido como true, a ligação não voltar a ligar após uma falha de rede. |Definido como false e deixe o stackexchange. redis voltar a ligar automaticamente. |
| ConnectRetry |Ligar o número de vezes para repetir tentativas de ligação durante a inicial. |Consulte as seguintes notas para obter orientações. |
| ConnectTimeout |Tempo limite em ms para operações de ligação. |Consulte as seguintes notas para obter orientações. |

Normalmente, os valores predefinidos do cliente são suficientes. Pode ajustar as opções com base na carga de trabalho.

* **Repetições**
  * Para ConnectRetry e ConnectTimeout, a orientação geral é efetuar a ativação rápida e tente novamente. Esta orientação baseia-se a sua carga de trabalho e quanto tempo no médio necessário para o seu cliente emitir um comando de Redis e receber uma resposta.
  * Deixe que o stackexchange. redis restabelece ligação automaticamente em vez de a verificar o estado de ligação e voltar a ligar-se. **Evite usar a propriedade ConnectionMultiplexer.IsConnected**.
  * Snowballing - por vezes, poderão ocorrer um problema em que a repetir a operação e as novas tentativas snowball e nunca recuperar. Se snowballing ocorrer, deve considerar a utilização de um algoritmo de repetição de término exponencial conforme descrito em [Repita orientações gerais](../best-practices-retry-general.md) publicados pelo grupo Microsoft Patterns & Practices.
* **Valores de tempo limite**
  * Considere a sua carga de trabalho e defina os valores em conformidade. Se estiver armazenando valores grandes, defina o tempo limite para um valor mais alto.
  * Definir `AbortOnConnectFail` para false e permite que stackexchange. redis voltar a ligar para.
  * Utilize uma única instância de ConnectionMultiplexer para a aplicação. Pode usar um LazyConnection para criar uma única instância, que é devolvida por uma propriedade de ligação, conforme mostrado na [ligar à cache usando a classe ConnectionMultiplexer](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).
  * Definir o `ConnectionMultiplexer.ClientName` propriedade para um nome exclusivo de instância de aplicação para fins de diagnóstico.
  * Utilizar vários `ConnectionMultiplexer` instâncias para cargas de trabalho personalizadas.
      * Pode seguir este modelo, se tiver uma carga variada na sua aplicação. Por exemplo:
      * Pode ter um multiplexador para lidar com grandes chaves.
      * Pode ter um multiplexador para lidar com pequenas chaves.
      * Pode definir valores diferentes para o tempo limite da conexão e lógica de repetição para cada ConnectionMultiplexer que utilizar.
      * Definir o `ClientName` propriedade em cada um multiplexador para ajudar com o diagnóstico.
      * Esta orientação pode levar a mais simplificada latência por `ConnectionMultiplexer`.

### <a name="what-azure-cache-for-redis-clients-can-i-use"></a>O Cache do Azure para clientes da Redis devo utilizar?
Uma das grandes coisas da Redis é que existem muitos clientes para dar suporte a muitas linguagens de programação diferentes. Para obter uma lista atual de clientes, consulte [clientes da Redis](https://redis.io/clients). Para tutoriais que abrangem várias linguagens diferentes e os clientes, veja [como utilizar a Cache do Azure para Redis](cache-dotnet-how-to-use-azure-redis-cache.md) e isso artigos colateral na tabela de conteúdos.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

<a name="cache-emulator"></a>

### <a name="is-there-a-local-emulator-for-azure-cache-for-redis"></a>Existe um emulador local para a Cache do Azure para Redis?
Não existe nenhum emulador local para a Cache de Redis do Azure, mas pode executar a versão de MSOpenTech de redis-server.exe do [Redis ferramentas da linha de comandos](https://github.com/MSOpenTech/redis/releases/) no local do computador e ligá-la para obter uma experiência semelhante a uma cache local emulador, conforme mostrado no exemplo a seguir:

    private static Lazy<ConnectionMultiplexer>
          lazyConnection = new Lazy<ConnectionMultiplexer>
        (() =>
        {
            // Connect to a locally running instance of Redis to simulate a local cache emulator experience.
            return ConnectionMultiplexer.Connect("127.0.0.1:6379");
        });

        public static ConnectionMultiplexer Connection
        {
            get
            {
                return lazyConnection.Value;
            }
        }


Opcionalmente, pode configurar uma [Conf](https://redis.io/topics/config) ficheiro para ficarem mais parecidos com o [predefinições de cache](cache-configure.md#default-redis-server-configuration) para a sua Cache online do Azure para o Redis se assim o desejar.

<a name="cache-commands"></a>

### <a name="how-can-i-run-redis-commands"></a>Como posso executar comandos da Redis?
Pode utilizar qualquer um dos comandos listados na [comandos da Redis](https://redis.io/commands#) exceto para os comandos listados na [Redis comandos não suportados na Cache do Azure para Redis](cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis). Tem várias opções para executar comandos da Redis.

* Se tiver uma cache Standard ou Premium, pode executar comandos da Redis utilizando o [consola de Redis](cache-configure.md#redis-console). A consola do Redis fornece uma forma segura para executar comandos de Redis no portal do Azure.
* Também pode utilizar as ferramentas de linha de comandos da Redis. Para usá-los, execute os seguintes passos:
* Transfira o [Redis ferramentas da linha de comandos](https://github.com/MSOpenTech/redis/releases/).
* Ligar para o cache com `redis-cli.exe`. Passar o ponto final de cache com que o -h mudar e a chave usando - a conforme mostrado no exemplo a seguir:
* `redis-cli -h <Azure Cache for Redis name>.redis.cache.windows.net -a <key>`

> [!NOTE]
> As ferramentas de linha de comandos da Redis não funcionam com a porta SSL, mas pode usar um utilitário como `stunnel` para ligar em segurança as ferramentas para a porta SSL, seguindo as instruções no [apresentamos o ASP.NET sessão do fornecedor de estado para Redis pré-visualização Versão](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) postagem de blog.
>
>

<a name="cache-reference"></a>

### <a name="why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services"></a>Por que não a Cache de Redis do Azure tem uma referência de biblioteca de classe do MSDN, como alguns dos outros serviços do Azure?
Cache do Microsoft Azure para Redis baseia-se em abrir popular da origem de Cache do Azure para Redis e podem ser acedidos por uma grande variedade de [clientes da Redis](https://redis.io/clients) para muitas linguagens de programação. Cada cliente tem sua própria API que faz chamadas para a Cache do Azure para a utilização de instância de Redis [comandos da Redis](https://redis.io/commands).

Uma vez que cada cliente é diferente, não não uma referência de classe centralizado no MSDN, e cada cliente mantém sua própria documentação de referência. Além da documentação de referência, existem vários tutoriais que mostram como introdução à Cache do Azure para Redis que utilizam idiomas diferentes e colocar em cache os clientes. Para aceder a estes tutoriais, consulte [como utilizar a Cache do Azure para Redis](cache-dotnet-how-to-use-azure-redis-cache.md) e isso artigos colateral na tabela de conteúdos.

### <a name="can-i-use-azure-cache-for-redis-as-a-php-session-cache"></a>Posso utilizar a Cache do Azure para Redis como uma cache de sessão do PHP?
Sim, utilizar a Cache do Azure para Redis como uma cache de sessão do PHP, especifique a cadeia de ligação para a Cache do Azure para a instância de Redis no `session.save_path`.

> [!IMPORTANT]
> Ao utilizar a Cache do Azure para Redis como uma cache de sessão do PHP, tem o URL a codificar a chave de segurança utilizada para ligar à cache, conforme mostrado no exemplo a seguir:
>
> `session.save_path = "tcp://mycache.redis.cache.windows.net:6379?auth=<url encoded primary or secondary key here>";`
>
> Se a chave não é codificada com URL, poderá receber uma exceção com uma mensagem como: `Failed to parse session.save_path`
>
>

Para obter mais informações sobre como utilizar a Cache do Azure para Redis como uma cache de sessão do PHP com o cliente de PhpRedis, consulte [manipulador do PHP sessão](https://github.com/phpredis/phpredis#php-session-handler).

### <a name="what-are-redis-databases"></a>Quais são as bases de dados Redis?

Bases de dados de redis são apenas uma separação lógica dos dados dentro da mesma instância de Redis. A memória do cache é partilhada entre todas as bases de dados e consumo de um determinado banco de dados depende de chaves/valores armazenados nessa base de dados de memória real. Por exemplo, uma cache de C6 tem 53 GB de memória. Pode optar por colocar todos os 53 GB numa base de dados ou pode dividi-lo entre várias bases de dados. 

> [!NOTE]
> Quando utilizar uma Cache do Azure Premium para Redis com o clustering ativado, apenas base de dados 0 está disponível. Esta limitação é uma limitação de Redis intrínseca e não é específica para a Cache de Redis do Azure. Para obter mais informações, consulte [é necessário efetuar quaisquer alterações à minha aplicação de cliente para utilizar o clustering?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 


<a name="cache-ssl"></a>

### <a name="when-should-i-enable-the-non-ssl-port-for-connecting-to-redis"></a>Quando deve a ativar a porta não SSL para conectar a Redis?
Redis server não suporta nativamente SSL, mas não da Cache de Redis do Azure. Se estiver a ligar à Cache do Azure para Redis e o cliente suportar o SSL, como stackexchange. redis, em seguida, deve usar SSL.

>[!NOTE]
>A porta não SSL está desativada por predefinição para nova Cache do Azure para instâncias de Redis. Se o cliente não suporta SSL, tem de ativar a porta não SSL, seguindo as instruções no [acessam portas](cache-configure.md#access-ports) secção a [configurar uma cache na Cache do Azure para Redis](cache-configure.md) artigo.
>
>

Redis ferramentas, tal como `redis-cli` não funcionam com a porta SSL, mas pode usar um utilitário como `stunnel` para ligar em segurança as ferramentas para a porta SSL, seguindo as instruções no [apresentamos o estado de fornecedor de sessão ASP.NET para Redis Versão de pré-visualização](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) postagem de blog.

Para obter instruções sobre como baixar as ferramentas de Redis, consulte a [como posso executar comandos da Redis?](#cache-commands) secção.

### <a name="what-are-some-production-best-practices"></a>Quais são algumas práticas recomendadas de produção?
* [Práticas recomendadas do stackexchange. redis](#stackexchangeredis-best-practices)
* [Configuração e conceitos](#configuration-and-concepts)
* [Teste de desempenho](#performance-testing)

#### <a name="stackexchangeredis-best-practices"></a>Práticas recomendadas do stackexchange. redis
* Definir `AbortConnect` como false, em seguida, permitir que o ConnectionMultiplexer voltar a ligar automaticamente. [Veja aqui para obter detalhes](https://gist.github.com/JonCole/36ba6f60c274e89014dd#file-se-redis-setabortconnecttofalse-md).
* Reutilizar o ConnectionMultiplexer - não crie um novo para cada solicitação. O `Lazy<ConnectionMultiplexer>` padrão [mostrada aqui](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache) é recomendado.
* Redis funciona melhor com valores menores, por isso considere talhar dos dados de maiores em várias chaves. Na [essa discussão de Redis](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ), 100 kb é considerado grandes. Leia [este artigo](https://gist.github.com/JonCole/db0e90bedeb3fc4823c2#large-requestresponse-size) para um problema de exemplo que pode ser causado por valores grandes.
* Configurar o seu [definições de ThreadPool](#important-details-about-threadpool-growth) para evitar tempos limite.
* Utilize, pelo menos, o connectTimeout predefinida de 5 segundos. Este intervalo forneceria stackexchange. redis tempo suficiente para restabelecer a ligação, em caso de um blip de rede.
* Lembre-se de que os custos de desempenho associada com operações diferentes, que está a executar. Por exemplo, o `KEYS` comando é uma operação (n) e deve ser evitado. O [redis.io site](https://redis.io/commands/) tem detalhes sobre a complexidade de tempo para cada operação que suporta. Clique em cada comando para ver a complexidade para cada operação.

#### <a name="configuration-and-concepts"></a>Configuração e conceitos
* Utilize os escalões padrão ou Premium para sistemas de produção. O escalão básico é um sistema de nó único com nenhuma replicação de dados e nenhum SLA. Além disso, utilize, pelo menos, uma cache de C1. C0 caches são normalmente utilizadas para cenários de desenvolvimento/teste simples.
* Lembre-se de que o Redis é um **dentro da memória** arquivo de dados. Leia [este artigo](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) para que está atento a cenários em que pode ocorrer perda de dados.
* Desenvolver o seu sistema, de modo a que possa tratar blips de ligação [devido a aplicação de patches e de ativação pós-falha](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md).

#### <a name="performance-testing"></a>Teste de desempenho
* Iniciar com `redis-benchmark.exe` ter uma noção do débito possível antes de escrever seu próprio desempenho testa. Uma vez `redis-benchmark` não suporta SSL, deve [ativar a porta não SSL através do portal do Azure](cache-configure.md#access-ports) antes de executar o teste. Para obter exemplos, consulte [como posso efetuar testes de desempenho e testar o desempenho de minha cache?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* O cliente VM utilizada para fins de teste deve estar na mesma região que a sua Cache do Azure para a instância de Redis.
* Recomendamos que utilize série de VM de Dv2 para o seu cliente, tal como ter melhor hardware e deve fornecer os melhores resultados.
* Certifique-se de que o cliente VM que escolher tem, pelo menos, tanta computação e capacidade de largura de banda, como a cache está a testar.
* Ative VRSS no computador cliente, se estiver no Windows. [Veja aqui para obter detalhes](https://technet.microsoft.com/library/dn383582.aspx).
* Instâncias de Redis do escalão Premium têm rede melhor débito e latência porque estão em execução em hardware melhor da CPU e da rede.

<a name="cache-redis-commands"></a>

### <a name="what-are-some-of-the-considerations-when-using-common-redis-commands"></a>Quais são algumas das considerações ao utilizar comandos da Redis comuns?
* Não deve executar determinados comandos da Redis que demorar muito tempo a concluir, sem compreender o impacto desses comandos.
  * Por exemplo, não execute o [chaves](https://redis.io/commands/keys) comando na produção, como pode demorar muito tempo para retornar dependendo do número de chaves. O redis é um servidor de thread único e processa comandos um por vez. Se tiver outros comandos emitidos depois de chaves, eles não serão processados até que o Redis processa o comando de chaves. O [redis.io site](https://redis.io/commands/) tem detalhes sobre a complexidade de tempo para cada operação que suporta. Clique em cada comando para ver a complexidade para cada operação.
* Tamanhos de chave - devo usar pequenas chaves/valores ou chaves/valores grandes? Em geral, ele depende do cenário. Se o seu cenário requer chaves maiores, pode ajustar o ConnectionTimeout e repita a valores e ajustar sua lógica de repetição. Da perspectiva do servidor Redis, valores menores são respeitadas para ter um desempenho melhor.
* Estas considerações não significam que não é possível armazenar valores maiores no Redis; deve estar ciente as seguintes considerações. Latências será mais. Se tiver um conjunto de dados que é maiores e um que seja mais pequenos, pode utilizar várias instâncias de ConnectionMultiplexer, cada um configurado com um conjunto diferente de valores de tempo limite e tente novamente, conforme descrito no anterior [o que fazer o stackexchange. redis Opções de configuração fazer](#cache-configuration) secção.

<a name="cache-benchmarking"></a>

### <a name="how-can-i-benchmark-and-test-the-performance-of-my-cache"></a>Como efetuar testes de desempenho e testar o desempenho de minha cache?
* [Ativar o diagnóstico da cache](cache-how-to-monitor.md#enable-cache-diagnostics) para poder [monitorizar](cache-how-to-monitor.md) o estado de funcionamento da cache. Pode ver as métricas no portal do Azure e [transferi-las e revê-las](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) com as ferramentas da sua escolha.
* Pode utilizar redis benchmark.exe para teste de carga do servidor de Redis.
* Certifique-se de que o teste de carga cliente e a Cache do Azure para Redis estão na mesma região.
* Utilizar redis cli.exe e monitorizar a cache com o comando INFO.
* Se a sua carga está causando a fragmentação de memória elevada, deve aumentar verticalmente para um tamanho maior de cache.
* Para obter instruções sobre como baixar as ferramentas de Redis, consulte a [como posso executar comandos da Redis?](#cache-commands) secção.

Os comandos seguintes fornecem um exemplo de uso redis benchmark.exe. Para obter resultados precisos, execute estes comandos a partir de uma VM na mesma região que a cache.

* Pedidos de Pipelined definido de teste com um payload de k 1

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t SET -n 1000000 -d 1024 -P 50`
* Teste Pipelined obter pedidos com um payload de k 1.
  NOTA: Execute o teste de conjunto mostrado acima primeiro para povoar a cache

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t GET -n 1000000 -d 1024 -P 50`

<a name="threadpool"></a>

### <a name="important-details-about-threadpool-growth"></a>Detalhes importantes sobre o crescimento de ThreadPool
O ThreadPool do CLR tem dois tipos de threads - "Trabalho" e "Porta de conclusão e/s" (também conhecido como IOCP) threads.

* Threads de trabalho são utilizados para ações como o processamento da `Task.Run(…)`, ou `ThreadPool.QueueUserWorkItem(…)` métodos. Esses threads também são usados por vários componentes do CLR, quando o trabalho tem de ser efetuadas num thread em segundo plano.
* Threads IOCP são utilizados quando acontece de e/s assíncrona (por exemplo, ler a partir da rede).

O pool de segmentos fornece novos threads de trabalho ou threads de conclusão de e/s sob demanda (sem qualquer limitação) até atingir a definição de "Mínimo" para cada tipo de thread. Por predefinição, o número mínimo de threads é definido como o número de processadores num sistema.

Assim que o número de threads (ocupados) existentes chega o "minimum" número de threads, o ThreadPool irá limitar a taxa a que ele injeta novos threads para um thread por 500 milissegundos. Normalmente, se o seu sistema obtém um fluxo de trabalho que precisam de um thread IOCP, ele processa esse trabalho muito rapidamente. No entanto, se o fluxo de trabalho é mais do que a definição de "Mínimo" configurada, haverá algum atraso no processamento parte do trabalho, como o ThreadPool aguarda uma das duas coisas acontecer.

1. Um thread existente seja liberada processar o trabalho.
2. Nenhum thread existente seja liberada para 500 MS, pelo que é criado um novo thread.

Basicamente, isso significa que quando o número de threads ocupados é superior ao mínimo de threads, provavelmente pagar um atraso de 500 MS antes do tráfego de rede é processado pela aplicação. Além disso, é importante observar que, quando um thread existente permanece inativo durante mais de 15 segundos (com base no que eu me lembro), ele será limpo e este ciclo de crescimento e shrinkage pode repetir.

Se observarmos uma mensagem de erro de exemplo do stackexchange. redis (criar 1.0.450 ou posterior), verá que agora imprime as estatísticas de ThreadPool (ver detalhes IOCP e de trabalho abaixo).

    System.TimeoutException: Timeout performing GET MyKey, inst: 2, mgr: Inactive,
    queue: 6, qu: 0, qs: 6, qc: 0, wr: 0, wq: 0, in: 0, ar: 0,
    IOCP: (Busy=6,Free=994,Min=4,Max=1000),
    WORKER: (Busy=3,Free=997,Min=4,Max=1000)

No exemplo anterior, pode ver que o segmento de IOCP existem 6 threads ocupados e o sistema está configurado para permitir 4 threads mínimos. Neste caso, o cliente iria provavelmente viu dois atrasos de 500 ms porque 6 > 4.

Tenha em atenção que o stackexchange. redis alcançam tempos limite se obtém otimizado crescimento de threads IOCP ou de trabalho.

### <a name="recommendation"></a>Recomendação
Tendo em conta estas informações, é altamente recomendável que os clientes definir o valor de configuração mínima de threads de trabalho e IOCP para algo maior do que o valor predefinido. Não podemos dar único orientações sobre o que este valor deve ser porque o valor correto para um aplicativo será demasiado alta/baixa para outra aplicação. Esta definição também pode afetar o desempenho de outras partes de aplicações complicadas, pelo que cada cliente tem de ajustar esta definição para suas necessidades específicas. Um bom ponto de partida é 200 ou 300, em seguida, testar e ajustar conforme necessário.

Como configurar esta definição:

* No ASP.NET, utilize o ["minIoThreads" ou "minWorkerThreads" definição de configuração] [ "minIoThreads" configuration setting] sob o `<processModel>` elemento de configuração em Web. config. Se estiver executando dentro de Web sites do Azure, esta definição não está exposta através de opções de configuração. No entanto, ainda deve ser capaz de configurar esta definição através de programação (veja abaixo) de seu método Application_Start em global.asax.cs.

  > [!NOTE] 
  > O valor especificado neste elemento de configuração é um *por núcleo* definição. Por exemplo, se tem uma máquina 4 core e quer sua definição minIOThreads esteja 200 em tempo de execução, usaria `<processModel minIoThreads="50"/>`.
  >

* Fora do ASP.NET e global. asax de Web sites do Azure, utilize o [ThreadPool.SetMinThreads (...)](/dotnet/api/system.threading.threadpool.setminthreads#System_Threading_ThreadPool_SetMinThreads_System_Int32_System_Int32_) API.

  > [!NOTE]
  > O valor especificado por esta API é uma configuração global, afetar todo o AppDomain. Se tiver uma máquina 4 core e defina o minWorkerThreads e minIOThreads para 50 por CPU durante o tempo de execução, usaria ThreadPool.SetMinThreads (200, 200).

<a name="server-gc"></a>

### <a name="enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis"></a>Ativar o GC de servidor obter mais débito no cliente, ao utilizar o stackexchange. redis
Ativar o GC de servidor pode otimizar o cliente e fornecer um melhor desempenho e a produtividade ao utilizar o stackexchange. redis. Para obter mais informações sobre o GC do servidor e como ativá-lo, consulte os artigos seguintes:

* [Para ativar o GC de servidor](/dotnet/framework/configure-apps/file-schema/runtime/gcserver-element)
* [Noções básicas de coleta de lixo](/dotnet/standard/garbage-collection/fundamentals)
* [Coleta de lixo e desempenho](/dotnet/standard/garbage-collection/performance)


### <a name="performance-considerations-around-connections"></a>Considerações de desempenho em torno de ligações

Cada escalão de preço tem limites diferentes para ligações de cliente, memória e largura de banda. Embora cada tamanho da cache permite *até* um determinado número de ligações, cada ligação ao Redis sobrecarga associada a ele. Um exemplo de tal sobrecarga seria a utilização de CPU e memória como resultado de encriptação de TLS/SSL. O limite máximo de ligação para um tamanho de cache determinada parte do princípio de uma cache de ânimo leve carregá-lo. Se carregar de sobrecarga de ligação *plus* carga de operações do cliente excede a capacidade para o sistema, a cache pode ter problemas de capacidade, mesmo que não excedeu o limite de ligação para o tamanho atual da cache.

Para obter mais informações sobre os limites de ligações diferentes para cada camada, consulte [Cache do Azure para Redis preços](https://azure.microsoft.com/pricing/details/cache/). Para obter mais informações sobre ligações e outras configurações predefinidas, consulte [configuração do servidor predefinido Redis](cache-configure.md#default-redis-server-configuration).

<a name="cache-monitor"></a>

### <a name="how-do-i-monitor-the-health-and-performance-of-my-cache"></a>Como posso monitorizar o estado de funcionamento e desempenho da minha cache?
Cache do Microsoft Azure para instâncias de Redis pode ser monitorizada no [portal do Azure](https://portal.azure.com). Pode ver métricas, afixar gráficos de métricas ao Startboard, personalizar o intervalo de data e hora dos gráficos de monitorização, adicionar e remover as métricas de gráficos e definir alertas quando forem cumpridas determinadas condições. Para obter mais informações, consulte [Monitor Azure Cache de Redis](cache-how-to-monitor.md).

A Cache do Azure para Redis **menu de recursos** também contém várias ferramentas de monitorização e as caches de resolução de problemas.

* **Diagnosticar e resolver problemas** fornece informações sobre problemas comuns e estratégias para solucioná-los.
* **Estado de funcionamento do recurso** monitoriza o recurso e informa se está a funcionar conforme esperado. Para obter mais informações sobre o serviço de estado de funcionamento de recursos do Azure, consulte [descrição geral do Estado de funcionamento de recursos do Azure](../resource-health/resource-health-overview.md).
* **Novo pedido de suporte** fornece opções para abrir um pedido de suporte para a sua cache.

Essas ferramentas permitem-lhe monitorizar o estado de funcionamento da Cache do Azure para instâncias de Redis e ajudarão a gerenciar seus aplicativos de colocação em cache. Para obter mais informações, consulte a secção "Suporte e resolução de problemas de definições" da [como configurar a Cache do Azure para Redis](cache-configure.md).

<a name="cache-timeouts"></a>

### <a name="why-am-i-seeing-timeouts"></a>Por que estou a ver tempos limite?
Tempos limite acontece no cliente que utilizam para comunicar com o Redis. Quando um comando é enviado ao servidor Redis, o comando está na fila e o servidor Redis, eventualmente, seleciona o comando e o executa. No entanto o cliente pode tempo limite durante este processo e se ele faz uma exceção é gerado no lado do chamada. Para obter mais informações sobre como resolver problemas de tempo limite, consulte [resolução de problemas do lado do cliente](cache-how-to-troubleshoot.md#client-side-troubleshooting) e [exceções de tempo limite do stackexchange. redis](cache-how-to-troubleshoot.md#stackexchangeredis-timeout-exceptions).

<a name="cache-disconnect"></a>

### <a name="why-was-my-client-disconnected-from-the-cache"></a>Por que motivo o meu cliente foi desligado da cache?
Seguem-se alguns motivo comum para desligar uma cache.

* Faz com que o lado do cliente
  * A aplicação de cliente foi reimplementada.
  * A aplicação cliente efetuar uma operação de dimensionamento.
    * No caso dos serviços Cloud ou aplicações Web, isso pode ser devido a escala automática.
  * Alterar a camada de rede no lado do cliente.
  * Erros transitórios ocorreram no cliente ou em nós de rede entre o cliente e o servidor.
  * Os limites de limiar de largura de banda foram atingidos.
  * CPU está vinculada a operações demorou demasiado tempo a concluir.
* Faz com que o lado do servidor
  * Sobre a oferta de cache padrão, a Cache do Azure para o serviço de Redis iniciada uma ativação pós-falha de nó primário para o nó secundário.
  * Azure foi aplicação de patches a instância em que a cache foi implementada
    * Isso pode ser para atualizações do servidor Redis ou manutenção geral de VM.

### <a name="which-azure-cache-offering-is-right-for-me"></a>Qual das ofertas da Cache do Azure é melhor para mim?
> [!IMPORTANT]
> De acordo com a do ano passado [anúncio](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/), serviço de serviço de Cache gerida do Azure e Cache na função do Azure **ter sido extinguido** 30 de Novembro de 2016. A nossa recomendação é usar [a Cache de Redis do Azure](https://azure.microsoft.com/services/cache/). Para obter informações sobre a migração, consulte [migrar do serviço de Cache gerida a cache de Redis do Azure](cache-migrate-to-redis.md).
>
>

### <a name="azure-cache-for-redis"></a>Cache do Azure para Redis
A Cache de Redis do Azure está disponível em geral em tamanhos até 53 GB e tem um SLA de 99,9% de disponibilidade. A nova [escalão premium](cache-premium-tier-intro.md) oferece tamanhos até 530 GB e suporte para clustering, VNET e a persistência, com um SLA de 99,9%.

A Cache de Redis do Azure oferece aos clientes a capacidade de utilizar uma Cache de Azure segura, dedicada para Redis, gerida pela Microsoft. Com esta oferta, possa aproveitar o vasto conjunto de funcionalidades e ecossistema fornecidas pela Redis e fiável de alojamento e monitorização da Microsoft.

Ao contrário das caches tradicionais que lidam apenas com pares chave-valor, o Redis é conhecido por seu altamente eficiente os tipos de dados. Redis também suporta a execução de operações atómicas nestes tipos, como acrescentar uma cadeia de caracteres; deixa de aumentar o valor num hash; Enviar para uma lista; computação União, interseção do conjunto e diferença; ou obtendo o membro com classificação mais alta de um conjunto ordenado. Outros recursos incluem suporte para transações, pub/sub, Lua scripts, de chaves com limitado de tempo de vida e as definições de configuração para que o Redis se comportem mais como uma cache tradicional.

Outro aspecto fundamental para o sucesso de Redis é o ecossistema de bom estado de funcionamento, vibrante de código fonte aberto criado com ela. Isso é refletido ao conjunto variado de clientes Redis disponíveis em vários idiomas. Este ecossistema e uma grande variedade de clientes que a Cache do Azure para Redis a ser utilizada por praticamente qualquer carga de trabalho que iria criar no Azure.

Para obter mais informações sobre como começar a Cache do Azure para Redis, consulte [como utilizar a cache do Azure para Redis](cache-dotnet-how-to-use-azure-redis-cache.md) e [Cache do Azure para a documentação do Redis](index.md).

### <a name="managed-cache-service"></a>Serviço de Cache gerida
[Managed Cache service foi extinto a 30 de Novembro de 2016.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

Para ver documentação arquivada, consulte [arquivados documentação do serviço de Cache gerida](/previous-versions/azure/azure-services/dn386094(v=azure.100)).

### <a name="in-role-cache"></a>Cache na Função
[Cache na função foi extinto a 30 de Novembro de 2016.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

Para ver documentação arquivada, consulte [documentação da Cache na função arquivados](/previous-versions/azure/azure-services/dn386103(v=azure.100)).

["minIoThreads" configuration setting]: https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx
