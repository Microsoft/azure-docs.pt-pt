---
title: Encaminhamento Dependente de Dados
description: Como utilizar a classe ShardMapManager em aplicações .NET para encaminhamento dependente de dados, uma característica de bases de dados de fragmentos na Base de Dados Azure SQL
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 60e8b4b21a9e62279cd0eccfabbbed680183e2a9
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92787028"
---
# <a name="use-data-dependent-routing-to-route-a-query-to-an-appropriate-database"></a>Utilize o encaminhamento dependente de dados para encaminhar uma consulta para uma base de dados apropriada
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

**O encaminhamento dependente de dados** é a capacidade de utilizar os dados numa consulta para encaminhar o pedido para uma base de dados apropriada. O encaminhamento dependente de dados é um padrão fundamental quando se trabalha com bases de dados de fragmentos. O contexto do pedido também pode ser utilizado para encaminhar o pedido, especialmente se a chave de fragmentos não fizer parte da consulta. Cada consulta ou transação específica de uma aplicação utilizando o encaminhamento dependente de dados é restrita ao acesso a uma base de dados por pedido. Para as ferramentas elásticas Azure SQL Database, este encaminhamento é realizado com a classe **ShardMapManager** [(Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.NET).](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)

A aplicação não necessita de rastrear várias cadeias de ligação ou localizações DB associadas a diferentes fatias de dados no ambiente desosseado. Em vez disso, o [Shard Map Manager](elastic-scale-shard-map-management.md) abre ligações às bases de dados corretas quando necessário, com base nos dados do mapa de fragmentos e no valor da chave de fragmentos que é o alvo do pedido da aplicação. A chave é tipicamente a *customer_id* , *tenant_id* , *date_key* , ou algum outro identificador específico que é um parâmetro fundamental do pedido de base de dados.

Para obter mais informações, consulte [o Servidor SQL de Escala com Data-Dependent Encaminhamento](/previous-versions/sql/sql-server-2005/administrator/cc966448(v=technet.10)).

## <a name="download-the-client-library"></a>Descarregue a biblioteca do cliente

Para baixar:

* A versão Java da biblioteca, ver [Repositório Central de Maven.](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools)
* A versão .NET da biblioteca, ver [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## <a name="using-a-shardmapmanager-in-a-data-dependent-routing-application"></a>Utilizando um ShardMapManager numa aplicação de encaminhamento dependente de dados

As aplicações devem instantaneamente o **ShardMapManager** durante a inicialização, utilizando a chamada de fábrica **GetSQLShardMapManager** [(Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)). Neste exemplo, tanto um **ShardMapManager** como um **ShardMap** específico que contém são inicializados. Este exemplo mostra os métodos GetSqlShardMapManager e GetRangeShardMap[(Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getrangeshardmap), [.NET).](/previous-versions/azure/dn824173(v=azure.100))

```Java
ShardMapManager smm = ShardMapManagerFactory.getSqlShardMapManager(connectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> rangeShardMap = smm.getRangeShardMap(Configuration.getRangeShardMapName(), ShardKeyType.Int32);
```

```csharp
ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> customerShardMap = smm.GetRangeShardMap<int>("customerMap"); 
```

### <a name="use-lowest-privilege-credentials-possible-for-getting-the-shard-map"></a>Use as credenciais de privilégio mais baixas possíveis para obter o mapa de fragmentos

Se uma aplicação não estiver a manipular o mapa de fragmentos em si, as credenciais utilizadas no método de fábrica devem ter permissões apenas de leitura na base de dados **Global Shard Map.** Estas credenciais são tipicamente diferentes das credenciais usadas para abrir ligações ao gestor de mapas de fragmentos. Consulte também [credenciais utilizadas para aceder à biblioteca de clientes Elastic Database.](elastic-scale-manage-credentials.md)

## <a name="call-the-openconnectionforkey-method"></a>Ligue para o método OpenConnectionForKey

O **método ShardMap.OpenConnectionForKey** [(Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkey), [.NET)](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey)devolve uma ligação pronta para emitir comandos na base de dados apropriada com base no valor do parâmetro **chave.** As informações shard estão em cache na aplicação pelo **ShardMapManager,** pelo que estes pedidos não envolvem normalmente uma procura de base de dados contra a base de dados **Global Shard Map.**

```Java
// Syntax:
public Connection openConnectionForKey(Object key, String connectionString, ConnectionOptions options)
```

```csharp
// Syntax:
public SqlConnection OpenConnectionForKey<TKey>(TKey key, string connectionString, ConnectionOptions options)
```

* O **parâmetro-chave** é usado como uma chave de procura no mapa de fragmentos para determinar a base de dados apropriada para o pedido.
* A **ligaçãoStragem** é utilizada apenas para passar as credenciais do utilizador para a ligação desejada. Nenhum nome de base de dados ou nome do servidor está incluído nesta *ligaçãoDese* o método determina a base de dados e o servidor utilizando o **ShardMap** .
* As **opções de conexão** [(Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.connectionoptions), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.connectionoptions)) devem ser definidas para **ConnectionOptions.Valide** se um ambiente onde mapas de fragmentos podem mudar e as linhas podem mover-se para outras bases de dados como resultado de operações de divisão ou fusão. Esta validação envolve uma breve consulta ao mapa de fragmentos local na base de dados-alvo (não ao mapa global de fragmentos) antes de a ligação ser entregue à aplicação.

