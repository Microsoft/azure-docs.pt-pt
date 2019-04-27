---
title: Consultas entre bases de dados na cloud com esquema diferente | Documentos da Microsoft
description: como configurar a consultas entre bases de dados através de partições verticais
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: WenJason
ms.author: v-jay
ms.reviewer: sstein
manager: digimobile
origin.date: 01/25/2019
ms.date: 02/25/2019
ms.openlocfilehash: e7ba8057cd22c5cc1080b4a6d95f17bf76d4acb2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60585428"
---
# <a name="query-across-cloud-databases-with-different-schemas-preview"></a>Consultar entre bases de dados de cloud com esquemas diferentes (pré-visualização)

![Consultar entre tabelas em diferentes bases de dados][1]

Bases de dados particionados verticalmente utilizam conjuntos de tabelas diferentes em diferentes bases de dados. Isso significa que o esquema é diferente em diferentes bases de dados. Por exemplo, todas as tabelas para o inventário estão num banco de dados enquanto todas as tabelas relacionadas com a gestão de contas estão numa segunda base de dados. 

## <a name="prerequisites"></a>Pré-requisitos

* O utilizador tem de ter permissão de alterar qualquer origem de dados externa. Esta permissão está incluída com a permissão ALTER DATABASE.
* São necessárias permissões de alterar qualquer origem de dados externa para fazer referência à origem de dados subjacente.

## <a name="overview"></a>Descrição geral

> [!NOTE]
> Ao contrário com a criação de partições horizontais, estas instruções DDL não dependem de definir uma camada de dados com um mapa de partições horizontais através da biblioteca de cliente da base de dados elástica.
>

1. [CRIAR CHAVE MESTRA](https://msdn.microsoft.com/library/ms174382.aspx)
2. [CRIAR UM ÂMBITO DE BASE DE DADOS CREDENCIAL](https://msdn.microsoft.com/library/mt270260.aspx)
3. [CRIAR ORIGEM DE DADOS EXTERNA](https://msdn.microsoft.com/library/dn935022.aspx)
4. [CRIAR TABELA EXTERNA](https://msdn.microsoft.com/library/dn935021.aspx) 

## <a name="create-database-scoped-master-key-and-credentials"></a>Criar chave mestra do banco de dados no âmbito e as credenciais

A credencial é utilizada pela consulta elástica para ligar aos seus bancos de dados remotos.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'master_key_password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]

> [!NOTE]
> Certifique-se de que o `<username>` não inclui quaisquer **"\@servername"** sufixo. 
>

## <a name="create-external-data-sources"></a>Criar origens de dados externas

Sintaxe:

    <External_Data_Source> ::=
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH 
               (TYPE = RDBMS,
                LOCATION = '<fully_qualified_server_name>',
                DATABASE_NAME = '<remote_database_name>',  
                CREDENTIAL = <credential_name> 
                ) [;] 

> [!IMPORTANT]
> O parâmetro de tipo deve ser definido como **RDBMS**. 
>

### <a name="example"></a>Exemplo

O exemplo a seguir ilustra o uso da instrução CREATE para origens de dados externas. 

    CREATE EXTERNAL DATA SOURCE RemoteReferenceData 
    WITH 
    ( 
        TYPE=RDBMS, 
        LOCATION='myserver.database.chinacloudapi.cn', 
        DATABASE_NAME='ReferenceData', 
        CREDENTIAL= SqlUser 
    ); 

Para obter a lista de origens de dados externas atual: 

    select * from sys.external_data_sources; 

### <a name="external-tables"></a>Tabelas externas

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

O exemplo seguinte mostra como obter a lista de tabelas externas da base de dados atual: 

    select * from sys.external_tables; 

### <a name="remarks"></a>Observações

Consulta elástica estende a sintaxe de tabela externa existente para definir tabelas externas que utilizam a origens de dados externas do tipo RDBMS. Uma definição de tabela externa para a criação de partições verticais abrange os seguintes aspetos: 

* **Esquema**: A tabela externa DDL define um esquema que podem utilizar as suas consultas. O esquema fornecido na sua definição de tabela externa tem de corresponder ao esquema das tabelas na base de dados remoto onde estão armazenados os dados reais. 
* **Referência da base de dados remota**: A tabela externa DDL refere-se a uma origem de dados externa. A origem de dados externo Especifica o nome do servidor de base de dados SQL e o nome de base de dados da base de dados remoto onde estão armazenados os dados da tabela real. 

A sintaxe para criar tabelas externas usando uma origem de dados externos, conforme descrito na secção anterior, é o seguinte: 

A cláusula DATA_SOURCE define a origem de dados externa (ou seja, a base de dados remota em caso de criação de partições verticais) que é utilizada para a tabela externa.  

As cláusulas SCHEMA_NAME e OBJECT_NAME fornecem a capacidade de mapear a definição de tabela externa para uma tabela num esquema diferente na base de dados remoto ou a uma tabela com um nome diferente, respectivamente. Isto é útil se pretender definir uma tabela externa para uma exibição de catálogo ou DMV na sua base de dados remoto - ou qualquer outra situação em que o nome da tabela remota já estiver atribuído localmente.  

A seguinte instrução DDL ignora uma definição de tabela externa existente do catálogo local. Ela não afeta a base de dados remota. 

    DROP EXTERNAL TABLE [ [ schema_name ] . | schema_name. ] table_name[;]  

**Permissões para criar/DROP tabela externa**: São necessárias permissões de alterar qualquer origem de dados externa para a tabela externa DDL que também é necessária para fazer referência à origem de dados subjacente.  

## <a name="security-considerations"></a>Considerações de segurança

Os utilizadores com acesso à tabela externa automaticamente obtém acesso às tabelas subjacentes remotos sob a credencial fornecida na definição de origem de dados externa. Deve gerenciar cuidadosamente o acesso à tabela externa para evitar indesejada de elevação de privilégios através da credencial da origem de dados externo. Permissões de SQL regulares podem ser utilizadas para conceder ou REVOGAR o acesso a uma tabela externa como se fosse uma tabela normal.  

## <a name="example-querying-vertically-partitioned-databases"></a>Exemplo: consultar verticalmente particionada bases de dados

A seguinte consulta executa uma junção de três vias entre as duas tabelas locais de pedidos e de linhas da ordem e a tabela remota para os clientes. Este é um exemplo do caso de utilização de dados de referência para a consulta elástica: 

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

## <a name="stored-procedure-for-remote-t-sql-execution-spexecuteremote"></a>Procedimento para execução remota de T-SQL armazenado: sp\_execute_remote

Consulta elástica também introduz um procedimento armazenado que fornece acesso direto à base de dados remota. O procedimento armazenado é chamado [sp\_execute \_remoto](https://msdn.microsoft.com/library/mt703714) e pode ser utilizado para executar procedimentos armazenados remotos ou o código T-SQL na base de dados remota. Ele usa os seguintes parâmetros: 

* Nome da origem de dados (nvarchar): O nome da origem de dados externa do tipo RDBMS. 
* Consulta (nvarchar): A consulta de T-SQL a ser executado na base de dados remota. 
* Declaração de parâmetro (nvarchar) - opcional: Cadeia com os dados de definições de tipo para os parâmetros utilizados no parâmetro de consulta (como sp_executesql). 
* Lista de valores parâmetro - opcional: Lista separada por vírgulas de valores de parâmetro (como sp_executesql).

O sp\_executar\_remoto da origem de dados externo fornecida nos parâmetros de invocação para executar a instrução T-SQL específica na base de dados remota. Utiliza a credencial da origem de dados externos para ligar à base de dados remota.  

Exemplo: 

```sql
    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse' 
```

## <a name="connectivity-for-tools"></a>Conectividade de ferramentas

Pode utilizar cadeias de ligação normais do SQL Server para ligar as ferramentas de integração BI e os dados para bases de dados no servidor de BD SQL com a consulta elástica ativada e as tabelas externas definidas. Certifique-se de que o SQL Server é suportado como uma origem de dados para a sua ferramenta. Em seguida, consulte a base de dados de consulta elástica e suas tabelas externas, assim como qualquer outro SQL Server da base de dados que iria a estabelecer ligação com a sua ferramenta. 

## <a name="best-practices"></a>Melhores práticas

* Certifique-se de que a base de dados do ponto final de consulta elástica, deu acesso à base de dados remoto ao permitir o acesso dos serviços do Azure na sua configuração de firewall da BD SQL. Certifique-se também de que a credencial fornecida na definição de origem de dados externa pode iniciar com êxito na base de dados remoto e tem as permissões para aceder à tabela remota.  
* Consulta elástica funciona melhor para consultas em que a maioria da computação pode ser feita nas bases de dados remotas. Normalmente, pode obter o melhor desempenho de consulta com predicados de filtro seletiva que pode ser avaliado nos bancos de dados remotos ou associações que podem ser realizadas completamente no banco de dados remoto. Outros padrões de consulta poderão ter de carregar grandes quantidades de dados do banco de dados remoto e podem ter um mau desempenho. 

## <a name="next-steps"></a>Passos Seguintes

* Para obter uma descrição geral de consulta elástica, veja [descrição geral de consulta elástica](sql-database-elastic-query-overview.md).
* Para obter um tutorial de criação de partições vertical, consulte [guia de introdução consultas entre bases de dados (criação de partições verticais)](sql-database-elastic-query-getting-started-vertical.md).
* Para obter um tutorial de criação de partições horizontais (fragmentação), consulte [guia de introdução consulta elástica para a criação de partições horizontais (fragmentação)](sql-database-elastic-query-getting-started.md).
* Para consultas de exemplo de sintaxe e para dados particionados horizontalmente, consulte [horizontalmente a consulta particionados dados)](sql-database-elastic-query-horizontal-partitioning.md)
* Ver [sp\_execute \_remoto](https://msdn.microsoft.com/library/mt703714) para um procedimento armazenado que executa uma instrução de Transact-SQL num único remoto SQL Database do Azure ou um conjunto de bases de dados que serve como fragmentos num esquema de partições horizontal.


<!--Image references-->
[1]: ./media/sql-database-elastic-query-vertical-partitioning/verticalpartitioning.png


<!--anchors-->