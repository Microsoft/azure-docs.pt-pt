---
title: Cache ASP.NET Provedor do Estado da Sessão
description: Aprenda a armazenar ASP.NET Session State em memória usando Azure Cache para Redis.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 05/01/2017
ms.openlocfilehash: 8083efe833ec80290713fc14d9cb89acd8263fa2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81010907"
---
# <a name="aspnet-session-state-provider-for-azure-cache-for-redis"></a>Fornecedor de Estado da Sessão ASP.NET para a Cache do Azure para Redis

O Azure Cache para o Redis fornece um fornecedor de estado de sessão que pode usar para armazenar o seu estado de sessão em memória com O Cache Azure para Redis em vez de uma base de dados do SQL Server. Para utilizar o fornecedor estatal da sessão de cache, primeiro configure a sua cache e, em seguida, configure a sua ASP.NET aplicação para cache utilizando o pacote Azure Cache para Redis Session State NuGet.

Muitas vezes não é prático numa aplicação de nuvem real para evitar armazenar alguma forma de estado para uma sessão de utilizador, mas algumas abordagens impactam o desempenho e a escalabilidade mais do que outras. Se tiver que armazenar o estado, a melhor solução é manter a quantidade de Estado pequena e armazená-la em cookies. Se isso não for viável, a próxima melhor solução é utilizar ASP.NET estado de sessão com um fornecedor para cache distribuído e em memória. A pior solução do ponto de vista do desempenho e da escalabilidade é utilizar uma base de dados apoiada por um fornecedor estatal de sessão. Este tópico fornece orientações sobre a utilização do ASP.NET Session State Provider for Azure Cache for Redis. Para obter informações sobre outras opções de estado da sessão, consulte ASP.NET opções do Estado de [Sessão.](#aspnet-session-state-options)

## <a name="store-aspnet-session-state-in-the-cache"></a>Armazenar o estado da sessão do ASP.NET na cache

Para configurar uma aplicação de cliente no Estúdio Visual utilizando o pacote Azure Cache para Redis Session State NuGet, clique em **NuGet Package Manager**, **Package Manager Console** do menu **Tools.**

Execute o seguinte comando a partir da janela `Package Manager Console`.
    

```powershell
Install-Package Microsoft.Web.RedisSessionStateProvider
```

> [!IMPORTANT]
> Se estiver a utilizar a função de agrupamento a partir do nível premium, deve utilizar [redisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 ou superior ou uma exceção é lançada. Passar para 2.0.1 ou superior é uma mudança de rutura; para mais informações, consulte [v2.0.0 Detalhes de mudança de rutura](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details). No momento desta atualização do artigo, a versão atual deste pacote é de 2.2.3.
> 
> 

O pacote NuGet do Fornecedor estatal Redis Session tem uma dependência do pacote StackExchange.Redis.StrongName. Se o pacote StackExchange.Redis.StrongName não estiver presente no seu projeto, está instalado.

>[!NOTE]
>Além do pacote StackExchange.Redis.StrongName, existe também a versão StackExchange.Redis não-strong-named. Se o seu projeto estiver a utilizar a versão StackExchange.Redis não forte, tem de desinstalá-lo, caso contrário obtém conflitos de nomeação no seu projeto. Para obter mais informações sobre estes pacotes, consulte [os clientes de cache Configure .NET](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).
>
>

O pacote NuGet descarrega e adiciona as referências de montagem necessárias e adiciona a seguinte secção no ficheiro web.config. Esta secção contém a configuração necessária para a sua aplicação ASP.NET utilizar o Cache Azure para o Redis Session State Provider.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
  <providers>
    <!-- Either use 'connectionString' OR 'settingsClassName' and 'settingsMethodName' OR use 'host','port','accessKey','ssl','connectionTimeoutInMilliseconds' and 'operationTimeoutInMilliseconds'. -->
    <!-- 'throwOnError','retryTimeoutInMilliseconds','databaseId' and 'applicationName' can be used with both options. -->
    <!--
      <add name="MySessionStateStore" 
        host = "127.0.0.1" [String]
        port = "" [number]
        accessKey = "" [String]
        ssl = "false" [true|false]
        throwOnError = "true" [true|false]
        retryTimeoutInMilliseconds = "5000" [number]
        databaseId = "0" [number]
        applicationName = "" [String]
        connectionTimeoutInMilliseconds = "5000" [number]
        operationTimeoutInMilliseconds = "1000" [number]
        connectionString = "<Valid StackExchange.Redis connection string>" [String]
        settingsClassName = "<Assembly qualified class name that contains settings method specified below. Which basically return 'connectionString' value>" [String]
        settingsMethodName = "<Settings method should be defined in settingsClass. It should be public, static, does not take any parameters and should have a return type of 'String', which is basically 'connectionString' value.>" [String]
        loggingClassName = "<Assembly qualified class name that contains logging method specified below>" [String]
        loggingMethodName = "<Logging method should be defined in loggingClass. It should be public, static, does not take any parameters and should have a return type of System.IO.TextWriter.>" [String]
        redisSerializerType = "<Assembly qualified class name that implements Microsoft.Web.Redis.ISerializer>" [String]
      />
    -->
    <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider"
         host=""
         accessKey=""
         ssl="true" />
  </providers>
</sessionState>
```

A secção comentada fornece um exemplo dos atributos e definições de amostra para cada atributo.

Configure os atributos com os valores da sua lâmina de cache no portal Microsoft Azure e configure os outros valores conforme desejado. Para obter instruções sobre o acesso às suas propriedades cache, consulte [Configure Azure Cache para configurações redis](cache-configure.md#configure-azure-cache-for-redis-settings).

* **anfitrião** – especifique o seu ponto final de cache.
* **porta** – utilize a sua porta não TLS/SSL ou a sua porta TLS/SSL, dependendo das definições de TLS.
* **acessórioChave** - utilize a chave primária ou secundária para a sua cache.
* **ssl** – verdadeiro se quiser garantir comunicações cache/cliente com TLS; de outra forma falso. Certifique-se de especificar a porta correta.
  * A porta não-TLS é desativada por padrão para novos caches. Especifique verdadeiramente para esta definição utilizar a porta TLS. Para obter mais informações sobre a ativação da porta não-TLS, consulte a secção Portas de [Acesso](cache-configure.md#access-ports) no tópico [de cache Da Configuração.](cache-configure.md)
* **throwOnError** – é verdade que se quiser lançar uma exceção se houver uma falha, ou falsa se quiser que a operação falhe silenciosamente. Pode verificar se existeuma falha, verificando a propriedade estática Microsoft.Web.Redis.Redis.RedisSessionStateProvider.LastException. O padrão é verdade.
* **retryTimeoutInMilliseconds** – As operações que falham são novamente tentadas durante este intervalo, especificadas em milissegundos. A primeira tentativa ocorre após 20 milissegundos, e depois as repetições ocorrem a cada segundo até que o intervalo timeoutInMillisegundos expira. Imediatamente após este intervalo, a operação é novamente tentada uma última vez. Se a operação continuar a falhar, a exceção é reposta para o chamador, dependendo da definição de lanceOnError. O valor padrão é 0, o que significa que não há repetições.
* **base de dadosId** – Especifica qual base de dados a utilizar para dados de saída de cache. Se não especificado, o valor padrão de 0 é utilizado.
* **nome de aplicação** – As `{<Application Name>_<Session ID>}_Data`teclas são armazenadas em redis como . Este esquema de nomeação permite que várias aplicações partilhem a mesma instância Redis. Este parâmetro é opcional e se não lhe fornecer um valor predefinido é utilizado.
* **conexãoTimeoutInMilliseconds** – Esta definição permite-lhe anular a definição de tempo de ligação no cliente StackExchange.Redis. Se não especificado, é utilizada a definição de tempo limite padrão de 5000. Para mais informações, consulte o modelo de [configuração StackExchange.Redis](https://go.microsoft.com/fwlink/?LinkId=398705).
* **operaçãoTimeoutInMilliseconds** – Esta definição permite-lhe anular a definição de tempo de sincronização no cliente StackExchange.Redis. Se não especificado, é utilizada a definição de sincronização predefinida de 1000. Para mais informações, consulte o modelo de [configuração StackExchange.Redis](https://go.microsoft.com/fwlink/?LinkId=398705).
* **redisSerializerType** - Esta definição permite especificar a serialização personalizada do conteúdo da sessão que é enviado para redis. O tipo especificado `Microsoft.Web.Redis.ISerializer` deve ser implementado e deve declarar um construtor público sem parâmetros. Por `System.Runtime.Serialization.Formatters.Binary.BinaryFormatter` defeito é utilizado.

Para obter mais informações sobre estas propriedades, consulte o anúncio original do post de blog no [Anúncio ASP.NET Session State Provider for Redis](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).

Não se esqueça de comentar a secção padrão de provedor de estado da inProc na sua web.config.

```xml
<!-- <sessionState mode="InProc"
     customProvider="DefaultSessionProvider">
     <providers>
        <add name="DefaultSessionProvider"
              type="System.Web.Providers.DefaultSessionStateProvider,
                    System.Web.Providers, Version=1.0.0.0, Culture=neutral,
                    PublicKeyToken=31bf3856ad364e35"
              connectionStringName="DefaultConnection" />
      </providers>
</sessionState> -->
```

Uma vez realizados estes passos, a sua aplicação está configurada para utilizar o Cache Azure para O Fornecedor Estatal redis Session. Quando utiliza o estado de sessão na sua aplicação, é armazenado num Azure Cache para redis.

> [!IMPORTANT]
> Os dados armazenados na cache devem ser ser serlizáveis, ao contrário dos dados que podem ser armazenados na memória padrão ASP.NET Fornecedor estatal de Sessão. Quando o Fornecedor de Estado de Redis for utilizado, certifique-se de que os tipos de dados que estão a ser armazenados em estado de sessão são serlizáveis.
> 
> 

## <a name="aspnet-session-state-options"></a>opções do Estado de ASP.NET Session

* In Memory Session State Provider - Este fornecedor armazena o Estado de Sessão na memória. O benefício de usar este fornecedor é simples e rápido. No entanto, não pode escalar as suas Aplicações Web se estiver a utilizar no fornecedor de memória, uma vez que não está distribuída.
* Sql Server Session State Provider - Este fornecedor armazena o Estado de Sessão no Servidor Sql. Utilize este fornecedor se pretender armazenar o estado de Sessão em armazenamento persistente. Pode escalar a sua Aplicação Web, mas usar o Sql Server para sessão tem um impacto de desempenho na sua Aplicação Web. Também pode utilizar este fornecedor com uma [configuração OLTP em Memória](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2017/11/28/asp-net-session-state-with-sql-server-in-memory-oltp/) para ajudar a melhorar o desempenho.
* Distribuído em Memória Sessão Fornecedor estatal como Azure Cache para Redis Session State Provider - Este provedor dá-lhe o melhor de ambos os mundos. A sua Web App pode ter um fornecedor de estado de sessão simples, rápido e escalável. Uma vez que este fornecedor armazena o estado session em cache, a sua aplicação tem de ter em conta todas as características associadas ao falar com um Cache distribuído em memória, como falhas de rede transitórias. Para obter as melhores práticas na utilização do Cache, consulte [a orientação](../best-practices-caching.md) de Caching da Microsoft Patterns & Práticas [Azure Cloud Application Design e Orientação de Implementação](https://github.com/mspnp/azure-guidance).

Para obter mais informações sobre o estado da sessão e outras boas práticas, consulte [as Melhores Práticas de Desenvolvimento Web (Building Real-World Cloud Apps com Azure)](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices).

## <a name="third-party-session-state-providers"></a>Fornecedores estatais de sessão de terceiros

* [NCache](https://www.alachisoft.com/ncache/session-index.html)
* [Apache inflama](https://apacheignite-net.readme.io/docs/aspnet-session-state-caching)

## <a name="next-steps"></a>Passos seguintes

Confira o [fornecedor de cache de saída ASP.NET para O Cache Azure para Redis](cache-aspnet-output-cache-provider.md).
