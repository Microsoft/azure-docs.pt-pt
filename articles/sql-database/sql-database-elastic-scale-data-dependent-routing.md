---
title: Encaminhamento Dependente de Dados
description: Como utilizar a classe ShardMapManager em aplicações .NET para encaminhamento dependente de dados, uma funcionalidade de bases de dados esfumaçadas na Base de Dados Azure SQL
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
ms.openlocfilehash: fbdf8e316368be02ebd0c4bfd320917c20d80777
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77069460"
---
# <a name="use-data-dependent-routing-to-route-a-query-to-appropriate-database"></a>Utilizar o encaminhamento dependente de dados para encaminhar uma consulta para base de dados apropriada

**O encaminhamento dependente de dados** é a capacidade de utilizar os dados numa consulta para encaminhar o pedido para uma base de dados apropriada. O encaminhamento dependente de dados é um padrão fundamental quando se trabalha com bases de dados esfarditas. O contexto do pedido também pode ser utilizado para encaminhar o pedido, especialmente se a chave sharding não fizer parte da consulta. Cada consulta ou transação específica numa aplicação utilizando o encaminhamento dependente de dados está restrita ao acesso a uma base de dados por pedido. Para as ferramentas elásticas azure SQL Database, este encaminhamento é realizado com a classe **ShardMapManager** [(Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.NET).](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)

A aplicação não necessita de rastrear várias cadeias de ligação ou localizações DB associadas a diferentes fatias de dados no ambiente fragmento. Em vez disso, o [Shard Map Manager](sql-database-elastic-scale-shard-map-management.md) abre ligações às bases de dados corretas quando necessário, com base nos dados no mapa do fragmento e no valor da chave de sharding que é o alvo do pedido da aplicação. A chave é tipicamente o *customer_id*, *tenant_id*, *date_key*, ou algum outro identificador específico que é um parâmetro fundamental do pedido de base de dados.

