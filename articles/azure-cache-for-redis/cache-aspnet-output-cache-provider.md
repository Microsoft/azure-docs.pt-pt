---
title: ASP.NET Fornecedor de Cache de Saída para Cache Azure para Redis
description: Aprenda a cache ASP.NET Saída de página usando Azure Cache para Redis. O Fornecedor de Cache de Saída Redis é um mecanismo de armazenamento fora de processo para dados de cache de saída.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 04/22/2018
ms.openlocfilehash: f1d8189068278b46e3ec3ea66875d79bb91e5e16
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010210"
---
# <a name="aspnet-output-cache-provider-for-azure-cache-for-redis"></a>ASP.NET Fornecedor de Cache de Saída para Cache Azure para Redis

O Fornecedor de Cache de Saída Redis é um mecanismo de armazenamento fora de processo para dados de cache de saída. Estes dados são especificamente para respostas HTTP completas (cachede de saída de página). O fornecedor liga-se ao novo ponto de extebilidade do fornecedor de cache de saída que foi introduzido no ASP.NET 4.

Para utilizar o Fornecedor de Cache de Saída Redis, primeiro configure a sua cache e, em seguida, configure a sua aplicação ASP.NET utilizando o pacote NuGet do Fornecedor de Cache de Saída Redis. Este tópico fornece orientações sobre a configuração da sua aplicação para utilizar o Fornecedor de Cache de Saída Redis. Para obter mais informações sobre a criação e configuração de um Cache Azure para o caso Redis, consulte [Criar uma cache](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

## <a name="store-aspnet-page-output-in-the-cache"></a>Armazenar ASP.NET saída de página no cache

Para configurar uma aplicação de cliente no Estúdio Visual utilizando o pacote Azure Cache para Redis Session State NuGet, clique em **NuGet Package Manager**, **Package Manager Console** do menu **Tools.**

Execute o seguinte comando a partir da janela `Package Manager Console`.

```
Install-Package Microsoft.Web.RedisOutputCacheProvider
```

O pacote NuGet do Fornecedor de Cache de Saída Redis tem uma dependência do pacote StackExchange.Redis.StrongName. Se o pacote StackExchange.Redis.StrongName não estiver presente no seu projeto, está instalado. Para obter mais informações sobre o pacote NuGet do Fornecedor de Cache de Saída Redis, consulte a página [NuGet do RedisOutputCacheProvider.](https://www.nuget.org/packages/Microsoft.Web.RedisOutputCacheProvider/)

>[!NOTE]
>Além do pacote StackExchange.Redis.StrongName, existe também a versão StackExchange.Redis não-strong-named. Se o seu projeto estiver a utilizar a versão StackExchange.Redis não forte, tem de desinstalá-lo; caso contrário, sentirá supor conflitos no seu projeto. Para obter mais informações sobre estes pacotes, consulte [os clientes de cache Configure .NET](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

O pacote NuGet descarrega e adiciona as referências de montagem necessárias e adiciona a seguinte secção no ficheiro web.config. Esta secção contém a configuração necessária para a sua aplicação ASP.NET utilizar o Fornecedor de Cache de Saída Redis.

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

Configure os atributos com os valores da sua lâmina de cache no portal Microsoft Azure e configure os outros valores conforme desejado. Para obter instruções sobre o acesso às suas propriedades cache, consulte [Configure Azure Cache para configurações redis](cache-configure.md#configure-azure-cache-for-redis-settings).

| Atributo | Tipo | Predefinição | Descrição |
| --------- | ---- | ------- | ----------- |
| *anfitrião* | string | "Hospedeiro local" | O endereço IP do servidor Redis ou nome do anfitrião |
| *porta* | inteiro positivo | 6379 (não-TLS/SSL)<br/>6380 (TLS/SSL) | Porta do servidor Redis |
| *acessoChave* | string | "" | Redis palavra-passe do servidor quando a autorização do Redis está ativada. O valor é string vazio por padrão, o que significa que o fornecedor do estado da sessão não usará nenhuma palavra-passe ao ligar-se ao servidor Redis. **Se o seu servidor Redis estiver numa rede acessível ao público como o Azure Redis Cache, certifique-se de permitir a autorização da Redis para melhorar a segurança e fornecer uma senha segura.** |
| *ssl* | boolean | **falso** | Se ligar ao servidor Redis via TLS. Este valor é **falso** por defeito porque redis não suporta TLS fora da caixa. **Se estiver a utilizar o Azure Redis Cache, que suporta o SSL fora da caixa, certifique-se de que o define para melhorar a segurança.**<br/><br/>A porta não-TLS é desativada por padrão para novos caches. Especifique **verdadeiramente** para esta definição utilizar a porta TLS. Para obter mais informações sobre a ativação da porta não-TLS, consulte a secção Portas de [Acesso](cache-configure.md#access-ports) no tópico [de cache Da Configuração.](cache-configure.md) |
| *base de dadosIdNumber* | inteiro positivo | 0 | *Este atributo só pode ser especificado através de web.config ou AppSettings.*<br/><br/>Especifique qual base de dados redis a utilizar. |
| *conexãoTimeOutInMilliseconds* | inteiro positivo | Fornecido por StackExchange.Redis | Usado para definir *ConnectTimeout* ao criar StackExchange.Redis.ConnectionMultiplexer. |
| *operaçãoTimeoutInMilliseconds* | inteiro positivo | Fornecido por StackExchange.Redis | Usado para definir *O SyncTimeout* ao criar StackExchange.Redis.ConnectionMultiplexer. |
| *conexãoString* (string de ligação StackExchange.Redis válido) | string | *n/a* | Ou uma referência paramétrica a AppSettings ou web.config, ou então uma cadeia de ligação StackExchange.Redis válida. Este atributo pode fornecer valores para *hospedeiro,* *porta,* *acessoChave,* *ssl,* e outros atributos StackExchange.Redis. Para uma análise mais atenta da *ligaçãoString*, consulte [definição de ligaçãoString](#setting-connectionstring) na secção [notas do Atributo.](#attribute-notes) |
| *definiçõesClassName*<br/>*definiçõesMethodName* | string<br/>string | *n/a* | *Estes atributos só podem ser especificados através de web.config ou AppSettings.*<br/><br/>Utilize estes atributos para fornecer uma cadeia de ligação. *DefiniçõesClassName* deve ser um nome de classe qualificado de montagem que contenha o método especificado por *definiçõesMethodName*.<br/><br/>O método especificado por *definiçõesO Nome método* deve ser público, estático e vazio (não tomar quaisquer parâmetros), com um tipo de **cadeia**de retorno . Este método devolve a cadeia de ligação real. |
| *logloggingClassName*<br/>*loginMethodName* | string<br/>string | *n/a* | *Estes atributos só podem ser especificados através de web.config ou AppSettings.*<br/><br/>Utilize estes atributos para desinbugicar a sua aplicação fornecendo registos da Session State/Output Cache juntamente com os registos do StackExchange.Redis. *logloggingClassName* deve ser um nome de classe qualificada de montagem que contenha o método especificado por *loginMethodName*.<br/><br/>O método especificado por *loginMethodName* deve ser público, estático e vazio (não tomar quaisquer parâmetros), com um tipo de devolução de **System.IO.TextWriter**. |
| *nome de aplicação* | string | O nome do módulo do processo atual ou "/" | *Apenas SessionStateProvider*<br/>*Este atributo só pode ser especificado através de web.config ou AppSettings.*<br/><br/>O prefixo de nome da aplicação para usar em cache Redis. O cliente pode usar a mesma cache Redis para diferentes fins. Para garantir que as teclas da sessão não colidem, pode ser pré-fixada com o nome da aplicação. |
| *throwOnError* | boolean | true | *Apenas SessionStateProvider*<br/>*Este atributo só pode ser especificado através de web.config ou AppSettings.*<br/><br/>Se lançar uma exceção quando ocorre um erro.<br/><br/>Para mais informações sobre *o throwOnError,* consulte [notas sobre o *throwOnError* ](#notes-on-throwonerror) na secção [de notas do Atributo.](#attribute-notes) |>*Microsoft.Web.Redis.Redis.RedisSessionStateProvider.LastException*. |
| *retryTimeoutInMillisegundos* | inteiro positivo | 5000 | *Apenas SessionStateProvider*<br/>*Este atributo só pode ser especificado através de web.config ou AppSettings.*<br/><br/>Quanto tempo para voltar a tentar quando uma operação falha. Se este valor for inferior ao *funcionamentoTimeoutInMillisegundos*, o fornecedor não voltará a tentar.<br/><br/>Para mais informações sobre *o retryTimeoutInMilliseconds,* consulte Notas sobre o tempo de [tempo *inMilissegundos* ](#notes-on-retrytimeoutinmilliseconds) na secção de [notas do Atributo.](#attribute-notes) |
| *redisSerializerType* | string | *n/a* | Especifica o nome de tipo qualificado de montagem de uma classe que implementa microsoft.Web.Redis. ISerializer e que contém a lógica personalizada para serializar e desserializar os valores. Para mais informações, consulte [sobre *redisSerializerType* ](#about-redisserializertype) na secção [de notas do Atributo.](#attribute-notes) |

## <a name="attribute-notes"></a>Notas de atributo

### <a name="setting-connectionstring"></a>Definição *de conexãoString*

O valor da *ligaçãoString* é usado como chave para obter a cadeia de ligação real a partir de AppSettings, se tal cadeia existir em AppSettings. Se não for encontrado dentro das AppSettings, o valor da *ligação String* será usado como chave para obter a cadeia de ligação real da secção web.config **ConnectionString,** se essa secção existir. Se a cadeia de ligação não existir nas AppSettings ou na secção web.config **ConnectionString,** o valor literal da *ligaçãoString* será usado como cadeia de ligação ao criar StackExchange.Redis.ConnectionMultiplexer.

Os exemplos seguintes ilustram como a *ligação String* é usada.

#### <a name="example-1"></a>Exemplo 1

```xml
<connectionStrings>
    <add name="MyRedisConnectionString" connectionString="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</connectionStrings>
```

Em `web.config`, use acima da tecla como valor de parâmetro em vez de valor real.

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

Em `web.config`, use acima da tecla como valor de parâmetro em vez de valor real.

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

### <a name="notes-on-throwonerror"></a>Notas sobre *o throwOnError*

Atualmente, se ocorrer um erro durante uma operação de sessão, o provedor do estado da sessão lançará uma exceção. Isto encerra o pedido.

Este comportamento foi modificado de forma a suportar as expectativas dos utilizadores existentes ASP.NET sessão de fornecedores estatais, ao mesmo tempo que proporciona a capacidade de agir em exceções, se desejar. O comportamento predefinido ainda abre uma exceção quando ocorre um erro, consistente com outros fornecedores de estado de sessão ASP.NET; o código existente deve funcionar da mesma forma que antes.

Se colocar o *lanceOnError* como **falso**, então em vez de lançar uma exceção quando ocorrer um erro, falhará silenciosamente. Para ver se houve um erro e, em caso afirmativo, descubra qual foi a exceção, verifique a propriedade estática *Microsoft.Web.Redis.Redis.RedisSessionStateProvider.LastException*.

### <a name="notes-on-retrytimeoutinmilliseconds"></a>Notas sobre *o tryTimeoutInMillisegundos*

Isto fornece alguma lógica de retry para simplificar o caso em que alguma operação de sessão deve voltar a tentar o fracasso devido a coisas como falha de rede, ao mesmo tempo que permite controlar o tempo de retry ou optar por não tentar totalmente.

Se definir *o tryTimeoutInMillisegundos* para um número, por exemplo 2000, então quando uma operação de sessão falhar, irá voltar a tentar durante 2000 milissegundos antes de o tratar como um erro. Então, para ter o provedor do estado da sessão para aplicar esta lógica de retry, basta configurar o tempo de tempo. A primeira retentativa acontecerá após 20 milissegundos, o que é suficiente na maioria dos casos quando uma falha de rede acontece. Depois disso, vai voltar a tentar a cada segundo até que se esforce. Logo após o intervalo, tentará novamente mais uma vez para se certificar de que não cortará o tempo de compensação por (no máximo) um segundo.

Se achar que não precisa de ser retentado (por exemplo, quando estiver a executar o servidor Redis na mesma máquina que a sua aplicação) ou se quiser lidar com a lógica de retry, desloque o tempo de *tempo InMilis segundos* para 0.

### <a name="about-redisserializertype"></a>Sobre *redisSerializerType*

Por padrão, a serialização para armazenar os valores em Redis é feita num formato binário fornecido pela classe **BinaryFormatter.** Utilize *redisSerializerType* para especificar o nome de tipo qualificado de montagem de uma classe que implementa **Microsoft.Web.Redis.ISerializer** e tem a lógica personalizada para serializar e desserializar os valores. Por exemplo, aqui está uma aula de serializador Json usando JSON.NET:

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

Assumindo que esta classe é definida num conjunto com o nome **MyCompanyDll,** pode definir o parâmetro *redisSerializerType* para usá-lo:

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

Adicione uma diretiva OutputCache a cada página para a qual deseja reduzir a saída.

```xml
<%@ OutputCache Duration="60" VaryByParam="*" %>
```

No exemplo anterior, os dados da página em cache permanecem na cache durante 60 segundos, e uma versão diferente da página é colocada em cache para cada combinação de parâmetros. Para obter mais informações sobre [@OutputCache](https://go.microsoft.com/fwlink/?linkid=320837)a diretiva OutputCache, consulte .

Uma vez realizados estes passos, a sua aplicação está configurada para utilizar o Fornecedor de Cache de Saída Redis.

## <a name="third-party-output-cache-providers"></a>Fornecedores de cache de saída de terceiros

* [NCache](https://www.alachisoft.com/blogs/how-to-use-a-distributed-cache-for-asp-net-output-cache/)
* [Ignição Apache](https://apacheignite-net.readme.io/docs/aspnet-output-caching)


## <a name="next-steps"></a>Passos seguintes

Confira o [ASP.NET Session State Provider for Azure Cache for Redis](cache-aspnet-session-state-provider.md).
