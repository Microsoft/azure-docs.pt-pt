---
title: Migrar bases de dados existentes para escalar Microsoft Docs
description: Converter bases de dados esforçadas para utilizar ferramentas de base de dados elásticas criando um gestor de mapas de fragmentos
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
ms.openlocfilehash: 9c47eebc965319575020eace87825af9d9ad5a18
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835024"
---
# <a name="migrate-existing-databases-to-scale-out"></a>Migrar bases de dados existentes para escalar

Gerencie facilmente as bases de dados existentes com bases de dados de base de dados Azure SQL (como a biblioteca de clientes da Base de [Dados Elástica).](sql-database-elastic-database-client-library.md) Primeiro converta um conjunto de bases de dados existentepara utilizar o gestor de mapas de [fragmentos](sql-database-elastic-scale-shard-map-management.md).

## <a name="overview"></a>Descrição geral

Para migrar uma base de dados existente:

1. Prepare a base de dados do gestor de [mapas de fragmentos.](sql-database-elastic-scale-shard-map-management.md)
2. Crie o mapa do fragmento.
3. Prepare os fragmentos individuais.  
4. Adicione mapeamentos ao mapa do fragmento.

Estas técnicas podem ser implementadas utilizando a biblioteca de [clientes .NET Framework,](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)ou os scripts PowerShell encontrados nos [scripts de ferramentas Azure SQL DB - Elásticos Base de Dados](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db). Os exemplos aqui usam os scripts PowerShell.

Para mais informações sobre o ShardMapManager, consulte a gestão do [mapa de Shard](sql-database-elastic-scale-shard-map-management.md). Para obter uma visão geral das ferramentas de base de dados elásticas, consulte a visão geral da Base de [Dados Elástica](sql-database-elastic-scale-introduction.md).

## <a name="prepare-the-shard-map-manager-database"></a>Prepare a base de dados do gestor de mapas de fragmentos

O gestor de mapas de fragmentos é uma base de dados especial que contém os dados para gerir bases de dados dimensionadas. Pode utilizar uma base de dados existente ou criar uma nova base de dados. Uma base de dados que atua como gestor de mapas não deve ser a mesma base de dados que um fragmento. O script PowerShell não cria a base de dados para si.

## <a name="step-1-create-a-shard-map-manager"></a>Passo 1: criar um gestor de mapas de fragmentos

```powershell
# Create a shard map manager
New-ShardMapManager -UserName '<user_name>' -Password '<password>' -SqlServerName '<server_name>' -SqlDatabaseName '<smm_db_name>'
#<server_name> and <smm_db_name> are the server name and database name
# for the new or existing database that should be used for storing
# tenant-database mapping information.
```

### <a name="to-retrieve-the-shard-map-manager"></a>Para recuperar o gestor de mapas de fragmentos

Após a criação, você pode recuperar o gestor de mapa de fragmentos com este cmdlet. Este passo é necessário sempre que precisa de usar o objeto ShardMapManager.

```powershell
# Try to get a reference to the Shard Map Manager  
$ShardMapManager = Get-ShardMapManager -UserName '<user_name>' -Password '<password>' -SqlServerName '<server_name>' -SqlDatabaseName '<smm_db_name>'
```

## <a name="step-2-create-the-shard-map"></a>Passo 2: criar o mapa do fragmento

Selecione o tipo de mapa de fragmentos para criar. A escolha depende da arquitetura da base de dados:

1. Inquilino único por base de dados (Para termos, consulte o [glossário](sql-database-elastic-scale-glossary.md).)
2. Vários inquilinos por base de dados (dois tipos):
   1. Mapeamento de listas
   2. Mapeamento de alcance

Para um modelo de inquilino único, crie um mapa de **mapeamento de lista.** O modelo de inquilino único atribui uma base de dados por inquilino. Este é um modelo eficaz para os desenvolvedores da SaaS, uma vez que simplifica a gestão.

![Mapeamento de listas][1]

O modelo multi-inquilino atribui vários inquilinos a uma base de dados individual (e pode distribuir grupos de inquilinos através de várias bases de dados). Use este modelo quando espera que cada inquilino tenha pequenas necessidades de dados. Neste modelo, atribua uma gama de inquilinos a uma base de dados utilizando mapeamento de **alcance.**

![Mapeamento de alcance][2]

Ou pode implementar um modelo de base de dados multi-inquilinos usando um *mapeamento* de lista para atribuir vários inquilinos a uma base de dados individual. Por exemplo, db1 é usado para armazenar informações sobre o id 1 e 5 do inquilino, e DB2 armazena dados para inquilino 7 e inquilino 10.

![Vários inquilinos em db único][3]

**Com base na sua escolha, escolha uma destas opções:**

### <a name="option-1-create-a-shard-map-for-a-list-mapping"></a>Opção 1: criar um mapa de fragmentos para um mapeamento de lista

Crie um mapa de fragmentos utilizando o objeto ShardMapManager.

```powershell
# $ShardMapManager is the shard map manager object
$ShardMap = New-ListShardMap -KeyType $([int]) -ListShardMapName 'ListShardMap' -ShardMapManager $ShardMapManager
```

### <a name="option-2-create-a-shard-map-for-a-range-mapping"></a>Opção 2: criar um mapa de fragmentos para um mapeamento de alcance

Para utilizar este padrão de mapeamento, os valores de ID do inquilino precisam de ser gamas contínuas, e é aceitável ter lacunas nas gamas, ignorando a gama ao criar as bases de dados.

```powershell
# $ShardMapManager is the shard map manager object
# 'RangeShardMap' is the unique identifier for the range shard map.  
$ShardMap = New-RangeShardMap -KeyType $([int]) -RangeShardMapName 'RangeShardMap' -ShardMapManager $ShardMapManager
```

### <a name="option-3-list-mappings-on-an-individual-database"></a>Opção 3: Lista de mapeamentos numa base de dados individual

A configuração deste padrão também requer a criação de um mapa de listas, como mostrado no passo 2, opção 1.

## <a name="step-3-prepare-individual-shards"></a>Passo 3: Preparar fragmentos individuais

Adicione cada fragmento (base de dados) ao gestor de mapas de fragmentos. Isto prepara as bases de dados individuais para armazenar informações de mapeamento. Execute este método em cada fragmento.

```powershell
Add-Shard -ShardMap $ShardMap -SqlServerName '<shard_server_name>' -SqlDatabaseName '<shard_database_name>'
# The $ShardMap is the shard map created in step 2.
```

## <a name="step-4-add-mappings"></a>Passo 4: Adicionar mapeamentos

A adição de mapeamentos depende do tipo de mapa de fragmentos que criou. Se criou um mapa de listas, adicione mapeamentos de listas. Se criou um mapa de alcance, adicione mapeamentos de alcance.

### <a name="option-1-map-the-data-for-a-list-mapping"></a>Opção 1: mapear os dados para um mapeamento de lista

Mapeie os dados adicionando um mapeamento de lista para cada inquilino.  

```powershell
# Create the mappings and associate it with the new shards
Add-ListMapping -KeyType $([int]) -ListPoint '<tenant_id>' -ListShardMap $ShardMap -SqlServerName '<shard_server_name>' -SqlDatabaseName '<shard_database_name>'
```

### <a name="option-2-map-the-data-for-a-range-mapping"></a>Opção 2: mapear os dados para um mapeamento de alcance

Adicione os mapeamentos de gama para toda a gama de ID do inquilino - associações de bases de dados:

```powershell
# Create the mappings and associate it with the new shards
Add-RangeMapping -KeyType $([int]) -RangeHigh '5' -RangeLow '1' -RangeShardMap $ShardMap -SqlServerName '<shard_server_name>' -SqlDatabaseName '<shard_database_name>'
```

### <a name="step-4-option-3-map-the-data-for-multiple-tenants-on-an-individual-database"></a>Opção Passo 4 3: mapear os dados para vários inquilinos numa base de dados individual

Para cada inquilino, execute o Add-ListMapping (opção 1).

## <a name="checking-the-mappings"></a>Verificando os mapeamentos

As informações sobre os fragmentos existentes e os mapeamentos associados a eles podem ser consultadas utilizando os seguintes comandos:  

```powershell
# List the shards and mappings
Get-Shards -ShardMap $ShardMap
Get-Mappings -ShardMap $ShardMap
```

## <a name="summary"></a>Resumo

Uma vez concluída a configuração, pode começar a utilizar a biblioteca de clientes da Base de Dados Elástica. Também pode utilizar [o encaminhamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md) e a consulta [multi-fragmento](sql-database-elastic-scale-multishard-querying.md).

## <a name="next-steps"></a>Passos seguintes

Obtenha os scripts PowerShell a partir de scripts de ferramentas de [base de dados Db-ElásticaS Azure SQL](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

As ferramentas também estão no GitHub: [Azure/elastic-db-tools](https://github.com/Azure/elastic-db-tools).

Utilize a ferramenta de fusão de divisão para mover dados de ou para um modelo multi-inquilino para um modelo único de inquilino. Consulte [a ferramenta de fusão Split](sql-database-elastic-scale-get-started.md).

## <a name="additional-resources"></a>Recursos adicionais

Para obter informações sobre os padrões da arquitetura de dados comuns de aplicações de base de dados de software como um serviço (Saas) de multi-inquilino, consulte [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database (Padrões de estrutura para Aplicações SaaS Multi-inquilino com a Base de Dados SQL do Azure)](sql-database-design-patterns-multi-tenancy-saas-applications.md).

## <a name="questions-and-feature-requests"></a>Perguntas e Pedidos de Recursos

Para perguntas, utilize a página de perguntas do Microsoft Q&A para a Base de [Dados SQL](https://docs.microsoft.com/answers/topics/azure-sql-database.html) e para pedidos de funcionalidades, adicione-as ao fórum de feedback da Base de [Dados SQL](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/sql-database-elastic-convert-to-use-elastic-tools/listmapping.png
[2]: ./media/sql-database-elastic-convert-to-use-elastic-tools/rangemapping.png
[3]: ./media/sql-database-elastic-convert-to-use-elastic-tools/multipleonsingledb.png
