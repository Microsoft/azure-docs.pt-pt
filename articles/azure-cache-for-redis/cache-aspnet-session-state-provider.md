---
title: Fornecedor de estado de sessão de ASP.NET de cache
description: Aprenda a armazenar ASP.NET sessão na memória utilizando a cache Azure para redis.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: devx-track-dotnet
ms.date: 05/01/2017
ms.openlocfilehash: ce77f5074d707da5cfb251a103653b96e4644b5f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92544533"
---
# <a name="aspnet-session-state-provider-for-azure-cache-for-redis"></a>Fornecedor de Estado da Sessão ASP.NET para a Cache do Azure para Redis

O Azure Cache for Redis fornece um fornecedor de estado de sessão que pode usar para armazenar a sua sessão com a Azure Cache para Redis em vez de uma base de dados SQL Server. Para utilizar o fornecedor de estado de caching, configurar primeiro o seu cache e, em seguida, configurar o seu ASP.NET pedido de cache utilizando o pacote Azure Cache for Redis Session State NuGet. Para aplicações ASP.NET Core, leia [sessão e gestão do estado em ASP.NET Core](/aspnet/core/fundamentals/app-state).

Muitas vezes não é prático numa aplicação de nuvem do mundo real para evitar armazenar algum tipo de estado para uma sessão de utilizador, mas algumas abordagens afetam o desempenho e a escalabilidade mais do que outras. Se tiver de armazenar o estado, a melhor solução é manter a quantidade de estado pequena e armazená-la em cookies. Se isso não for viável, a próxima melhor solução é usar ASP.NET estado de sessão com um fornecedor para cache distribuído na memória. A pior solução do ponto de vista de desempenho e escalabilidade é utilizar uma base de dados apoiada pelo estado. Este tópico fornece orientações sobre a utilização do fornecedor de estado de sessão de ASP.NET para a azure cache para redis. Para obter informações sobre outras opções de estado da [sessão, consulte ASP.NET opções do Estado da Sessão](#aspnet-session-state-options).

## <a name="store-aspnet-session-state-in-the-cache"></a>Armazenar o estado da sessão do ASP.NET na cache

Para configurar uma aplicação do cliente no Estúdio Visual utilizando o pacote Azure Cache for Redis Session State NuGet, clique em **NuGet Package Manager**, **Package Manager Consola** a partir do menu **Ferramentas.**

Execute o seguinte comando a partir da janela `Package Manager Console`.
    

```powershell
Install-Package Microsoft.Web.RedisSessionStateProvider
```

> [!IMPORTANT]
> Se estiver a utilizar a função de clustering a partir do nível premium, deve utilizar [o RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 ou superior ou uma exceção é lançada. Passar para 2.0.1 ou superior é uma mudança de rutura; para obter mais informações, consulte [v2.0.0 Breaking Change Details](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details). No momento desta atualização do artigo, a versão atual deste pacote é 2.2.3.
> 
> 

O pacote NuGet do Fornecedor de Estado redis tem uma dependência do pacote StackExchange.Redis.StrongName. Se o pacote StackExchange.Redis.StrongName não estiver presente no seu projeto, está instalado.

>[!NOTE]
>Além do pacote StackExchange.Redis.StrongName, há também a versão stackExchange.Redis sem nome forte. Se o seu projeto estiver a utilizar a versão stackExchange.Redis sem nome forte, tem de desinstalá-la, caso contrário terá conflitos de nomeação no seu projeto. Para obter mais informações sobre estes pacotes, consulte [clientes Configure .NET cache](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).
>
>

O pacote NuGet descarrega e adiciona as referências de montagem necessárias e adiciona a seguinte secção no seu ficheiro web.config. Esta secção contém a configuração necessária para a sua aplicação ASP.NET utilizar a Cache Azure para Redis Session State Provider.

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

Configure os atributos com os valores da sua lâmina de cache no portal Microsoft Azure e configuure os outros valores conforme desejado. Para obter instruções sobre o acesso às propriedades do seu cache, consulte [a Cache Configure Azure para as definições de Redis](cache-configure.md#configure-azure-cache-for-redis-settings).

* **host** – especifique o seu ponto final de cache.
* **porta** – utilize a porta não TLS/SSL ou a porta TLS/SSL, dependendo das definições de TLS.
* **accessKey** – utilize a tecla primária ou secundária para o seu cache.
* **ssl** – verdadeiro se quiser garantir comunicações cache/cliente com TLS; caso contrário falso. Certifique-se de especificar a porta correta.
  * A porta não-TLS é desativada por defeito para novos caches. Especifique a verdade para esta definição para utilizar a porta TLS. Para obter mais informações sobre a ativação da porta não-TLS, consulte a secção [Portas de Acesso](cache-configure.md#access-ports) no tópico [Configure um](cache-configure.md) tópico de cache.
* **throwOnError** – verdadeiro se quiser que seja lançada uma exceção se houver uma falha, ou falsa se quiser que a operação falhe silenciosamente. Pode verificar se existe uma falha verificando a propriedade estática Microsoft.Web.Redis.Redis.RedisSessionStateProvider.LastException. O padrão é verdadeiro.
* **reda oTimeoutInMilisconds** – As operações que falham são novamente julgadas durante este intervalo, especificadas em milissegundos. A primeira repetição ocorre após 20 milissegundos, e então as retrações ocorrem a cada segundo até que o intervalo de RetryTimeoutInMilisconds expire. Imediatamente após este intervalo, a operação é novamente experimentada uma última vez. Se a operação continuar a falhar, a exceção é reles para o autor da chamada, dependendo da definição throwOnError. O valor padrão é 0, o que significa que não há retrações.
* **databaseId** – Especifica qual base de dados utilizar para dados de saída de cache. Se não for especificado, utiliza-se o valor predefinido de 0.
* **nome de aplicação** – As chaves são armazenadas em redis como `{<Application Name>_<Session ID>}_Data` . Este esquema de nomeação permite que várias aplicações partilhem a mesma instância Redis. Este parâmetro é opcional e se não fornecer um valor predefinido é usado.
* **conexãoTimeoutInMilliseconds** – Esta definição permite-lhe substituir a definição connectTimeout no cliente StackExchange.Redis. Se não for especificado, utiliza-se a definição padrão de 5000 do connectTimeout. Para obter mais informações, consulte [o modelo de configuração StackExchange.Redis](https://go.microsoft.com/fwlink/?LinkId=398705).
* **operationTimeoutInMilliseconds** – Esta definição permite-lhe substituir a definição syncTimeout no cliente StackExchange.Redis. Se não for especificado, é utilizada a definição padrão de sincronizaçãotimeout de 1000. Para obter mais informações, consulte [o modelo de configuração StackExchange.Redis](https://go.microsoft.com/fwlink/?LinkId=398705).
* **redisSerializerType** - Esta definição permite especificar a serialização personalizada do conteúdo da sessão que é enviado para o Redis. O tipo especificado deve ser implementado `Microsoft.Web.Redis.ISerializer` e deve declarar o construtor público sem parâmetros. Por defeito  `System.Runtime.Serialization.Formatters.Binary.BinaryFormatter` é usado.

Para obter mais informações sobre estas propriedades, consulte o anúncio original do blog post no [Anunciado ASP.NET Session State Provider for Redis](https://devblogs.microsoft.com/aspnet/announcing-asp-net-session-state-provider-for-redis-preview-release/).

Não se esqueça de comentar a secção padrão de provedores do estado de sessão inProc na sua web.config.

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

Uma vez executadas estas etapas, a sua aplicação é configurada para utilizar a Cache Azure para Redis Session State Provider. Quando utiliza o estado de sessão na sua aplicação, é armazenado numa Cache Azure para a instância Redis.

> [!IMPORTANT]
> Os dados armazenados na cache devem ser serializáveis, ao contrário dos dados que podem ser armazenados no padrão de memória ASP.NET Provedor de Estado de Sessão. Quando o Fornecedor de Estado de Sessão para Redis for utilizado, certifique-se de que os tipos de dados que estão a ser armazenados em estado de sessão são serializáveis.
> 
> 

## <a name="aspnet-session-state-options"></a>Opções de Estado da Sessão ASP.NET

* In Memory Session State Provider - Este fornecedor armazena o Estado de Sessão na memória. O benefício da utilização deste fornecedor é que é simples e rápido. No entanto, não é possível escalar as suas Aplicações Web se estiver a utilizar no fornecedor de memória, uma vez que não está distribuída.
* Sql Server Session State Provider - Este fornecedor armazena o Estado de Sessão no Sql Server. Utilize este fornecedor se pretender armazenar o estado de Sessão em armazenamento persistente. Pode escalar a sua Web App, mas usar o Sql Server for Session tem um impacto de desempenho na sua Web App. Também pode utilizar este fornecedor com uma [configuração OLTP in-memory](/archive/blogs/sqlserverstorageengine/asp-net-session-state-with-sql-server-in-memory-oltp) para ajudar a melhorar o desempenho.
* Fornecedor de Estado distribuído em sessão de memória, como Azure Cache para Redis Session State Provider - Este fornecedor dá-lhe o melhor de ambos os mundos. A sua Web App pode ter um fornecedor de estado de sessão simples, rápido e escalável. Como este fornecedor armazena o estado de Sessão num Cache, a sua aplicação tem de ter em conta todas as características associadas ao falar com uma Cache Distribuída na Memória, como falhas de rede transitórias. Para obter as melhores práticas sobre a utilização da Cache, consulte a orientação de [Caching](/azure/architecture/best-practices/caching) da Microsoft Patterns & Practices [Azure Cloud Application Design e Orientação de Implementação](https://github.com/mspnp/azure-guidance).

Para obter mais informações sobre o estado da sessão e outras boas práticas, consulte [as Melhores Práticas de Desenvolvimento Web (Building Real-World Cloud Apps com Azure)](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices).

## <a name="third-party-session-state-providers"></a>Fornecedores estatais de sessão de terceiros

* [NCache](https://www.alachisoft.com/ncache/session-index.html)
* [Apache inflamar](https://apacheignite-net.readme.io/docs/aspnet-session-state-caching)

## <a name="next-steps"></a>Passos seguintes

Consulte o [fornecedor de cache de saída ASP.NET para a cache Azure para redis](cache-aspnet-output-cache-provider.md).