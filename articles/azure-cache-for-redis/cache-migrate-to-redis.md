---
title: Emigrar aplicações do Serviço de Cache Gerido para redis - Azure
description: Saiba como migrar o Serviço de Cache Gerido e as aplicações de Cache em Papel para Azure Cache para Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 05/30/2017
ms.author: yegu
ms.openlocfilehash: 9596b8cb771f114cb09c5d6c6ae33b4fc4a8cada
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74122681"
---
# <a name="migrate-from-managed-cache-service-to-azure-cache-for-redis"></a>Migrar do Serviço de Cache Gerida para a Cache do Azure para Redis
Migrar as suas aplicações que utilizam o Serviço de Cache Gerido Azure para O Cache Azure para Redis pode ser realizado com alterações mínimas na sua aplicação, dependendo das funcionalidades do Serviço de Cache Gerida sacados utilizadas pela sua aplicação de cache. Embora as APIs não sejam exatamente as mesmas, são semelhantes, e grande parte do seu código existente que utiliza o Serviço de Cache Gerido para aceder a uma cache pode ser reutilizado com alterações mínimas. Este artigo mostra como fazer as alterações de configuração e aplicação necessárias para migrar as suas aplicações do Serviço de Cache Gerida para utilizar o Azure Cache para Redis, e mostra como algumas das funcionalidades do Azure Cache para Redis podem ser usadas para implementar a funcionalidade de um cache service gerido.

>[!NOTE]
>O Serviço de Cache gerido e a Cache em Funções foram [reformados a](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/) 30 de novembro de 2016. Se tiver alguma implantação em Cache em Papel que queira migrar para Azure Cache para Redis, pode seguir os passos deste artigo.

## <a name="migration-steps"></a>Passos de Migração
São necessários os seguintes passos para migrar uma aplicação de Serviço cache gerido para utilizar o Azure Cache para redis.

* Map Managed Cache Service dispõe de Azure Cache para Redis
* Escolha uma oferta de cache
* Criar um Cache
* Configure os Clientes Cache
  * Remova a configuração do serviço de cache gerido
  * Configure um cliente cache usando o pacote StackExchange.Redis NuGet
* Código de serviço de cache gerido migratório
  * Ligue-se à cache utilizando a classe ConnectionMultiplexer
  * Aceder a tipos de dados primitivos na cache
  * Trabalhar com objetos .NET na cache
* Migrar ASP.NET Estado de Sessão e produção para Azure Cache para Redis 

## <a name="map-managed-cache-service-features-to-azure-cache-for-redis"></a>Map Managed Cache Service dispõe de Azure Cache para Redis
O Azure Managed Cache Service e o Azure Cache for Redis são semelhantes, mas implementam algumas das suas funcionalidades de diferentes formas. Esta secção descreve algumas das diferenças e fornece orientações sobre a implementação das funcionalidades do Serviço de Cache Gerido em Azure Cache para Redis.

