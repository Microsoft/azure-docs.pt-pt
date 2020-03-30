---
title: Reportagem através de bases de dados de nuvem escaldada
description: como configurar consultas elásticas sobre divisórias horizontais
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 01/03/2019
ms.openlocfilehash: 79abaade22fc107fa4c848607ff48232eeeb58ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823766"
---
# <a name="reporting-across-scaled-out-cloud-databases-preview"></a>Reportagem através de bases de dados de nuvem escalada (pré-visualização)

![Consulta através de fragmentos][1]

As bases de dados estampadas distribuem linhas através de um nível de dados escalado. O esquema é idêntico em todas as bases de dados participantes, também conhecidas como partição horizontal. Utilizando uma consulta elástica, pode criar relatórios que abrangem todas as bases de dados numa base de dados espumosa.

Para um início rápido, consulte relatórios através de [bases de dados de nuvem escaldada](sql-database-elastic-query-getting-started.md).

Para bases de dados não esfarditas, consulte a Consulta através de bases de [dados em nuvem com diferentes esquemas](sql-database-elastic-query-vertical-partitioning.md).

## <a name="prerequisites"></a>Pré-requisitos

* Crie um mapa de fragmentos utilizando a biblioteca de clientes de base de dados elástica. ver gestão de [mapas shard](sql-database-elastic-scale-shard-map-management.md). Ou use a aplicação de amostra [sintetizada com ferramentas](sql-database-elastic-scale-get-started.md)de base de dados elásticas.
* Em alternativa, consulte as bases de dados existentes para as [bases de dados dimensionadas](sql-database-elastic-convert-to-use-elastic-tools.md).
* O utilizador deve possuir qualquer permissão DE DADOS EXTERNA FONTE. Esta permissão está incluída com a permissão alter DATABASE.
* ALTERE quaisquer permissões externas de FONTE DE DADOS são necessárias para se referir à fonte de dados subjacente.

## <a name="overview"></a>Descrição geral

Estas declarações criam a representação de metadados do seu nível de dados fragmentos na base de dados de consultas elásticas.

1. [CRIAR CHAVE MESTRE](https://msdn.microsoft.com/library/ms174382.aspx)
2. [CRIAR BASE DE DADOS CREDENCIAL SCOPED](https://msdn.microsoft.com/library/mt270260.aspx)
3. [CRIAR FONTE DE DADOS EXTERNAS](https://msdn.microsoft.com/library/dn935022.aspx)
4. [CRIAR TABELA EXTERNA](https://msdn.microsoft.com/library/dn935021.aspx)

## <a name="11-create-database-scoped-master-key-and-credentials"></a>1.1 Criar chave-mestra e credenciais de base de dados

A credencial é utilizada pela consulta elástica para se ligar às suas bases de dados remotas.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]

> [!NOTE]
> Certifique-se de que o *"\<nome\>de utilizador "* não inclui qualquer sufixo de *"nome\@de servidor".*

## <a name="12-create-external-data-sources"></a>1.2 Criar fontes de dados externas

Sintaxe:

    <External_Data_Source> ::=
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH
            (TYPE = SHARD_MAP_MANAGER,
                       LOCATION = '<fully_qualified_server_name>',
            DATABASE_NAME = ‘<shardmap_database_name>',
            CREDENTIAL = <credential_name>,
            SHARD_MAP_NAME = ‘<shardmapname>’
                   ) [;]

### <a name="example"></a>Exemplo

    CREATE EXTERNAL DATA SOURCE MyExtSrc
    WITH
    (
        TYPE=SHARD_MAP_MANAGER,
        LOCATION='myserver.database.windows.net',
        DATABASE_NAME='ShardMapDatabase',
        CREDENTIAL= SMMUser,
        SHARD_MAP_NAME='ShardMap'
    );

Recuperar a lista das fontes de dados externas atuais:

    select * from sys.external_data_sources;

A fonte de dados externa refere o seu mapa de fragmentos. Uma consulta elástica utiliza então a fonte de dados externa e o mapa de fragmentos subjacente para enumerar as bases de dados que participam no nível de dados.
As mesmas credenciais são usadas para ler o mapa do fragmento e aceder aos dados sobre os fragmentos durante o processamento de uma consulta elástica.

## <a name="13-create-external-tables"></a>1.3 Criar tabelas externas

Sintaxe:  

    CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name  
        ( { <column_definition> } [ ,...n ])
        { WITH ( <sharded_external_table_options> ) }
    ) [;]  

    <sharded_external_table_options> ::=
      DATA_SOURCE = <External_Data_Source>,
      [ SCHEMA_NAME = N'nonescaped_schema_name',]
      [ OBJECT_NAME = N'nonescaped_object_name',]
      DISTRIBUTION = SHARDED(<sharding_column_name>) | REPLICATED |ROUND_ROBIN

**Exemplo**

    CREATE EXTERNAL TABLE [dbo].[order_line](
         [ol_o_id] int NOT NULL,
         [ol_d_id] tinyint NOT NULL,
         [ol_w_id] int NOT NULL,
         [ol_number] tinyint NOT NULL,
         [ol_i_id] int NOT NULL,
         [ol_delivery_d] datetime NOT NULL,
         [ol_amount] smallmoney NOT NULL,
         [ol_supply_w_id] int NOT NULL,
         [ol_quantity] smallint NOT NULL,
         [ol_dist_info] char(24) NOT NULL
    )

    WITH
    (
        DATA_SOURCE = MyExtSrc,
         SCHEMA_NAME = 'orders',
         OBJECT_NAME = 'order_details',
        DISTRIBUTION=SHARDED(ol_w_id)
    );

Recuperar a lista de tabelas externas da base de dados atual:

    SELECT * from sys.external_tables;

Para deixar cair as tabelas externas:

    DROP EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name[;]

### <a name="remarks"></a>Observações

A\_cláusula FONTE DE DADOS define a fonte de dados externa (um mapa de fragmentos) que é utilizada para a tabela externa.  

As cláusulas\_SCHEMA NAME e OBJECT\_NAME mapeiam a definição de tabela externa para uma tabela num esquema diferente. Se omitido, presume-se que o esquema do objeto remoto é "dbo" e o seu nome é assumido como idêntico ao nome de tabela externo que está a ser definido. Isto é útil se o nome da sua tabela remota já estiver na base de dados onde pretende criar a tabela externa. Por exemplo, pretende definir uma tabela externa para obter uma visão agregada das vistas do catálogo ou dMVs no seu nível de dados escalonado. Uma vez que as vistas do catálogo e os DMVs já existem localmente, não é possível utilizar os seus nomes para a definição de tabela externa. Em vez disso, utilize um nome diferente e utilize o nome da vista\_de catálogo\_ou o nome do DMV nas cláusulas SCHEMA NAME e/ou OBJECT NAME. (Ver o exemplo abaixo.)

A cláusula DE DISTRIBUIÇÃO especifica a distribuição de dados utilizada para esta tabela. O processador de consulta utiliza as informações fornecidas na cláusula DISTRIBUTION para construir os planos de consulta mais eficientes.

1. **SHARDED** significa que os dados são horizontalmente divididos através das bases de dados. A chave de divisão para a distribuição de dados é o **<sharding_column_name>** parâmetro.
2. **Replicado** significa que cópias idênticas da tabela estão presentes em cada base de dados. É da sua responsabilidade garantir que as réplicas são idênticas nas bases de dados.
3. **ROUND\_ROBIN** significa que a tabela é horizontalmente dividida utilizando um método de distribuição dependente da aplicação.

**Referência do nível**de dados : A tabela externa DDL refere-se a uma fonte externa de dados. A fonte de dados externa especifica um mapa de fragmentos que fornece à tabela externa as informações necessárias para localizar todas as bases de dados no seu nível de dados.

### <a name="security-considerations"></a>Considerações de segurança

Os utilizadores com acesso à tabela externa obtêm automaticamente acesso às tabelas remotas subjacentes sob a credencial dada na definição externa de fonte de dados. Evite a elevação indesejada dos privilégios através da credencial da fonte de dados externa. Utilize o GRANT ou o REVOGATO para uma mesa externa, tal como se fosse uma tabela regular.  

Depois de definir a sua fonte de dados externa e as suas tabelas externas, pode agora utilizar o T-SQL completo sobre as suas tabelas externas.

## <a name="example-querying-horizontal-partitioned-databases"></a>Exemplo: consulta de bases de dados horizontais divididas

A seguinte consulta realiza uma ligação tridireccional entre armazéns, encomendas e linhas de encomendas e utiliza vários agregados e um filtro seletivo. Assume (1) divisória horizontal (sharding) e (2) que os armazéns, encomendas e linhas de ordem são esfumaçados pela coluna id do armazém, e que a consulta elástica pode co-localizar as juntas nos fragmentos e processar a parte cara da consulta nos fragmentos em paralelo.

```sql
    select  
         w_id as warehouse,
         o_c_id as customer,
         count(*) as cnt_orderline,
         max(ol_quantity) as max_quantity,
         avg(ol_amount) as avg_amount,
         min(ol_delivery_d) as min_deliv_date
    from warehouse
    join orders
    on w_id = o_w_id
    join order_line
    on o_id = ol_o_id and o_w_id = ol_w_id
    where w_id > 100 and w_id < 200
    group by w_id, o_c_id
```

## <a name="stored-procedure-for-remote-t-sql-execution-sp_execute_remote"></a>Procedimento armazenado para execução remota de T-SQL: sp\_execute_remote

A consulta elástica também introduz um procedimento armazenado que proporciona acesso direto aos fragmentos. O procedimento armazenado é chamado [sp\_executar \_remoto](https://msdn.microsoft.com/library/mt703714) e pode ser usado para executar procedimentos armazenados à distância ou código T-SQL nas bases de dados remotas. São precisos os seguintes parâmetros:

* Nome da fonte de dados (nvarchar): O nome da fonte externa de dados do tipo RDBMS.
* Consulta (nvarchar): A consulta T-SQL a ser executada em cada fragmento.
* Declaração de parâmetro (nvarchar) - opcional: Corda com definições de tipo de dados para os parâmetros utilizados no parâmetro de consulta (como sp_executesql).
* Lista de valor dos parâmetros - opcional: Lista separada da vírmets com a vírpar (como sp_executesql).

O\_sp\_executar remote utiliza a fonte de dados externa fornecida nos parâmetros de invocação para executar a declaração T-SQL dada nas bases de dados remotas. Utiliza a credencial da fonte externa de dados para se ligar à base de dados do gestor de mapas de fragmentos e às bases de dados remotas.  

Exemplo:

```sql
    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse'
```

## <a name="connectivity-for-tools"></a>Conectividade para ferramentas

Utilize as cadeias regulares de ligação do SQL Server para ligar a sua aplicação, o seu BI e as ferramentas de integração de dados à base de dados com as definições de tabela externa. Certifique-se de que o SQL Server é suportado como fonte de dados para a sua ferramenta. Em seguida, consulte a base de dados de consulta elástica como qualquer outra base de dados do SQL Server ligada à ferramenta e utilize tabelas externas da sua ferramenta ou aplicação como se fossem mesas locais.

## <a name="best-practices"></a>Melhores práticas

* Certifique-se de que a base de dados de pontofinal de consulta elástica teve acesso à base de dados do shardmap e a todos os fragmentos através das firewalls SQL DB.  
* Validar ou impor a distribuição de dados definida pela tabela externa. Se a distribuição real de dados for diferente da distribuição especificada na definição da tabela, as suas consultas podem produzir resultados inesperados.
* A consulta elástica atualmente não realiza a eliminação do fragmento quando predica sobre a tecla sharding permitir-lhe-ia excluir com segurança certos fragmentos do processamento.
* A consulta elástica funciona melhor para consultas onde a maior parte da computação pode ser feita nos fragmentos. Normalmente obtém-se o melhor desempenho de consulta com predicados de filtro seletivo que podem ser avaliados nos fragmentos ou se juntam sobre as teclas de partição que podem ser realizadas de forma alinhada com divisórias em todos os fragmentos. Outros padrões de consulta podem precisar de carregar grandes quantidades de dados dos fragmentos para o nó da cabeça e podem ter um desempenho fraco

## <a name="next-steps"></a>Passos seguintes

* Para uma visão geral da consulta elástica, consulte a visão geral da [consulta elástica](sql-database-elastic-query-overview.md).
* Para um tutorial de partição vertical, consulte [Iniciar-se com consulta de base de dados cruzada (partição vertical)](sql-database-elastic-query-getting-started-vertical.md).
* Para consultas de sintaxe e amostras para dados verticalmente divididos, consulte a [consulta de dados verticalmente divididos)](sql-database-elastic-query-vertical-partitioning.md)
* Para um tutorial de partição horizontal (sharding), consulte [Iniciar-se com consulta elástica para partição horizontal (sharding)](sql-database-elastic-query-getting-started.md).
* Consulte o [sp\_execute \_o controlo remoto](https://msdn.microsoft.com/library/mt703714) para um procedimento armazenado que execute uma declaração Transact-SQL numa única base de dados remota do Azure SQL ou num conjunto de bases de dados que servem como fragmentos num esquema de partição horizontal.

<!--Image references-->
[1]: ./media/sql-database-elastic-query-horizontal-partitioning/horizontalpartitioning.png
<!--anchors-->
