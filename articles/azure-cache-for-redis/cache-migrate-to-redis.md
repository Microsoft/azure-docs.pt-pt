---
title: Aplicações de Serviço de Cache Geridos migração para Redis - Azure
description: Saiba como migrar aplicações de Cache Geridas e Cache in-Role para Azure Cache para Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/23/2020
ms.author: yegu
ROBOTS: NOINDEX
ms.openlocfilehash: 4e867f28209230cf33b0f94e7cc8ca12d015ff15
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/07/2020
ms.locfileid: "88008564"
---
# <a name="migrate-from-managed-cache-service-to-azure-cache-for-redis-deprecated"></a>Migrar do Serviço de Cache Gerido para Azure Cache para Redis (depreciado)
A migração das suas aplicações que utilizam o Serviço de Cache Gerido Azure para Azure Cache para Redis pode ser realizada com alterações mínimas na sua aplicação, dependendo das funcionalidades do Serviço cache gerido utilizado pela sua aplicação de cache. Embora as APIs não sejam exatamente as mesmas, são semelhantes, e grande parte do seu código existente que utiliza o Serviço de Cache Gerido para aceder a uma cache pode ser reutilizado com alterações mínimas. Este artigo mostra como fazer as alterações de configuração e aplicação necessárias para migrar as suas aplicações do Serviço cache gerido para usar a Cache Azure para Redis, e mostra como algumas das funcionalidades do Azure Cache para Redis podem ser usadas para implementar a funcionalidade de uma cache do Serviço cache gerido.

>[!NOTE]
>O Serviço de Cache gerido e a Cache In-Role foram [retirados a](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/) 30 de novembro de 2016. Se tiver alguma implementação em Cache in-Role que pretenda migrar para Azure Cache para Redis, pode seguir os passos deste artigo.

## <a name="migration-steps"></a>Passos de Migração
São necessárias as seguintes etapas para migrar uma aplicação de Serviço cache gerido para utilizar a Cache Azure para redis.

* Recursos do Serviço cache gerido do mapa para Azure Cache para Redis
* Escolha uma oferta de cache
* Criar uma Cache
* Configure os Clientes Cache
  * Remover a configuração de serviço de cache gerida
  * Configure um cliente cache usando o StackExchange.Redis NuGet Package
* Código de serviço de Cache gerido migrar
  * Ligue-se à cache utilizando a classe ConnectionMultiplexer
  * Aceder a tipos de dados primitivos na cache
  * Trabalhar com objetos .NET na cache
* Migrar ASP.NET Estado da Sessão e Saída caching para Azure Cache para Redis 

## <a name="map-managed-cache-service-features-to-azure-cache-for-redis"></a>Recursos do Serviço cache gerido do mapa para Azure Cache para Redis
O Serviço de Cache Gerido Azure e o Cache Azure para Redis são similares, mas implementam algumas das suas funcionalidades de diferentes maneiras. Esta secção descreve algumas das diferenças e fornece orientações sobre a implementação das características do Serviço de Cache Gerido em Cache Azure para Redis.

