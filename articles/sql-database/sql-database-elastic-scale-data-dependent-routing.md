---
title: Encaminhamento dependente de dados com o SQL Database do Azure | Documentos da Microsoft
description: Como utilizar a classe ShardMapManager nas aplicações de .NET para dependente de dados, encaminhamento, uma funcionalidade de bases de dados em partição horizontal na base de dados do Azure SQL
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
ms.openlocfilehash: fe9098592fcfde2d5e23b78a3e33f2b4ebb9e2dc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60584969"
---
# <a name="use-data-dependent-routing-to-route-a-query-to-appropriate-database"></a>Utilizar o encaminhamento para encaminhar uma consulta para o banco de dados apropriado de dependente de dados

**Encaminhamento dependente de dados** é a capacidade de utilizar os dados numa consulta para encaminhar a solicitação para um banco de dados apropriado. Encaminhamento dependente de dados é um padrão fundamental ao trabalhar com bancos de dados em partição horizontal. O contexto de solicitação também pode ser utilizado para encaminhar a solicitação, especialmente se a chave de fragmentação não é parte da consulta. Cada consulta específica ou a transação num aplicativo com o encaminhamento dependente de dados é restrita a acessar um banco de dados por pedido. Para as ferramentas do Azure SQL da base de dados elásticas, esse roteamento é realizado com o **ShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)) classe.

O aplicativo não precisa de controlar várias cadeias de ligação ou locais de DB associados diferentes setores de dados no ambiente em partição horizontal. Em vez disso, o [Gestor de mapas de partições horizontais](sql-database-elastic-scale-shard-map-management.md) abre-se ligações às bases de dados corretos quando necessário, com base nos dados no mapa de partições horizontais e o valor da chave de fragmentação que é o destino do pedido da aplicação. A chave é, normalmente, o *customer_id*, *tenant_id*, *date_key*, ou um outro identificador específico, que é um parâmetro fundamental do pedido da base de dados.

Para obter mais informações, consulte [dimensionar o SQL Server com o encaminhamento dependente de dados](https://technet.microsoft.com/library/cc966448.aspx).

## <a name="download-the-client-library"></a>Transferir a biblioteca de cliente

Para transferir:

* A versão de Java da biblioteca, consulte [repositório Central Maven](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools).
* A versão do .NET da biblioteca, consulte [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## <a name="using-a-shardmapmanager-in-a-data-dependent-routing-application"></a>Utilizar um ShardMapManager numa aplicação de encaminhamento dependente de dados

Aplicativos devem instanciar o **ShardMapManager** durante a inicialização, através da chamada de fábrica **GetSQLShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.NET ](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)). Neste exemplo, tanto um **ShardMapManager** e um específico **ShardMap** nele contidos são inicializados. Este exemplo mostra o GetSqlShardMapManager e GetRangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getrangeshardmap), [.NET](https://docs.microsoft.com/previous-versions/azure/dn824173(v=azure.100))) métodos.

```Java
ShardMapManager smm = ShardMapManagerFactory.getSqlShardMapManager(connectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> rangeShardMap = smm.getRangeShardMap(Configuration.getRangeShardMapName(), ShardKeyType.Int32);
```

```csharp
ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> customerShardMap = smm.GetRangeShardMap<int>("customerMap"); 
```

### <a name="use-lowest-privilege-credentials-possible-for-getting-the-shard-map"></a>Utilizar credenciais de privilégio mais baixas possíveis para obter o mapa de partições horizontais

Se um aplicativo não está manipulando o mapa de partições horizontais em si, as credenciais utilizadas no método de fábrica devem ter permissões só de leitura **mapa de partições horizontais Global** base de dados. Estas credenciais são normalmente diferentes das credenciais utilizadas para abrir ligações para o Gestor de mapas de partições horizontais. Consulte também [credenciais utilizadas para aceder a biblioteca de clientes de bases de dados elásticas](sql-database-elastic-scale-manage-credentials.md).

## <a name="call-the-openconnectionforkey-method"></a>Chamar o método OpenConnectionForKey

O **método ShardMap.OpenConnectionForKey** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkey), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey)) retorna uma ligação pronto para emitir comandos para a base de dados apropriado com base no valor da **chave** parâmetro. Informações de partição horizontal é colocado em cache no aplicativo, o **ShardMapManager**, para que estes pedidos normalmente não envolvem uma pesquisa de base de dados contra a **Global mapa de partições horizontais** base de dados.

```Java
// Syntax:
public Connection openConnectionForKey(Object key, String connectionString, ConnectionOptions options)
```

```csharp
// Syntax:
public SqlConnection OpenConnectionForKey<TKey>(TKey key, string connectionString, ConnectionOptions options)
```

* O **chave** parâmetro é utilizado como uma chave de pesquisa em mapa de partições horizontais para determinar a base de dados adequado para o pedido.
* O **connectionString** é usado para passar as credenciais de utilizador para a ligação pretendida. Nenhum nome de base de dados ou o nome do servidor que está incluído nesta *connectionString* uma vez que o método determina a base de dados e o servidor com o **ShardMap**.
* O **connectionOptions** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.connectionoptions), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.connectionoptions)) deve ser definido como **ConnectionOptions.Validate** se um ambiente onde o Maio de mapas de partições horizontais alteração e de linhas podem passar para outras bases de dados como resultado de operações de intercalação ou divisão. Esta validação envolve uma consulta breve para o mapa de partições horizontais local no destino da base de dados (não para o mapa de partições horizontais global) antes da ligação é entregue à aplicação.

Se a validação contra o mapa de partições horizontais local falhar (que indica que a cache é incorreta), o Gestor de mapas de partições horizontais consulta o mapa de partições horizontais global para obter o novo valor correto para a pesquisa, atualizar a cache e obter e retornar a conexão de banco de dados apropriadas .

