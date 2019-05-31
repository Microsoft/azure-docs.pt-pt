---
title: Gerir credenciais na biblioteca de cliente de base de dados elástica | Documentos da Microsoft
description: Como definir o nível certo de credenciais, o administrador para só de leitura, para aplicações de bases de dados elásticas
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
ms.date: 01/03/2019
ms.openlocfilehash: 8a62ec95c715c08a8fddc09f0c8e5f5bba368556
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241764"
---
# <a name="credentials-used-to-access-the-elastic-database-client-library"></a>Credenciais utilizadas para acessar a biblioteca de cliente da base de dados elástica

O [biblioteca de clientes de bases de dados elásticas](sql-database-elastic-database-client-library.md) utiliza três tipos diferentes de credenciais para aceder a [Gestor de mapas de partições horizontais](sql-database-elastic-scale-shard-map-management.md). Dependendo da necessidade, utilize a credencial com o nível mais baixo de acesso possíveis.

* **Credenciais de gestão**: para criar ou manipular um Gestor de mapas de partições horizontais. (Consulte a [glossário](sql-database-elastic-scale-glossary.md).)
* **Credenciais de acesso**: para aceder a um Gestor de mapas de partições horizontais existente para obter informações sobre as partições horizontais.
* **As credenciais de ligação**: ligar a partições horizontais.

Consulte também [gestão de bases de dados e inícios de sessão na base de dados do Azure SQL](sql-database-manage-logins.md).

## <a name="about-management-credentials"></a>Informações sobre credenciais de gestão

Credenciais de gestão são utilizadas para criar uma **ShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)) objeto para aplicativos que manipulam mapas de partições horizontais. (Por exemplo, veja [adicionando uma partição horizontal com as ferramentas de bases de dados elásticas](sql-database-elastic-scale-add-a-shard.md) e [encaminhamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md)). O utilizador da biblioteca de cliente de escala elástica cria os utilizadores SQL e os inícios de sessão SQL e certifica-se de que cada um é concedido as permissões de leitura/escrita sobre a base de dados do mapa de partições horizontais global e todas as partições horizontais bases de dados também. Estas credenciais são utilizadas para manter o mapa de partições horizontais global e os mapas de partições horizontais local quando as alterações para o mapa de partições horizontais são realizadas. Por exemplo, utilize as credenciais de gestão para criar o objeto de Gestor de mapa de partições horizontais (usando **GetSqlShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)):

```java
// Obtain a shard map manager.
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmAdminConnectionString,ShardMapManagerLoadPolicy.Lazy);
```

A variável **smmAdminConnectionString** é uma cadeia de ligação que contém as credenciais de gestão. O ID de utilizador e palavra-passe fornecem acesso de leitura/escrita para a base de dados de mapas de partições horizontais e as partições horizontais individuais. A cadeia de ligação de gestão também inclui o nome do servidor e o nome de base de dados para identificar a base de dados do mapa de partições horizontais global. Esta é uma cadeia de ligação típica para essa finalidade:

```java
"Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;”
```

Não utilize valores na forma de "username@server" — em vez disso, utilize apenas o valor de "nomedeutilizador".  Isto acontece porque as credenciais devem trabalhar com a base de dados de Gestor de mapas de partições horizontais e de partições horizontais individuais, que podem ser em servidores diferentes.

## <a name="access-credentials"></a>Credenciais de acesso

Ao criar uma partição horizontal Gestor de mapas num aplicativo que não administrar mapas de partições horizontais, utilize as credenciais que têm permissões só de leitura no mapa de partições horizontais global. As informações obtidas do mapa de partições horizontais global com estas credenciais são utilizadas para [encaminhamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md) e para povoar a cache de mapa de partições horizontais no cliente. As credenciais são fornecidas através do mesmo padrão de chamada para **GetSqlShardMapManager**:

```java
// Obtain shard map manager.
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmReadOnlyConnectionString, ShardMapManagerLoadPolicy.Lazy);  
```

Observe o uso do **smmReadOnlyConnectionString** para refletir a utilização de credenciais diferentes para este acesso em nome de **não-administrador** utilizadores: estas credenciais não devem fornecer permissões de escrita no o mapa de partições horizontais global.

## <a name="connection-credentials"></a>Credenciais de ligação

Credenciais adicionais são necessários quando utilizar o **OpenConnectionForKey** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkey), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey)) método para aceder a uma partição horizontal associada com uma chave de fragmentação. Estas credenciais têm de fornecer permissões para acesso só de leitura para as tabelas de mapa de partições horizontais local que residem em partição horizontal. Isto é necessário para executar a validação de ligação de encaminhamento dependente de dados em partição horizontal. Este fragmento de código permite o acesso a dados no contexto de encaminhamento dependente de dados:

```csharp
using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>(targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate))
```

Neste exemplo, **smmUserConnectionString** contém a cadeia de ligação para as credenciais de utilizador. Para o Azure SQL DB, esta é uma cadeia de ligação típica para as credenciais de utilizador:

```java
"User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;”  
```

Como com as credenciais de administrador, não utilize valores na forma de "username@server". Em vez disso, simplesmente utilize "nomedeutilizador".  Observe também que a cadeia de ligação não contém um nome de servidor e nome de base de dados. Isso ocorre porque o **OpenConnectionForKey** chamada direciona automaticamente a ligação à partição horizontal correto com base na chave. Por conseguinte, o nome de base de dados e o nome do servidor não são fornecidos.

## <a name="see-also"></a>Consulte também

[Gerir bases de dados e inícios de sessão na Base de Dados SQL do Azure](sql-database-manage-logins.md)

[Securing your SQL Database (Proteger a sua Base de Dados SQL)](sql-database-security-overview.md)

[Tarefas elásticas da base de dados](elastic-jobs-overview.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
