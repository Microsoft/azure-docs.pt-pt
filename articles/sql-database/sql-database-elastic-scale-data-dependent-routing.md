---
title: Roteamento dependente de dados com o banco de dado SQL do Azure
description: Como usar a classe ShardMapManager em aplicativos .NET para roteamento dependente de dados, um recurso de bancos de dados fragmentados no banco de dados SQL do Azure
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
ms.openlocfilehash: 319f2744cd437aea51a05592cd7a26f8b58fe761
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73690284"
---
# <a name="use-data-dependent-routing-to-route-a-query-to-appropriate-database"></a>Usar o roteamento dependente de dados para rotear uma consulta para o banco de dado apropriado

O **Roteamento Dependente de dados** é a capacidade de usar os dados em uma consulta para rotear a solicitação para um banco de dados apropriado. O roteamento dependente de dados é um padrão fundamental ao trabalhar com bancos de dado fragmentados. O contexto da solicitação também pode ser usado para rotear a solicitação, especialmente se a chave de fragmentação não fizer parte da consulta. Cada consulta ou transação específica em um aplicativo que usa o roteamento dependente de dados é restrita ao acesso a um banco por solicitação. Para as ferramentas elásticas do banco de dados SQL do Azure, esse roteamento é realizado com a classe **ShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)).

O aplicativo não precisa controlar várias cadeias de conexão ou locais de BD associados a diferentes fatias de dados no ambiente fragmentado. Em vez disso, o [Gerenciador de mapa de fragmentos](sql-database-elastic-scale-shard-map-management.md) abre conexões para os bancos de dados corretos quando necessário, com base nos dados no mapa de fragmentos e no valor da chave de fragmentação que é o destino da solicitação do aplicativo. Normalmente, a chave é a *Customer_ID*, *tenant_id*, *date_key*ou algum outro identificador específico que é um parâmetro fundamental da solicitação de banco de dados.

Para obter mais informações, consulte [escalar horizontalmente SQL Server com roteamento dependente de dados](https://technet.microsoft.com/library/cc966448.aspx).

## <a name="download-the-client-library"></a>Baixar a biblioteca de cliente

Para baixar:

* A versão do Java da biblioteca, consulte [repositório central do Maven](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools).
* A versão do .NET da biblioteca, consulte [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## <a name="using-a-shardmapmanager-in-a-data-dependent-routing-application"></a>Usando um ShardMapManager em um aplicativo de roteamento dependente de dados

Os aplicativos devem instanciar o **ShardMapManager** durante a inicialização, usando a chamada de fábrica **GetSQLShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)). Neste exemplo, um **ShardMapManager** e um **ShardMap** específico que ele contém são inicializados. Este exemplo mostra os métodos GetSqlShardMapManager e GetRangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getrangeshardmap), [.net](https://docs.microsoft.com/previous-versions/azure/dn824173(v=azure.100))).

```Java
ShardMapManager smm = ShardMapManagerFactory.getSqlShardMapManager(connectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> rangeShardMap = smm.getRangeShardMap(Configuration.getRangeShardMapName(), ShardKeyType.Int32);
```

```csharp
ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> customerShardMap = smm.GetRangeShardMap<int>("customerMap"); 
```

### <a name="use-lowest-privilege-credentials-possible-for-getting-the-shard-map"></a>Use as credenciais de privilégio mais baixo possíveis para obter o mapa de fragmentos

Se um aplicativo não estiver manipulando o mapa de fragmentos em si, as credenciais usadas no método de fábrica deverão ter permissões somente leitura no banco de dados do **mapa de fragmentos global** . Essas credenciais são normalmente diferentes das credenciais usadas para abrir conexões com o Gerenciador de mapa de fragmentos. Consulte também [as credenciais usadas para acessar a biblioteca de cliente do banco de dados elástico](sql-database-elastic-scale-manage-credentials.md).

## <a name="call-the-openconnectionforkey-method"></a>Chamar o método OpenConnectionForKey

O **método ShardMap. OpenConnectionForKey** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkey), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey)) retorna uma conexão pronta para emitir comandos para o banco de dados apropriado com base no valor do parâmetro de **chave** . As informações de fragmento são armazenadas em cache no aplicativo pelo **ShardMapManager**, portanto, essas solicitações normalmente não envolvem uma pesquisa de banco de dados no banco de dados do **mapa de fragmentos global** .

