---
title: Relatórios através de bases de dados de nuvem escalonadas
description: como configurar consultas elásticas sobre divisórias horizontais
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 01/03/2019
ms.openlocfilehash: 148c4828309738a18dbda5fd35ea634e8384bfde
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792111"
---
# <a name="reporting-across-scaled-out-cloud-databases-preview"></a>Reportagem em bases de dados de nuvem escalonadas (pré-visualização)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

![Consulta através de fragmentos][1]

Bases de dados de fragmentos distribuem linhas através de um nível de dados escalonado. O esquema é idêntico em todas as bases de dados participantes, também conhecidas como divisórias horizontais. Usando uma consulta elástica, pode criar relatórios que abrangem todas as bases de dados numa base de dados de fragmentos.

Para obter um arranque rápido, consulte [reportando através de bases de dados de nuvem escalonadas](elastic-query-getting-started.md).

Para bases de dados não cobertas, consulte consulta através de [bases de dados em nuvem com esquemas diferentes](elastic-query-vertical-partitioning.md).

## <a name="prerequisites"></a>Pré-requisitos

* Crie um mapa de fragmentos utilizando a biblioteca de clientes de base de dados elástica. ver [gestão de mapas de fragmentos.](elastic-scale-shard-map-management.md) Ou use a aplicação de amostra em [Começar com ferramentas de base de dados elásticas.](elastic-scale-get-started.md)
* Em alternativa, consulte [as bases de dados existentes para bases de dados em escala.](elastic-convert-to-use-elastic-tools.md)
* O utilizador deve possuir qualquer permissão DE FONTE DE DADOS EXTERNA. Esta permissão está incluída na permissão ALTER DATABASE.
* ALTERAR QUAISquer permissões EXTERNAS DE FONTE DE DADOS são necessárias para consultar a fonte de dados subjacente.

## <a name="overview"></a>Descrição geral

Estas declarações criam a representação de metadados do seu nível de dados fragmentos na base de dados de consulta elástica.

1. [CRIAR CHAVE-MESTRE](/sql/t-sql/statements/create-master-key-transact-sql)
2. [CRIAR CREDENCIAL DE ÂMBITO DE BASE DE DADOS](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)
3. [CRIAR FONTE DE DADOS EXTERNA](/sql/t-sql/statements/create-external-data-source-transact-sql)
4. [CRIAR TABELA EXTERNA](/sql/t-sql/statements/create-external-table-transact-sql)

## <a name="11-create-database-scoped-master-key-and-credentials"></a>1.1 Criar chave e credenciais principais de âmbito de base de dados

A credencial é utilizada pela consulta elástica para ligar às suas bases de dados remotas.  

```sql
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';
CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
SECRET = '<password>'
[;]
```

> [!NOTE]
> Certifique-se de que o *" \<username\> " não* inclui qualquer sufixo de "nome de *\@ servidor".*

## <a name="12-create-external-data-sources"></a>1.2 Criar fontes de dados externas

Sintaxe:

```sql
<External_Data_Source> ::=
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH
        (TYPE = SHARD_MAP_MANAGER,
                   LOCATION = '<fully_qualified_server_name>',
        DATABASE_NAME = ‘<shardmap_database_name>',
        CREDENTIAL = <credential_name>,
        SHARD_MAP_NAME = ‘<shardmapname>’
               ) [;]
```

### <a name="example"></a>Exemplo

```sql
CREATE EXTERNAL DATA SOURCE MyExtSrc
WITH
(
    TYPE=SHARD_MAP_MANAGER,
    LOCATION='myserver.database.windows.net',
    DATABASE_NAME='ShardMapDatabase',
    CREDENTIAL= SMMUser,
    SHARD_MAP_NAME='ShardMap'
);
```

Recuperar a lista das fontes de dados externas atuais:

```sql
select * from sys.external_data_sources;
```

A fonte de dados externa refere o seu mapa de fragmentos. Uma consulta elástica utiliza então a fonte de dados externa e o mapa de fragmentos subjacentes para enumerar as bases de dados que participam no nível de dados.
As mesmas credenciais são usadas para ler o mapa de fragmentos e para aceder aos dados dos fragmentos durante o processamento de uma consulta elástica.

## <a name="13-create-external-tables"></a>1.3 Criar tabelas externas

Sintaxe:  

```sql
CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name  
    ( { <column_definition> } [ ,...n ])
    { WITH ( <sharded_external_table_options> ) }
) [;]  

<sharded_external_table_options> ::=
  DATA_SOURCE = <External_Data_Source>,
  [ SCHEMA_NAME = N'nonescaped_schema_name',]
  [ OBJECT_NAME = N'nonescaped_object_name',]
  DISTRIBUTION = SHARDED(<sharding_column_name>) | REPLICATED |ROUND_ROBIN
```

**Exemplo**

```sql
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
```

Recuperar a lista de tabelas externas da base de dados atual:

```sql
SELECT * from sys.external_tables;
```

Para deixar cair tabelas externas:

```sql
DROP EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name[;]
```

### <a name="remarks"></a>Observações

A cláusula DATA \_ SOURCE define a fonte de dados externa (um mapa de fragmentos) que é utilizada para a tabela externa.  

As cláusulas SCHEMA \_ NAME e OBJECT NAME \_ mapeiam a definição de tabela externa para uma tabela num esquema diferente. Se omitido, presume-se que o esquema do objeto remoto é "dbo" e presume-se que o seu nome é idêntico ao nome da mesa externa que está a ser definido. Isto é útil se o nome da sua tabela remota já estiver na base de dados onde pretende criar a tabela externa. Por exemplo, pretende definir uma tabela externa para obter uma visão agregada das vistas do catálogo ou dos DMVs no seu nível de dados escalonado. Uma vez que as vistas de catálogo e DMVs já existem localmente, não pode usar os seus nomes para a definição de tabela externa. Em vez disso, use um nome diferente e use o nome da vista do catálogo ou do DMV nas cláusulas SCHEMA \_ NAME e/ou OBJECT \_ NAME. (Veja o exemplo abaixo.)

A cláusula DISTRIBUIÇÃO especifica a distribuição de dados utilizada para esta tabela. O processador de consulta utiliza as informações fornecidas na cláusula DISTRIBUIÇÃO para construir os planos de consulta mais eficientes.

1. **SHARDED** significa que os dados são divididos horizontalmente através das bases de dados. A chave de partição para a distribuição de dados é o parâmetro **<sharding_column_name>.**
2. **Replicado** significa que cópias idênticas da tabela estão presentes em cada base de dados. É da sua responsabilidade garantir que as réplicas são idênticas em todas as bases de dados.
3. **RODADA \_ ROBIN** significa que a tabela é horizontalmente dividida utilizando um método de distribuição dependente da aplicação.

**Referência do nível de dados** : A tabela externa DDL refere-se a uma fonte de dados externa. A fonte de dados externos especifica um mapa de fragmentos que fornece à tabela externa as informações necessárias para localizar todas as bases de dados no seu nível de dados.

### <a name="security-considerations"></a>Considerações de segurança

Os utilizadores com acesso à tabela externa têm automaticamente acesso às tabelas remotas subjacentes sob a credencial dada na definição de fonte de dados externa. Evite a elevação de privilégios não desejada através da credencial da fonte de dados externa. Utilize GRANT ou REVOKE para uma tabela externa como se fosse uma tabela normal.  

Uma vez definida a sua fonte de dados externa e as suas tabelas externas, pode agora utilizar o T-SQL completo sobre as suas tabelas externas.

## <a name="example-querying-horizontal-partitioned-databases"></a>Exemplo: consulta de bases de dados horizontais divididas

A seguinte consulta realiza uma junção a três entre armazéns, encomendas e linhas de encomenda e utiliza vários agregados e um filtro seletivo. Assume (1) a partição horizontal (fragmento) e (2) que os armazéns, encomendas e linhas de encomenda são arredores pela coluna de identificação do armazém, e que a consulta elástica pode co-localizar as juntas nos fragmentos e processar a parte cara da consulta nos fragmentos em paralelo.

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

## <a name="stored-procedure-for-remote-t-sql-execution-sp_execute_remote"></a>Procedimento armazenado para execução remota de T-SQL: \_ execute_remote

A consulta elástica também introduz um procedimento armazenado que proporciona acesso direto aos fragmentos. O procedimento armazenado chama-se [sp \_ executar \_ remoto](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database) e pode ser usado para executar procedimentos de armazenação remota ou código T-SQL nas bases de dados remotas. São necessários os seguintes parâmetros:

* Nome de origem de dados (nvarchar): O nome da fonte de dados externa do tipo RDBMS.
* Consulta (nvarchar): A consulta T-SQL a executar em cada fragmento.
* Declaração de parâmetros (nvarchar) - opcional: Cadeia com definições de tipo de dados para os parâmetros utilizados no parâmetro de consulta (como sp_executesql).
* Lista de valores de parâmetros - opcional: Lista separada por vírgula de valores de parâmetros (como sp_executesql).

O sp \_ executo \_ remotamente a fonte de dados externa fornecida nos parâmetros de invocação para executar a declaração T-SQL dada nas bases de dados remotas. Utiliza a credencial da fonte de dados externa para ligar à base de dados do gestor de mapas e às bases de dados remotas.  

Exemplo:

```sql
    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse'
```

## <a name="connectivity-for-tools"></a>Conectividade para ferramentas

Utilize as cadeias regulares de ligação do SQL Server para ligar a sua aplicação, o seu BI e ferramentas de integração de dados à base de dados com as definições de tabela externa. Certifique-se de que o SQL Server é suportado como fonte de dados para a sua ferramenta. Em seguida, consulte a base de dados de consulta elástica como qualquer outra base de dados do SQL Server ligada à ferramenta e utilize tabelas externas da sua ferramenta ou aplicação como se fossem tabelas locais.

## <a name="best-practices"></a>Melhores práticas

* Certifique-se de que a base de dados de pontos finais de consulta elástica teve acesso à base de dados de mapas de caco e a todos os fragmentos através das firewalls da Base de Dados SQL.  
* Validar ou impor a distribuição de dados definida pela tabela externa. Se a sua distribuição de dados real for diferente da distribuição especificada na definição da sua tabela, as suas consultas podem produzir resultados inesperados.
* Atualmente, a consulta elástica não realiza a eliminação de fragmentos quando predicados sobre a chave de fragmentos lhe permitiriam excluir com segurança certos fragmentos do processamento.
* A consulta elástica funciona melhor para consultas onde a maior parte do cálculo pode ser feito nos fragmentos. Normalmente obtém o melhor desempenho de consulta com predicados de filtro seletivos que podem ser avaliados nos fragmentos ou junta-se sobre as teclas de partição que podem ser realizadas de forma alinhada com partição em todos os fragmentos. Outros padrões de consulta podem precisar de carregar grandes quantidades de dados dos fragmentos até ao nó da cabeça e podem ter um desempenho fraco

## <a name="next-steps"></a>Passos seguintes

* Para obter uma visão geral da consulta elástica, consulte [a visão geral da consulta elástica](elastic-query-overview.md).
* Para obter um tutorial de partição vertical, consulte [Começar com consulta de base de dados cruzada (partição vertical)](elastic-query-getting-started-vertical.md).
* Para consultas de sintaxe e amostra para dados verticalmente divididos, consulte [consulta de dados partidos verticalmente)](elastic-query-vertical-partitioning.md)
* Para obter um tutorial de partição horizontal (fragmento), consulte [Começar com consulta elástica para partição horizontal (fragmento)](elastic-query-getting-started.md).
* Consulte [o sp executar o controlo \_ \_ remoto](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database) para um procedimento armazenado que execute uma declaração Transact-SQL numa única base de dados remota do Azure SQL ou num conjunto de bases de dados que servem de fragmentos num esquema de partição horizontal.

<!--Image references-->
[1]: ./media/elastic-query-horizontal-partitioning/horizontalpartitioning.png
<!--anchors-->