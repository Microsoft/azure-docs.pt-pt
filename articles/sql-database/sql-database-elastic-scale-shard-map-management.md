---
title: Aumentar horizontalmente uma base de dados SQL do Azure | Documentos da Microsoft
description: Como utilizar o ShardMapManager, biblioteca de clientes de bases de dados elásticas
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: a9c857ab9e9a3cfc0d1314600b612c4e6293173d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60332318"
---
# <a name="scale-out-databases-with-the-shard-map-manager"></a>Aumentar horizontalmente de bases de dados com o Gestor de mapas de partições horizontais

Para aumentar horizontalmente de bases de dados no SQL Azure, utilize um Gestor de mapas de partições horizontais. O Gestor de mapas de partições horizontais é uma base de dados especial que mantém informações de mapeamento global sobre todas as partições horizontais (bases de dados) num conjunto de partições horizontais. Os metadados permite que um aplicativo ligar à base de dados correta com base no valor do **chave de fragmentação**. Além disso, cada partição horizontal no conjunto contém mapas que monitorizam os dados de partição horizontal local (conhecido como **shardlets**).

![Gestão de mapas de partições horizontais](./media/sql-database-elastic-scale-shard-map-management/glossary.png)

Compreender como esses mapas são construídos é essencial para a gestão de mapas de partições horizontais. Isso é feito usando a classe ShardMapManager ([Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager), foi encontrado no [biblioteca de clientes de bases de dados elásticas](sql-database-elastic-database-client-library.md) para gerir os mapas de partições horizontais.  

## <a name="shard-maps-and-shard-mappings"></a>Mapas de partições horizontais e os mapeamentos de partição horizontal

Para cada partição horizontal, tem de selecionar o tipo de mapa de partições horizontais para criar. A escolha depende da arquitetura de base de dados:

1. Inquilino individual por base de dados  
2. Vários inquilinos por base de dados (dois tipos):
   1. Mapeamento de lista
   2. Mapeamento de intervalo

Para um modelo do inquilino único, crie uma **mapeamento de lista** mapa de partições horizontais. O modelo do inquilino único atribui uma base de dados por inquilino. Este é um modelo eficaz para os programadores de SaaS, como ele simplifica a gestão.

![Mapeamento de lista][1]

O modelo do multi-inquilino atribui vários inquilinos para uma base de dados individual (e pode distribuir os grupos de inquilinos em várias bases de dados). Utilize este modelo quando esperar de cada inquilino ter necessidades de dados pequeno. Nesse modelo, atribuir um intervalo de inquilinos para uma base de dados com **mapeamento de intervalo**.

![Mapeamento de intervalo][2]

Ou pode implementar um modelo de base de dados do multi-inquilino com um *mapeamento de lista* atribuir vários inquilinos para uma base de dados individual. Por exemplo, DB1 tem um é utilizado para armazenar informações sobre o inquilino do ID de 1 e 5 e DB2 armazena dados de inquilinos 7 e inquilinos 10.

![Vários inquilinos numa único DB][3]

### <a name="supported-types-for-sharding-keys"></a>Tipos suportados para chaves de fragmentação

Dimensionamento elástico suporta os seguintes tipos como chaves de fragmentação:

| .NET | Java |
| --- | --- |
| inteiro |inteiro |
| long |long |
| GUID |uuid |
| byte[]  |byte[] |
| datetime | timestamp |
| TimeSpan | Duração|
| datetimeoffset |offsetdatetime |

### <a name="list-and-range-shard-maps"></a>Mapas de partições horizontais de lista e o intervalo

Mapas de partições horizontais podem ser construídos usando **valores da chave de listas de fragmentação individual**, ou pode ser construídos, o com **valores da chave de intervalos de fragmentação**.

### <a name="list-shard-maps"></a>Mapas de partições horizontais de lista

**As partições horizontais** contêm **shardlets** e o mapeamento de shardlets a partições horizontais é mantido por um mapa de partições horizontais. R **mapa de partições horizontais de lista** é uma associação entre os valores de chave individuais que identificam os shardlets e as bases de dados que funcionam como partições horizontais.  **Lista de mapeamentos** são fundamentais explícito e diferentes valores podem ser mapeados para a mesma base de dados. Por exemplo, o valor de chave 1 mapeia para A base de dados e valores de chave 3 e 6, ambos os mapas para o b de base de dados.

| Chave | Localização de partição horizontal |
| --- | --- |
| 1 |Database_A |
| 3 |Database_B |
| 4 |Database_C |
| 6 |Database_B |
| ... |... |

### <a name="range-shard-maps"></a>Mapas de partições horizontais de intervalo

Num **mapa de partições horizontais de intervalo**, o intervalo de chaves é descrito por um par **[baixo valor, alto valor)** onde o *valor baixo* é a chave mínimo no intervalo e o *elevada Valor* é o primeiro valor maior do que o intervalo.

Por exemplo, **[0, 100)** inclui todos os números inteiros igual ou maior que 0 e menor que 100. Tenha em atenção que vários intervalos podem apontar para o mesmo banco de dados e intervalos separados são suportados (por exemplo, [100,200) e [400,600), ambos apontam para C de base de dados no exemplo seguinte.)

| Chave | Localização de partição horizontal |
| --- | --- |
| [1,50) |Database_A |
| [50,100) |Database_B |
| [100,200) |Database_C |
| [400,600) |Database_C |
| ... |... |

Cada uma das tabelas mostradas acima é um exemplo de conceitual de um **ShardMap** objeto. Cada linha é um exemplo simplificado de um indivíduo **PointMapping** (para o mapa de partições horizontais de lista) ou **RangeMapping** (para o mapa de partições horizontais de intervalo) objeto.

## <a name="shard-map-manager"></a>Gestor de mapas de partições horizontais

Na biblioteca do cliente, o Gestor de mapas de partições horizontais é uma coleção de mapas de partições horizontais. Os dados geridos por um **ShardMapManager** instância é mantida em três locais:

1. **Mapa de partições horizontais global (GSM)** : Especifique uma base de dados para ser usado como o repositório de todos os seus mapas de partições horizontais e os mapeamentos. Tabelas especiais e procedimentos armazenados são criados automaticamente para gerenciar as informações. Isto é, normalmente, um pequeno banco de dados e pouco acedidos, e não deve ser utilizada para outras necessidades do aplicativo. As tabelas estão num esquema especial chamado **__ShardManagement**.
2. **Mapa de partições horizontais local (LSM)** : Cada base de dados que especificar para ser uma partição horizontal é modificado para conter vários pequenas tabelas e procedimentos armazenados especiais que contenham e gerir informações de mapa de partições horizontais específicas para essa partição horizontal. Esta informação é redundante com as informações do GSM e permite que o aplicativo validar as informações de mapa de partições horizontais em cache sem colocar qualquer carga no GSM; o aplicativo usa o LSM para determinar se um mapeamento em cache ainda é válido. As tabelas correspondente a LSM em cada partição horizontal também estão no esquema **__ShardManagement**.
3. **Cache do aplicativo**: Cada instância de aplicativo Acessando um **ShardMapManager** objeto mantém um cache na memória local de seus mapeamentos. Ele armazena informações de encaminhamento que recentemente foi obtidas.

## <a name="constructing-a-shardmapmanager"></a>Construir uma ShardMapManager

R **ShardMapManager** objeto é construído de acordo com uma fábrica ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory)) padrão. O **ShardMapManagerFactory.GetSqlShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)) método aceita as credenciais (incluindo o nome do servidor e o nome de base de dados que contém o GSM) a forma de um **ConnectionString** e retorna uma instância de um **ShardMapManager**.  

**Atenção:** O **ShardMapManager** deve ser instanciado apenas uma vez por domínio de aplicativo, dentro do código de inicialização para uma aplicação. Criação de instâncias adicionais de ShardMapManager no mesmo domínio de aplicativo resulta em maior de memória e utilização de CPU do aplicativo. R **ShardMapManager** pode conter qualquer número de mapas de partições horizontais. Embora um mapa de partição horizontal única pode ser suficiente para muitos aplicativos, há ocasiões em que quando os diferentes conjuntos de bases de dados são utilizados para esquema diferente ou para fins de exclusivos; Nesses casos, vários mapas de partições horizontais podem ser preferíveis.

Nesse código, um aplicativo tenta abrir um existente **ShardMapManager** com o TryGetSqlShardMapManager ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.trygetsqlshardmapmanager), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) método. Se os objetos que representam um Global **ShardMapManager** (GSM) não o fizer, ainda existe no banco de dados, a biblioteca de cliente cria-los usando o CreateSqlShardMapManager ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.createsqlshardmapmanager), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager)) método.

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

Para a versão do .NET, pode utilizar o PowerShell para criar um novo Gestor de mapas de partições horizontais. Um exemplo está disponível [aqui](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

## <a name="get-a-rangeshardmap-or-listshardmap"></a>Obter um RangeShardMap ou ListShardMap

Depois de criar uma partição horizontal Gestor de mapas, pode obter o RangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) ou ListShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)) com o TryGetRangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetrangeshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap)), o TryGetListShardMap ([Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetlistshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap)), ou o GetShardMap ([ Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap)) método.

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

