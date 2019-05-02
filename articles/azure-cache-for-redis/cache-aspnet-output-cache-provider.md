---
title: Fornecedor de Cache de saída do ASP.NET para a Cache do Azure para Redis
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
ms.date: 04/22/2018
ms.author: yegu
ms.openlocfilehash: a93d21b07dc486f743694ee99f60018ed4ef517c
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943862"
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
>Além do pacote de StrongName nome forte, também há a versão de não-nome forte do stackexchange. redis. Se a versão de stackexchange. redis não-nome forte é utilizada no teu projeto tem de desinstalá-lo. caso contrário, irá ocorrer conflitos de nomenclatura no seu projeto. Para obter mais informações sobre esses pacotes, consulte [clientes de cache do .NET configurar](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

O pacote NuGet transfere e adiciona as referências de assemblagem necessárias e adiciona a seção a seguir em seu arquivo Web. config. Esta secção contém a configuração necessária para a sua aplicação de ASP.NET utilizar o fornecedor de Cache de saída de Redis.

```xml
<caching>
  <outputCache defaultProvider="MyRedisOutputCache">
    <providers>
      <add name="MyRedisOutputCache" type="Microsoft.Web.Redis.RedisOutputCacheProvider"
           host=""
           accessKey=""
           ssl="true" />
    </providers>
  </outputCache>
</caching>
```

Configurar os atributos com os valores do seu painel de cache no portal do Microsoft Azure e configure os outros valores conforme pretendido. Para obter instruções sobre como acessar as propriedades de seu cache, consulte [configurar a Cache do Azure para as definições de Redis](cache-configure.md#configure-azure-cache-for-redis-settings).

| Atributo | Type | Predefinição | Descrição |
| --------- | ---- | ------- | ----------- |
| *host* | string | "localhost" | O Redis IP endereço ou anfitrião nome do servidor |
| *port* | número inteiro positivo | 6379 (non-SSL)<br/>6380 (SSL) | Porta do servidor de redis |
| *accessKey* | string | "" | O redis palavra-passe do servidor quando a autorização de Redis está ativada. O valor é uma cadeia vazia por padrão, o que significa que o provedor de estado de sessão não utiliza qualquer palavra-passe, ao ligar ao servidor Redis. **Se o servidor de Redis está numa rede acessível ao público, como a Cache de Redis do Azure, certifique-se de que ativar a autorização de Redis melhorar a segurança e forneça uma palavra-passe segura.** |
| *ssl* | boolean | **false** | Se ligar ao servidor Redis através de SSL. Este valor é **false** por padrão porque o Redis não suporta SSL prontos a utilizar. **Se estiver a utilizar a Cache de Redis do Azure que oferece suporte à SSL imediato, certifique-se de que defina esta opção para definida como true para melhorar a segurança.**<br/><br/>Por predefinição, a porta não SSL está desativada para as novas caches. Especifique **true** para esta definição utilizar a porta SSL. Para obter mais informações sobre como ativar a porta não SSL, consulte a [portas de acesso](cache-configure.md#access-ports) secção a [configurar uma cache](cache-configure.md) tópico. |
| *databaseIdNumber* | número inteiro positivo | 0 | *Esse atributo pode ser especificado apenas por meio de Web. config ou AppSettings.*<br/><br/>Especifique qual banco de dados de Redis para utilizar. |
| *connectionTimeoutInMilliseconds* | número inteiro positivo | Fornecido pelo stackexchange. redis | Usado para definir *ConnectTimeout* durante a criação de StackExchange.Redis.ConnectionMultiplexer. |
| *operationTimeoutInMilliseconds* | número inteiro positivo | Fornecido pelo stackexchange. redis | Usado para definir *SyncTimeout* durante a criação de StackExchange.Redis.ConnectionMultiplexer. |
| *connectionString* (cadeia de ligação do stackexchange. redis válido) | string | *n/a* | Uma parâmetro referência AppSettings ou Web. config, caso contrário, uma cadeia de ligação válida do stackexchange. redis. Este atributo pode fornecer valores para *host*, *porta*, *accessKey*, *ssl*e outros atributos de stackexchange. redis. Por um mais próximo examinar *connectionString*, consulte [definição connectionString](#setting-connectionstring) no [notas de atributo](#attribute-notes) secção. |
| *settingsClassName*<br/>*settingsMethodName* | string<br/>string | *n/a* | *Estes atributos podem ser especificados apenas por meio de Web. config ou AppSettings.*<br/><br/>Utilize estes atributos para fornecer uma cadeia de ligação. *settingsClassName* deve ser um nome de classe qualificado da assemblagem que contém o método especificado pela *settingsMethodName*.<br/><br/>O método especificado por *settingsMethodName* deve ser públicos, estáticos e void (não tem nenhum parâmetro), com um tipo de retorno **cadeia de caracteres**. Este método devolve a cadeia de ligação real. |
| *loggingClassName*<br/>*loggingMethodName* | string<br/>string | *n/a* | *Estes atributos podem ser especificados apenas por meio de Web. config ou AppSettings.*<br/><br/>Use esses atributos para depurar seu aplicativo, fornecendo registos a partir do Cache de estado de sessão/saída, juntamente com os registos do stackexchange. redis. *loggingClassName* deve ser um nome de classe qualificado da assemblagem que contém o método especificado pela *loggingMethodName*.<br/><br/>O método especificado por *loggingMethodName* deve ser públicos, estáticos e void (não tem nenhum parâmetro), com um tipo de retorno **TextWriter**. |
| *applicationName* | string | O nome do módulo do processo atual ou "/" | *SessionStateProvider apenas*<br/>*Esse atributo pode ser especificado apenas por meio de Web. config ou AppSettings.*<br/><br/>O prefixo de nome de aplicação para utilizar na cache de Redis. O cliente pode usar o mesmo cache de Redis para diferentes fins. Para assegurar que as chaves de sessão não estão em conflito, pode ser prefixado com o nome da aplicação. |
| *throwOnError* | boolean | true | *SessionStateProvider apenas*<br/>*Esse atributo pode ser especificado apenas por meio de Web. config ou AppSettings.*<br/><br/>Se deve lançar uma exceção quando ocorre um erro.<br/><br/>Para obter mais informações sobre *throwOnError*, consulte [notas sobre *throwOnError* ](#notes-on-throwonerror) no [notas de atributo](#attribute-notes) secção. |>*Microsoft.Web.Redis.RedisSessionStateProvider.LastException*. |
| *retryTimeoutInMilliseconds* | número inteiro positivo | 5000 | *SessionStateProvider apenas*<br/>*Esse atributo pode ser especificado apenas por meio de Web. config ou AppSettings.*<br/><br/>Quanto para tentar novamente quando ocorre uma falha de uma operação. Se este valor for inferior a *operationTimeoutInMilliseconds*, o fornecedor não voltará a tentar.<br/><br/>Para obter mais informações sobre *retryTimeoutInMilliseconds*, consulte [notas sobre *retryTimeoutInMilliseconds* ](#notes-on-retrytimeoutinmilliseconds) no [notas de atributo](#attribute-notes) secção. |
| *redisSerializerType* | string | *n/a* | Especifica o nome de tipo qualificado de assemblagem de uma classe que implementa Microsoft.Web.Redis. ISerializer e que contém a lógica personalizada para serializar e desserializar os valores. Para obter mais informações, consulte [sobre *redisSerializerType* ](#about-redisserializertype) no [notas de atributo](#attribute-notes) secção. |
|

## <a name="attribute-notes"></a>Notas de atributo

### <a name="setting-connectionstring"></a>Definição *connectionString*

O valor de *connectionString* é utilizado como chave para obter a cadeia de ligação real de AppSettings, se uma cadeia de caracteres existe no AppSettings. Se não for encontrada no interior de AppSettings, o valor de *connectionString* será utilizado como chave ao obter a cadeia de ligação real da Web. config **ConnectionString** secção, se existe essa secção. Se a cadeia de ligação não existe no AppSettings ou Web. config **ConnectionString** secção, o valor literal *connectionString* será utilizada como a cadeia de ligação ao criar StackExchange.Redis.ConnectionMultiplexer.

Os exemplos a seguir ilustram como *connectionString* é utilizado.

#### <a name="example-1"></a>Exemplo 1

```xml
<connectionStrings>
    <add name="MyRedisConnectionString" connectionString="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</connectionStrings>
```

No `web.config`, utilize acima chave como valor de parâmetro em vez do valor real.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "MyRedisConnectionString"/>
    </providers>
</sessionState>
```

#### <a name="example-2"></a>Exemplo 2

```xml
<appSettings>
    <add key="MyRedisConnectionString" value="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</appSettings>
```

No `web.config`, utilize acima chave como valor de parâmetro em vez do valor real.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "MyRedisConnectionString"/>
    </providers>
</sessionState>
```

#### <a name="example-3"></a>Exemplo 3

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False"/>
    </providers>
</sessionState>
```

### <a name="notes-on-throwonerror"></a>Notas sobre *throwOnError*

Atualmente, se ocorrer um erro durante uma operação de sessão, o provedor de estado de sessão lançará uma exceção. Isso encerra a aplicação.

Esse comportamento foi modificado de forma que suporta as expectativas dos usuários do fornecedor de estado de sessão do existentes ASP.NET enquanto também fornecem a capacidade de agir sobre exceções, se assim o desejar. O comportamento padrão ainda lança uma exceção quando ocorre um erro, consistente com outros fornecedores de estado de sessão do ASP.NET; código existente deve funcionar as mesmas de antes.

Se definir *throwOnError* ao **falso**, em seguida, em vez de gerar uma exceção quando ocorre um erro, ele falhará silenciosamente. Para ver se Ocorreu um erro e, se assim for, descobrir qual era a exceção, verifique a propriedade estática *Microsoft.Web.Redis.RedisSessionStateProvider.LastException*.

### <a name="notes-on-retrytimeoutinmilliseconds"></a>Notas sobre *retryTimeoutInMilliseconds*

Isso fornece alguma lógica de repetição para simplificar o caso em que uma operação de sessão deve repetir em caso de falha devido a coisas como falha de rede, ao mesmo tempo controlar o tempo limite de repetição ou recusar as repetições inteiramente.

Se definir *retryTimeoutInMilliseconds* para um número, por exemplo 2000, em seguida, quando uma operação de sessão falhar, irá tentar novamente para 2000 milissegundos antes de tratá-la como um erro. Portanto, para que o provedor de estado de sessão para aplicar a lógica de repetição, apenas configure o tempo limite. Da primeira repetição vai ocorrer após a 20 milissegundos, que é suficiente na maioria dos casos, quando ocorre uma falha de rede. Depois disso, ele será repetida a cada segundo, até atingir o tempo limite. Logo após o tempo limite, ele voltará a tentar mais uma vez para se certificar de que ele não cortados o tempo limite por (no máximo) um segundo.

Se achar que não precisa repetir (por exemplo, quando estiver a executar o servidor Redis no mesmo computador que seu aplicativo) ou se deseja manipular a lógica de repetição por conta própria e defina *retryTimeoutInMilliseconds* como 0.

### <a name="about-redisserializertype"></a>Sobre *redisSerializerType*

Por predefinição, a serialização para armazenar os valores no Redis é feita num formato binário fornecido pelos **BinaryFormatter** classe. Uso *redisSerializerType* para especificar o nome de tipo qualificado de assemblagem de uma classe que implementa **Microsoft.Web.Redis.ISerializer** e tem a lógica personalizada para serializar e desserializar os valores. Por exemplo, aqui está uma classe de serializador Json com JSON.NET:

```cs
namespace MyCompany.Redis
{
    public class JsonSerializer : ISerializer
    {
        private static JsonSerializerSettings _settings = new JsonSerializerSettings() { TypeNameHandling = TypeNameHandling.All };

        public byte[] Serialize(object data)
        {
            return Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(data, _settings));
        }

        public object Deserialize(byte[] data)
        {
            if (data == null)6t6
            {
                return null;
            }
            return JsonConvert.DeserializeObject(Encoding.UTF8.GetString(data), _settings);
        }
    }
}
```

Supondo que essa classe é definido num assembly com o nome **MyCompanyDll**, pode definir o parâmetro *redisSerializerType* usá-lo:

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvider"
             name = "MySessionStateStore"
             redisSerializerType = "MyCompany.Redis.JsonSerializer,MyCompanyDll"
             ... />
    </providers>
</sessionState>
```

## <a name="output-cache-directive"></a>Diretiva de cache de saída

Adicione uma diretiva OutputCache para cada página para o qual pretende colocar em cache a saída.

```xml
<%@ OutputCache Duration="60" VaryByParam="*" %>
```

No anterior exemplo, os dados de página em cache permanecem na cache durante 60 segundos e uma versão diferente da página é colocado em cache para cada combinação de parâmetro. Para obter mais informações sobre a diretiva OutputCache, consulte [ @OutputCache ](https://go.microsoft.com/fwlink/?linkid=320837).

Assim que estas etapas são realizadas, seu aplicativo está configurado para utilizar o fornecedor de Cache de saída de Redis.

## <a name="next-steps"></a>Passos Seguintes

Veja a [fornecedor de estado de sessão do ASP.NET para a Cache de Redis do Azure](cache-aspnet-session-state-provider.md).
