---
title: Consultar em bancos de dados de nuvem com esquema diferente | Microsoft Docs
description: como configurar consultas entre bancos de dados em partições verticais
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
ms.openlocfilehash: 5657490474a401d9e3074ed6ab250a34ef0a5d8d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568535"
---
# <a name="query-across-cloud-databases-with-different-schemas-preview"></a>Consultar em bancos de dados de nuvem com esquemas diferentes (versão prévia)

![Consultar em tabelas em bancos de dados diferentes][1]

Os bancos de dados particionados verticalmente usam conjuntos diferentes de tabelas em bancos de dados diferentes. Isso significa que o esquema é diferente em bancos de dados diferentes. Por exemplo, todas as tabelas para o inventário estão em um banco de dados enquanto todas as tabelas relacionadas à contabilidade estão em um segundo banco de dados. 

## <a name="prerequisites"></a>Pré-requisitos

* O usuário deve ter a permissão alterar qualquer fonte de dados externa. Essa permissão está incluída com a permissão ALTER DATABASE.
* As permissões alterar qualquer fonte de dados externa são necessárias para fazer referência à fonte de dados subjacente.

## <a name="overview"></a>Descrição geral

> [!NOTE]
> Ao contrário do particionamento horizontal, essas instruções DDL não dependem da definição de uma camada de dados com um mapa de fragmentos por meio da biblioteca de cliente do banco de dado elástico.
>

1. [CRIAR CHAVE MESTRA](https://msdn.microsoft.com/library/ms174382.aspx)
2. [CRIAR CREDENCIAL NO ESCOPO DO BANCO DE DADOS](https://msdn.microsoft.com/library/mt270260.aspx)
3. [CRIAR FONTE DE DADOS EXTERNA](https://msdn.microsoft.com/library/dn935022.aspx)
4. [CRIAR TABELA EXTERNA](https://msdn.microsoft.com/library/dn935021.aspx) 

## <a name="create-database-scoped-master-key-and-credentials"></a>Criar chave mestra no escopo do banco de dados e credenciais

A credencial é usada pela consulta elástica para se conectar aos seus bancos de dados remotos.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'master_key_password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]

> [!NOTE]
> Verifique se o `<username>` não inclui nenhum sufixo **"\@ServerName"** . 
>

## <a name="create-external-data-sources"></a>Criar fontes de dados externas

Sintaxe

    <External_Data_Source> ::=
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH 
               (TYPE = RDBMS,
                LOCATION = ’<fully_qualified_server_name>’,
                DATABASE_NAME = ‘<remote_database_name>’,  
                CREDENTIAL = <credential_name> 
                ) [;] 

> [!IMPORTANT]
> O parâmetro de tipo deve ser definido como **RDBMS**. 
>

### <a name="example"></a>Exemplo

O exemplo a seguir ilustra o uso da instrução CREATE para fontes de dados externas. 

    CREATE EXTERNAL DATA SOURCE RemoteReferenceData 
    WITH 
    ( 
        TYPE=RDBMS, 
        LOCATION='myserver.database.windows.net', 
        DATABASE_NAME='ReferenceData', 
        CREDENTIAL= SqlUser 
    ); 

Para recuperar a lista de fontes de dados externas atuais: 

    select * from sys.external_data_sources; 

### <a name="external-tables"></a>Tabelas externas

Sintaxe

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

O exemplo a seguir mostra como recuperar a lista de tabelas externas do banco de dados atual: 

    select * from sys.external_tables; 

### <a name="remarks"></a>Observações

A consulta elástica estende a sintaxe da tabela externa existente para definir tabelas externas que usam fontes de dados externas do tipo RDBMS. Uma definição de tabela externa para o particionamento vertical abrange os seguintes aspectos: 

* **Esquema**: A DDL da tabela externa define um esquema que suas consultas podem usar. O esquema fornecido em sua definição de tabela externa precisa corresponder ao esquema das tabelas no banco de dados remoto em que o dado real está armazenado. 
* **Referência de banco de dados remoto**: A tabela externa DDL refere-se a uma fonte de dados externa. A fonte de dados externa especifica o nome do servidor do banco de dado SQL e o nome do banco de dados remoto em que estão armazenados o dado da tabela real. 

Usando uma fonte de dados externa, conforme descrito na seção anterior, a sintaxe para criar tabelas externas é a seguinte: 

A cláusula DATA_SOURCE define a fonte de dados externa (ou seja, o banco de dado remoto em caso de particionamento vertical) que é usada para a tabela externa.  

As cláusulas SCHEMA_NAME e OBJECT_NAME fornecem a capacidade de mapear a definição da tabela externa para uma tabela em um esquema diferente no banco de dados remoto ou para uma tabela com um nome diferente, respectivamente. Isso será útil se você quiser definir uma tabela externa para uma exibição de catálogo ou DMV no seu banco de dados remoto – ou em qualquer outra situação em que o nome da tabela remota já esteja em uso localmente.  

A instrução DDL a seguir descarta uma definição de tabela externa existente do catálogo local. Ele não afeta o banco de dados remoto. 

    DROP EXTERNAL TABLE [ [ schema_name ] . | schema_name. ] table_name[;]  

**Permissões para criar/DEScartar tabela externa**: As permissões alterar qualquer fonte de dados externa são necessárias para a DDL de tabela externa, que também é necessária para fazer referência à fonte de dados subjacente.  

## <a name="security-considerations"></a>Considerações de segurança

Os usuários com acesso à tabela externa têm acesso automaticamente às tabelas remotas subjacentes sob a credencial fornecida na definição da fonte de dados externa. Você deve gerenciar cuidadosamente o acesso à tabela externa para evitar a elevação indesejada de privilégios por meio da credencial da fonte de dados externa. As permissões SQL regulares podem ser usadas para conceder ou REVOGAr o acesso a uma tabela externa como se fosse uma tabela normal.  

## <a name="example-querying-vertically-partitioned-databases"></a>Exemplo: consultando bancos de dados particionados verticalmente

A consulta a seguir executa uma junção de três vias entre as duas tabelas locais para pedidos e linhas de ordem e a tabela remota para clientes. Este é um exemplo do caso de uso de dados de referência para a consulta elástica: 

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

## <a name="stored-procedure-for-remote-t-sql-execution-spexecuteremote"></a>Procedimento armazenado para execução remota de T-SQL:\_SP execute_remote

A consulta elástica também apresenta um procedimento armazenado que fornece acesso direto ao banco de dados remoto. O procedimento armazenado é chamado [SP\_execute \_Remote](https://msdn.microsoft.com/library/mt703714) e pode ser usado para executar procedimentos armazenados remotos ou código T-SQL no banco de dados remoto. Ele usa os seguintes parâmetros: 

* Nome da fonte de dados (nvarchar): O nome da fonte de dados externa do tipo RDBMS. 
* Consulta (nvarchar): A consulta T-SQL a ser executada no banco de dados remoto. 
* Declaração de parâmetro (nvarchar) – opcional: Cadeia de caracteres com definições de tipo de dados para os parâmetros usados no parâmetro de consulta (como sp_executesql). 
* Lista de valores de parâmetro-opcional: Lista separada por vírgulas de valores de parâmetro (como sp_executesql).

O SP\_execute\_Remote usa a fonte de dados externa fornecida nos parâmetros de invocação para executar a instrução T-SQL especificada no banco de dados remoto. Ele usa a credencial da fonte de dados externa para se conectar ao banco de dado remoto.  

Exemplo: 

```sql
    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse' 
```

## <a name="connectivity-for-tools"></a>Conectividade para ferramentas

Você pode usar cadeias de conexão SQL Server regulares para conectar suas ferramentas de integração de BI e de dados a bancos de dado no servidor de BD SQL que tem uma consulta elástica habilitada e tabelas externas definidas. Verifique se SQL Server tem suporte como uma fonte de dados para sua ferramenta. Em seguida, consulte o banco de dados de consulta elástica e suas tabelas externas, assim como qualquer outro banco de dados SQL Server ao qual você se conectaria com sua ferramenta. 

## <a name="best-practices"></a>Melhores práticas

* Verifique se o banco de dados de ponto de extremidade de consulta elástica recebeu acesso ao banco de dados remoto habilitando o acesso para serviços do Azure em sua configuração de firewall de banco de dados SQL. Além disso, certifique-se de que a credencial fornecida na definição da fonte de dados externa possa fazer logon com êxito no banco de dado remoto e tenha as permissões para acessar a tabela remota.  
* A consulta elástica funciona melhor para consultas em que a maior parte da computação pode ser feita nos bancos de dados remotos. Normalmente, você obtém o melhor desempenho de consulta com predicados de filtro seletivo que podem ser avaliados nos bancos de dados remotos ou junções que podem ser executadas completamente no banco de dados remoto. Outros padrões de consulta podem precisar carregar grandes quantidades de dados do banco de dado remoto e podem funcionar mal. 

## <a name="next-steps"></a>Passos Seguintes

* Para obter uma visão geral da consulta elástica, consulte [visão geral da consulta elástica](sql-database-elastic-query-overview.md).
* Para obter um tutorial de particionamento vertical, consulte [introdução à consulta entre bancos de dados (particionamento vertical)](sql-database-elastic-query-getting-started-vertical.md).
* Para obter um tutorial horizontal de particionamento (fragmentação), consulte [introdução à consulta elástica para particionamento horizontal (fragmentação)](sql-database-elastic-query-getting-started.md).
* Para obter sintaxe e exemplos de consultas para dados particionados horizontalmente, consulte [consultando dados particionados horizontalmente)](sql-database-elastic-query-horizontal-partitioning.md)
* Confira [\_SP \_execute Remote](https://msdn.microsoft.com/library/mt703714) para um procedimento armazenado que execute uma instrução Transact-SQL em um único banco de dados SQL do Azure remoto ou em conjunto de dados que servem como fragmentos em um esquema de particionamento horizontal.


<!--Image references-->
[1]: ./media/sql-database-elastic-query-vertical-partitioning/verticalpartitioning.png


<!--anchors-->
