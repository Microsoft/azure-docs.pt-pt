---
title: Migrar as bases de dados existentes para escalar
description: Converter bases de dados de fragmentos para utilizar ferramentas elásticas de base de dados criando um gestor de mapas de fragmentos
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
ms.openlocfilehash: 95afd0b9117caca4b531c453417217d8a8de7c7f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91443480"
---
# <a name="migrate-existing-databases-to-scale-out"></a>Migrar as bases de dados existentes para escalar
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Gerencie facilmente as bases de dados de fragmentos em escala existentes utilizando ferramentas (como a biblioteca de [clientes Elastic Database).](elastic-database-client-library.md) Primeiro converta um conjunto de bases de dados existente para utilizar o [gestor de mapas de fragmentos.](elastic-scale-shard-map-management.md)

## <a name="overview"></a>Descrição geral

Para migrar uma base de dados de fragmentos existente:

1. Prepare a [base de dados do gestor de mapas de fragmentos.](elastic-scale-shard-map-management.md)
2. Crie o mapa de fragmentos.
3. Prepare os fragmentos individuais.  
4. Adicione mapeamentos ao mapa de fragmentos.

Estas técnicas podem ser implementadas utilizando a biblioteca de [clientes .NET Framework,](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)ou os scripts PowerShell encontrados na [Base de Dados Azure SQL - Scripts de ferramentas de base de dados elásticas](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db). Os exemplos aqui usam os scripts PowerShell.

Para obter mais informações sobre o ShardMapManager, consulte [a gestão do mapa Shard.](elastic-scale-shard-map-management.md) Para obter uma visão geral das ferramentas Elastic Database, consulte a [visão geral das funcionalidades da Base de Dados Elástica](elastic-scale-introduction.md).

## <a name="prepare-the-shard-map-manager-database"></a>Preparar a base de dados do gestor de mapas de fragmentos

O gestor de mapas de fragmentos é uma base de dados especial que contém os dados para gerir bases de dados de escala. Pode utilizar uma base de dados existente ou criar uma nova base de dados. Uma base de dados que atua como gestor de mapas de fragmentos não deve ser a mesma base de dados que um fragmento. O script PowerShell não cria a base de dados para si.

## <a name="step-1-create-a-shard-map-manager"></a>Passo 1: Criar um gestor de mapas de fragmentos

```powershell
# Create a shard map manager
New-ShardMapManager -UserName '<user_name>' -Password '<password>' -SqlServerName '<server_name>' -SqlDatabaseName '<smm_db_name>'
#<server_name> and <smm_db_name> are the server name and database name
# for the new or existing database that should be used for storing
# tenant-database mapping information.
```

### <a name="to-retrieve-the-shard-map-manager"></a>Para recuperar o gestor de mapas de fragmentos

Após a criação, você pode recuperar o gestor de mapas de fragmentos com este cmdlet. Este passo é necessário sempre que precisar de usar o objeto ShardMapManager.

```powershell
# Try to get a reference to the Shard Map Manager  
$ShardMapManager = Get-ShardMapManager -UserName '<user_name>' -Password '<password>' -SqlServerName '<server_name>' -SqlDatabaseName '<smm_db_name>'
```

## <a name="step-2-create-the-shard-map"></a>Passo 2: Criar o mapa de fragmentos

Selecione o tipo de mapa de fragmentos para criar. A escolha depende da arquitetura da base de dados:

1. Único inquilino por base de dados (Para termos, consulte o [glossário](elastic-scale-glossary.md).)
2. Vários inquilinos por base de dados (dois tipos):
   1. Mapeamento de lista
   2. Mapeamento de gama

Para um modelo de inquilino único, crie uma lista de mapas de fragmentos de **mapeamento.** O modelo de inquilino único atribui uma base de dados por inquilino. Este é um modelo eficaz para os desenvolvedores do SaaS, uma vez que simplifica a gestão.

![Mapeamento de lista][1]

O modelo multi-inquilino atribui vários inquilinos a uma base de dados individual (e pode distribuir grupos de inquilinos através de várias bases de dados). Use este modelo quando espera que cada inquilino tenha pequenas necessidades de dados. Neste modelo, atribua uma gama de inquilinos a uma base de dados utilizando **mapeamento de gama.**

![Mapeamento de gama][2]

Ou pode implementar um modelo de base de dados multi-inquilino usando um *mapeamento de lista* para atribuir vários inquilinos a uma base de dados individual. Por exemplo, o DB1 é usado para armazenar informações sobre o ID 1 e 5 do inquilino, e dB2 armazena dados para inquilino 7 e inquilino 10.

