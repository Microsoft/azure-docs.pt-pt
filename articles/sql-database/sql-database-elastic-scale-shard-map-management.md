---
title: Escalar horizontalmente um banco de dados
description: Como usar o ShardMapManager, a biblioteca de cliente do banco de dados elástico
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 8175563d8c1c2ec59b4195b2ede06f6e1dbf8556
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823566"
---
# <a name="scale-out-databases-with-the-shard-map-manager"></a>Expandir bancos de dados com o Gerenciador de mapa de fragmentos

Para dimensionar facilmente os bancos de dados no SQL Azure, use um Gerenciador de mapa de fragmentos. O Gerenciador de mapa de fragmentos é um banco de dados especial que mantém informações de mapeamento global sobre todos os fragmentos (bancos de dados) em um conjunto de fragmentos. Os metadados permitem que um aplicativo se conecte ao banco de dados correto com base no valor da **chave de fragmentação**. Além disso, cada fragmento no conjunto contém mapas que acompanham os dados do fragmento local (conhecidos como **shardlets**).

![Gerenciamento de mapa de fragmentos](./media/sql-database-elastic-scale-shard-map-management/glossary.png)

Entender como esses mapas são construídos é essencial para o gerenciamento de mapa de fragmentos. Isso é feito usando a classe ShardMapManager ([Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager), encontrada na [biblioteca de cliente do banco de dados elástico](sql-database-elastic-database-client-library.md) para gerenciar mapas de fragmentos.  

## <a name="shard-maps-and-shard-mappings"></a>Mapas de fragmentos e mapeamentos de fragmentos

Para cada fragmento, você deve selecionar o tipo de mapa de fragmentos a ser criado. A escolha depende da arquitetura do banco de dados:

1. Locatário único por banco de dados  
2. Vários locatários por banco de dados (dois tipos):
   1. Mapeamento de lista
   2. Mapeamento de intervalo

Para um modelo de locatário único, crie um mapa de fragmento de **mapeamento de lista** . O modelo de locatário único atribui um banco de dados por locatário. Esse é um modelo eficaz para desenvolvedores de SaaS à medida que simplifica o gerenciamento.

![Mapeamento de lista][1]

O modelo multilocatário atribui vários locatários a um banco de dados individual (e você pode distribuir grupos de locatários em vários bancos de dados). Use esse modelo quando você espera que cada locatário tenha pequenas necessidades de dados. Nesse modelo, atribua um intervalo de locatários a um banco de dados usando o **mapeamento de intervalo**.

![Mapeamento de intervalo][2]

Ou você pode implementar um modelo de banco de dados multilocatário usando um *mapeamento de lista* para atribuir vários locatários a um banco de dados individual. Por exemplo, DB1 é usado para armazenar informações sobre a ID de locatário 1 e 5, e o DB2 armazena dados para o locatário 7 e o locatário 10.

![Vários locatários em um único banco de BD][3]

### <a name="supported-types-for-sharding-keys"></a>Tipos com suporte para chaves de fragmentação

A escala elástica dá suporte aos seguintes tipos como chaves de fragmentação:

| .NET | Java |
| --- | --- |
| número inteiro |número inteiro |
| Longas |Longas |
| guid |uuid |
| Byte []  |Byte [] |
| datetime | carimbo de data/hora |
| período | Permanência|
| DateTimeOffset |offsetdatetime |

### <a name="list-and-range-shard-maps"></a>Mapas de fragmentos de lista e intervalo

Mapas de fragmento podem ser construídos usando **listas de valores de chave de fragmentação individuais**ou podem ser construídos usando **intervalos de valores de chave de fragmentação**.

### <a name="list-shard-maps"></a>Listar mapas de fragmentos

Os **fragmentos** contêm **shardlets** e o mapeamento de shardlets para fragmentos é mantido por um mapa de fragmentos. Um **mapa de fragmentos de lista** é uma associação entre os valores de chave individuais que identificam o shardlets e os bancos de dados que servem como fragmentos.  Os **mapeamentos de lista** são explícitos e valores de chave diferentes podem ser mapeados para o mesmo banco de dados. Por exemplo, o valor de chave 1 é mapeado para o banco de dados A e os valores de chave 3 e 6 são mapeados para o banco de dados B.

| Chave | Localização do fragmento |
| --- | --- |
| 1 |Database_A |
| 3 |Database_B |
| 4 |Database_C |
| 6 |Database_B |
| ... |... |

### <a name="range-shard-maps"></a>Mapas de fragmentos de intervalo

Em um **mapa de fragmentos de intervalo**, o intervalo de chaves é descrito por um par **[valor baixo, valor alto)** em que o *valor baixo* é a chave mínima no intervalo e o *valor alto* é o primeiro valor maior que o intervalo.

Por exemplo, **[0, 100)** inclui todos os inteiros maiores ou iguais a 0 e menores que 100. Observe que vários intervalos podem apontar para o mesmo banco de dados, e os intervalos não contíguos têm suporte (por exemplo, [100.200) e [400.600) aponte para o banco de dados C no exemplo a seguir.)

| Chave | Localização do fragmento |
| --- | --- |
| [1, 50) |Database_A |
| [50.100) |Database_B |
| [100.200) |Database_C |
| [400.600) |Database_C |
| ... |... |

Cada uma das tabelas mostradas acima é um exemplo conceitual de um objeto **ShardMap** . Cada linha é um exemplo simplificado de um **PointMapping** individual (para o mapa de fragmentos de lista) ou o objeto **RangeMapping** (para o mapa de fragmentos de intervalo).

## <a name="shard-map-manager"></a>Gerenciador de mapa de fragmentos

Na biblioteca de cliente, o Gerenciador de mapa de fragmentos é uma coleção de mapas de fragmentos. Os dados gerenciados por uma instância de **ShardMapManager** são mantidos em três locais:

1. **GSM (mapa de fragmentos global)** : você especifica um banco de dados para servir como o repositório de todos os mapas e mapeamentos de fragmentos. As tabelas especiais e os procedimentos armazenados são criados automaticamente para gerenciar as informações. Normalmente, isso é um pequeno banco de dados e levemente acessado, e não deve ser usado para outras necessidades do aplicativo. As tabelas estão em um esquema especial chamado **__ShardManagement**.
2. **LSM (mapa de fragmentos local)** : cada banco de dados que você especifica para ser um fragmento é modificado para conter várias tabelas pequenas e procedimentos armazenados especiais que contêm e gerenciam informações de mapa de fragmentos específicas para esse fragmento. Essas informações são redundantes com as informações no GSM e permitem que o aplicativo valide informações de mapa de fragmentos em cache sem fazer nenhuma carga no GSM; o aplicativo usa o LSM para determinar se um mapeamento em cache ainda é válido. As tabelas correspondentes ao LSM em cada fragmento também estão no **__ShardManagement**de esquema.
3. **Cache de aplicativo**: cada instância do aplicativo que acessa um objeto **ShardMapManager** mantém um cache na memória local de seus mapeamentos. Ele armazena informações de roteamento que foram recuperadas recentemente.

## <a name="constructing-a-shardmapmanager"></a>Construindo um ShardMapManager

Um objeto **ShardMapManager** é construído usando um padrão de fábrica ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory)). O método **ShardMapManagerFactory. GetSqlShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)) usa as credenciais (incluindo o nome do servidor e o nome do banco de dados que contém o GSM) na forma de um **ConnectionString** e retorna uma instância de um  **ShardMapManager**.  

**Observação:** O **ShardMapManager** deve ser instanciado apenas uma vez por domínio de aplicativo, dentro do código de inicialização de um aplicativo. A criação de instâncias adicionais do ShardMapManager no mesmo domínio de aplicativo resulta em maior utilização de memória e CPU do aplicativo. Um **ShardMapManager** pode conter qualquer número de mapas de fragmentos. Embora um único mapa de fragmentos possa ser suficiente para muitos aplicativos, há ocasiões em que diferentes conjuntos de bancos de dados são usados para um esquema diferente ou para fins exclusivos; nesses casos, vários mapas de fragmentos podem ser preferíveis.

Nesse código, um aplicativo tenta abrir um **ShardMapManager** existente com o método TryGetSqlShardMapManager ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.trygetsqlshardmapmanager), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) . Se os objetos que representam um **ShardMapManager** global (GSM) ainda não existirem no banco de dados, a biblioteca de cliente os criará usando o método CreateSqlShardMapManager ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.createsqlshardmapmanager), [.net](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager)).

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

Para a versão do .NET, você pode usar o PowerShell para criar um novo Gerenciador de mapa de fragmentos. Um exemplo está disponível [aqui](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

## <a name="get-a-rangeshardmap-or-listshardmap"></a>Obter um RangeShardMap ou ListShardMap

Depois de criar um Gerenciador de mapa de fragmentos, você pode obter o RangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) ou ListShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)) usando o método trygetrangeshardmap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetrangeshardmap), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap)), o TryGetListShardMap ([Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetlistshardmap), [. NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap)) ou o método GetShardMap ([Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getshardmap), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap)).

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

### <a name="shard-map-administration-credentials"></a>Credenciais de administração do mapa de fragmentos

Os aplicativos que administram e manipulam mapas de fragmentos são diferentes daqueles que usam o fragmento mapeia para rotear conexões.

Para administrar mapas de fragmentos (adicionar ou alterar fragmentos, mapas de fragmentos, mapeamentos de fragmentos, etc.), você deve criar uma instância do **ShardMapManager** usando **as credenciais que têm privilégios de leitura/gravação no banco de dados GSM e em cada banco de dados que serve como um fragmento**. As credenciais devem permitir gravações nas tabelas no GSM e no LSM, pois as informações do mapa de fragmentos são inseridas ou alteradas, bem como para a criação de tabelas do LSM em novos fragmentos.  

Consulte [as credenciais usadas para acessar a biblioteca de cliente do banco de dados elástico](sql-database-elastic-scale-manage-credentials.md).

### <a name="only-metadata-affected"></a>Somente metadados afetados

Os métodos usados para popular ou alterar os dados do **ShardMapManager** não alteram os dados do usuário armazenados nos próprios fragmentos. Por exemplo, **métodos como createfragment,** **DeleteShard**, **UpdateMapping**, etc. afetam apenas os metadados do mapa do fragmento. Eles não removem, adicionam ou alteram os dados do usuário contidos nos fragmentos. Em vez disso, esses métodos são projetados para serem usados em conjunto com operações separadas que você executa para criar ou Remover bancos de dados reais ou que movem linhas de um fragmento para outro para reequilibrar um ambiente fragmentado.  (A ferramenta de **divisão/mesclagem** incluída com ferramentas de banco de dados elástico usa essas APIs juntamente com a movimentação de movimentações reais entre os fragmentos.) Consulte [dimensionamento usando a ferramenta de divisão/mesclagem do banco de dados elástico](sql-database-elastic-scale-overview-split-and-merge.md).

## <a name="data-dependent-routing"></a>Encaminhamento dependente de dados

O Gerenciador de mapa de fragmentos é usado em aplicativos que exigem conexões de banco de dados para executar as operações específicas do aplicativo. Essas conexões devem ser associadas ao banco de dados correto. Isso é conhecido como **Roteamento Dependente de dados**. Para esses aplicativos, crie uma instância de um objeto do Gerenciador de mapa de fragmentos da fábrica usando as credenciais que têm acesso somente leitura no banco de dados GSM. Solicitações individuais para conexões posteriores fornecem credenciais necessárias para se conectar ao banco de dados de fragmento apropriado.

Observe que esses aplicativos (usando **ShardMapManager** abertos com credenciais somente leitura) não podem fazer alterações nos mapas ou mapeamentos. Para essas necessidades, crie aplicativos administrativos específicos ou scripts do PowerShell que forneçam credenciais com maior privilégio, conforme discutido anteriormente. Consulte [as credenciais usadas para acessar a biblioteca de cliente do banco de dados elástico](sql-database-elastic-scale-manage-credentials.md).

Para obter mais informações, consulte [Roteamento Dependente de dados](sql-database-elastic-scale-data-dependent-routing.md).

## <a name="modifying-a-shard-map"></a>Modificando um mapa de fragmentos

Um mapa de fragmentos pode ser alterado de maneiras diferentes. Todos os métodos a seguir modificam os metadados que descrevem os fragmentos e seus mapeamentos, mas não modificam fisicamente os dados dentro dos fragmentos, nem eles criam ou excluem os bancos reais.  Algumas das operações no mapa de fragmentos descritas abaixo podem precisar ser coordenadas por ações administrativas que movem fisicamente os dados ou que adicionam e removem bancos que servem como fragmentos.

Esses métodos funcionam juntos como os blocos de construção disponíveis para modificar a distribuição geral de dados em seu ambiente de banco de dados fragmentado.  

* Para adicionar ou remover fragmentos **: Use** createfragment ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.createshard), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard)) e **DeleteShard** ([Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.deleteshard), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.deleteshard)) da classe shardmap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap)).
  
    O servidor e o banco de dados que representam o fragmento de destino já devem existir para que essas operações sejam executadas. Esses métodos não têm nenhum impacto sobre os bancos de dados propriamente ditos, somente em metadados no mapa de fragmentos.
