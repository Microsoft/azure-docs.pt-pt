---
title: FAQ da Cache do Azure para Redis
description: Aprenda as respostas a perguntas, padrões e boas práticas comuns para Azure Cache para Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: fc19f247a9a915bfafde68f652784c42285928ca
ms.sourcegitcommit: 9bfd94307c21d5a0c08fe675b566b1f67d0c642d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2020
ms.locfileid: "84974930"
---
# <a name="azure-cache-for-redis-faq"></a>FAQ da Cache do Azure para Redis
Aprenda as respostas a perguntas, padrões e boas práticas comuns para Azure Cache para Redis.

## <a name="what-if-my-question-isnt-answered-here"></a>E se a minha pergunta não for respondida aqui?
Se a sua pergunta não estiver listada aqui, avise-nos e nós o ajudaremos a encontrar uma resposta.

* Você pode colocar uma pergunta nos comentários no final desta FAQ e envolver-se com a equipe Azure Cache e outros membros da comunidade sobre este artigo.
* Para chegar a um público mais vasto, pode publicar uma pergunta na página de perguntas do [Microsoft Q&Uma página de perguntas para Azure Cache](https://docs.microsoft.com/answers/topics/azure-cache-redis.html) e envolver-se com a equipa do Azure Cache e outros membros da comunidade.
* Se quiser fazer um pedido de funcionalidade, pode submeter os seus pedidos e ideias ao [Azure Cache para a Voz do Utilizador Redis.](https://feedback.azure.com/forums/169382-cache)
* Também pode enviar-nos um e-mail no [Azure Cache External Feedback](mailto:azurecache@microsoft.com).

## <a name="azure-cache-for-redis-basics"></a>Cache Azure para o básico de Redis
As FAQs nesta secção cobrem alguns dos fundamentos da Cache Azure para Redis.

* [O que é a Cache do Azure para Redis?](#what-is-azure-cache-for-redis)
* [Como posso começar com Azure Cache para Redis?](#how-can-i-get-started-with-azure-cache-for-redis)

As perguntas que se seguem abrangem conceitos e perguntas básicas sobre Azure Cache para Redis e são respondidas numa das outras secções de FAQ.

* [Que Azure Cache para a oferta e tamanho do Redis devo usar?](#what-azure-cache-for-redis-offering-and-size-should-i-use)
* [Que Azure Cache para clientes Redis posso usar?](#what-azure-cache-for-redis-clients-can-i-use)
* [Há algum emulador local para o Azure Cache para o Redis?](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [Como monitorizo a saúde e o desempenho da minha cache?](#how-do-i-monitor-the-health-and-performance-of-my-cache)

## <a name="planning-faqs"></a>Planeamento de Perguntas Frequentes
* [Que Azure Cache para a oferta e tamanho do Redis devo usar?](#what-azure-cache-for-redis-offering-and-size-should-i-use)
* [Azure Cache para o desempenho de Redis](#azure-cache-for-redis-performance)
* [Em que região devo localizar o meu cache?](#in-what-region-should-i-locate-my-cache)
* [Como vou cobrar por Azure Cache para Redis?](#how-am-i-billed-for-azure-cache-for-redis)
* [Posso usar a Azure Cache para Redis com Azure Government Cloud, Azure China Cloud ou Microsoft Azure Germany?](#can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany)

## <a name="development-faqs"></a>Perguntas frequentes de desenvolvimento
* [O que fazem as opções de configuração StackExchange.Redis?](#what-do-the-stackexchangeredis-configuration-options-do)
* [Que Azure Cache para clientes Redis posso usar?](#what-azure-cache-for-redis-clients-can-i-use)
* [Há algum emulador local para o Azure Cache para o Redis?](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [Como posso comandar o Redis?](#how-can-i-run-redis-commands)
* [Porque é que o Azure Cache para a Redis não tem uma referência à biblioteca da classe MSDN como alguns dos outros serviços da Azure?](#why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services)
* [Posso usar a Cache Azure para redis como cache de sessão PHP?](#can-i-use-azure-cache-for-redis-as-a-php-session-cache)
* [O que são bases de dados do Redis?](#what-are-redis-databases)

## <a name="security-faqs"></a>Perguntas de Segurança
* [Quando devo ativar a porta não-TLS/SSL para a ligação ao Redis?](#when-should-i-enable-the-non-tlsssl-port-for-connecting-to-redis)

## <a name="production-faqs"></a>FaQs de produção
* [Quais são as melhores práticas de produção?](#what-are-some-production-best-practices)
* [Quais são algumas das considerações ao usar comandos redis comuns?](#what-are-some-of-the-considerations-when-using-common-redis-commands)
* [Como posso comparar e testar o desempenho da minha cache?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [Detalhes importantes sobre o crescimento da ThreadPool](#important-details-about-threadpool-growth)
* [Ative o servidor GC para obter mais produção sobre o cliente ao utilizar o StackExchange.Redis](#enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis)
* [Considerações de desempenho em torno de ligações](#performance-considerations-around-connections)

## <a name="monitoring-and-troubleshooting-faqs"></a>Monitorização e resolução de problemas das FAQ
As PERGUNTAS Frequentes nesta secção abrangem questões comuns de monitorização e resolução de problemas. Para obter mais informações sobre a monitorização e resolução de problemas da sua Cache Azure para instâncias Redis, consulte [Como monitorizar a Cache Azure para Redis](cache-how-to-monitor.md) e os vários guias de resolução de problemas.

* [Como monitorizo a saúde e o desempenho da minha cache?](#how-do-i-monitor-the-health-and-performance-of-my-cache)
* [Por que estou a ver intervalos?](#why-am-i-seeing-timeouts)
* [Porque é que o meu cliente foi desligado da cache?](#why-was-my-client-disconnected-from-the-cache)

## <a name="prior-cache-offering-faqs"></a>Cache Prior oferecendo FAQs
* [Qual oferta de Azure Cache é a certa para mim?](#which-azure-cache-offering-is-right-for-me)

### <a name="what-is-azure-cache-for-redis"></a>O que é a Cache do Azure para Redis?
A azure Cache for Redis baseia-se no popular software de código aberto [Redis](https://redis.io/). Dá-lhe acesso a um Azure Cache seguro e dedicado para Redis, gerido pela Microsoft, e acessível a partir de qualquer aplicação dentro do Azure. Para obter uma visão geral mais detalhada, consulte a página de produto [Azure Cache for Redis](https://azure.microsoft.com/services/cache/) no Azure.com.

### <a name="how-can-i-get-started-with-azure-cache-for-redis"></a>Como posso começar com Azure Cache para Redis?
Há várias maneiras de começar com Azure Cache para Redis.

* Pode consultar um dos nossos tutoriais disponíveis para [.NET,](cache-dotnet-how-to-use-azure-redis-cache.md) [ASP.NET,](cache-web-app-howto.md) [Java,](cache-java-get-started.md) [Node.js](cache-nodejs-get-started.md)e [Python.](cache-python-get-started.md)
* Pode ver [Como Construir Aplicações de Alto Desempenho utilizando o Microsoft Azure Cache para Redis](https://azure.microsoft.com/documentation/videos/how-to-build-high-performance-apps-using-microsoft-azure-cache/).
* Pode consultar a documentação do cliente para os clientes que correspondam à linguagem de desenvolvimento do seu projeto para ver como usar o Redis. Há muitos clientes Redis que podem ser usados com Azure Cache para Redis. Para uma lista de clientes redis, [https://redis.io/clients](https://redis.io/clients) veja.

Se ainda não tem uma conta Azure, pode:

* [Criar uma conta do Azure gratuitamente](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero). Recebe créditos que podem ser utilizados para experimentar os serviços pagos do Azure. Mesmo depois de gastar todos os créditos, pode manter a conta e utilizar os serviços e recursos gratuitos do Azure.
* [Ativar os benefícios de subscritor do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). A sua subscrição do MSDN dá-lhe créditos todos os meses que pode utilizar em serviços pagos do Azure.

<a name="cache-size"></a>

### <a name="what-azure-cache-for-redis-offering-and-size-should-i-use"></a>Que Azure Cache para a oferta e tamanho do Redis devo usar?
Cada Cache Azure para redis oferece diferentes níveis de **tamanho,** largura de **banda,** **alta disponibilidade**e opções **SLA.**

Seguem-se considerações para a escolha de uma oferta cache.

* **Memória**: Os níveis Básico e Standard oferecem 250 MB – 53 GB. O nível Premium oferece até 1,2 TB (como cluster) ou 120 GB (não agrupados). Para mais informações, consulte [a Cache Azure para redis pricing](https://azure.microsoft.com/pricing/details/cache/).
* **Desempenho da rede**: Se tiver uma carga de trabalho que exija uma elevada produção, o nível Premium oferece mais largura de banda em comparação com Standard ou Basic. Também dentro de cada nível, caches de tamanho maior têm mais largura de banda devido ao VM subjacente que hospeda a cache. Para mais informações, consulte a [tabela seguinte.](#cache-performance)
* **Produção**: O nível Premium oferece o máximo de produção disponível. Se o servidor de cache ou o cliente atingir os limites de largura de banda, poderá receber intervalos de tempo do lado do cliente. Para mais informações, consulte a tabela a seguir.
* **Alta Disponibilidade/SLA**: Azure Cache for Redis garante que uma cache Standard/Premium está disponível pelo menos 99,9% das vezes. Para saber mais sobre o nosso SLA, consulte [Azure Cache para Redis Pricing.](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) O SLA cobre apenas a conectividade com os pontos finais cache. O SLA não cobre a proteção contra a perda de dados. Recomendamos a utilização da funcionalidade de persistência de dados Redis no nível Premium para aumentar a resiliência contra a perda de dados.
* **Retenção de Dados Redis**: O nível Premium permite-lhe persistir os dados da cache numa conta de Armazenamento Azure. Numa cache Basic/Standard, todos os dados são armazenados apenas na memória. As questões subjacentes às infraestruturas podem resultar em potenciais perdas de dados. Recomendamos a utilização da funcionalidade de persistência de dados Redis no nível Premium para aumentar a resiliência contra a perda de dados. A Azure Cache para Redis oferece opções RDB e AOF (pré-visualização) na persistência de Redis. Para obter mais informações, consulte [Como configurar a persistência para uma Cache Premium Azure para Redis](cache-how-to-premium-persistence.md).
* **Redis Cluster**: Para criar caches maiores que 120 GB, ou para obter dados de fragmentos em vários nós Redis, pode utilizar o clustering Redis, que está disponível no nível Premium. Cada nó consiste num par de cache primário/réplica para uma elevada disponibilidade. Para obter mais informações, consulte [Como configurar o agrupamento para uma Cache Premium Azure para Redis.](cache-how-to-premium-clustering.md)
* **Segurança reforçada e isolamento de rede**: A implantação da Rede Virtual Azure (VNET) proporciona uma maior segurança e isolamento para o seu Azure Cache para Redis, bem como sub-redes, políticas de controlo de acesso e outras funcionalidades para restringir ainda mais o acesso. Para obter mais informações, consulte [Como configurar o suporte da Rede Virtual para uma Cache Premium Azure para Redis.](cache-how-to-premium-vnet.md)
* **Configure Redis**: Nos níveis Standard e Premium, pode configurar redis para notificações keyspace.
* **Número máximo de ligações**ao cliente : O nível Premium oferece o número máximo de clientes que podem ligar-se ao Redis, com um maior número de ligações para caches de maior dimensão. O agrupamento não aumenta o número de ligações disponíveis para uma cache agrupada. Para mais informações, consulte [a Cache Azure para obter preços de Redis](https://azure.microsoft.com/pricing/details/cache/).
* **Núcleo Dedicado para Redis Server**: No nível Premium, todos os tamanhos de cache têm um núcleo dedicado para Redis. Nos níveis Básico/Standard, o tamanho C1 e acima têm um núcleo dedicado para o servidor Redis.
* **O Redis é de rosca única,** pelo que ter mais de dois núcleos não proporciona benefícios adicionais em ter apenas dois núcleos, mas tamanhos de VM maiores normalmente têm mais largura de banda do que tamanhos menores. Se o servidor de cache ou o cliente atingir os limites de largura de banda, então você recebe intervalos de tempo do lado do cliente.
* **Melhorias de desempenho**: Caches no nível Premium são implantados em hardware que tem processadores mais rápidos, proporcionando um melhor desempenho em comparação com o nível Básico ou Standard. Caches de nível premium têm maior produção e latências mais baixas.

<a name="cache-performance"></a>

### <a name="azure-cache-for-redis-performance"></a>Azure Cache para o desempenho de Redis
A tabela seguinte mostra os valores máximos de largura de banda observados durante o ensaio de vários tamanhos de caches Standard e Premium utilizando `redis-benchmark.exe` de um IaaS VM contra a Cache Azure para o ponto final redis. Para a produção de TLS, o redis-benchmark é usado com atordoado para ligar à Cache Azure para o ponto final redis.

>[!NOTE] 
>Estes valores não estão garantidos e não há SLA para estes números, mas devem ser típicos. Deve carregar a sua própria aplicação para determinar o tamanho certo da cache para a sua aplicação.
>Estes números podem mudar à medida que publicamos resultados mais recentes periodicamente.
>

A partir desta tabela, podemos tirar as seguintes conclusões:

* A produção para os caches que têm o mesmo tamanho é maior no nível Premium em comparação com o nível Standard. Por exemplo, com uma Cache de 6 GB, a produção de P1 é de 180.000 pedidos por segundo (RPS) em comparação com 100.000 RPS para C3.
* Com o agrupamento Redis, a produção aumenta linearmente à medida que aumenta o número de fragmentos (nós) no cluster. Por exemplo, se criar um cluster P4 de 10 fragmentos, então a produção disponível é de 400.000 * 10 = 4 milhões de RPS.
* A produção para tamanhos-chave maiores é maior no nível Premium em comparação com o Standard Tier.

| Escalão de preço | Tamanho | Núcleos de CPU | Largura de banda disponível | Tamanho do valor de 1 KB | Tamanho do valor de 1 KB |
| --- | --- | --- | --- | --- | --- |
| **Tamanhos padrão de cache** | | |**Megabits por seg (Mb/s) / Megabytes por seg (MB/s)** |**Pedidos por segundo (RPS) Non-SSL** |**Pedidos por segundo (RPS) SSL** |
| CO | 250 MB | Partilhado | 100 / 12.5  |  15 000 |   7.500 |
| C1 |   1 GB | 1      | 500 / 62.5  |  38,000 |  20,720 |
| C2 | 2,5 GB | 2      | 500 / 62.5  |  41,000 |  37,000 |
| C3 |   6 GB | 4      | 1000 / 125  | 100.000 |  90.000 |
| C4 |  13 GB | 2      | 500 / 62.5  |  60 000 |  55,000 |
| C5 |  26 GB | 4      | 1,000 / 125 | 102,000 |  93,000 |
| C6 |  53 GB | 8      | 2,000 / 250 | 126,000 | 120,000 |
| **Tamanhos de cache premium** | |**Núcleos de CPU por fragmento** | **Megabits por seg (Mb/s) / Megabytes por seg (MB/s)** |**Pedidos por segundo (RPS) Não-SSL, por fragmento** |**Pedidos por segundo (RPS) SSL, por fragmento** |
| P1 |   6 GB |  2 | 1,500 / 187.5 | 180,000 | 172,000 |
| P2 |  13 GB |  4 | 3,000 / 375   | 350,000 | 341,000 |
| P3 |  26 GB |  4 | 3,000 / 375   | 350,000 | 341,000 |
| P4 |  53 GB |  8 | 6,000 / 750   | 400,000 | 373,000 |
| P5 | 120 GB | 20 | 6,000 / 750   | 400,000 | 373,000 |

Para obter instruções sobre a configuração ou o descarregamento das ferramentas Redis, `redis-benchmark.exe` como, ver como [posso executar os comandos Redis?](#cache-commands)

<a name="cache-region"></a>

### <a name="in-what-region-should-i-locate-my-cache"></a>Em que região devo localizar o meu cache?
Para melhor desempenho e menor latência, localize o seu Azure Cache para Redis na mesma região que a sua aplicação de cliente cache.

<a name="cache-billing"></a>

### <a name="how-am-i-billed-for-azure-cache-for-redis"></a>Como vou cobrar por Azure Cache para Redis?
Azure Cache para o preço do Redis está [aqui.](https://azure.microsoft.com/pricing/details/cache/) A página de preços lista os preços como uma taxa horária. Os caches são faturados por minuto a partir do momento em que a cache é criada até ao momento em que uma cache é eliminada. Não há opção para parar ou fazer uma pausa na faturação de uma cache.

### <a name="can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany"></a>Posso usar a Azure Cache para Redis com Azure Government Cloud, Azure China Cloud ou Microsoft Azure Germany?
Sim, Azure Cache para Redis está disponível em Azure Government Cloud, Azure China 21Vianet Cloud e Microsoft Azure Germany. Os URLs para aceder e gerir a Cache Azure para Redis são diferentes nestas nuvens em comparação com a Nuvem Pública de Azure.

| Cloud   | Sufixo dns para Redis            |
|---------|---------------------------------|
| Público  | *.redis.cache.windows.net       |
| US Gov  | *.redis.cache.usgovcloudapi.net |
| Alemanha | *.redis.cache.cloudapi.de       |
| China   | *.redis.cache.chinacloudapi.cn  |

Para obter mais informações sobre considerações ao utilizar a Cache Azure para Redis com outras nuvens, consulte os seguintes links.

- [Bases de Dados do Governo de Azure - Azure Cache para Redis](../azure-government/documentation-government-services-database.md#azure-cache-for-redis)
- [Azure China 21Vianet Cloud - Cache Azure para Redis](https://www.azure.cn/home/features/redis-cache/)
- [Microsoft Azure Alemanha](https://azure.microsoft.com/overview/clouds/germany/)

Para obter informações sobre a utilização do Azure Cache para Redis com PowerShell em Azure Government Cloud, Azure China 21Vianet Cloud e Microsoft Azure Germany, consulte [Como ligar-se a outras nuvens - Azure Cache para Redis PowerShell](cache-how-to-manage-redis-cache-powershell.md#how-to-connect-to-other-clouds).

<a name="cache-configuration"></a>

### <a name="what-do-the-stackexchangeredis-configuration-options-do"></a>O que fazem as opções de configuração StackExchange.Redis?
StackExchange.Redis tem muitas opções. Esta secção fala sobre algumas das configurações comuns. Para obter informações mais detalhadas sobre as opções StackExchange.Redis, consulte a [configuração StackExchange.Redis](https://stackexchange.github.io/StackExchange.Redis/Configuration).

| ConfigurationOptions | Description | Recomendação |
| --- | --- | --- |
| AbortOnConnectFail |Quando definido como verdadeiro, a ligação não se reconectará após uma falha de rede. |Definido como falso e deixe o StackExchange.Redis reconectar-se automaticamente. |
| ConnectRetry |O número de vezes para repetir tentativas de ligação durante a ligação inicial. |Consulte as seguintes notas para obter orientação. |
| ConnectTimeout |Tempo limite na Ms para operações de ligação. |Consulte as seguintes notas para obter orientação. |

Normalmente, os valores padrão do cliente são suficientes. Pode afinar as opções com base na sua carga de trabalho.

* **Tentativas**
  * Para o ConnectRetry e o ConnectTimeout, a orientação geral é falhar rapidamente e voltar a tentar. Esta orientação baseia-se na sua carga de trabalho e no tempo que, em média, leva para o seu cliente emitir um comando Redis e receber uma resposta.
  * Deixe o StackExchange.Redis reconectar-se automaticamente em vez de verificar o estado da ligação e voltar a ligar-se. **Evite utilizar a propriedade ConnectionMultiplexer.IsConnected**.
  * Bola de Neve - às vezes pode encontrar-se com um problema em que está a tentar e as tentativas de bola de neve e nunca se recupera. Se ocorrer bola de neve, deve considerar a utilização de um algoritmo de recuo exponencial, conforme descrito na [orientação geral da Retry](../best-practices-retry-general.md) publicada pelo grupo Microsoft Patterns & Practices.
  
* **Valores de tempo limite**
  * Considere a sua carga de trabalho e desa um valor definido. Se estiver a armazenar grandes valores, desa um prazo de tempo superior.
  * Definido `AbortOnConnectFail` como falso e deixe StackExchange.Redis reconectar-se para si.
  * Utilize uma única instância ConnectionMultiplexer para a aplicação. Pode utilizar um LazyConnection para criar uma única instância que seja devolvida por uma propriedade Connection, como mostrado em [Connect à cache utilizando a classe ConnectionMultiplexer](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).
  * Desaprote a `ConnectionMultiplexer.ClientName` propriedade para um nome único de instância de aplicação para fins de diagnóstico.
  * Utilize `ConnectionMultiplexer` várias instâncias para cargas de trabalho personalizadas.
      * Pode seguir este modelo se tiver carga variada na sua aplicação. Por exemplo:
      * Pode ter um multiplexer para lidar com chaves grandes.
      * Pode ter um multiplexer para lidar com teclas pequenas.
      * Pode definir valores diferentes para intervalos de tempo de ligação e tentar a lógica de cada ConnectionMultiplexer que utilizar.
      * Desloge a `ClientName` propriedade em cada multiplexer para ajudar no diagnóstico.
      * Esta orientação pode levar a uma latência mais simplificada por `ConnectionMultiplexer` .

### <a name="what-azure-cache-for-redis-clients-can-i-use"></a>Que Azure Cache para clientes Redis posso usar?
Uma das grandes coisas sobre o Redis é que há muitos clientes que apoiam muitas línguas de desenvolvimento diferentes. Para obter uma lista atual de clientes, consulte os [clientes redis.](https://redis.io/clients) Para tutoriais que abrangem vários idiomas e clientes diferentes, veja [Como usar a Cache Azure para Redis](cache-dotnet-how-to-use-azure-redis-cache.md) e os seus artigos de irmãos na tabela de conteúdos.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

<a name="cache-emulator"></a>

### <a name="is-there-a-local-emulator-for-azure-cache-for-redis"></a>Há algum emulador local para o Azure Cache para o Redis?
Não existe nenhum emulador local para a Azure Cache para Redis, mas pode executar a versão MSOpenTech de redis-server.exe a partir das ferramentas da [linha de comando Redis](https://github.com/MSOpenTech/redis/releases/) na sua máquina local e conectá-la para obter uma experiência semelhante a um emulador de cache local, como mostra o seguinte exemplo:

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


Pode configurar opcionalmente um ficheiro [redis.conf](https://redis.io/topics/config) para combinar mais de perto as [definições de cache padrão](cache-configure.md#default-redis-server-configuration) para o seu Azure Cache on-line para Redis, se desejar.

<a name="cache-commands"></a>

### <a name="how-can-i-run-redis-commands"></a>Como posso comandar o Redis?
Pode utilizar qualquer um dos comandos listados nos [comandos Redis,](https://redis.io/commands#) exceto os comandos listados nos [comandos Redis não suportados em Cache Azure para Redis](cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis). Tem várias opções para executar comandos Redis.

* Se tiver uma cache Standard ou Premium, pode executar comandos Redis utilizando a [Consola Redis.](cache-configure.md#redis-console) A consola Redis fornece uma forma segura de executar comandos Redis no portal Azure.
* Também pode utilizar as ferramentas da linha de comando Redis. Para usá-los, execute os seguintes passos:
* Descarregue as [ferramentas da linha de comando Redis.](https://github.com/MSOpenTech/redis/releases/)
* Ligue-se à cache utilizando `redis-cli.exe` . Passe no ponto final da cache utilizando o interruptor -h e a chave utilizando -a como mostrado no exemplo seguinte:
* `redis-cli -h <Azure Cache for Redis name>.redis.cache.windows.net -a <key>`

> [!NOTE]
> As ferramentas da linha de comando Redis não funcionam com a porta TLS, mas pode utilizar um utilitário de modo `stunnel` a ligar as ferramentas de forma segura à porta TLS seguindo as instruções da [ferramenta de linha de comando Redis com a Cache Azure para](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-redis-cli-tool) o artigo Redis.
>
>

<a name="cache-reference"></a>

### <a name="why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services"></a>Porque é que o Azure Cache para a Redis não tem uma referência à biblioteca da classe MSDN como alguns dos outros serviços da Azure?
O Microsoft Azure Cache para Redis baseia-se no popular cache Azure para Redis. Pode ser acedido por uma grande variedade de [clientes Redis](https://redis.io/clients) para muitas linguagens de programação. Cada cliente tem a sua própria API que faz chamadas para a Cache Azure para a instância Redis usando [comandos Redis](https://redis.io/commands).

Como cada cliente é diferente, não há uma referência de classe centralizada na MSDN, e cada cliente mantém a sua própria documentação de referência. Além da documentação de referência, existem vários tutoriais que mostram como começar com a Azure Cache para Redis usando diferentes idiomas e clientes cache. Para aceder a estes tutoriais, veja [Como usar a Cache Azure para Redis](cache-dotnet-how-to-use-azure-redis-cache.md) e os seus artigos de irmãos na tabela de conteúdos.

### <a name="can-i-use-azure-cache-for-redis-as-a-php-session-cache"></a>Posso usar a Cache Azure para redis como cache de sessão PHP?
Sim, para utilizar a Cache Azure para Redis como cache de sessão PHP, especifique a cadeia de ligação da sua Cache Azure para a instância Redis em `session.save_path` .

> [!IMPORTANT]
> Ao utilizar a Cache Azure para Redis como cache de sessão PHP, deve codificar a chave de segurança utilizada para ligar à cache, como mostra o seguinte exemplo:
>
> `session.save_path = "tcp://mycache.redis.cache.windows.net:6379?auth=<url encoded primary or secondary key here>";`
>
> Se a chave não estiver codificada por URL, poderá receber uma exceção com uma mensagem como:`Failed to parse session.save_path`
>
>

Para obter mais informações sobre a utilização do Azure Cache para Redis como cache de sessão PHP com o cliente PhpRedis, consulte o [manipulador php Session](https://github.com/phpredis/phpredis#php-session-handler).

### <a name="what-are-redis-databases"></a>O que são bases de dados do Redis?

As bases de dados redis são apenas uma separação lógica de dados dentro do mesmo caso Redis. A memória do cache é partilhada entre todas as bases de dados e o consumo real de memória de uma determinada base de dados depende das chaves/valores armazenados nessa base de dados. Por exemplo, uma cache C6 tem 53 GB de memória, e um P5 tem 120 GB. Pode optar por colocar todos os 53 GB / 120 GB numa base de dados ou pode dividi-lo entre várias bases de dados. 

> [!NOTE]
> Ao utilizar uma Cache Premium Azure para Redis com clustering ativado, apenas a base de dados 0 está disponível. Esta limitação é uma limitação intrínseca do Redis e não é específica para Azure Cache para Redis. Para mais informações, consulte [Preciso de fazer alterações à aplicação do meu cliente para utilizar o clustering?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 


<a name="cache-ssl"></a>

### <a name="when-should-i-enable-the-non-tlsssl-port-for-connecting-to-redis"></a>Quando devo ativar a porta não-TLS/SSL para a ligação ao Redis?
O servidor Redis não suporta o TLS de forma nativa, mas o Azure Cache para Redis tem. Se estiver a ligar-se ao Azure Cache para Redis e o seu cliente apoiar o TLS, como o StackExchange.Redis, então deve utilizar o TLS.

>[!NOTE]
>A porta não-TLS é desativada por padrão para a nova Cache Azure para instâncias Redis. Se o seu cliente não suporta TLS, então deve ativar a porta não-TLS seguindo as instruções na secção portas [de acesso](cache-configure.md#access-ports) do [Configure uma cache em Cache Azure para artigo redis.](cache-configure.md)
>
>

As ferramentas vermelhas como `redis-cli` não funcionam com a porta TLS, mas pode utilizar um utilitário de modo a ligar as `stunnel` ferramentas de forma segura à porta TLS seguindo as instruções no anunciado ASP.NET sessão do fornecedor de estado para o post [de pré-visualização do Redis.](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)

Para obter instruções sobre o download das ferramentas Redis, veja como posso executar os [comandos Redis?](#cache-commands)

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
* Ativar o VRSS na máquina do cliente se estiver no Windows. [Consulte aqui para mais detalhes.](https://technet.microsoft.com/library/dn383582.aspx)
* As instâncias de nível premium Redis têm melhor latência e produção de rede porque estão a executar hardware melhor tanto para CPU como para a Rede.

<a name="cache-redis-commands"></a>

### <a name="what-are-some-of-the-considerations-when-using-common-redis-commands"></a>Quais são algumas das considerações ao usar comandos redis comuns?

* Evite usar certos comandos Redis que demoram muito tempo a ser concluídos, a menos que compreenda completamente o impacto destes comandos. Por exemplo, não executar o comando [KEYS](https://redis.io/commands/keys) em produção. Dependendo do número de chaves, pode levar muito tempo para voltar. Redis é um servidor de uma só linha e processa comandos um de cada vez. Se tiver outros comandos emitidos após o KEYS, estes não serão processados até que o Redis processe o comando KEYS. O [site redis.io](https://redis.io/commands/) tem detalhes sobre a complexidade do tempo para cada operação que suporta. Clique em cada comando para ver a complexidade de cada operação.
* Tamanhos das chaves - devo utilizar pequenas chaves/valores ou grandes chaves/valores? Depende do cenário. Se o seu cenário necessitar de teclas maiores, pode ajustar o ConnectionTimeout, depois tentar os valores e ajustar a sua lógica de repetição. Do ponto de vista do servidor Redis, valores mais pequenos dão um melhor desempenho.
* Estas considerações não significam que não se possa armazenar valores maiores em Redis; deve estar ciente das seguintes considerações. As latências serão mais altas. Se tiver um conjunto de dados que seja maior e um menor, pode utilizar várias instâncias ConnectionMultiplexer, cada uma configurada com um conjunto diferente de valores de tempo e retripamento, conforme descrito no anterior O que fazem as opções de [configuração StackExchange.Redis fazem secção.](#cache-configuration)

<a name="cache-benchmarking"></a>

### <a name="how-can-i-benchmark-and-test-the-performance-of-my-cache"></a>Como posso comparar e testar o desempenho da minha cache?
* [Ativar o diagnóstico da cache](cache-how-to-monitor.md#enable-cache-diagnostics) para poder [monitorizar](cache-how-to-monitor.md) o estado de funcionamento da cache. Pode ver as métricas no portal Azure e também pode [descarregá-las e revê-las](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) utilizando as ferramentas à sua escolha.
* Pode utilizar redis-benchmark.exe para carregar o seu servidor Redis.
* Certifique-se de que o cliente de teste de carga e o Cache Azure para Redis estão na mesma região.
* Utilize redis-cli.exe e monitorize a cache utilizando o comando INFO.
* Se a sua carga estiver a causar uma grande fragmentação de memória, deverá escalar até um tamanho de cache maior.
* Para obter instruções sobre o download das ferramentas Redis, veja como posso executar os [comandos Redis?](#cache-commands)

Os seguintes comandos fornecem um exemplo de utilização redis-benchmark.exe. Para obter resultados precisos, execute estes comandos a partir de um VM na mesma região que o seu cache.

* Teste pipelined set solicita usando uma carga útil de 1k

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t SET -n 1000000 -d 1024 -P 50`
* Teste pipelined GET solicita usando uma carga útil de 1k.
  NOTA: Executar o teste SET apresentado acima primeiro para preencher cache

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t GET -n 1000000 -d 1024 -P 50`

<a name="threadpool"></a>

### <a name="important-details-about-threadpool-growth"></a>Detalhes importantes sobre o crescimento da ThreadPool
O CLR ThreadPool tem dois tipos de fios - fios "Worker" e "I/O Completion Port" (IOCP).

* Os fios dos trabalhadores são usados para coisas como processar o `Task.Run(…)` , ou `ThreadPool.QueueUserWorkItem(…)` métodos. Estes fios também são utilizados por vários componentes no CLR quando o trabalho tem de acontecer num fio de fundo.
* Os fios do IOCP são utilizados quando acontece iO assíncronos, como quando lê a partir da rede.

A piscina de roscas fornece novos fios de trabalho ou fios de conclusão de E/S a pedido (sem qualquer estrangulamento) até atingir a definição "Mínimo" para cada tipo de fio. Por predefinição, o número mínimo de fios é definido para o número de processadores de um sistema.

Uma vez que o número de fios existentes (ocupados) atinja o número "mínimo" de fios, o ThreadPool acelerará a velocidade a que injeta novos fios num fio por 500 milissegundos. Normalmente, se o seu sistema tiver uma explosão de trabalho a precisar de um fio IOCP, irá processar isso rapidamente. No entanto, se a explosão de trabalho for mais do que a configuração "Mínima" configurada, haverá algum atraso no processamento de parte do trabalho, uma vez que o ThreadPool espera que uma de duas coisas aconteça.

1. Um fio existente torna-se livre para processar o trabalho.
2. Nenhum fio existente torna-se livre por 500 ms, por isso é criado um novo fio.

Basicamente, significa que quando o número de threads Busy é maior do que os threads Min, você provavelmente está pagando um atraso de 500 ms antes do tráfego de rede ser processado pela aplicação. Além disso, é importante notar que quando um fio existente permanece inativo por mais de 15 segundos (com base no que me lembro), será limpo e este ciclo de crescimento e encolhimento pode repetir-se.

Se olharmos para uma mensagem de erro de exemplo do StackExchange.Redis (construa 1.0.450 ou mais tarde), verá que agora imprime as estatísticas da ThreadPool (ver detalhes do IOCP e do WORKER abaixo).

    System.TimeoutException: Timeout performing GET MyKey, inst: 2, mgr: Inactive,
    queue: 6, qu: 0, qs: 6, qc: 0, wr: 0, wq: 0, in: 0, ar: 0,
    IOCP: (Busy=6,Free=994,Min=4,Max=1000),
    WORKER: (Busy=3,Free=997,Min=4,Max=1000)

No exemplo anterior, pode ver-se que para o fio DO IOCP existem seis fios ocupados e o sistema está configurado para permitir quatro fios mínimos. Neste caso, o cliente provavelmente teria visto dois atrasos de 500 ms, porque 6 > 4.

Note que StackExchange.Redis pode atingir intervalos se o crescimento de fios DE IOCP ou WORKER for acelerado.

### <a name="recommendation"></a>Recomendação

Tendo em conta estas informações, recomendamos vivamente que os clientes estabeleçam o valor mínimo de configuração para fios IOCP e WORKER para algo maior do que o valor padrão. Não podemos dar orientações de tamanho único sobre o que deve ser este valor, porque o valor certo para uma aplicação será provavelmente demasiado alto ou baixo para outra aplicação. Esta definição também pode ter impacto no desempenho de outras partes de aplicações complicadas, pelo que cada cliente precisa de afinar esta definição às suas necessidades específicas. Um bom ponto de partida é 200 ou 300, depois testar e ajustar conforme necessário.

Como configurar este cenário:

* Recomendamos alterar esta definição programáticamente utilizando o método [ThreadPool.SetMinThreads (...)](/dotnet/api/system.threading.threadpool.setminthreads#System_Threading_ThreadPool_SetMinThreads_System_Int32_System_Int32_) em `global.asax.cs` . Por exemplo:

```cs
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

* Também é possível especificar a definição de fios mínimos utilizando a [definição de configuração *minIoThreads* ou *minWorkerThreads* ](https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx) sob o `<processModel>` elemento de configuração em `Machine.config` , normalmente localizado em `%SystemRoot%\Microsoft.NET\Framework\[versionNumber]\CONFIG\` . **A definição do número de fios mínimos desta forma não é geralmente recomendada, porque é uma definição de todo o sistema.**

  > [!NOTE]
  > O valor especificado neste elemento de configuração é uma definição *por núcleo.* Por exemplo, se tiver uma máquina de 4 núcleos e quiser que a sua definição *de minIoThreads* seja de 200 em tempo de execução, utilizará `<processModel minIoThreads="50"/>` .
  >

<a name="server-gc"></a>

### <a name="enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis"></a>Ative o servidor GC para obter mais produção sobre o cliente ao utilizar o StackExchange.Redis
Ativar o servidor GC pode otimizar o cliente e proporcionar um melhor desempenho e produção ao utilizar o StackExchange.Redis. Para obter mais informações sobre o servidor GC e como o habilitar, consulte os seguintes artigos:

* [Para ativar o servidor GC](/dotnet/framework/configure-apps/file-schema/runtime/gcserver-element)
* [Fundamentos da Coleção de Lixo](/dotnet/standard/garbage-collection/fundamentals)
* [Recolha e Performance do Lixo](/dotnet/standard/garbage-collection/performance)


### <a name="performance-considerations-around-connections"></a>Considerações de desempenho em torno de ligações

Cada nível de preços tem limites diferentes para ligações ao cliente, memória e largura de banda. Embora cada tamanho de cache permita *até* um certo número de ligações, cada ligação ao Redis tem sobrecarga associada a ele. Um exemplo de tal sobrecarga seria o uso de CPU e memória como resultado da encriptação TLS/SSL. O limite máximo de ligação para um dado tamanho de cache pressupõe uma cache levemente carregada. Se a carga da sobrecarga de ligação *mais* a carga das operações do cliente exceder a capacidade do sistema, a cache pode experimentar problemas de capacidade mesmo que não tenha excedido o limite de ligação para o tamanho atual da cache.

Para obter mais informações sobre os diferentes limites de ligações para cada nível, consulte [Azure Cache para preços de Redis](https://azure.microsoft.com/pricing/details/cache/). Para obter mais informações sobre ligações e outras configurações predefinicionam, consulte [a configuração do servidor Redis padrão](cache-configure.md#default-redis-server-configuration).

<a name="cache-monitor"></a>

### <a name="how-do-i-monitor-the-health-and-performance-of-my-cache"></a>Como monitorizo a saúde e o desempenho da minha cache?
O Microsoft Azure Cache para instâncias Redis pode ser monitorizado no [portal Azure](https://portal.azure.com). Pode ver métricas, gráficos de métricas de pin para o Startboard, personalizar a data e a gama de horários dos gráficos de monitorização, adicionar e remover métricas dos gráficos e definir alertas quando certas condições são satisfeitas. Para obter mais informações, consulte [o Monitor Azure Cache para Redis](cache-how-to-monitor.md).

O menu Azure Cache for Redis **Resource** também contém várias ferramentas para monitorizar e resolver problemas nas suas caches.

* **Diagnosticar e resolver problemas** fornece informações sobre questões e estratégias comuns para resolvê-los.
* **A saúde dos recursos** observa o seu recurso e diz-lhe se está a funcionar como esperado. Para obter mais informações sobre o serviço de saúde Azure Resource, consulte [a visão geral da saúde do Recurso Azure.](../resource-health/resource-health-overview.md)
* **O novo pedido de apoio** oferece opções para abrir um pedido de apoio para a sua cache.

Estas ferramentas permitem-lhe monitorizar a saúde do seu Azure Cache para instâncias Redis e ajudá-lo a gerir as suas aplicações de cache. Para obter mais informações, consulte a secção "Apoiar & configurações de resolução de problemas" de [Como configurar a Cache Azure para Redis](cache-configure.md).

<a name="cache-timeouts"></a>

### <a name="why-am-i-seeing-timeouts"></a>Por que estou a ver intervalos?
Os intervalos acontecem no cliente que usas para falar com o Redis. Quando um comando é enviado para o servidor Redis, o comando é feito na fila e o servidor Redis eventualmente pega no comando e executa-o. No entanto, o cliente pode sair do tempo durante este processo e se o fizer uma exceção é levantada no lado da chamada. Para obter mais informações sobre problemas de tempo de resolução de problemas, consulte [a resolução de problemas do lado do cliente](cache-troubleshoot-client.md) e as [exceções de tempo limite de stackExchange.Redis](cache-troubleshoot-timeouts.md#stackexchangeredis-timeout-exceptions).

<a name="cache-disconnect"></a>

### <a name="why-was-my-client-disconnected-from-the-cache"></a>Porque é que o meu cliente foi desligado da cache?
Os seguintes são uma razão comum para uma desconexão de cache.

* Causas do lado do cliente
  * O pedido do cliente foi redistribuído.
  * A aplicação do cliente realizou uma operação de escala.
    * No caso dos Serviços cloud ou web Apps, isso pode ser devido a autoscalagem.
  * A camada de rede do lado do cliente mudou.
  * Ocorreram erros transitórios no cliente ou nos nós de rede entre o cliente e o servidor.
  * Os limites do limiar da largura de banda foram atingidos.
  * As operações ligadas à CPU demoraram muito tempo a ser concluídas.
* Causas do lado do servidor
  * Na oferta de cache padrão, o serviço Azure Cache for Redis iniciou uma falha do nó primário para o nó secundário.
  * Azure estava remendando o caso onde a cache foi implantada
    * Isto pode ser para atualizações do servidor Redis ou manutenção geral de VM.

### <a name="which-azure-cache-offering-is-right-for-me"></a>Qual oferta de Azure Cache é a certa para mim?
> [!IMPORTANT]
> De acordo com o [anúncio](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)do ano passado, o Serviço de Cache Gerido a Azure e o serviço Azure In-Role Cache **foram retirados** a 30 de novembro de 2016. A nossa recomendação é usar [a Cache Azure para Redis.](https://azure.microsoft.com/services/cache/) Para obter informações sobre migração, consulte [Migrar do Serviço de Cache Gerido para Azure Cache para Redis](cache-migrate-to-redis.md).
>
>

### <a name="azure-cache-for-redis"></a>Cache do Azure para Redis
Azure Cache for Redis está geralmente disponível em tamanhos até 120 GB e tem uma disponibilidade SLA de 99,9%. O novo [nível premium](cache-premium-tier-intro.md) oferece tamanhos até 1,2 TB e suporte para clustering, VNET e persistência, com um SLA de 99,9%.

O Azure Cache para Redis dá aos clientes a capacidade de usar um Cache Azure seguro e dedicado para a Redis, gerido pela Microsoft. Com esta oferta, você pode aproveitar o conjunto de recursos e ecossistemas ricos fornecidos pela Redis, e hospedagem e monitorização confiáveis da Microsoft.

Ao contrário dos caches tradicionais que lidam apenas com pares de valores-chave, o Redis é popular pelos seus tipos de dados altamente performantes. O Redis também suporta a execução de operações atómicas neste tipo, como a anexação a uma corda; incrementando o valor em um haxixe; empurrando para uma lista; computação conjunto intersecção, união e diferença; ou obter o membro com a classificação mais alta em um conjunto classificado. Outras funcionalidades incluem suporte para transações, pub/sub, scripts Lua, teclas com um tempo limitado de vida, e configurações de configuração para fazer Redis comportar-se mais como uma cache tradicional.

Outro aspeto chave para o sucesso do Redis é o saudável e vibrante ecossistema de código aberto construído à sua volta. Isto reflete-se no conjunto diversificado de clientes Redis disponíveis em vários idiomas. Este ecossistema e uma vasta gama de clientes permitem que a Azure Cache para o Redis seja usada por quase qualquer carga de trabalho que construiria dentro do Azure.

Para obter mais informações sobre como começar com a Azure Cache para Redis, consulte [Como Usar a Cache Azure para redis](cache-dotnet-how-to-use-azure-redis-cache.md) e [Azure Cache para documentação Redis](index.yml).

### <a name="managed-cache-service"></a>Serviço cache gerido
[O serviço de Cache gerido foi reformado a 30 de novembro de 2016.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

Para ver documentação arquivada, consulte a [documentação do serviço de cache gerido arquivado.](/previous-versions/azure/azure-services/dn386094(v=azure.100))

### <a name="in-role-cache"></a>In-Role Cache
[In-Role Cache foi reformado a 30 de novembro de 2016.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

Para visualizar documentação arquivada, consulte a [documentação da Cache arquivada.](/previous-versions/azure/azure-services/dn386103(v=azure.100))

["minIoThreads" configuration setting]: https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx
