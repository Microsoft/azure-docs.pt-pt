---
title: Fornecedor de Cache de saída do ASP.NET de cache
description: Saiba como colocar em cache de saída de página ASP.NET usando o Cache do Azure para Redis
services: cache
documentationcenter: na
author: yegu-ms
manager: jhubbard
editor: tysonn
ms.assetid: 78469a66-0829-484f-8660-b2598ec60fbf
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 02/14/2017
ms.author: yegu
ms.openlocfilehash: 3aa2e9e773eb0c07b5f10a57dabf1138b9f3f288
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60232903"
---
# <a name="aspnet-output-cache-provider-for-azure-cache-for-redis"></a>Fornecedor de Cache de saída do ASP.NET para a Cache do Azure para Redis
O fornecedor de Cache de saída de Redis é um mecanismo de armazenamento fora do processo para dados de cache de saída. Estes dados são especificamente para respostas HTTP completas (cache de saída de página). O fornecedor se conecta o nova saída cache fornecedor ponto de extensibilidade que foi introduzido no ASP.NET 4.

Para utilizar o fornecedor de Cache de saída de Redis, primeiro de configurar a cache e, em seguida, configure a sua aplicação ASP.NET com o pacote NuGet de fornecedor do Cache de saída de Redis. Este tópico fornece orientações sobre como configurar a sua aplicação para utilizar o fornecedor de Cache de saída de Redis. Para obter mais informações sobre como criar e configurar uma Cache do Azure para a instância de Redis, consulte [criar uma cache](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

## <a name="store-aspnet-page-output-in-the-cache"></a>Store saída de página do ASP.NET na cache
Para configurar uma aplicação cliente no Visual Studio com a Cache do Azure para o pacote NuGet do Estado de sessão de Redis, clique em **Gestor de pacotes NuGet**, **Package Manager Console** partir o **ferramentas**  menu.

Execute o seguinte comando a partir da janela `Package Manager Console`.
    
```
Install-Package Microsoft.Web.RedisOutputCacheProvider
```

O pacote de Redis NuGet de fornecedor de Cache de saída tem uma dependência no pacote de StrongName. Se o pacote de StrongName não estiver presente no seu projeto, ele é instalado. Para obter mais informações sobre o pacote de Redis NuGet de fornecedor de Cache de saída, consulte a [RedisOutputCacheProvider](https://www.nuget.org/packages/Microsoft.Web.RedisOutputCacheProvider/) página NuGet.

>[!NOTE]
>Além do pacote de StrongName nome forte, também há a versão de não-nome forte do stackexchange. redis. Se seu projeto estiver a utilizar a versão de stackexchange. redis não-nome forte que deve desinstalá-lo, caso contrário, obter conflitos de nomenclatura no seu projeto. Para obter mais informações sobre esses pacotes, consulte [clientes de cache do .NET configurar](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).
>
>

O pacote NuGet transfere e adiciona as referências de assemblagem necessárias e adiciona a seção a seguir em seu arquivo Web. config. Esta secção contém a configuração necessária para a sua aplicação de ASP.NET utilizar o fornecedor de Cache de saída de Redis.

```xml
<caching>
  <outputCache defaultProvider="MyRedisOutputCache">
    <providers>
      <!-- For more details check https://github.com/Azure/aspnet-redis-providers/wiki -->
      <!-- Either use 'connectionString' OR 'settingsClassName' and 'settingsMethodName' OR use 'host','port','accessKey','ssl','connectionTimeoutInMilliseconds' and 'operationTimeoutInMilliseconds'. -->
      <!-- 'databaseId' and 'applicationName' can be used with both options. -->
      <!--
      <add name="MyRedisOutputCache" 
        host = "127.0.0.1" [String]
        port = "" [number]
        accessKey = "" [String]
        ssl = "false" [true|false]
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
      <add name="MyRedisOutputCache" type="Microsoft.Web.Redis.RedisOutputCacheProvider"
           host=""
           accessKey=""
           ssl="true" />
    </providers>
  </outputCache>
</caching>
```

A seção Comentada fornece um exemplo dos atributos e exemplos de definições para cada atributo.

Configurar os atributos com os valores do seu painel de cache no portal do Microsoft Azure e configure os outros valores conforme pretendido. Para obter instruções sobre como acessar as propriedades de seu cache, consulte [configurar a Cache do Azure para as definições de Redis](cache-configure.md#configure-azure-cache-for-redis-settings).

* **anfitrião** – especifique o ponto final da cache.
* **porta** – utilizar a porta não SSL ou a porta SSL, consoante as definições de ssl.
* **accessKey** – utilizar a chave primária ou secundária para a sua cache.
* **SSL** – VERDADEIRO se pretender proteger as comunicações de cliente de cache/com ssl; caso contrário FALSO. Certifique-se de que especifique a porta correta.
  * Por predefinição, a porta não SSL está desativada para as novas caches. Especifica verdadeiro para esta definição utilizar a porta SSL. Para obter mais informações sobre como ativar a porta não SSL, consulte a [portas de acesso](cache-configure.md#access-ports) secção a [configurar uma cache](cache-configure.md) tópico.
* **databaseId** – especificado qual banco de dados a utilizar para a cache de dados de saída. Se não for especificado, é utilizado o valor predefinido de 0.
* **applicationName** – as chaves são armazenadas no redis como `<AppName>_<SessionId>_Data`. Este esquema de nomeação permite que vários aplicativos compartilhem a mesma chave. Este parâmetro é opcional e se não fornecer um valor predefinido é utilizado.
* **connectionTimeoutInMilliseconds** – esta definição permite-lhe substituir a definição de connectTimeout no cliente stackexchange. redis. Se não for especificado, é utilizada a predefinição de connectTimeout de 5000. Para obter mais informações, consulte [modelo de configuração stackexchange. redis](https://go.microsoft.com/fwlink/?LinkId=398705).
* **operationTimeoutInMilliseconds** – esta definição permite-lhe substituir a definição de syncTimeout no cliente stackexchange. redis. Se não for especificado, é utilizada a predefinição de syncTimeout de 1000. Para obter mais informações, consulte [modelo de configuração stackexchange. redis](https://go.microsoft.com/fwlink/?LinkId=398705).

Adicione uma diretiva OutputCache para cada página para o qual pretende colocar em cache a saída.

```
<%@ OutputCache Duration="60" VaryByParam="*" %>
```

No anterior exemplo, os dados de página em cache permanecem na cache durante 60 segundos e uma versão diferente da página é colocado em cache para cada combinação de parâmetro. Para obter mais informações sobre a diretiva OutputCache, consulte [ @OutputCache ](https://go.microsoft.com/fwlink/?linkid=320837).

Assim que estas etapas são realizadas, seu aplicativo está configurado para utilizar o fornecedor de Cache de saída de Redis.

## <a name="next-steps"></a>Passos Seguintes
Veja a [fornecedor de estado de sessão do ASP.NET para a Cache de Redis do Azure](cache-aspnet-session-state-provider.md).