* Para criar ou remover pontos ou intervalos mapeados para os fragmentos: use **CreateRangeMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.createrangemapping), [.net](https://docs.microsoft.com/previous-versions/azure/dn841993(v=azure.100))), **DeleteMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.deletemapping), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) da classe classe rangeshardmapping ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)), e **CreatePointMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap.createpointmapping), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)) da classe ListShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)).
  
    Vários pontos ou intervalos diferentes podem ser mapeados para o mesmo fragmento. Esses métodos afetam apenas os metadados-eles não afetam os dados que já podem estar presentes em fragmentos. Se os dados precisarem ser removidos do Database para serem consistentes com as operações **DeleteMapping** , você executará essas operações separadamente, mas em conjunto com esses métodos.  
* Para dividir intervalos existentes em dois ou mesclar intervalos adjacentes em um: use **SplitMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.splitmapping), [.net](https://msdn.microsoft.com/library/azure/dn824205.aspx)) e **MergeMappings** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.mergemappings), [.net](https://msdn.microsoft.com/library/azure/dn824201.aspx)).  
  
    Observe que as operações de divisão e mesclagem **não alteram o fragmento para o qual os valores de chave são mapeados**. Uma divisão divide um intervalo existente em duas partes, mas deixa ambas como mapeadas para o mesmo fragmento. Uma mesclagem opera em dois intervalos adjacentes que já estão mapeados para o mesmo fragmento, unindo-os em um único intervalo.  A movimentação de pontos ou intervalos entre fragmentos precisa ser coordenada usando **UpdateMapping** em conjunto com a movimentação de dados real.  Você pode usar o serviço de **divisão/mesclagem** que faz parte das ferramentas de banco de dados elástico para coordenar alterações de mapa de fragmentos com a movimentação de dados, quando a movimentação é necessária.
* Para remapear (ou mover) pontos individuais ou intervalos para fragmentos diferentes: use **UpdateMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.updatemapping), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)).  
  
    Como os dados podem precisar ser movidos de um fragmento para outro a fim de serem consistentes com as operações de **UpdateMapping** , você precisará executar esse movimento separadamente, mas em conjunto com o uso desses métodos.

* Para fazer mapeamentos online e offline: use **MarkMappingOffline** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.markmappingoffline), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) e **MarkMappingOnline** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.markmappingonline), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) para controlar o estado online de um mapeamento.
  
    Determinadas operações em mapeamentos de fragmentos só são permitidas quando um mapeamento está em um estado "offline", incluindo **UpdateMapping** e **DeleteMapping**. Quando um mapeamento está offline, uma solicitação dependente de dados com base em uma chave incluída nesse mapeamento retorna um erro. Além disso, quando um intervalo é colocado offline pela primeira vez, todas as conexões com o fragmento afetado são automaticamente eliminadas para evitar resultados inconsistentes ou incompletos para consultas direcionadas a intervalos que estão sendo alterados.

Os mapeamentos são objetos imutáveis no .net.  Todos os métodos acima que alteram mapeamentos também invalidam quaisquer referências a eles em seu código. Para facilitar a execução de sequências de operações que alteram o estado de um mapeamento, todos os métodos que alteram um mapeamento retornam uma nova referência de mapeamento, de modo que as operações podem ser encadeadas. Por exemplo, para excluir um mapeamento existente no shardmap SM que contém a chave 25, você pode executar o seguinte:

```
    sm.DeleteMapping(sm.MarkMappingOffline(sm.GetMappingForKey(25)));
```

## <a name="adding-a-shard"></a>Adicionando um fragmento

Geralmente, os aplicativos precisam adicionar novos fragmentos para manipular dados que são esperados de novas chaves ou intervalos de chaves, para um mapa de fragmentos que já existe. Por exemplo, um aplicativo fragmentado por ID de locatário pode precisar provisionar um novo fragmento para um novo locatário, ou os dados fragmentados mensalmente podem precisar de um novo fragmento provisionado antes do início de cada novo mês.

Se o novo intervalo de valores de chave ainda não fizer parte de um mapeamento existente e não for necessária nenhuma movimentação de dados, será simples adicionar o novo fragmento e associar a nova chave ou o intervalo a esse fragmento. Para obter detalhes sobre como adicionar novos fragmentos, consulte [adicionando um novo fragmento](sql-database-elastic-scale-add-a-shard.md).

No entanto, para cenários que exigem movimentação de dados, a ferramenta de divisão/mesclagem é necessária para orquestrar a movimentação de dados entre os fragmentos em combinação com as atualizações de mapa de fragmentos necessárias. Para obter detalhes sobre como usar a ferramenta de mesclagem/divisão, consulte [visão geral da divisão/mesclagem](sql-database-elastic-scale-overview-split-and-merge.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-shard-map-management/listmapping.png
[2]: ./media/sql-database-elastic-scale-shard-map-management/rangemapping.png
[3]: ./media/sql-database-elastic-scale-shard-map-management/multipleonsingledb.png
