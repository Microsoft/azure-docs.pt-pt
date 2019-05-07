---
title: Fornecedor de estado de sessão do cache ASP.NET | Documentos da Microsoft
description: Saiba como armazenar o estado de sessão do ASP.NET usando o Cache do Azure para Redis
services: cache
documentationcenter: na
author: yegu-ms
manager: jhubbard
editor: tysonn
ms.assetid: 192f384c-836a-479a-bb65-8c3e6d6522bb
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 05/01/2017
ms.author: yegu
ms.openlocfilehash: 7333fa51da1cd5bbd9175d56571ec1d17cbbe33f
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65203931"
---
# <a name="aspnet-session-state-provider-for-azure-cache-for-redis"></a>Fornecedor de estado de sessão ASP.NET para a Cache do Azure para Redis

A Cache de Redis do Azure fornece um fornecedor de estado de sessão que pode utilizar para armazenar sua sessão estado dentro da memória com o Azure Cache de Redis em vez de uma base de dados do SQL Server. Para utilizar o fornecedor de estado de sessão colocação em cache, comece por configurar a cache e, em seguida, configure a sua aplicação de ASP.NET para a cache com a Cache do Azure para o pacote NuGet do Estado de sessão de Redis.

Muitas vezes, não é prático numa aplicação na cloud reais para evitar o armazenamento de alguma forma de estado para uma sessão de utilizador, mas algumas abordagens afetam o desempenho e escalabilidade mais do que outras pessoas. Se tiver de armazenar o estado, é a melhor solução manter a quantidade de estado de pequeno e o armazenamos em cookies. Se que não é viável, a próxima melhor solução é usar o estado de sessão do ASP.NET com um fornecedor de cache distribuída, dentro da memória. A pior solução de um desempenho e o ponto de vista de escalabilidade é a utilizar uma base de dados de fornecedor de estado de sessão de segurança. Este tópico fornece orientações sobre como usar o provedor de estado de sessão do ASP.NET para a Cache do Azure para Redis. Para obter informações sobre outras opções de estado de sessão, consulte [opções de estado de sessão ASP.NET](#aspnet-session-state-options).

## <a name="store-aspnet-session-state-in-the-cache"></a>Armazenar o estado da sessão do ASP.NET na cache

Para configurar uma aplicação cliente no Visual Studio com a Cache do Azure para o pacote NuGet do Estado de sessão de Redis, clique em **Gestor de pacotes NuGet**, **Package Manager Console** partir o **ferramentas**  menu.

Execute o seguinte comando a partir da janela `Package Manager Console`.
    

```powershell
Install-Package Microsoft.Web.RedisSessionStateProvider
```

> [!IMPORTANT]
> Se estiver a utilizar a funcionalidade de clustering o escalão premium, tem de utilizar [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 ou superior, ou uma exceção é lançada. Mover para 2.0.1 ou superior é uma alteração de última hora; Para obter mais informações, consulte [v2.0.0 detalhes de alteração de última hora](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details). Durante a atualização deste artigo, a versão atual deste pacote é 2.2.3.
> 
> 

O pacote NuGet de fornecedor do Estado de sessão de Redis tem uma dependência no pacote de StrongName. Se o pacote de StrongName não estiver presente no seu projeto, ele é instalado.

>[!NOTE]
>Além do pacote de StrongName nome forte, também há a versão de não-nome forte do stackexchange. redis. Se seu projeto estiver a utilizar a versão de stackexchange. redis não-nome forte que deve desinstalá-lo, caso contrário, obter conflitos de nomenclatura no seu projeto. Para obter mais informações sobre esses pacotes, consulte [clientes de cache do .NET configurar](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).
>
>

O pacote NuGet transfere e adiciona as referências de assemblagem necessárias e adiciona a seção a seguir em seu arquivo Web. config. Esta secção contém a configuração necessária para a sua aplicação de ASP.NET utilizar a Cache do Azure para o fornecedor de estado de sessão de Redis.

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

A seção Comentada fornece um exemplo dos atributos e exemplos de definições para cada atributo.

Configurar os atributos com os valores do seu painel de cache no portal do Microsoft Azure e configure os outros valores conforme pretendido. Para obter instruções sobre como acessar as propriedades de seu cache, consulte [configurar a Cache do Azure para as definições de Redis](cache-configure.md#configure-azure-cache-for-redis-settings).

* **anfitrião** – especifique o ponto final da cache.
* **porta** – utilizar a porta não SSL ou a porta SSL, consoante as definições de ssl.
* **accessKey** – utilizar a chave primária ou secundária para a sua cache.
* **SSL** – VERDADEIRO se pretender proteger as comunicações de cliente de cache/com ssl; caso contrário FALSO. Certifique-se de que especifique a porta correta.
  * Por predefinição, a porta não SSL está desativada para as novas caches. Especifica verdadeiro para esta definição utilizar a porta SSL. Para obter mais informações sobre como ativar a porta não SSL, consulte a [portas de acesso](cache-configure.md#access-ports) secção a [configurar uma cache](cache-configure.md) tópico.
* **throwOnError** – VERDADEIRO se pretender que uma exceção fosse emitida se houver uma falha, ou FALSO se pretender que a operação falhar silenciosamente. Pode verificar uma falha ao verificar a propriedade Microsoft.Web.Redis.RedisSessionStateProvider.LastException estática. A predefinição é verdadeira.
* **retryTimeoutInMilliseconds** – operações que não sejam repetidas durante este intervalo, especificado em milissegundos. Da primeira repetição ocorre após 20 milissegundos e, em seguida, as tentativas ocorrem a cada segundo até que o intervalo de retryTimeoutInMilliseconds expira. Imediatamente após este intervalo, a operação será repetida uma vez final. Se a operação continuar a falhar, a exceção é lançada volta ao chamador, consoante a definição de throwOnError. O valor predefinido é 0, o que significa que não existem repetições.
* **databaseId** – Especifica qual banco de dados a utilizar para dados de saída da cache. Se não for especificado, é utilizado o valor predefinido de 0.
* **applicationName** – as chaves são armazenadas no redis como `{<Application Name>_<Session ID>}_Data`. Este esquema de nomeação permite que vários aplicativos compartilhem a mesma instância de Redis. Este parâmetro é opcional e se não fornecer um valor predefinido é utilizado.
* **connectionTimeoutInMilliseconds** – esta definição permite-lhe substituir a definição de connectTimeout no cliente stackexchange. redis. Se não for especificado, é utilizada a predefinição de connectTimeout de 5000. Para obter mais informações, consulte [modelo de configuração stackexchange. redis](https://go.microsoft.com/fwlink/?LinkId=398705).
* **operationTimeoutInMilliseconds** – esta definição permite-lhe substituir a definição de syncTimeout no cliente stackexchange. redis. Se não for especificado, é utilizada a predefinição de syncTimeout de 1000. Para obter mais informações, consulte [modelo de configuração stackexchange. redis](https://go.microsoft.com/fwlink/?LinkId=398705).
* **redisSerializerType** -esta definição permite-lhe especificar a serialização personalizada de conteúdo de sessão que é enviado ao Redis. O tipo especificado tem de implementar `Microsoft.Web.Redis.ISerializer` e tem de declarar o construtor sem parâmetros público. Por predefinição `System.Runtime.Serialization.Formatters.Binary.BinaryFormatter` é utilizado.

Para obter mais informações sobre estas propriedades, veja o anúncio de mensagem de blogue original em [apresentamos o estado de fornecedor de sessão ASP.NET para Redis](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).

Não se esqueça de comentar a secção de fornecedor de estado de sessão de padrão InProc na Web. config.

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

Assim que estas etapas são realizadas, seu aplicativo está configurado para utilizar a Cache do Azure para o fornecedor de estado de sessão de Redis. Quando utiliza o estado da sessão na sua aplicação, são armazenado num Cache do Azure para a instância de Redis.

> [!IMPORTANT]
> Dados armazenados na cache tem de ser serializáveis, ao contrário dos dados que podem ser armazenados no padrão fornecedor de estado de sessão do ASP.NET na memória. Quando o fornecedor de estado de sessão para o Redis é utilizado, certifique-se de que os tipos de dados que estão sendo armazenados no estado de sessão são serializáveis.
> 
> 

## <a name="aspnet-session-state-options"></a>Opções de estado de sessão do ASP.NET

* No fornecedor de estado de sessão de memória – este fornecedor armazena o estado da sessão na memória. A vantagem de utilizar este fornecedor é é simples e rápida. No entanto não pode dimensionar aplicações Web se estiver a utilizar no fornecedor de memória, uma vez que não está a ser distribuída.
* Fornecedor de estado de sessão do SQL Server – este fornecedor armazena o estado da sessão no Sql Server. Se pretender armazenar o estado da sessão no armazenamento persistente, utilize este fornecedor. Pode dimensionar a sua aplicação Web, mas utilizar o Sql Server para a sessão tem um impacto de desempenho na sua aplicação Web. Também pode utilizar este fornecedor com uma [configuração do OLTP dentro da memória](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2017/11/28/asp-net-session-state-with-sql-server-in-memory-oltp/) para ajudar a melhorar o desempenho.
* Distribuído na memória sessão fornecedor de estado, tais como o Cache do Azure para o fornecedor de Redis nas estado de sessão - este fornecedor dá-lhe o melhor dos dois mundos. A aplicação Web pode ter um fornecedor de estado de sessão simples, rápida e dimensionável. Uma vez que esse provedor armazena o estado da sessão em Cache, a aplicação tem de ter em consideração todas as características associadas ao conversar com uma Cache distribuída na memória, como falhas de rede transitórios. Para melhores práticas sobre como utilizar a Cache, consulte [orientações sobre a colocação em cache](../best-practices-caching.md) da Microsoft Patterns & Practices [Design de aplicações do Azure na Cloud e orientações de implementação](https://github.com/mspnp/azure-guidance).

Para obter mais informações sobre o estado da sessão e outras práticas recomendadas, consulte [práticas recomendadas de desenvolvimento Web (criar aplicações de Cloud de mundo Real com o Azure)](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices).

## <a name="next-steps"></a>Passos Seguintes

Veja a [fornecedor de Cache de saída do ASP.NET para a Cache de Redis do Azure](cache-aspnet-output-cache-provider.md).
