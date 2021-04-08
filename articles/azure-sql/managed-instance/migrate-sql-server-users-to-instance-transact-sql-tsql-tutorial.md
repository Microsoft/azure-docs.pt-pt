---
title: Migrar utilizadores e grupos sql server windows para SQL Managed Instance usando T-SQL
description: Saiba como migrar utilizadores e grupos windows em uma instância sql server para Azure SQL Managed Instance
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: seo-lt-2019, sqldbrb=1
ms.topic: tutorial
author: GitHubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 10/30/2019
ms.openlocfilehash: f2dd34ab7c6ee5be26836e4abb86960605ee44ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "84708686"
---
# <a name="tutorial-migrate-windows-users-and-groups-in-a-sql-server-instance-to-azure-sql-managed-instance-using-t-sql-ddl-syntax"></a>Tutorial: Migrar utilizadores e grupos windows em uma instância sql servidor para Azure SQL Managed Instance usando a sintaxe T-SQL DDL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

> [!NOTE]
> A sintaxe utilizada para migrar utilizadores e grupos para SQL Managed Instance neste artigo está em **pré-visualização pública**.

Este artigo leva-o através do processo de migração dos seus utilizadores e grupos windows no seu SQL Server para Azure SQL Managed Instance usando a sintaxe T-SQL.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> - Criar logins para SQL Server
> - Criar uma base de dados de teste para migração
> - Criar logins, utilizadores e funções
> - Faça cópia de segurança e restaure a sua base de dados para SQL Managed Instance (MI)
> - Migrar manualmente os utilizadores para o MI utilizando a sintaxe do UTILIZADOR ALTER
> - Testar a autenticação com os novos utilizadores mapeados

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, aplicam-se os seguintes pré-requisitos:

- O domínio Windows é federado com Azure Ative Directory (Azure AD).
- Acesso ao Ative Directory para criar utilizadores/grupos.
- Um servidor SQL existente no seu ambiente no local.
- Uma ocorrência gerida pelo SQL existente. Ver [Quickstart: Criar uma sql Managed Instance](instance-create-quickstart.md).
  - A `sysadmin` no SQL Managed Instance deve ser usado para criar logins AD Azure.
- [Criar um administrador AD Azure para sql Managed Instance](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).
- Pode ligar-se à sua SQL Managed Instance dentro da sua rede. Consulte os seguintes artigos para obter informações adicionais:
  - [Ligar a aplicação ao Azure SQL Managed Instance](connect-application-instance.md)
  - [Quickstart: Configurar uma ligação ponto-a-local a uma Instância Gerida Azure SQL a partir de instalações](point-to-site-p2s-configure.md)
  - [Configure o ponto final público em Azure SQL Gestd Instance](public-endpoint-configure.md)

## <a name="t-sql-ddl-syntax"></a>Sintaxe T-SQL DDL

Abaixo estão a sintaxe T-SQL DDL utilizada para apoiar a migração de utilizadores e grupos windows de uma instância sql server para SQL Managed Instance com autenticação AD Azure.

```sql
-- For individual Windows users with logins
ALTER USER [domainName\userName] WITH LOGIN = [loginName@domainName.com];

--For individual groups with logins
ALTER USER [domainName\groupName] WITH LOGIN=[groupName]
```

## <a name="arguments"></a>Argumentos

_domínioName_</br>
Especifica o nome de domínio do utilizador.

_nome de utilizador_</br>
Especifica o nome do utilizador identificado dentro da base de dados.

_= domainName.com de \@ nome de login_</br>
Remaps um utilizador para o login AZure AD

_nome de grupoName_</br>
Especifica o nome do grupo identificado dentro da base de dados.

## <a name="part-1-create-logins-in-sql-server-for-windows-users-and-groups"></a>Parte 1: Criar logins no SQL Server para utilizadores e grupos windows

> [!IMPORTANT]
> A seguinte sintaxe cria um utilizador e um login de grupo no seu SQL Server. Tem de se certificar de que o utilizador e o grupo existem dentro do seu Ative Directory (AD) antes de executar a sintaxe abaixo. </br> </br>
> Utilizadores: testUser1, testGroupUser </br>
> Grupo: migração - testGroupUser precisa pertencer ao grupo migratório em AD

O exemplo abaixo cria um login no SQL Server para uma conta chamada _testUser1_ sob o domínio _aadsqlmi_.

```sql
-- Sign into SQL Server as a sysadmin or a user that can create logins and databases

use master;  
go

-- Create Windows login
create login [aadsqlmi\testUser1] from windows;
go;

/** Create a Windows group login which contains one user [aadsqlmi\testGroupUser].
testGroupUser will need to be added to the migration group in Active Directory
**/
create login [aadsqlmi\migration] from windows;
go;


-- Check logins were created
select * from sys.server_principals;
go;
```