Se a validação contra o mapa de fragmentos local falhar (indicando que a cache está incorreta), o Shard Map Manager consulta o mapa global de fragmentos para obter o novo valor correto para a procura, atualizar a cache e obter e devolver a ligação de base de dados apropriada.

Use **Opções de Ligação.Nenhuma** só quando não são esperadas alterações de mapeamento de fragmentos enquanto uma aplicação estiver online. Nesse caso, os valores em cache podem ser assumidos como sempre corretos, e a chamada extra de validação de ida e volta para a base de dados-alvo pode ser ignorada com segurança. Isso reduz o tráfego de bases de dados. As **perguntas de ligação** também podem ser definidas através de um valor num ficheiro de configuração para indicar se são ou não esperadas alterações de fragmentos durante um período de tempo.  

Este exemplo utiliza o valor de uma chave de número inteiro **CustomerID,** utilizando um objeto **ShardMap** denominado **clienteShardMap** .  

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

O método **OpenConnectionForKey** devolve uma nova ligação já aberta à base de dados correta. As ligações utilizadas desta forma ainda aproveitam ao máximo a ligação.

O **método OpenConnectionForKeyAsync** [(Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkeyasync), [.NET)](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkeyasync)também está disponível se a sua aplicação utilizar a programação assíncronus.

## <a name="integrating-with-transient-fault-handling"></a>Integração com o tratamento transitório de falhas

Uma das melhores práticas no desenvolvimento de aplicações de acesso a dados na nuvem é garantir que as falhas transitórias são apanhadas pela app, e que as operações são novamente experimentadas várias vezes antes de lançar um erro. O tratamento transitório de falhas para aplicações em nuvem é discutido no Tratamento transitório de falhas[(Java](/java/api/com.microsoft.azure.elasticdb.core.commons.transientfaulthandling), [.NET](/previous-versions/msp-n-p/dn440719(v=pandp.60))).

O manuseamento de falhas transitórias pode coexistir naturalmente com o padrão de encaminhamento Data-Dependent. O requisito-chave é voltar a tentar todo o pedido de acesso aos dados, incluindo o bloco **de utilização** que obteve a ligação de encaminhamento dependente de dados. O exemplo anterior pode ser reescrito da seguinte forma.

### <a name="example---data-dependent-routing-with-transient-fault-handling"></a>Exemplo - encaminhamento dependente de dados com tratamento de falhas transitórias

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

Os pacotes necessários para implementar o manuseamento de falhas transitórias são descarregados automaticamente quando se constrói a aplicação de amostra de base de dados elástica.

## <a name="transactional-consistency"></a>Consistência transacional

As propriedades transacionais são garantidas para todas as operações locais a um fragmento. Por exemplo, as transações submetidas através de encaminhamento dependente de dados executam no âmbito do fragmento-alvo para a ligação. Neste momento, não existem capacidades para a inscrição de múltiplas ligações numa transação, pelo que não existem garantias transacionais para operações realizadas em fragmentos.

## <a name="next-steps"></a>Passos seguintes

Para desprender um fragmento, ou para voltar a colocar um fragmento, consulte [utilizar a classe RecoveryManager para corrigir problemas de mapa de fragmentos](elastic-database-recovery-manager.md).

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]