---
title: Gerenciando credenciais na biblioteca de cliente do banco de dados elástico | Microsoft Docs
description: Como definir o nível certo de credenciais, administrador para somente leitura, para aplicativos de banco de dados elástico
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: d89e83092775828016c2c47a96164319f5474c1e
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568414"
---
# <a name="credentials-used-to-access-the-elastic-database-client-library"></a>Credenciais usadas para acessar a biblioteca de cliente do banco de dados elástico

A [biblioteca de cliente do banco de dados elástico](sql-database-elastic-database-client-library.md) usa três tipos diferentes de credenciais para acessar o [Gerenciador de mapa de fragmentos](sql-database-elastic-scale-shard-map-management.md). Dependendo da necessidade, use a credencial com o menor nível de acesso possível.

* **Credenciais de gerenciamento**: para criar ou manipular um Gerenciador de mapa de fragmentos. (Consulte o [Glossário](sql-database-elastic-scale-glossary.md).)
* **Credenciais de acesso**: para acessar um Gerenciador de mapa de fragmentos existente para obter informações sobre fragmentos.
* **Credenciais de conexão**: para se conectar a fragmentos.

Consulte também [Gerenciando bancos de dados e logons no Azure SQL Database](sql-database-manage-logins.md).

## <a name="about-management-credentials"></a>Sobre as credenciais de gerenciamento

As credenciais de gerenciamento são usadas para criar um objeto **ShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)) para aplicativos que manipulam mapas de fragmentos. (Por exemplo, consulte [adicionando um fragmento usando as ferramentas de banco](sql-database-elastic-scale-add-a-shard.md) de dados elástico e o [Roteamento Dependente de dado](sql-database-elastic-scale-data-dependent-routing.md)). O usuário da biblioteca de cliente de escala elástica cria os usuários do SQL e os logons do SQL e garante que cada um receba as permissões de leitura/gravação no banco de dados do mapa de fragmentos global e também em todos os bancos de dado de fragmentos. Essas credenciais são usadas para manter o mapa de fragmentos global e os mapas de fragmentos locais quando as alterações no mapa de fragmentos são executadas. Por exemplo, use as credenciais de gerenciamento para criar o objeto do Gerenciador de mapa de fragmentos (usando **GetSqlShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)):

```java
// Obtain a shard map manager.
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmAdminConnectionString,ShardMapManagerLoadPolicy.Lazy);
```

A variável **smmAdminConnectionString** é uma cadeia de conexão que contém as credenciais de gerenciamento. A ID de usuário e a senha fornecem acesso de leitura/gravação ao banco de dados do mapa de fragmentos e a fragmentos individuais. A cadeia de conexão de gerenciamento também inclui o nome do servidor e o nome do banco de dados para identificar o banco de dados do mapa de fragmentos global. Aqui está uma cadeia de conexão típica para essa finalidade:

```java
"Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;”
```

Não use valores na forma de "username@server" — em vez disso, basta usar o valor "username".  Isso ocorre porque as credenciais devem funcionar no banco de dados do Gerenciador de mapa de fragmentos e fragmentos individuais, que podem estar em servidores diferentes.

## <a name="access-credentials"></a>Credenciais de acesso

Ao criar um Gerenciador de mapa de fragmentos em um aplicativo que não administra mapas de fragmentos, use credenciais que tenham permissões somente leitura no mapa de fragmentos global. As informações recuperadas do mapa de fragmentos global sob essas credenciais são usadas para [Roteamento Dependente de dados](sql-database-elastic-scale-data-dependent-routing.md) e para popular o cache de mapa de fragmentos no cliente. As credenciais são fornecidas por meio do mesmo padrão de chamada para **GetSqlShardMapManager**:

```java
// Obtain shard map manager.
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmReadOnlyConnectionString, ShardMapManagerLoadPolicy.Lazy);  
```

Observe o uso do **smmReadOnlyConnectionString** para refletir o uso de credenciais diferentes para esse acesso em nome de usuários **não administradores** : essas credenciais não devem fornecer permissões de gravação no mapa de fragmentos global.

## <a name="connection-credentials"></a>Credenciais de conexão

Credenciais adicionais são necessárias ao usar o método **OpenConnectionForKey** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkey), [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey)) para acessar um fragmento associado a uma chave de fragmentação. Essas credenciais precisam fornecer permissões para acesso somente leitura às tabelas do mapa de fragmentos locais que residem no fragmento. Isso é necessário para executar a validação de conexão para roteamento dependente de dados no fragmento. Este trecho de código permite o acesso a dados no contexto do roteamento dependente de dados:

```csharp
using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>(targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate))
```

Neste exemplo, **smmUserConnectionString** mantém a cadeia de conexão para as credenciais do usuário. Para o BD SQL do Azure, aqui está uma cadeia de conexão típica para as credenciais do usuário:

```java
"User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;”  
```

Assim como acontece com as credenciais de administrador, não use valores na forma deusername@server"". Em vez disso, basta usar "username".  Observe também que a cadeia de conexão não contém um nome de servidor e um nome de banco de dados. Isso ocorre porque a chamada **OpenConnectionForKey** direciona automaticamente a conexão para o fragmento correto com base na chave. Portanto, o nome do banco de dados e o nome do servidor não são fornecidos.

## <a name="see-also"></a>Consulte também

[Gerir bases de dados e inícios de sessão na Base de Dados SQL do Azure](sql-database-manage-logins.md)

[Securing your SQL Database (Proteger a sua Base de Dados SQL)](sql-database-security-overview.md)

[trabalhos de Banco de Dados Elástico](elastic-jobs-overview.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
