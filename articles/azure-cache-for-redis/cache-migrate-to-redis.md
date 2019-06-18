---
title: Migrar aplicações de serviço de Cache gerida para Redis - Azure | Documentos da Microsoft
description: Saiba como migrar aplicativos de serviço de Cache gerida e Cache na função para a Cache do Azure para Redis
services: cache
documentationcenter: na
author: yegu-ms
manager: jhubbard
editor: tysonn
ms.assetid: 041f077b-8c8e-4d7c-a3fc-89d334ed70d6
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 05/30/2017
ms.author: yegu
ms.openlocfilehash: 116e54fd39af801cf8941a974da2b72c483097dc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60830300"
---
# <a name="migrate-from-managed-cache-service-to-azure-cache-for-redis"></a>Migrar do serviço de Cache gerida, para a Cache do Azure, para Redis
Migrar seus aplicativos que utilizam o serviço de Cache gerida do Azure para a Cache do Azure para Redis pode ser feito com alterações mínimas à sua aplicação, dependendo dos recursos de serviço de Cache gerida utilizado pela sua aplicação de colocação em cache. Enquanto as APIs não são exatamente os mesmos recursos serem similares e grande parte do seu código existente que utiliza o serviço de Cache gerida para aceder uma cache pode ser reutilizado com alterações mínimas. Este artigo mostra como tornar as configurações necessárias e o aplicativo é alterado para migrar as suas aplicações de serviço de Cache gerida a utilizar a Cache do Azure para Redis e mostra como alguns dos recursos de Cache do Azure para Redis podem ser usado para implementar a funcionalidade de uma cache de serviço de Cache gerida.

>[!NOTE]
>Serviços de Cache gerida e de Cache na função foram [extinto](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/) 30 de Novembro de 2016. Se tiver quaisquer implementações de Cache na função que pretende migrar para o Azure Cache de Redis, pode seguir os passos neste artigo.

## <a name="migration-steps"></a>Passos de migração
Os seguintes passos são necessários para migrar uma aplicação de serviço de Cache gerida a utilizar a Cache do Azure para Redis.

* Mapear recursos de serviço de Cache gerida para a Cache do Azure para Redis
* Escolha uma oferta de Cache
* Criar uma Cache
* Configurar os clientes de Cache
  * Remover a configuração do serviço de Cache gerida
  * Configurar um cliente de cache com o pacote NuGet do stackexchange. redis
* Migrar o código de serviço de Cache gerida
  * Ligar à cache usando a classe ConnectionMultiplexer
  * Tipos de dados primitivos de acesso na cache
  * Trabalhar com objetos .NET na cache
* Migrar o estado de sessão do ASP.NET e a saída de colocação em cache para a Cache do Azure, para Redis 

## <a name="map-managed-cache-service-features-to-azure-cache-for-redis"></a>Mapear recursos de serviço de Cache gerida para a Cache do Azure para Redis
A Cache de Redis do Azure e serviço de Cache gerida do Azure são semelhantes, mas implementam algumas das respetivas funcionalidades de formas diferentes. Esta secção descreve algumas das diferenças e fornece orientação sobre como implementar os recursos do serviço de Cache gerida na Cache do Azure para Redis.