![Vários inquilinos em DB único][3]

**Com base na sua escolha, escolha uma destas opções:**

### <a name="option-1-create-a-shard-map-for-a-list-mapping"></a>Opção 1: Criar um mapa de fragmentos para um mapeamento de lista

Crie um mapa de fragmentos utilizando o objeto ShardMapManager.

```powershell
# $ShardMapManager is the shard map manager object
$ShardMap = New-ListShardMap -KeyType $([int]) -ListShardMapName 'ListShardMap' -ShardMapManager $ShardMapManager
```

### <a name="option-2-create-a-shard-map-for-a-range-mapping"></a>Opção 2: Criar um mapa de fragmentos para um mapeamento de gama

Para utilizar este padrão de mapeamento, os valores de ID do inquilino precisam de ser gamas contínuas, e é aceitável ter lacunas nas gamas, ignorando o intervalo ao criar as bases de dados.

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

A adição de mapeamentos depende do tipo de mapa de fragmentos que criou. Se criou um mapa de listas, adiciona mapeamentos de listas. Se criou um mapa de alcance, adiciona mapeamentos de alcance.

### <a name="option-1-map-the-data-for-a-list-mapping"></a>Opção 1: Mapear os dados para um mapeamento de lista

Mapear os dados adicionando uma lista de mapeamento para cada inquilino.  

```powershell
# Create the mappings and associate it with the new shards
Add-ListMapping -KeyType $([int]) -ListPoint '<tenant_id>' -ListShardMap $ShardMap -SqlServerName '<shard_server_name>' -SqlDatabaseName '<shard_database_name>'
```

### <a name="option-2-map-the-data-for-a-range-mapping"></a>Opção 2: Mapear os dados para um mapeamento de gama

Adicione os mapeamentos de gama para toda a gama de ID do inquilino - associações de base de dados:

```powershell
# Create the mappings and associate it with the new shards
Add-RangeMapping -KeyType $([int]) -RangeHigh '5' -RangeLow '1' -RangeShardMap $ShardMap -SqlServerName '<shard_server_name>' -SqlDatabaseName '<shard_database_name>'
```

### <a name="step-4-option-3-map-the-data-for-multiple-tenants-on-an-individual-database"></a>Passo 4 opção 3: Mapear os dados para vários inquilinos numa base de dados individual

Para cada inquilino, executar o Add-ListMapping (opção 1).

## <a name="checking-the-mappings"></a>Verificação dos mapeamentos

As informações sobre os fragmentos existentes e os mapeamentos associados podem ser consultadas utilizando os seguintes comandos:  

```powershell
# List the shards and mappings
Get-Shards -ShardMap $ShardMap
Get-Mappings -ShardMap $ShardMap
```

## <a name="summary"></a>Resumo

Uma vez concluída a configuração, pode começar a utilizar a biblioteca de clientes Elastic Database. Também pode utilizar [o encaminhamento dependente de dados](elastic-scale-data-dependent-routing.md) e [consulta multi-fragmentos](elastic-scale-multishard-querying.md).

## <a name="next-steps"></a>Passos seguintes

Obtenha os scripts PowerShell da [Azure SQL Database-Elastic scripts de ferramentas de base de dados](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

As ferramentas também estão no GitHub: [Azure/elastic-db-tools](https://github.com/Azure/elastic-db-tools).

Utilize a ferramenta de fusão dividida para mover dados de ou de um modelo multi-inquilino para um único modelo de inquilino. Ver [ferramenta de fusão Split](elastic-scale-get-started.md).

## <a name="additional-resources"></a>Recursos adicionais

Para obter informações sobre os padrões da arquitetura de dados comuns de aplicações de base de dados de software como um serviço (Saas) de multi-inquilino, consulte [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database (Padrões de estrutura para Aplicações SaaS Multi-inquilino com a Base de Dados SQL do Azure)](saas-tenancy-app-design-patterns.md).

## <a name="questions-and-feature-requests"></a>Perguntas e pedidos de recursos

Para obter dúvidas, utilize a [página de perguntas do Microsoft Q&A para a Base de Dados SQL](https://docs.microsoft.com/answers/topics/azure-sql-database.html) e para pedidos de funcionalidades, adicione-as ao [fórum de feedback da Base de Dados SQL](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/elastic-convert-to-use-elastic-tools/listmapping.png
[2]: ./media/elastic-convert-to-use-elastic-tools/rangemapping.png
[3]: ./media/elastic-convert-to-use-elastic-tools/multipleonsingledb.png
