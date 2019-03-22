---
title: Migrar bases de dados existentes para aumentar horizontalmente | Documentos da Microsoft
description: Converter bases de dados em partição horizontal para utilizar as ferramentas de bases de dados elásticas através da criação de uma partição horizontal Gestor de mapas
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
ms.date: 01/25/2019
ms.openlocfilehash: 49686e407b2d733c04bad31706c6c4f315bf28bf
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58008676"
---
# <a name="migrate-existing-databases-to-scale-out"></a>Migrar bases de dados existentes para aumentar horizontalmente
Gerencie facilmente sua aumentadas horizontalmente em partição horizontal bases de dados existentes com as ferramentas de base de dados de base de dados do Azure SQL (por exemplo, o [biblioteca de clientes de bases de dados elásticas](sql-database-elastic-database-client-library.md)). Comece por converter um conjunto existente de bases de dados para utilizar o [Gestor de mapas de partições horizontais](sql-database-elastic-scale-shard-map-management.md). 

## <a name="overview"></a>Descrição geral
Para migrar uma base de dados em partição horizontal existente: 

1. Preparar o [base de dados de Gestor de mapas de partições horizontais](sql-database-elastic-scale-shard-map-management.md).
2. Crie o mapa de partições horizontais.
3. Prepare-se as partições horizontais individuais.  
4. Adicione mapeamentos para o mapa de partições horizontais.

Essas técnicas podem ser implementadas através de um a [biblioteca de cliente do .NET Framework](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/), ou os scripts do PowerShell adrese [BD SQL do Azure - scripts de ferramentas de bases de dados elásticas](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db). Os exemplos aqui utilizam scripts do PowerShell.

Para obter mais informações sobre o ShardMapManager, consulte [gestão de mapas de partições horizontais](sql-database-elastic-scale-shard-map-management.md). Para uma descrição geral das ferramentas de bases de dados elásticas, consulte [visão geral dos recursos de bases de dados elásticas](sql-database-elastic-scale-introduction.md).

## <a name="prepare-the-shard-map-manager-database"></a>Preparar a base de dados de Gestor de mapas de partições horizontais
O Gestor de mapas de partições horizontais é uma base de dados especial que contém os dados para gerir bases de dados de escalamento horizontal. Pode utilizar uma base de dados existente ou criar uma nova base de dados. Uma base de dados que atua como Gestor de mapas de partições horizontais não deve ser o mesmo banco de dados como uma partição horizontal. O script do PowerShell não cria a base de dados para. 

## <a name="step-1-create-a-shard-map-manager"></a>Passo 1: criar uma partição horizontal Gestor de mapas
    # Create a shard map manager. 
    New-ShardMapManager -UserName '<user_name>' 
    -Password '<password>' 
    -SqlServerName '<server_name>' 
    -SqlDatabaseName '<smm_db_name>' 
    #<server_name> and <smm_db_name> are the server name and database name 
    # for the new or existing database that should be used for storing 
    # tenant-database mapping information.

### <a name="to-retrieve-the-shard-map-manager"></a>Para obter o Gestor de mapas de partições horizontais
Após a criação, pode obter o Gestor de mapas de partições horizontais com este cmdlet. Este passo é necessário sempre que precisa usar o objeto de ShardMapManager.

    # Try to get a reference to the Shard Map Manager  
    $ShardMapManager = Get-ShardMapManager -UserName '<user_name>' 
    -Password '<password>' 
    -SqlServerName '<server_name>' 
    -SqlDatabaseName '<smm_db_name>' 


## <a name="step-2-create-the-shard-map"></a>Passo 2: criar o mapa de partições horizontais
Selecione o tipo de mapa de partições horizontais para criar. A escolha depende da arquitetura de base de dados: 

1. Inquilino individual por base de dados (para termos, consulte a [glossário](sql-database-elastic-scale-glossary.md).) 
2. Vários inquilinos por base de dados (dois tipos):
   1. Mapeamento de lista
   2. Mapeamento de intervalo

Para um modelo do inquilino único, crie uma **mapeamento de lista** mapa de partições horizontais. O modelo do inquilino único atribui uma base de dados por inquilino. Este é um modelo eficaz para os programadores de SaaS, como ele simplifica a gestão.

![Mapeamento de lista][1]

O modelo do multi-inquilino atribui vários inquilinos para uma base de dados individual (e pode distribuir os grupos de inquilinos em várias bases de dados). Utilize este modelo quando esperar de cada inquilino ter necessidades de dados pequeno. Nesse modelo, atribuir um intervalo de inquilinos para uma base de dados com **mapeamento de intervalo**. 

![Mapeamento de intervalo][2]

Ou pode implementar um modelo de base de dados do multi-inquilino com um *mapeamento de lista* atribuir vários inquilinos para uma base de dados individual. Por exemplo, DB1 tem um é utilizado para armazenar informações sobre o inquilino do ID de 1 e 5 e DB2 armazena dados de inquilinos 7 e inquilinos 10. 

![Vários inquilinos numa único DB][3] 

**Com base na sua escolha, escolha uma destas opções:**

### <a name="option-1-create-a-shard-map-for-a-list-mapping"></a>Opção 1: criar um mapa de partições horizontais para um mapeamento de lista
Crie um mapa de partições horizontais usando o objeto de ShardMapManager. 

    # $ShardMapManager is the shard map manager object. 
    $ShardMap = New-ListShardMap -KeyType $([int]) 
    -ListShardMapName 'ListShardMap' 
    -ShardMapManager $ShardMapManager 


### <a name="option-2-create-a-shard-map-for-a-range-mapping"></a>Opção 2: criar um mapa de partições horizontais para um mapeamento de intervalo
Para utilizar este padrão de mapeamento, valores de ID de inquilino tem de ser intervalos contínuos, e é aceitável ter lacuna nos intervalos por ignorar o intervalo durante a criação de bases de dados.

    # $ShardMapManager is the shard map manager object 
    # 'RangeShardMap' is the unique identifier for the range shard map.  
    $ShardMap = New-RangeShardMap 
    -KeyType $([int]) 
    -RangeShardMapName 'RangeShardMap' 
    -ShardMapManager $ShardMapManager 

### <a name="option-3-list-mappings-on-an-individual-database"></a>Opção 3: Lista de mapeamentos numa base de dados individual
Também como configurar este padrão requer a criação de um mapa de lista, conforme mostrado no passo 2, a opção 1.

## <a name="step-3-prepare-individual-shards"></a>Passo 3: Preparar as partições horizontais individuais
Adicione cada partição horizontal (base de dados) para o Gestor de mapas de partições horizontais. Isto prepara as bases de dados individuais para armazenar informações de mapeamento. Execute esse método em cada partição horizontal.

    Add-Shard 
    -ShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>'
    # The $ShardMap is the shard map created in step 2.


## <a name="step-4-add-mappings"></a>Passo 4: Adicionar mapeamentos
A adição de mapeamentos depende do tipo de mapa de partições horizontais que criou. Se tiver criado um mapa de lista, adicione os mapeamentos de lista. Se tiver criado um mapa de intervalo, adicione os mapeamentos de intervalo.

### <a name="option-1-map-the-data-for-a-list-mapping"></a>Opção 1: mapear os dados para um mapeamento de lista
Mapeie os dados ao adicionar um mapeamento de lista para cada inquilino.  

    # Create the mappings and associate it with the new shards 
    Add-ListMapping 
    -KeyType $([int]) 
    -ListPoint '<tenant_id>' 
    -ListShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>' 

### <a name="option-2-map-the-data-for-a-range-mapping"></a>Opção 2: mapear os dados para um mapeamento de intervalo
Adicione os mapeamentos de intervalo para todos os inquilinos ID intervalo - associações de base de dados:

    # Create the mappings and associate it with the new shards 
    Add-RangeMapping 
    -KeyType $([int]) 
    -RangeHigh '5' 
    -RangeLow '1' 
    -RangeShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>' 


### <a name="step-4-option-3-map-the-data-for-multiple-tenants-on-an-individual-database"></a>Opção 3 do passo 4: mapear os dados de vários inquilinos numa base de dados individual
Para cada inquilino, execute o Add-ListMapping (opção 1). 

## <a name="checking-the-mappings"></a>A verificar os mapeamentos
Informações sobre as partições horizontais existentes e os mapeamentos associados a eles podem ser consultadas com os seguintes comandos:  

    # List the shards and mappings 
    Get-Shards -ShardMap $ShardMap 
    Get-Mappings -ShardMap $ShardMap 

## <a name="summary"></a>Resumo
Depois de concluir a configuração, pode começar a utilizar a biblioteca de clientes de bases de dados elásticas. Também pode utilizar [encaminhamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md) e [consultas de vários fragmentos](sql-database-elastic-scale-multishard-querying.md).

## <a name="next-steps"></a>Passos Seguintes
Obter os scripts do PowerShell da [scripts de ferramentas de base de dados do Azure SQL DB elástica](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

As ferramentas também estão disponíveis no GitHub: [Azure/elástico-db-tools](https://github.com/Azure/elastic-db-tools).

Utilize a ferramenta de dividir / unir para mover dados de ou para um modelo do multi-inquilino para um modelo de inquilino único. Ver [ferramenta de intercalação de divisão](sql-database-elastic-scale-get-started.md).

## <a name="additional-resources"></a>Recursos adicionais
Para obter informações sobre os padrões da arquitetura de dados comuns de aplicações de base de dados de software como um serviço (Saas) de multi-inquilino, consulte [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database (Padrões de estrutura para Aplicações SaaS Multi-inquilino com a Base de Dados SQL do Azure)](sql-database-design-patterns-multi-tenancy-saas-applications.md).

## <a name="questions-and-feature-requests"></a>Perguntas e pedidos de funcionalidades
Para fazer perguntas, use o [fórum de base de dados SQL](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) e para pedidos de funcionalidades, adicioná-los para o [fórum de comentários de base de dados SQL](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/sql-database-elastic-convert-to-use-elastic-tools/listmapping.png
[2]: ./media/sql-database-elastic-convert-to-use-elastic-tools/rangemapping.png
[3]: ./media/sql-database-elastic-convert-to-use-elastic-tools/multipleonsingledb.png

