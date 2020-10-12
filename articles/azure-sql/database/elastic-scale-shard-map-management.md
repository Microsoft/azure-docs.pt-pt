---
title: Escale uma base de dados
description: Como utilizar o ShardMapManager, biblioteca de clientes de base de dados elástica
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 503a55bf49d97f00f26044aef3e19b0fec58b37d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84047476"
---
# <a name="scale-out-databases-with-the-shard-map-manager"></a>Escale as bases de dados com o gestor de mapas de fragmentos
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Para escalar facilmente as bases de dados na Base de Dados Azure SQL, utilize um gestor de mapas de fragmentos. O gestor de mapas de fragmentos é uma base de dados especial que mantém informações globais de mapeamento sobre todos os fragmentos (bases de dados) num conjunto de fragmentos. Os metadados permitem que uma aplicação se conecte à base de dados correta com base no valor **da chave de fragmentos.** Além disso, cada fragmento do conjunto contém mapas que rastreiam os dados de fragmentos locais (conhecidos como **shardlets).**

![Gestão de mapas de partições horizontais](./media/elastic-scale-shard-map-management/glossary.png)

Compreender como estes mapas são construídos é essencial para a gestão de mapas de fragmentos. Isto é feito usando a classe ShardMapManager[(Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager), encontrada na [biblioteca de clientes Elastic Database](elastic-database-client-library.md) para gerir mapas de fragmentos.  

## <a name="shard-maps-and-shard-mappings"></a>Mapas de fragmentos e mapeamentos de fragmentos

Para cada fragmento, deve selecionar o tipo de mapa de fragmentos para criar. A escolha depende da arquitetura da base de dados:

1. Inquilino único por base de dados  
2. Vários inquilinos por base de dados (dois tipos):
   1. Mapeamento de lista
   2. Mapeamento de gama

Para um modelo de inquilino único, crie um mapa **de fragmentos de mapeamento de listas.** O modelo de inquilino único atribui uma base de dados por inquilino. Este é um modelo eficaz para os desenvolvedores do SaaS, uma vez que simplifica a gestão.

![Mapeamento de lista][1]

O modelo multi-inquilino atribui vários inquilinos a uma base de dados individual (e pode distribuir grupos de inquilinos através de várias bases de dados). Use este modelo quando espera que cada inquilino tenha pequenas necessidades de dados. Neste modelo, atribua uma gama de inquilinos a uma base de dados utilizando **mapeamento de gama.**

![Mapeamento de gama][2]

Ou pode implementar um modelo de base de dados multi-inquilino usando um *mapeamento de lista* para atribuir vários inquilinos a uma base de dados individual. Por exemplo, o DB1 é usado para armazenar informações sobre o ID 1 e 5 do inquilino, e dB2 armazena dados para inquilino 7 e inquilino 10.

![Vários inquilinos em DB único][3]

### <a name="supported-types-for-sharding-keys"></a>Tipos suportados para chaves de caco

Suporte de balança elástica os seguintes tipos como teclas de caco:

| .NET | Java |
| --- | --- |
| número inteiro |número inteiro |
| long |long |
| guid |uuid |
| byte[]  |byte[] |
| datetime | carimbo de data/hora |
| timespan | duration|
| datatimeoffoff |offsetdatetime |

### <a name="list-and-range-shard-maps"></a>Mapas de fragmentos de lista e alcance

Os mapas de fragmentos podem ser construídos utilizando **listas de valores-chave de fragmentos individuais,** ou podem ser construídos utilizando **gamas de valores-chave de fragmentos**.

### <a name="list-shard-maps"></a>Mapas de fragmentos de lista

**Os fragmentos** contêm **fragmentos** e o mapeamento de shardlets para fragmentos é mantido por um mapa de fragmentos. Um **mapa de fragmentos de lista** é uma associação entre os valores-chave individuais que identificam os fragmentos e as bases de dados que servem de fragmentos.  **Os mapeamentos das listas** são explícitos e diferentes valores-chave podem ser mapeados para a mesma base de dados. Por exemplo, o valor chave 1 mapas para a Base de Dados A, e os valores-chave 3 e 6 ambos os mapas para a Base de Dados B.

| Chave | Localização de fragmento |
| --- | --- |
| 1 |Database_A |
| 3 |Database_B |
| 4 |Database_C |
| 6 |Database_B |
| ... |... |

### <a name="range-shard-maps"></a>Mapas de fragmentos de gama

Num **mapa de fragmentos de gama,** a gama de chaves é descrita por um par **[Low Value, High Value)** onde o *Baixo Valor* é a chave mínima na gama, e o *Valor Alto* é o primeiro valor superior ao intervalo.

Por exemplo, **[0,100)** inclui todos os inteiros maiores ou iguais a 0 e menos de 100. Note que várias gamas podem apontar para a mesma base de dados, e as gamas de desarticulação são suportadas (por exemplo, [100.200) e [400.600) ambos apontam para a Base de Dados C no exemplo seguinte.)

| Chave | Localização de fragmento |
| --- | --- |
| [1,50) |Database_A |
| [50,100) |Database_B |
| [100,200) |Database_C |
| [400,600) |Database_C |
| ... |... |

Cada uma das tabelas acima mostradas é um exemplo conceptual de um objeto **ShardMap.** Cada linha é um exemplo simplificado de um **pointMapping** individual (para o mapa de fragmentos de lista) ou **objeto RangeMapping** (para o mapa de fragmentos de alcance).

## <a name="shard-map-manager"></a>Gestor de mapas de partições horizontais

Na biblioteca dos clientes, o gestor de mapas de fragmentos é uma coleção de mapas de fragmentos. Os dados geridos por um exemplo **de ShardMapManager** são mantidos em três lugares:

1. **Mapa Global do Fragmento (GSM)**: Você especifica uma base de dados para servir de repositório para todos os seus mapas e mapeamentos de fragmentos. As tabelas especiais e os procedimentos armazenados são automaticamente criados para gerir a informação. Trata-se tipicamente de uma pequena base de dados e de acesso leve, e não deve ser utilizada para outras necessidades da aplicação. As mesas estão num esquema especial chamado **__ShardManagement.**
2. **Mapa do Fragmento Local (LSM)**: Todas as bases de dados que especifica para ser um fragmento são modificadas para conter várias pequenas tabelas e procedimentos especiais armazenados que contêm e gerem informações de mapas de fragmentos específicas desse fragmento. Esta informação é redundante com as informações no GSM, e permite que a aplicação valide informações de mapas de fragmentos em cache sem colocar qualquer carga no GSM; a aplicação utiliza o LSM para determinar se um mapeamento em cache ainda é válido. As tabelas correspondentes ao LSM em cada fragmento também estão no esquema **__ShardManagement**.
3. **Cache de aplicação**: Cada instância de aplicação que acede a um objeto **ShardMapManager** mantém uma cache de memória local dos seus mapeamentos. Armazena informação de encaminhamento que foi recentemente recuperada.

## <a name="constructing-a-shardmapmanager"></a>Construção de um ShardMapManager

Um objeto **ShardMapManager** é construído com um padrão de fábrica[(Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory), [.NET).](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory) O método **ShardMapManagerFactory.GetSqlShardMapManager** [(Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)) tem credenciais (incluindo o nome do servidor e nome de base de dados que detém o GSM) sob a forma de um **ConnectionString** e devolve uma instância de um **ShardMapManager**.  

**Nota:** O **ShardMapManager** deve ser instantâneo apenas uma vez por domínio de aplicação, dentro do código de inicialização de uma aplicação. A criação de instâncias adicionais de ShardMapManager no mesmo domínio da aplicação resulta num aumento da memória e utilização do CPU da aplicação. Um **ShardMapManager** pode conter qualquer número de mapas de fragmentos. Embora um único mapa de fragmentos possa ser suficiente para muitas aplicações, há momentos em que diferentes conjuntos de bases de dados são usados para esquemas diferentes ou para fins únicos; nesses casos, vários mapas de fragmentos podem ser preferíveis.

Neste código, uma aplicação tenta abrir um **ShardMapManager** existente com o método TryGetSqlShardMapManager[(Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.trygetsqlshardmapmanager), [.NET.](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) Se os objetos que representam um **ShardMapManager** Global (GSM) ainda não existirem dentro da base de dados, a biblioteca do cliente cria-os utilizando o método CreateSqlShardMapManager[(Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.createsqlshardmapmanager), [.NET).](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager)

```Java
// Try to get a reference to the Shard Map Manager in the shardMapManager database.
// If it doesn't already exist, then create it.
ShardMapManager shardMapManager = null;
boolean shardMapManagerExists = ShardMapManagerFactory.tryGetSqlShardMapManager(shardMapManagerConnectionString,ShardMapManagerLoadPolicy.Lazy, refShardMapManager);
shardMapManager = refShardMapManager.argValue;

if (shardMapManagerExists) {
    ConsoleUtils.writeInfo("Shard Map %s already exists", shardMapManager);
}
else {
    // The Shard Map Manager does not exist, so create it
    shardMapManager = ShardMapManagerFactory.createSqlShardMapManager(shardMapManagerConnectionString);
    ConsoleUtils.writeInfo("Created Shard Map %s", shardMapManager);
}
```

```csharp
// Try to get a reference to the Shard Map Manager via the Shard Map Manager database.  
// If it doesn't already exist, then create it.
ShardMapManager shardMapManager;
bool shardMapManagerExists = ShardMapManagerFactory.TryGetSqlShardMapManager(
                                        connectionString,
                                        ShardMapManagerLoadPolicy.Lazy,
                                        out shardMapManager);

if (shardMapManagerExists)
{
    Console.WriteLine("Shard Map Manager already exists");
}
else
{
    // Create the Shard Map Manager.
    ShardMapManagerFactory.CreateSqlShardMapManager(connectionString);
    Console.WriteLine("Created SqlShardMapManager");

    shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(
            connectionString,
            ShardMapManagerLoadPolicy.Lazy);

// The connectionString contains server name, database name, and admin credentials for privileges on both the GSM and the shards themselves.
}
```

Para a versão .NET, pode utilizar o PowerShell para criar um novo Shard Map Manager. Um exemplo está disponível [aqui.](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db)

## <a name="get-a-rangeshardmap-or-listshardmap"></a>Obtenha um RangeShardMap ou ListShardMap

Depois de criar um gestor de mapas de fragmentos, pode obter o RangeShardMap[(Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap), [.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)ou ListShardMap[(Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)) utilizando o método TryGetRangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetrangeshardmap), [.NET),](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap)o método TryGetListShardMap[(Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetlistshardmap), [.NET),](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap)ou o método GetShardMap[(Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getshardmap), [.NET).](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap)

```Java
// Creates a new Range Shard Map with the specified name, or gets the Range Shard Map if it already exists.
static <T> RangeShardMap<T> createOrGetRangeShardMap(ShardMapManager shardMapManager,
            String shardMapName,
            ShardKeyType keyType) {
    // Try to get a reference to the Shard Map.
    ReferenceObjectHelper<RangeShardMap<T>> refRangeShardMap = new ReferenceObjectHelper<>(null);
    boolean isGetSuccess = shardMapManager.tryGetRangeShardMap(shardMapName, keyType, refRangeShardMap);
    RangeShardMap<T> shardMap = refRangeShardMap.argValue;

    if (isGetSuccess && shardMap != null) {
        ConsoleUtils.writeInfo("Shard Map %1$s already exists", shardMap.getName());
    }
    else {
        // The Shard Map does not exist, so create it
        try {
            shardMap = shardMapManager.createRangeShardMap(shardMapName, keyType);
        }
        catch (Exception e) {
            e.printStackTrace();
        }
        ConsoleUtils.writeInfo("Created Shard Map %1$s", shardMap.getName());
    }

    return shardMap;
}
```

```csharp
// Creates a new Range Shard Map with the specified name, or gets the Range Shard Map if it already exists.
public static RangeShardMap<T> CreateOrGetRangeShardMap<T>(ShardMapManager shardMapManager, string shardMapName)
{
    // Try to get a reference to the Shard Map.
    RangeShardMap<T> shardMap;
    bool shardMapExists = shardMapManager.TryGetRangeShardMap(shardMapName, out shardMap);

    if (shardMapExists)
    {
        ConsoleUtils.WriteInfo("Shard Map {0} already exists", shardMap.Name);
    }
    else
    {
        // The Shard Map does not exist, so create it
        shardMap = shardMapManager.CreateRangeShardMap<T>(shardMapName);
        ConsoleUtils.WriteInfo("Created Shard Map {0}", shardMap.Name);
    }

    return shardMap;
}
```

### <a name="shard-map-administration-credentials"></a>Credenciais de administração de mapas de fragmentos

As aplicações que administram e manipulam mapas de fragmentos são diferentes das que usam os mapas de fragmentos para encaminhar ligações.

Para administrar mapas de fragmentos (adicionar ou alterar fragmentos, mapas de fragmentos, mapeamentos de fragmentos, etc.) deve instantaneamente o **ShardMapManager** usando **credenciais que tenham lido/escrever privilégios na base de dados GSM e em cada base de dados que sirva como fragmento.** As credenciais devem permitir a escrita contra as tabelas tanto no GSM como no LSM, uma vez que as informações sobre mapas fragmentos são inseridas ou alteradas, bem como para a criação de tabelas de LSM em novos fragmentos.  

Consulte [as credenciais utilizadas para aceder à biblioteca de clientes elastic Database](elastic-scale-manage-credentials.md).

### <a name="only-metadata-affected"></a>Apenas metadados afetados

Os métodos utilizados para povoar ou alterar os dados **ShardMapManager** não alteram os dados do utilizador armazenados nos próprios fragmentos. Por exemplo, métodos como **CreateShard**, **DeleteShard,** **UpdateMapping,** etc. afetam apenas os metadados do mapa de fragmentos. Não removem, adicionam ou alteram os dados do utilizador contidos nos fragmentos. Em vez disso, estes métodos são concebidos para serem utilizados em conjunto com operações separadas que executa para criar ou remover bases de dados reais, ou que movem filas de um fragmento para outro para reequilibrar um ambiente fragmento.  (A ferramenta **de fusão dividida** incluída com ferramentas de base de dados elásticas utiliza estas APIs juntamente com a orquestração do movimento real de dados entre fragmentos.) Consulte a escala utilizando a ferramenta de fusão dividida da [Base de Dados Elástica](elastic-scale-overview-split-and-merge.md).

## <a name="data-dependent-routing"></a>Encaminhamento dependente de dados

O gestor de mapas de fragmentos é utilizado em aplicações que requerem ligações de base de dados para realizar as operações de dados específicas da aplicação. Estas ligações devem estar associadas à base de dados correta. Isto é conhecido como **Data Dependent Encaminhing**. Para estas aplicações, instantânea um objeto de gestor de mapas de fragmentos da fábrica usando credenciais que têm acesso apenas de leitura na base de dados GSM. Os pedidos individuais para ligações posteriores fornecem credenciais necessárias para a ligação à base de dados de fragmentos apropriada.

Note que estas aplicações (utilizando **ShardMapManager** abertas com credenciais apenas de leitura) não podem fazer alterações nos mapas ou mapeamentos. Para essas necessidades, crie aplicações administrativas específicas ou scripts PowerShell que forneçam credenciais privilegiadas mais elevadas, como discutido anteriormente. Consulte [as credenciais utilizadas para aceder à biblioteca de clientes elastic Database](elastic-scale-manage-credentials.md).

Para obter mais informações, consulte [o encaminhamento dependente de Dados](elastic-scale-data-dependent-routing.md).

## <a name="modifying-a-shard-map"></a>Modificando um mapa de fragmentos

Um mapa de fragmentos pode ser alterado de diferentes maneiras. Todos os seguintes métodos modificam os metadados que descrevem os fragmentos e os seus mapeamentos, mas não modificam fisicamente os dados dentro dos fragmentos, nem criam ou apagam as bases de dados reais.  Algumas das operações no mapa de fragmentos descritas abaixo podem ter de ser coordenadas com ações administrativas que movem fisicamente dados ou que adicionam e removem bases de dados que servem de fragmentos.

Estes métodos funcionam em conjunto como os blocos de construção disponíveis para modificar a distribuição global de dados no seu ambiente de base de dados.  

* Para adicionar ou remover fragmentos: utilize **CreateShard** [(Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.createshard), [.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard)e **DeleteShard** [(Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.deleteshard), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.deleteshard)) da classe shardmap[(Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap), [.NET).](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap)
  
    O servidor e base de dados que representa o fragmento alvo já devem existir para que estas operações executem. Estes métodos não têm qualquer impacto nas próprias bases de dados, apenas nos metadados no mapa de fragmentos.
* Para criar ou remover pontos ou intervalos mapeados para os fragmentos: utilize **o CreateRangeMapping** [(Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.createrangemapping), [.NET](https://docs.microsoft.com/previous-versions/azure/dn841993(v=azure.100))), **DeleteMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.deletemapping), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) da classe RangeShardMapping[(Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) e **CreatePointMapping** [(Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap.createpointmapping), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)) da classe ListShardMap[(Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap), [.NET).](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)
  
    Muitos pontos ou intervalos diferentes podem ser mapeados para o mesmo fragmento. Estes métodos apenas afetam metadados - não afetam quaisquer dados que possam já estar presentes em fragmentos. Se os dados precisarem de ser removidos da base de dados para serem consistentes com as operações **de DeleteMapping,** executa essas operações separadamente, mas em conjunto com a utilização destes métodos.  
* Para dividir as gamas existentes em duas, ou fundir as gamas adjacentes numa: utilize **splitMapping** [(Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.splitmapping), [.NET)](https://msdn.microsoft.com/library/azure/dn824205.aspx)e **MergeMappings** [(Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.mergemappings), [.NET](https://msdn.microsoft.com/library/azure/dn824201.aspx)).  
  
    Note que as operações de divisão e fusão **não alteram o fragmento para o qual os valores-chave estão mapeados**. Uma divisão quebra um alcance existente em duas partes, mas deixa ambas como mapeadas para o mesmo fragmento. Uma fusão opera em duas gamas adjacentes que já estão mapeadas para o mesmo fragmento, unindo-as numa única gama.  O movimento de pontos ou intervalos entre fragmentos tem de ser coordenado utilizando **o UpdateMapping** em conjunto com o movimento real de dados.  Pode utilizar o serviço **Split/Merge** que faz parte de ferramentas de base de dados elásticas para coordenar alterações de mapas de fragmentos com o movimento de dados, quando o movimento é necessário.
* Para re mapear (ou mover) pontos ou intervalos individuais para diferentes fragmentos: utilize **UpdateMapping** [(Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.updatemapping), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)).  
  
    Uma vez que os dados podem ter de ser transferidos de um fragmento para outro para ser consistente com as operações **de UpdateMapping,** é necessário executar esse movimento separadamente, mas em conjunto com a utilização destes métodos.

* Para fazer mapeamentos on-line e offline: utilize **o MarkMappingOffline** [(Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.markmappingoffline), [.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)e **o MarkMappingOnline** [(Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.markmappingonline), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) para controlar o estado online de um mapeamento.
  
    Certas operações em mapeamentos de fragmentos só são permitidas quando um mapeamento está em estado "offline", incluindo **UpdateMapping** e **DeleteMapping**. Quando um mapeamento está offline, um pedido dependente de dados baseado numa chave incluída nesse mapeamento retorna um erro. Além disso, quando um intervalo é inicialmente desligado, todas as ligações com o fragmento afetado são automaticamente eliminadas de modo a evitar resultados inconsistentes ou incompletos para consultas dirigidas contra as gamas que estão a ser alteradas.

Os mapeamentos são objetos imutáveis em .NET.  Todos os métodos acima que alteram os mapeamentos também invalidam quaisquer referências a eles no seu código. Para facilitar a realização de sequências de operações que alteram o estado de um mapeamento, todos os métodos que alteram um mapeamento devolvem uma nova referência de mapeamento, para que as operações possam ser acorrentadas. Por exemplo, para eliminar um mapeamento existente no sm de mapas de shardmap que contém a chave 25, pode executar o seguinte:

```
    sm.DeleteMapping(sm.MarkMappingOffline(sm.GetMappingForKey(25)));
```

## <a name="adding-a-shard"></a>Adicionando um fragmento

As aplicações muitas vezes precisam adicionar novos fragmentos para lidar com dados que são esperados a partir de novas teclas ou gamas de chaves, para um mapa de fragmentos que já existe. Por exemplo, um pedido de identificação do inquilino pode precisar de providenciar um novo fragmento para um novo inquilino, ou os dados mensais são necessários para precisar de um novo fragmento previsto antes do início de cada novo mês.

Se a nova gama de valores-chave já não faz parte de um mapeamento existente e não é necessário nenhum movimento de dados, é simples adicionar o novo fragmento e associar a nova chave ou gama a esse fragmento. Para obter detalhes sobre a adição de novos fragmentos, consulte [adicionar um novo fragmento](elastic-scale-add-a-shard.md).

No entanto, para cenários que requerem movimento de dados, a ferramenta de fusão dividida é necessária para orquestrar o movimento de dados entre fragmentos em combinação com as atualizações necessárias do mapa de fragmentos. Para obter detalhes sobre a utilização da ferramenta de fusão dividida, consulte [a visão geral da fusão dividida](elastic-scale-overview-split-and-merge.md)

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/elastic-scale-shard-map-management/listmapping.png
[2]: ./media/elastic-scale-shard-map-management/rangemapping.png
[3]: ./media/elastic-scale-shard-map-management/multipleonsingledb.png
