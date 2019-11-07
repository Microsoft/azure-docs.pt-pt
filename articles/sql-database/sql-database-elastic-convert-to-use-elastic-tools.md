---
title: Migrar bancos de dados existentes para escalar horizontalmente
description: Converter bancos de dados fragmentados para usar ferramentas de banco de dados elástico criando um Gerenciador de mapa de fragmentos
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
ms.openlocfilehash: b88c56872408a7ffe127a4e96d2313301d44c892
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73690542"
---
# <a name="migrate-existing-databases-to-scale-out"></a>Migrar bancos de dados existentes para escalar horizontalmente
Gerencie facilmente seus bancos de dados fragmentados existentes em escala horizontal usando as ferramentas de banco de dados do banco de dados SQL do Azure (como a [biblioteca de cliente do banco de dados elástico](sql-database-elastic-database-client-library.md)). Primeiro, converta um conjunto existente de bancos de dados para usar o [Gerenciador de mapa de fragmentos](sql-database-elastic-scale-shard-map-management.md). 

## <a name="overview"></a>Descrição geral
Para migrar um banco de dados fragmentado existente: 

1. Prepare o [banco de dados do Gerenciador de mapa de fragmentos](sql-database-elastic-scale-shard-map-management.md).
2. Crie o mapa de fragmentos.
3. Prepare os fragmentos individuais.  
4. Adicione mapeamentos ao mapa de fragmentos.

Essas técnicas podem ser implementadas usando a [biblioteca de cliente do .NET Framework](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)ou os scripts do PowerShell encontrados em [banco de dados SQL do Azure-scripts de ferramentas de banco de dados elástico](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db). Os exemplos aqui usam os scripts do PowerShell.

Para obter mais informações sobre o ShardMapManager, consulte [Gerenciamento de mapa de fragmentos](sql-database-elastic-scale-shard-map-management.md). Para obter uma visão geral das ferramentas de banco de dados elástico, consulte [visão geral dos recursos do banco de dados elástico](sql-database-elastic-scale-introduction.md).

## <a name="prepare-the-shard-map-manager-database"></a>Preparar o banco de dados do Gerenciador de mapa de fragmentos
O Gerenciador de mapa de fragmentos é um banco de dados especial que contém os dados a serem gerenciados. Você pode usar um banco de dados existente ou criar um novo banco de dados. Um banco de dados que atua como Gerenciador de mapa de fragmentos não deve ser o mesmo banco de dados que um fragmento. O script do PowerShell não cria o banco de dados para você. 

## <a name="step-1-create-a-shard-map-manager"></a>Etapa 1: criar um Gerenciador de mapa de fragmentos
    # Create a shard map manager. 
    New-ShardMapManager -UserName '<user_name>' 
    -Password '<password>' 
    -SqlServerName '<server_name>' 
    -SqlDatabaseName '<smm_db_name>' 
    #<server_name> and <smm_db_name> are the server name and database name 
    # for the new or existing database that should be used for storing 
    # tenant-database mapping information.

### <a name="to-retrieve-the-shard-map-manager"></a>Para recuperar o Gerenciador de mapa de fragmentos
Após a criação, você pode recuperar o Gerenciador de mapa de fragmentos com este cmdlet. Essa etapa é necessária toda vez que você precisar usar o objeto ShardMapManager.

    # Try to get a reference to the Shard Map Manager  
    $ShardMapManager = Get-ShardMapManager -UserName '<user_name>' 
    -Password '<password>' 
    -SqlServerName '<server_name>' 
    -SqlDatabaseName '<smm_db_name>' 


## <a name="step-2-create-the-shard-map"></a>Etapa 2: criar o mapa de fragmentos
Selecione o tipo de mapa de fragmentos a ser criado. A escolha depende da arquitetura do banco de dados: 

1. Locatário único por banco de dados (para termos, consulte o [Glossário](sql-database-elastic-scale-glossary.md)). 
2. Vários locatários por banco de dados (dois tipos):
   1. Mapeamento de lista
   2. Mapeamento de intervalo

Para um modelo de locatário único, crie um mapa de fragmento de **mapeamento de lista** . O modelo de locatário único atribui um banco de dados por locatário. Esse é um modelo eficaz para desenvolvedores de SaaS à medida que simplifica o gerenciamento.

![Mapeamento de lista][1]

O modelo multilocatário atribui vários locatários a um banco de dados individual (e você pode distribuir grupos de locatários em vários bancos de dados). Use esse modelo quando você espera que cada locatário tenha pequenas necessidades de dados. Nesse modelo, atribua um intervalo de locatários a um banco de dados usando o **mapeamento de intervalo**. 

![Mapeamento de intervalo][2]

Ou você pode implementar um modelo de banco de dados multilocatário usando um *mapeamento de lista* para atribuir vários locatários a um banco de dados individual. Por exemplo, DB1 é usado para armazenar informações sobre a ID de locatário 1 e 5, e o DB2 armazena dados para o locatário 7 e o locatário 10. 

![Vários locatários em um único banco de BD][3] 

**Com base em sua escolha, escolha uma destas opções:**

