---
title: Migrar SQL Server usuários e grupos locais do Windows para a instância gerenciada do banco de dados SQL do Azure usando a sintaxe DDL T-SQL | Microsoft Docs
description: Saiba mais sobre como migrar SQL Server usuários e grupos locais do Windows para a instância gerenciada
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: GitHubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 10/22/2019
ms.openlocfilehash: ca0997010fef40c0927960c04588c031dd85fff8
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795281"
---
# <a name="tutorial-migrate-sql-server-on-premises-windows-users-and-groups-to-azure-sql-database-managed-instance-using-t-sql-ddl-syntax"></a>Tutorial: migrar SQL Server usuários e grupos locais do Windows para a instância gerenciada do banco de dados SQL do Azure usando a sintaxe DDL T-SQL

> [!NOTE]
> A sintaxe usada para migrar usuários e grupos para a instância gerenciada neste artigo está em **Visualização pública**.

Este artigo orienta você pelo processo de migração de seus usuários e grupos locais do Windows em sua SQL Server para uma instância gerenciada do banco de dados SQL do Azure existente usando a sintaxe T-SQL.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> - Criar logons para SQL Server
> - Criar um banco de dados de teste para migração
> - Criar logons, usuários e funções
> - Fazer backup e restaurar seu banco de dados para a instância gerenciada (MI)
> - Migre usuários manualmente para MI usando ALTER USER Syntax
> - Testando a autenticação com os novos usuários mapeados

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, os seguintes pré-requisitos se aplicam:

- O domínio do Windows é federado com o Azure Active Directory (AD do Azure).
- Acesso a Active Directory para criar usuários/grupos.
- Um SQL Server existente no seu ambiente local.
- Uma instância gerenciada existente. Consulte [início rápido: criar uma instância gerenciada do banco de dados SQL do Azure](sql-database-managed-instance-get-started.md).
- Você pode se conectar à sua instância gerenciada em sua rede. Consulte os seguintes artigos para obter informações adicionais: 
    - [Conectar seu aplicativo à instância gerenciada do banco de dados SQL do Azure](sql-database-managed-instance-connect-app.md)
    - [Início rápido: configurar uma conexão ponto a site com um Instância Gerenciada do Banco de Dados SQL do Azure local](sql-database-managed-instance-configure-p2s.md)
    - [Configurar o ponto final público na instância gerida da Base de Dados SQL do Azure](sql-database-managed-instance-public-endpoint-configure.md)

## <a name="t-sql-ddl-syntax"></a>Sintaxe DDL T-SQL

Veja abaixo a sintaxe DDL T-SQL usada para dar SQL Server suporte à migração de grupos e usuários locais do Windows para a instância gerenciada com a autenticação do Azure AD.

```sql
-- For individual Windows users with logins 
ALTER USER [domainName\userName] WITH LOGIN = [loginName@domainName.com]; 

--For individual groups with logins 
ALTER USER [domainName\groupName] WITH LOGIN=[groupName] 
```

## <a name="arguments"></a>Argumentos

_domainName_</br>
Especifica o nome de domínio do usuário.

_Usu_</br>
Especifica o nome do usuário identificado dentro do banco de dados.

_= loginName\@domainName.com_</br>
Remapeia um usuário para o logon do Azure AD

_groupName_</br>
Especifica o nome do grupo identificado dentro do banco de dados.

## <a name="part-1-create-logins-for-sql-server-on-premises-users-and-groups"></a>Parte 1: criar logons para SQL Server usuários e grupos locais

> [!IMPORTANT]
> A sintaxe a seguir cria um logon de usuário e grupo no seu SQL Server. Você precisará certificar-se de que o usuário e o grupo existam dentro de seu Active Directory (AD) antes de executar a sintaxe abaixo. </br> </br>
> Usuários: testUser1, testGroupUser </br>
> Grupo: Migration-testGroupUser precisa pertencer ao grupo de migração no AD

O exemplo a seguir cria um logon no SQL Server para uma conta chamada _testUser1_ no domínio _aadsqlmi_. 

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

Crie um banco de dados para este teste.

```sql
-- Create a database called [migration]
create database migration 
go
```

## <a name="part-2-create-windows-users-and-groups-then-add-roles-and-permissions"></a>Parte 2: criar usuários e grupos do Windows e adicionar funções e permissões

Use a sintaxe a seguir para criar o usuário de teste.

```sql
use migration;  
go 
 
-- Create Windows user [aadsqlmi\testUser1] with login 
create user [aadsqlmi\testUser1] from login [aadsqlmi\testUser1]; 
go 
```

Verifique as permissões do usuário:

```sql
-- Check the user in the Metadata 
select * from sys.database_principals; 
go 
 
-- Display the permissions – should only have CONNECT permissions
select user_name(grantee_principal_id), * from sys.database_permissions; 
go
```

Crie uma função e atribua o usuário de teste a essa função:

