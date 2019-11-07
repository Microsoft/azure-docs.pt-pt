---
title: Segurança de instância gerenciada do banco de dados SQL do Azure usando entidades do Azure AD Server (logons)
description: Saiba mais sobre técnicas e recursos para proteger uma instância gerenciada no banco de dados SQL do Azure e usar entidades de segurança do servidor do Azure AD (logons)
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: GitHubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 11/06/2019
ms.openlocfilehash: 4816b59c56f10a20f74777ad1badc11b59a599d1
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73689469"
---
# <a name="tutorial-managed-instance-security-in-azure-sql-database-using-azure-ad-server-principals-logins"></a>Tutorial: segurança de instância gerenciada no banco de dados SQL do Azure usando entidades do Azure AD Server (logons)

A instância gerenciada fornece quase todos os recursos de segurança que a última SQL Server local (Enterprise Edition) Mecanismo de Banco de Dados:

- Limitando o acesso em um ambiente isolado
- Usar mecanismos de autenticação que exigem identidade (Azure AD, autenticação SQL)
- Usar a autorização com associações baseadas em função e permissões
- Habilitar recursos de segurança

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> - Criar uma entidade de segurança (logon) do servidor do Azure Active Directory (AD) para uma instância gerenciada
> - Conceder permissões para entidades de segurança de servidor do Azure AD (logons) em uma instância gerenciada
> - Criar usuários do Azure AD de entidades de segurança do Azure AD Server (logons)
> - Atribuir permissões aos usuários do Azure AD e gerenciar a segurança do banco de dados
> - Usar representação com usuários do Azure AD
> - Usar consultas entre bancos de dados com usuários do Azure AD
> - Saiba mais sobre os recursos de segurança, como proteção contra ameaças, auditoria, máscara de dados e criptografia

Para saber mais, confira os artigos visão geral e [recursos](sql-database-managed-instance.md) da [instância gerenciada do banco de dados SQL do Azure](sql-database-managed-instance-index.yml) .

## <a name="prerequisites"></a>Pré-requisitos

Para concluir o tutorial, verifique se você tem os seguintes pré-requisitos:

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)
- Uma instância gerenciada do banco de dados SQL do Azure
  - Siga este artigo: [início rápido: criar uma instância gerenciada do banco de dados SQL do Azure](sql-database-managed-instance-get-started.md)
- É possível acessar sua instância gerenciada e [provisionar um administrador do Azure ad para a instância gerenciada](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance). Para saber mais, consulte:
    - [Conectar seu aplicativo a uma instância gerenciada](sql-database-managed-instance-connect-app.md) 
    - [Arquitetura de conectividade da instância gerenciada](sql-database-managed-instance-connectivity-architecture.md)
    - [Configurar e gerenciar a autenticação de Azure Active Directory com o SQL](sql-database-aad-authentication-configure.md)

## <a name="limiting-access-to-your-managed-instance"></a>Limitando o acesso à sua instância gerenciada

As instâncias gerenciadas podem ser acessadas por meio de um endereço IP privado. Assim como um ambiente SQL Server local isolado, os aplicativos ou usuários precisam acessar a rede de instância gerenciada (VNet) antes que uma conexão possa ser estabelecida. Para obter mais informações, consulte o seguinte artigo, [conectar seu aplicativo a uma instância gerenciada](sql-database-managed-instance-connect-app.md).

Também é possível configurar um ponto de extremidade de serviço na instância gerenciada, que permite conexões públicas, da mesma maneira que o banco de dados SQL do Azure. Para obter mais informações, consulte o seguinte artigo [Configurar o ponto de extremidade público na instância gerenciada do banco de dados SQL do Azure](sql-database-managed-instance-public-endpoint-configure.md).

> [!NOTE] 
> Mesmo com os pontos de extremidade de serviço habilitados, [as regras de firewall do banco de dados SQL](sql-database-firewall-configure.md) não se aplicam. A instância gerenciada tem seu próprio [firewall interno](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md) para gerenciar a conectividade.

