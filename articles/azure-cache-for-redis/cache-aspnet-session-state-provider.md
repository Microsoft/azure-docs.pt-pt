---
title: Provedor de estado de sessão ASP.NET de cache
description: Saiba como armazenar o estado de sessão ASP.NET na memória usando o cache do Azure para Redis.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 05/01/2017
ms.openlocfilehash: 5c9af862ca2df3d812384c0f4ab660730aece872
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75433548"
---
# <a name="aspnet-session-state-provider-for-azure-cache-for-redis"></a>Provedor de estado de sessão ASP.NET para o cache do Azure para Redis

O cache do Azure para Redis fornece um provedor de estado de sessão que você pode usar para armazenar o estado de sessão na memória com o cache do Azure para Redis em vez de um banco de dados SQL Server. Para usar o provedor de estado de sessão de cache, primeiro configure o cache e, em seguida, configure seu aplicativo ASP.NET para cache usando o pacote NuGet do estado de sessão do cache do Azure para Redis.

Geralmente não é prático em um aplicativo de nuvem do mundo real evitar o armazenamento de alguma forma de estado para uma sessão de usuário, mas algumas abordagens afetam o desempenho e a escalabilidade mais do que outras. Se você precisar armazenar o estado, a melhor solução é manter a quantidade de estado pequeno e armazená-lo em cookies. Se isso não for viável, a próxima melhor solução é usar o estado de sessão ASP.NET com um provedor de cache na memória distribuído. A pior solução de um ponto de vista de desempenho e escalabilidade é usar um provedor de estado de sessão com suporte de banco de dados. Este tópico fornece orientação sobre como usar o provedor de estado de sessão ASP.NET para o cache do Azure para Redis. Para obter informações sobre outras opções de estado de sessão, consulte [Opções de estado de sessão do ASP.net](#aspnet-session-state-options).

## <a name="store-aspnet-session-state-in-the-cache"></a>Armazenar o estado da sessão do ASP.NET na cache

Para configurar um aplicativo cliente no Visual Studio usando o pacote NuGet de estado de sessão do cache do Azure para Redis, clique em **Gerenciador de pacotes NuGet**, **console do Gerenciador de pacotes** no menu **ferramentas** .

Execute o seguinte comando a partir da janela `Package Manager Console`.
    

```powershell
Install-Package Microsoft.Web.RedisSessionStateProvider
```

> [!IMPORTANT]
> Se você estiver usando o recurso de clustering da camada Premium, deverá usar o [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 ou superior ou uma exceção será lançada. Mudar para 2.0.1 ou superior é uma alteração significativa; para obter mais informações, consulte [v 2.0.0 de alteração significativa](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details). No momento da atualização deste artigo, a versão atual desse pacote é 2.2.3.
> 
> 

O pacote NuGet do provedor de estado de sessão Redis tem uma dependência no pacote StackExchange. Redis. StrongName. Se o pacote StackExchange. Redis. StrongName não estiver presente no seu projeto, ele será instalado.

>[!NOTE]
>Além do pacote StackExchange. Redis. StrongName de nome forte, também há a versão do StackExchange. Redis que não é de nome forte. Se o seu projeto estiver usando a versão StackExchange. Redis que não é de nome forte, você deverá desinstalá-la, caso contrário, você obterá conflitos de nomenclatura em seu projeto. Para obter mais informações sobre esses pacotes, consulte [configurar clientes de cache .net](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).
>
>

O pacote NuGet baixa e adiciona as referências de assembly necessárias e adiciona a seção a seguir ao seu arquivo Web. config. Esta seção contém a configuração necessária para seu aplicativo ASP.NET usar o cache do Azure para o provedor de estado de sessão Redis.

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

A seção comentada fornece um exemplo dos atributos e das configurações de exemplo para cada atributo.

Configure os atributos com os valores da sua folha de cache no portal do Microsoft Azure e configure os outros valores conforme desejado. Para obter instruções sobre como acessar suas propriedades de cache, consulte [Configurar o cache do Azure para configurações de Redis](cache-configure.md#configure-azure-cache-for-redis-settings).

* **host** – especifique o ponto de extremidade do cache.
* **porta** – Use sua porta não SSL ou sua porta SSL, dependendo das configurações de SSL.
* **accessKey** – use a chave primária ou secundária para o cache.
* **SSL** – verdadeiro se você quiser proteger as comunicações de cache/cliente com SSL; caso contrário, false. Certifique-se de especificar a porta correta.
  * Por predefinição, a porta não SSL está desativada para as novas caches. Especifique true para que essa configuração Use a porta SSL. Para obter mais informações sobre como habilitar a porta não SSL, consulte a seção [portas de acesso](cache-configure.md#access-ports) no tópico [configurar um cache](cache-configure.md) .
* **throwOnError** – true se você quiser que uma exceção seja gerada se houver uma falha ou false se quiser que a operação falhe silenciosamente. Você pode verificar se há uma falha verificando a propriedade estática Microsoft. Web. Redis. RedisSessionStateProvider. LastException. O padrão é true.
* **retryTimeoutInMilliseconds** – as operações que falham são repetidas durante esse intervalo, especificadas em milissegundos. A primeira repetição ocorre após 20 milissegundos e as repetições ocorrem a cada segundo até que o intervalo de retryTimeoutInMilliseconds expire. Imediatamente após esse intervalo, a operação é repetida uma última vez. Se a operação ainda falhar, a exceção será gerada de volta para o chamador, dependendo da configuração de throwOnError. O valor padrão é 0, o que significa que não há novas tentativas.
* **DatabaseID** – especifica o banco de dados a ser usado para armazenar em cache o dado de saída. Se não for especificado, o valor padrão de 0 será usado.
* **ApplicationName** – as chaves são armazenadas em redis como `{<Application Name>_<Session ID>}_Data`. Esse esquema de nomenclatura permite que vários aplicativos compartilhem a mesma instância de Redis. Esse parâmetro é opcional e, se você não fornecer um valor padrão, será usado.
* **connectionTimeoutInMilliseconds** – essa configuração permite que você substitua a configuração connectTimeout no cliente stackexchange. Redis. Se não for especificado, a configuração padrão de connectTimeout de 5000 será usada. Para obter mais informações, consulte [modelo de configuração stackexchange. Redis](https://go.microsoft.com/fwlink/?LinkId=398705).
* **operationTimeoutInMilliseconds** – essa configuração permite que você substitua a configuração syncTimeout no cliente stackexchange. Redis. Se não for especificado, a configuração padrão de syncTimeout de 1000 será usada. Para obter mais informações, consulte [modelo de configuração stackexchange. Redis](https://go.microsoft.com/fwlink/?LinkId=398705).
* **redisSerializerType** – essa configuração permite que você especifique a serialização personalizada do conteúdo da sessão que é enviada para o Redis. O tipo especificado deve implementar `Microsoft.Web.Redis.ISerializer` e deve declarar o construtor público sem parâmetros. Por padrão `System.Runtime.Serialization.Formatters.Binary.BinaryFormatter` é usado.

Para obter mais informações sobre essas propriedades, consulte o anúncio de postagem do blog original em [anunciando provedor de estado de sessão do ASP.net para Redis](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).

Não se esqueça de comentar a seção provedor de estado de sessão InProc padrão em seu Web. config.

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

Depois que essas etapas forem executadas, seu aplicativo será configurado para usar o cache do Azure para o provedor de estado de sessão Redis. Quando você usa o estado de sessão em seu aplicativo, ele é armazenado em um cache do Azure para a instância Redis.

> [!IMPORTANT]
> Os dados armazenados no cache devem ser serializáveis, ao contrário dos dados que podem ser armazenados no provedor de estado de sessão ASP.NET na memória padrão. Quando o provedor de estado de sessão para Redis for usado, certifique-se de que os tipos de dados que estão sendo armazenados no estado de sessão sejam serializáveis.
> 
> 

## <a name="aspnet-session-state-options"></a>Opções de estado de sessão do ASP.NET

* Provedor de estado de sessão de memória-esse provedor armazena o estado da sessão na memória. O benefício de usar esse provedor é simples e rápido. No entanto, você não poderá dimensionar seus aplicativos Web se estiver usando o provedor de memória, pois ele não está distribuído.
* Provedor de estado de sessão do SQL Server-esse provedor armazena o estado de sessão no SQL Server. Use este provedor se você quiser armazenar o estado de sessão no armazenamento persistente. Você pode dimensionar seu aplicativo Web, mas usar o SQL Server para sessão tem um impacto no desempenho em seu aplicativo Web. Você também pode usar esse provedor com uma [configuração de OLTP na memória](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2017/11/28/asp-net-session-state-with-sql-server-in-memory-oltp/) para ajudar a melhorar o desempenho.
* Distribuído no provedor de estado de sessão de memória, como o cache do Azure para o provedor de estado de sessão Redis-esse provedor oferece o melhor dos dois mundos. Seu aplicativo Web pode ter um provedor de estado de sessão simples, rápido e escalonável. Como esse provedor armazena o estado de sessão em um cache, seu aplicativo precisa levar em consideração todas as características associadas ao se comunicar com um cache distribuído na memória, como falhas de rede transitórias. Para obter as práticas recomendadas sobre como usar o cache, consulte [diretrizes de cache](../best-practices-caching.md) dos padrões da Microsoft & práticas de [design e implementação do aplicativo de nuvem do Azure](https://github.com/mspnp/azure-guidance).

Para obter mais informações sobre o estado de sessão e outras práticas recomendadas, consulte [práticas recomendadas de desenvolvimento para a Web (criando aplicativos de nuvem do mundo real com o Azure)](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices).

## <a name="next-steps"></a>Passos seguintes

Confira o [provedor de cache de saída ASP.net para o cache do Azure para Redis](cache-aspnet-output-cache-provider.md).