| Recurso de Serviço cache gerido | Suporte de serviço de cache gerido | Azure Cache para apoio redis |
| --- | --- | --- |
| Caches nomeados |Uma cache padrão é configurada, e nas ofertas de cache Standard e Premium, até nove caches nomeados adicionais podem ser configurados se desejar. |O Azure Cache for Redis tem um número configurável de bases de dados (padrão de 16) que podem ser usadas para implementar uma funcionalidade semelhante às caches nomeadas. Para obter mais informações, veja [O que são as bases de dados Redis?](cache-faq.md#what-are-redis-databases) e [Configuração do servidor predefinido Redis](cache-configure.md#default-redis-server-configuration). |
| Elevada Disponibilidade |Proporciona uma elevada disponibilidade para itens na cache nas ofertas de cache Standard e Premium. Se os itens forem perdidos devido a uma falha, as cópias de backup dos itens na cache ainda estão disponíveis. Os escritos para a cache secundária são feitos sincronizadamente. |A alta disponibilidade está disponível nas ofertas de cache Standard e Premium, que têm uma configuração primária/réplica de dois nós (cada fragmento numa cache Premium tem um par primário/réplica). Os escritos para a réplica são feitos assincronicamente. Para mais informações, consulte [O Cache Azure para obter preços redis](https://azure.microsoft.com/pricing/details/cache/). |
| Notificações |Permite que os clientes recebam notificações assíncronas quando uma variedade de operações de cache ocorrem numa cache com o nome. |As aplicações do cliente podem utilizar notificações de pub/sub ou [Keyspace](cache-configure.md#keyspace-notifications-advanced-settings) Redis para obter uma funcionalidade semelhante às notificações. |
| Cache local |Armazena uma cópia de objetos em cache localmente sobre o cliente para acesso extrarrápido. |As aplicações do cliente teriam de implementar esta funcionalidade utilizando um dicionário ou estrutura de dados semelhante. |
| Política de Despejo |Nenhuma ou LRU. A política padrão é LRU. |Azure Cache for Redis apoia as seguintes políticas de despejo: volátil-lru, allkeys-lru, volátil-random, allkeys-random, volatile-ttl, nodespejo. A política de incumprimento é volátil-lru. Para mais informações, consulte a [configuração do servidor Predefinido Redis](cache-configure.md#default-redis-server-configuration). |
| Política de Expiração |A política de expiração por defeito é absoluta e o intervalo de validade padrão é de 10 minutos. As políticas de sliding e nunca também estão disponíveis. |Por predefinição, os itens na cache não expiram, mas uma expiração pode ser configurada por cada base de escrita utilizando sobrecargas de conjunto de cache. |
| Regiões e Marcação |As regiões são subgrupos para artigos em cache. As regiões também apoiam a anotação de itens em cache com cordas descritivas adicionais chamadas tags. As regiões apoiam a capacidade de realizar operações de busca em quaisquer itens marcados naquela região. Todos os itens dentro de uma região estão localizados dentro de um único nó do aglomerado de cache. |um Azure Cache for Redis consiste num único nó (a menos que o cluster Redis esteja ativado) para que o conceito de regiões de Serviço de Cache Geridonão se aplique. Redis suporta operações de pesquisa e wildcard ao recuperar chaves para que as etiquetas descritivas possam ser incorporadas dentro dos nomes-chave e usadas para recuperar os itens mais tarde. Para um exemplo de implementação de uma solução de marcação utilizando redis, consulte a marcação de [cache implementação com redis](https://stackify.com/implementing-cache-tagging-redis/). |
| Serialização |O Cache gerido suporta o NetDataContractSerializer, binaryFormatter e o uso de serializadors personalizados. O predefinido é NetDataContractSerializer. |É da responsabilidade da aplicação do cliente serializar objetos .NET antes de os colocar na cache, com a escolha do serializador até ao desenvolvedor de aplicações do cliente. Para obter mais informações e código de amostra, consulte [Trabalhar com objetos .NET na cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache). |
| Emulador de cache |O Cache gerido fornece um emulador local. |O Azure Cache para redis não tem um emulador, mas pode [executar a construção mSOpenTech de redis-server.exe localmente](cache-faq.md#cache-emulator) para proporcionar uma experiência de emulador. |

## <a name="choose-a-cache-offering"></a>Escolha uma oferta de cache
O Microsoft Azure Cache for Redis está disponível nos seguintes níveis:

* **Básico** – Único nó. Vários tamanhos até 53 GB.
* **Standard** – Dois nós Primário/Réplica. Vários tamanhos até 53 GB. SLA de 99,9%.
* **Premium** – Dois nós Primário/Réplica com um máximo de 10 shards. Vários tamanhos de 6 GB a 1,2 TB. Todas as funcionalidades do escalão Standard e mais, incluindo o suporte para o [Cluster de Redis](cache-how-to-premium-clustering.md), a [Persistência de Redis](cache-how-to-premium-persistence.md) e a [Azure Virtual Network](cache-how-to-premium-vnet.md). SLA de 99,9%.

Cada escalão difere em termos de funcionalidades e preços. As funcionalidades são abordadas mais tarde neste guia e para mais informações sobre preços, consulte Detalhes de [Preços de Cache](https://azure.microsoft.com/pricing/details/cache/).

Um ponto de partida para a migração é escolher o tamanho que corresponde ao tamanho da sua cache de cache anterior e, em seguida, escalar para cima ou para baixo dependendo dos requisitos da sua aplicação. Para obter mais informações sobre a escolha do Azure Cache certo para a oferta redis, veja [o que azure cache para redis oferecendo e tamanho se eu usar](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use).

## <a name="create-a-cache"></a>Criar um Cache
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="configure-the-cache-clients"></a>Configure os Clientes Cache
Uma vez que a cache é criada e configurada, o próximo passo é remover a configuração do Serviço de Cache Gerido, e adicionar o Cache Azure para configuração Redis e referências para que os clientes cache possam aceder à cache.

* Remova a configuração do serviço de cache gerido
* Configure um cliente cache usando o pacote StackExchange.Redis NuGet

### <a name="remove-the-managed-cache-service-configuration"></a>Remova a configuração do serviço de cache gerido
Antes de as aplicações do cliente poderem ser configuradas para O Cache Azure para Redis, as referências de configuração e montagem do Serviço de Cache geridos existentes devem ser removidas desinstalando o pacote NuGet do Serviço cache gerido.

Para desinstalar o pacote NuGet do Serviço Cache Gerido, clique no projeto do cliente no **Solution Explorer** e escolha **Gerir pacotes NuGet**. Selecione o nó de **pacotes instalados** e o tipo W**dotesAzure.Caching** na caixa de pacotes instalada seletiva. Selecione **Windows** **Azure Cache** (ou **Windows** **Azure Caching** dependendo da versão do pacote NuGet), clique em **Desinstalar**, e depois clique em **Fechar**.

![Pacote NuGet de serviço de cache gerido azure](./media/cache-migrate-to-redis/IC757666.jpg)

Desinstalar o pacote NuGet do Serviço Cache Gerido remove os conjuntos de serviços de cache geridos e as entradas do Serviço cache gerido na app.config ou web.config da aplicação do cliente. Uma vez que algumas definições personalizadas podem não ser removidas ao desinstalar o pacote NuGet, abrir web.config ou app.config e garantir que os seguintes elementos são removidos.

Certifique-se `dataCacheClients` de que `configSections` a entrada é removida do elemento. Não retire todo `configSections` o elemento; basta remover `dataCacheClients` a entrada, se estiver presente.

```xml
<configSections>
  <!-- Existing sections omitted for clarity. -->
  <section name="dataCacheClients"type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection, Microsoft.ApplicationServer.Caching.Core" allowLocation="true" allowDefinition="Everywhere"/>
</configSections>
```

Certifique-se `dataCacheClients` de que a secção é removida. A `dataCacheClients` secção será semelhante ao seguinte exemplo.

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

Uma vez removida a configuração do Serviço cache gerido, pode configurar o cliente cache conforme descrito na seguinte secção.

### <a name="configure-a-cache-client-using-the-stackexchangeredis-nuget-package"></a>Configure um cliente cache usando o pacote StackExchange.Redis NuGet
[!INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

## <a name="migrate-managed-cache-service-code"></a>Código de serviço de cache gerido migratório
A API para o StackExchange.Azure Cache para o cliente Redis é semelhante ao Serviço de Cache Gerido. Esta secção fornece uma visão geral das diferenças.

### <a name="connect-to-the-cache-using-the-connectionmultiplexer-class"></a>Ligue-se à cache utilizando a classe ConnectionMultiplexer
No Serviço de Cache Gerido, as ligações `DataCacheFactory` à `DataCache` cache foram tratadas pelas classes e classes. Em Azure Cache for Redis, estas ligações são geridas pela `ConnectionMultiplexer` classe.

Adicione a seguinte declaração utilizando a parte superior de qualquer ficheiro a partir do qual pretende aceder à cache.

```csharp
using StackExchange.Redis
```

Se este espaço de nome não resolver, certifique-se de que adicionou o pacote StackExchange.Redis NuGet conforme descrito em [Quickstart: Use Azure Cache para Redis com uma aplicação .NET](cache-dotnet-how-to-use-azure-redis-cache.md).

> [!NOTE]
> Note que o cliente StackExchange.Redis requer .NET Framework 4 ou superior.
> 
> 

Para ligar a um Cache Azure para `ConnectionMultiplexer.Connect` a instância Redis, ligue para o método estático e passe no ponto final e na chave. Uma abordagem para partilhar uma instância `ConnectionMultiplexer` na sua aplicação consiste em ter uma propriedade estática que devolva uma instância ligada, tal como no seguinte exemplo. Esta abordagem fornece uma forma segura de `ConnectionMultiplexer` inicializar uma única instância ligada. Neste exemplo `abortConnect` é falso, o que significa que a chamada terá sucesso mesmo que não seja estabelecida uma ligação à cache. Uma funcionalidade-chave do `ConnectionMultiplexer` consiste no restauro automático da conectividade à cache assim que o problema de rede, ou outros problemas, tiverem sido resolvidos.

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

O ponto final da cache, as chaves e as portas podem ser obtidos a partir do **Azure Cache para a** lâmina Redis para a sua instância cache. Para mais informações, consulte [Azure Cache para obter propriedades Redis](cache-configure.md#properties).

Assim que a ligação estiver estabelecida, devolva uma referência `ConnectionMultiplexer.GetDatabase` à base de dados Azure Cache for Redis, ligando para o método. O objeto devolvido do método `GetDatabase` é um objeto pass-through simples e não precisa de ser armazenado.

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

O cliente StackExchange.Redis `RedisKey` `RedisValue` utiliza os tipos e tipos para aceder e armazenar itens na cache. Estes tipos mapeiam na maioria dos tipos de linguagem primitiva, incluindo cordas, e muitas vezes não são usados diretamente. As Cordas Redis são o tipo mais básico de valor Redis, e podem conter muitos tipos de dados, incluindo fluxos binários serializados, e embora você possa não usar o tipo diretamente, você usará métodos que contenham `String` o nome. Para a maioria dos tipos de dados primitivos, `StringSet` armazena e recupera itens da cache utilizando os e `StringGet` métodos, a menos que esteja a armazenar coleções ou outros tipos de dados redis na cache. 

`StringSet`e `StringGet` são semelhantes ao `Put` Serviço `Get` de Cache Gerido e métodos, com uma grande diferença é que antes de definir e obter um objeto .NET na cache deve serializá-lo primeiro. 

Ao `StringGet`ligar, se o objeto existir, é devolvido, e se não o fizer, o nulo é devolvido. Neste caso, pode recuperar o valor da fonte de dados desejada e armazená-lo na cache para posterior utilização. Este padrão é conhecido como o padrão de cache-aside.

Para especificar a expiração de um item na cache, utilize o parâmetro `TimeSpan` de `StringSet`.

```csharp
cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));
```

O Azure Cache for Redis pode funcionar com objetos .NET, bem como tipos de dados primitivos, mas antes de um objeto .NET poder ser colocado, deve ser serializado. Esta serialização é da responsabilidade do desenvolvedor de aplicações, e confere ao desenvolvedor flexibilidade na escolha do serializador. Para obter mais informações e código de amostra, consulte [Trabalhar com objetos .NET na cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

## <a name="migrate-aspnet-session-state-and-output-caching-to-azure-cache-for-redis"></a>Migrar ASP.NET Estado de Sessão e produção para Azure Cache para Redis
O Azure Cache para a Redis tem fornecedores tanto para ASP.NET Session State como para o Page Output. Para migrar a sua aplicação que utiliza as versões do Serviço de Cache Geridadeste fornecedores, primeiro remova as secções existentes do seu web.config e, em seguida, configure o Azure Cache para versões Redis dos fornecedores. Para obter instruções sobre a utilização do Cache Azure para fornecedores de ASP.NET Redis, consulte [ASP.NET Session State Provider for Azure Cache for Redis](cache-aspnet-session-state-provider.md) e ASP.NET Output Cache Provider for [Azure Cache for Redis](cache-aspnet-output-cache-provider.md).

## <a name="next-steps"></a>Passos seguintes
Explore a [documentação Azure Cache para Redis](https://azure.microsoft.com/documentation/services/cache/) para tutoriais, amostras, vídeos e muito mais.