| Funcionalidade de Serviço cache gerido | Suporte de serviço cache gerido | Cache Azure para suporte redis |
| --- | --- | --- |
| Caches nomeados |Uma cache padrão é configurada, e nas ofertas de cache Standard e Premium, até nove caches nomeados adicionais podem ser configurados se desejar. |O Azure Cache para Redis tem um número configurável de bases de dados (padrão de 16) que podem ser usadas para implementar uma funcionalidade semelhante a caches nomeados. Para obter mais informações, veja [O que são as bases de dados Redis?](cache-development-faq.md#what-are-redis-databases) e [Configuração do servidor predefinido Redis](cache-configure.md#default-redis-server-configuration). |
| Elevada Disponibilidade |Proporciona alta disponibilidade para itens na cache nas ofertas de cache Standard e Premium. Se os itens forem perdidos devido a uma falha, as cópias de cópias de segurança dos itens na cache ainda estão disponíveis. As gravações para a cache de réplica são feitas sincronizadamente. |A alta disponibilidade está disponível nas ofertas de cache Standard e Premium, que têm uma configuração de dois nós Primário/Réplica (cada fragmento numa cache Premium tem um par primário/réplica). As gravações para a réplica são feitas assíncroneamente. Para mais informações, consulte [a Cache Azure para obter preços de Redis](https://azure.microsoft.com/pricing/details/cache/). |
| Notificações |Permite que os clientes recebam notificações assíncronos quando uma variedade de operações de cache ocorrem em um cache nomeado. |As aplicações do cliente podem usar notificações de pub/sub ou [Keyspace do Redis](cache-configure.md#keyspace-notifications-advanced-settings) para obter uma funcionalidade semelhante às notificações. |
| Cache local |Armazena uma cópia de objetos em cache localmente no cliente para acesso extrarrápido. |As aplicações do cliente teriam de implementar esta funcionalidade utilizando um dicionário ou estrutura de dados semelhante. |
| Política de Despejos |Nenhuma ou LRU. A política por defeito é LRU. |Azure Cache for Redis apoia as seguintes políticas de despejo: volátil-lru, allkeys-lru, volátil-random, allkeys-random, volatile-ttl, noeviction. A política padrão é volátil-lru. Para obter mais informações, consulte [a configuração do servidor Padrão Redis](cache-configure.md#default-redis-server-configuration). |
| Política de Expiração |A política de expiração por defeito é absoluta e o intervalo de validade por defeito é de 10 minutos. As políticas de deslizamento e nunca estão também disponíveis. |Por predefinição, os itens na cache não expiram, mas uma expiração pode ser configurada por base de escrita utilizando sobrecargas definidas por cache. |
| Regiões e Marcação |As regiões são subgrupos para itens em cache. As regiões também suportam a anotação de itens em cache com cordas descritivas adicionais chamadas tags. As regiões apoiam a capacidade de realizar operações de busca em quaisquer itens marcados naquela região. Todos os itens dentro de uma região estão localizados dentro de um único nó do aglomerado de cache. |um Cache Azure para Redis consiste de um único nó (a menos que o cluster Redis esteja habilitado) pelo que o conceito de regiões de Serviço cache gerido não se aplica. O Redis suporta operações de pesquisa e wildcard ao recuperar as chaves para que as tags descritivas possam ser incorporadas nos nomes-chave e usadas para recuperar os itens mais tarde. Para um exemplo de implementação de uma solução de marcação utilizando o Redis, consulte [a marcação de cache de implementação com redis](https://stackify.com/implementing-cache-tagging-redis/). |
| Serialização |A Managed Cache suporta o NetDataContractSerializer, o BinaryFormatter e o uso de serializers personalizados. O padrão é NetDataContractSerializer. |É da responsabilidade da aplicação do cliente serializar objetos .NET antes de os colocar na cache, com a escolha do serializador até ao desenvolvedor de aplicações do cliente. Para obter mais informações e código de amostra, consulte [Trabalhar com objetos .NET na cache.](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache) |
| Emulador de cache |A Cache gerida fornece um emulador de cache local. |Azure Cache para Redis não tem um emulador, mas você pode [executar Redis localmente](cache-development-faq.md#is-there-a-local-emulator-for-azure-cache-for-redis) para fornecer uma experiência emuladora. |

## <a name="choose-a-cache-offering"></a>Escolha uma oferta de cache
Microsoft Azure Cache for Redis está disponível nos seguintes níveis:

* **Básico** – Único nó. Vários tamanhos até 53 GB.
* **Standard** – Dois nós Primário/Réplica. Vários tamanhos até 53 GB. SLA de 99,9%.
* **Premium** – Dois nós Primário/Réplica com um máximo de 10 shards. Vários tamanhos de 6 GB a 1,2 TB. Todas as funcionalidades do escalão Standard e mais, incluindo o suporte para o [Cluster de Redis](cache-how-to-premium-clustering.md), a [Persistência de Redis](cache-how-to-premium-persistence.md) e a [Azure Virtual Network](cache-how-to-premium-vnet.md). SLA de 99,9%.

Cada escalão difere em termos de funcionalidades e preços. As funcionalidades são abordadas mais tarde neste guia, e para obter mais informações sobre preços, consulte [Detalhes de Preços da Cache](https://azure.microsoft.com/pricing/details/cache/).

Um ponto de partida para a migração é escolher o tamanho que corresponde ao tamanho da sua cache anterior do Serviço de Cache Gerido e, em seguida, escalar para cima ou para baixo dependendo dos requisitos da sua aplicação. Para obter mais informações sobre a escolha da cache Azure certa para a oferta redis, consulte [escolher o nível certo.](cache-overview.md#choosing-the-right-tier)

## <a name="create-a-cache"></a>Criar uma Cache
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="configure-the-cache-clients"></a>Configure os Clientes Cache
Uma vez criada e configurada a cache, o próximo passo é remover a configuração do Serviço cache gerido e adicionar a Cache Azure para configuração e referências redis para que os clientes de cache possam aceder à cache.

* Remover a configuração de serviço de cache gerida
* Configure um cliente cache usando o StackExchange.Redis NuGet Package

### <a name="remove-the-managed-cache-service-configuration"></a>Remover a configuração de serviço de cache gerida
Antes de as aplicações do cliente poderem ser configuradas para Azure Cache para Redis, a configuração e referências de montagem do Serviço cache gerido existentes devem ser removidas desinstalando o pacote Desinstalado Serviço cache gerido NuGet.

Para desinstalar o pacote NuGet do Serviço cache gerido, clique com o botão direito no projeto do cliente no **Solution Explorer** e escolha **Gerir pacotes NuGet**. Selecione o nó **de pacotes instalados** e digite W**indowsAzure.Caching** na caixa de pacotes instalados Search. Selecione **o Windows** **Azure Cache** (ou **Caching Windows** **Azure** dependendo da versão do pacote NuGet), clique em **Desinstalar**e, em seguida, clique em **Fechar**.

![Desinstalar pacote nuget de serviço de cache gerido Azure](./media/cache-migrate-to-redis/IC757666.jpg)

Desinstalar o pacote NuGet do Serviço cache gerido remove os conjuntos de Serviço cache gerido e as entradas do Serviço cache gerido no app.config ou web.config da aplicação do cliente. Como algumas definições personalizadas não podem ser removidas ao desinstalar o pacote NuGet, abra web.config ou app.config e certifique-se de que os seguintes elementos são removidos.

Certifique-se de que a `dataCacheClients` entrada é removida do `configSections` elemento. Não retire todo o `configSections` elemento; basta retirar a `dataCacheClients` entrada, se estiver presente.

```xml
<configSections>
  <!-- Existing sections omitted for clarity. -->
  <section name="dataCacheClients"type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection, Microsoft.ApplicationServer.Caching.Core" allowLocation="true" allowDefinition="Everywhere"/>
</configSections>
```

Certifique-se de que a `dataCacheClients` secção é removida. A `dataCacheClients` secção será semelhante ao seguinte exemplo.

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

Uma vez removida a configuração do Serviço cache gerido, pode configurar o cliente cache conforme descrito na secção seguinte.

### <a name="configure-a-cache-client-using-the-stackexchangeredis-nuget-package"></a>Configure um cliente cache usando o StackExchange.Redis NuGet Package
[!INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

## <a name="migrate-managed-cache-service-code"></a>Código de serviço de Cache gerido migrar
A API para o StackExchange.Azure Cache para cliente Redis é semelhante ao Serviço de Cache Gerido. Esta secção fornece uma visão geral das diferenças.

### <a name="connect-to-the-cache-using-the-connectionmultiplexer-class"></a>Ligue-se à cache utilizando a classe ConnectionMultiplexer
No Serviço de Cache Gerido, as ligações à cache foram tratadas pelas `DataCacheFactory` classes e `DataCache` classes. Em Azure Cache para Redis, estas ligações são geridas pela `ConnectionMultiplexer` classe.

Adicione a seguinte declaração usando a parte superior de qualquer ficheiro a partir do qual pretende aceder à cache.

```csharp
using StackExchange.Redis
```

Se este espaço de nome não resolver, certifique-se de que adicionou o pacote StackExchange.Redis NuGet como descrito no [Quickstart: Use Azure Cache para Redis com uma aplicação .NET](cache-dotnet-how-to-use-azure-redis-cache.md).

> [!NOTE]
> Note que o cliente StackExchange.Redis requer .NET Framework 4 ou superior.
> 
> 

Para ligar a uma cache Azure para a instância Redis, ligue para o método estático `ConnectionMultiplexer.Connect` e passe no ponto final e na chave. Uma abordagem para partilhar uma instância `ConnectionMultiplexer` na sua aplicação consiste em ter uma propriedade estática que devolva uma instância ligada, tal como no seguinte exemplo. Esta abordagem proporciona uma forma segura de inicializar uma única `ConnectionMultiplexer` instância ligada. Neste exemplo `abortConnect` é definido como falso, o que significa que a chamada será bem sucedida mesmo que não seja estabelecida uma ligação à cache. Uma funcionalidade-chave do `ConnectionMultiplexer` consiste no restauro automático da conectividade à cache assim que o problema de rede, ou outros problemas, tiverem sido resolvidos.

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

O ponto final da cache, as chaves e as portas podem ser obtidos a partir da **cache Azure para** a lâmina Redis para a sua instância de cache. Para mais informações, consulte [a cache Azure para as propriedades redis.](cache-configure.md#properties)

Uma vez estabelecida a ligação, devolva uma referência à cache Azure para a base de dados Redis, ligando para o `ConnectionMultiplexer.GetDatabase` método. O objeto devolvido do método `GetDatabase` é um objeto pass-through simples e não precisa de ser armazenado.

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

O cliente StackExchange.Redis utiliza os `RedisKey` e tipos para aceder e armazenar `RedisValue` itens na cache. Estes tipos mapeiam para a maioria dos tipos de linguagem primitiva, incluindo cordas, e muitas vezes não são usados diretamente. As Cordas Redis são o tipo mais básico de valor Redis, e podem conter muitos tipos de dados, incluindo fluxos binários serializados, e embora não possa utilizar o tipo diretamente, você usará métodos que contenham `String` o nome. Para a maioria dos tipos de dados primitivos, armazena e recupera itens da cache utilizando os `StringSet` métodos e `StringGet` métodos, a menos que esteja a armazenar coleções ou outros tipos de dados Redis na cache. 

`StringSet`e `StringGet` são semelhantes ao Serviço de Cache Gerido `Put` e `Get` métodos, com uma grande diferença é que antes de definir e colocar um objeto .NET na cache deve serializá-lo primeiro. 

Ao `StringGet` ligar, se o objeto existir, é devolvido e, se não o fizer, o nulo é devolvido. Neste caso, pode recuperar o valor da fonte de dados desejada e armazená-lo na cache para posterior utilização. Este padrão é conhecido como o padrão de cache-aside.

Para especificar a expiração de um item na cache, utilize o parâmetro `TimeSpan` de `StringSet`.

```csharp
cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));
```

A azure Cache para Redis pode trabalhar com objetos .NET, bem como tipos de dados primitivos, mas antes que um objeto .NET possa ser em cache deve ser serializado. Esta serialização é da responsabilidade do desenvolvedor de aplicações, e dá ao desenvolvedor flexibilidade na escolha do serializer. Para obter mais informações e código de amostra, consulte [Trabalhar com objetos .NET na cache.](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache)

## <a name="migrate-aspnet-session-state-and-output-caching-to-azure-cache-for-redis"></a>Migrar ASP.NET Estado da Sessão e Saída caching para Azure Cache para Redis
A Azure Cache para Redis tem fornecedores para ASP.NET sessão state e page output caching. Para migrar a sua aplicação que utiliza as versões Managed Cache Service destes fornecedores, retire primeiro as secções existentes do seu web.config e, em seguida, configuure a Cache Azure para versões Redis dos fornecedores. Para obter instruções sobre a utilização da Cache Azure para os fornecedores de ASP.NET Redis, consulte [ASP.NET Fornecedor de Estado de Sessão para Cache Azure para Redis](cache-aspnet-session-state-provider.md) e ASP.NET Fornecedor de Cache de Saída para Cache de [Azure para Redis](cache-aspnet-output-cache-provider.md).

## <a name="next-steps"></a>Passos seguintes
Explore a [cache Azure para a documentação redis](https://azure.microsoft.com/documentation/services/cache/) para tutoriais, amostras, vídeos e muito mais.

