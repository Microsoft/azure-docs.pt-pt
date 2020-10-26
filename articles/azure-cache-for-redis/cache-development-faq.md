---
title: Cache Azure para redis desenvolvimento FAQs
description: Aprenda as respostas a perguntas comuns que o ajudam a desenvolver para Azure Cache para Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 08/06/2020
ms.openlocfilehash: be2e4a002d1daf4da7d042f1fd7d5bf0e9a01377
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92544516"
---
# <a name="azure-cache-for-redis-development-faqs"></a>Cache Azure para redis desenvolvimento FAQs

Este artigo fornece respostas a perguntas comuns sobre como desenvolver para Azure Cache para Redis.

## <a name="common-questions-and-answers"></a>Perguntas comuns e respostas
Esta secção abrange as seguintes PERGUNTAS Frequentes:

* [Como posso começar com Azure Cache para Redis?](#how-can-i-get-started-with-azure-cache-for-redis)
* [O que fazem as opções de configuração StackExchange.Redis?](#what-do-the-stackexchangeredis-configuration-options-do)
* [Que Azure Cache para clientes Redis posso usar?](#what-azure-cache-for-redis-clients-can-i-use)
* [Há algum emulador local para o Azure Cache para o Redis?](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [Como posso comandar o Redis?](#how-can-i-run-redis-commands)
* [Porque é que o Azure Cache para o Redis não tem uma referência à biblioteca da classe MSDN?](#why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference)
* [Posso usar a Cache Azure para redis como cache de sessão PHP?](#can-i-use-azure-cache-for-redis-as-a-php-session-cache)
* [O que são bases de dados do Redis?](#what-are-redis-databases)

### <a name="how-can-i-get-started-with-azure-cache-for-redis"></a>Como posso começar com Azure Cache para Redis?
Há várias maneiras de começar com Azure Cache para Redis.

* Pode consultar um dos nossos tutoriais disponíveis para [.NET,](cache-dotnet-how-to-use-azure-redis-cache.md) [ASP.NET,](cache-web-app-howto.md) [Java,](cache-java-get-started.md) [Node.js](cache-nodejs-get-started.md)e [Python.](cache-python-get-started.md)
* Pode ver [Como Construir High-Performance aplicações Usando o Microsoft Azure Cache para Redis](https://azure.microsoft.com/documentation/videos/how-to-build-high-performance-apps-using-microsoft-azure-cache/).
* Pode consultar a documentação do cliente para os clientes que correspondam à linguagem de desenvolvimento do seu projeto para ver como usar o Redis. Há muitos clientes Redis que podem ser usados com Azure Cache para Redis. Para uma lista de clientes redis, [https://redis.io/clients](https://redis.io/clients) veja.

Se ainda não tem uma conta Azure, pode:

* [Criar uma conta do Azure gratuitamente](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero). Recebe créditos que podem ser utilizados para experimentar os serviços pagos do Azure. Mesmo depois de gastar todos os créditos, pode manter a conta e utilizar os serviços e recursos gratuitos do Azure.
* [Ativar os benefícios de subscritor do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). A sua subscrição do MSDN dá-lhe créditos todos os meses que pode utilizar em serviços pagos do Azure.

### <a name="what-do-the-stackexchangeredis-configuration-options-do"></a>O que fazem as opções de configuração StackExchange.Redis?
StackExchange.Redis tem muitas opções. Esta secção fala sobre algumas das configurações comuns. Para obter informações mais detalhadas sobre as opções StackExchange.Redis, consulte a [configuração StackExchange.Redis](https://stackexchange.github.io/StackExchange.Redis/Configuration).

| ConfigurationOptions | Descrição | Recomendação |
| --- | --- | --- |
| AbortOnConnectFail |Quando definido como verdadeiro, a ligação não se reconectará após uma falha de rede. |Definido como falso e deixe o StackExchange.Redis reconectar-se automaticamente. |
| ConnectRetry |O número de vezes para repetir tentativas de ligação durante a ligação inicial. |Consulte as seguintes notas para obter orientação. |
| ConnectTimeout |Tempo limite na Ms para operações de ligação. |Consulte as seguintes notas para obter orientação. |

Normalmente, os valores padrão do cliente são suficientes. Pode afinar as opções com base na sua carga de trabalho.

* **Tentativas**
  * Para o ConnectRetry e o ConnectTimeout, a orientação geral é falhar rapidamente e voltar a tentar. Esta orientação baseia-se na sua carga de trabalho e no tempo que, em média, leva para o seu cliente emitir um comando Redis e receber uma resposta.
  * Deixe o StackExchange.Redis reconectar-se automaticamente em vez de verificar o estado da ligação e voltar a ligar-se. **Evite utilizar a propriedade ConnectionMultiplexer.IsConnected** .
  * Bola de Neve - às vezes pode encontrar-se com um problema em que está a tentar e as tentativas de bola de neve e nunca se recupera. Se ocorrer bola de neve, deve considerar a utilização de um algoritmo de recuo exponencial, conforme descrito na [orientação geral da Retry](/azure/architecture/best-practices/transient-faults) publicada pelo grupo Microsoft Patterns & Practices.
  
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

### <a name="is-there-a-local-emulator-for-azure-cache-for-redis"></a>Há algum emulador local para o Azure Cache para o Redis?
Não existe nenhum emulador local para a Azure Cache para Redis, mas pode executar a versão MSOpenTech de redis-server.exe a partir das ferramentas da [linha de comando Redis](https://github.com/MSOpenTech/redis/releases/) na sua máquina local e conectá-la para obter uma experiência semelhante a um emulador de cache local, como mostra o seguinte exemplo:

```csharp
private static Lazy<ConnectionMultiplexer>
    lazyConnection = new Lazy<ConnectionMultiplexer> (() =>
    {
        // Connect to a locally running instance of Redis to simulate
        // a local cache emulator experience.
        return ConnectionMultiplexer.Connect("127.0.0.1:6379");
    });

public static ConnectionMultiplexer Connection
{
    get
    {
        return lazyConnection.Value;
    }
}
```

Pode configurar opcionalmente um ficheiro [redis.conf](https://redis.io/topics/config) para combinar mais de perto as [definições de cache padrão](cache-configure.md#default-redis-server-configuration) para o seu Azure Cache on-line para Redis, se desejar.

### <a name="how-can-i-run-redis-commands"></a>Como posso comandar o Redis?
Pode utilizar qualquer um dos comandos listados nos [comandos Redis,](https://redis.io/commands#) exceto os comandos listados nos [comandos Redis não suportados em Cache Azure para Redis](cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis). Tem várias opções para executar comandos Redis.

* Se tiver uma cache Standard ou Premium, pode executar comandos Redis utilizando a [Consola Redis.](cache-configure.md#redis-console) A consola Redis fornece uma forma segura de executar comandos Redis no portal Azure.
* Também pode utilizar as ferramentas da linha de comando Redis. Para usá-los, execute os seguintes passos:
* Descarregue as [ferramentas da linha de comando Redis.](https://github.com/MSOpenTech/redis/releases/)
* Ligue-se à cache utilizando `redis-cli.exe` . Passe no ponto final da cache utilizando o interruptor -h e a chave utilizando -a como mostrado no exemplo seguinte:
* `redis-cli -h <Azure Cache for Redis name>.redis.cache.windows.net -a <key>`

> [!NOTE]
> As ferramentas da linha de comando Redis não funcionam com a porta TLS, mas pode utilizar um utilitário de modo `stunnel` a ligar as ferramentas de forma segura à porta TLS seguindo as instruções da [ferramenta de linha de comando Redis com a Cache Azure para](./cache-how-to-redis-cli-tool.md) o artigo Redis.
>
>

### <a name="why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference"></a>Porque é que o Azure Cache para o Redis não tem uma referência à biblioteca da classe MSDN?
O Microsoft Azure Cache for Redis baseia-se na popular loja de dados de código aberto na memória, Redis. Pode ser acedido por uma grande variedade de [clientes Redis](https://redis.io/clients) para muitas linguagens de programação. Cada cliente tem a sua própria API que faz chamadas para a Cache Azure para a instância Redis usando [comandos Redis](https://redis.io/commands).

Como cada cliente é diferente, não há uma referência de classe centralizada na MSDN, e cada cliente mantém a sua própria documentação de referência. Além da documentação de referência, existem vários tutoriais que mostram como começar com a Azure Cache para Redis usando diferentes idiomas e clientes cache. Para aceder a estes tutoriais, veja [Como usar a Cache Azure para Redis](cache-dotnet-how-to-use-azure-redis-cache.md) e os seus artigos de irmãos na tabela de conteúdos.

### <a name="can-i-use-azure-cache-for-redis-as-a-php-session-cache"></a>Posso usar a Cache Azure para redis como cache de sessão PHP?
Sim, para utilizar a Cache Azure para Redis como cache de sessão PHP, especifique a cadeia de ligação da sua Cache Azure para a instância Redis em `session.save_path` .

> [!IMPORTANT]
> Ao utilizar a Cache Azure para Redis como cache de sessão PHP, deve codificar a chave de segurança utilizada para ligar à cache, como mostra o seguinte exemplo:
>
> `session.save_path = "tcp://mycache.redis.cache.windows.net:6379?auth=<url encoded primary or secondary key here>";`
>
> Se a chave não estiver codificada por URL, poderá receber uma exceção com uma mensagem como: `Failed to parse session.save_path`
>

Para obter mais informações sobre a utilização do Azure Cache para Redis como cache de sessão PHP com o cliente PhpRedis, consulte o [manipulador php Session](https://github.com/phpredis/phpredis#php-session-handler).

### <a name="what-are-redis-databases"></a>O que são bases de dados do Redis?

As bases de dados redis são apenas uma separação lógica de dados dentro do mesmo caso Redis. A memória do cache é partilhada entre todas as bases de dados e o consumo real de memória de uma determinada base de dados depende das chaves/valores armazenados nessa base de dados. Por exemplo, uma cache C6 tem 53 GB de memória, e um P5 tem 120 GB. Pode optar por colocar todos os 53 GB / 120 GB numa base de dados ou pode dividi-lo entre várias bases de dados. 

> [!NOTE]
> Ao utilizar uma Cache Premium Azure para Redis com clustering ativado, apenas a base de dados 0 está disponível. Esta limitação é uma limitação intrínseca do Redis e não é específica para Azure Cache para Redis. Para mais informações, veja [Se preciso de fazer alterações à aplicação do meu cliente para utilizar o clustering?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [outros Azure Cache para Redis FAQs](cache-faq.md).