```sql 
-- Create a role with some permissions and assign the user to the role
create role UserMigrationRole; 
go

grant CONNECT, SELECT, View DATABASE STATE, VIEW DEFINITION to UserMigrationRole; 
go 

alter role UserMigrationRole add member [aadsqlmi\testUser1]; 
go 
``` 

Use a consulta a seguir para exibir nomes de usuário atribuídos a uma função específica:

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

Use a sintaxe a seguir para criar um grupo. Em seguida, adicione o grupo à função `db_owner`.

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

Crie uma tabela de teste e adicione alguns dados usando a seguinte sintaxe:

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

## <a name="part-3-backup-and-restore-the-individual-user-database-to-managed-instance"></a>Parte 3: fazer backup e restaurar o banco de dados de usuário individual para a instância gerenciada

Crie um backup do banco de dados de migração usando o artigo [copiar bancos de dados com backup e restauração](/sql/relational-databases/databases/copy-databases-with-backup-and-restore)ou use a seguinte sintaxe:

```sql
use master; 
go 
backup database migration to disk = 'C:\Migration\migration.bak'; 
go
```

Siga nosso [início rápido: restaurar um banco de dados para uma instância gerenciada](sql-database-managed-instance-get-started-restore.md).

## <a name="part-4-migrate-users-to-managed-instance"></a>Parte 4: migrar usuários para a instância gerenciada

Execute o comando ALTER USER para concluir o processo de migração na instância gerenciada.

1. Entre em sua instância gerenciada usando a conta do administrador do SQL para a instância gerenciada. Em seguida, crie seu logon do Azure AD na instância gerenciada usando a seguinte sintaxe:

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

1. Verifique a migração para o banco de dados, a tabela e as entidades de segurança corretas.

    ```sql
    -- Switch to the database migration that is already restored for MI 
    use migration;  
    go 
     
    --Check if the restored table test exist and contain a row 
    select * from test; 
    go 
     
    -- Check that the SQL on-premise Windows user/group exists  
    select * from sys.database_principals; 
    go 
    -- the old user aadsqlmi\testUser1 should be there 
    -- the old group aadsqlmi\migration should be there
    ```

1. Use a sintaxe ALTER USER para mapear o usuário local para o logon do Azure AD.

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

1. Use a sintaxe ALTER USER para mapear o grupo local para o logon do Azure AD.

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

## <a name="part-5-testing-azure-ad-user-or-group-authentication"></a>Parte 5: testando o usuário do Azure AD ou a autenticação de grupo

Testar a autenticação para a instância gerenciada usando o usuário mapeado anteriormente para o logon do Azure AD usando a sintaxe ALTER USER.
 
1. Faça logon na VM federada usando sua assinatura MI como `aadsqlmi\testUser1`
1. Usando o SQL Server Management Studio (SSMS), entre em sua instância gerenciada usando a autenticação **integrada Active Directory** , conectando-se ao banco de dados `migration`.
    1. Você também pode entrar usando as credenciais de testUser1@aadsqlmi.net com a opção SSMS **Active Directory – universal com suporte a MFA**. No entanto, nesse caso, você não pode usar o mecanismo de logon único e deve digitar uma senha. Você não precisará usar uma VM federada para fazer logon em sua instância gerenciada.
1. Como parte da **seleção**de membro da função, você pode selecionar na tabela `test`

    ```sql
    Select * from test  --  and see one row (1,10)
    ```


Teste a autenticação em uma instância gerenciada usando um membro de um grupo do Windows `migration`. O usuário `aadsqlmi\testGroupUser` deve ter sido adicionado ao grupo `migration` antes da migração.

1. Faça logon na VM federada usando sua assinatura MI como `aadsqlmi\testGroupUser` 
1. Usando o SSMS com **Active Directory autenticação integrada** , conecte-se ao servidor mi e ao banco de dados `migration`
    1. Você também pode entrar usando as credenciais de testGroupUser@aadsqlmi.net com a opção SSMS **Active Directory – universal com suporte a MFA**. No entanto, nesse caso, você não pode usar o mecanismo de logon único e deve digitar uma senha. Você não precisará usar uma VM federada para fazer logon em sua instância gerenciada. 
1. Como parte da função de `db_owner`, você pode criar uma nova tabela.

    ```sql
    -- Create table named 'new' with a default schema
    Create table dbo.new ( a int, b int)
    ```
                             
> [!NOTE] 
> Devido a um problema de design conhecido para o banco de BD SQL do Azure, uma instrução criar uma tabela executada como um membro de um grupo falhará com o seguinte erro: </br> </br>
> `Msg 2760, Level 16, State 1, Line 4 
The specified schema name "testGroupUser@aadsqlmi.net" either does not exist or you do not have permission to use it.` </br> </br>
> A solução alternativa atual é criar uma tabela com um esquema existente no caso acima < dbo. New >

## <a name="next-steps"></a>Passos seguintes

- [Tutorial: migrar SQL Server para uma instância gerenciada do banco de dados SQL do Azure offline usando DMS](../dms/tutorial-sql-server-to-managed-instance.md?toc=/azure/sql-database/toc.json)