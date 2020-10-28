---
title: Consulta através de bases de dados de nuvem com esquemas diferentes
description: como configurar consultas de base de dados cruzadas sobre divisórias verticais
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: c507a4c618713ba83d25b9defa918092db1a3c8e
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792094"
---
# <a name="query-across-cloud-databases-with-different-schemas-preview"></a>Consulta através de bases de dados em nuvem com esquemas diferentes (pré-visualização)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

![Consulta através de tabelas em diferentes bases de dados][1]

Bases de dados verticalmente divididas utilizam diferentes conjuntos de tabelas em diferentes bases de dados. Isto significa que o esquema é diferente em diferentes bases de dados. Por exemplo, todas as tabelas de inventário estão numa base de dados, enquanto todas as tabelas relacionadas com a contabilidade estão numa segunda base de dados.

## <a name="prerequisites"></a>Pré-requisitos

* O utilizador deve possuir qualquer permissão DE FONTE DE DADOS EXTERNA. Esta permissão está incluída na permissão ALTER DATABASE.
* ALTERAR QUAISquer permissões EXTERNAS DE FONTE DE DADOS são necessárias para consultar a fonte de dados subjacente.

## <a name="overview"></a>Descrição geral

> [!NOTE]
> Ao contrário da partição horizontal, estas declarações DDL não dependem da definição de um nível de dados com um mapa de fragmentos através da biblioteca de clientes de base de dados elástica.
>

1. [CRIAR CHAVE-MESTRE](/sql/t-sql/statements/create-master-key-transact-sql)
2. [CRIAR CREDENCIAL DE ÂMBITO DE BASE DE DADOS](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)
3. [CRIAR FONTE DE DADOS EXTERNA](/sql/t-sql/statements/create-external-data-source-transact-sql)
4. [CRIAR TABELA EXTERNA](/sql/t-sql/statements/create-external-table-transact-sql)

## <a name="create-database-scoped-master-key-and-credentials"></a>Criar chave e credenciais principais de âmbito de base de dados

A credencial é utilizada pela consulta elástica para ligar às suas bases de dados remotas.  

```sql
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'master_key_password';
CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
SECRET = '<password>'
[;]
```

> [!NOTE]
> Certifique-se de que `<username>` o **\@ "nome de servidor"** não inclui qualquer sufixo de "nome de servidor".

## <a name="create-external-data-sources"></a>Criar fontes de dados externas

Sintaxe:

<External_Data_Source> ::= CRIAR <data_source_name> DE FONTE DE DADOS EXTERNOS COM (TIPO = RDBMS, LOCALIZAÇÃO = '<fully_qualified_server_name>', DATABASE_NAME = '<remote_database_name>',  
    CREDENCIAL = <credential_name> ) [;]

> [!IMPORTANT]
> O parâmetro TYPE deve ser definido como **RDBMS** .

### <a name="example"></a>Exemplo

O exemplo a seguir ilustra a utilização da declaração CREATE para fontes de dados externas.

```sql
CREATE EXTERNAL DATA SOURCE RemoteReferenceData
   WITH
      (
         TYPE=RDBMS,
         LOCATION='myserver.database.windows.net',
         DATABASE_NAME='ReferenceData',
         CREDENTIAL= SqlUser
      );
```

Para recuperar a lista das fontes de dados externas atuais:

```sql
select * from sys.external_data_sources;
```

### <a name="external-tables"></a>Tabelas Externas

Sintaxe:

CRIAR TABELA EXTERNA [ database_name . [schema_name ] . | schema_name. ] table_name  
    ( {<column_definition> } [ ,... n ]) {WITH (<rdbms_external_table_options>) } [;].

<rdbms_external_table_options> ::= DATA_SOURCE = <External_Data_Source>, [ SCHEMA_NAME = N'nonescaped_schema_name',] [ OBJECT_NAME = N'nonescaped_object_name',]

### <a name="example"></a>Exemplo

```sql
CREATE EXTERNAL TABLE [dbo].[customer]
   (
      [c_id] int NOT NULL,
      [c_firstname] nvarchar(256) NULL,
      [c_lastname] nvarchar(256) NOT NULL,
      [street] nvarchar(256) NOT NULL,
      [city] nvarchar(256) NOT NULL,
      [state] nvarchar(20) NULL,
      DATA_SOURCE = RemoteReferenceData
   );
```

O exemplo a seguir mostra como recuperar a lista de tabelas externas da base de dados atual:

```sql
select * from sys.external_tables;
```

### <a name="remarks"></a>Observações

A consulta elástica estende a sintaxe de tabela externa existente para definir tabelas externas que utilizam fontes de dados externas do tipo RDBMS. Uma definição de quadro externo para a partição vertical abrange os seguintes aspetos:

* **Schema** : A tabela externa DDL define um esquema que as suas consultas podem utilizar. O esquema fornecido na definição da sua tabela externa tem de corresponder ao esquema das tabelas na base de dados remota onde os dados reais são armazenados.
* **Referência de base de dados remota** : A tabela externa DDL refere-se a uma fonte de dados externa. A fonte de dados externa especifica o nome do servidor e o nome da base de dados da base de dados remota onde os dados reais da tabela são armazenados.

Utilizando uma fonte de dados externa, tal como delineada na secção anterior, a sintaxe para criar tabelas externas é a seguinte:

A cláusula DATA_SOURCE define a fonte de dados externa (isto é, a base de dados remota em caso de partição vertical) que é utilizada para a tabela externa.  

As cláusulas SCHEMA_NAME e OBJECT_NAME fornecem a capacidade de mapear a definição de tabela externa para uma tabela num esquema diferente na base de dados remota, ou para uma tabela com um nome diferente, respectivamente. Isto é útil se pretender definir uma tabela externa para uma visualização de catálogo ou DMV na sua base de dados remota - ou qualquer outra situação em que o nome da mesa remota já esteja tomado localmente.  

A seguinte declaração DDL deixa cair uma definição de tabela externa existente do catálogo local. Não afeta a base de dados remota.

```sql
DROP EXTERNAL TABLE [ [ schema_name ] . | schema_name. ] table_name[;]  
```

**Permissões para a TABELA EXTERNA CREATE/DROP** : ALTERE quaisquer permissões EXTERNAs DE FONTE DE DADOS para a tabela externa DDL que também é necessária para se referir à fonte de dados subjacente.  

## <a name="security-considerations"></a>Considerações de segurança

Os utilizadores com acesso à tabela externa têm automaticamente acesso às tabelas remotas subjacentes sob a credencial dada na definição de fonte de dados externa. Deve gerir cuidadosamente o acesso à tabela externa de modo a evitar a elevação de privilégios não desejada através da credencial da fonte de dados externa. Permissões SQL regulares podem ser usadas para conceder ou revogar o acesso a uma mesa externa como se fosse uma mesa regular.  

## <a name="example-querying-vertically-partitioned-databases"></a>Exemplo: consulta de bases de dados verticalmente divididas

A seguinte consulta realiza uma junção a três entre as duas mesas locais para encomendas e linhas de encomenda e a tabela remota para os clientes. Este é um exemplo do caso de utilização de dados de referência para consulta elástica:

```sql
    SELECT
     c_id as customer,
     c_lastname as customer_name,
     count(*) as cnt_orderline,
     max(ol_quantity) as max_quantity,
     avg(ol_amount) as avg_amount,
     min(ol_delivery_d) as min_deliv_date
    FROM customer
    JOIN orders
    ON c_id = o_c_id
    JOIN  order_line
    ON o_id = ol_o_id and o_c_id = ol_c_id
    WHERE c_id = 100
```

## <a name="stored-procedure-for-remote-t-sql-execution-sp_execute_remote"></a>Procedimento armazenado para execução remota de T-SQL: \_ execute_remote

A consulta elástica também introduz um procedimento armazenado que proporciona acesso direto à base de dados remota. O procedimento armazenado chama-se [sp \_ executar \_ remoto](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database) e pode ser usado para executar procedimentos de armazenação remota ou código T-SQL na base de dados remota. São necessários os seguintes parâmetros:

* Nome de origem de dados (nvarchar): O nome da fonte de dados externa do tipo RDBMS.
* Consulta (nvarchar): A consulta T-SQL a ser executada na base de dados remota.
* Declaração de parâmetros (nvarchar) - opcional: Cadeia com definições de tipo de dados para os parâmetros utilizados no parâmetro de consulta (como sp_executesql).
* Lista de valores de parâmetros - opcional: Lista separada por vírgula de valores de parâmetros (como sp_executesql).

O sp \_ executo \_ remotamente a fonte de dados externa fornecida nos parâmetros de invocação para executar a declaração T-SQL dada na base de dados remota. Utiliza a credencial da fonte de dados externa para ligar à base de dados remota.  

Exemplo:

```sql
    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse'
```

## <a name="connectivity-for-tools"></a>Conectividade para ferramentas

Pode utilizar as cadeias regulares de ligação do SQL Server para ligar as suas ferramentas de integração de dados e BI às bases de dados do servidor que tem uma consulta elástica ativada e tabelas externas definidas. Certifique-se de que o SQL Server é suportado como fonte de dados para a sua ferramenta. Em seguida, consulte a base de dados de consulta elástica e as suas tabelas externas, tal como qualquer outra base de dados do SQL Server a que se ligaria à sua ferramenta.

## <a name="best-practices"></a>Melhores práticas

* Certifique-se de que a base de dados de ponto final de consulta elástica teve acesso à base de dados remota, permitindo o acesso aos Serviços Azure na sua configuração de firewall Azure SQL Database. Certifique-se também de que a credencial fornecida na definição de fonte de dados externa pode entrar com sucesso na base de dados remota e tem as permissões para aceder à tabela remota.  
* A consulta elástica funciona melhor para consultas onde a maior parte do cálculo pode ser feito nas bases de dados remotas. Normalmente obtém o melhor desempenho de consulta com predicados de filtro seletivos que podem ser avaliados nas bases de dados remotas ou junções que podem ser realizadas completamente na base de dados remota. Outros padrões de consulta podem ter de carregar grandes quantidades de dados a partir da base de dados remota e podem ter um desempenho fraco.

## <a name="next-steps"></a>Passos seguintes

* Para obter uma visão geral da consulta elástica, consulte [a visão geral da consulta elástica](elastic-query-overview.md).
* Para obter um tutorial de partição vertical, consulte [Começar com consulta de base de dados cruzada (partição vertical)](elastic-query-getting-started-vertical.md).
* Para obter um tutorial de partição horizontal (fragmento), consulte [Começar com consulta elástica para partição horizontal (fragmento)](elastic-query-getting-started.md).
* Para consultas de sintaxe e amostra para dados com divisórias horizontais, consulte [consulta de dados com partição horizontal)](elastic-query-horizontal-partitioning.md)
* Consulte [o sp executar o controlo \_ \_ remoto](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database) para um procedimento armazenado que execute uma declaração Transact-SQL numa única base de dados remota do Azure SQL ou num conjunto de bases de dados que servem de fragmentos num esquema de partição horizontal.

<!--Image references-->
[1]: ./media/elastic-query-vertical-partitioning/verticalpartitioning.png

<!--anchors-->