### <a name="shard-map-administration-credentials"></a>Credenciais de administração do mapa de partições horizontais

Os aplicativos que administram e manipulam os mapas de partições horizontais são diferentes das que usam os mapas de partições horizontais para ligações de rota.

Para administrar os mapas de partições horizontais (adicionar ou alterar as partições horizontais, mapas de partições horizontais, mapeamentos de partição horizontal, etc.) tem de instanciar o **ShardMapManager** usando **leitura/escritam de credenciais com privilégios em ambas as a base de dados GSM e em cada base de dados que funciona como uma partição horizontal**. As credenciais têm de permitir para escritas contra as tabelas no GSM e LSM como informações de mapa de partições horizontais são introduzidas ou alteradas, bem como para a criação de tabelas LSM em novas partições horizontais.  

Ver [credenciais utilizadas para aceder a biblioteca de clientes de bases de dados elásticas](sql-database-elastic-scale-manage-credentials.md).

### <a name="only-metadata-affected"></a>Apenas os metadados afetados

Métodos usados para preencher ou alterar o **ShardMapManager** dados nem alteram os dados de utilizador armazenados nas partições horizontais propriamente ditas. Por exemplo, os métodos como **CreateShard**, **DeleteShard**, **UpdateMapping**, etc. afetam apenas os metadados de mapa de partições horizontais. Eles não remova, adicionar ou alterar os dados de utilizador contidos nas partições horizontais. Em vez disso, esses métodos são concebidos para serem utilizados em conjunto com operações separadas que efetuar para criar ou remover bases de dados real, ou que mover linhas de uma partição para outra reequilibrar um ambiente em partição horizontal.  (A **dividir / unir** ferramenta incluída com ferramentas de bases de dados elásticas faz uso dessas APIs, juntamente com a orquestrar o movimento de dados entre partições horizontais.) Ver [dimensionar com a ferramenta de dividir / unir da base de dados elástica](sql-database-elastic-scale-overview-split-and-merge.md).

## <a name="data-dependent-routing"></a>Encaminhamento dependente de dados

O Gestor de mapas de partições horizontais é usado em aplicativos que necessitam de ligações de base de dados para realizar as operações de dados específicos da aplicação. Essas ligações tem de ser associadas com a base de dados correto. Isso é conhecido como **encaminhamento dependente de dados**. Para estas aplicações, instancio um objeto de Gestor de mapa de partições horizontais da fábrica com as credenciais que têm acesso só de leitura na base de dados GSM. Pedidos individuais para ligações posteriores fornecem credenciais necessárias para ligar à base de dados de partição horizontal adequada.

Tenha em atenção que estas aplicações (usando **ShardMapManager** aberta com as credenciais só de leitura) não pode efetuar alterações ao maps ou mapeamentos. Para essas necessidades, crie aplicativos administrativos específicos ou scripts do PowerShell que fornecem credenciais com mais privilégios, como discutido anteriormente. Ver [credenciais utilizadas para aceder a biblioteca de clientes de bases de dados elásticas](sql-database-elastic-scale-manage-credentials.md).

Para obter mais informações, consulte [encaminhamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md).

## <a name="modifying-a-shard-map"></a>Modificar um mapa de partições horizontais

Um mapa de partições horizontais pode ser alterado de formas diferentes. Todos os métodos seguintes modificar os metadados que descrevem as partições horizontais e os respetivos mapeamentos, mas eles não fisicamente modifique dados em partições horizontais, nem fazerem criar ou eliminar as bases de dados reais.  Algumas das operações no mapa de partições horizontais descrito abaixo talvez precise ser coordenadas com ações administrativas que mover fisicamente os dados ou que adicionar e remover bases de dados que serve como partições horizontais.

Esses métodos funcionam em conjunto como os blocos de construção disponíveis para modificar a distribuição global de dados no seu ambiente de base de dados em partição horizontal.  

* Para adicionar ou remover partições horizontais: utilizar **CreateShard** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.createshard), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard)) e **DeleteShard** ([Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.deleteshard), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.deleteshard)) da shardmap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap)) classe.
  
    O servidor e base de dados que representa a partição horizontal de destino tem de existir para essas operações executar. Esses métodos não tem qualquer impacto nas bases de dados propriamente ditos, apenas em metadados no mapa de partições horizontais.