Crie uma base de dados para este teste.

```sql
-- Create a database called [migration]
create database migration
go
```

## <a name="part-2-create-windows-users-and-groups-then-add-roles-and-permissions"></a>Parte 2: Criar utilizadores e grupos windows, em seguida, adicionar papéis e permissões

Utilize a seguinte sintaxe para criar o utilizador de teste.

```sql
use migration;  
go

-- Create Windows user [aadsqlmi\testUser1] with login
create user [aadsqlmi\testUser1] from login [aadsqlmi\testUser1];
go
```

Verifique as permissões do utilizador:

```sql
-- Check the user in the Metadata
select * from sys.database_principals;
go

-- Display the permissions – should only have CONNECT permissions
select user_name(grantee_principal_id), * from sys.database_permissions;
go
```

Crie uma função e atribua o seu utilizador de teste a esta função:

```sql
-- Create a role with some permissions and assign the user to the role
create role UserMigrationRole;
go

grant CONNECT, SELECT, View DATABASE STATE, VIEW DEFINITION to UserMigrationRole;
go

alter role UserMigrationRole add member [aadsqlmi\testUser1];
go
```

Utilize a seguinte consulta para exibir os nomes de utilizador atribuídos a uma função específica:

```sql
-- Display user name assigned to a specific role
SELECT DP1.name AS DatabaseRoleName,
   isnull (DP2.name, 'No members') AS DatabaseUserName
 FROM sys.database_role_members AS DRM
 RIGHT OUTER JOIN sys.database_principals AS DP1
   ON DRM.role_principal_id = DP1.principal_id
 LEFT OUTER JOIN sys.database_principals AS DP2
   ON DRM.member_principal_id = DP2.principal_id
WHERE DP1.type = 'R'
ORDER BY DP1.name;
```

Utilize a seguinte sintaxe para criar um grupo. Em seguida, adicione o grupo ao papel `db_owner` .

```sql
-- Create Windows group
create user [aadsqlmi\migration] from login [aadsqlmi\migration];
go

-- ADD 'db_owner' role to this group
sp_addrolemember 'db_owner', 'aadsqlmi\migration';
go

--Check the db_owner role for 'aadsqlmi\migration' group
select is_rolemember('db_owner', 'aadsqlmi\migration')
go
-- Output  ( 1 means YES)
```

Crie uma tabela de testes e adicione alguns dados utilizando a seguinte sintaxe:

```sql
-- Create a table and add data
create table test ( a int, b int);
go

insert into test values (1,10)
go

-- Check the table values
select * from test;
go
```

## <a name="part-3-backup-and-restore-the-individual-user-database-to-sql-managed-instance"></a>Parte 3: Cópia de segurança e restauro da base de dados individual dos utilizadores para a SQL Managed Instance

Crie uma cópia de segurança da base de dados de migração utilizando as [bases de dados de cópia do](/sql/relational-databases/databases/copy-databases-with-backup-and-restore)artigo copiar e restaurar, ou utilizar a seguinte sintaxe:

```sql
use master;
go
backup database migration to disk = 'C:\Migration\migration.bak';
go
```

Siga o nosso [Quickstart: Restaurar uma base de dados para uma SQL Managed Instance](restore-sample-database-quickstart.md).

## <a name="part-4-migrate-users-to-sql-managed-instance"></a>Parte 4: Migrar os utilizadores para a SQL Managed Instance

Execute o comando ALTER USER para concluir o processo de migração em SQL Managed Instance.

1. Inscreva-se na sua SQL Managed Instance utilizando a conta de administração Azure AD para a SQL Managed Instance. Em seguida, crie o seu login AD Azure na SqL Managed Instance utilizando a seguinte sintaxe. Para obter mais informações, consulte [Tutorial: SQL Managed Instance security in Azure SQL Database using Azure AD server (logins)](aad-security-configure-tutorial.md).

    ```sql
    use master
    go

    -- Create login for AAD user [testUser1@aadsqlmi.net]
    create login [testUser1@aadsqlmi.net] from external provider
    go

    -- Create login for the Azure AD group [migration]. This group contains one user [testGroupUser@aadsqlmi.net]
    create login [migration] from external provider
    go

    --Check the two new logins
    select * from sys.server_principals
    go
    ```

1. Verifique a sua migração para obter a base de dados, tabela e principais corretas.

    ```sql
    -- Switch to the database migration that is already restored for MI
    use migration;
    go

    --Check if the restored table test exist and contain a row
    select * from test;
    go

    -- Check that the SQL on-premises Windows user/group exists  
    select * from sys.database_principals;
    go
    -- the old user aadsqlmi\testUser1 should be there
    -- the old group aadsqlmi\migration should be there
    ```