| Funcionalidade de serviço de Cache gerida | Suporte de serviço de Cache gerido | Cache do Azure para o suporte de Redis |
| --- | --- | --- |
| Caches nomeadas |Uma cache de predefinição está configurada e, na cache Standard e Premium podem ser configuradas ofertas, até nove adicionais com o nome caches se assim o desejar. |A Cache de Redis do Azure tem um número configurável de bases de dados (predefinição de 16) que pode ser usado para implementar uma funcionalidade semelhante à caches nomeadas. Para obter mais informações, veja [O que são as bases de dados Redis?](cache-faq.md#what-are-redis-databases) e [Configuração do servidor predefinido Redis](cache-configure.md#default-redis-server-configuration). |
| Elevada Disponibilidade |Fornece elevada disponibilidade para itens na cache nas ofertas de cache Standard e Premium. Se os itens são perdidos devido a uma falha, cópias de segurança dos itens na cache ainda estão disponíveis. Escritas para a cache secundária são efetuadas de forma síncrona. |Elevada disponibilidade está disponível nas ofertas de cache Standard e Premium, que tem uma configuração de primário/réplica de dois nós (cada partição horizontal numa Premium cache tem um par de primário/réplica). Escritas para a réplica são feitas de forma assíncrona. Para obter mais informações, consulte [Cache do Azure para Redis preços](https://azure.microsoft.com/pricing/details/cache/). |
| Notificações |Permite aos clientes receber notificações assíncronas quando ocorrerem várias operações de cache num cache nomeado. |Aplicações cliente podem utilizar o Redis pub/sub ou [notificações Keyspace](cache-configure.md#keyspace-notifications-advanced-settings) para atingir uma funcionalidade semelhante para enviar notificações. |
| Cache local |Armazena uma cópia dos objetos em cache localmente no cliente para um acesso muito rápido. |Aplicativos cliente seriam necessário implementar essa funcionalidade usando um dicionário ou uma estrutura de dados semelhantes. |
| Política de expulsão |Nenhum ou LRU. A política predefinida é LRU. |A Cache de Redis do Azure suporta as seguintes políticas de expulsão: lru volátil, allkeys lru, ttl de volátil volátil aleatório, allkeys-aleatório, noeviction. A política predefinida é lru volátil. Para obter mais informações, consulte [configuração do servidor predefinido Redis](cache-configure.md#default-redis-server-configuration). |
| Política de expiração |A política de expiração predefinida é absoluto e o intervalo de validade predefinido é de 10 minutos. As políticas de deslizante e nunca também estão disponíveis. |Por padrão, isso não ocorra itens na cache, mas uma expiração pode ser configurada numa base por escrita usando sobrecargas de conjunto de cache. |
| Regiões e etiquetagem |Regiões estão subgrupos para itens em cache. Regiões também suportam a anotação de itens em cache com adicionais seqüências de caracteres descritivas, chamadas de etiquetas. Regiões suportam a capacidade de executar operações de pesquisa em todos os itens marcados nessa região. Todos os itens dentro de uma região estão localizados num único nó de cluster de cache. |uma Cache do Azure para Redis consiste num único nó (a menos que o cluster de Redis está ativado) para que o conceito de regiões de serviço de Cache gerida não se aplica. O redis oferece suporte à pesquisa e as operações de caráter universal ao obter as chaves para que marcas descritivas podem ser incorporadas dentro os nomes de chaves e usadas para recuperar os itens mais tarde. Para obter um exemplo de implementação de uma solução de etiquetagem através do Redis, veja [implementando o cache de marcação com Redis](https://stackify.com/implementing-cache-tagging-redis/). |
| Serialização |Cache gerida suporta o NetDataContractSerializer, BinaryFormatter e a utilização de Serializadores personalizados. A predefinição é o NetDataContractSerializer. |É da responsabilidade da aplicação cliente para serializar objetos .NET antes de colocá-las para a cache, com a escolher o serializador pretendido cargo do desenvolvedor do aplicativo de cliente. Para obter mais informações e código de exemplo, consulte [trabalhar com objetos .NET na cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache). |
| Emulador de cache |Cache gerida fornece um emulador de local cache. |A Cache de Redis do Azure não tem um emulador, mas pode [executar localmente a compilação de MSOpenTech do redis server.exe](cache-faq.md#cache-emulator) para proporcionar uma experiência de emulador. |

## <a name="choose-a-cache-offering"></a>Escolha uma oferta de Cache
Cache de Redis do Microsoft Azure está disponível nos seguintes escalões:

* **Básico** – Único nó. Vários tamanhos até 53 GB.
* **Standard** – Dois nós Primário/Réplica. Vários tamanhos até 53 GB. SLA de 99,9%.
* **Premium** – Dois nós Primário/Réplica com um máximo de 10 shards. Vários tamanhos, de 6 GB a 530 GB. Todas as funcionalidades do escalão Standard e mais, incluindo o suporte para o [Cluster de Redis](cache-how-to-premium-clustering.md), a [Persistência de Redis](cache-how-to-premium-persistence.md) e a [Azure Virtual Network](cache-how-to-premium-vnet.md). SLA de 99,9%.

Cada escalão difere em termos de funcionalidades e preços. As funcionalidades são abrangidas mais adiante neste guia e para obter mais informações sobre os preços, consulte [detalhes dos preços da Cache](https://azure.microsoft.com/pricing/details/cache/).

Um ponto de partida para a migração é escolher o tamanho que corresponde ao tamanho da cache, o serviço de Cache gerida anterior e, em seguida, aumente ou reduza verticalmente consoante os requisitos da sua aplicação. Para obter mais informações sobre como escolher a Cache do Azure adequada para a oferta de Redis, consulte [que a Cache do Azure para o tamanho e oferta de Redis devo utilizar](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use).

## <a name="create-a-cache"></a>Criar uma Cache
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="configure-the-cache-clients"></a>Configurar os clientes de Cache
Assim que o cache é criada e configurada, a próxima etapa é remover a configuração do serviço de Cache gerida e adicionar a Cache do Azure para a configuração de Redis e faz referência para que os clientes de cache podem aceder à cache.

* Remover a configuração do serviço de Cache gerida
* Configurar um cliente de cache com o pacote NuGet do stackexchange. redis

### <a name="remove-the-managed-cache-service-configuration"></a>Remover a configuração do serviço de Cache gerida
Antes das aplicações cliente podem ser configuradas para a Cache do Azure para Redis, o existente Managed Cache Service referências de assembly de configuração e tem de ser removidas ao desinstalar o pacote NuGet do serviço de Cache gerida.

Para desinstalar o pacote NuGet do serviço de Cache gerida, com o botão direito no projeto de cliente no **Explorador de soluções** e escolha **gerir pacotes NuGet**. Selecione o **pacotes instalados** nó e o tipo W**indowsAzure.Caching** na caixa de pacotes instalados de pesquisa. Selecione **Windows** **Cache do Azure** (ou **Windows** **cache do Azure** dependendo da versão do pacote NuGet), clique em **Uninstall**e, em seguida, clique em **fechar**.

![Desinstalar o pacote de NuGet do serviço de Cache gerida do Azure](./media/cache-migrate-to-redis/IC757666.jpg)

Desinstalar o pacote NuGet do serviço de Cache gerida remove os assemblies de serviço de Cache gerida e as entradas de serviço de Cache gerida no App. config ou Web. config do aplicativo cliente. Uma vez que algumas definições personalizadas não podem ser removidas quando desinstalar o pacote NuGet, abra o Web. config ou App. config e certifique-se de que os seguintes elementos são removidos.

Certifique-se de que o `dataCacheClients` entrada é removida do `configSections` elemento. Não remova toda a `configSections` elemento; vamos remover a `dataCacheClients` entrada, se estiver presente.

```xml
<configSections>
  <!-- Existing sections omitted for clarity. -->
  <section name="dataCacheClients"type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection, Microsoft.ApplicationServer.Caching.Core" allowLocation="true" allowDefinition="Everywhere"/>
</configSections>
```

Certifique-se de que o `dataCacheClients` secção é removida. O `dataCacheClients` secção será semelhante ao seguinte exemplo.

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

Assim que a configuração do serviço de Cache gerida for removida, pode configurar o cliente de cache, conforme descrito na secção seguinte.

### <a name="configure-a-cache-client-using-the-stackexchangeredis-nuget-package"></a>Configurar um cliente de cache com o pacote NuGet do stackexchange. redis
[!INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

## <a name="migrate-managed-cache-service-code"></a>Migrar o código de serviço de Cache gerida
A API para a Cache de StackExchange.Azure para o cliente de Redis é semelhante ao serviço de Cache gerida. Esta seção fornece uma descrição geral das diferenças.

### <a name="connect-to-the-cache-using-the-connectionmultiplexer-class"></a>Ligar à cache usando a classe ConnectionMultiplexer
No serviço de Cache gerida, as ligações para a cache foram processadas pela `DataCacheFactory` e `DataCache` classes. Na Cache de Redis do Azure, estas ligações são geridas pelo `ConnectionMultiplexer` classe.

Adicione a seguinte instrução na parte superior de qualquer ficheiro a partir do qual pretende aceder à cache a utilizar.

```csharp
using StackExchange.Redis
```

Se não resolver este espaço de nomes, certifique-se de que adicionou o pacote NuGet do stackexchange. redis, tal como descrito no [início rápido: Utilizar a Cache do Azure para Redis com um aplicativo .NET](cache-dotnet-how-to-use-azure-redis-cache.md).

> [!NOTE]
> Tenha em atenção que o cliente stackexchange. redis requer o .NET Framework 4 ou superior.
> 
> 

Para ligar a uma Cache do Azure para a instância de Redis, chame o estático `ConnectionMultiplexer.Connect` método e passe o ponto final e a chave. Uma abordagem para partilhar uma instância `ConnectionMultiplexer` na sua aplicação consiste em ter uma propriedade estática que devolva uma instância ligada, tal como no seguinte exemplo. Esta abordagem fornece uma forma segura para thread para inicializar uma única ligada `ConnectionMultiplexer` instância. Neste exemplo `abortConnect` está definido como FALSO, o que significa que a chamada terá êxito mesmo que não é possível estabelecer uma ligação para a cache. Uma funcionalidade-chave do `ConnectionMultiplexer` consiste no restauro automático da conectividade à cache assim que o problema de rede, ou outros problemas, tiverem sido resolvidos.

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

O ponto final da cache, as chaves e as portas podem ser obtidas a partir da **a Cache de Redis do Azure** painel para a instância da cache. Para obter mais informações, consulte [Cache do Azure para as propriedades de Redis](cache-configure.md#properties).

Assim que a ligação é estabelecida, devolver uma referência para a Cache do Azure para a base de dados de Redis, chamando o `ConnectionMultiplexer.GetDatabase` método. O objeto devolvido do método `GetDatabase` é um objeto pass-through simples e não precisa de ser armazenado.

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

O cliente stackexchange. redis utiliza o `RedisKey` e `RedisValue` tipos para acessar e armazenamento de itens na cache. Estes tipos de mapeiam para o primitivos mais tipos de linguagens, incluindo a cadeia de caracteres e muitas vezes não são usados diretamente. Redis cadeias de caracteres são o tipo mais básico do valor de Redis e podem conter vários tipos de dados, incluindo fluxos de binários serializados, e enquanto não pode utilizar o tipo diretamente, que irá utilizar os métodos que contêm `String` no nome. Para tipos de dados mais primitivos, armazenar e obter itens a partir da cache com o `StringSet` e `StringGet` métodos, a menos que estiver armazenando coleções ou outros tipos de dados de Redis no cache. 

`StringSet` e `StringGet` são semelhantes para o Managed Cache Service `Put` e `Get` métodos, com uma grande diferença é que antes de definir e obter um objeto .NET para a cache tem serializá-lo primeiro. 

Ao chamar `StringGet`, se o objeto existir, será devolvido e, se não for retornado nulo. Neste caso, pode obter o valor da fonte de dados pretendida e armazená-lo na cache para uso subseqüente. Este padrão é conhecido como o padrão cache-aside.

Para especificar a expiração de um item na cache, utilize o parâmetro `TimeSpan` de `StringSet`.

```csharp
cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));
```

A Cache de Redis do Azure pode trabalhar com objetos .NET, bem como tipos de dados primitivos, mas para que um objeto .NET pode ser colocado em cache tem de ser serializado. Esta serialização é da responsabilidade do programador da aplicação e fornece a flexibilidade de programador escolher o serializador pretendido. Para obter mais informações e código de exemplo, consulte [trabalhar com objetos .NET na cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

## <a name="migrate-aspnet-session-state-and-output-caching-to-azure-cache-for-redis"></a>Migrar o estado de sessão do ASP.NET e a saída de colocação em cache para a Cache do Azure, para Redis
A Cache de Redis do Azure tem fornecedores para o estado de sessão do ASP.NET e cache de saída de página. Para migrar a sua aplicação que utiliza as versões de serviço de Cache gerida desses provedores, remova primeiro as seções existentes do Web. config e, em seguida, configurar a Cache do Azure para versões de Redis dos fornecedores. Para obter instruções sobre como utilizar a Cache do Azure para fornecedores de Redis ASP.NET, consulte [fornecedor de estado de sessão do ASP.NET para a Cache de Redis do Azure](cache-aspnet-session-state-provider.md) e [fornecedor de Cache de saída do ASP.NET para a Cache de Redis do Azure](cache-aspnet-output-cache-provider.md).

## <a name="next-steps"></a>Passos Seguintes
Explore os [Cache do Azure para a documentação do Redis](https://azure.microsoft.com/documentation/services/cache/) tutoriais, exemplos, vídeos e muito mais.

