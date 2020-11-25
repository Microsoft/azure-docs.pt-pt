---
title: ASP.NET fornecedor de cache de saída para cache Azure para Redis
description: Saiba como cache ASP.NET saída de página usando Azure Cache para Redis. O Redis Output Cache Provider é um mecanismo de armazenamento fora do processo para os dados da cache de saída.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: devx-track-csharp
ms.topic: conceptual
ms.date: 04/22/2018
ms.openlocfilehash: 6d711b07a10e04dcdf31259f3e53c9687af28e28
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993392"
---
# <a name="aspnet-output-cache-provider-for-azure-cache-for-redis"></a>ASP.NET fornecedor de cache de saída para cache Azure para Redis

O Redis Output Cache Provider é um mecanismo de armazenamento fora do processo para os dados da cache de saída. Estes dados são especificamente para respostas HTTP completas (caching de saída de página). O fornecedor liga-se ao novo ponto de extensibilidade do fornecedor de cache de saída que foi introduzido no ASP.NET 4. Para aplicações ASP.NET Core, leia [a resposta no núcleo ASP.NET](/aspnet/core/performance/caching/response). 

Para utilizar o Fornecedor de Cache de Saída Redis, configurar primeiro o seu cache e, em seguida, configurar a sua aplicação ASP.NET utilizando o pacote NuGet do Fornecedor de Cache de Saída Redis. Este tópico fornece orientações sobre a configuração da sua aplicação para utilizar o Fornecedor de Cache de Saída Redis. Para obter mais informações sobre a criação e configuração de uma Cache Azure para a instância Redis, consulte [Criar uma cache](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

## <a name="store-aspnet-page-output-in-the-cache"></a>Armazenar ASP.NET saída de página na cache

Para configurar uma aplicação do cliente no Estúdio Visual utilizando o pacote Azure Cache for Redis Session State NuGet, clique em **NuGet Package Manager**, **Package Manager Consola** a partir do menu **Ferramentas.**

Execute o seguinte comando a partir da janela `Package Manager Console`.

```
Install-Package Microsoft.Web.RedisOutputCacheProvider
```

O pacote NuGet do Fornecedor de Cache de Saída Redis tem uma dependência do pacote StackExchange.Redis.StrongName. Se o pacote StackExchange.Redis.StrongName não estiver presente no seu projeto, está instalado. Para obter mais informações sobre o pacote NuGet do Fornecedor de Cache de Saída Redis, consulte a página [RedisOutputCacheProvider](https://www.nuget.org/packages/Microsoft.Web.RedisOutputCacheProvider/) NuGet.

>[!NOTE]
>Além do pacote StackExchange.Redis.StrongName, há também a versão stackExchange.Redis sem nome forte. Se o seu projeto estiver a utilizar a versão StackExchange.Redis sem nome forte, deve desinstalá-la; caso contrário, experimentará o nome de conflitos no seu projeto. Para obter mais informações sobre estes pacotes, consulte [clientes Configure .NET cache](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

O pacote NuGet descarrega e adiciona as referências de montagem necessárias e adiciona a seguinte secção no seu ficheiro web.config. Esta secção contém a configuração necessária para a sua aplicação ASP.NET utilizar o Fornecedor de Cache de Saída Redis.

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

Configure os atributos com os valores da sua lâmina de cache no portal Microsoft Azure e configuure os outros valores conforme desejado. Para obter instruções sobre o acesso às propriedades do seu cache, consulte [a Cache Configure Azure para as definições de Redis](cache-configure.md#configure-azure-cache-for-redis-settings).

| Atributo | Tipo | Predefinição | Descrição |
| --------- | ---- | ------- | ----------- |
| *hospedeiro* | string | "Localhost" | O endereço IP do servidor Redis ou o nome de anfitrião |
| *porto* | inteiro positivo | 6379 (não-TLS/SSL)<br/>6380 (TLS/SSL) | Porta de servidor Redis |
| *acessoKey* | string | "" | Palavra-passe do servidor Redis quando a autorização do Redis estiver ativada. O valor é de cadeia vazia por padrão, o que significa que o fornecedor do estado da sessão não utilizará nenhuma palavra-passe quando ligar ao servidor Redis. **Se o seu servidor Redis estiver numa rede acessível ao público como a Cache Azure Redis, certifique-se de que ativa a autorização do Redis para melhorar a segurança e fornecer uma senha segura.** |
| *ssl* | boolean | **falso** | Se ligar ao servidor Redis via TLS. Este valor é **falso** por padrão porque o Redis não suporta tLS fora da caixa. **Se estiver a utilizar o Azure Redis Cache, que suporta sSL fora da caixa, certifique-se de que o define para melhorar a segurança.**<br/><br/>A porta não-TLS é desativada por defeito para novos caches. Especifique **a versão verdadeira** para a utilização da porta não-TLS. Para obter mais informações sobre a ativação da porta não-TLS, consulte a secção [Portas de Acesso](cache-configure.md#access-ports) no tópico [Configure um](cache-configure.md) tópico de cache. |
| *databaseIdNumber* | inteiro positivo | 0 | *Este atributo só pode ser especificado através de web.config ou AppSettings.*<br/><br/>Especifique qual base de dados Redis utilizar. |
| *conexãoTimeoutInMiliss* | inteiro positivo | Fornecido por StackExchange.Redis | Usado para definir *ConnectTimeout ao* criar StackExchange.Redis.ConnectionMultiplexer. |
| *operaçãoTimeoutInMiliss* | inteiro positivo | Fornecido por StackExchange.Redis | Usado para definir *SyncTimeout ao* criar StackExchange.Redis.ConnectionMultiplexer. |
| *ligaçãoStragem* (Cadeia de ligação Valid StackExchange.Redis) | string | *n/a* | Ou uma referência de parâmetro a AppSettings ou web.config, ou então uma cadeia de conexão StackExchange.Redis válida. Este atributo pode fornecer valores para *o anfitrião,* *porta,* *accessKey,* *ssl e outros atributos* StackExchange.Redis. Para uma análise mais atenta da *ligação,* consulte [a ligação DefiniçãoDestando](#setting-connectionstring) na secção [notas do Atributo.](#attribute-notes) |
| *definiçõesFinaname*<br/>*definiçõesMethodName* | string<br/>string | *n/a* | *Estes atributos só podem ser especificados através de web.config ou AppSettings.*<br/><br/>Utilize estes atributos para fornecer uma cadeia de ligação. *definições O Nome de Classe* deve ser um nome de classe qualificado de montagem que contenha o método especificado pelas *definiçõesMethodName*.<br/><br/>O método especificado pelas *definições O Nome de Method* deve ser público, estático e vazio (não tomar quaisquer parâmetros), com um tipo de **cadeia** de retorno . Este método devolve a cadeia de ligação real. |
| *nome de classes de registo*<br/>*nome de Method logging* | string<br/>string | *n/a* | *Estes atributos só podem ser especificados através de web.config ou AppSettings.*<br/><br/>Utilize estes atributos para depurar a sua aplicação fornecendo registos da Sessão Estado/Cache de saída juntamente com registos de StackExchange.Redis. *loggingClassName* deve ser um nome de classe qualificado de montagem que contém o método especificado por *loggingMethodName*.<br/><br/>O método especificado por *loggingMethodName* deve ser público, estático e vazio (não tomar quaisquer parâmetros), com um tipo de retorno de **System.IO.TextWriter**. |
| *aplicativoName* | string | O nome do módulo do processo atual ou "/" | *SessionStateProvider apenas*<br/>*Este atributo só pode ser especificado através de web.config ou AppSettings.*<br/><br/>O prefixo do nome da aplicação para usar na cache Redis. O cliente pode utilizar a mesma cache Redis para diferentes finalidades. Para garantir que as teclas de sessão não colidem, pode ser pré-fixado com o nome da aplicação. |
| *throwOnError* | boolean | true | *SessionStateProvider apenas*<br/>*Este atributo só pode ser especificado através de web.config ou AppSettings.*<br/><br/>Se lançar uma exceção quando ocorre um erro.<br/><br/>Para obter mais informações sobre *o throwOnError*, consulte [notas no *throwOnError*](#notes-on-throwonerror) na secção [notas do Atributo.](#attribute-notes) |>*Microsoft.Web.Redis.RedisSessionStateProvider.LastException*. |
| *retryTimeoutInMilliseconds* | inteiro positivo | 5000 | *SessionStateProvider apenas*<br/>*Este atributo só pode ser especificado através de web.config ou AppSettings.*<br/><br/>Quanto tempo para tentar novamente quando uma operação falha. Se este valor for inferior *ao funcionamentoTimeoutInMilisconds,* o fornecedor não voltará a tentar.<br/><br/>Para obter mais informações sobre *retryTimeoutInMilliseconds*, consulte [notas sobre *retryTimeoutInMilisconds*](#notes-on-retrytimeoutinmilliseconds) na secção [notas de atributo.](#attribute-notes) |
| *redisSerializerType* | string | *n/a* | Especifica o nome de tipo qualificado de montagem de uma classe que implementa Microsoft.Web.Redis. ISerializador e que contém a lógica personalizada para serializar e desseializar os valores. Para obter mais informações, consulte [ *Sobre o redisSerializerType*](#about-redisserializertype) na secção [notas do Atributo.](#attribute-notes) |

## <a name="attribute-notes"></a>Notas de atributo

### <a name="setting-connectionstring"></a>Definição *de ligaçãoDesse*

O valor da *ligaçãoStragem* é usado como chave para obter a cadeia de conexão real de AppSettings, se tal cadeia existir em AppSettings. Se não for encontrado dentro de AppSettings, o valor da *ligaçãoSe* ser utilizado como chave para obter o fio de ligação real da secção **web.config ConnectionString,** se essa secção existir. Se o fio de ligação não existir em AppSettings ou na secção **web.config ConnectionString,** o valor literal da *ligação O Adá de Ligação* será utilizado como cadeia de ligação ao criar StackExchange.Redis.ConnectionMultiplexer.

Os exemplos a seguir ilustram como a *ligação Adagem* é utilizada.

#### <a name="example-1"></a>Exemplo 1

```xml
<connectionStrings>
    <add name="MyRedisConnectionString" connectionString="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</connectionStrings>
```

Em `web.config` , usar acima da chave como valor de parâmetro em vez de valor real.

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

Em `web.config` , usar acima da chave como valor de parâmetro em vez de valor real.

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

### <a name="notes-on-throwonerror"></a>Notas no *throwOnError*

Atualmente, se ocorrer um erro durante uma operação de sessão, o provedor do estado da sessão lançará uma exceção. Isto encerra o pedido.

Este comportamento foi modificado de forma a suportar as expectativas dos utilizadores do fornecedor estatal de sessão ASP.NET existentes, ao mesmo tempo que fornece a capacidade de agir em exceções, se desejar. O comportamento predefinido ainda abre uma exceção quando ocorre um erro, consistente com outros fornecedores do Estado de sessão de ASP.NET; código existente deve funcionar da mesma forma que antes.

Se colocar *o throwOnError* em **falso,** em vez de lançar uma exceção quando ocorre um erro, falhará silenciosamente. Para ver se houve um erro e, em caso afirmativo, descubra qual foi a exceção, verifique a propriedade estática *Microsoft.Web.Redis.Redis.RedisSessionStateProvider.LastException*.

### <a name="notes-on-retrytimeoutinmilliseconds"></a>Notas sobre *retryTimeoutInMilisconds*

Isto fornece alguma lógica de retenção para simplificar o caso em que alguma operação de sessão deve voltar a tentar em falhas devido a falhas de rede, ao mesmo tempo que lhe permite controlar o tempo limite de retry ou optar por não voltar a tentar completamente.

Se definir *retryTimeoutInMilisconds* para um número, por exemplo 2000, então quando uma operação de sessão falhar, ele tentará novamente por 2000 milissegundos antes de tratá-lo como um erro. Assim, para ter o provedor do estado da sessão para aplicar esta lógica de retenção, basta configurar o tempo limite. A primeira repetição acontecerá após 20 milissegundos, o que é suficiente na maioria dos casos quando uma falha de rede acontece. Depois disso, vai voltar a tentar a cada segundo até que se escasseia. Logo após o intervalo, tentará novamente para garantir que não cortará o tempo limite por (no máximo) um segundo.

Se acha que não precisa de voltar a tentar (por exemplo, quando estiver a executar o servidor Redis na mesma máquina que a sua aplicação) ou se quiser lidar com a lógica de repetição, defina *retryTimeoutInMilisconds* para 0.

### <a name="about-redisserializertype"></a>Sobre *o redisSerializerType*

Por predefinição, a serialização para armazenar os valores no Redis é feita num formato binário fornecido pela classe **BinaryFormatter.** Utilize *o redisSerializerType* para especificar o nome do tipo qualificado de montagem de uma classe que implementa **Microsoft.Web.Redis.ISerializer** e tem a lógica personalizada para serializar e desseializar os valores. Por exemplo, aqui está uma aula de serializer Json usando JSON.NET:

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
            if (data == null)
            {
                return null;
            }
            return JsonConvert.DeserializeObject(Encoding.UTF8.GetString(data), _settings);
        }
    }
}
```

Assumindo que esta classe é definida num conjunto com o nome **MyCompanyDll,** pode definir o *parâmetro redisSerializerType* para usá-lo:

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

## <a name="output-cache-directive"></a>Diretiva relativa à cache de saída

Adicione uma diretiva OutputCache a cada página para a qual deseja cache a saída.

```xml
<%@ OutputCache Duration="60" VaryByParam="*" %>
```

No exemplo anterior, os dados da página em cache permanecem na cache durante 60 segundos, e uma versão diferente da página é em cache para cada combinação de parâmetros. Para obter mais informações sobre a diretiva OutputCache, consulte [@OutputCache](/previous-versions/dotnet/netframework-4.0/hdxfb6cy(v=vs.100)) .

Uma vez realizados estes passos, a sua aplicação está configurada para utilizar o Fornecedor de Cache de Saída Redis.

## <a name="third-party-output-cache-providers"></a>Fornecedores de cache de saída de terceiros

* [NCache](https://www.alachisoft.com/blogs/how-to-use-a-distributed-cache-for-asp-net-output-cache/)
* [Ignição de Apache](https://apacheignite-net.readme.io/docs/aspnet-output-caching)


## <a name="next-steps"></a>Passos seguintes

Consulte o [fornecedor de estado de sessão de ASP.NET para a Azure Cache para Redis](cache-aspnet-session-state-provider.md).