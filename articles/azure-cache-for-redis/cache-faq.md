---
title: Perguntas frequentes sobre o cache do Azure para Redis | Microsoft Docs
description: Conheça as respostas a perguntas comuns, padrões e práticas recomendadas para o cache do Azure para Redis
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
ms.date: 04/29/2019
ms.author: yegu
ms.openlocfilehash: 42d0d7dcc4e10e6f9bfad02a68f3ec176b8a7fb4
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71316000"
---
# <a name="azure-cache-for-redis-faq"></a>FAQ da Cache do Azure para Redis
Conheça as respostas a perguntas comuns, padrões e práticas recomendadas para o cache do Azure para Redis.

## <a name="what-if-my-question-isnt-answered-here"></a>E se minha pergunta não for respondida aqui?
Se sua pergunta não estiver listada aqui, informe-nos e ajudaremos você a encontrar uma resposta.

* Você pode postar uma pergunta nos comentários no final destas perguntas frequentes e entrar em contato com a equipe de cache do Azure e outros membros da Comunidade sobre este artigo.
* Para alcançar um público mais amplo, você pode postar uma pergunta no [Fórum do MSDN do cache do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=azurecache) e entrar em contato com a equipe de cache do Azure e outros membros da Comunidade.
* Se você quiser fazer uma solicitação de recurso, poderá enviar suas solicitações e ideias para o [cache do Azure para Redis User Voice](https://feedback.azure.com/forums/169382-cache).
* Você também pode enviar um email para nós em [comentários externos do cache do Azure](mailto:azurecache@microsoft.com).

## <a name="azure-cache-for-redis-basics"></a>Cache do Azure para noções básicas do Redis
As perguntas frequentes nesta seção abrangem alguns dos fundamentos do cache do Azure para Redis.

* [O que é o cache do Azure para Redis?](#what-is-azure-cache-for-redis)
* [Como posso começar a usar o cache do Azure para Redis?](#how-can-i-get-started-with-azure-cache-for-redis)

As perguntas frequentes a seguir abordam os conceitos básicos e as perguntas sobre o cache do Azure para Redis e são respondidas em uma das outras seções de perguntas frequentes.

* [Qual o cache do Azure para oferta e tamanho do Redis devo usar?](#what-azure-cache-for-redis-offering-and-size-should-i-use)
* [Qual cache do Azure para clientes do Redis posso usar?](#what-azure-cache-for-redis-clients-can-i-use)
* [Há um emulador local para o cache do Azure para Redis?](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [Como fazer monitorar a integridade e o desempenho do meu cache?](#how-do-i-monitor-the-health-and-performance-of-my-cache)

## <a name="planning-faqs"></a>Perguntas frequentes sobre planejamento
* [Qual o cache do Azure para oferta e tamanho do Redis devo usar?](#what-azure-cache-for-redis-offering-and-size-should-i-use)
* [Cache do Azure para desempenho Redis](#azure-cache-for-redis-performance)
* [Em qual região devo localizar meu cache?](#in-what-region-should-i-locate-my-cache)
* [Como sou cobrado pelo cache do Azure para Redis?](#how-am-i-billed-for-azure-cache-for-redis)
* [Posso usar o cache do Azure para Redis com a nuvem do Azure governamental, a nuvem do Azure na China ou o Microsoft Azure Alemanha?](#can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany)

## <a name="development-faqs"></a>Perguntas frequentes sobre desenvolvimento
* [O que as opções de configuração StackExchange. Redis fazem?](#what-do-the-stackexchangeredis-configuration-options-do)
* [Qual cache do Azure para clientes do Redis posso usar?](#what-azure-cache-for-redis-clients-can-i-use)
* [Há um emulador local para o cache do Azure para Redis?](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [Como posso executar comandos do Redis?](#how-can-i-run-redis-commands)
* [Por que o cache do Azure para Redis tem uma referência da biblioteca de classes do MSDN como alguns dos outros serviços do Azure?](#why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services)
* [Posso usar o cache do Azure para Redis como um cache de sessão do PHP?](#can-i-use-azure-cache-for-redis-as-a-php-session-cache)
* [O que são bancos de dados Redis?](#what-are-redis-databases)

## <a name="security-faqs"></a>Perguntas frequentes sobre segurança
* [Quando devo habilitar a porta não SSL para se conectar ao Redis?](#when-should-i-enable-the-non-ssl-port-for-connecting-to-redis)

## <a name="production-faqs"></a>Perguntas frequentes sobre produção
* [O que são algumas práticas recomendadas de produção?](#what-are-some-production-best-practices)
* [Quais são algumas das considerações ao usar comandos comuns do Redis?](#what-are-some-of-the-considerations-when-using-common-redis-commands)
* [Como fazer o benchmark e testar o desempenho do meu cache?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [Detalhes importantes sobre o crescimento de ThreadPool](#important-details-about-threadpool-growth)
* [Habilite o GC do servidor para obter mais taxa de transferência no cliente ao usar StackExchange. Redis](#enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis)
* [Considerações sobre desempenho em relação a conexões](#performance-considerations-around-connections)

## <a name="monitoring-and-troubleshooting-faqs"></a>Perguntas frequentes sobre monitoramento e solução de problemas
As perguntas frequentes nesta seção abordam perguntas comuns de monitoramento e solução de problemas. Para obter mais informações sobre como monitorar e solucionar problemas do cache do Azure para instâncias do Redis, consulte [como monitorar o cache do Azure para Redis](cache-how-to-monitor.md) e [como solucionar problemas do cache do Azure para Redis](cache-how-to-troubleshoot.md).

* [Como fazer monitorar a integridade e o desempenho do meu cache?](#how-do-i-monitor-the-health-and-performance-of-my-cache)
* [Por que estou vendo tempos limite?](#why-am-i-seeing-timeouts)
* [Por que meu cliente foi desconectado do cache?](#why-was-my-client-disconnected-from-the-cache)

## <a name="prior-cache-offering-faqs"></a>Perguntas frequentes sobre a oferta de cache anterior
* [Qual oferta de cache do Azure é adequada para mim?](#which-azure-cache-offering-is-right-for-me)

### <a name="what-is-azure-cache-for-redis"></a>O que é o cache do Azure para Redis?
O cache do Azure para Redis é baseado no popular [Redis](https://redis.io/)de software livre. Ele fornece acesso a um cache do Azure seguro e dedicado para Redis, gerenciado pela Microsoft e acessível de qualquer aplicativo no Azure. Para obter uma visão geral mais detalhada, consulte a página de produto [cache do Azure para Redis](https://azure.microsoft.com/services/cache/) no Azure.com.

### <a name="how-can-i-get-started-with-azure-cache-for-redis"></a>Como posso começar a usar o cache do Azure para Redis?
Há várias maneiras de começar a usar o cache do Azure para Redis.

* Você pode conferir um dos nossos tutoriais disponíveis para [.net](cache-dotnet-how-to-use-azure-redis-cache.md), [ASP.net](cache-web-app-howto.md), [Java](cache-java-get-started.md), [node. js](cache-nodejs-get-started.md)e [Python](cache-python-get-started.md).
* Você pode assistir [a criar aplicativos de alto desempenho usando o Cache Microsoft Azure para Redis](https://azure.microsoft.com/documentation/videos/how-to-build-high-performance-apps-using-microsoft-azure-cache/).
* Você pode conferir a documentação do cliente para os clientes que correspondem à linguagem de desenvolvimento do seu projeto para ver como usar o Redis. Há muitos clientes Redis que podem ser usados com o cache do Azure para Redis. Para obter uma lista de clientes do Redis [https://redis.io/clients](https://redis.io/clients), consulte.

Se você ainda não tiver uma conta do Azure, poderá:

* [Criar uma conta do Azure gratuitamente](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero). Receberá créditos que podem ser utilizados para experimentar os serviços pagos do Azure. Mesmo depois de gastar todos os créditos, pode manter a conta e utilizar os serviços e recursos gratuitos do Azure.
* [Ativar os benefícios de subscritor do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). A sua subscrição do MSDN dá-lhe créditos todos os meses que pode utilizar em serviços pagos do Azure.

<a name="cache-size"></a>

### <a name="what-azure-cache-for-redis-offering-and-size-should-i-use"></a>Qual o cache do Azure para oferta e tamanho do Redis devo usar?
Cada cache do Azure para oferta de Redis fornece níveis diferentes de **tamanho**, **largura de banda**, **alta disponibilidade**e opções de **SLA** .

Veja a seguir as considerações para escolher uma oferta de cache.

* **Memória**: As camadas básica e standard oferecem 250 MB a 53 GB. A camada Premium oferece até 1,2 TB (como um cluster) ou 120 GB (não clusterizado). Para obter mais informações, consulte [Azure cache for Redis Pricing](https://azure.microsoft.com/pricing/details/cache/).
* **Desempenho de rede**: Se você tiver uma carga de trabalho que exija alta taxa de transferência, a camada Premium oferecerá mais largura de banda em comparação com o Standard ou Basic. Além disso, dentro de cada camada, caches de tamanho maior têm mais largura de banda devido à VM subjacente que hospeda o cache. Para obter mais informações, consulte a [tabela a seguir](#cache-performance).
* **Taxa de transferência**: A camada Premium oferece a taxa de transferência máxima disponível. Se o servidor de cache ou o cliente atingir os limites de largura de banda, você poderá receber tempos limite no lado do cliente. Para obter mais informações, consulte a tabela a seguir.
* **Alta disponibilidade/SLA**: O cache do Azure para Redis garante que um cache Standard/Premium esteja disponível em pelo menos 99,9% do tempo. Para saber mais sobre nosso SLA, veja [preços do cache do Azure para Redis](https://azure.microsoft.com/support/legal/sla/cache/v1_0/). O SLA cobre apenas a conectividade com os pontos de extremidade do cache. O SLA não engloba a proteção contra perda de dados. É recomendável usar o recurso de persistência de dados do Redis na camada Premium para aumentar a resiliência contra perda de dados.
* **Persistência de dados Redis**: A camada Premium permite que você persista os dados de cache em uma conta de armazenamento do Azure. Em um cache básico/Standard, todos os dados são armazenados apenas na memória. Problemas de infraestrutura subjacentes podem resultar em potencial perda de dados. É recomendável usar o recurso de persistência de dados do Redis na camada Premium para aumentar a resiliência contra perda de dados. O cache do Azure para Redis oferece opções de RDB e AOF (em breve) no Redis persistence. Para obter mais informações, consulte [como configurar a persistência para um cache do Azure Premium para Redis](cache-how-to-premium-persistence.md).
* **Cluster Redis**: Para criar caches com mais de 120 GB ou para fragmentar dados em vários nós do Redis, você pode usar o clustering do Redis, que está disponível na camada Premium. Cada nó consiste em um par de cache primário/de réplica para alta disponibilidade. Para obter mais informações, consulte [como configurar o clustering para um cache Premium do Azure para Redis](cache-how-to-premium-clustering.md).
* **Segurança aprimorada e isolamento de rede**: A implantação da VNET (rede virtual) do Azure fornece segurança e isolamento aprimorados para o cache do Azure para Redis, bem como para sub-redes, políticas de controle de acesso e outros recursos para restringir ainda mais o acesso. Para obter mais informações, consulte [como configurar o suporte de rede virtual para um cache do Azure Premium para Redis](cache-how-to-premium-vnet.md).
* **Configurar Redis**: Nas camadas Standard e Premium, você pode configurar o Redis para notificações de keyspace.
* **Número máximo de conexões de cliente**: A camada Premium oferece o número máximo de clientes que podem se conectar ao Redis, com um número maior de conexões para caches de tamanho maior. O clustering não aumenta o número de conexões disponíveis para um cache clusterizado. Para obter mais informações, consulte [Azure cache for Redis Pricing](https://azure.microsoft.com/pricing/details/cache/).
* **Núcleo dedicado para o servidor Redis**: Na camada Premium, todos os tamanhos de cache têm um núcleo dedicado para Redis. Nas camadas básica/Standard, o tamanho C1 e acima têm um núcleo dedicado para o servidor Redis.
* O **Redis é de thread único** , então ter mais de dois núcleos não oferece um benefício adicional em ter apenas dois núcleos, mas tamanhos de VM maiores normalmente têm mais largura de banda do que tamanhos menores. Se o servidor de cache ou o cliente atingir os limites de largura de banda, você receberá tempos limite no lado do cliente.
* **Aprimoramentos de desempenho**: Os caches na camada Premium são implantados em hardware que tem processadores mais rápidos, proporcionando melhor desempenho em comparação com a camada básica ou Standard. Os caches da camada Premium têm mais taxa de transferência e latências menores.

<a name="cache-performance"></a>

### <a name="azure-cache-for-redis-performance"></a>Cache do Azure para desempenho Redis
A tabela a seguir mostra os valores máximos de largura de banda observados durante o teste de vários `redis-benchmark.exe` tamanhos de caches padrão e Premium usando uma VM IaaS no cache do Azure para o ponto de extremidade Redis. Para a taxa de transferência SSL, Redis-benchmark é usado com stunnel para se conectar ao cache do Azure para o ponto de extremidade Redis.

>[!NOTE] 
>Esses valores não são garantidos e não há SLA para esses números, mas devem ser típicos. Você deve testar o teste de seu próprio aplicativo para determinar o tamanho de cache certo para seu aplicativo.
>Esses números podem mudar à medida que publicamos os resultados mais recentes periodicamente.
>

A partir desta tabela, podemos desenhar as seguintes conclusões:

* A taxa de transferência para os caches que têm o mesmo tamanho é maior na camada Premium em comparação com a camada Standard. Por exemplo, com um cache de 6 GB, a taxa de transferência de P1 é de 180.000 solicitações por segundo (RPS) em comparação com 100.000 RPS para C3.
* Com o clustering Redis, a taxa de transferência aumenta linearmente à medida que você aumenta o número de fragmentos (nós) no cluster. Por exemplo, se você criar um cluster P4 de 10 fragmentos, a taxa de transferência disponível será 400.000 * 10 = 4 milhões RPS.
* A taxa de transferência para maiores tamanhos de chave é maior na camada Premium em comparação com a camada Standard.

| Escalão de preço | Size | Núcleos de CPU | Largura de banda disponível | tamanho do valor de 1 KB | tamanho do valor de 1 KB |
| --- | --- | --- | --- | --- | --- |
| **Tamanhos de cache padrão** | | |**Megabits por segundo (MB/s)/megabytes por segundo (MB/s)** |**RPS (solicitações por segundo) não SSL** |**SSL de solicitações por segundo (RPS)** |
| C0 | 250 MB | Partilhado | 100 / 12.5  |  15,000 |   7,500 |
| C1 |   1 GB | 1      | 500 / 62.5  |  38.000 |  20.720 |
| C2 | 2,5 GB | 2      | 500 / 62.5  |  41.000 |  37.000 |
| C3 |   6 GB | 4      | 1000 / 125  | 100,000 |  90,000 |
| C4 |  13 GB | 2      | 500 / 62.5  |  60,000 |  55.000 |
| C5 |  26 GB | 4      | 1,000 / 125 | 102.000 |  93.000 |
| C6 |  53 GB | 8      | 2,000 / 250 | 126.000 | 120,000 |
| **Tamanhos de cache Premium** | |**Núcleos de CPU por fragmento** | **Megabits por segundo (MB/s)/megabytes por segundo (MB/s)** |**RPS (solicitações por segundo) não SSL, por fragmento** |**SSL de solicitações por segundo (RPS), por fragmento** |
| P1 |   6 GB |  2 | 1,500 / 187.5 | 180,000 | 172.000 |
| P2 |  13 GB |  4 | 3,000 / 375   | 350.000 | 341.000 |
| P3 |  26 GB |  4 | 3,000 / 375   | 350.000 | 341.000 |
| P4 |  53 GB |  8 | 6,000 / 750   | 400,000 | 373.000 |
| P5 | 120 GB | 20 | 6,000 / 750   | 400,000 | 373.000 |

Para obter instruções sobre como configurar o stunnel ou baixar as ferramentas `redis-benchmark.exe`do Redis, como, consulte a seção [como posso executar comandos do Redis?](#cache-commands) .

<a name="cache-region"></a>

### <a name="in-what-region-should-i-locate-my-cache"></a>Em qual região devo localizar meu cache?
Para obter o melhor desempenho e a menor latência, localize o cache do Azure para Redis na mesma região que o aplicativo cliente de cache.

<a name="cache-billing"></a>

### <a name="how-am-i-billed-for-azure-cache-for-redis"></a>Como sou cobrado pelo cache do Azure para Redis?
O cache do Azure para o preço do Redis está [aqui](https://azure.microsoft.com/pricing/details/cache/). A página de preços lista os preços como uma taxa por hora. Os caches são cobrados por minuto a partir do momento em que o cache é criado até o momento em que um cache é excluído. Não há nenhuma opção para parar ou pausar a cobrança de um cache.

### <a name="can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany"></a>Posso usar o cache do Azure para Redis com a nuvem do Azure governamental, a nuvem do Azure na China ou o Microsoft Azure Alemanha?
Sim, o cache do Azure para Redis está disponível na nuvem do Azure governamental, na nuvem do Azure China 21Vianet e Microsoft Azure Alemanha. As URLs para acessar e gerenciar o cache do Azure para Redis são diferentes nessas nuvens em comparação com a nuvem pública do Azure.

| Nuvem   | Sufixo DNS para Redis            |
|---------|---------------------------------|
| Público  | *.redis.cache.windows.net       |
| US Gov  | *.redis.cache.usgovcloudapi.net |
| Alemanha | *.redis.cache.cloudapi.de       |
| China   | *.redis.cache.chinacloudapi.cn  |

Para obter mais informações sobre considerações ao usar o cache do Azure para Redis com outras nuvens, consulte os links a seguir.

- [Bancos de dados do Azure governamental-cache do Azure para Redis](../azure-government/documentation-government-services-database.md#azure-cache-for-redis)
- [Azure China 21Vianet Cloud-cache do Azure para Redis](https://www.azure.cn/home/features/redis-cache/)
- [Microsoft Azure Alemanha](https://azure.microsoft.com/overview/clouds/germany/)

Para obter informações sobre como usar o cache do Azure para Redis com o PowerShell na nuvem do Azure governamental, nuvem do Azure China 21Vianet e Microsoft Azure Alemanha, consulte [como se conectar a outras nuvens-cache do Azure para Redis PowerShell](cache-howto-manage-redis-cache-powershell.md#how-to-connect-to-other-clouds).

<a name="cache-configuration"></a>

### <a name="what-do-the-stackexchangeredis-configuration-options-do"></a>O que as opções de configuração StackExchange. Redis fazem?
StackExchange. Redis tem muitas opções. Esta seção aborda algumas das configurações comuns. Para obter informações mais detalhadas sobre as opções StackExchange. Redis, consulte [configuração de stackexchange. Redis](https://stackexchange.github.io/StackExchange.Redis/Configuration).

| ConfigurationOptions | Descrição | Recomendação |
| --- | --- | --- |
| AbortOnConnectFail |Quando definido como true, a conexão não será reconectada após uma falha de rede. |Defina como false e permita que StackExchange. Redis reconecte automaticamente. |
| ConnectRetry |O número de vezes para repetir tentativas de conexão durante a conexão inicial. |Consulte as notas a seguir para obter diretrizes. |
| ConnectTimeout |Tempo limite em MS para operações de conexão. |Consulte as notas a seguir para obter diretrizes. |

Normalmente, os valores padrão do cliente são suficientes. Você pode ajustar as opções com base em sua carga de trabalho.

* **Tentativas**
  * Para ConnectRetry e ConnectTimeout, as diretrizes gerais são fail fast e tentar novamente. Essa orientação se baseia em sua carga de trabalho e em quanto tempo em média leva para o cliente emitir um comando Redis e receber uma resposta.
  * Permita que o StackExchange. Redis se reconecte automaticamente em vez de verificar o status da conexão e se reconectar. **Evite usar a propriedade ConnectionMultiplexer. IsConnected**.
  * Bola – às vezes, você pode encontrar um problema em que está tentando novamente e as novas tentativas bola e nunca são recuperadas. Se bola ocorrer, você deverá considerar o uso de um algoritmo de repetição de retirada exponencial, conforme descrito em [diretrizes gerais de repetição](../best-practices-retry-general.md) publicadas pelo grupo de práticas de & de padrões da Microsoft.
* **Valores de tempo limite**
  * Considere sua carga de trabalho e defina os valores de acordo. Se você estiver armazenando valores grandes, defina o tempo limite como um valor mais alto.
  * Defina `AbortOnConnectFail` como false e deixe o stackexchange. Redis reconectar para você.
  * Use uma única instância de ConnectionMultiplexer para o aplicativo. Você pode usar um LazyConnection para criar uma única instância que é retornada por uma propriedade de conexão, conforme mostrado em [conectar-se ao cache usando a classe ConnectionMultiplexer](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).
  * Defina a `ConnectionMultiplexer.ClientName` Propriedade como um nome exclusivo da instância de aplicativo para fins de diagnóstico.
  * Use várias `ConnectionMultiplexer` instâncias para cargas de trabalho personalizadas.
      * Você pode seguir esse modelo se tiver uma carga variável em seu aplicativo. Por exemplo:
      * Você pode ter um multiplexador para lidar com chaves grandes.
      * Você pode ter um multiplexador para lidar com chaves pequenas.
      * Você pode definir valores diferentes para tempos limite de conexão e lógica de repetição para cada ConnectionMultiplexer que você usar.
      * Defina a `ClientName` Propriedade em cada multiplexador para ajudar com o diagnóstico.
      * Essas diretrizes podem levar a uma latência mais simplificada `ConnectionMultiplexer`por.

### <a name="what-azure-cache-for-redis-clients-can-i-use"></a>Qual cache do Azure para clientes do Redis posso usar?
Uma das ótimas coisas sobre o Redis é que há muitos clientes oferecendo suporte a várias linguagens de desenvolvimento diferentes. Para obter uma lista atual de clientes, consulte [clientes do Redis](https://redis.io/clients). Para obter tutoriais que abrangem várias linguagens e clientes diferentes, consulte [como usar o cache do Azure para Redis](cache-dotnet-how-to-use-azure-redis-cache.md) e seus artigos irmãos no sumário.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

<a name="cache-emulator"></a>

### <a name="is-there-a-local-emulator-for-azure-cache-for-redis"></a>Há um emulador local para o cache do Azure para Redis?
Não há um emulador local para o cache do Azure para Redis, mas você pode executar a versão MSOpenTech do Redis-Server. exe nas [ferramentas de linha de comando do Redis](https://github.com/MSOpenTech/redis/releases/) em seu computador local e conectar-se a ela para obter uma experiência semelhante a um emulador de cache local, conforme mostrado no exemplo a seguir:

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


Opcionalmente, você pode configurar um arquivo [Redis. conf](https://redis.io/topics/config) para corresponder melhor às [configurações de cache padrão](cache-configure.md#default-redis-server-configuration) para o cache do Azure online para Redis, se desejado.

<a name="cache-commands"></a>

### <a name="how-can-i-run-redis-commands"></a>Como posso executar comandos do Redis?
Você pode usar qualquer um dos comandos listados em [comandos Redis](https://redis.io/commands#) , exceto pelos comandos listados em [comandos Redis sem suporte no cache do Azure para Redis](cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis). Você tem várias opções para executar comandos Redis.

* Se você tiver um cache Standard ou Premium, poderá executar comandos Redis usando o [console do Redis](cache-configure.md#redis-console). O console do Redis fornece uma maneira segura de executar comandos do Redis no portal do Azure.
* Você também pode usar as ferramentas de linha de comando do Redis. Para usá-los, execute as seguintes etapas:
* Baixe as [ferramentas de linha de comando do Redis](https://github.com/MSOpenTech/redis/releases/).
* Conecte-se ao cache `redis-cli.exe`usando. Passe o ponto de extremidade do cache usando a opção-h e a chave usando-a, conforme mostrado no exemplo a seguir:
* `redis-cli -h <Azure Cache for Redis name>.redis.cache.windows.net -a <key>`

> [!NOTE]
> As ferramentas de linha de comando Redis não funcionam com a porta SSL, mas você pode usar um utilitário `stunnel` como para conectar com segurança as ferramentas à porta SSL seguindo as instruções no artigo [como usar a ferramenta de linha de comando Redis com o cache do Azure para Redis](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-redis-cli-tool) .
>
>

<a name="cache-reference"></a>

### <a name="why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services"></a>Por que o cache do Azure para Redis tem uma referência da biblioteca de classes do MSDN como alguns dos outros serviços do Azure?
O cache Microsoft Azure para Redis é baseado no popular cache do Azure de software livre para Redis. Ele pode ser acessado por uma ampla variedade de [clientes Redis](https://redis.io/clients) para muitas linguagens de programação. Cada cliente tem sua própria API que faz chamadas para o cache do Azure para a instância Redis usando [comandos Redis](https://redis.io/commands).

Como cada cliente é diferente, não há uma referência de classe centralizada no MSDN, e cada cliente mantém sua própria documentação de referência. Além da documentação de referência, há vários tutoriais que mostram como começar a usar o cache do Azure para Redis usando diferentes idiomas e clientes de cache. Para acessar esses tutoriais, consulte [como usar o cache do Azure para Redis](cache-dotnet-how-to-use-azure-redis-cache.md) e seus artigos irmãos no sumário.

### <a name="can-i-use-azure-cache-for-redis-as-a-php-session-cache"></a>Posso usar o cache do Azure para Redis como um cache de sessão do PHP?
Sim, para usar o cache do Azure para Redis como um cache de sessão do PHP, especifique a cadeia de conexão para o cache `session.save_path`do Azure para a instância Redis no.

> [!IMPORTANT]
> Ao usar o cache do Azure para Redis como um cache de sessão do PHP, você deve codificar por URL a chave de segurança usada para se conectar ao cache, conforme mostrado no exemplo a seguir:
>
> `session.save_path = "tcp://mycache.redis.cache.windows.net:6379?auth=<url encoded primary or secondary key here>";`
>
> Se a chave não for codificada em URL, você poderá receber uma exceção com uma mensagem como:`Failed to parse session.save_path`
>
>

Para obter mais informações sobre como usar o cache do Azure para Redis como um cache de sessão do PHP com o cliente PhpRedis, consulte [manipulador de sessão do PHP](https://github.com/phpredis/phpredis#php-session-handler).

### <a name="what-are-redis-databases"></a>O que são bancos de dados Redis?

Os bancos de dados do Redis são apenas uma separação lógica de data dentro da mesma instância Redis. A memória de cache é compartilhada entre todos os bancos de dados e o consumo real de memória de um determinado banco de dado depende das chaves/valores armazenados nesse banco de dados. Por exemplo, um cache C6 tem 53 GB de memória e um P5 tem 120 GB. Você pode optar por colocar todos os 53 GB/120 GB em um banco de dados ou pode dividi-los entre vários bancos de dados. 

> [!NOTE]
> Ao usar um cache do Azure Premium para Redis com clustering habilitado, somente o banco de dados 0 está disponível. Essa limitação é uma limitação Redis intrínseca e não é específica do cache do Azure para Redis. Para obter mais informações, consulte [preciso fazer qualquer alteração no meu aplicativo cliente para usar o clustering?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 


<a name="cache-ssl"></a>

### <a name="when-should-i-enable-the-non-ssl-port-for-connecting-to-redis"></a>Quando devo habilitar a porta não SSL para se conectar ao Redis?
O servidor Redis não dá suporte nativo a SSL, mas o cache do Azure para Redis. Se você estiver se conectando ao cache do Azure para Redis e seu cliente oferecer suporte a SSL, como StackExchange. Redis, você deve usar SSL.

>[!NOTE]
>A porta não SSL é desabilitada por padrão para o novo cache do Azure para instâncias de Redis. Se o cliente não oferecer suporte a SSL, você deverá habilitar a porta não SSL seguindo as instruções na seção portas de [acesso](cache-configure.md#access-ports) do artigo [configurar um cache no cache do Azure para Redis](cache-configure.md) .
>
>

Ferramentas de Redis como `redis-cli` não funcionam com a porta SSL, mas você pode usar um utilitário `stunnel` como para conectar com segurança as ferramentas à porta SSL seguindo as instruções na [versão prévia do provedor de estado de sessão ASP.net para Redis ](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)postagem no blog.

Para obter instruções sobre como baixar as ferramentas do Redis, consulte a seção [como posso executar comandos do Redis?](#cache-commands)

### <a name="what-are-some-production-best-practices"></a>O que são algumas práticas recomendadas de produção?
* [Práticas recomendadas do StackExchange. Redis](#stackexchangeredis-best-practices)
* [Configuração e conceitos](#configuration-and-concepts)
* [Teste de desempenho](#performance-testing)

#### <a name="stackexchangeredis-best-practices"></a>Práticas recomendadas do StackExchange. Redis
* Defina `AbortConnect` como false e, em seguida, deixe o ConnectionMultiplexer se reconectar automaticamente. [Consulte aqui para obter detalhes](https://gist.github.com/JonCole/36ba6f60c274e89014dd#file-se-redis-setabortconnecttofalse-md).
* Reutilize o ConnectionMultiplexer-não crie um novo para cada solicitação. O `Lazy<ConnectionMultiplexer>` padrão [mostrado aqui](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache) é recomendado.
* O Redis funciona melhor com valores menores, portanto, considere a possibilidade de aumentar dados maiores em várias chaves. Nesta [discussão de Redis](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ), 100 KB é considerado grande. Leia [Este artigo](https://gist.github.com/JonCole/db0e90bedeb3fc4823c2#large-requestresponse-size) para obter um problema de exemplo que pode ser causado por valores grandes.
* Defina [as configurações de ThreadPool](#important-details-about-threadpool-growth) para evitar tempos limite.
* Use pelo menos o connectTimeout padrão de 5 segundos. Esse intervalo dá ao StackExchange. Redis tempo suficiente para restabelecer a conexão no caso de uma rede Blip.
* Esteja atento aos custos de desempenho associados a diferentes operações que você está executando. Por exemplo, o `KEYS` comando é uma operação O (n) e deve ser evitado. O [site do Redis.Io](https://redis.io/commands/) tem detalhes sobre a complexidade do tempo para cada operação que ele suporta. Clique em cada comando para ver a complexidade de cada operação.

#### <a name="configuration-and-concepts"></a>Configuração e conceitos
* Use a camada Standard ou Premium para sistemas de produção. A camada básica é um sistema de nó único sem replicação de dados e nenhum SLA. Além disso, use pelo menos um cache C1. Caches C0 normalmente são usados para cenários simples de desenvolvimento/teste.
* Lembre-se de que Redis é um armazenamento **de dados na memória** . Leia [Este artigo](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) para que você esteja ciente dos cenários em que podem ocorrer perda de dados.
* Desenvolva seu sistema de modo que ele possa lidar com blips de conexão [devido a patches e failover](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md).

#### <a name="performance-testing"></a>Teste de desempenho
* Comece usando `redis-benchmark.exe` o para ter uma ideia de possível taxa de transferência antes de escrever seus próprios testes de desempenho. Como `redis-benchmark` o não oferece suporte a SSL, você deve [habilitar a porta não SSL por meio do portal do Azure antes de](cache-configure.md#access-ports) executar o teste. Para obter exemplos, consulte [como posso avaliar e testar o desempenho do meu cache?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* A VM do cliente usada para teste deve estar na mesma região que o cache do Azure para a instância Redis.
* É recomendável usar a série de VMs Dv2 para seu cliente, pois ele tem um hardware melhor e deve fornecer os melhores resultados.
* Verifique se a VM do cliente escolhida tem pelo menos tanto capacidade de computação quanto de largura de banda quanto o cache que você está testando.
* Habilite o VRSS no computador cliente se você estiver no Windows. [Consulte aqui para obter detalhes](https://technet.microsoft.com/library/dn383582.aspx).
* As instâncias de Redis da camada Premium têm melhor latência de rede e taxa de transferência porque estão em execução em hardware melhor para CPU e rede.

<a name="cache-redis-commands"></a>

### <a name="what-are-some-of-the-considerations-when-using-common-redis-commands"></a>Quais são algumas das considerações ao usar comandos comuns do Redis?

* Evite usar determinados comandos Redis que levam muito tempo para serem concluídos, a menos que você entenda totalmente o impacto desses comandos. Por exemplo, não execute o comando [Keys](https://redis.io/commands/keys) em produção. Dependendo do número de chaves, pode levar muito tempo para retornar. Redis é um servidor de thread único e processa comandos um de cada vez. Se você tiver outros comandos emitidos após as chaves, eles não serão processados até que o Redis processe o comando KEYS. O [site do Redis.Io](https://redis.io/commands/) tem detalhes sobre a complexidade do tempo para cada operação que ele suporta. Clique em cada comando para ver a complexidade de cada operação.
* Tamanhos de chave – devo usar pequenos valores/chave ou valores/chave grandes? Depende do cenário. Se seu cenário exigir chaves maiores, você poderá ajustar o ConnectionTimeout e, em seguida, repetir os valores e ajustar sua lógica de repetição. Do ponto de vista do Redis Server, valores menores oferecem melhor desempenho.
* Essas considerações não significam que você não pode armazenar valores maiores em Redis; Você deve estar ciente das considerações a seguir. As latências serão mais altas. Se você tiver um conjunto de dados maior e outro menor, poderá usar várias instâncias de ConnectionMultiplexer, cada uma configurada com um conjunto diferente de valores de tempo limite e de repetição, conforme descrito no anterior, o [que fazer a configuração stackexchange. Redis opções](#cache-configuration) da seção.

<a name="cache-benchmarking"></a>

### <a name="how-can-i-benchmark-and-test-the-performance-of-my-cache"></a>Como fazer o benchmark e testar o desempenho do meu cache?
* [Ativar o diagnóstico da cache](cache-how-to-monitor.md#enable-cache-diagnostics) para poder [monitorizar](cache-how-to-monitor.md) o estado de funcionamento da cache. Pode ver as métricas no portal do Azure e [transferi-las e revê-las](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) com as ferramentas da sua escolha.
* Você pode usar Redis-benchmark. exe para testar a carga do servidor Redis.
* Verifique se o cliente de teste de carga e o cache do Azure para Redis estão na mesma região.
* Use Redis-CLI. exe e monitore o cache usando o comando INFO.
* Se a carga estiver causando alta fragmentação de memória, você deverá escalar verticalmente para um tamanho de cache maior.
* Para obter instruções sobre como baixar as ferramentas do Redis, consulte a seção [como posso executar comandos do Redis?](#cache-commands)

Os comandos a seguir fornecem um exemplo de como usar Redis-benchmark. exe. Para obter resultados precisos, execute estes comandos de uma VM na mesma região que o cache.

* Testar solicitações de conjunto em pipeline usando uma carga de 1K

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t SET -n 1000000 -d 1024 -P 50`
* Teste solicitações GET em pipeline usando uma carga de 1K.
  NOTA: Execute o teste SET mostrado acima primeiro para popular o cache

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t GET -n 1000000 -d 1024 -P 50`

<a name="threadpool"></a>

### <a name="important-details-about-threadpool-growth"></a>Detalhes importantes sobre o crescimento de ThreadPool
O ThreadPool do CLR tem dois tipos de threads – threads "Worker" e "porta de conclusão de e/s" (IOCP).

* Os threads de trabalho são usados para coisas `Task.Run(…)`como processar `ThreadPool.QueueUserWorkItem(…)` os métodos ou. Esses threads também são usados por vários componentes no CLR quando o trabalho precisa ocorrer em um thread em segundo plano.
* Os threads IOCP são usados quando a e/s assíncrona acontece, como ao ler da rede.

O pool de segmentos fornece novos threads de trabalho ou threads de conclusão de e/s sob demanda (sem nenhuma limitação) até atingir a configuração "mínima" para cada tipo de thread. Por padrão, o número mínimo de threads é definido como o número de processadores em um sistema.

Depois que o número de threads (ocupados) existentes atinge o número "mínimo" de threads, o ThreadPool limitará a taxa na qual injeta novos threads para um thread por 500 milissegundos. Normalmente, se o seu sistema obtiver uma intermitência de trabalho que precisa de um thread IOCP, ele processará esse trabalho rapidamente. No entanto, se a intermitência de trabalho for mais do que a configuração "mínima" configurada, haverá algum atraso no processamento de parte do trabalho, pois o ThreadPool aguarda uma das duas coisas a acontecer.

1. Um thread existente torna-se livre para processar o trabalho.
2. Nenhum thread existente torna-se gratuito para 500 MS, portanto, um novo thread é criado.

Basicamente, isso significa que, quando o número de threads ocupados for maior que o mínimo de threads, você provavelmente está pagando um atraso de 500 MS antes que o tráfego de rede seja processado pelo aplicativo. Além disso, é importante observar que, quando um thread existente permanece ocioso por mais de 15 segundos (com base no que eu me lembro), ele será limpo e esse ciclo de crescimento e redução poderá se repetir.

Se olharmos um exemplo de mensagem de erro de StackExchange. Redis (Build 1.0.450 ou posterior), você verá que agora ele imprime estatísticas de ThreadPool (consulte os detalhes de IOCP e de trabalho abaixo).

    System.TimeoutException: Timeout performing GET MyKey, inst: 2, mgr: Inactive,
    queue: 6, qu: 0, qs: 6, qc: 0, wr: 0, wq: 0, in: 0, ar: 0,
    IOCP: (Busy=6,Free=994,Min=4,Max=1000),
    WORKER: (Busy=3,Free=997,Min=4,Max=1000)

No exemplo anterior, você pode ver que, para o thread IOCP, há seis threads ocupados e o sistema está configurado para permitir quatro threads mínimos. Nesse caso, o cliente provavelmente terá visto atrasos de 2 500 a MS, pois 6 > 4.

Observe que StackExchange. Redis pode atingir tempos limite se o crescimento de IOCP ou threads de trabalho for limitado.

### <a name="recommendation"></a>Recomendação

Dadas essas informações, é altamente recomendável que os clientes definam o valor mínimo de configuração para IOCP e threads de trabalho para algo maior que o valor padrão. Não podemos dar a orientação de um único tamanho sobre o que esse valor deve ter porque o valor certo para um aplicativo provavelmente será muito alto ou baixo para outro aplicativo. Essa configuração também pode afetar o desempenho de outras partes de aplicativos complicados, de modo que cada cliente precisa ajustar essa configuração para suas necessidades específicas. Um bom ponto de partida é 200 ou 300 e, em seguida, teste e ajuste conforme necessário.

Como definir essa configuração:

* É recomendável alterar essa configuração programaticamente usando o método [ThreadPool. SetMinThreads (...)](/dotnet/api/system.threading.threadpool.setminthreads#System_Threading_ThreadPool_SetMinThreads_System_Int32_System_Int32_) no `global.asax.cs`. Por exemplo:

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
  > O valor especificado por esse método é uma configuração global, afetando todo o AppDomain. Por exemplo, se você tiver uma máquina de 4 núcleos e quiser definir *MinWorkerThreads* e *minIoThreads* como 50 por CPU durante o tempo de execução, você usaria **ThreadPool. SetMinThreads (200, 200)** .

* Também é possível especificar a configuração de threads mínimos usando o `<processModel>` parâmetro de [configuração *minIoThreads* ou *MinWorkerThreads* ](https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx) no elemento de configuração `Machine.config`no, geralmente localizado `%SystemRoot%\Microsoft.NET\Framework\[versionNumber]\CONFIG\` em . **Definir o número de threads mínimos dessa maneira geralmente não é recomendado, pois é uma configuração de todo o sistema.**

  > [!NOTE]
  > O valor especificado neste elemento de configuração é uma configuração *por núcleo* . Por exemplo, se você tiver uma máquina de 4 núcleos e quiser que sua configuração de *minIoThreads* seja 200 em tempo de execução `<processModel minIoThreads="50"/>`, você usaria.
  >

<a name="server-gc"></a>

### <a name="enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis"></a>Habilite o GC do servidor para obter mais taxa de transferência no cliente ao usar StackExchange. Redis
Habilitar o GC do servidor pode otimizar o cliente e fornecer melhor desempenho e taxa de transferência ao usar StackExchange. Redis. Para obter mais informações sobre o GC do servidor e como habilitá-lo, consulte os seguintes artigos:

* [Para habilitar o GC do servidor](/dotnet/framework/configure-apps/file-schema/runtime/gcserver-element)
* [Conceitos básicos da coleta de lixo](/dotnet/standard/garbage-collection/fundamentals)
* [Coleta de lixo e desempenho](/dotnet/standard/garbage-collection/performance)


### <a name="performance-considerations-around-connections"></a>Considerações sobre desempenho em relação a conexões

Cada tipo de preço tem limites diferentes para conexões de cliente, memória e largura de banda. Embora cada tamanho de cache permita *até* um determinado número de conexões, cada conexão com Redis tem sobrecarga associada a ela. Um exemplo de tal sobrecarga seria o uso de CPU e memória como resultado da criptografia TLS/SSL. O limite máximo de conexões para um determinado tamanho de cache pressupõe um cache levemente carregado. Se a carga da sobrecarga de conexão *mais* a carga das operações do cliente exceder a capacidade para o sistema, o cache poderá ter problemas de capacidade mesmo que você não tenha excedido o limite de conexão para o tamanho do cache atual.

Para obter mais informações sobre os limites de conexões diferentes para cada camada, consulte [Azure cache for Redis Pricing](https://azure.microsoft.com/pricing/details/cache/). Para obter mais informações sobre conexões e outras configurações padrão, consulte [configuração padrão do servidor Redis](cache-configure.md#default-redis-server-configuration).

<a name="cache-monitor"></a>

### <a name="how-do-i-monitor-the-health-and-performance-of-my-cache"></a>Como fazer monitorar a integridade e o desempenho do meu cache?
O cache Microsoft Azure para instâncias Redis pode ser monitorado no [portal do Azure](https://portal.azure.com). Você pode exibir as métricas, fixar os gráficos de métricas no quadro inicial, personalizar o intervalo de data e hora dos gráficos de monitoramento, adicionar e remover métricas dos gráficos e definir alertas quando determinadas condições forem atendidas. Para obter mais informações, consulte [monitorar o cache do Azure para Redis](cache-how-to-monitor.md).

O **menu de recursos** do cache do Azure para Redis também contém várias ferramentas para monitorar e solucionar problemas de seus caches.

* **Diagnosticar e resolver problemas** fornece informações sobre problemas comuns e estratégias para resolvê-los.
* O **Resource Health** inspeciona o recurso e informa se ele está sendo executado conforme o esperado. Para obter mais informações sobre o serviço de integridade de recursos do Azure, consulte [visão geral do Azure Resource Health](../resource-health/resource-health-overview.md).
* **Nova solicitação de suporte** fornece opções para abrir uma solicitação de suporte para seu cache.

Essas ferramentas permitem monitorar a integridade do cache do Azure para instâncias Redis e ajudá-lo a gerenciar seus aplicativos de cache. Para obter mais informações, consulte a seção "suporte & configurações de solução de problemas" de [como configurar o cache do Azure para Redis](cache-configure.md).

<a name="cache-timeouts"></a>

### <a name="why-am-i-seeing-timeouts"></a>Por que estou vendo tempos limite?
Os tempos limite acontecem no cliente que você usa para se comunicar com o Redis. Quando um comando é enviado para o servidor Redis, o comando é colocado em fila e o servidor Redis eventualmente seleciona o comando e o executa. No entanto, o cliente pode atingir o tempo limite durante esse processo e, se ele faz uma exceção, é gerado no lado da chamada. Para obter mais informações sobre como solucionar problemas de tempo limite, consulte exceções [do cliente](cache-how-to-troubleshoot.md#client-side-troubleshooting) e de [tempo limite do stackexchange. Redis](cache-how-to-troubleshoot.md#stackexchangeredis-timeout-exceptions).

<a name="cache-disconnect"></a>

### <a name="why-was-my-client-disconnected-from-the-cache"></a>Por que meu cliente foi desconectado do cache?
A seguir estão alguns motivos comuns para uma desconexão do cache.

* Causas do lado do cliente
  * O aplicativo cliente foi reimplantado.
  * O aplicativo cliente executou uma operação de dimensionamento.
    * No caso de serviços de nuvem ou aplicativos Web, isso pode ser devido ao dimensionamento automático.
  * A camada de rede no lado do cliente foi alterada.
  * Erros transitórios ocorreram no cliente ou nos nós de rede entre o cliente e o servidor.
  * Os limites do limite de largura de banda foram atingidos.
  * As operações associadas à CPU levaram muito tempo para serem concluídas.
* Causas do lado do servidor
  * Na oferta de cache padrão, o cache do Azure para o serviço Redis iniciou um failover do nó primário para o nó secundário.
  * O Azure estava corrigindo a instância em que o cache foi implantado
    * Isso pode ser para atualizações do servidor Redis ou manutenção geral da VM.

### <a name="which-azure-cache-offering-is-right-for-me"></a>Qual das ofertas da Cache do Azure é melhor para mim?
> [!IMPORTANT]
> De acordo com o [comunicado](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)do ano passado, o serviço de cache gerenciado do Azure e o serviço de cache na função do Azure foram **desativados** em 30 de novembro de 2016. Nossa recomendação é usar o [cache do Azure para Redis](https://azure.microsoft.com/services/cache/). Para obter informações sobre como migrar, consulte [migrar do serviço de cache gerenciado para o cache do Azure para Redis](cache-migrate-to-redis.md).
>
>

### <a name="azure-cache-for-redis"></a>Cache do Azure para Redis
O cache do Azure para Redis está geralmente disponível em tamanhos de até 120 GB e tem um SLA de disponibilidade de 99,9%. A nova [camada Premium](cache-premium-tier-intro.md) oferece tamanhos de até 1,2 TB e suporte para clustering, VNET e persistência, com um SLA de 99,9%.

O cache do Azure para Redis oferece aos clientes a capacidade de usar um cache do Azure seguro e dedicado para o Redis, gerenciado pela Microsoft. Com essa oferta, você tem de aproveitar o rico conjunto de recursos e o ecossistema fornecido pelo Redis, além de hospedagem e monitoramento confiáveis da Microsoft.

Ao contrário dos caches tradicionais que lidam apenas com os pares chave-valor, o Redis é popular para seus tipos de dados altamente de alto desempenho. O Redis também dá suporte à execução de operações atômicas nesses tipos, como anexar a uma cadeia de caracteres; incrementando o valor em um hash; enviando por push para uma lista; Calculando interseção de conjunto, União e diferença; ou obtendo o membro com a classificação mais alta em um conjunto classificado. Outros recursos incluem suporte para transações, pub/sub, scripts de lua, chaves com tempo de vida limitado e definições de configuração para fazer com que o Redis se comporte mais como um cache tradicional.

Outro aspecto fundamental do sucesso do Redis é o ecossistema íntegro e vibrante de software livre criado em relação a ele. Isso se reflete no conjunto variado de clientes Redis disponíveis em vários idiomas. Esse ecossistema e uma ampla variedade de clientes permitem que o cache do Azure para Redis seja usado por praticamente qualquer carga de trabalho que você criar dentro do Azure.

Para obter mais informações sobre a introdução ao cache do Azure para Redis, consulte [como usar o cache do Azure para Redis](cache-dotnet-how-to-use-azure-redis-cache.md) e a [documentação do cache do Azure para Redis](index.md).

### <a name="managed-cache-service"></a>Serviço de cache gerenciado
[O serviço de cache gerenciado foi desativado em 30 de novembro de 2016.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

Para exibir a documentação arquivada, consulte [documentação do serviço de cache gerenciado arquivado](/previous-versions/azure/azure-services/dn386094(v=azure.100)).

### <a name="in-role-cache"></a>Cache na Função
[Cache na Função foi desativado em 30 de novembro de 2016.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

Para exibir a documentação arquivada, consulte [documentação de cache na função arquivada](/previous-versions/azure/azure-services/dn386103(v=azure.100)).

["minIoThreads" configuration setting]: https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx
