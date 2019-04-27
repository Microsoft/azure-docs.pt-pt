---
title: Relatórios entre bases de dados de cloud aumentadas horizontalmente | Documentos da Microsoft
description: como configurar a consultas elásticas através de partições horizontais
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
manager: craigg
ms.date: 01/03/2019
ms.openlocfilehash: 3b2b472407175df307c569704d4c7611737c4ea1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60694357"
---
# <a name="reporting-across-scaled-out-cloud-databases-preview"></a>Relatórios entre bases de dados de cloud aumentadas horizontalmente (pré-visualização)

![Consultas em partições horizontais][1]

Bases de dados em partição horizontal distribuam linhas num dados aumentados horizontalmente camadas. O esquema é idêntico em todos os participantes bases de dados, também conhecidos como criação de partições horizontais. Usando uma consulta elástica, pode criar relatórios que abrangem todas as bases de dados numa base de dados em partição horizontal.

Para um início rápido, consulte [relatórios entre bases de dados de cloud aumentadas horizontalmente](sql-database-elastic-query-getting-started.md).

Para bases de dados não em partição horizontal, consulte [consultas entre bases de dados na cloud com esquemas diferentes](sql-database-elastic-query-vertical-partitioning.md).

## <a name="prerequisites"></a>Pré-requisitos

* Crie um mapa de partições horizontais com a biblioteca de cliente de base de dados elástica. ver [gestão de mapas de partições horizontais](sql-database-elastic-scale-shard-map-management.md). Ou utilize a aplicação de exemplo na [introdução às ferramentas de bases de dados elásticas](sql-database-elastic-scale-get-started.md).
* Em alternativa, veja [Migrar bases de dados existentes para bases de dados aumentadas horizontalmente](sql-database-elastic-convert-to-use-elastic-tools.md).
* O utilizador tem de ter permissão de alterar qualquer origem de dados externa. Esta permissão está incluída com a permissão ALTER DATABASE.
* São necessárias permissões de alterar qualquer origem de dados externa para fazer referência à origem de dados subjacente.

## <a name="overview"></a>Descrição geral

Essas instruções criar a representação de metadados de sua camada de dados em partição horizontal na base de dados de consulta elástica.