Uso **ConnectionOptions.None** apenas quando as alterações de mapeamento de partição horizontal não são esperadas enquanto um aplicativo está online. Nesse caso, os valores do cache podem ser considerados sempre esteja correto, e a chamada de validação extra ida e volta para a base de dados de destino pode ser ignorada com segurança. Que reduz o tráfego de base de dados. O **connectionOptions** também pode ser definido por meio de um valor num ficheiro de configuração para indicar se as alterações de fragmentação devem ou não durante um período de tempo.  

Este exemplo utiliza o valor de uma chave de número inteiro **CustomerID**, com um **ShardMap** com o nome do objeto **customerShardMap**.  

```Java
int customerId = 12345;
int productId = 4321;
// Looks up the key in the shard map and opens a connection to the shard
try (Connection conn = shardMap.openConnectionForKey(customerId, Configuration.getCredentialsConnectionString())) {
    // Create a simple command that will insert or update the customer information
    PreparedStatement ps = conn.prepareStatement("UPDATE Sales.Customer SET PersonID = ? WHERE CustomerID = ?");

    ps.setInt(1, productId);
    ps.setInt(2, customerId);
    ps.executeUpdate();
} catch (SQLException e) {
    e.printStackTrace();
}
```

```csharp
int customerId = 12345;
int newPersonId = 4321;

// Connect to the shard for that customer ID. No need to call a SqlConnection
// constructor followed by the Open method.
using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId, Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate))
{
    // Execute a simple command.
    SqlCommand cmd = conn.CreateCommand();
    cmd.CommandText = @"UPDATE Sales.Customer
                        SET PersonID = @newPersonID WHERE CustomerID = @customerID";

    cmd.Parameters.AddWithValue("@customerID", customerId);cmd.Parameters.AddWithValue("@newPersonID", newPersonId);
    cmd.ExecuteNonQuery();
}  
```

O **OpenConnectionForKey** método retorna uma nova conexão já aberta para a base de dados correto. Ligações utilizadas assim tirar total partido do agrupamento de ligações.

O **método OpenConnectionForKeyAsync** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkeyasync), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkeyasync)) também está disponível se a aplicação faz uso de programação assíncrona.

## <a name="integrating-with-transient-fault-handling"></a>Integração com o processamento de falhas transitórias

É uma prática recomendada no desenvolvimento de aplicativos de acesso de dados na cloud garantir que as falhas transitórias são detetadas pela aplicação e que as operações são repetidas várias vezes antes que ocorra um erro. Processamento para aplicações na cloud de falhas transitórias é abordada no processamento de erros transitórios ([Java](/java/api/com.microsoft.azure.elasticdb.core.commons.transientfaulthandling), [.NET](https://docs.microsoft.com/previous-versions/msp-n-p/dn440719(v=pandp.60))).

Processamento de falhas transitórias pode coexistir, naturalmente, com o padrão de encaminhamento dependente de dados. O principal requisito é repetir o acesso de dados inteiro pedido incluindo o **usando** bloco que obteve a ligação de encaminhamento dependente de dados. O exemplo anterior poderia ser reescrito da seguinte forma.

### <a name="example---data-dependent-routing-with-transient-fault-handling"></a>Exemplo - encaminhamento com o processamento de falhas transitórias dependente de dados

```Java
int customerId = 12345;
int productId = 4321;
try {
    SqlDatabaseUtils.getSqlRetryPolicy().executeAction(() -> {
        // Looks up the key in the shard map and opens a connection to the shard
        try (Connection conn = shardMap.openConnectionForKey(customerId, Configuration.getCredentialsConnectionString())) {
            // Create a simple command that will insert or update the customer information
            PreparedStatement ps = conn.prepareStatement("UPDATE Sales.Customer SET PersonID = ? WHERE CustomerID = ?");

            ps.setInt(1, productId);
            ps.setInt(2, customerId);
            ps.executeUpdate();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    });
} catch (Exception e) {
    throw new StoreException(e.getMessage(), e);
}
```

```csharp
int customerId = 12345;
int newPersonId = 4321;

Configuration.SqlRetryPolicy.ExecuteAction(() =&gt;
{
    // Connect to the shard for a customer ID.
    using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId, Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate))
    {
        // Execute a simple command
        SqlCommand cmd = conn.CreateCommand();

        cmd.CommandText = @"UPDATE Sales.Customer
                            SET PersonID = @newPersonID
                            WHERE CustomerID = @customerID";

        cmd.Parameters.AddWithValue("@customerID", customerId);
        cmd.Parameters.AddWithValue("@newPersonID", newPersonId);
        cmd.ExecuteNonQuery();

        Console.WriteLine("Update completed");
    }
});
```

Os pacotes necessários para implementar o processamento de falhas transitórias são transferidos automaticamente quando cria a aplicação de exemplo da base de dados elástica.

## <a name="transactional-consistency"></a>Consistência transacional

Propriedades transacionais são garantidas para todas as operações locais para uma partição horizontal. Por exemplo, transações enviadas por meio de encaminhamento dependente de dados de execução dentro do escopo da partição horizontal de destino para a ligação. Neste momento, não há nenhum capacidades fornecidas para inscrição várias ligações numa transação e, portanto, não há transacionais garantias para operações executadas em partições horizontais.

## <a name="next-steps"></a>Passos Seguintes

Para anular a exposição de uma partição horizontal, ou tentar expor uma partição horizontal, consulte [usando a classe RecoveryManager para corrigir problemas de mapa de partições horizontais](sql-database-elastic-database-recovery-manager.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]