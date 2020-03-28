---
title: Migrar utilizadores e grupos do SQL ServerWindows para a instância gerida usando t-SQL
description: Saiba como migrar o SQL Server no local utilizadores e grupos do Windows para gerir a instância
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.topic: tutorial
author: GitHubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 10/30/2019
ms.openlocfilehash: 2c8d7252b4e4ca8caa465727c0d2328c4aafaefb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "74227929"
---
# <a name="tutorial-migrate-sql-server-on-premises-windows-users-and-groups-to-azure-sql-database-managed-instance-using-t-sql-ddl-syntax"></a>Tutorial: Migrar O Servidor SQL no local utilizadores e grupos do Windows para a Base de Dados Azure SQL gerida através da sintaxe T-SQL DDL

> [!NOTE]
> A sintaxe utilizada para migrar utilizadores e grupos para gerir a instância neste artigo está em **pré-visualização pública.**

Este artigo leva-o através do processo de migração dos utilizadores e grupos do Windows no local no seu Servidor SQL para uma instância gerida pela Base de Dados Azure SQL existente utilizando a sintaxe T-SQL.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> - Criar logins para o Servidor SQL
> - Criar uma base de dados de teste para migração
> - Criar logins, utilizadores e funções
> - Backup e restaurar a sua base de dados para a instância gerida (MI)
> - Migrar manualmente os utilizadores para o MI utilizando a sintaxe ALTER USER
> - Testar a autenticação com os novos utilizadores mapeados

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, aplicam-se os seguintes pré-requisitos:

- O domínio Windows é federado com O Diretório Ativo Azure (Azure AD).
- Acesso ao Diretório Ativo para criar utilizadores/grupos.
- Um Servidor SQL existente no seu ambiente no local.
- Um caso gerido existente. Ver [Quickstart: Criar uma base de dados Azure SQL gerida](sql-database-managed-instance-get-started.md).
  - A `sysadmin` no caso gerido deve ser utilizado para criar logins Azure AD.
- [Crie um administrador Azure AD para a ocorrência gerida](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance).
- Pode ligar-se à sua instância gerida dentro da sua rede. Consulte os seguintes artigos para obter informações adicionais: 
    - [Ligar a aplicação à instância gerida de Base de Dados SQL do Azure](sql-database-managed-instance-connect-app.md)
    - [Quickstart: Configure uma ligação ponto-a-local a uma base de dados Azure SQL Gerida a partir do local](sql-database-managed-instance-configure-p2s.md)
    - [Configurar o ponto final público na instância gerida da Base de Dados SQL do Azure](sql-database-managed-instance-public-endpoint-configure.md)

## <a name="t-sql-ddl-syntax"></a>Sintaxe DDL T-SQL

Abaixo estão a sintaxe DDL T-SQL utilizada para suportar os utilizadores do SQL Server no local e a migração de grupos para a localização gerida com autenticação AD Azure.

```sql
-- For individual Windows users with logins 
ALTER USER [domainName\userName] WITH LOGIN = [loginName@domainName.com]; 

--For individual groups with logins 
ALTER USER [domainName\groupName] WITH LOGIN=[groupName] 
```

## <a name="arguments"></a>Argumentos

_nome de domínio_</br>
Especifica o nome de domínio do utilizador.

_userName_</br>
Especifica o nome do utilizador identificado dentro da base de dados.

_= loginName\@domainName.com_</br>
Remapeie um utilizador para o login azure ad

_nome de grupo_</br>
Especifica o nome do grupo identificado dentro da base de dados.

## <a name="part-1-create-logins-for-sql-server-on-premises-users-and-groups"></a>Parte 1: Criar logins para utilizadores e grupos do SQL Server no local

> [!IMPORTANT]
> A seguinte sintaxe cria um utilizador e um login de grupo no seu Servidor SQL. Terá de se certificar de que o utilizador e o grupo existem dentro do seu Diretório Ativo (AD) antes de executar a sintaxe abaixo. </br> </br>
> Utilizadores: testUser1, testGroupUser </br>
> Grupo: migração - testGroupUser precisa pertencer ao grupo de migração em AD

O exemplo abaixo cria um login no SQL Server para uma conta chamada _testUser1_ sob o domínio _adsqlmi_. 

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

## <a name="part-2-create-windows-users-and-groups-then-add-roles-and-permissions"></a>Parte 2: Criar utilizadores e grupos do Windows e, em seguida, adicionar funções e permissões

Utilize a seguinte sintaxe para criar o utilizador do teste.

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

Crie uma função e atribua o utilizador do teste a esta função:

```sql 
-- Create a role with some permissions and assign the user to the role
create role UserMigrationRole; 
go

grant CONNECT, SELECT, View DATABASE STATE, VIEW DEFINITION to UserMigrationRole; 
go 

alter role UserMigrationRole add member [aadsqlmi\testUser1]; 
go 
``` 

Utilize a seguinte consulta para exibir os nomes dos utilizadores atribuídos a uma função específica:

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