1. [CRIAR CHAVE MESTRA](https://msdn.microsoft.com/library/ms174382.aspx)
2. [CRIAR UM ÂMBITO DE BASE DE DADOS CREDENCIAL](https://msdn.microsoft.com/library/mt270260.aspx)
3. [CRIAR ORIGEM DE DADOS EXTERNA](https://msdn.microsoft.com/library/dn935022.aspx)
4. [CRIAR TABELA EXTERNA](https://msdn.microsoft.com/library/dn935021.aspx)

## <a name="11-create-database-scoped-master-key-and-credentials"></a>1.1 criar a chave mestra do banco de dados no âmbito e as credenciais

A credencial é utilizada pela consulta elástica para ligar aos seus bancos de dados remotos.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]

> [!NOTE]
> Certifique-se de que o *"\<nome de utilizador\>"* não inclui quaisquer *"\@servername"* sufixo.

## <a name="12-create-external-data-sources"></a>1.2 Criar origens de dados externas

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

Obter a lista de origens de dados externas atual:

    select * from sys.external_data_sources;

A origem de dados externo faz referência a seu mapa de partições horizontais. Uma consulta elástica utiliza, em seguida, a origem de dados externos e o mapa de partições horizontais subjacente para enumerar as bases de dados que participam na camada de dados.
As mesmas credenciais são utilizadas para ler o mapa de partições horizontais e acessar os dados em partições horizontais durante o processamento de uma consulta elástica.

## <a name="13-create-external-tables"></a>1.3 criar tabelas externas

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

Obter a lista de tabelas externas da base de dados atual:

    SELECT * from sys.external_tables;

Para remover as tabelas externas:

    DROP EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name[;]

### <a name="remarks"></a>Observações

Os dados\_cláusula de origem define a origem de dados externo (um mapa de partições horizontais) que é utilizada para a tabela externa.  

O esquema\_nome e o OBJETO\_cláusulas de nome mapeiam a definição de tabela externa para uma tabela num esquema diferente. Se for omitido, o esquema do objeto remoto é considerado como "dbo" e o respetivo nome é considerado como idêntico ao nome de tabela externa que está a ser definido. Isto é útil se o nome da sua tabela remota já estiver atribuído na base de dados onde pretende criar tabela externa. Por exemplo, pretende definir uma tabela externa para obter uma visualização agregada dos modos de exibição de catálogo ou camada de DMVs nos seus dados aumentados horizontalmente. Uma vez que as exibições de catálogo e DMVs de existir localmente, não é possível utilizar os respetivos nomes para a definição de tabela externa. Em vez disso, utilize um nome diferente e utilizar a vista de catálogo ou a DMV nome no esquema\_nome e/ou OBJETO\_cláusulas de nome. (Consulte o exemplo abaixo.)

A cláusula de distribuição Especifica a distribuição de dados utilizada para esta tabela. O processador de consultas utiliza as informações fornecidas na cláusula de distribuição para criar os planos de consulta mais eficientes.

1. **Em partição horizontal** significa que dados são particionados horizontalmente entre bases de dados. A chave de criação de partições para a distribuição de dados é o **< sharding_column_name >** parâmetro.
2. **REPLICADOS** significa que as cópias idênticas da tabela estão presentes em cada base de dados. É da responsabilidade do cliente, certifique-se de que as réplicas são idênticas entre bases de dados.
3. **ARREDONDA\_ROBIN** significa que a tabela horizontalmente é particionada usando um método de distribuição de aplicações dependentes.

**Referência de camada de dados**: A tabela externa DDL refere-se a uma origem de dados externa. A origem de dados externo Especifica um mapa de partições horizontais que fornece a tabela externa com as informações necessárias para localizar todas as bases de dados na camada de dados.

### <a name="security-considerations"></a>Considerações de segurança

Os utilizadores com acesso à tabela externa automaticamente obtém acesso às tabelas subjacentes remotos sob a credencial fornecida na definição de origem de dados externa. Evite indesejada de elevação de privilégios através da credencial da origem de dados externo. Utilize GRANT ou REVOKE para uma tabela externa como se fosse uma tabela normal.  

Depois de definir a origem de dados externos e as tabelas externas, agora pode utilizar o T-SQL completa sobre as tabelas externas.

## <a name="example-querying-horizontal-partitioned-databases"></a>Exemplo: consultar bases de dados particionadas horizontais

A seguinte consulta executa uma junção de três vias entre armazéns, orders e linhas da ordem e utiliza várias agregações e um filtro seletivo. Parte do princípio de criação de partições (1) horizontal (fragmentação) e (2) que estão em partição horizontal pela coluna de id de armazém armazéns, pedidos e das linhas da ordem, e que a consulta elástica pode colocalize as junções em partições horizontais e processar o Caro parte da consulta em partições horizontais de paralelo.

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

## <a name="stored-procedure-for-remote-t-sql-execution-spexecuteremote"></a>Procedimento para execução remota de T-SQL armazenado: sp\_execute_remote

Consulta elástica também introduz um procedimento armazenado que fornece acesso direto às partições horizontais. O procedimento armazenado é chamado [sp\_execute \_remoto](https://msdn.microsoft.com/library/mt703714) e pode ser usada para executar procedimentos armazenados remotos ou o código T-SQL nas bases de dados remotas. Ele usa os seguintes parâmetros:

* Nome da origem de dados (nvarchar): O nome da origem de dados externa do tipo RDBMS.
* Consulta (nvarchar): A consulta de T-SQL a ser executado em cada partição horizontal.
* Declaração de parâmetro (nvarchar) - opcional: Cadeia com os dados de definições de tipo para os parâmetros utilizados no parâmetro de consulta (como sp_executesql).
* Lista de valores parâmetro - opcional: Lista separada por vírgulas de valores de parâmetro (como sp_executesql).

O sp\_executar\_remoto usa a origem de dados externa fornecida nos parâmetros de invocação para executar a instrução T-SQL específica nas bases de dados remotas. Utiliza a credencial da origem de dados externos para ligar a base de dados do Gestor de shardmap e as bases de dados remotas.  

Exemplo:

```sql
    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse'
```

## <a name="connectivity-for-tools"></a>Conectividade de ferramentas

Utilizar cadeias de ligação normais do SQL Server para ligar a sua aplicação, as ferramentas de integração BI e os dados na base de dados com as definições de tabela externa. Certifique-se de que o SQL Server é suportado como uma origem de dados para a sua ferramenta. Em seguida, referência da base de dados de consulta elástica, como qualquer outro banco de dados do SQL Server ligado à ferramenta e o uso de tabelas externas sua ferramenta ou aplicativo como se fossem tabelas locais.

## <a name="best-practices"></a>Melhores práticas

* Certifique-se de que a base de dados do ponto final de consulta elástica tem sido dado acesso para a base de dados shardmap e todas as partições horizontais através de firewalls de BD SQL.  
* Validar ou impor a distribuição de dados definida pela tabela externa. Se a sua distribuição de dados real é diferente da distribuição especificada na definição da tabela, as suas consultas poderão resultar em resultados inesperados.
* Consulta elástica atualmente não efetua a eliminação de partições horizontais quando predicados sobre a chave de fragmentação permitiria a excluir com segurança determinadas as partições horizontais de processamento.
* Consulta elástica funciona melhor para consultas em que a maioria da computação pode ser feita em partições horizontais. Normalmente, pode obter o melhor desempenho de consulta com predicados de filtro seletiva que pode ser avaliado no partições horizontais ou associações sobre as criação de partições as chaves que podem ser executadas de forma a partição alinhada em todas as partições horizontais. Outros padrões de consulta poderão ter de carregar grandes quantidades de dados das partições horizontais para o nó principal e podem ter um mau desempenho

## <a name="next-steps"></a>Passos Seguintes

* Para obter uma descrição geral de consulta elástica, veja [descrição geral de consulta elástica](sql-database-elastic-query-overview.md).
* Para obter um tutorial de criação de partições vertical, consulte [guia de introdução consultas entre bases de dados (criação de partições verticais)](sql-database-elastic-query-getting-started-vertical.md).
* Para consultas de exemplo de sintaxe e para dados particionados verticalmente, consulte [consultar verticalmente particionada dados)](sql-database-elastic-query-vertical-partitioning.md)
* Para obter um tutorial de criação de partições horizontais (fragmentação), consulte [guia de introdução consulta elástica para a criação de partições horizontais (fragmentação)](sql-database-elastic-query-getting-started.md).
* Ver [sp\_execute \_remoto](https://msdn.microsoft.com/library/mt703714) para um procedimento armazenado que executa uma instrução de Transact-SQL num único remoto SQL Database do Azure ou um conjunto de bases de dados que serve como fragmentos num esquema de partições horizontal.

<!--Image references-->
[1]: ./media/sql-database-elastic-query-horizontal-partitioning/horizontalpartitioning.png
<!--anchors-->
