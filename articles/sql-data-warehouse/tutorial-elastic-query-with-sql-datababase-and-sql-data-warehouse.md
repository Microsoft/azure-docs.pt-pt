---
title: 'Tutorial: Consulta elástica com o armazém de dados SQL do Azure | Documentos da Microsoft'
description: Este tutorial utiliza a funcionalidade de consulta elástica para consultar a Azure SQL Data Warehouse da base de dados do Azure SQL.
services: sql-data-warehouse
author: hirokib
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/14/2018
ms.author: elbutter
ms.reviewer: igorstan
ms.openlocfilehash: 4dc0be045bceaaac4b71c653d82f7f9db834c3ec
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58486064"
---
# <a name="tutorial-use-elastic-query-to-access-data-in-azure-sql-data-warehouse-from-azure-sql-database"></a>Tutorial: Utilizar a consulta elástica da base de dados do Azure SQL para aceder a dados no Azure SQL Data Warehouse

Este tutorial utiliza a funcionalidade de consulta elástica para consultar a Azure SQL Data Warehouse da base de dados do Azure SQL. 

## <a name="prerequisites-for-the-tutorial"></a>Pré-requisitos para o tutorial

Antes de iniciar o tutorial, tem de ter os seguintes pré-requisitos:

1. Instalar o SQL Server Management Studio (SSMS).
2. Criar um servidor SQL do Azure com um armazém de dados e da base de dados dentro deste servidor.
3. Configure regras de firewall para aceder ao servidor SQL do Azure.

## <a name="set-up-connection-between-sql-data-warehouse-and-sql-database-instances"></a>Configurar a ligação entre instâncias do SQL Data Warehouse e a base de dados SQL 

1. Utilizando o SSMS ou outro cliente de consultas, abra uma nova consulta de base de dados **mestre** no seu servidor lógico.

2. Crie um início de sessão e um utilizador que representa a base de dados SQL para ligação de armazém de dados.

   ```sql
   CREATE LOGIN SalesDBLogin WITH PASSWORD = 'aReallyStrongPassword!@#';
   ```

3. Utilizando o SSMS ou outro cliente de consultas, abra uma nova consulta para o **instância de armazém de dados SQL** no seu servidor lógico.

4. Criar um utilizador na instância de armazém de dados com o início de sessão que criou no passo 2

   ```sql
   CREATE USER SalesDBUser FOR LOGIN SalesDBLogin;
   ```

5. Conceder permissões ao utilizador no passo 4, que teria como base de dados SQL gostaria de executar. Neste exemplo, permissão é apenas a ser concedida para SELECIONAR num esquema específico, que ilustra como a Microsoft pode limitar consultas da base de dados SQL a um domínio específico. 

   ```sql
   GRANT SELECT ON SCHEMA :: [dbo] TO SalesDBUser;
   ```

6. Utilizando o SSMS ou outro cliente de consultas, abra uma nova consulta para o **instância de base de dados SQL** no seu servidor lógico.

7. Se ainda não tiver uma, crie uma chave mestra. 

   ```sql
   CREATE MASTER KEY; 
   ```

8. Crie uma credencial com âmbito de base de dados com as credenciais que criou no passo 2.

   ```sql
   CREATE DATABASE SCOPED CREDENTIAL SalesDBElasticCredential
   WITH IDENTITY = 'SalesDBLogin',
   SECRET = 'aReallyStrongPassword@#!';
   ```

9. Crie uma origem de dados externa que aponta para a instância de armazém de dados.

   ```sql
   CREATE EXTERNAL DATA SOURCE EnterpriseDwSrc WITH 
       (TYPE = RDBMS, 
       LOCATION = '<SERVER NAME>.database.windows.net', 
       DATABASE_NAME = '<SQL DATA WAREHOUSE NAME>', 
       CREDENTIAL = SalesDBElasticCredential, 
   ) ;
   ```

10. Agora, pode criar tabelas externas que fazem referência a esta origem de dados externa. Consultas usando essas tabelas são enviadas para a instância de armazém de dados deve ser processada e enviada para a instância de base de dados.


## <a name="elastic-query-from-sql-database-to-sql-data-warehouse"></a>Consulta elástica da base de dados SQL ao SQL data warehouse

Os passos seguintes, irá criar uma tabela na nossa instância de armazém de dados com vários valores. Em seguida, demonstraremos como configurar a uma tabela externa para consultar a instância de armazém de dados da instância de base de dados.

1. Utilizando o SSMS ou outro cliente de consultas, abra uma nova consulta para o **o SQL Data Warehouse** no seu servidor lógico.

2. Submeter a consulta seguinte para criar uma **OrdersInformation** tabela na sua instância de armazém de dados.

   ```sql
   CREATE TABLE [dbo].[OrderInformation]
   ( 
       [OrderID] [int] NOT NULL, 
       [CustomerID] [int] NOT NULL 
   ) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8)
   ```

3. Utilizando o SSMS ou outro cliente de consultas, abra uma nova consulta para o **base de dados SQL** no seu servidor lógico.

4. Submeter a consulta seguinte para criar uma definição de tabela externa que aponta para o **OrderInformation** tabela na instância do armazém de dados.

   ```sql
   CREATE EXTERNAL TABLE [dbo].[OrderInformation]
   ( 
       [OrderID] [int] NOT NULL,
       [CustomerID] [int] NOT NULL 
   ) 
   WITH 
   (
        DATA_SOURCE = EnterpriseDwSrc,
    SCHEMA_NAME = N'dbo',
    OBJECT_NAME = N'OrderInformation'
   )
   ```

5. Observe que agora tem uma definição de tabela externa no seu **instância de base de dados SQL**.

   ![definição de tabela externa de consulta elástica](media/sql-data-warehouse-elastic-query-with-sql-database/elastic-query-external-table.png)


6. Submeta a consulta seguinte, que consulta a instância de armazém de dados. Deverá receber os cinco valores que inseriu na etapa 2. 

```sql
SELECT * FROM [dbo].[OrderInformation];
```

> [!NOTE]
>
> Tenha em atenção que, apesar de alguns valores, esta consulta leva um tempo considerável para retornar. Ao utilizar a consulta elástica com o armazém de dados, deve considerar os custos gerais de processamento de consultas e o movimento de durante a transmissão. Utilize a execução remota de consulta elástica o poder de computação, latência, é a prioridade.

Parabéns, tiver definido as noções básicas de consulta elástica. 

## <a name="next-steps"></a>Passos Seguintes
Para obter recomendações, veja [melhores práticas para utilizar a consulta elástica com o Azure SQL Data Warehouse](how-to-use-elastic-query-with-sql-data-warehouse.md).