### <a name="option-1-create-a-shard-map-for-a-list-mapping"></a>Opção 1: criar um mapa de fragmentos para um mapeamento de lista
Crie um mapa de fragmentos usando o objeto ShardMapManager. 

    # $ShardMapManager is the shard map manager object. 
    $ShardMap = New-ListShardMap -KeyType $([int]) 
    -ListShardMapName 'ListShardMap' 
    -ShardMapManager $ShardMapManager 


### <a name="option-2-create-a-shard-map-for-a-range-mapping"></a>Opção 2: criar um mapa de fragmentos para um mapeamento de intervalo
Para utilizar esse padrão de mapeamento, os valores de ID de locatário precisam ser intervalos contínuos e é aceitável ter lacuna nos intervalos, ignorando o intervalo ao criar os bancos de dados.

    # $ShardMapManager is the shard map manager object 
    # 'RangeShardMap' is the unique identifier for the range shard map.  
    $ShardMap = New-RangeShardMap 
    -KeyType $([int]) 
    -RangeShardMapName 'RangeShardMap' 
    -ShardMapManager $ShardMapManager 

### <a name="option-3-list-mappings-on-an-individual-database"></a>Opção 3: listar mapeamentos em um banco de dados individual
A configuração desse padrão também requer a criação de um mapa de lista, conforme mostrado na etapa 2, opção 1.

## <a name="step-3-prepare-individual-shards"></a>Etapa 3: preparar fragmentos individuais
Adicione cada fragmento (banco de dados) ao Gerenciador de mapa de fragmentos. Isso prepara os bancos de dados individuais para armazenar informações de mapeamento. Execute este método em cada fragmento.

    Add-Shard 
    -ShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>'
    # The $ShardMap is the shard map created in step 2.


## <a name="step-4-add-mappings"></a>Etapa 4: Adicionar mapeamentos
A adição de mapeamentos depende do tipo de mapa de fragmentos que você criou. Se você criou um mapa de lista, você adiciona mapeamentos de lista. Se você criou um mapa de intervalo, você adiciona mapeamentos de intervalo.

### <a name="option-1-map-the-data-for-a-list-mapping"></a>Opção 1: mapear os dados para um mapeamento de lista
Mapeie os dados adicionando um mapeamento de lista para cada locatário.  

    # Create the mappings and associate it with the new shards 
    Add-ListMapping 
    -KeyType $([int]) 
    -ListPoint '<tenant_id>' 
    -ListShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>' 

### <a name="option-2-map-the-data-for-a-range-mapping"></a>Opção 2: mapear os dados para um mapeamento de intervalo
Adicione os mapeamentos de intervalo para todas as associações de banco de dados de intervalo de ID de locatário:

    # Create the mappings and associate it with the new shards 
    Add-RangeMapping 
    -KeyType $([int]) 
    -RangeHigh '5' 
    -RangeLow '1' 
    -RangeShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>' 


### <a name="step-4-option-3-map-the-data-for-multiple-tenants-on-an-individual-database"></a>Etapa 4 Option 3: mapear os dados para vários locatários em um banco de dados individual
Para cada locatário, execute o Add-ListMapping (opção 1). 

## <a name="checking-the-mappings"></a>Verificando os mapeamentos
As informações sobre os fragmentos existentes e os mapeamentos associados a eles podem ser consultadas usando os seguintes comandos:  

    # List the shards and mappings 
    Get-Shards -ShardMap $ShardMap 
    Get-Mappings -ShardMap $ShardMap 

## <a name="summary"></a>Resumo
Depois de concluir a instalação, você pode começar a usar a biblioteca de cliente do banco de dados elástico. Você também pode usar o [Roteamento Dependente de dados](sql-database-elastic-scale-data-dependent-routing.md) e [a consulta de vários fragmentos](sql-database-elastic-scale-multishard-querying.md).

## <a name="next-steps"></a>Passos seguintes
Obtenha os scripts do PowerShell do [banco de dados SQL do Azure-scripts de ferramentas de banco de dados elástico](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

As ferramentas também estão no GitHub: [Azure/elástico-DB-Tools](https://github.com/Azure/elastic-db-tools).

Use a ferramenta de divisão/mesclagem para mover dados de ou para um modelo multilocatário para um único modelo de locatário. Consulte [ferramenta de divisão e mesclagem](sql-database-elastic-scale-get-started.md).

## <a name="additional-resources"></a>Recursos adicionais
Para obter informações sobre os padrões da arquitetura de dados comuns de aplicações de base de dados de software como um serviço (Saas) de multi-inquilino, consulte [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database (Padrões de estrutura para Aplicações SaaS Multi-inquilino com a Base de Dados SQL do Azure)](sql-database-design-patterns-multi-tenancy-saas-applications.md).

## <a name="questions-and-feature-requests"></a>Perguntas e solicitações de recursos
Para perguntas, use o [Fórum do banco de dados SQL](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) e as solicitações de recursos, adicione-os ao [Fórum de comentários do banco de dados SQL](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/sql-database-elastic-convert-to-use-elastic-tools/listmapping.png
[2]: ./media/sql-database-elastic-convert-to-use-elastic-tools/rangemapping.png
[3]: ./media/sql-database-elastic-convert-to-use-elastic-tools/multipleonsingledb.png