1. Utilize a sintaxe ALTER USER para mapear o utilizador no local para o login Azure AD.

    ```sql
    /** Execute the ALTER USER command to alter the Windows user [aadsqlmi\testUser1]
    to map to the Azure AD user testUser1@aadsqlmi.net
    **/
    alter user [aadsqlmi\testUser1] with login = [testUser1@aadsqlmi.net];
    go

    -- Check the principal
    select * from sys.database_principals;
    go
    -- New user testUser1@aadsqlmi.net should be there instead
    --Check new user permissions  - should only have CONNECT permissions
    select user_name(grantee_principal_id), * from sys.database_permissions;
    go

    -- Check a specific role
    -- Display Db user name assigned to a specific role
    SELECT DP1.name AS DatabaseRoleName,
    isnull (DP2.name, 'No members') AS DatabaseUserName
    FROM sys.database_role_members AS DRM
    RIGHT OUTER JOIN sys.database_principals AS DP1
    ON DRM.role_principal_id = DP1.principal_id
    LEFT OUTER JOIN sys.database_principals AS DP2
    ON DRM.member_principal_id = DP2.principal_id
    WHERE DP1.type = 'R'
    ORDER BY DP1.name;
    ```

1. Utilize a sintaxe ALTER USER para mapear o grupo no local para o login AZure AD.

    ```sql
    /** Execute ALTER USER command to alter the Windows group [aadsqlmi\migration]
    to the Azure AD group login [migration]
    **/
    alter user [aadsqlmi\migration] with login = [migration];
    -- old group migration is changed to Azure AD migration group
    go

    -- Check the principal
    select * from sys.database_principals;
    go

    --Check the group permission - should only have CONNECT permissions
    select user_name(grantee_principal_id), * from sys.database_permissions;
    go

    --Check the db_owner role for 'aadsqlmi\migration' user
    select is_rolemember('db_owner', 'migration')
    go
    -- Output 1 means 'YES'
    ```

## <a name="part-5-testing-azure-ad-user-or-group-authentication"></a>Parte 5: Teste de autenticação do utilizador azure ou da autenticação do grupo

Teste autenticando a SQL Managed Instance utilizando o utilizador previamente mapeado para o login AD Azure utilizando a sintaxe ALTER USER.

1. Inicie sessão no VM federado utilizando a sua subscrição de Instância Gerida Azure SQL como `aadsqlmi\testUser1`
1. Utilizando o SQL Server Management Studio (SSMS), inscreva-se na sua SQL Managed Instance utilizando a autenticação **integrada do Diretório Ativo,** conectando-se à base de dados `migration` .
    1. Também pode iniciar serção utilizando as testUser1@aadsqlmi.net credenciais com a opção SSMS **Ative Directory – Universal com suporte MFA.** No entanto, neste caso, não pode utilizar o mecanismo Single Sign On e deve escrever uma palavra-passe. Não precisará de usar um VM federado para iniciar sessão na sua SQL Managed Instance.
1. Como parte do membro da função **SELECT,** pode selecionar a partir da `test` tabela

    ```sql
    Select * from test  --  and see one row (1,10)
    ```

Teste autenticado para uma SQL Managed Instance utilizando um membro de um grupo Windows `migration` . O utilizador `aadsqlmi\testGroupUser` deveria ter sido adicionado ao grupo antes da `migration` migração.

1. Inicie sessão no VM federado utilizando a sua subscrição de Instância Gerida Azure SQL como `aadsqlmi\testGroupUser`
1. Utilizando SSMS com autenticação **integrada do Diretório Ativo,** ligue-se ao servidor Azure SQL Managed Instance e à base de dados `migration`
    1. Também pode iniciar serção utilizando as testGroupUser@aadsqlmi.net credenciais com a opção SSMS **Ative Directory – Universal com suporte MFA.** No entanto, neste caso, não pode utilizar o mecanismo Single Sign On e deve escrever uma palavra-passe. Não precisará de utilizar um VM federado para iniciar sessão na sua SQL Managed Instance.
1. Como parte do `db_owner` papel, pode criar uma nova mesa.

    ```sql
    -- Create table named 'new' with a default schema
    Create table dbo.new ( a int, b int)
    ```

> [!NOTE]
> Devido a um problema de design conhecido para a Base de Dados Azure SQL, uma declaração de tabela executada como membro de um grupo falhará com o seguinte erro: </br> </br>
> `Msg 2760, Level 16, State 1, Line 4
The specified schema name "testGroupUser@aadsqlmi.net" either does not exist or you do not have permission to use it.` </br> </br>
> A atual solução é criar uma tabela com um esquema existente no caso acima de <dbo.new>

## <a name="next-steps"></a>Passos seguintes

[Tutorial: Migrar o SqL Server para Azure SQL Managed Instance offline usando DMS](../../dms/tutorial-sql-server-to-managed-instance.md?toc=/azure/sql-database/toc.json)
