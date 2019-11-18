---
title: Migrar aplicativos do serviço de cache gerenciado para o Redis-Azure
description: Saiba como migrar aplicativos de Cache na Função e serviço de cache gerenciado para o cache do Azure para Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 05/30/2017
ms.author: yegu
ms.openlocfilehash: 9596b8cb771f114cb09c5d6c6ae33b4fc4a8cada
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122681"
---
# <a name="migrate-from-managed-cache-service-to-azure-cache-for-redis"></a>Migrar do serviço de cache gerenciado para o cache do Azure para Redis
Migrar seus aplicativos que usam o serviço de cache gerenciado do Azure para o cache do Azure para Redis pode ser realizado com alterações mínimas em seu aplicativo, dependendo dos recursos do serviço de cache Gerenciado usados pelo seu aplicativo de cache. Embora as APIs não sejam exatamente as mesmas, elas são semelhantes, e grande parte do código existente que usa o serviço de cache gerenciado para acessar um cache pode ser reutilizada com alterações mínimas. Este artigo mostra como fazer as alterações necessárias na configuração e no aplicativo para migrar seus aplicativos do serviço de cache gerenciado para usar o cache do Azure para Redis e mostra como alguns dos recursos do cache do Azure para Redis podem ser usados para implementar a funcionalidade do um cache do serviço de cache gerenciado.

>[!NOTE]
>O serviço de cache gerenciado e Cache na Função foram [desativados](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/) em 30 de novembro de 2016. Se você tiver Cache na Função implantações que deseja migrar para o cache do Azure para Redis, poderá seguir as etapas neste artigo.

## <a name="migration-steps"></a>Etapas de migração
As etapas a seguir são necessárias para migrar um aplicativo de serviço de cache gerenciado para usar o cache do Azure para Redis.

* Mapear recursos do serviço de cache gerenciado para o cache do Azure para Redis
* Escolher uma oferta de cache
* Criar um cache
* Configurar os clientes de cache
  * Remover a configuração do serviço de cache gerenciado
  * Configurar um cliente de cache usando o pacote NuGet StackExchange. Redis
* Migrar código do serviço de cache gerenciado
  * Conectar-se ao cache usando a classe ConnectionMultiplexer
  * Acessar tipos de dados primitivos no cache
  * Trabalhar com objetos .NET na cache
* Migrar o estado de sessão ASP.NET e o cache de saída para o cache do Azure para Redis 

## <a name="map-managed-cache-service-features-to-azure-cache-for-redis"></a>Mapear recursos do serviço de cache gerenciado para o cache do Azure para Redis
O serviço de cache gerenciado do Azure e o cache do Azure para Redis são semelhantes, mas implementam alguns de seus recursos de maneiras diferentes. Esta seção descreve algumas das diferenças e fornece orientação sobre como implementar os recursos do serviço de cache gerenciado no cache do Azure para Redis.

