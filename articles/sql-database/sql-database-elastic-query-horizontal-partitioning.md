---
title: Relatórios entre bancos de dados de nuvem expandidos
description: como configurar consultas elásticas em partições horizontais
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
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823766"
---
# <a name="reporting-across-scaled-out-cloud-databases-preview"></a>Relatórios entre bancos de dados de nuvem expandidos (visualização)

![Consultar entre fragmentos][1]

Os bancos de dados fragmentados distribuem linhas por uma camada horizontal. O esquema é idêntico em todos os bancos de dados participantes, também conhecido como particionamento horizontal. Usando uma consulta elástica, você pode criar relatórios que abrangem todos os bancos de dados em um banco de dados fragmentado.

Para um início rápido, consulte [relatórios entre bancos de dados de nuvem expandidos](sql-database-elastic-query-getting-started.md).

Para bancos de dados não fragmentados, consulte [consultar entre bancos de dados de nuvem com esquemas diferentes](sql-database-elastic-query-vertical-partitioning.md).

## <a name="prerequisites"></a>Pré-requisitos

* Crie um mapa de fragmentos usando a biblioteca de cliente do banco de dados elástico. consulte [Gerenciamento de mapa de fragmentos](sql-database-elastic-scale-shard-map-management.md). Ou use o aplicativo de exemplo em introdução [às ferramentas de banco de dados elástico](sql-database-elastic-scale-get-started.md).
* Como alternativa, consulte [migrar bancos de dados existentes para bancos de dados escalados horizontalmente](sql-database-elastic-convert-to-use-elastic-tools.md).
* O usuário deve ter a permissão alterar qualquer fonte de dados externa. Essa permissão está incluída com a permissão ALTER DATABASE.
* As permissões alterar qualquer fonte de dados externa são necessárias para fazer referência à fonte de dados subjacente.

## <a name="overview"></a>Descrição geral

Essas instruções criam a representação de metadados da camada de dados fragmentados no banco de dado de consulta elástica.

1. [CRIAR CHAVE MESTRA](https://msdn.microsoft.com/library/ms174382.aspx)
2. [CRIAR CREDENCIAL NO ESCOPO DO BANCO DE DADOS](https://msdn.microsoft.com/library/mt270260.aspx)
3. [CRIAR FONTE DE DADOS EXTERNA](https://msdn.microsoft.com/library/dn935022.aspx)
4. [CRIAR TABELA EXTERNA](https://msdn.microsoft.com/library/dn935021.aspx)

## <a name="11-create-database-scoped-master-key-and-credentials"></a>1,1 criar chave mestra no escopo do banco de dados e credenciais

A credencial é usada pela consulta elástica para se conectar aos seus bancos de dados remotos.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]

> [!NOTE]
> Verifique se o *"\<nome de usuário\>"* não inclui nenhum sufixo *"\@ServerName"* .

## <a name="12-create-external-data-sources"></a>1,2 criar fontes de dados externas

Sintaxe

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

Recupere a lista de fontes de dados externas atuais:

    select * from sys.external_data_sources;

A fonte de dados externa referencia seu mapa de fragmentos. Em seguida, uma consulta elástica usa a fonte de dados externa e o mapa de fragmentos subjacente para enumerar os bancos de dados que participam da camada de dado.
As mesmas credenciais são usadas para ler o mapa de fragmentos e para acessar os dados nos fragmentos durante o processamento de uma consulta elástica.

## <a name="13-create-external-tables"></a>1,3 criar tabelas externas

Sintaxe  

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

Recupere a lista de tabelas externas do banco de dados atual:

    SELECT * from sys.external_tables;

Para descartar tabelas externas:

    DROP EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name[;]

### <a name="remarks"></a>Observações

A cláusula DATA\_SOURCE define a fonte de dados externa (um mapa de fragmentos) que é usada para a tabela externa.  

O esquema\_nome e objeto\_cláusulas de nome mapeiam a definição da tabela externa para uma tabela em um esquema diferente. Se for omitido, o esquema do objeto remoto será considerado "dbo" e seu nome será considerado idêntico ao nome da tabela externa que está sendo definido. Isso será útil se o nome da sua tabela remota já estiver no banco de dados em que você deseja criar a tabela externa. Por exemplo, você deseja definir uma tabela externa para obter uma exibição agregada de exibições de catálogo ou DMVs em sua camada de dados expandida. Como as exibições de catálogo e DMVs já existem localmente, você não pode usar seus nomes para a definição de tabela externa. Em vez disso, use um nome diferente e use a exibição do catálogo ou o nome da DMV no esquema\_nome e/ou objeto\_cláusulas NAME. (Consulte o exemplo abaixo.)

A cláusula DISTRIBUTION especifica a distribuição de dados usada para esta tabela. O processador de consultas utiliza as informações fornecidas na cláusula DISTRIBUTION para criar os planos de consulta mais eficientes.

1. **Fragmentado** significa que os dados são particionados horizontalmente em todos os bancos. A chave de particionamento para a distribuição de dados é a **< sharding_column_name parâmetro >** .
2. **Replicado** significa que cópias idênticas da tabela estão presentes em cada banco de dados. É sua responsabilidade garantir que as réplicas sejam idênticas em todos os bancos de dados.
3. **Round\_Robin** significa que a tabela é particionada horizontalmente usando um método de distribuição dependente de aplicativo.

**Referência da camada de dados**: a DDL da tabela externa se refere a uma fonte de dados externa. A fonte de dados externa especifica um mapa de fragmentos que fornece a tabela externa com as informações necessárias para localizar todos os bancos de dados em sua camada.

### <a name="security-considerations"></a>Considerações de segurança

Os usuários com acesso à tabela externa têm acesso automaticamente às tabelas remotas subjacentes sob a credencial fornecida na definição da fonte de dados externa. Evite a elevação de privilégios indesejados por meio da credencial da fonte de dados externa. Use GRANT ou REVOKE para uma tabela externa, assim como se fosse uma tabela normal.  

Depois de definir sua fonte de dados externa e suas tabelas externas, agora você pode usar o T-SQL completo em suas tabelas externas.

## <a name="example-querying-horizontal-partitioned-databases"></a>Exemplo: consultando bancos de dados particionados horizontais

A consulta a seguir executa uma junção de três vias entre depósitos, pedidos e linhas de ordem e usa várias agregações e um filtro seletivo. Ele pressupõe (1) particionamento horizontal (fragmentação) e (2) que os depósitos, pedidos e linhas de ordem são fragmentados pela coluna ID do depósito e que a consulta elástica pode colocalizar as junções nos fragmentos e processar a parte cara da consulta nos fragmentos em completa.

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

## <a name="stored-procedure-for-remote-t-sql-execution-sp_execute_remote"></a>Procedimento armazenado para execução remota de T-SQL: SP\_execute_remote

A consulta elástica também apresenta um procedimento armazenado que fornece acesso direto aos fragmentos. O procedimento armazenado é chamado [sp\_execute \_Remote](https://msdn.microsoft.com/library/mt703714) e pode ser usado para executar procedimentos armazenados remotos ou código t-SQL nos bancos de dados remotos. Ele usa os seguintes parâmetros:

* Nome da fonte de dados (nvarchar): o nome da fonte de dados externa do tipo RDBMS.
* Consulta (nvarchar): a consulta T-SQL a ser executada em cada fragmento.
* Declaração de parâmetro (nvarchar)-opcional: cadeia de caracteres com definições de tipo de dados para os parâmetros usados no parâmetro de consulta (como sp_executesql).
* Lista de valores de parâmetro-opcional: lista separada por vírgulas de valores de parâmetro (como sp_executesql).

O SP\_executar\_remoto usa a fonte de dados externa fornecida nos parâmetros de invocação para executar a instrução T-SQL fornecida nos bancos de dados remotos. Ele usa a credencial da fonte de dados externa para conectar-se ao banco de dado do shardmap Manager e aos bancos de dados remotos.  

Exemplo:

```sql
    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse'
```

## <a name="connectivity-for-tools"></a>Conectividade para ferramentas

Use as cadeias de conexão SQL Server regulares para conectar seu aplicativo, suas ferramentas de integração de BI e dados ao banco de dado com suas definições de tabela externa. Verifique se SQL Server tem suporte como uma fonte de dados para sua ferramenta. Em seguida, referencie o banco de dados de consulta elástica como qualquer outro banco de dados SQL Server conectado à ferramenta e use tabelas externas de sua ferramenta ou aplicativo como se fossem tabelas locais.

## <a name="best-practices"></a>Melhores práticas

* Verifique se o banco de dados de ponto de extremidade de consulta elástica recebeu acesso ao banco de dados shardmap e a todos os fragmentos por meio dos firewalls de BD SQL.  
* Validar ou impor a distribuição de dados definida pela tabela externa. Se sua distribuição de dados real for diferente da distribuição especificada na definição de tabela, suas consultas poderão gerar resultados inesperados.
* Atualmente, a consulta elástica não executa a eliminação de fragmentos quando os predicados sobre a chave de fragmentação permitiriam que a ti excluisse com segurança determinados fragmentos do processamento.
* A consulta elástica funciona melhor para consultas em que a maior parte da computação pode ser feita nos fragmentos. Normalmente, você obtém o melhor desempenho de consulta com predicados de filtro seletivo que podem ser avaliados nos fragmentos ou junções sobre as chaves de particionamento que podem ser executadas em uma maneira alinhada à partição em todos os fragmentos. Outros padrões de consulta podem precisar carregar grandes quantidades de dados dos fragmentos para o nó principal e podem funcionar de forma ruim

## <a name="next-steps"></a>Passos seguintes

* Para obter uma visão geral da consulta elástica, consulte [visão geral da consulta elástica](sql-database-elastic-query-overview.md).
* Para obter um tutorial de particionamento vertical, consulte [introdução à consulta entre bancos de dados (particionamento vertical)](sql-database-elastic-query-getting-started-vertical.md).
* Para obter sintaxe e exemplos de consultas para dados particionados verticalmente, consulte [consultando dados particionados verticalmente)](sql-database-elastic-query-vertical-partitioning.md)
* Para obter um tutorial horizontal de particionamento (fragmentação), consulte [introdução à consulta elástica para particionamento horizontal (fragmentação)](sql-database-elastic-query-getting-started.md).
* Consulte [sp\_execute \_Remote](https://msdn.microsoft.com/library/mt703714) para obter um procedimento armazenado que executa uma instrução TRANSACT-SQL em um único banco de dados SQL do Azure remoto ou conjunto de bancos de dados que serve como fragmentos em um esquema de particionamento horizontal.

<!--Image references-->
[1]: ./media/sql-database-elastic-query-horizontal-partitioning/horizontalpartitioning.png
<!--anchors-->
