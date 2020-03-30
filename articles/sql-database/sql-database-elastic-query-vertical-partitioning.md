---
title: Consulta através de bases de dados de nuvem com diferentes esquemas
description: como configurar consultas de base de dados cruzadas sobre divisórias verticais
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: d5983d25685242a696300f293231bbf987e8442d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823722"
---
# <a name="query-across-cloud-databases-with-different-schemas-preview"></a>Consulta através de bases de dados de nuvem com diferentes schemas (pré-visualização)

![Consulta entre mesas em diferentes bases de dados][1]

As bases de dados verticalmente divididas utilizam diferentes conjuntos de tabelas em diferentes bases de dados. Isto significa que o esquema é diferente em diferentes bases de dados. Por exemplo, todas as tabelas para inventário estão numa base de dados, enquanto todas as tabelas relacionadas com contabilidade estão numa segunda base de dados. 

## <a name="prerequisites"></a>Pré-requisitos

* O utilizador deve possuir qualquer permissão DE DADOS EXTERNA FONTE. Esta permissão está incluída com a permissão alter DATABASE.
* ALTERE quaisquer permissões externas de FONTE DE DADOS são necessárias para se referir à fonte de dados subjacente.

## <a name="overview"></a>Descrição geral

> [!NOTE]
> Ao contrário da partilha horizontal, estas declarações dDL não dependem da definição de um nível de dados com um mapa de fragmentos através da biblioteca de clientes de base de dados elástica.
>

1. [CRIAR CHAVE MESTRE](https://msdn.microsoft.com/library/ms174382.aspx)
2. [CRIAR BASE DE DADOS CREDENCIAL SCOPED](https://msdn.microsoft.com/library/mt270260.aspx)
3. [CRIAR FONTE DE DADOS EXTERNAS](https://msdn.microsoft.com/library/dn935022.aspx)
4. [CRIAR TABELA EXTERNA](https://msdn.microsoft.com/library/dn935021.aspx) 

## <a name="create-database-scoped-master-key-and-credentials"></a>Criar chave-mestra e credenciais de base de dados

A credencial é utilizada pela consulta elástica para se ligar às suas bases de dados remotas.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'master_key_password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]

> [!NOTE]
> Certifique-se `<username>` de que o sufixo "nome de servidor" não inclui qualquer sufixo de **"nome\@de servidor".** 
>

## <a name="create-external-data-sources"></a>Criar fontes de dados externas

Sintaxe:

    <External_Data_Source> ::=
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH 
               (TYPE = RDBMS,
                LOCATION = ’<fully_qualified_server_name>’,
                DATABASE_NAME = ‘<remote_database_name>’,  
                CREDENTIAL = <credential_name> 
                ) [;] 

> [!IMPORTANT]
> O parâmetro TYPE deve ser definido para **RDBMS**. 
>

### <a name="example"></a>Exemplo

O exemplo que se segue ilustra a utilização da declaração CREATE para fontes de dados externas. 

    CREATE EXTERNAL DATA SOURCE RemoteReferenceData 
    WITH 
    ( 
        TYPE=RDBMS, 
        LOCATION='myserver.database.windows.net', 
        DATABASE_NAME='ReferenceData', 
        CREDENTIAL= SqlUser 
    ); 

Para recuperar a lista das fontes de dados externas atuais: 

    select * from sys.external_data_sources; 

### <a name="external-tables"></a>Tabelas Externas

Sintaxe:

    CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name . ] table_name  
    ( { <column_definition> } [ ,...n ])     
    { WITH ( <rdbms_external_table_options> ) } 
    )[;] 

    <rdbms_external_table_options> ::= 
      DATA_SOURCE = <External_Data_Source>, 
      [ SCHEMA_NAME = N'nonescaped_schema_name',] 
      [ OBJECT_NAME = N'nonescaped_object_name',] 

### <a name="example"></a>Exemplo

```sql
    CREATE EXTERNAL TABLE [dbo].[customer]( 
        [c_id] int NOT NULL, 
        [c_firstname] nvarchar(256) NULL, 
        [c_lastname] nvarchar(256) NOT NULL, 
        [street] nvarchar(256) NOT NULL, 
        [city] nvarchar(256) NOT NULL, 
        [state] nvarchar(20) NULL, 
        [country] nvarchar(50) NOT NULL, 
    ) 
    WITH 
    ( 
           DATA_SOURCE = RemoteReferenceData 
    ); 
```

O exemplo que se segue mostra como recuperar a lista de tabelas externas da base de dados atual: 

    select * from sys.external_tables; 

### <a name="remarks"></a>Observações

A consulta elástica alarga a sintaxe externa existente para definir quadros externos que utilizam fontes de dados externas do tipo RDBMS. Uma definição externa de tabela para a partição vertical abrange os seguintes aspetos: 

* **Schema**: A tabela externa DDL define um esquema que as suas consultas podem usar. O esquema fornecido na definição de tabela externa tem de coincidir com o esquema das tabelas na base de dados remota onde os dados reais são armazenados. 
* **Referência à base**de dados remota : A tabela externa DDL refere-se a uma fonte de dados externa. A fonte de dados externa especifica o nome do servidor da Base de Dados SQL e o nome da base de dados da base de dados remota onde os dados reais da tabela são armazenados. 

Utilizando uma fonte de dados externa, tal como delineado na secção anterior, a sintaxe para criar tabelas externas é a seguinte: 

A cláusula DATA_SOURCE define a fonte de dados externa (isto é, a base de dados remota em caso de divisão vertical) que é utilizada para a tabela externa.  

As cláusulas SCHEMA_NAME e OBJECT_NAME fornecem a capacidade de mapear a definição externa da tabela para uma tabela num esquema diferente na base de dados remota, ou para uma tabela com um nome diferente, respectivamente. Isto é útil se pretender definir uma tabela externa para uma vista de catálogo ou DMV na sua base de dados remota - ou qualquer outra situação em que o nome da tabela remota já seja tomado localmente.  

A seguinte declaração dDL deixa cair uma definição de tabela externa existente do catálogo local. Não afeta a base de dados remota. 

    DROP EXTERNAL TABLE [ [ schema_name ] . | schema_name. ] table_name[;]  

**Permissões para QUADRO EXTERNO CREATE/DROP**: ALTERAR Quaisquer permissões EXTERNAS FONTE DE DADOS São necessárias para dDL de tabela externa que também é necessária para se referir à fonte de dados subjacente.  

## <a name="security-considerations"></a>Considerações de segurança

Os utilizadores com acesso à tabela externa obtêm automaticamente acesso às tabelas remotas subjacentes sob a credencial dada na definição externa de fonte de dados. Deve gerir cuidadosamente o acesso à tabela externa de modo a evitar a elevação indesejada de privilégios através da credencial da fonte de dados externa. Permissões SQL regulares podem ser usadas para conceder ou REVOGAR o acesso a uma tabela externa, assim como se fosse uma tabela regular.  

## <a name="example-querying-vertically-partitioned-databases"></a>Exemplo: consulta de bases de dados verticalmente divididas

A seguinte consulta realiza uma ligação tridireccional entre as duas mesas locais para encomendas e linhas de encomenda e a tabela remota para os clientes. Este é um exemplo do caso de utilização de dados de referência para consulta elástica: 

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

## <a name="stored-procedure-for-remote-t-sql-execution-sp_execute_remote"></a>Procedimento armazenado para execução remota de T-SQL: sp\_execute_remote

A consulta elástica também introduz um procedimento armazenado que proporciona acesso direto à base de dados remota. O procedimento armazenado é chamado [sp\_executar \_remoto](https://msdn.microsoft.com/library/mt703714) e pode ser usado para executar procedimentos armazenados à distância ou código T-SQL na base de dados remota. São precisos os seguintes parâmetros: 

* Nome da fonte de dados (nvarchar): O nome da fonte externa de dados do tipo RDBMS. 
* Consulta (nvarchar): A consulta T-SQL a ser executada na base de dados remota. 
* Declaração de parâmetro (nvarchar) - opcional: Corda com definições de tipo de dados para os parâmetros utilizados no parâmetro de consulta (como sp_executesql). 
* Lista de valor dos parâmetros - opcional: Lista separada da vírmets com a vírpar (como sp_executesql).

O\_sp\_executar remote utiliza a fonte de dados externa fornecida nos parâmetros de invocação para executar a declaração T-SQL dada na base de dados remota. Utiliza a credencial da fonte de dados externa para se ligar à base de dados remota.  

Exemplo: 

```sql
    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse' 
```

## <a name="connectivity-for-tools"></a>Conectividade para ferramentas

Pode utilizar cordas regulares de ligação sQL Server para ligar as ferramentas de bio e integração de dados às bases de dados do servidor SQL DB que tem consulta elástica ativada e tabelas externas definidas. Certifique-se de que o SQL Server é suportado como fonte de dados para a sua ferramenta. Em seguida, consulte a base de dados de consulta elástica e as suas tabelas externas, tal como qualquer outra base de dados do SQL Server a que se ligaria à sua ferramenta. 

## <a name="best-practices"></a>Melhores práticas

* Certifique-se de que a base de dados de pontofinal de consulta elástica teve acesso à base de dados remota, permitindo o acesso dos Serviços Azure na sua configuração de firewall SQL DB. Certifique-se também de que a credencial fornecida na definição externa de fonte de dados pode entrar com sucesso na base de dados remota e tem as permissões para aceder à tabela remota.  
* A consulta elástica funciona melhor para consultas onde a maior parte da computação pode ser feita nas bases de dados remotas. Normalmente obtém o melhor desempenho de consulta com predicados de filtro seletivo que podem ser avaliados nas bases de dados remotas ou juntas que podem ser executadas completamente na base de dados remota. Outros padrões de consulta podem precisar de carregar grandes quantidades de dados a partir da base de dados remota e podem ter um desempenho fraco. 

## <a name="next-steps"></a>Passos seguintes

* Para uma visão geral da consulta elástica, consulte a visão geral da [consulta elástica](sql-database-elastic-query-overview.md).
* Para um tutorial de partição vertical, consulte [Iniciar-se com consulta de base de dados cruzada (partição vertical)](sql-database-elastic-query-getting-started-vertical.md).
* Para um tutorial de partição horizontal (sharding), consulte [Iniciar-se com consulta elástica para partição horizontal (sharding)](sql-database-elastic-query-getting-started.md).
* Para consultas de sintaxe e amostras para dados horizontalmente divididos, consulte dados [horizontalmente divididos)](sql-database-elastic-query-horizontal-partitioning.md)
* Consulte o [sp\_execute \_o controlo remoto](https://msdn.microsoft.com/library/mt703714) para um procedimento armazenado que execute uma declaração Transact-SQL numa única base de dados remota do Azure SQL ou num conjunto de bases de dados que servem como fragmentos num esquema de partição horizontal.


<!--Image references-->
[1]: ./media/sql-database-elastic-query-vertical-partitioning/verticalpartitioning.png


<!--anchors-->