Utilize a seguinte sintaxe para criar um grupo. Em seguida, adicione `db_owner`o grupo ao papel .

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

Crie uma tabela de teste e adicione alguns dados utilizando a seguinte sintaxe:

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

## <a name="part-3-backup-and-restore-the-individual-user-database-to-managed-instance"></a>Parte 3: Cópia de segurança e restaurar a base de dados individual do utilizador para a instância gerida

Criar uma cópia de segurança da base de dados de migração utilizando o artigo [Copy Databases with Backup and Restore,](/sql/relational-databases/databases/copy-databases-with-backup-and-restore)ou use a seguinte sintaxe:

```sql
use master; 
go 
backup database migration to disk = 'C:\Migration\migration.bak'; 
go
```

Siga o nosso [Quickstart: Restaurar uma base de dados para uma instância gerida](sql-database-managed-instance-get-started-restore.md).

## <a name="part-4-migrate-users-to-managed-instance"></a>Parte 4: Utilizadores migrados para instância gerida

> [!NOTE]
> O administrador da Azure AD para a funcionalidade de instância gerida após a criação mudou. Para mais informações, consulte a funcionalidade de [administração da New Azure AD para O MI](sql-database-aad-authentication-configure.md#new-azure-ad-admin-functionality-for-mi).

Execute o comando ALTER USER para completar o processo de migração em instância gerida.

1. Inscreva-se na sua instância gerida utilizando a conta de administração da AD Azure para a sua gestão gerida. Em seguida, crie o seu login Azure AD na instância gerida utilizando a seguinte sintaxe. Para mais informações, consulte Tutorial: Segurança de instância gerida na Base de [Dados Azure SQL utilizando os diretores de servidores Azure AD (logins)](sql-database-managed-instance-aad-security-tutorial.md).

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

1. Verifique a sua migração para obter a base de dados, tabela e diretores corretos.

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

1. Utilize a sintaxe ALTER USER para mapear o utilizador no local para o login Da AD Azure.

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

1. Utilize a sintaxe ALTER USER para mapear o grupo no local para o login Azure AD.

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

## <a name="part-5-testing-azure-ad-user-or-group-authentication"></a>Parte 5: Testar o utilizador ou a autenticação do grupo Azure

Teste de autenticação para a instância gerida utilizando o utilizador previamente mapeado para o login Azure AD utilizando a sintaxe ALTER USER.
 
1. Inicie sessão no VM federado usando a sua subscrição MI como`aadsqlmi\testUser1`
1. Utilizando o SQL Server Management Studio (SSMS), inscreva-se na sua instância gerida `migration`utilizando a autenticação integrada do **Diretório Ativo,** conectando-se à base de dados .
    1. Também pode iniciar sessão utilizando as testUser1@aadsqlmi.net credenciais com a opção SSMS Ative **Diretório – Universal com suporte mFA**. No entanto, neste caso, não pode utilizar o mecanismo De Entrada Única e tem de escrever uma palavra-passe. Não precisa de usar um VM federado para iniciar sessão na sua instância gerida.
1. Como parte do membro do papel **SELECT,** pode selecionar a partir da `test` tabela

    ```sql
    Select * from test  --  and see one row (1,10)
    ```


Teste autenticando a uma instância gerida utilizando um `migration`membro de um grupo Windows . O `aadsqlmi\testGroupUser` utilizador deveria ter sido `migration` adicionado ao grupo antes da migração.

1. Inicie sessão no VM federado usando a sua subscrição MI como`aadsqlmi\testGroupUser` 
1. Utilização de SSMS com autenticação integrada de **Diretório Ativo,** ligue-se ao servidor MI e à base de dados`migration`
    1. Também pode iniciar sessão utilizando as testGroupUser@aadsqlmi.net credenciais com a opção SSMS Ative **Diretório – Universal com suporte mFA**. No entanto, neste caso, não pode utilizar o mecanismo De Entrada Única e tem de escrever uma palavra-passe. Não precisa de usar um VM federado para entrar na sua instância gerida. 
1. Como parte `db_owner` do papel, pode criar uma nova mesa.

    ```sql
    -- Create table named 'new' with a default schema
    Create table dbo.new ( a int, b int)
    ```
                             
> [!NOTE] 
> Devido a um problema de design conhecido para o Azure SQL DB, uma declaração de mesa executada como membro de um grupo falhará com o seguinte erro: </br> </br>
> `Msg 2760, Level 16, State 1, Line 4 
The specified schema name "testGroupUser@aadsqlmi.net" either does not exist or you do not have permission to use it.` </br> </br>
> A atual suposição é criar uma tabela com um esquema existente no caso acima <dbo.new>

## <a name="next-steps"></a>Passos seguintes

- [Tutorial: Migrar o Servidor SQL para uma Base de Dados Azure SQL gerida offline usando DMS](../dms/tutorial-sql-server-to-managed-instance.md?toc=/azure/sql-database/toc.json)