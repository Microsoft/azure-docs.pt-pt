---
title: FAQ da Cache do Azure para Redis
description: Aprenda as respostas a perguntas, padrões e boas práticas comuns para Azure Cache for Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: b95ee80a7a99009918f4869b62a3e3768e6e58d3
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83828275"
---
# <a name="azure-cache-for-redis-faq"></a>FAQ da Cache do Azure para Redis
Aprenda as respostas a perguntas comuns, padrões e boas práticas para Azure Cache for Redis.

## <a name="what-if-my-question-isnt-answered-here"></a>E se a minha pergunta não for respondida aqui?
Se a sua pergunta não estiver listada aqui, avise-nos e ajudaremos a encontrar uma resposta.

* Você pode publicar uma pergunta nos comentários no final desta FAQ e envolver-se com a equipe Azure Cache e outros membros da comunidade sobre este artigo.
* Para chegar a um público mais vasto, pode publicar uma pergunta no Microsoft Q&Uma página de [perguntas para Azure Cache](https://docs.microsoft.com/answers/topics/azure-cache-redis.html) e envolver-se com a equipa Azure Cache e outros membros da comunidade.
* Se quiser fazer um pedido de funcionalidade, pode submeter os seus pedidos e ideias ao [Azure Cache para Redis User Voice](https://feedback.azure.com/forums/169382-cache).
* Pode também enviar-nos um e-mail para o [Azure Cache External Feedback](mailto:azurecache@microsoft.com).

## <a name="azure-cache-for-redis-basics"></a>Azure Cache para redis básicos
As FAQs desta secção cobrem alguns dos fundamentos de Azure Cache para Redis.

* [O que é a Cache do Azure para Redis?](#what-is-azure-cache-for-redis)
* [Como posso começar com O Cache Azure para redis?](#how-can-i-get-started-with-azure-cache-for-redis)

As seguintes PERGUNTAS abrangem conceitos básicos e perguntas sobre Azure Cache for Redis e são respondidas numa das outras secções de PERGUNTAS FREQUENTES.

* [Que Azure Cache para redis oferecendo e tamanho devo usar?](#what-azure-cache-for-redis-offering-and-size-should-i-use)
* [Que Cache Azure para clientes Redis posso usar?](#what-azure-cache-for-redis-clients-can-i-use)
* [Há algum emmulador local para o Azure Cache para o Redis?](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [Como posso monitorizar a saúde e o desempenho da minha cache?](#how-do-i-monitor-the-health-and-performance-of-my-cache)

## <a name="planning-faqs"></a>Planejamento de FaQs
* [Que Azure Cache para redis oferecendo e tamanho devo usar?](#what-azure-cache-for-redis-offering-and-size-should-i-use)
* [Azure Cache para performance Redis](#azure-cache-for-redis-performance)
* [Em que região devo localizar o meu cache?](#in-what-region-should-i-locate-my-cache)
* [Como é que fui cobrado pelo Azure Cache pelo Redis?](#how-am-i-billed-for-azure-cache-for-redis)
* [Posso usar o Azure Cache para Redis com azure government cloud, Azure China Cloud ou Microsoft Azure Germany?](#can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany)

## <a name="development-faqs"></a>Perguntas-Gerais de Desenvolvimento
* [O que fazem as opções de configuração StackExchange.Redis?](#what-do-the-stackexchangeredis-configuration-options-do)
* [Que Cache Azure para clientes Redis posso usar?](#what-azure-cache-for-redis-clients-can-i-use)
* [Há algum emmulador local para o Azure Cache para o Redis?](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [Como posso executar os comandos redis?](#how-can-i-run-redis-commands)
* [Porque é que o Azure Cache para redis não tem uma referência da biblioteca da classe MSDN como alguns dos outros serviços do Azure?](#why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services)
* [Posso usar o Cache Azure para redis como cache de sessão php?](#can-i-use-azure-cache-for-redis-as-a-php-session-cache)
* [O que são bases de dados redis?](#what-are-redis-databases)

## <a name="security-faqs"></a>Perguntas de Segurança
* [Quando devo ativar a porta não-TLS/SSL para a ligação ao Redis?](#when-should-i-enable-the-non-tlsssl-port-for-connecting-to-redis)

## <a name="production-faqs"></a>FaQs de produção
* [Quais são as melhores práticas de produção?](#what-are-some-production-best-practices)
* [Quais são algumas das considerações quando se usam comandos redis comuns?](#what-are-some-of-the-considerations-when-using-common-redis-commands)
* [Como posso comparar e testar o desempenho da minha cache?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [Detalhes importantes sobre o crescimento da ThreadPool](#important-details-about-threadpool-growth)
* [Ativar o server GC para obter mais entrada no cliente ao utilizar StackExchange.Redis](#enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis)
* [Considerações de desempenho em torno de ligações](#performance-considerations-around-connections)

## <a name="monitoring-and-troubleshooting-faqs"></a>Monitorização e resolução de problemas de FAQs
As PERGUNTAS Frequentes desta secção cobrem questões comuns de monitorização e resolução de problemas. Para obter mais informações sobre monitorização e resolução de problemas do seu Azure Cache para os casos Redis, consulte [Como monitorizar o Azure Cache for Redis](cache-how-to-monitor.md) e os vários guias de resolução de problemas.

* [Como posso monitorizar a saúde e o desempenho da minha cache?](#how-do-i-monitor-the-health-and-performance-of-my-cache)
* [Porque é que estou a ver intervalos?](#why-am-i-seeing-timeouts)
* [Porque é que o meu cliente foi desligado da cache?](#why-was-my-client-disconnected-from-the-cache)

## <a name="prior-cache-offering-faqs"></a>Cache Anterior oferecendo FAQs
* [Que oferta de Azure Cache é certa para mim?](#which-azure-cache-offering-is-right-for-me)

### <a name="what-is-azure-cache-for-redis"></a>O que é a Cache do Azure para Redis?
Azure Cache for Redis é baseado no popular software de código aberto [Redis.](https://redis.io/) Dá-lhe acesso a um Azure Cache para Redis, seguro e dedicado, gerido pela Microsoft, e acessível a partir de qualquer aplicação dentro do Azure. Para obter uma visão mais detalhada, consulte a página de produtos [Azure Cache para Redis](https://azure.microsoft.com/services/cache/) na Azure.com.

### <a name="how-can-i-get-started-with-azure-cache-for-redis"></a>Como posso começar com O Cache Azure para redis?
Há várias maneiras de começar com Azure Cache para Redis.

* Pode consultar um dos nossos tutoriais disponíveis para [.NET,](cache-dotnet-how-to-use-azure-redis-cache.md) [ASP.NET](cache-web-app-howto.md), [Java,](cache-java-get-started.md) [Node.js](cache-nodejs-get-started.md)e [Python.](cache-python-get-started.md)
* Pode ver [como construir aplicações de alto desempenho utilizando o Cache Microsoft Azure para Redis](https://azure.microsoft.com/documentation/videos/how-to-build-high-performance-apps-using-microsoft-azure-cache/).
* Pode consultar a documentação do cliente para os clientes que correspondem à linguagem de desenvolvimento do seu projeto para ver como utilizar o Redis. Há muitos clientes Redis que podem ser usados com Azure Cache para Redis. Para uma lista de clientes redis, [https://redis.io/clients](https://redis.io/clients) veja.

Se ainda não tem uma conta Azure, pode:

* [Criar uma conta do Azure gratuitamente](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero). Recebe créditos que podem ser utilizados para experimentar os serviços pagos do Azure. Mesmo depois de gastar todos os créditos, pode manter a conta e utilizar os serviços e recursos gratuitos do Azure.
* [Ativar os benefícios de subscritor do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). A sua subscrição do MSDN dá-lhe créditos todos os meses que pode utilizar em serviços pagos do Azure.

<a name="cache-size"></a>

### <a name="what-azure-cache-for-redis-offering-and-size-should-i-use"></a>Que Azure Cache para redis oferecendo e tamanho devo usar?
Cada oferta de Cache Azure para Redis oferece diferentes níveis de **tamanho,** **largura de banda,** **alta disponibilidade**e opções **de SLA.**

Seguem-se considerações para escolher uma oferta de Cache.

* **Memória**: Os níveis Básico e Standard oferecem 250 MB – 53 GB. O nível Premium oferece até 1,2 TB (como cluster) ou 120 GB (não agrupado). Para mais informações, consulte [Azure Cache para preços redis](https://azure.microsoft.com/pricing/details/cache/).
* **Desempenho da rede**: Se tiver uma carga de trabalho que requer uma elevada entrada, o nível Premium oferece mais largura de banda em comparação com standard ou Básico. Também dentro de cada nível, caches de tamanho maior têm mais largura de banda devido ao VM subjacente que acolhe a cache. Para mais informações, consulte a [tabela seguinte](#cache-performance).
* **Entrada**: O nível Premium oferece o máximo de entrada disponível. Se o servidor cache ou cliente atingir os limites de largura de banda, poderá receber intervalos de tempo do lado do cliente. Para mais informações, consulte a tabela a seguir.
* **Alta Disponibilidade/SLA**: Azure Cache for Redis garante que uma cache Standard/Premium está disponível pelo menos 99,9% das vezes. Para saber mais sobre o nosso SLA, consulte [Azure Cache para preços redis](https://azure.microsoft.com/support/legal/sla/cache/v1_0/). O SLA cobre apenas a conectividade com os pontos finais do Cache. O SLA não cobre a proteção contra a perda de dados. Recomendamos a utilização da funcionalidade de persistência de dados Redis no nível Premium para aumentar a resiliência contra a perda de dados.
* **Redis Data Persistence**: O nível Premium permite-lhe persistir os dados de cache numa conta de Armazenamento Azure. Numa cache Basic/Standard, todos os dados são armazenados apenas na memória. As questões subjacentes à infraestrutura podem resultar numa potencial perda de dados. Recomendamos a utilização da funcionalidade de persistência de dados Redis no nível Premium para aumentar a resiliência contra a perda de dados. Azure Cache for Redis oferece opções RDB e AOF (em breve) na persistência de Redis. Para mais informações, consulte [Como configurar a persistência de um Cache Premium Azure para Redis](cache-how-to-premium-persistence.md).
* **Redis Cluster**: Para criar caches superiores a 120 GB, ou para fragmentos de dados em vários nós Redis, pode utilizar o clusterredis, que está disponível no nível Premium. Cada nó consiste num par de cache primário/réplica para alta disponibilidade. Para mais informações, consulte [Como configurar o agrupamento para um Cache Premium Azure para Redis](cache-how-to-premium-clustering.md).
* **Reforço**da segurança e isolamento da rede : A implantação da Rede Virtual Azure (VNET) proporciona uma maior segurança e isolamento para o seu Azure Cache para Redis, bem como subredes, políticas de controlo de acesso e outras funcionalidades para restringir ainda mais o acesso. Para mais informações, consulte [como configurar o suporte da Rede Virtual para um Cache Premium Azure para Redis](cache-how-to-premium-vnet.md).
* **Configure Redis**: Tanto nos níveis Standard como Premium, pode configurar redis para notificações keyspace.
* **Número máximo de ligações ao cliente**: O nível Premium oferece o número máximo de clientes que podem ligar-se à Redis, com um maior número de ligações para caches de maior dimensão. O agrupamento não aumenta o número de ligações disponíveis para uma cache agrupada. Para mais informações, consulte [O Cache Azure para obter preços redis](https://azure.microsoft.com/pricing/details/cache/).
* **Core dedicado para Redis Server**: No nível Premium, todos os tamanhos de cache têm um núcleo dedicado para redis. Nos níveis Basic/Standard, o tamanho C1 e acima têm um núcleo dedicado para o servidor Redis.
* **Redis é de roscar-se,** por isso ter mais de dois núcleos não oferece benefícios adicionais por ter apenas dois núcleos, mas tamanhos de VM maiores normalmente têm mais largura de banda do que tamanhos menores. Se o servidor cache ou cliente atingir os limites de largura de banda, então receberá intervalos de tempo no lado do cliente.
* **Melhorias**de desempenho : Os caches no nível Premium são implantados em hardware que tem processadores mais rápidos, proporcionando um melhor desempenho em comparação com o nível Básico ou Standard. Os caches de nível premium têm maior entrada e lestabeleceções mais baixas.

<a name="cache-performance"></a>

### <a name="azure-cache-for-redis-performance"></a>Azure Cache para performance Redis
A tabela seguinte mostra os valores máximos de largura de banda observados enquanto testa vários tamanhos de caches Standard e Premium utilizando `redis-benchmark.exe` de um IaaS VM contra o Azure Cache para ponto final redis. Para a entrada de TLS, o redis-benchmark é usado com um túnel para ligar ao Azure Cache para redis endpoint.

>[!NOTE] 
>Estes valores não estão garantidos e não há SLA para estes números, mas devem ser típicos. Deve carregar o teste da sua própria aplicação para determinar o tamanho certo da cache para a sua aplicação.
>Estes números podem mudar à medida que publicamos resultados mais recentes periodicamente.
>

A partir desta tabela, podemos tirar as seguintes conclusões:

* A entrada para os caches do mesmo tamanho é maior no nível Premium em comparação com o nível Standard. Por exemplo, com uma cache de 6 GB, a entrada de P1 é de 180.000 pedidos por segundo (RPS) em comparação com 100.000 RPS para C3.
* Com o agrupamento Redis, a entrada aumenta linearmente à medida que aumenta o número de fragmentos (nós) no cluster. Por exemplo, se criar um aglomerado P4 de 10 fragmentos, então a entrada disponível é de 400.000 * 10 = 4 milhões de RPS.
* A entrada para tamanhos-chave maiores é maior no nível Premium em comparação com o Standard Tier.

| Escalão de preço | Tamanho | Núcleos de CPU | Largura de banda disponível | Tamanho de valor de 1 KB | Tamanho de valor de 1 KB |
| --- | --- | --- | --- | --- | --- |
| **Tamanhos de cache padrão** | | |**Megabits por seg (Mb/s) / Megabytes por seg (MB/s)** |**Pedidos por segundo (RPS) Não-SSL** |**Pedidos por segundo (RPS) SSL** |
| CO | 250 MB | Partilhado | 100 / 12.5  |  15 000 |   7.500 |
| C1 |   1 GB | 1      | 500 / 62.5  |  38,000 |  20,720 |
| C2 | 2,5 GB | 2      | 500 / 62.5  |  41,000 |  37,000 |
| C3 |   6 GB | 4      | 1000 / 125  | 100 000 |  90.000 |
| C4 |  13 GB | 2      | 500 / 62.5  |  60 000 |  55,000 |
| C5 |  26 GB | 4      | 1,000 / 125 | 102,000 |  93,000 |
| C6 |  53 GB | 8      | 2,000 / 250 | 126,000 | 120,000 |
| **Tamanhos de cache premium** | |**Núcleos de CPU por fragmento** | **Megabits por seg (Mb/s) / Megabytes por seg (MB/s)** |**Pedidos por segundo (RPS) Não-SSL, por fragmento** |**Pedidos por segundo (RPS) SSL, por fragmento** |
| P1 |   6 GB |  2 | 1,500 / 187.5 | 180,000 | 172,000 |
| P2 |  13 GB |  4 | 3,000 / 375   | 350,000 | 341,000 |
| P3 |  26 GB |  4 | 3,000 / 375   | 350,000 | 341,000 |
| P4 |  53 GB |  8 | 6,000 / 750   | 400,000 | 373,000 |
| P5 | 120 GB | 20 | 6,000 / 750   | 400,000 | 373,000 |

Para obter instruções sobre a instalação do túnel ou o download das ferramentas Redis, tais como `redis-benchmark.exe` , veja a secção De como posso executar [comandos Redis?](#cache-commands)

<a name="cache-region"></a>

### <a name="in-what-region-should-i-locate-my-cache"></a>Em que região devo localizar o meu cache?
Para melhor desempenho e menor latência, localize o seu Azure Cache para Redis na mesma região que a sua aplicação de cliente cache.

<a name="cache-billing"></a>

### <a name="how-am-i-billed-for-azure-cache-for-redis"></a>Como é que fui cobrado pelo Azure Cache pelo Redis?
Azure Cache para os preços redis está [aqui.](https://azure.microsoft.com/pricing/details/cache/) A página de preços lista os preços como uma taxa horária. Os caches são faturados por minuto a partir do momento em que a cache é criada até ao momento em que uma cache é eliminada. Não há opção para parar ou parar a faturação de um cache.

### <a name="can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany"></a>Posso usar o Azure Cache para Redis com azure government cloud, Azure China Cloud ou Microsoft Azure Germany?
Sim, o Azure Cache for Redis está disponível na Azure Government Cloud, Azure China 21Vianet Cloud e Microsoft Azure Germany. Os URLs para aceder e gerir o Azure Cache para Redis são diferentes nestas nuvens em comparação com a Nuvem Pública Azure.

| Nuvem   | Dns Sufixo para Redis            |
|---------|---------------------------------|
| Público  | *.redis.cache.windows.net       |
| US Gov  | *.redis.cache.usgovcloudapi.net |
| Alemanha | *.redis.cache.cloudapi.de       |
| China   | *.redis.cache.chinacloudapi.cn  |

Para obter mais informações sobre considerações ao utilizar O Cache Azure para Redis com outras nuvens, consulte os seguintes links.

- [Bases de dados do Governo Azure - Azure Cache for Redis](../azure-government/documentation-government-services-database.md#azure-cache-for-redis)
- [Azure China 21Vianet Cloud - Azure Cache for Redis](https://www.azure.cn/home/features/redis-cache/)
- [Microsoft Azure Alemanha](https://azure.microsoft.com/overview/clouds/germany/)

Para obter informações sobre a utilização de Azure Cache para Redis com PowerShell em Azure Government Cloud, Azure China 21Vianet Cloud, e Microsoft Azure Germany, veja [como se conectar a outras nuvens - Azure Cache para Redis PowerShell](cache-how-to-manage-redis-cache-powershell.md#how-to-connect-to-other-clouds).

<a name="cache-configuration"></a>

### <a name="what-do-the-stackexchangeredis-configuration-options-do"></a>O que fazem as opções de configuração StackExchange.Redis?
StackExchange.Redis tem muitas opções. Esta secção fala sobre algumas das configurações comuns. Para obter informações mais detalhadas sobre as opções StackExchange.Redis, consulte a [configuração StackExchange.Redis](https://stackexchange.github.io/StackExchange.Redis/Configuration).

| ConfigurationOptions | Descrição | Recomendação |
| --- | --- | --- |
| AbortOnConnectFail |Quando for verdadeira, a ligação não se reconecta após uma falha de rede. |Despumre a falso e deixe stackExchange.Redis voltar a ligar-se automaticamente. |
| ConnectRetry |O número de vezes para repetir as tentativas de ligação durante a ligação inicial. |Consulte as seguintes notas para obter orientação. |
| ConnectTimeout |Intervalo em Ms para operações de ligação. |Consulte as seguintes notas para obter orientação. |

Normalmente, os valores padrão do cliente são suficientes. Pode afinar as opções com base na sua carga de trabalho.

* **Tentativas**
  * Para o ConnectRetry e o ConnectTimeout, a orientação geral é falhar rapidamente e voltar a tentar novamente. Esta orientação baseia-se na sua carga de trabalho e no tempo que o seu cliente leva para emitir um comando Redis e receber uma resposta.
  * Deixe o StackExchange.Redis voltar a ligar-se automaticamente em vez de verificar o estado da ligação e voltar a ligar-se. **Evite utilizar a propriedade ConnectionMultiplexer.IsConnected**.
  * Bola de neve - às vezes pode-se encontrar um problema em que se está a tentar e a bola de neve tenta e nunca se recupera. Se ocorrer uma bola de neve, deve considerar a utilização de um algoritmo exponencial de retry backoff, conforme descrito na [orientação geral de Retry](../best-practices-retry-general.md) publicada pelo grupo Microsoft Patterns & Practices.
  
* **Valores de timeout**
  * Considere a sua carga de trabalho e detete os valores em conformidade. Se estiver a armazenar valores elevados, detete o prazo para um valor mais elevado.
  * `AbortOnConnectFail`Despumre a falso e deixe stackExchange.Redis voltar a ligar-se para si.
  * Utilize uma única instância de Ligação Multiplexer para a aplicação. Pode utilizar uma LazyConnection para criar uma única instância que é devolvida por uma propriedade de Ligação, como mostra o [Connect à cache utilizando a classe ConnectionMultiplexer](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).
  * Deteto a propriedade para um nome único para fins de `ConnectionMultiplexer.ClientName` diagnóstico.
  * Utilize `ConnectionMultiplexer` várias instâncias para cargas de trabalho personalizadas.
      * Pode seguir este modelo se tiver uma carga variada na sua aplicação. Por exemplo:
      * Podes ter um multiplexer para lidar com chaves grandes.
      * Podes ter um multiplexer para lidar com chaves pequenas.
      * Pode definir diferentes valores para intervalos de ligação e voltar a tentar a lógica de cada LigaçãoMultiplexer que utiliza.
      * Desloque a `ClientName` propriedade em cada multiplexer para ajudar com diagnósticos.
      * Esta orientação pode conduzir a uma latência mais simplificada por `ConnectionMultiplexer` .

### <a name="what-azure-cache-for-redis-clients-can-i-use"></a>Que Cache Azure para clientes Redis posso usar?
Uma das grandes coisas sobre redis é que há muitos clientes apoiando muitas línguas de desenvolvimento diferentes. Para uma lista atual de clientes, consulte [clientes Redis.](https://redis.io/clients) Para tutoriais que cobrem várias línguas e clientes diferentes, veja [como usar o Azure Cache para redis](cache-dotnet-how-to-use-azure-redis-cache.md) e os seus artigos de irmãos na tabela de conteúdos.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

<a name="cache-emulator"></a>

### <a name="is-there-a-local-emulator-for-azure-cache-for-redis"></a>Há algum emmulador local para o Azure Cache para o Redis?
Não existe emulador local para O Cache Azure para Redis, mas pode executar a versão MSOpenTech do redis-server.exe a partir das ferramentas de [linha de comando Redis](https://github.com/MSOpenTech/redis/releases/) na sua máquina local e ligar-se a ele para obter uma experiência semelhante a um emulador local, como mostra o seguinte exemplo:

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


Pode configurar opcionalmente um ficheiro [redis.conf](https://redis.io/topics/config) para combinar mais estreitamente as [definições](cache-configure.md#default-redis-server-configuration) de cache padrão para o seu Azure Cache online para Redis, se desejar.

<a name="cache-commands"></a>

### <a name="how-can-i-run-redis-commands"></a>Como posso executar os comandos redis?
Pode utilizar qualquer um dos comandos listados nos [comandos Redis,](https://redis.io/commands#) exceto os comandos listados nos [comandos Redis não suportados em Azure Cache for Redis](cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis). Tem várias opções para executar comandos Redis.

* Se tiver uma cache Standard ou Premium, pode executar comandos Redis utilizando a [Consola Redis](cache-configure.md#redis-console). A consola Redis fornece uma forma segura de executar comandos Redis no portal Azure.
* Também pode utilizar as ferramentas de linha de comando Redis. Para usá-los, execute os seguintes passos:
* Descarregue as [ferramentas da linha de comando Redis](https://github.com/MSOpenTech/redis/releases/).
* Ligue-se à cache utilizando `redis-cli.exe` . Passe no ponto final da cache utilizando o interruptor -h e a tecla utilizando - a como mostrado no seguinte exemplo:
* `redis-cli -h <Azure Cache for Redis name>.redis.cache.windows.net -a <key>`

> [!NOTE]
> As ferramentas de linha de comando Redis não funcionam com a porta TLS, mas pode utilizar um utilitário como `stunnel` ligar as ferramentas à porta TLS seguindo as instruções na ferramenta de linha de comando Redis com o artigo [Azure Cache para redis.](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-redis-cli-tool)
>
>

<a name="cache-reference"></a>

### <a name="why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services"></a>Porque é que o Azure Cache para redis não tem uma referência da biblioteca da classe MSDN como alguns dos outros serviços do Azure?
O Microsoft Azure Cache for Redis baseia-se no popular Azure Cache de origem aberta para redis. Pode ser acedido por uma grande variedade de [clientes Redis](https://redis.io/clients) para muitas linguagens de programação. Cada cliente tem a sua própria API que faz chamadas para o Azure Cache for Redis, usando [comandos Redis.](https://redis.io/commands)

Como cada cliente é diferente, não existe uma referência de classe centralizada na MSDN, e cada cliente mantém a sua própria documentação de referência. Além da documentação de referência, existem vários tutoriais que mostram como começar com o Azure Cache for Redis usando diferentes idiomas e clientes cache. Para aceder a estes tutoriais, veja [como usar o Azure Cache para redis](cache-dotnet-how-to-use-azure-redis-cache.md) e os seus artigos de irmãos na tabela de conteúdos.

### <a name="can-i-use-azure-cache-for-redis-as-a-php-session-cache"></a>Posso usar o Cache Azure para redis como cache de sessão php?
Sim, para utilizar o Cache Azure para Redis como cache de sessão PHP, especifique a cadeia de ligação ao seu Cache Azure para o caso Redis em `session.save_path` .

> [!IMPORTANT]
> Ao utilizar o Azure Cache para Redis como cache de sessão PHP, deve codificar a chave de segurança utilizada para se ligar à cache, como mostra o seguinte exemplo:
>
> `session.save_path = "tcp://mycache.redis.cache.windows.net:6379?auth=<url encoded primary or secondary key here>";`
>
> Se a chave não estiver codificada em URL, poderá receber uma exceção com uma mensagem como:`Failed to parse session.save_path`
>
>

Para obter mais informações sobre a utilização do Azure Cache para Redis como cache de sessão PHP com o cliente PhpRedis, consulte [php session handler](https://github.com/phpredis/phpredis#php-session-handler).

### <a name="what-are-redis-databases"></a>O que são bases de dados redis?

As bases de dados redis são apenas uma separação lógica de dados dentro da mesma instância redis. A memória cache é partilhada entre todas as bases de dados e o consumo real de memória de uma determinada base de dados depende das chaves/valores armazenados nessa base de dados. Por exemplo, uma cache C6 tem 53 GB de memória, e um P5 tem 120 GB. Pode optar por colocar todos os 53 GB / 120 GB numa base de dados ou pode dividi-lo entre várias bases de dados. 

> [!NOTE]
> Ao utilizar um Cache Premium Azure para Redis com clustering ativado, apenas está disponível a base de dados 0. Esta limitação é uma limitação intrínseca de Redis e não é específica de Azure Cache para Redis. Para mais informações, consulte [Se preciso de fazer alterações na aplicação do meu cliente para utilizar o clustering?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 


<a name="cache-ssl"></a>

### <a name="when-should-i-enable-the-non-tlsssl-port-for-connecting-to-redis"></a>Quando devo ativar a porta não-TLS/SSL para a ligação ao Redis?
O servidor Redis não suporta de forma nativa o TLS, mas o Azure Cache para redis sim. Se está a ligar-se ao Azure Cache para redis e o seu cliente suporta TLS, como StackExchange.Redis, então deve utilizar O TLS.

>[!NOTE]
>A porta não-TLS é desativada por padrão para o novo Azure Cache para os casos Redis. Se o seu cliente não suportar tLS, então deve ativar a porta não TLS seguindo as instruções na secção portas de [acesso](cache-configure.md#access-ports) da [Configuração de uma cache em Azure Cache para artigo redis.](cache-configure.md)
>
>

Ferramentas vermelhas como `redis-cli` não funcionam com a porta TLS, mas pode utilizar um utilitário como ligar as `stunnel` ferramentas de forma segura à porta TLS seguindo as instruções no anúncio ASP.NET post de blog do State Provider para o Lançamento de [Visualização redis.](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)

Para obter instruções sobre o download das ferramentas Redis, veja a secção [de comandos Redis?](#cache-commands)

### <a name="what-are-some-production-best-practices"></a>Quais são as melhores práticas de produção?
* [StackExchange.Redis boas práticas](#stackexchangeredis-best-practices)
* [Configuração e conceitos](#configuration-and-concepts)
* [Teste de desempenho](#performance-testing)

#### <a name="stackexchangeredis-best-practices"></a>StackExchange.Redis boas práticas
* `AbortConnect`Despumre a falso e deixe o ConnectionMultiplexer voltar a ligar-se automaticamente. [Consulte aqui para mais detalhes.](https://gist.github.com/JonCole/36ba6f60c274e89014dd#file-se-redis-setabortconnecttofalse-md)
* Reutilizar o ConnectionMultiplexer - não crie um novo para cada pedido. O `Lazy<ConnectionMultiplexer>` padrão mostrado [aqui](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache) é recomendado.
* Redis funciona melhor com valores menores, por isso considere cortar dados maiores em várias teclas. [Nesta discussão redis,](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ)100 kb é considerado grande. Leia [este artigo](https://gist.github.com/JonCole/db0e90bedeb3fc4823c2#large-requestresponse-size) para um problema de exemplo que pode ser causado por grandes valores.
* Configure as [definições de ThreadPool](#important-details-about-threadpool-growth) para evitar intervalos.
* Utilize pelo menos o tempo de ligação predefinido de 5 segundos. Este intervalo dá ao StackExchange.Redis tempo suficiente para restabelecer a ligação em caso de blip de rede.
* Esteja ciente dos custos de desempenho associados a diferentes operações que está a executar. Por exemplo, o `KEYS` comando é uma operação O(n) e deve ser evitado. O [site redis.io](https://redis.io/commands/) tem detalhes em torno da complexidade do tempo para cada operação que suporta. Clique em cada comando para ver a complexidade de cada operação.

#### <a name="configuration-and-concepts"></a>Configuração e conceitos
* Utilizar o Standard ou o Premium Tier para sistemas de produção. O Escalão Básico é um sistema de nó único sem replicação de dados e sem SLA. Além disso, utilize pelo menos uma cache C1. Os caches C0 são normalmente usados para cenários simples de dev/teste.
* Lembre-se que Redis é uma loja de dados **em memória.** Leia [este artigo](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) para que esteja ciente de cenários onde a perda de dados pode ocorrer.
* Desenvolva o seu sistema de modo a que possa lidar com os blips de ligação [devido a remendos e falhas](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md).

#### <a name="performance-testing"></a>Teste de desempenho
* Comece por usar para obter uma sensação de possível entrada antes de `redis-benchmark.exe` escrever os seus próprios testes perf. Como `redis-benchmark` não suporta TLS, deve [ativar a porta Não-TLS através do portal Azure](cache-configure.md#access-ports) antes de executar o teste. Por exemplo, ver [como posso comparar e testar o desempenho da minha cache?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* O VM cliente utilizado para testes deve estar na mesma região que o seu Azure Cache para redis.
* Recomendamos a utilização da Série VM Dv2 para o seu cliente, uma vez que têm melhor hardware e devem dar os melhores resultados.
* Certifique-se de que o vM do seu cliente que escolhe tem pelo menos tanta capacidade de computação e largura de banda como a cache que está a testar.
* Ative o VRSS na máquina do cliente se estiver no Windows. [Consulte aqui para mais detalhes.](https://technet.microsoft.com/library/dn383582.aspx)
* Os casos de Redis de nível premium têm melhor latência e entrada de rede porque estão a funcionar com um melhor hardware tanto para cpu como para network.

<a name="cache-redis-commands"></a>

### <a name="what-are-some-of-the-considerations-when-using-common-redis-commands"></a>Quais são algumas das considerações quando se usam comandos redis comuns?

* Evite utilizar certos comandos Redis que demoram muito tempo a ser concluídos, a menos que compreenda completamente o impacto destes comandos. Por exemplo, não executar o comando [KEYS](https://redis.io/commands/keys) em produção. Dependendo do número de chaves, pode levar muito tempo a regressar. Redis é um servidor de um fio único e processa comandos um de cada vez. Se tiver outros comandos emitidos após as KEYS, não serão processados até que a Redis processe o comando KEYS. O [site redis.io](https://redis.io/commands/) tem detalhes em torno da complexidade do tempo para cada operação que suporta. Clique em cada comando para ver a complexidade de cada operação.
* Tamanhos-chave - devo usar pequenas chaves/valores ou grandes chaves/valores? Depende do cenário. Se o seu cenário necessitar de teclas maiores, pode ajustar o Tempo de Ligação, em seguida, retentar os valores e ajustar a sua lógica de retry. Do ponto de vista do servidor Redis, valores menores dão melhor desempenho.
* Estas considerações não significam que não se pode armazenar valores maiores em Redis; deve estar ciente das seguintes considerações. As tardios serão mais altas. Se tiver um conjunto de dados maiore e menor, pode utilizar várias instâncias de ConnectionMultiplexer, cada uma configurada com um conjunto diferente de valores de tempo e de retry, como descrito no anterior O que as opções de [configuração StackExchange.Redis fazem](#cache-configuration) a secção.

<a name="cache-benchmarking"></a>

### <a name="how-can-i-benchmark-and-test-the-performance-of-my-cache"></a>Como posso comparar e testar o desempenho da minha cache?
* [Ativar o diagnóstico da cache](cache-how-to-monitor.md#enable-cache-diagnostics) para poder [monitorizar](cache-how-to-monitor.md) o estado de funcionamento da cache. Pode ver as métricas no portal Azure e também pode [descarregá-las e revê-las](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) utilizando as ferramentas da sua escolha.
* Pode utilizar redis-benchmark.exe para carregar o teste do seu servidor Redis.
* Certifique-se de que o cliente de teste de carga e o Cache Azure para Redis estão na mesma região.
* Utilize redis-cli.exe e monitorize a cache utilizando o comando INFO.
* Se a sua carga estiver a causar uma fragmentação de memória elevada, deve escalar até um tamanho maior.
* Para obter instruções sobre o download das ferramentas Redis, veja a secção [de comandos Redis?](#cache-commands)

Os seguintes comandos fornecem um exemplo de utilização de redis-benchmark.exe. Para obter resultados precisos, execute estes comandos a partir de um VM na mesma região que a sua cache.

* Teste de pedidos de SET pipelined utilizando uma carga útil de 1k

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t SET -n 1000000 -d 1024 -P 50`
* Teste os pedidos get pipelined utilizando uma carga útil de 1k.
  NOTA: Executar o teste SET mostrado acima do primeiro para povoar cache

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t GET -n 1000000 -d 1024 -P 50`

<a name="threadpool"></a>

### <a name="important-details-about-threadpool-growth"></a>Detalhes importantes sobre o crescimento da ThreadPool
O CLR ThreadPool tem dois tipos de fios - "Trabalhador" e "I/O Completion Port" (IOCP).

* Os fios dos trabalhadores são usados para coisas como processar `Task.Run(…)` os, ou `ThreadPool.QueueUserWorkItem(…)` métodos. Estes fios também são utilizados por vários componentes no CLR quando o trabalho tem de acontecer num fio de fundo.
* Os fios DOICosão são usados quando o IO assíncrono acontece, como quando se lê a partir da rede.

O conjunto de rosca fornece novos fios de trabalho ou fios de conclusão de I/S a pedido (sem qualquer estrangulamento) até atingir a definição "Mínima" para cada tipo de fio. Por padrão, o número mínimo de fios é definido para o número de processadores num sistema.

Uma vez que o número de fios existentes (ocupados) atinge o número "mínimo" de fios, o ThreadPool irá acelerar a velocidade a que injeta novos fios a um fio por 500 milissegundos. Normalmente, se o seu sistema tiver uma explosão de trabalho que precisa de um fio COI, irá processar esse trabalho rapidamente. No entanto, se a explosão de trabalho for mais do que a configuração "Mínima" configurada, haverá algum atraso no processamento de parte do trabalho enquanto a ThreadPool espera que aconteça uma de duas coisas.

1. Um fio existente torna-se livre para processar o trabalho.
2. Nenhum fio existente fica livre por 500 ms, por isso é criado um novo fio.

Basicamente, significa que quando o número de fios Ocupados é maior do que os fios Min, você provavelmente está pagando um atraso de 500 ms antes que o tráfego da rede seja processado pela aplicação. Além disso, é importante notar que quando um fio existente permanece inativo por mais de 15 segundos (com base no que me lembro), será limpo e este ciclo de crescimento e encolhimento pode repetir-se.

Se olharmos para uma mensagem de erro de exemplo de StackExchange.Redis (construir 1.0.450 ou mais tarde), verá que agora imprime estatísticas da ThreadPool (ver detalhes do IOCP e do TRABALHADOR abaixo).

    System.TimeoutException: Timeout performing GET MyKey, inst: 2, mgr: Inactive,
    queue: 6, qu: 0, qs: 6, qc: 0, wr: 0, wq: 0, in: 0, ar: 0,
    IOCP: (Busy=6,Free=994,Min=4,Max=1000),
    WORKER: (Busy=3,Free=997,Min=4,Max=1000)

No exemplo anterior, pode ver que para o fio IOCP existem seis fios ocupados e o sistema está configurado para permitir quatro fios mínimos. Neste caso, o cliente provavelmente teria visto dois atrasos de 500 ms, porque 6 > 4.

Note que StackExchange.Redis pode atingir os intervalos se o crescimento de linhas DOIP ou TRABALHADOR for estrangulado.

### <a name="recommendation"></a>Recomendação

Tendo em conta esta informação, recomendamos vivamente que os clientes estabeleçam o valor mínimo de configuração para os fios IOCP e WORKER para algo maior do que o valor padrão. Não podemos dar orientações de tamanho único sobre o que este valor deve ser porque o valor certo para uma aplicação será provavelmente demasiado alto ou baixo para outra aplicação. Esta definição também pode impactar o desempenho de outras partes de aplicações complicadas, pelo que cada cliente precisa de afinar esta configuração às suas necessidades específicas. Um bom ponto de partida é 200 ou 300, depois testar e ajustar conforme necessário.

Como configurar esta definição:

* Recomendamos que esta definição seja alterada programáticamente utilizando o método [ThreadPool.SetMinThreads (...)](/dotnet/api/system.threading.threadpool.setminthreads#System_Threading_ThreadPool_SetMinThreads_System_Int32_System_Int32_) em `global.asax.cs` . Por exemplo:

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
  > O valor especificado por este método é uma definição global, afetando todo o AppDomain. Por exemplo, se tiver uma máquina de 4 núcleos e pretender definir *minWorkerThreads* e *minIoThreads* a 50 por CPU durante o tempo de execução, utilizaria **ThreadPool.SetMinThreads (200, 200)**.

* Também é possível especificar a definição de fios mínimos utilizando a [configuração *minIoThreads* ou *minWorkerThreads* ](https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx) sob o elemento de `<processModel>` configuração em `Machine.config` , normalmente localizado em `%SystemRoot%\Microsoft.NET\Framework\[versionNumber]\CONFIG\` . **A definição do número de fios mínimos desta forma não é geralmente recomendada, porque se trata de uma definição ao nível do Sistema.**

  > [!NOTE]
  > O valor especificado neste elemento de configuração é uma definição *por núcleo.* Por exemplo, se tiver uma máquina de 4 núcleos e quiser que a definição de *minIoThreads* seja de 200 no tempo de execução, utilizaria `<processModel minIoThreads="50"/>` .
  >

<a name="server-gc"></a>

### <a name="enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis"></a>Ativar o server GC para obter mais entrada no cliente ao utilizar StackExchange.Redis
Ativar o servidor GC pode otimizar o cliente e fornecer um melhor desempenho e entrada ao utilizar StackExchange.Redis. Para obter mais informações sobre o servidor GC e como o ativar, consulte os seguintes artigos:

* [Para ativar o servidor GC](/dotnet/framework/configure-apps/file-schema/runtime/gcserver-element)
* [Fundamentos da Coleção de Lixo](/dotnet/standard/garbage-collection/fundamentals)
* [Recolha e Performance de Lixo](/dotnet/standard/garbage-collection/performance)


### <a name="performance-considerations-around-connections"></a>Considerações de desempenho em torno de ligações

Cada nível de preços tem limites diferentes para ligações ao cliente, memória e largura de banda. Embora cada tamanho de cache permita *até* um certo número de ligações, cada ligação com Redis tem sobrecarga associada a ele. Um exemplo de tal sobrecarga seria cpU e uso de memória como resultado da encriptação TLS/SSL. O limite máximo de ligação para um dado tamanho de cache pressupõe uma cache levemente carregada. Se a carga a partir de ligação superior *mais* a carga das operações do cliente exceder a capacidade do sistema, a cache pode experimentar problemas de capacidade mesmo que não tenha excedido o limite de ligação para o tamanho atual do cache.

Para obter mais informações sobre os diferentes limites de ligações para cada nível, consulte [O Cache Azure para preços Redis](https://azure.microsoft.com/pricing/details/cache/). Para obter mais informações sobre ligações e outras configurações predefinidas, consulte a [configuração do servidor Predefinido Redis](cache-configure.md#default-redis-server-configuration).

<a name="cache-monitor"></a>

### <a name="how-do-i-monitor-the-health-and-performance-of-my-cache"></a>Como posso monitorizar a saúde e o desempenho da minha cache?
Os casos do Microsoft Azure Cache para redis podem ser monitorizados no [portal Azure](https://portal.azure.com). Pode visualizar métricas, gráficos de métricas de pin os gráficos para a Startboard, personalizar a gama de data e hora dos gráficos de monitorização, adicionar e remover métricas dos gráficos, e definir alertas quando determinadas condições são satisfeitas. Para mais informações, consulte [Monitor Azure Cache for Redis](cache-how-to-monitor.md).

O menu Azure Cache for Redis **Resource** também contém várias ferramentas para monitorizar e resolver problemas nos seus caches.

* **Diagnosticar e resolver problemas** fornece informações sobre questões e estratégias comuns para resolvê-los.
* **A saúde** dos recursos observa o seu recurso e diz-lhe se está a funcionar como esperado. Para mais informações sobre o serviço de saúde azure Resource, consulte a [visão geral da saúde do Recurso Azure.](../resource-health/resource-health-overview.md)
* **Novo pedido de apoio** oferece opções para abrir um pedido de apoio para a sua cache.

Estas ferramentas permitem-lhe monitorizar a saúde do seu Azure Cache para os casos Redis e ajudá-lo a gerir as suas aplicações de cache. Para mais informações, consulte a secção "Suporte & configurações de resolução de problemas" de [Como configurar o Azure Cache para Redis](cache-configure.md).

<a name="cache-timeouts"></a>

### <a name="why-am-i-seeing-timeouts"></a>Porque é que estou a ver intervalos?
Os intervalos acontecem no cliente que usas para falar com o Redis. Quando um comando é enviado para o servidor Redis, o comando é feito em fila e o servidor Redis eventualmente apanha o comando e executa-o. No entanto, o cliente pode sair durante este processo e se o fizer uma exceção é levantada no lado da chamada. Para obter mais informações sobre problemas de resolução de tempo, consulte as exceções de [timeout do lado do cliente](cache-troubleshoot-client.md) e [StackExchange.Redis](cache-troubleshoot-timeouts.md#stackexchangeredis-timeout-exceptions).

<a name="cache-disconnect"></a>

### <a name="why-was-my-client-disconnected-from-the-cache"></a>Porque é que o meu cliente foi desligado da cache?
Seguem-se alguma razão comum para uma desconexão de cache.

* Causas do lado do cliente
  * A aplicação do cliente foi redistribuída.
  * A aplicação do cliente realizou uma operação de escala.
    * No caso dos Serviços cloud ou aplicações web, isto pode ser devido a autoscalcificação.
  * A camada de rede do lado do cliente mudou.
  * Erros transitórios ocorreram no cliente ou nos nós de rede entre o cliente e o servidor.
  * Os limites de limiar de largura de banda foram atingidos.
  * As operações ligadas à CPU demoraram muito tempo a ser concluídas.
* Causas do lado do servidor
  * Na oferta padrão de cache, o serviço Azure Cache for Redis iniciou uma falha do nó primário para o nó secundário.
  * O Azure estava a remendar o caso em que a cache foi implantada.
    * Isto pode ser para atualizações do servidor Redis ou manutenção geral vm.

### <a name="which-azure-cache-offering-is-right-for-me"></a>Que oferta de Azure Cache é certa para mim?
> [!IMPORTANT]
> De acordo com o [anúncio](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)do ano passado, o Azure Managed Cache Service e o serviço Azure In-Role Cache **foram retirados** a 30 de novembro de 2016. A nossa recomendação é usar [o Azure Cache para redis.](https://azure.microsoft.com/services/cache/) Para obter informações sobre a migração, consulte [Migrate do Serviço de Cache Gerido para Azure Cache for Redis](cache-migrate-to-redis.md).
>
>

### <a name="azure-cache-for-redis"></a>Cache do Azure para Redis
O Azure Cache for Redis está geralmente disponível em tamanhos até 120 GB e tem uma disponibilidade de SLA de 99,9%. O novo [nível premium](cache-premium-tier-intro.md) oferece tamanhos até 1,2 TB e suporte para clustering, VNET e persistência, com um SLA de 99,9%.

O Azure Cache para a Redis dá aos clientes a capacidade de utilizar um Azure Cache para Redis, seguro e dedicado, gerido pela Microsoft. Com esta oferta, você pode aproveitar o conjunto de funcionalidades ricas e o ecossistema fornecido pela Redis, e hospedagem e monitorização confiáveis da Microsoft.

Ao contrário dos caches tradicionais que lidam apenas com pares de valor-chave, redis é popular pelos seus tipos de dados altamente performantes. A Redis também suporta a execução de operações atómicas neste tipo, como a despesa com uma corda; incrementando o valor num hash; empurrando para uma lista; cruzamento de conjuntode computação, união e diferença; ou obter o membro com a classificação mais alta em um conjunto classificado. Outras funcionalidades incluem suporte para transações, pub/sub, scripts Lua, chaves com um tempo limitado para viver, e configurações de configuração para fazer redis comportar-se mais como um cache tradicional.

Outro aspeto fundamental para o sucesso da Redis é o saudável e vibrante ecossistema de código aberto construído à sua volta. Isto reflete-se no conjunto diversificado de clientes Redis disponíveis em várias línguas. Este ecossistema e uma vasta gama de clientes permitem que o Azure Cache para redis seja usado por quase toda a carga de trabalho que construiria dentro do Azure.

Para mais informações sobre o início com Azure Cache for Redis, consulte [Como Usar o Cache Azure para Redis](cache-dotnet-how-to-use-azure-redis-cache.md) e [Azure Cache para documentação redis.](index.yml)

### <a name="managed-cache-service"></a>Serviço cache gerido
[O serviço de Cache gerido foi retirado a 30 de novembro de 2016.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

Para ver a documentação arquivada, consulte a Documentação do Serviço de [Cache Gerida Arquivada](/previous-versions/azure/azure-services/dn386094(v=azure.100)).

### <a name="in-role-cache"></a>Cache em Papel
[In-Role Cache foi reformado a 30 de novembro de 2016.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

Para ver a documentação arquivada, consulte documentação de [cache arquivada em funções.](/previous-versions/azure/azure-services/dn386103(v=azure.100))

["minIoThreads" configuration setting]: https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx
