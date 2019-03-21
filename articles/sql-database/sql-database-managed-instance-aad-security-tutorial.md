---
title: Segurança de instância, utilização de principais de servidor do Azure AD (inícios de sessão) de gerida de base de dados SQL do Azure | Documentos da Microsoft
description: Conheça as técnicas e funcionalidades para proteger uma instância gerida na base de dados do Azure SQL e utilizar principais de servidor do Azure AD (inícios de sessão)
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
manager: craigg
ms.date: 02/20/2019
ms.openlocfilehash: 7511b85384c2c64c823d93df4369b0fea3e64b51
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226220"
---
# <a name="tutorial-managed-instance-security-in-azure-sql-database-using-azure-ad-server-principals-logins"></a>Tutorial: Segurança de instância gerida na base de dados do SQL Azure utilização de principais de servidor do Azure AD (inícios de sessão)

Instância gerida fornece quase todos os recursos de segurança que o mais recente do SQL Server no local tem de motor de base de dados (Enterprise Edition):

- Limitar o acesso num ambiente isolado
- Utilize os mecanismos de autenticação que necessitem de identidade (Azure AD, autenticação do SQL)
- Utilizar a autorização com permissões e associações baseadas em função
- Ativar funcionalidades de segurança

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> - Criar um Azure Active Directory (AD) principal do servidor (início de sessão) para uma instância gerida
> - Conceder permissões aos principais de servidor do Azure AD (inícios de sessão) numa instância gerida
> - Criar utilizadores do Azure AD a partir do Azure principais de servidor do AD (inícios de sessão)
> - Atribuir permissões a utilizadores do Azure AD e gerir a segurança da base de dados
> - Utilize a representação com utilizadores do Azure AD
> - Utilizar consultas entre bases de dados com utilizadores do Azure AD
> - Saiba mais sobre as funcionalidades de segurança, como proteção contra ameaças, auditoria, máscara de dados e encriptação

> [!NOTE]
> Está a principais de servidor do Azure AD (inícios de sessão) para instâncias geridas **pré-visualização pública**.

Para obter mais informações, consulte a [gerida de base de dados do Azure SQL descrição geral da instância](sql-database-managed-instance-index.yml) e [capacidades](sql-database-managed-instance.md) artigos.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir o tutorial, certifique-se de que tem os seguintes pré-requisitos:

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)
- Uma instância gerida da base de dados do Azure SQL
  - Siga este artigo: [Quickstart: Criar uma instância gerida da base de dados do Azure SQL](sql-database-managed-instance-get-started.md)
- Capaz de aceder à sua instância gerida e [aprovisionado o administrador do Azure AD para a instância gerida](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance). Para saber mais, consulte:
    - [Ligar a sua aplicação para uma instância gerida](sql-database-managed-instance-connect-app.md) 
    - [Arquitetura de conectividade de instância gerida](sql-database-managed-instance-connectivity-architecture.md)
    - [Configurar e gerir a autenticação do Azure Active Directory com o SQL](sql-database-aad-authentication-configure.md)

## <a name="limiting-access-to-your-managed-instance"></a>Limitar o acesso à sua instância gerida

Instâncias geridas só podem ser acedidas através de um endereço IP privado. Não existem não existem pontos finais de serviço que estão disponíveis para ligar a uma instância gerida de fora da rede de instância gerida. Muito como um ambiente no local do SQL Server isolado, aplicativos ou usuários precisam de acesso à rede da instância gerida (VNet) antes de pode ser estabelecida uma ligação. Para obter mais informações, consulte o seguinte artigo [ligar a sua aplicação para uma instância gerida](sql-database-managed-instance-connect-app.md).

> [!NOTE] 
> Uma vez que as instâncias geridas só podem ser acessadas dentro da sua VNET [regras de firewall da base de dados SQL](sql-database-firewall-configure.md) não se aplicam. A instância gerida tem seu próprio [firewall interno](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).

## <a name="create-an-azure-ad-server-principal-login-for-a-managed-instance-using-ssms"></a>Criar um Azure AD principal do servidor (início de sessão) para uma instância gerida com o SSMS

O primeiro principal do servidor do Azure AD (início de sessão) tem de ser criado pela conta do SQL Server standard (não pertencente ao azure AD) é um `sysadmin`. Veja os artigos seguintes para obter exemplos de ligar à sua instância gerida:

- [Quickstart: Configurar a VM do Azure para ligar a uma instância gerida](sql-database-managed-instance-configure-vm.md)
- [Quickstart: Configurar uma ligação de ponto a site para uma instância gerida do local](sql-database-managed-instance-configure-p2s.md)

> [!IMPORTANT]
> O administrador do Azure AD utilizado para configurar a instância gerida não pode ser utilizado para criar um Azure AD principal do servidor (início de sessão) dentro da instância gerida. Tem de criar o primeiro Azure principal do servidor AD (início de sessão) a utilizar uma conta do SQL Server que é um `sysadmin`. Esta é uma limitação temporária que será removida depois de principais de servidor do Azure AD (inícios de sessão) tornam-se em GA. Verá o seguinte erro se tentar utilizar uma conta de administrador do Azure AD para criar o início de sessão: `Msg 15247, Level 16, State 1, Line 1 User does not have permission to perform this action.`

1. Inicie sessão na sua instância gerida a utilizar uma conta do SQL Server standard (não pertencente ao azure AD) é uma `sysadmin`, utilizando [SQL Server Management Studio](sql-database-managed-instance-configure-p2s.md#use-ssms-to-connect-to-the-managed-instance).

2. Na **Object Explorer**, clique com o botão direito do servidor e escolha **nova consulta**.

3. Na janela da consulta, utilize a seguinte sintaxe para criar um início de sessão para um local conta do Azure AD:

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    Este exemplo cria um início de sessão para a conta nativeuser@aadsqlmi.onmicrosoft.com.

    ```sql
    USE master
    GO
    CREATE LOGIN [nativeuser@aadsqlmi.onmicrosoft.com] FROM EXTERNAL PROVIDER
    GO
    ```

4. Na barra de ferramentas, selecione **Execute** para criar o início de sessão.

5. Verifique o início de sessão recentemente adicionado, executando o seguinte comando do T-SQL:

    ```sql
    SELECT *  
    FROM sys.server_principals;  
    GO
    ```

    ![native-login.png](media/sql-database-managed-instance-security-tutorial/native-login.png)

Para obter mais informações, consulte [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current).

## <a name="granting-permissions-to-allow-the-creation-of-managed-instance-logins"></a>A conceder permissões para permitir a criação de inícios de sessão de instância gerida

Para criar outras entidades de segurança de servidor do Azure AD (inícios de sessão), funções do SQL Server ou permissões têm de ser concedidas para a entidade de segurança (SQL ou do Azure AD).

### <a name="sql-authentication"></a>Autenticação do SQL

- Se o início de sessão é uma entidade de segurança do SQL, apenas inícios de sessão que fazem parte do `sysadmin` função pode utilizar o comando create para criar inícios de sessão para uma conta do Azure AD.

### <a name="azure-ad-authentication"></a>Autenticação do Azure AD

- Para permitir que o Azure AD criado recentemente principal do servidor (início de sessão) a capacidade de criar outros inícios de sessão para outros utilizadores do Azure AD, grupos ou aplicações, conceder o início de sessão `sysadmin` ou `securityadmin` função de servidor. 
- No mínimo, **ALTER ANY LOGIN** têm de ser concedidas permissões para o principal do servidor do Azure AD (início de sessão) para criar outro do Azure AD principais de servidor (inícios de sessão). 
- Por predefinição, o permissões padrão concedidas a recentemente criada do Azure AD principais de servidor (inícios de sessão) no ramo principal é: **LIGAR SQL** e **ver qualquer base de dados**.
- O `sysadmin` função de servidor de pode ser concedida a muitas entidades de servidor de AD do Azure (inícios de sessão) dentro de uma instância gerida.

Para adicionar o início de sessão para o `sysadmin` função de servidor:

1. Iniciar sessão para a instância gerida novamente, ou a ligação existente com o SQL Principal que é um `sysadmin`.

1. Na **Object Explorer**, clique com o botão direito do servidor e escolha **nova consulta**.

1. Conceder o principal do servidor do Azure AD (início de sessão) a `sysadmin` função de servidor utilizando a seguinte sintaxe de T-SQL:

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER login_name
    GO
    ```

    O exemplo a seguir concede a `sysadmin` função de servidor para o início de sessão nativeuser@aadsqlmi.onmicrosoft.com

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER [nativeuser@aadsqlmi.onmicrosoft.com]
    GO
    ```

## <a name="create-additional-azure-ad-server-principals-logins-using-ssms"></a>Criar Azure AD adicional principais de servidor (inícios de sessão) com o SSMS

Assim que o principal do servidor do Azure AD (início de sessão) foi criado e fornecido com o `sysadmin` privilégios, o início de sessão desse pode criar logons adicionais usando o **do fornecedor externo** cláusula com **CREATE LOGIN**.

1. Ligue-se para a instância gerida com o servidor do Azure AD principal (início de sessão), com o SQL Server Management Studio. Introduza o nome de anfitrião da instância gerida. Para a autenticação no SSMS, existem três opções de escolha ao iniciar sessão com uma conta do Azure AD:

   - Active Directory - Universal com o suporte MFA
   - Active Directory - Palavra-passe
   - Active Directory - Integrado </br>

     ![ssms-login-prompt.png](media/sql-database-managed-instance-security-tutorial/ssms-login-prompt.png)

     Para obter mais informações, consulte o artigo seguinte: [Autenticação Universal com a Base de Dados SQL e o SQL Data Warehouse (suporte do SSMS para MFA)](sql-database-ssms-mfa-authentication.md)

1. Selecione **Active Directory - Universal com o suporte MFA**. Isso exibirá uma janela de início de sessão do multi-factor Authentication (MFA). Inicie sessão com a palavra-passe do Azure AD.

    ![mfa-login-prompt.png](media/sql-database-managed-instance-security-tutorial/mfa-login-prompt.png)

1. No SSMS **Object Explorer**, clique com o botão direito do servidor e escolha **nova consulta**.
1. Na janela da consulta, utilize a seguinte sintaxe para criar um início de sessão para outra conta do Azure AD:

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    Este exemplo cria um início de sessão do utilizador do Azure AD bob@aadsqlmi.net, cujo aadsqlmi.net de domínio está federada com o aadsqlmi.onmicrosoft.com do Azure AD.

    Execute o seguinte comando do T-SQL. Federado do Azure contas AD são as substituições de instância gerida para utilizadores e inícios de sessão do Windows no local.

    ```sql
    USE master
    GO
    CREATE LOGIN [bob@aadsqlmi.net] FROM EXTERNAL PROVIDER
    GO
    ```

1. Criar uma base de dados na instância gerida utilizando o [CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current) sintaxe. Esta base de dados será utilizado para testar os inícios de sessão do utilizador na secção seguinte.
    1. Na **Object Explorer**, clique com o botão direito do servidor e escolha **nova consulta**.
    1. Na janela da consulta, utilize a sintaxe seguinte para criar uma base de dados com o nome **MyMITestDB**.

        ```sql
        CREATE DATABASE MyMITestDB;
        GO
        ```

1. Crie um início de sessão de instância gerida para um grupo no Azure AD. O grupo têm de existir no Azure AD antes de poder adicionar o início de sessão para a instância gerida. Ver [criar um grupo básico e adicionar membros com o Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Criar um grupo _mygroup_ e adicionar membros a este grupo.

1. Abra uma nova janela de consulta no SQL Server Management Studio.

    Este exemplo assume que existe um grupo chamado _mygroup_ no Azure AD. Execute o seguinte comando:

    ```sql
    USE master
    GO
    CREATE LOGIN [mygroup] FROM EXTERNAL PROVIDER
    GO
    ```

1. Como um teste, inicie sessão para a instância gerida com o início de sessão recentemente criado ou grupo. Abra uma nova ligação para a instância gerida e utilizar o novo início de sessão durante a autenticação.
1. Na **Object Explorer**, clique com o botão direito do servidor e escolha **nova consulta** para a nova ligação.
1. Verifique as permissões de servidor recentemente criado para o principal de servidor do Azure AD (início de sessão) executando o seguinte comando:

      ```sql
      SELECT * FROM sys.fn_my_permissions (NULL, 'DATABASE')
      GO
      ```

> [!NOTE]
> Utilizadores de convidado do Azure AD são suportados para inícios de sessão de instância gerida, apenas quando são adicionadas como parte de um grupo do Azure AD. Um utilizador convidado é uma conta que é convidada para o Azure AD, que pertence a instância gerida, do outro Azure AD. Por exemplo, joe@contoso.com (conta do Azure AD) ou steve@outlook.com (MSA conta) podem ser adicionada a um grupo no aadsqlmi Azure AD. Assim que os utilizadores são adicionados a um grupo, um início de sessão pode ser criado a instância gerida **mestre** base de dados para o grupo utilizando o **CREATE LOGIN** sintaxe. Utilizadores convidados que são membros deste grupo podem ligar-se à instância gerida utilizando os inícios de sessão atuais (por exemplo, joe@contoso.com ou steve@outlook.com).

## <a name="create-an-azure-ad-user-from-the-azure-ad-server-principal-login-and-give-permissions"></a>Criar um utilizador do Azure AD principal do servidor (início de sessão) e conceder permissões

Autorização para bases de dados individuais funciona grande parte da mesma forma o na instância gerida de forma que funciona com o SQL Server no local. Um utilizador pode ser criado a partir de um início de sessão existente na base de dados e ser fornecido com permissões nessa base de dados ou adicionado a uma função de base de dados.

Agora que criámos uma base de dados chamado **MyMITestDB**, e um início de sessão que tenha apenas as permissões predefinidas, a próxima etapa é criar um utilizador a partir desse início de sessão. No momento, o início de sessão pode ligar para o gerenciado de instância e veja todas as bases de dados, mas não é possível interagir com as bases de dados. Se iniciar sessão com a conta do Azure AD que tenha as permissões predefinidas e tente expandir a base de dados recentemente criado, verá o seguinte erro:

![ssms-db-not-accessible.png](media/sql-database-managed-instance-security-tutorial/ssms-db-not-accessible.png)

Para obter mais informações sobre a concessão de permissões de base de dados, consulte [introdução a permissões de motor de base de dados](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions).

### <a name="create-an-azure-ad-user-and-create-a-sample-table"></a>Criar um utilizador do Azure AD e criar uma tabela de exemplo

1. Inicie sessão na sua instância gerida utilizando um `sysadmin` conta ao utilizar o SQL Server Management Studio.
1. Na **Object Explorer**, clique com o botão direito do servidor e escolha **nova consulta**.
1. Na janela da consulta, utilize a seguinte sintaxe para criar um utilizador de um principal de servidor do Azure AD (início de sessão):

    ```sql
    USE <Database Name> -- provide your database name
    GO
    CREATE USER user_name FROM LOGIN login_name
    GO
    ```

    O exemplo seguinte cria um utilizador bob@aadsqlmi.net do início de sessão bob@aadsqlmi.net:

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [bob@aadsqlmi.net] FROM LOGIN [bob@aadsqlmi.net]
    GO
    ```

1. Também é suportada para criar um utilizador de um principal de servidor do Azure AD (início de sessão) que é um grupo.

    O exemplo seguinte cria um início de sessão para o grupo do Azure AD _mygroup_ que existe no seu Azure AD.

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [mygroup] FROM LOGIN [mygroup]
    GO
    ```

    Todos os utilizadores que pertencem à **mygroup** pode aceder a **MyMITestDB** base de dados.

    > [!IMPORTANT]
    > Ao criar um **USUÁRIO** a partir de um principal do servidor do Azure AD (início de sessão), especificar o user_name como o mesmo login_name partir **início de sessão**.

    Para obter mais informações, consulte [criar utilizador](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current).

1. Numa nova janela de consulta, crie uma tabela de teste com o seguinte comando do T-SQL:

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

1. Crie uma ligação no SSMS, com o utilizador que foi criado. Observará que não é possível ver a tabela **TestTable** que foi criada com o `sysadmin` anteriormente. É necessário fornecer ao usuário com permissões para ler os dados da base de dados.

1. Pode verificar a permissão atual, que o utilizador tem executando o seguinte comando:

    ```sql
    SELECT * FROM sys.fn_my_permissions('MyMITestDB','DATABASE')
    GO
    ```

### <a name="add-users-to-database-level-roles"></a>Adicionar utilizadores às funções de nível de base de dados

Para o utilizador ver os dados na base de dados, podemos fornecer [funções ao nível da base de dados](/sql/relational-databases/security/authentication-access/database-level-roles) ao usuário.

1. Inicie sessão na sua instância gerida utilizando um `sysadmin` conta ao utilizar o SQL Server Management Studio.

1. Na **Object Explorer**, clique com o botão direito do servidor e escolha **nova consulta**.

1. Conceder ao utilizador do Azure AD a `db_datareader` função de base de dados utilizando a seguinte sintaxe de T-SQL:

    ```sql
    Use <Database Name> -- provide your database name
    ALTER ROLE db_datareader ADD MEMBER user_name
    GO
    ```

    O exemplo a seguir fornece ao usuário bob@aadsqlmi.net e o grupo _mygroup_ com `db_datareader` permissões no **MyMITestDB** base de dados:

    ```sql
    USE MyMITestDB
    GO
    ALTER ROLE db_datareader ADD MEMBER [bob@aadsqlmi.net]
    GO
    ALTER ROLE db_datareader ADD MEMBER [mygroup]
    GO
    ```

1. Verificação do utilizador do Azure AD que foi criada na base de dados existe ao executar o comando seguinte:

    ```sql
    SELECT * FROM sys.database_principals
    GO
    ```

1. Criar uma nova ligação para a instância gerida com o utilizador que tenha sido adicionado para o `db_datareader` função.
1. Expanda a base de dados no **Object Explorer** para ver a tabela.

    ![ssms-test-table.png](media/sql-database-managed-instance-security-tutorial/ssms-test-table.png)

1. Abra uma nova janela de consulta e execute a instrução SELECT seguinte:

    ```sql
    SELECT *
    FROM TestTable
    ```

    É capaz de ver dados da tabela? Deverá ver as colunas que está a ser devolvidas.

    ![ssms-test-table-query.png](media/sql-database-managed-instance-security-tutorial/ssms-test-table-query.png)

## <a name="impersonating-azure-ad-server-level-principals-logins"></a>Representação de entidades de segurança do Azure AD ao nível do servidor (inícios de sessão)

Instância gerida suporta a representação de entidades de segurança do Azure AD ao nível do servidor (inícios de sessão).

### <a name="test-impersonation"></a>Representação de teste

1. Inicie sessão na sua instância gerida utilizando um `sysadmin` conta ao utilizar o SQL Server Management Studio.

1. Na **Object Explorer**, clique com o botão direito do servidor e escolha **nova consulta**.

1. Na janela da consulta, utilize o seguinte comando para criar um novo procedimento armazenado:

    ```sql
    USE MyMITestDB
    GO  
    CREATE PROCEDURE dbo.usp_Demo  
    WITH EXECUTE AS 'bob@aadsqlmi.net'  
    AS  
    SELECT user_name();  
    GO
    ```

1. Utilize o seguinte comando para ver o que é o utilizador estiver representando ao executar o procedimento armazenado **bob\@aadsqlmi.net**.

    ```sql
    Exec dbo.usp_Demo
    ```

1. Representação de teste usando a instrução de executar o início de sessão de AS:

    ```sql
    EXECUTE AS LOGIN = 'bob@aadsqlmi.net'
    GO
    SELECT SUSER_SNAME()
    REVERT
    GO
    ```

> [!NOTE]
> Apenas os SQL server principais ao nível (inícios de sessão) que fazem parte do `sysadmin` função pode executar as seguintes operações de filtragem de entidades de segurança do Azure AD: 
> - EXECUTAR COMO UTILIZADOR
> - EXECUTAR COMO INÍCIO DE SESSÃO

## <a name="using-cross-database-queries-in-managed-instances"></a>Usando consultas entre bases de dados em instâncias geridas

Consultas entre bases de dados são suportadas para contas do Azure AD com entidades de servidor do Azure AD (inícios de sessão). Para testar uma consulta entre bases de dados com um grupo do Azure AD, é necessário criar outro banco de dados e uma tabela. Pode ignorar a criação de outra base de dados e tabela se ainda existir.

1. Inicie sessão na sua instância gerida utilizando um `sysadmin` conta ao utilizar o SQL Server Management Studio.
1. Na **Object Explorer**, clique com o botão direito do servidor e escolha **nova consulta**.
1. Na janela da consulta, utilize o seguinte comando para criar uma base de dados com o nome **MyMITestDB2** e com o nome de tabela **TestTable2**:

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

1. Numa nova janela de consulta, execute o seguinte comando para criar o usuário _mygroup_ na base de dados nova **MyMITestDB2**e conceder permissões SELECT nessa base de dados para _mygroup_:

    ```sql
    USE MyMITestDB2
    GO
    CREATE USER [mygroup] FROM LOGIN [mygroup]
    GO
    GRANT SELECT TO [mygroup]
    GO
    ```

1. Início de sessão para a instância gerida com o SQL Server Management Studio como membro do grupo do Azure AD _mygroup_. Abra uma nova janela de consulta e execute a instrução SELECT entre bases de dados:

    ```sql
    USE MyMITestDB
    SELECT * FROM MyMITestDB2..TestTable2
    GO
    ```

    Deverá ver os resultados de tabela a partir **TestTable2**.

## <a name="additional-scenarios-supported-for-azure-ad-server-principals-logins-public-preview"></a>Cenários adicionais, suportados para principais de servidor do Azure AD (inícios de sessão) (pré-visualização pública) 

- Execuções de gerenciamento e a tarefa de agente do SQL são suportadas para principais de servidor do Azure AD (inícios de sessão).
- Base de dados de cópia de segurança e restaurar as operações podem ser executadas por entidades de servidor do Azure AD (inícios de sessão).
- [Auditoria](sql-database-managed-instance-auditing.md) de todas as instruções relacionadas com principais de servidor do Azure AD (inícios de sessão) e eventos de autenticação.
- Dedicado a ligação de administrador para os principais de servidor do Azure AD (inícios de sessão) que são membros do `sysadmin` função de servidor.
- Principais de servidor do Azure AD (inícios de sessão) são suportadas com a utilização a [sqlcmd utilitário](/sql/tools/sqlcmd-utility) e [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) ferramenta.
- Acionadores de início de sessão são suportados para eventos de início de sessão provenientes de principais de servidor do Azure AD (inícios de sessão).
- Correio de Mediador de serviço e DB pode ser configurados com principais de servidor do Azure AD (inícios de sessão).


## <a name="next-steps"></a>Passos Seguintes

### <a name="enable-security-features"></a>Ativar funcionalidades de segurança

Consulte o seguinte [geridos recursos de segurança de recursos de instância](sql-database-managed-instance.md#azure-sql-database-security-features) artigo para obter uma lista abrangente de formas de proteger a sua base de dados. As seguintes funcionalidades de segurança são discutidas:

- [Auditoria de instância gerida](sql-database-managed-instance-auditing.md) 
- [Sempre encriptado](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [Deteção de ameaças](sql-database-managed-instance-threat-detection.md) 
- [Máscara de dados dinâmica](/sql/relational-databases/security/dynamic-data-masking)
- [Segurança ao nível da linha](/sql/relational-databases/security/row-level-security) 
- [Encriptação de dados transparente (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="managed-instance-capabilities"></a>Funcionalidades de instância gerida

Para uma visão geral das funcionalidades de instância gerida, consulte:

> [!div class="nextstepaction"]
> [Capacidades de instância gerida](sql-database-managed-instance.md)