```Java
// Syntax:
public Connection openConnectionForKey(Object key, String connectionString, ConnectionOptions options)
```

```csharp
// Syntax:
public SqlConnection OpenConnectionForKey<TKey>(TKey key, string connectionString, ConnectionOptions options)
```

* O parâmetro **Key** é usado como uma chave de pesquisa no mapa de fragmentos para determinar o banco de dados apropriado para a solicitação.
* O **ConnectionString** é usado para passar apenas as credenciais do usuário para a conexão desejada. Nenhum nome de banco de dados ou nome de servidor está incluído nessa *ConnectionString* , pois o método determina o banco de dados e o servidor usando o **ShardMap**.
* As **ConnectionOptions** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.connectionoptions), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.connectionoptions)) devem ser definidas como **ConnectionOptions. valide** se um ambiente em que mapas de fragmentos podem ser alterados e as linhas podem ser movidas para outros bancos de dados como resultado de operações de divisão ou mesclagem. Essa validação envolve uma breve consulta ao mapa de fragmentos local no banco de dados de destino (não ao mapa de fragmentos global) antes que a conexão seja entregue ao aplicativo.

Se a validação em relação ao mapa de fragmentos local falhar (indicando que o cache está incorreto), o Gerenciador de mapa de fragmentos consultará o mapa de fragmentos global para obter o novo valor correto para a pesquisa, atualizará o cache e obterá e retornará a conexão de banco de dados apropriada .

Use **ConnectionOptions. None** somente quando as alterações de mapeamento de fragmentos não forem esperadas enquanto um aplicativo estiver online. Nesse caso, os valores em cache podem ser considerados sempre corretos e a chamada de validação de ida e volta extra para o banco de dados de destino pode ser ignorada com segurança. Isso reduz o tráfego do banco de dados. As **ConnectionOptions** também podem ser definidas por meio de um valor em um arquivo de configuração para indicar se as alterações de fragmentação são esperadas ou não durante um período de tempo.  

Este exemplo usa o valor de uma chave de inteiro **CustomerID**, usando um objeto **ShardMap** chamado **customerShardMap**.  

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

O método **OpenConnectionForKey** retorna uma nova conexão já aberta ao banco de dados correto. As conexões utilizadas dessa maneira ainda aproveitam totalmente o pool de conexões.

O **método openconnectionforkeyasync Method** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkeyasync), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkeyasync)) também estará disponível se seu aplicativo fizer uso da programação assíncrona.

## <a name="integrating-with-transient-fault-handling"></a>Integração com tratamento de falhas transitórias

Uma prática recomendada no desenvolvimento de aplicativos de acesso a dados na nuvem é garantir que as falhas transitórias sejam detectadas pelo aplicativo e que as operações sejam repetidas várias vezes antes de gerar um erro. O tratamento de falhas transitórias para aplicativos de nuvem é discutido em tratamento de falhas transitórias ([Java](/java/api/com.microsoft.azure.elasticdb.core.commons.transientfaulthandling), [.net](https://docs.microsoft.com/previous-versions/msp-n-p/dn440719(v=pandp.60))).

O tratamento de falhas transitórias pode coexistir naturalmente com o padrão de roteamento dependente de dados. O principal requisito é repetir a solicitação de acesso a dados inteira, incluindo o bloco **using** que obteve a conexão de roteamento dependente de dados. O exemplo anterior poderia ser reescrito da seguinte maneira.

### <a name="example---data-dependent-routing-with-transient-fault-handling"></a>Exemplo-roteamento dependente de dados com tratamento de falhas transitórias

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

Os pacotes necessários para implementar o tratamento de falhas transitórias são baixados automaticamente quando você cria o aplicativo de exemplo de banco de dados elástico.

## <a name="transactional-consistency"></a>Consistência transacional

As propriedades transacionais são garantidas para todas as operações locais em um fragmento. Por exemplo, as transações enviadas por meio do roteamento dependente de dados são executadas dentro do escopo do fragmento de destino para a conexão. Neste momento, não há recursos fornecidos para inscrever várias conexões em uma transação e, portanto, não há nenhuma garantia transacional para operações executadas em fragmentos.

## <a name="next-steps"></a>Passos seguintes

Para desanexar um fragmento ou anexar novamente um fragmento, consulte [usando a classe RecoveryManager para corrigir problemas de mapa de fragmentos](sql-database-elastic-database-recovery-manager.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]