## <a name="create-an-azure-ad-server-principal-login-for-a-managed-instance-using-ssms"></a>Criar uma entidade de segurança de servidor do Azure AD (logon) para uma instância gerenciada usando o SSMS

A primeira entidade de segurança de servidor do Azure AD (logon) pode ser criada pela conta de SQL Server padrão (não Azure AD) que é uma `sysadmin`ou o administrador do Azure AD para a instância gerenciada criada durante o processo de provisionamento. Para obter mais informações, consulte [provisionar um administrador de Azure Active Directory para sua instância gerenciada](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance). Essa funcionalidade foi alterada desde a [ga das entidades de segurança do Azure Ad Server](sql-database-aad-authentication-configure.md#new-azure-ad-admin-functionality-for-mi).

Consulte os seguintes artigos para obter exemplos de como se conectar à sua instância gerenciada:

- [Início rápido: configurar a VM do Azure para se conectar a uma instância gerenciada](sql-database-managed-instance-configure-vm.md)
- [Início rápido: configurar uma conexão ponto a site com uma instância gerenciada do local](sql-database-managed-instance-configure-p2s.md)

1. Faça logon em sua instância gerenciada usando uma conta de SQL Server padrão (não Azure AD) que seja uma `sysadmin` ou um administrador do Azure AD para MI, usando [SQL Server Management Studio](sql-database-managed-instance-configure-p2s.md#use-ssms-to-connect-to-the-managed-instance).

2. No Pesquisador de **objetos**, clique com o botão direito do mouse no servidor e escolha **nova consulta**.

3. Na janela de consulta, use a sintaxe a seguir para criar um logon para uma conta local do Azure AD:

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    Este exemplo cria um logon para a conta nativeuser@aadsqlmi.onmicrosoft.com.

    ```sql
    USE master
    GO
    CREATE LOGIN [nativeuser@aadsqlmi.onmicrosoft.com] FROM EXTERNAL PROVIDER
    GO
    ```

4. Na barra de ferramentas, selecione **executar** para criar o logon.

5. Verifique o logon recém-adicionado, executando o seguinte comando T-SQL:

    ```sql
    SELECT *  
    FROM sys.server_principals;  
    GO
    ```

    ![Native-login. png](media/sql-database-managed-instance-security-tutorial/native-login.png)

Para obter mais informações, consulte [Create login](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current).

## <a name="granting-permissions-to-allow-the-creation-of-managed-instance-logins"></a>Concedendo permissões para permitir a criação de logons de instância gerenciada

Para criar outras entidades de segurança do servidor do Azure AD (logons), SQL Server funções ou permissões devem ser concedidas à entidade de segurança (SQL ou Azure AD).

### <a name="sql-authentication"></a>Autenticação do SQL

- Se o logon for uma entidade de segurança SQL, somente os logons que fizerem parte da função `sysadmin` poderão usar o comando Create para criar logons para uma conta do Azure AD.

### <a name="azure-ad-authentication"></a>Autenticação do Azure AD

- Para permitir que a entidade de segurança de servidor do Azure AD recém-criada (logon) a capacidade de criar outros logons para outros usuários, grupos ou aplicativos do Azure AD, conceda o logon `sysadmin` ou a função de servidor `securityadmin`. 
- No mínimo, a permissão **ALTER ANY login** deve ser concedida à entidade de segurança de servidor do Azure AD (logon) para criar outras entidades de segurança do servidor do Azure AD (logons). 
- Por padrão, a permissão padrão concedida a entidades de segurança de servidor do Azure AD recém-criadas (logons) no mestre é: **conectar SQL** e **exibir qualquer banco de dados**.
- A função de servidor `sysadmin` pode ser concedida a muitas entidades de segurança de servidor do Azure AD (logons) em uma instância gerenciada.

Para adicionar o logon à função de servidor `sysadmin`:

1. Faça logon na instância gerenciada novamente ou use a conexão existente com o administrador do Azure AD ou a entidade de segurança do SQL que é um `sysadmin`.

1. No Pesquisador de **objetos**, clique com o botão direito do mouse no servidor e escolha **nova consulta**.

1. Conceda à entidade de segurança do servidor do Azure AD (logon) a função de servidor `sysadmin` usando a seguinte sintaxe T-SQL:

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER login_name
    GO
    ```

    O exemplo a seguir concede a função de servidor `sysadmin` ao logon nativeuser@aadsqlmi.onmicrosoft.com

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER [nativeuser@aadsqlmi.onmicrosoft.com]
    GO
    ```

## <a name="create-additional-azure-ad-server-principals-logins-using-ssms"></a>Criar entidades de segurança adicionais do servidor do Azure AD (logons) usando o SSMS

Depois que a entidade de segurança do servidor do Azure AD (logon) tiver sido criada e fornecida com privilégios de `sysadmin`, esse logon poderá criar logons adicionais usando a cláusula **do provedor externo** com **Create login**.

1. Conecte-se à instância gerenciada com a entidade de segurança de servidor do Azure AD (logon), usando SQL Server Management Studio. Insira o nome do host da instância gerenciada. Para autenticação no SSMS, há três opções para escolher ao fazer logon com uma conta do Azure AD:

   - Active Directory-universal com suporte a MFA
   - Active Directory-senha
   - Integrado ao Active Directory </br>

     ![SSMS-login-prompt. png](media/sql-database-managed-instance-security-tutorial/ssms-login-prompt.png)

     Para obter mais informações, consulte o seguinte artigo: [autenticação universal com Banco de dados SQL e SQL data warehouse (suporte do SSMS para MFA)](sql-database-ssms-mfa-authentication.md)

1. Selecione **Active Directory – universal com suporte a MFA**. Isso abre uma janela de logon da MFA (autenticação multifator). Entre com sua senha do Azure AD.

    ![MFA-login-prompt. png](media/sql-database-managed-instance-security-tutorial/mfa-login-prompt.png)

1. No **pesquisador de objetos**do SSMS, clique com o botão direito do mouse no servidor e escolha **nova consulta**.
1. Na janela de consulta, use a sintaxe a seguir para criar um logon para outra conta do Azure AD:

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    Este exemplo cria um logon para o usuário do Azure AD bob@aadsqlmi.net, cujo domínio aadsqlmi.net é federado com o aadsqlmi.onmicrosoft.com do Azure AD.

    Execute o comando T-SQL a seguir. As contas federadas do Azure AD são as substituições de instância gerenciada para logons e usuários locais do Windows.

    ```sql
    USE master
    GO
    CREATE LOGIN [bob@aadsqlmi.net] FROM EXTERNAL PROVIDER
    GO
    ```

1. Crie um banco de dados na instância gerenciada usando a sintaxe [CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current) . Este banco de dados será usado para testar logons de usuário na próxima seção.
    1. No Pesquisador de **objetos**, clique com o botão direito do mouse no servidor e escolha **nova consulta**.
    1. Na janela de consulta, use a sintaxe a seguir para criar um banco de dados chamado **MyMITestDB**.

        ```sql
        CREATE DATABASE MyMITestDB;
        GO
        ```

1. Crie um logon de instância gerenciada para um grupo no Azure AD. O grupo precisará existir no Azure AD antes que você possa adicionar o logon à instância gerenciada. Consulte [criar um grupo básico e adicionar membros usando Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Crie um grupo _myGroup_ e adicione membros a esse grupo.

1. Abra uma nova janela de consulta no SQL Server Management Studio.

    Este exemplo supõe que exista um grupo chamado _myGroup_ no Azure AD. Execute o seguinte comando:

    ```sql
    USE master
    GO
    CREATE LOGIN [mygroup] FROM EXTERNAL PROVIDER
    GO
    ```

1. Como um teste, faça logon na instância gerenciada com o logon ou grupo recém-criado. Abra uma nova conexão com a instância gerenciada e use o novo logon ao autenticar.
1. No Pesquisador de **objetos**, clique com o botão direito do mouse no servidor e escolha **nova consulta** para a nova conexão.
1. Verifique as permissões do servidor para a entidade de segurança de servidor do Azure AD recém-criada (logon) executando o seguinte comando:

      ```sql
      SELECT * FROM sys.fn_my_permissions (NULL, 'DATABASE')
      GO
      ```

> [!NOTE]
> Os usuários convidados do Azure AD têm suporte para logons de instância gerenciada, somente quando adicionados como parte de um grupo do Azure AD. Um usuário convidado do Azure AD é uma conta que é convidada para o Azure AD à qual a instância gerenciada pertence, de outro Azure AD. Por exemplo, joe@contoso.com (conta do Azure AD) ou steve@outlook.com (conta MSA) podem ser adicionados a um grupo no aadsqlmi do Azure AD. Depois que os usuários são adicionados a um grupo, um logon pode ser criado no banco de dados **mestre** de instância gerenciada para o grupo usando a sintaxe **Create login** . Os usuários convidados que são membros deste grupo podem se conectar à instância gerenciada usando seus logons atuais (por exemplo, joe@contoso.com ou steve@outlook.com).

## <a name="create-an-azure-ad-user-from-the-azure-ad-server-principal-login-and-give-permissions"></a>Criar um usuário do Azure AD da entidade de segurança de servidor do Azure AD (logon) e conceder permissões

A autorização para bancos de dados individuais funciona muito da mesma maneira na instância gerenciada como faz com SQL Server local. Um usuário pode ser criado a partir de um logon existente em um banco de dados e ser fornecido com permissões nesse banco de dados ou adicionado a uma função de banco de dados.

Agora que criamos um banco de dados chamado **MyMITestDB**e um logon que tem apenas permissões padrão, a próxima etapa é criar um usuário a partir desse logon. No momento, o logon pode se conectar à instância gerenciada e ver todos os bancos de dados, mas não pode interagir com os bancos de dados. Se você entrar com a conta do Azure AD que tem as permissões padrão e tentar expandir o banco de dados recém-criado, você verá o seguinte erro:

![SSMS-DB-not-ACCESSIBLE. png](media/sql-database-managed-instance-security-tutorial/ssms-db-not-accessible.png)

Para obter mais informações sobre como conceder permissões de banco de dados, consulte [introdução com permissões de mecanismo de banco de dados](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions).

### <a name="create-an-azure-ad-user-and-create-a-sample-table"></a>Criar um usuário do Azure AD e criar uma tabela de exemplo

1. Faça logon em sua instância gerenciada usando uma conta de `sysadmin` usando SQL Server Management Studio.
1. No Pesquisador de **objetos**, clique com o botão direito do mouse no servidor e escolha **nova consulta**.
1. Na janela de consulta, use a sintaxe a seguir para criar um usuário do Azure AD de uma entidade de segurança de servidor do Azure AD (logon):

    ```sql
    USE <Database Name> -- provide your database name
    GO
    CREATE USER user_name FROM LOGIN login_name
    GO
    ```

    O exemplo a seguir cria um usuário bob@aadsqlmi.net do logon bob@aadsqlmi.net:

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [bob@aadsqlmi.net] FROM LOGIN [bob@aadsqlmi.net]
    GO
    ```

1. Também há suporte para criar um usuário do Azure AD de uma entidade de segurança de servidor do Azure AD (logon) que seja um grupo.

    O exemplo a seguir cria um logon para o grupo _myGroup_ do Azure AD que existe no Azure AD.

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [mygroup] FROM LOGIN [mygroup]
    GO
    ```

    Todos os usuários que pertencem a **myGroup** podem acessar o banco de dados **MyMITestDB** .

    > [!IMPORTANT]
    > Ao criar um **usuário** de uma entidade de segurança de servidor do Azure AD (logon), especifique o user_name como o mesmo Login_name do **logon**.

    Para obter mais informações, consulte [criar usuário](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current).

1. Em uma nova janela de consulta, crie uma tabela de teste usando o seguinte comando T-SQL:

    ```sql
    USE MyMITestDB
    GO
    CREATE TABLE TestTable
    (
    AccountNum varchar(10),
    City varchar(255),
    Name varchar(255),
    State varchar(2)
    );
    ```

1. Crie uma conexão no SSMS com o usuário que foi criado. Você observará que não é possível ver a tabela **TestTable** criada pelo `sysadmin` anteriormente. Precisamos fornecer permissões ao usuário para ler dados do banco de dado.

1. Você pode verificar a permissão atual que o usuário tem executando o seguinte comando:

    ```sql
    SELECT * FROM sys.fn_my_permissions('MyMITestDB','DATABASE')
    GO
    ```

### <a name="add-users-to-database-level-roles"></a>Adicionar usuários a funções de nível de banco de dados

Para que o usuário veja os dados no banco de dado, podemos fornecer [funções de nível de dados](/sql/relational-databases/security/authentication-access/database-level-roles) ao usuário.

1. Faça logon em sua instância gerenciada usando uma conta de `sysadmin` usando SQL Server Management Studio.

1. No Pesquisador de **objetos**, clique com o botão direito do mouse no servidor e escolha **nova consulta**.

1. Conceda ao usuário do Azure AD a função de banco de dados `db_datareader` usando a seguinte sintaxe T-SQL:

    ```sql
    Use <Database Name> -- provide your database name
    ALTER ROLE db_datareader ADD MEMBER user_name
    GO
    ```

    O exemplo a seguir fornece o usuário bob@aadsqlmi.net e o grupo _myGroup_ com permissões `db_datareader` no banco de dados **MyMITestDB** :

    ```sql
    USE MyMITestDB
    GO
    ALTER ROLE db_datareader ADD MEMBER [bob@aadsqlmi.net]
    GO
    ALTER ROLE db_datareader ADD MEMBER [mygroup]
    GO
    ```

1. Verifique se o usuário do Azure AD criado no banco de dados existe executando o seguinte comando:

    ```sql
    SELECT * FROM sys.database_principals
    GO
    ```

1. Crie uma nova conexão com a instância gerenciada com o usuário que foi adicionado à função de `db_datareader`.
1. Expanda o banco de dados no **pesquisador de objetos** para ver a tabela.

    ![SSMS-Test-Table. png](media/sql-database-managed-instance-security-tutorial/ssms-test-table.png)

1. Abra uma nova janela de consulta e execute a seguinte instrução SELECT:

    ```sql
    SELECT *
    FROM TestTable
    ```

    Você consegue ver os dados da tabela? Você deve ver as colunas que estão sendo retornadas.

    ![SSMS-Test-Table-Query. png](media/sql-database-managed-instance-security-tutorial/ssms-test-table-query.png)

## <a name="impersonating-azure-ad-server-level-principals-logins"></a>Representando entidades de segurança no nível de servidor do Azure AD (logons)

A instância gerenciada dá suporte à representação de entidades de segurança no nível de servidor do Azure AD (logons).

### <a name="test-impersonation"></a>Representação de teste

1. Faça logon em sua instância gerenciada usando uma conta de `sysadmin` usando SQL Server Management Studio.

1. No Pesquisador de **objetos**, clique com o botão direito do mouse no servidor e escolha **nova consulta**.

1. Na janela de consulta, use o seguinte comando para criar um novo procedimento armazenado:

    ```sql
    USE MyMITestDB
    GO  
    CREATE PROCEDURE dbo.usp_Demo  
    WITH EXECUTE AS 'bob@aadsqlmi.net'  
    AS  
    SELECT user_name();  
    GO
    ```

1. Use o comando a seguir para ver que o usuário que você está representando ao executar o procedimento armazenado é **bob\@aadsqlmi.net**.

    ```sql
    Exec dbo.usp_Demo
    ```

1. Teste a representação usando a instrução EXECUTE AS LOGIN:

    ```sql
    EXECUTE AS LOGIN = 'bob@aadsqlmi.net'
    GO
    SELECT SUSER_SNAME()
    REVERT
    GO
    ```

> [!NOTE]
> Somente as entidades de segurança no nível do SQL Server (logons) que fazem parte da função `sysadmin` podem executar as seguintes operações direcionadas a entidades do Azure AD: 
> - EXECUTAR COMO USUÁRIO
> - EXECUTAR COMO LOGON

## <a name="using-cross-database-queries-in-managed-instances"></a>Usando consultas entre bancos de dados em instâncias gerenciadas

Há suporte para consultas entre bancos de dados para contas do Azure AD com entidades de segurança do servidor do Azure AD (logons). Para testar uma consulta entre bancos de dados com um grupo do Azure AD, precisamos criar outro banco de dados e tabela. Você pode ignorar a criação de outro banco de dados e tabela, se já existir um.

1. Faça logon em sua instância gerenciada usando uma conta de `sysadmin` usando SQL Server Management Studio.
1. No Pesquisador de **objetos**, clique com o botão direito do mouse no servidor e escolha **nova consulta**.
1. Na janela de consulta, use o seguinte comando para criar um banco de dados chamado **MyMITestDB2** e a tabela denominada **TestTable2**:

    ```sql
    CREATE DATABASE MyMITestDB2;
    GO
    USE MyMITestDB2
    GO
    CREATE TABLE TestTable2
    (
    EmpId varchar(10),
    FirstName varchar(255),
    LastName varchar(255),
    Status varchar(10)
    );
    ```

1. Em uma nova janela de consulta, execute o seguinte comando para criar o usuário _myGroup_ no novo banco de dados **MyMITestDB2**e conceda permissões SELECT nesse banco de dados para _myGroup_:

    ```sql
    USE MyMITestDB2
    GO
    CREATE USER [mygroup] FROM LOGIN [mygroup]
    GO
    GRANT SELECT TO [mygroup]
    GO
    ```

1. Entre na instância gerenciada usando SQL Server Management Studio como um membro do grupo _myGroup_do Azure AD. Abra uma nova janela de consulta e execute a instrução SELECT do banco de dados cruzado:

    ```sql
    USE MyMITestDB
    SELECT * FROM MyMITestDB2..TestTable2
    GO
    ```

    Você deve ver os resultados da tabela de **TestTable2**.

## <a name="additional-scenarios-supported-for-azure-ad-server-principals-logins"></a>Cenários adicionais com suporte para entidades de segurança do servidor do Azure AD (logons)

- As execuções de trabalho e gerenciamento do SQL Agent têm suporte para entidades de segurança do servidor do Azure AD (logons).
- As operações de backup e restauração do banco de dados podem ser executadas por entidades de segurança do servidor do Azure AD (logons).
- [Auditoria](sql-database-managed-instance-auditing.md) de todas as instruções relacionadas a entidades de autenticação do servidor do Azure AD (logons) e eventos.
- Conexão de administrador dedicada para entidades de segurança do servidor do Azure AD (logons) que são membros da função de servidor `sysadmin`.
- As entidades de segurança do servidor do Azure AD (logons) têm suporte com o uso do [utilitário sqlcmd](/sql/tools/sqlcmd-utility) e da ferramenta de [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) .
- Os gatilhos de logon têm suporte para eventos de logon provenientes de entidades de segurança de servidor do Azure AD (logons).
- Service Broker e o email de BD podem ser configurados usando entidades de segurança do servidor do Azure AD (logons).


## <a name="next-steps"></a>Passos seguintes

### <a name="enable-security-features"></a>Habilitar recursos de segurança

Consulte o artigo recursos de [segurança de funcionalidades de instância gerenciada](sql-database-managed-instance.md#azure-sql-database-security-features) a seguir para obter uma lista abrangente de maneiras de proteger seu banco de dados. Os seguintes recursos de segurança são discutidos:

- [Auditoria de instância gerenciada](sql-database-managed-instance-auditing.md) 
- [Sempre encriptado](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [Deteção de ameaças](sql-database-managed-instance-threat-detection.md) 
- [Máscara de dados dinâmica](/sql/relational-databases/security/dynamic-data-masking)
- [Segurança em nível de linha](/sql/relational-databases/security/row-level-security) 
- [TDE (Transparent Data Encryption)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="managed-instance-capabilities"></a>Funcionalidades de instância gerida

Para obter uma visão geral completa de um recurso de instância gerenciada, consulte:

> [!div class="nextstepaction"]
> [Recursos de instância gerenciada](sql-database-managed-instance.md)