Para mais informações, consulte [o SqL Server com encaminhamento dependente de dados](https://technet.microsoft.com/library/cc966448.aspx).

## <a name="download-the-client-library"></a>Descarregue a biblioteca de clientes

Para descarregar:

* A versão Java da biblioteca, ver [Maven Central Repositório](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools).
* A versão .NET da biblioteca, ver [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## <a name="using-a-shardmapmanager-in-a-data-dependent-routing-application"></a>Usando um ShardMapManager numa aplicação de encaminhamento dependente de dados

As aplicações devem instantaneamente o **ShardMapManager** durante a inicialização, utilizando a chamada de fábrica **GetSQLShardMapManager** [(Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)). Neste exemplo, tanto um **ShardMapManager** como um **ShardMap** específico que contém são inicializados. Este exemplo mostra os métodos GetSqlShardMapManager e GetRangeShardMap[(Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getrangeshardmap), [.NET).](https://docs.microsoft.com/previous-versions/azure/dn824173(v=azure.100))

```Java
ShardMapManager smm = ShardMapManagerFactory.getSqlShardMapManager(connectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> rangeShardMap = smm.getRangeShardMap(Configuration.getRangeShardMapName(), ShardKeyType.Int32);
```

```csharp
ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> customerShardMap = smm.GetRangeShardMap<int>("customerMap"); 
```

### <a name="use-lowest-privilege-credentials-possible-for-getting-the-shard-map"></a>Use credenciais de privilégio mais baixas possíveis para obter o mapa do fragmento

Se uma aplicação não estiver a manipular o mapa do fragmento em si, as credenciais utilizadas no método de fábrica devem ter permissões apenas de leitura na base de dados global do Mapa do **Fragmento.** Estas credenciais são tipicamente diferentes das credenciais usadas para abrir ligações ao gestor de mapas de fragmentos. Consulte também [credenciais utilizadas para aceder à biblioteca de clientes da Base de Dados Elástica.](sql-database-elastic-scale-manage-credentials.md)

## <a name="call-the-openconnectionforkey-method"></a>Ligue para o método OpenConnectionForKey

O **método ShardMap.OpenConnectionForKey** [(Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkey), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey)) devolve uma ligação pronta para emitir comandos à base de dados adequada com base no valor do **parâmetro-chave.** A informação do Fragmento é colocada em cache na aplicação pelo **ShardMapManager,** pelo que estes pedidos normalmente não envolvem uma pesquisa de base de dados contra a base de dados **Global Shard Map.**

```Java
// Syntax:
public Connection openConnectionForKey(Object key, String connectionString, ConnectionOptions options)
```

```csharp
// Syntax:
public SqlConnection OpenConnectionForKey<TKey>(TKey key, string connectionString, ConnectionOptions options)
```

* O **parâmetro-chave** é utilizado como chave de pesquisa no mapa do fragmento para determinar a base de dados adequada para o pedido.
* A **ligaçãoString** é utilizada para passar apenas as credenciais de utilizador para a ligação desejada. Nenhum nome de base de dados ou nome do servidor está incluído nesta *ligaçãoString* uma vez que o método determina a base de dados e o servidor usando o **ShardMap**.
* As **opções** de ligação[(Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.connectionoptions), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.connectionoptions)) devem ser definidas para **ConnectionOptions.Valide** se um ambiente onde os mapas de fragmentos podem mudar e as linhas podem mover-se para outras bases de dados em resultado de operações de divisão ou fusão. Esta validação envolve uma breve consulta ao mapa local na base de dados do alvo (não ao mapa global do fragmento) antes de a ligação ser entregue à aplicação.

Se a validação contra o mapa local falhar (indicando que a cache está incorreta), o Shard Map Manager questiona o mapa global do fragmento para obter o novo valor correto para a procura, atualizar a cache e obter e devolver a conexão de base de dados apropriada .

Utilize Opções de **Ligação.Nenhuma** apenas quando não são esperadas alterações de mapeamento de fragmentos enquanto uma aplicação estiver online. Nesse caso, os valores em cache podem ser considerados sempre corretos, e a chamada de validação extra de ida e volta para a base de dados do alvo pode ser ignorada com segurança. Isso reduz o tráfego de bases de dados. As opções de **ligação** também podem ser definidas através de um valor num ficheiro de configuração para indicar se as alterações de sharding são esperadas ou não durante um período de tempo.  

Este exemplo utiliza o valor de uma chave de **identificação**do cliente integer, utilizando um objeto **ShardMap** chamado **clienteShardMap**.  

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

O método **OpenConnectionForKey** devolve uma nova ligação já aberta à base de dados correta. As ligações utilizadas desta forma ainda tiram o máximo partido do pool de ligação.

O **método OpenConnectionForKeyAsync** [(Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkeyasync), [.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkeyasync)também está disponível se a sua aplicação fizer a utilização de uma programação assíncrona.

## <a name="integrating-with-transient-fault-handling"></a>Integração com manuseamento transitório de falhas

A melhor prática no desenvolvimento de aplicações de acesso a dados na nuvem é garantir que falhas transitórias são apanhadas pela app, e que as operações são novamente tentadas várias vezes antes de lançar um erro. O manuseamento transitório de falhas para aplicações em nuvem é discutido no Transitório Fault Handling[(Java](/java/api/com.microsoft.azure.elasticdb.core.commons.transientfaulthandling), [.NET).](https://docs.microsoft.com/previous-versions/msp-n-p/dn440719(v=pandp.60))

O manuseamento transitório de falhas pode coexistir naturalmente com o padrão de encaminhamento dependente de dados. O requisito principal é rejulgar todo o pedido de acesso de dados, incluindo o bloco **de utilização** que obteve a ligação de encaminhamento dependente de dados. O exemplo anterior poderia ser reescrito da seguinte forma.

### <a name="example---data-dependent-routing-with-transient-fault-handling"></a>Exemplo - encaminhamento dependente de dados com manuseamento transitório de falhas

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

Configuration.SqlRetryPolicy.ExecuteAction(() -> {

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

As embalagens necessárias para implementar o manuseamento transitório de falhas são descarregadas automaticamente quando se constrói a aplicação de amostra elástica de dados de dados.

## <a name="transactional-consistency"></a>Consistência transacional

As propriedades transacionais são garantidas para todas as operações locais a um fragmento. Por exemplo, as transações submetidas através de encaminhamento dependente de dados executam no âmbito do fragmento-alvo para a ligação. Neste momento, não existem capacidades para alistar múltiplas ligações numa transação, pelo que não existem garantias transacionais para operações realizadas em fragmentos.

## <a name="next-steps"></a>Passos seguintes

Para desprender um fragmento, ou para voltar a colocar um fragmento, consulte [Utilizar a classe RecoveryManager para corrigir problemas](sql-database-elastic-database-recovery-manager.md) no mapa do fragmento

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