| Recurso de serviço de cache gerenciado | Suporte ao serviço de cache gerenciado | Cache do Azure para suporte a Redis |
| --- | --- | --- |
| Caches nomeados |Um cache padrão é configurado e, nas ofertas de cache Standard e Premium, até nove caches nomeados adicionais podem ser configurados se desejado. |O cache do Azure para Redis tem um número configurável de bancos de dados (padrão de 16) que pode ser usado para implementar uma funcionalidade semelhante para caches nomeados. Para obter mais informações, veja [O que são as bases de dados Redis?](cache-faq.md#what-are-redis-databases) e [Configuração do servidor predefinido Redis](cache-configure.md#default-redis-server-configuration). |
| Elevada Disponibilidade |Fornece alta disponibilidade para itens no cache nas ofertas de cache Standard e Premium. Se os itens forem perdidos devido a uma falha, as cópias de backup dos itens no cache ainda estarão disponíveis. As gravações no cache secundário são feitas de forma síncrona. |A alta disponibilidade está disponível nas ofertas de cache Standard e Premium, que têm uma configuração primária/réplica de dois nós (cada fragmento em um cache Premium tem um par primário/de réplica). As gravações na réplica são feitas de forma assíncrona. Para obter mais informações, consulte [Azure cache for Redis Pricing](https://azure.microsoft.com/pricing/details/cache/). |
| Notificações |Permite que os clientes recebam notificações assíncronas quando várias operações de cache ocorrem em um cache nomeado. |Os aplicativos cliente podem usar as notificações Redis pub/sub ou [keyspace](cache-configure.md#keyspace-notifications-advanced-settings) para obter uma funcionalidade semelhante às notificações. |
| Cache local |Armazena uma cópia de objetos armazenados em cache localmente no cliente para acesso extra e rápido. |Os aplicativos cliente precisariam implementar essa funcionalidade usando um dicionário ou uma estrutura de dados semelhante. |
| Política de remoção |Nenhum ou LRU. A política padrão é LRU. |O cache do Azure para Redis dá suporte às seguintes políticas de remoção: volátil-LRU, AllKeys-LRU, volátil-aleatório, AllKeys-Random, volátil-TTL, noremotion. A política padrão é volátil-LRU. Para obter mais informações, consulte [configuração padrão do servidor Redis](cache-configure.md#default-redis-server-configuration). |
| Política de expiração |A política de expiração padrão é absoluta e o intervalo de expiração padrão é 10 minutos. As políticas deslizantes e nunca também estão disponíveis. |Por padrão, os itens no cache não expiram, mas uma expiração pode ser configurada por gravação usando sobrecargas de conjunto de cache. |
| Regiões e marcação |As regiões são subgrupos para itens armazenados em cache. As regiões também dão suporte à anotação de itens armazenados em cache com cadeias de caracteres descritivas adicionais chamadas marcas. As regiões dão suporte à capacidade de executar operações de pesquisa em todos os itens marcados nessa região. Todos os itens dentro de uma região estão localizados em um único nó do cluster de cache. |um cache do Azure para Redis consiste em um único nó (a menos que o cluster Redis esteja habilitado), de modo que o conceito de regiões do serviço de cache gerenciado não se aplica. O Redis dá suporte a operações de pesquisa e curinga ao recuperar chaves para que marcas descritivas possam ser inseridas dentro dos nomes de chave e usadas para recuperar os itens posteriormente. Para obter um exemplo de implementação de uma solução de marcação usando o Redis, consulte [implementando a marcação de cache com Redis](https://stackify.com/implementing-cache-tagging-redis/). |
| Série |O cache gerenciado dá suporte a NetDataContractSerializer, BinaryFormatter e ao uso de serializadores personalizados. O padrão é NetDataContractSerializer. |É responsabilidade do aplicativo cliente serializar objetos .NET antes de colocá-los no cache, com a escolha do serializador até o desenvolvedor do aplicativo cliente. Para obter mais informações e código de exemplo, consulte [trabalhar com objetos .net no cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache). |
| Emulador de cache |O cache gerenciado fornece um emulador de cache local. |O cache do Azure para Redis não tem um emulador, mas você pode [executar a compilação MSOpenTech do Redis-Server. exe localmente](cache-faq.md#cache-emulator) para fornecer uma experiência de emulador. |

## <a name="choose-a-cache-offering"></a>Escolher uma oferta de cache
O cache Microsoft Azure para Redis está disponível nas seguintes camadas:

* **Básico** – Único nó. Vários tamanhos até 53 GB.
* **Standard** – Dois nós Primário/Réplica. Vários tamanhos até 53 GB. SLA de 99,9%.
* **Premium** – Dois nós Primário/Réplica com um máximo de 10 shards. Vários tamanhos de 6 GB a 1,2 TB. Todas as funcionalidades do escalão Standard e mais, incluindo o suporte para o [Cluster de Redis](cache-how-to-premium-clustering.md), a [Persistência de Redis](cache-how-to-premium-persistence.md) e a [Azure Virtual Network](cache-how-to-premium-vnet.md). SLA de 99,9%.

Cada escalão difere em termos de funcionalidades e preços. Os recursos são abordados posteriormente neste guia e, para obter mais informações sobre preços, consulte [detalhes de preços de cache](https://azure.microsoft.com/pricing/details/cache/).

Um ponto de partida para a migração é escolher o tamanho que corresponde ao tamanho do cache do serviço de cache gerenciado anterior e, em seguida, escalar ou reduzir verticalmente, dependendo dos requisitos do seu aplicativo. Para obter mais informações sobre como escolher o cache do Azure correto para oferta de Redis, consulte [qual o cache do Azure para oferta e tamanho do Redis devo usar](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use).

## <a name="create-a-cache"></a>Criar um cache
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="configure-the-cache-clients"></a>Configurar os clientes de cache
Depois que o cache é criado e configurado, a próxima etapa é remover a configuração do serviço de cache gerenciado e adicionar o cache do Azure para a configuração e as referências do Redis para que os clientes de cache possam acessar o cache.

* Remover a configuração do serviço de cache gerenciado
* Configurar um cliente de cache usando o pacote NuGet StackExchange. Redis

### <a name="remove-the-managed-cache-service-configuration"></a>Remover a configuração do serviço de cache gerenciado
Antes que os aplicativos cliente possam ser configurados para o cache do Azure para Redis, a configuração existente do serviço de cache gerenciado e as referências de assembly devem ser removidas desinstalando o pacote NuGet do serviço de cache gerenciado.

Para desinstalar o pacote NuGet do serviço de cache gerenciado, clique com o botão direito do mouse no projeto cliente no **Gerenciador de soluções** e escolha **gerenciar pacotes NuGet**. Selecione o nó **pacotes instalados** e digite W**indowsAzure. Caching** na caixa Pesquisar pacotes instalados. Selecione **Windows** **Azure cache** (ou **Windows** **Azure Caching** dependendo da versão do pacote NuGet), clique em **desinstalar**e, em seguida, clique em **fechar**.

![Desinstalar o pacote NuGet do serviço de cache gerenciado do Azure](./media/cache-migrate-to-redis/IC757666.jpg)

A desinstalação do pacote NuGet do serviço de cache gerenciado remove os assemblies do serviço de cache gerenciado e as entradas do serviço de cache gerenciado no app. config ou Web. config do aplicativo cliente. Como algumas configurações personalizadas podem não ser removidas ao desinstalar o pacote NuGet, abra o Web. config ou o app. config e verifique se os elementos a seguir foram removidos.

Certifique-se de que a entrada de `dataCacheClients` seja removida do elemento `configSections`. Não remova todo o elemento `configSections`; Basta remover a entrada `dataCacheClients`, se ela estiver presente.

```xml
<configSections>
  <!-- Existing sections omitted for clarity. -->
  <section name="dataCacheClients"type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection, Microsoft.ApplicationServer.Caching.Core" allowLocation="true" allowDefinition="Everywhere"/>
</configSections>
```

Verifique se a seção `dataCacheClients` foi removida. A seção `dataCacheClients` será semelhante ao exemplo a seguir.

```xml
<dataCacheClients>
  <dataCacheClientname="default">
    <!--To use the in-role flavor of Azure Cache, set identifier to be the cache cluster role name -->
    <!--To use the Azure Managed Cache Service, set identifier to be the endpoint of the cache cluster -->
    <autoDiscoverisEnabled="true"identifier="[Cache role name or Service Endpoint]"/>

    <!--<localCache isEnabled="true" sync="TimeoutBased" objectCount="100000" ttlValue="300" />-->
    <!--Use this section to specify security settings for connecting to your cache. This section is not required if your cache is hosted on a role that is a part of your cloud service. -->
    <!--<securityProperties mode="Message" sslEnabled="true">
      <messageSecurity authorizationInfo="[Authentication Key]" />
    </securityProperties>-->
  </dataCacheClient>
</dataCacheClients>
```

Depois que a configuração do serviço de cache gerenciado for removida, você poderá configurar o cliente de cache, conforme descrito na seção a seguir.

### <a name="configure-a-cache-client-using-the-stackexchangeredis-nuget-package"></a>Configurar um cliente de cache usando o pacote NuGet StackExchange. Redis
[!INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

## <a name="migrate-managed-cache-service-code"></a>Migrar código do serviço de cache gerenciado
A API para o cache StackExchange. Azure para o cliente Redis é semelhante ao serviço de cache gerenciado. Esta seção fornece uma visão geral das diferenças.

### <a name="connect-to-the-cache-using-the-connectionmultiplexer-class"></a>Conectar-se ao cache usando a classe ConnectionMultiplexer
No serviço de cache gerenciado, as conexões com o cache foram manipuladas pelas classes `DataCacheFactory` e `DataCache`. No cache do Azure para Redis, essas conexões são gerenciadas pela classe `ConnectionMultiplexer`.

Adicione a seguinte instrução using à parte superior de qualquer arquivo do qual você deseja acessar o cache.

```csharp
using StackExchange.Redis
```

Se esse namespace não resolver, certifique-se de ter adicionado o pacote NuGet StackExchange. Redis conforme descrito em [início rápido: usar o cache do Azure para Redis com um aplicativo .net](cache-dotnet-how-to-use-azure-redis-cache.md).

> [!NOTE]
> Observe que o cliente StackExchange. Redis requer .NET Framework 4 ou superior.
> 
> 

Para se conectar a um cache do Azure para a instância Redis, chame o método estático `ConnectionMultiplexer.Connect` e passe o ponto de extremidade e a chave. Uma abordagem para partilhar uma instância `ConnectionMultiplexer` na sua aplicação consiste em ter uma propriedade estática que devolva uma instância ligada, tal como no seguinte exemplo. Essa abordagem fornece uma maneira thread-safe de inicializar uma única instância de `ConnectionMultiplexer` conectada. Neste exemplo `abortConnect` é definido como false, o que significa que a chamada terá sucesso mesmo se uma conexão com o cache não for estabelecida. Uma funcionalidade-chave do `ConnectionMultiplexer` consiste no restauro automático da conectividade à cache assim que o problema de rede, ou outros problemas, tiverem sido resolvidos.

```csharp
private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
{
    return ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");
});

public static ConnectionMultiplexer Connection
{
    get
    {
        return lazyConnection.Value;
    }
}
```

O ponto de extremidade, as chaves e as portas do cache podem ser obtidos na folha **cache do Azure para Redis** para sua instância de cache. Para obter mais informações, consulte [cache do Azure para propriedades Redis](cache-configure.md#properties).

Depois que a conexão for estabelecida, retorne uma referência ao cache do Azure para o banco de dados Redis chamando o método `ConnectionMultiplexer.GetDatabase`. O objeto devolvido do método `GetDatabase` é um objeto pass-through simples e não precisa de ser armazenado.

```csharp
IDatabase cache = Connection.GetDatabase();

// Perform cache operations using the cache object...
// Simple put of integral data types into the cache
cache.StringSet("key1", "value");
cache.StringSet("key2", 25);

// Simple get of data types from the cache
string key1 = cache.StringGet("key1");
int key2 = (int)cache.StringGet("key2");
```

O cliente StackExchange. Redis usa os tipos `RedisKey` e `RedisValue` para acessar e armazenar itens no cache. Esses tipos mapeiam para a maioria dos tipos de linguagem primitivos, incluindo cadeia de caracteres, e geralmente não são usados diretamente. As cadeias de caracteres Redis são o tipo mais básico de valor de Redis e podem conter muitos tipos de dados, incluindo fluxos binários serializados e, embora você não possa usar o tipo diretamente, você usará métodos que contenham `String` no nome. Para a maioria dos tipos de dados primitivos, você armazena e recupera itens do cache usando os métodos `StringSet` e `StringGet`, a menos que você esteja armazenando coleções ou outros tipos de dados Redis no cache. 

`StringSet` e `StringGet` são semelhantes aos métodos de `Put` e `Get` do serviço de cache gerenciado, com uma grande diferença de que, antes de definir e obter um objeto .NET no cache, você deve serializá-lo primeiro. 

Ao chamar `StringGet`, se o objeto existir, ele será retornado e, se não for, NULL será retornado. Nesse caso, você pode recuperar o valor da fonte de dados desejada e armazená-lo no cache para uso posterior. Esse padrão é conhecido como o padrão de reserva de cache.

Para especificar a expiração de um item na cache, utilize o parâmetro `TimeSpan` de `StringSet`.

```csharp
cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));
```

O cache do Azure para Redis pode trabalhar com objetos .NET, bem como tipos de dados primitivos, mas antes que um objeto .NET possa ser armazenado em cache, ele deve ser serializado. Essa serialização é responsabilidade do desenvolvedor do aplicativo e oferece a flexibilidade do desenvolvedor na escolha do serializador. Para obter mais informações e código de exemplo, consulte [trabalhar com objetos .net no cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

## <a name="migrate-aspnet-session-state-and-output-caching-to-azure-cache-for-redis"></a>Migrar o estado de sessão ASP.NET e o cache de saída para o cache do Azure para Redis
O cache do Azure para Redis tem provedores para o estado de sessão do ASP.NET e o cache de saída de página. Para migrar seu aplicativo que usa as versões do serviço de cache gerenciado desses provedores, primeiro remova as seções existentes do Web. config e, em seguida, configure o cache do Azure para versões Redis dos provedores. Para obter instruções sobre como usar o cache do Azure para provedores do Redis ASP.NET, consulte [provedor de estado de sessão ASP.net para o cache do Azure para Redis](cache-aspnet-session-state-provider.md) e [ASP.NET provedor de cache de saída para o cache do Azure para Redis](cache-aspnet-output-cache-provider.md).

## <a name="next-steps"></a>Passos seguintes
Explore a [documentação do cache do Azure para Redis](https://azure.microsoft.com/documentation/services/cache/) para obter tutoriais, exemplos, vídeos e muito mais.

