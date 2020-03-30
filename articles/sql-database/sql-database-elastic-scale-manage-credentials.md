---
title: Gestão de credenciais na biblioteca de clientes de base de dados elástica
description: Como definir o nível certo de credenciais, administrador apenas para leitura, para aplicações de base de dados elásticas
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
ms.openlocfilehash: 91689a32a128584aade8081905e3d1aa3ecb0a97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823567"
---
# <a name="credentials-used-to-access-the-elastic-database-client-library"></a>Credenciais usadas para aceder à biblioteca de clientes da Base de Dados Elástica

A biblioteca de [clientes da Base](sql-database-elastic-database-client-library.md) de Dados Elástica usa três tipos diferentes de credenciais para aceder ao gestor de mapas de [fragmentos.](sql-database-elastic-scale-shard-map-management.md) Dependendo da necessidade, utilize a credencial com o nível mais baixo de acesso possível.

* **Credenciais de gestão:** para criar ou manipular um gestor de mapas de fragmentos. (Ver o [glossário](sql-database-elastic-scale-glossary.md).)
* **Credenciais de acesso:** aceder a um gestor de mapas de fragmentos existente para obter informações sobre fragmentos.
* **Credenciais de ligação:** para ligar aos fragmentos.

Consulte também a gestão de bases de [dados e logins na Base de Dados Azure SQL](sql-database-manage-logins.md).

## <a name="about-management-credentials"></a>Sobre credenciais de gestão

As credenciais de gestão são usadas para criar um objeto **ShardMapManager** [(Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)) para aplicações que manipulam mapas de fragmentos. (Por exemplo, ver [Adicionar um fragmento utilizando ferramentas de base](sql-database-elastic-scale-add-a-shard.md) de dados elásticas e [encaminhamento dependente de dados).](sql-database-elastic-scale-data-dependent-routing.md) O utilizador da biblioteca de clientes em escala elástica cria os utilizadores SQL e logins SQL e certifica-se de que cada um recebe permissões de leitura/escrita na base de dados global de mapas de fragmentos e em todas as bases de dados de fragmentos também. Estas credenciais são usadas para manter o mapa global do fragmento e os mapas locais quando são realizadas alterações ao mapa do fragmento. Por exemplo, utilize as credenciais de gestão para criar o objeto gestor de mapas de fragmentos (utilizando **GetSqlShardMapManager** [(Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.NET):](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)

```java
// Obtain a shard map manager.
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmAdminConnectionString,ShardMapManagerLoadPolicy.Lazy);
```

O **smmAdminConnectionString** variável é uma cadeia de ligação que contém as credenciais de gestão. O ID do utilizador e a palavra-passe fornecem acesso de leitura/escrita tanto à base de dados do mapa de fragmentos como aos fragmentos individuais. A cadeia de ligação de gestão também inclui o nome do servidor e nome da base de dados para identificar a base de dados global de mapas de fragmentos. Aqui está uma cadeia de ligação típica para o efeito:

```java
"Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;”
```

Não utilize valores sobusername@servera forma de " — em vez disso, utilize apenas o valor do "nome de utilizador".  Isto porque as credenciais devem funcionar contra a base de dados do gestor de mapas de fragmentos e fragmentos individuais, que podem estar em servidores diferentes.

## <a name="access-credentials"></a>Credenciais de acesso

Ao criar um gestor de mapas de fragmentos numa aplicação que não administra mapas de fragmentos, use credenciais que tenham apenas permissões de leitura no mapa global do fragmento. A informação obtida a partir do mapa global de fragmentos sob estas credenciais é usada para [encaminhamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md) e para povoar a cache do mapa de fragmentos no cliente. As credenciais são fornecidas através do mesmo padrão de chamada para **GetSqlShardMapManager:**

```java
// Obtain shard map manager.
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmReadOnlyConnectionString, ShardMapManagerLoadPolicy.Lazy);  
```

Note a utilização do **smmReadOnlyConnectionString** para refletir a utilização de diferentes credenciais para este acesso em nome de utilizadores **não administrativos:** estas credenciais não devem fornecer permissões de escrita no mapa global do fragmento.

## <a name="connection-credentials"></a>Credenciais de ligação

São necessárias credenciais adicionais ao utilizar o método **OpenConnectionForKey** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkey), [.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey)para aceder a um fragmento associado a uma chave de sharding. Estas credenciais precisam de fornecer permissões para o acesso apenas de leitura às tabelas de mapas locais que residem no fragmento. Isto é necessário para realizar a validação da ligação para o encaminhamento dependente de dados no fragmento. Este código permite o acesso de dados no contexto do encaminhamento dependente de dados:

```csharp
using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>(targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate))
```

Neste exemplo, o **smmUserConnectionString** detém a cadeia de ligação para as credenciais do utilizador. Para o Azure SQL DB, aqui está uma cadeia de ligação típica para credenciais de utilizador:

```java
"User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;”  
```

Tal como acontece com as credenciais de administração, não utilize valores sob a forma de "username@server." Em vez disso, utilize apenas "username".  Note também que a cadeia de ligação não contém um nome de servidor e nome de base de dados. Isto porque a chamada **OpenConnectionForKey** direciona automaticamente a ligação ao fragmento correto com base na chave. Por conseguinte, o nome da base de dados e o nome do servidor não são fornecidos.

## <a name="see-also"></a>Consulte também

[Gerir bases de dados e inícios de sessão na Base de Dados SQL do Azure](sql-database-manage-logins.md)

[Proteger a sua Base de Dados SQL](sql-database-security-overview.md)

[Trabalhos de base de dados elásticos](elastic-jobs-overview.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