* Para criar ou remover pontos ou intervalos de que são mapeados para as partições horizontais: utilizar **CreateRangeMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.createrangemapping), [.NET](https://docs.microsoft.com/previous-versions/azure/dn841993(v=azure.100))), **DeleteMapping** ( [Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.deletemapping), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) da RangeShardMapping ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) classe, e **CreatePointMapping**  ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap.createpointmapping), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)) da ListShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)) classe.
  
    Muitos pontos diferentes ou intervalos podem ser mapeados para a mesma partição horizontal. Esses métodos afetam apenas metadados - não afetam todos os dados que podem já estar presentes em partições horizontais. Se precisam de dados a serem removidos da base de dados para que seja consistente com **DeleteMapping** operações, executar essas operações em separado, mas em conjunto com o uso desses métodos.  
* Para dividir intervalos existentes em duas ou unir intervalos adjacentes a um: utilizar **SplitMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.splitmapping), [.NET](https://msdn.microsoft.com/library/azure/dn824205.aspx)) e **MergeMappings** ( [Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.mergemappings), [.NET](https://msdn.microsoft.com/library/azure/dn824201.aspx)).  
  
    Tenha em atenção que divide e intercalar operations **não altere a partição horizontal para o qual os valores de chave são mapeados**. Uma divisão divide um intervalo existente em duas partes, mas deixa ambos como mapeado para a mesma partição horizontal. Uma mesclagem opera em dois intervalos de adjacentes que já são mapeados para a mesma partição horizontal, União-los num único intervalo.  Tem de ser coordenados, utilizando o movimento dos pontos ou intervalos de próprios entre partições horizontais **UpdateMapping** em conjunto com o movimento de dados.  Pode utilizar o **dividir/unir** service, ou seja, parte das ferramentas de bases de dados elásticas para coordenar as alterações de mapa de partições horizontais com o movimento de dados, quando é necessário o movimento.
* Para mapear novamente (ou mover) pontos individuais ou intervalos de partições horizontais diferentes: utilizar **UpdateMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.updatemapping), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)).  
  
    Uma vez que os dados poderão ter de ser movidas de uma partição para outra para que seja consistente com **UpdateMapping** operações, tiver de realizar esse movimento separadamente, mas em conjunto com o uso desses métodos.

* Para tirar mapeamentos online e offline: utilizar **MarkMappingOffline** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.markmappingoffline), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) e **MarkMappingOnline** ([ Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.markmappingonline), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) para controlar o estado online de um mapeamento.
  
    Determinadas operações nos mapeamentos de partição horizontal só são permitidas quando um mapeamento está no estado "offline", incluindo **UpdateMapping** e **DeleteMapping**. Quando um mapeamento estiver offline, um pedido de dependente de dados com base numa chave incluída nesse mapeamento devolve um erro. Além disso, quando um intervalo primeiro fica offline, todas as ligações para a partição horizontal afetada são eliminadas automaticamente para evitar resultados inconsistentes ou incompletos para consultas direcionadas intervalos a ser alterados.

Mapeamentos são objetos imutáveis no .net.  Todos os métodos acima alterar mapeamentos também invalidar todas as referências aos mesmos no seu código. Para tornar mais fácil fazer seqüências de operações que alteram o estado de um mapeamento, todos os métodos que alterar um mapeamento a devolver uma nova referência de mapeamento, para que as operações podem ser em cadeia. Por exemplo, para eliminar um mapeamento do sm shardmap que contém a chave de 25 existente, pode executar o seguinte:

```
    sm.DeleteMapping(sm.MarkMappingOffline(sm.GetMappingForKey(25)));
```

## <a name="adding-a-shard"></a>Adicionar uma partição horizontal

Aplicativos muitas vezes, precisam de adicionar novas partições horizontais para lidar com dados que são esperados de chaves nova ou intervalos de chaves, para um mapa de partições horizontais que já existe. Por exemplo, uma aplicação em partição horizontal por ID de inquilino pode ter de aprovisionar uma nova partição horizontal para um novo inquilino ou mensal de dados em partição horizontal poderá ter uma nova partição horizontal aprovisionada antes do início de cada mês de novo.

Se o novo intervalo de valores de chave já não faz parte de um mapeamento existente e, sem movimento de dados é necessário, é simples adicionar a nova partição horizontal e associar a nova chave ou um intervalo para essa partição horizontal. Para obter detalhes sobre como adicionar novas partições horizontais, veja [adicionando uma nova partição horizontal](sql-database-elastic-scale-add-a-shard.md).

Para cenários que exigem o movimento de dados, no entanto, a ferramenta de dividir / unir é necessária para orquestrar o movimento de dados entre partições horizontais em combinação com as atualizações de mapa de partições horizontais necessário. Para obter detalhes sobre como utilizar a ferramenta de divisão / intercalação, veja [descrição geral de dividir / unir](sql-database-elastic-scale-overview-split-and-merge.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-shard-map-management/listmapping.png
[2]: ./media/sql-database-elastic-scale-shard-map-management/rangemapping.png
[3]: ./media/sql-database-elastic-scale-shard-map-management/multipleonsingledb.png
