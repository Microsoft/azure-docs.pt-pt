---
title: Gestão de credenciais na biblioteca de clientes de base de dados elástica
description: Como definir o nível certo de credenciais, administrador para apenas leitura, para aplicações de base de dados elásticas
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: 51439edd1d8c7094a5b857821f632ace9e2dea53
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91442771"
---
# <a name="credentials-used-to-access-the-elastic-database-client-library"></a>Credenciais utilizadas para aceder à biblioteca de clientes elastic Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A [biblioteca de clientes Elastic Database](elastic-database-client-library.md) utiliza três tipos diferentes de credenciais para aceder ao gestor de [mapas de fragmentos.](elastic-scale-shard-map-management.md) Dependendo da necessidade, utilize a credencial com o nível de acesso mais baixo possível.

* **Credenciais de gestão:** para criar ou manipular um gestor de mapas de fragmentos. (Ver o [glossário](elastic-scale-glossary.md).)
* **Credenciais de acesso:** aceder a um gestor de mapas de fragmentos existente para obter informações sobre fragmentos.
* **Credenciais de ligação:** para ligar aos fragmentos.

Consulte também [bases de dados e logins na Base de Dados Azure SQL](logins-create-manage.md).

## <a name="about-management-credentials"></a>Sobre credenciais de gestão

As credenciais de gestão são usadas para criar um objeto **ShardMapManager** [(Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)para aplicações que manipulam mapas de fragmentos. (Por exemplo, ver [Adicionar um fragmento utilizando ferramentas elásticas de base de dados](elastic-scale-add-a-shard.md) e [encaminhamento dependente de dados).](elastic-scale-data-dependent-routing.md) O utilizador da biblioteca de clientes de escala elástica cria os utilizadores SQL e os logins SQL e garante que cada um recebe as permissões de leitura/escrita na base de dados global de mapas de fragmentos e em todas as bases de dados de fragmentos também. Estas credenciais são usadas para manter o mapa global de fragmentos e os mapas de fragmentos locais quando são realizadas alterações ao mapa de fragmentos. Por exemplo, utilize as credenciais de gestão para criar o objeto de gestor de mapas de fragmentos (utilizando **o GetSqlShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)):

```java
// Obtain a shard map manager.
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmAdminConnectionString,ShardMapManagerLoadPolicy.Lazy);
```

O **smmAdminConnectionS** variável é uma cadeia de conexão que contém as credenciais de gestão. O ID do utilizador e a palavra-passe fornecem acesso de leitura/escrita tanto à base de dados de mapas de fragmentos como aos fragmentos individuais. A cadeia de ligação de gestão também inclui o nome do servidor e o nome da base de dados para identificar a base de dados global de mapas de fragmentos. Aqui está uma cadeia de ligação típica para o efeito:

```java
"Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;”
```

Não utilize valores sob a forma de username@server " — em vez disso, utilize apenas o valor "username".  Isto porque as credenciais devem funcionar contra a base de dados do gestor de mapas de fragmentos e fragmentos individuais, que podem estar em diferentes servidores.

## <a name="access-credentials"></a>Credenciais de acesso

Ao criar um gestor de mapas de fragmentos numa aplicação que não administra mapas de fragmentos, use credenciais que tenham permissões apenas de leitura no mapa global de fragmentos. A informação obtida a partir do mapa global de fragmentos sob estas credenciais é usada para [encaminhamento dependente de dados](elastic-scale-data-dependent-routing.md) e para preencher a cache de mapa de fragmentos no cliente. As credenciais são fornecidas através do mesmo padrão de chamada para **GetSqlShardMapManager:**

```java
// Obtain shard map manager.
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmReadOnlyConnectionString, ShardMapManagerLoadPolicy.Lazy);  
```

Note a utilização **do smmReadOnlyConnectionString** para refletir a utilização de diferentes credenciais para este acesso em nome de utilizadores **não administrativos:** estas credenciais não devem fornecer permissões de escrita no mapa global de fragmentos.

## <a name="connection-credentials"></a>Credenciais de ligação

São necessárias credenciais adicionais quando se utiliza o método **OpenConnectionForKey** [(Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkey), [.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey)para aceder a um fragmento associado a uma chave de fragmentos. Estas credenciais precisam de fornecer permissões para acesso apenas de leitura às tabelas de mapas de fragmentos locais que residem no fragmento. Isto é necessário para efetuar a validação da ligação para o encaminhamento dependente de dados no fragmento. Este código de corte permite o acesso dos dados no contexto do encaminhamento dependente de dados:

```csharp
using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>(targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate))
```

Neste exemplo, **o SMMUserConnectionString** detém a cadeia de ligação para as credenciais do utilizador. Para a Base de Dados Azure SQL, aqui está uma cadeia de ligação típica para credenciais de utilizador:

```java
"User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;”  
```

Tal como acontece com as credenciais de administração, não utilize valores sob a forma de " username@server ". Em vez disso, basta usar "username".  Note também que a cadeia de ligação não contém um nome de servidor e nome de base de dados. Isto porque a chamada **OpenConnectionForKey** direciona automaticamente a ligação para o fragmento correto com base na tecla. Assim, o nome da base de dados e o nome do servidor não são fornecidos.

## <a name="see-also"></a>Consulte também

[Gerir bases de dados e inícios de sessão na Base de Dados SQL do Azure](logins-create-manage.md)

[Proteger a sua Base de Dados SQL](security-overview.md)

[Trabalhos de base de dados elásticas](elastic-jobs-overview.md)

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]
