---
title: SqL Managed Instance security with Azure AD server principals (logins)
description: Saiba mais técnicas e funcionalidades para garantir a Azure SQL Managed Instance e use os principais servidores AD do Azure (logins)
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.topic: tutorial
author: GitHubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 11/06/2019
ms.openlocfilehash: 50544507f9d83c216bb6c18e004c5ce7ad1ca346
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105639845"
---
# <a name="tutorial-security-in-azure-sql-managed-instance-using-azure-ad-server-principals-logins"></a>Tutorial: Segurança no Azure SQL Managed Instance com principais (inícios de sessão) do servidor do Azure Active Directory
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

A Azure SQL Managed Instance fornece quase todas as funcionalidades de segurança que o mais recente motor de base de dados SQL Server (Enterprise Edition) tem:

- Limite o acesso num ambiente isolado
- Utilize mecanismos de autenticação que exijam identidade: Diretório Ativo Azure (Azure AD) e Autenticação SQL
- Utilizar a autorização com membros e permissões baseados em funções
- Ativar funcionalidades de segurança

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> - Crie um servidor AD AD (login) para uma instância gerida
> - Conceder permissões aos principais dos servidores AD da Azure (logins) num caso gerido
> - Criar utilizadores AD Azure a partir de principais servidores AD do Azure (logins)
> - Atribuir permissões aos utilizadores de AD do Azure e gerir a segurança da base de dados
> - Use a personificação com utilizadores AZure AD
> - Utilize consultas de base de dados cruzadas com utilizadores AZure AD
> - Conheça as funcionalidades de segurança, tais como proteção de ameaças, auditoria, mascaramento de dados e encriptação

Para saber mais, consulte a [visão geral da Azure SQL Managed Instance](sql-managed-instance-paas-overview.md). 

## <a name="prerequisites"></a>Pré-requisitos

Para completar o tutorial, certifique-se de que tem os seguintes pré-requisitos:

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)
- Um caso gerido
  - Siga este artigo: [Quickstart: Criar uma instância gerida](instance-create-quickstart.md)
- Capaz de aceder à sua instância gerida e [a provisionou um administrador AD AZure para a instância gerida](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance). Para saber mais, veja:
  - [Ligue a sua aplicação a uma instância gerida](connect-application-instance.md)
  - [SQL Managed Instance arquitetura de conectividade](connectivity-architecture-overview.md)
  - [Configure e gere a autenticação do Azure Ative Directory com o SQL](../database/authentication-aad-configure.md)

## <a name="limit-access"></a>Limite de acesso 

As instâncias geridas podem ser acedidas através de um endereço IP privado. Tal como um ambiente isolado do SQL Server, as aplicações ou os utilizadores precisam de acesso à rede SQL Managed Instance (VNet) antes de ser estabelecida uma ligação. Para obter mais informações, consulte [Conecte a sua aplicação à SQL Managed Instance](connect-application-instance.md).

Também é possível configurar um ponto final de serviço em uma instância gerida, que permite conexões públicas da mesma forma que para Azure SQL Database.
Para obter mais informações, consulte [o ponto final público do Configure em Azure SQL Managed Instance](public-endpoint-configure.md).

> [!NOTE]
> Mesmo com os pontos finais de serviço ativados, as regras de [firewall da Base de Dados Azure SQL](../database/firewall-configure.md) não se aplicam. A Azure SQL Managed Instance tem a sua própria [firewall incorporada](management-endpoint-verify-built-in-firewall.md) para gerir a conectividade.

## <a name="create-an-azure-ad-server-principal-login-using-ssms"></a>Criar um servidor AD AD (login) usando SSMS

O primeiro principal do servidor AD AD (login) pode ser criado pela conta administrador padrão SQL (non-Azure AD) que é um `sysadmin` , ou o administrador AD AD A azul para a instância gerida criada durante o processo de provisionamento. Para obter mais informações, consulte [Disposição de um administrador do Azure Ative Directory para a SQL Managed Instance](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).

Consulte os seguintes artigos para exemplos de ligação à SQL Managed Instance:

- [Quickstart: Configure Azure VM para ligar à SQL Managed Instance](connect-vm-instance-configure.md)
- [Quickstart: Configurar uma ligação ponto-a-local à SQL Managed Instance a partir de instalações](point-to-site-p2s-configure.md)

1. Inicie sessão na sua instância gerida utilizando uma conta de login padrão SQL (non-Azure AD) que é um `sysadmin` administrador AD ou Azure para exemplos geridos sql, usando [o SQL Server Management Studio](point-to-site-p2s-configure.md#connect-with-ssms).

2. No **Object Explorer,** clique com o botão direito no servidor e escolha **Nova Consulta**.

3. Na janela de consulta, utilize a seguinte sintaxe para criar um login para uma conta AD Azure local:

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    Este exemplo cria um login para a nativeuser@aadsqlmi.onmicrosoft.com conta.

    ```sql
    USE master
    GO
    CREATE LOGIN [nativeuser@aadsqlmi.onmicrosoft.com] FROM EXTERNAL PROVIDER
    GO
    ```

4. Na barra de ferramentas, **selecione Executar** para criar o login.

5. Verifique o login recém-adicionado, executando o seguinte comando T-SQL:

    ```sql
    SELECT *  
    FROM sys.server_principals;  
    GO
    ```

    ![Screenshot do separador Resultados no S M S Object Explorer mostrando o nome, principal_id, sid, tipo e type_desc do login recém-adicionado.](./media/aad-security-configure-tutorial/native-login.png)

Para mais informações, consulte [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true).

## <a name="grant-permissions-to-create-logins"></a>Conceder permissões para criar logins

Para criar outros principais do servidor Azure (logins), as funções ou permissões do SQL Server devem ser concedidas ao principal (SQL ou Azure AD).

### <a name="sql-authentication"></a>Autenticação do SQL

- Se o login for um principal SQL, apenas os logins que fazem parte da `sysadmin` função podem usar o comando criar para criar logins para uma conta AD Azure.

### <a name="azure-ad-authentication"></a>Autenticação do Azure AD

- Para permitir ao recém-criado Azure AD servidor principal (login) a capacidade de criar outros logins para outros utilizadores, grupos ou aplicações Azure Ad, conceder o papel de login `sysadmin` ou `securityadmin` servidor.
- No mínimo, **altere qualquer** permissão DE LOGIN deve ser concedida ao principal do servidor AD Azure (login) para criar outros principais do servidor Azure (logins).
- Por predefinição, a permissão padrão concedida aos recém-criados principais servidores AD (logins) em master é: **CONNECT SQL** e **VER QUALQUER BASE DE DADOS**.
- A `sysadmin` função do servidor pode ser concedida a muitos principais servidores AD (logins) dentro de um caso gerido.

Para adicionar o login à função do `sysadmin` servidor:

1. Inicie novamente o caso gerido, ou utilize a ligação existente com o administrador AD AD ou com o principal SQL Azure que é um `sysadmin` .

1. No **Object Explorer,** clique com o botão direito no servidor e escolha **Nova Consulta**.

1. Conceda ao principal do servidor AD (login) a função do `sysadmin` servidor utilizando a seguinte sintaxe T-SQL:

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER login_name
    GO
    ```

    O exemplo seguinte concede a função do `sysadmin` servidor ao login nativeuser@aadsqlmi.onmicrosoft.com

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER [nativeuser@aadsqlmi.onmicrosoft.com]
    GO
    ```

## <a name="create-additional-azure-ad-server-principals-logins-using-ssms"></a>Criar principais servidores AZure (logins) adicionais utilizando SSMS

Uma vez criado o principal do servidor AD (login) Azure, e fornecido com `sysadmin` privilégios, esse login pode criar logins adicionais utilizando a cláusula **DE FORNECEDOR EXTERNO** com CREATE **LOGIN**.

1. Conecte-se à instância gerida com o principal do servidor AD Azure (login), utilizando o SQL Server Management Studio. Insira o nome de anfitrião da sua sql Gestd Instance. Para autenticação em SSMS, existem três opções para escolher quando iniciar sessão com uma conta AD Azure:

   - Diretório Ativo - Universal com suporte MFA
   - Diretório Ativo - Senha
   - Diretório Ativo - Integrado </br>

     ![Screenshot do diálogo 'Connect to Server' em S S M S com Ative Directory - Universal com suporte MFA selecionado no dropdown de autenticação.](./media/aad-security-configure-tutorial/ssms-login-prompt.png)

     Para obter mais informações, consulte [a Autenticação Universal (suporte SSMS para autenticação multi-factor)](../database/authentication-mfa-ssms-overview.md).

1. Selecione **Ative Directory - Universal com suporte MFA**. Isto traz uma janela de login de autenticação multi-factor. Inscreva-se com a sua senha AD Azure.

    ![Screenshot da janela de login de autenticação multi-factor com o cursor no campo de senha de entrada.](./media/aad-security-configure-tutorial/mfa-login-prompt.png)

1. No SSMS **Object Explorer,** clique com o botão direito no servidor e escolha **Nova Consulta**.
1. Na janela de consulta, utilize a seguinte sintaxe para criar um login para outra conta AD Azure:

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    Este exemplo cria um login para o utilizador Azure bob@aadsqlmi.net AD, cujo domínio aadsqlmi.net é federado com o domínio Azure AD aadsqlmi.onmicrosoft.com.

    Execute o seguinte comando T-SQL. As contas Federated AZure AD são as substituições de SqL Managed Instance para logins e utilizadores do Windows no local.

    ```sql
    USE master
    GO
    CREATE LOGIN [bob@aadsqlmi.net] FROM EXTERNAL PROVIDER
    GO
    ```

1. Crie uma base de dados no caso gerido utilizando a sintaxe [CREATE DATABASE.](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current&preserve-view=true) Esta base de dados será utilizada para testar os logins dos utilizadores na secção seguinte.
    1. No **Object Explorer,** clique com o botão direito no servidor e escolha **Nova Consulta**.
    1. Na janela de consulta, utilize a seguinte sintaxe para criar uma base de dados chamada **MyMITestDB**.

        ```sql
        CREATE DATABASE MyMITestDB;
        GO
        ```

1. Crie um login sql Managed Instance para um grupo em Azure AD. O grupo terá de existir em Azure AD antes de poder adicionar o login à SQL Managed Instance. Consulte [Criar um grupo básico e adicionar membros utilizando o Azure Ative Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Crie um _grupo de grupo_ e adicione membros a este grupo.

1. Abra uma nova janela de consulta no SQL Server Management Studio.

    Este exemplo pressupõe que existe um grupo chamado _mygroup_ em Azure AD. Execute o seguinte comando:

    ```sql
    USE master
    GO
    CREATE LOGIN [mygroup] FROM EXTERNAL PROVIDER
    GO
    ```

1. Como teste, inicie sessão na instância gerida com o login ou grupo recém-criado. Abra uma nova ligação à instância gerida e utilize o novo login ao autenticar.
1. No **Object Explorer,** clique com o botão direito no servidor e escolha **Nova Consulta** para a nova ligação.
1. Verifique as permissões do servidor para o recém-criado servidor AD (login) executando o seguinte comando:

      ```sql
      SELECT * FROM sys.fn_my_permissions (NULL, 'DATABASE')
      GO
      ```

> [!NOTE]
> Os utilizadores de hóspedes Azure AD são suportados para logins DE Exemplo Gerido SQL, apenas quando adicionados como parte de um Grupo AD Azure. Um utilizador convidado da Azure AD é uma conta que é convidada para a instância AD Azure a que pertence a instância gerida, a partir de outra instância AD Azure. Por exemplo, joe@contoso.com (conta Azure AD) ou steve@outlook.com (conta Microsoft) podem ser adicionados a um grupo na instância aadsqlmi Ad Azure. Uma vez que os utilizadores são adicionados a um grupo, um login pode ser criado na base de dados **principal** sql Managed Instance para o grupo usando a sintaxe **DE LOGIN CREATE.** Os utilizadores convidados que são membros deste grupo podem ligar-se à instância gerida utilizando os seus logins atuais (por exemplo, joe@contoso.com ou steve@outlook.com ).

## <a name="create-an-azure-ad-user-from-the-azure-ad-server-principal-login"></a>Criar um utilizador AD Azure a partir do servidor AD Azure (login)

A autorização para bases de dados individuais funciona da mesma forma em SQL Managed Instance como faz com bases de dados no SQL Server. Um utilizador pode ser criado a partir de um login existente numa base de dados, e ser fornecido com permissões nessa base de dados, ou adicionado a uma função de base de dados.

Agora que criámos uma base de dados chamada **MyMITestDB**, e um login que só tem permissões padrão, o próximo passo é criar um utilizador a partir desse login. De momento, o login pode ligar-se à instância gerida, e ver todas as bases de dados, mas não pode interagir com as bases de dados. Se iniciar sedições com a conta AD Azure que tem as permissões por defeito e tentar expandir a base de dados recém-criada, verá o seguinte erro:

![Screenshot de uma mensagem de erro do S S M S Object Explorer que diz "A base de dados MyMITestDB não está acessível. (ObjectExplorer)".](./media/aad-security-configure-tutorial/ssms-db-not-accessible.png)

Para obter mais informações sobre a concessão de permissões de base de dados, consulte ["Começar com permissões do motor de base de dados".](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions)

### <a name="create-an-azure-ad-user-and-create-a-sample-table"></a>Crie um utilizador AD Azure e crie uma tabela de amostras

1. Inicie sessão na sua instância gerida utilizando uma `sysadmin` conta utilizando o SQL Server Management Studio.
1. No **Object Explorer,** clique com o botão direito no servidor e escolha **Nova Consulta**.
1. Na janela de consulta, utilize a seguinte sintaxe para criar um utilizador AD Azure a partir de um servidor AD AD Azure (login):

    ```sql
    USE <Database Name> -- provide your database name
    GO
    CREATE USER user_name FROM LOGIN login_name
    GO
    ```

    O exemplo a seguir cria um utilizador bob@aadsqlmi.net a partir do login bob@aadsqlmi.net:

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [bob@aadsqlmi.net] FROM LOGIN [bob@aadsqlmi.net]
    GO
    ```

1. Também é suportado para criar um utilizador AZure AD a partir de um servidor AD AD (login) que é um grupo.

    O exemplo a seguir cria um login para o grupo Azure AD _que_ existe no seu exemplo AD Azure.

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [mygroup] FROM LOGIN [mygroup]
    GO
    ```

    Todos os utilizadores que pertençam ao *meu grupo* podem aceder à base de dados **MyMITestDB.**

    > [!IMPORTANT]
    > Ao criar um **UTILIZADOR** a partir de um servidor AD AD principal (login), especifique a user_name como a mesma login_name do **LOGIN**.

    Para obter mais informações, consulte [CREATE USER](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current&preserve-view=true).

1. Numa nova janela de consulta, crie uma mesa de teste utilizando o seguinte comando T-SQL:

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

1. Criar uma ligação em SSMS com o utilizador que foi criado. Vai notar que não consegue ver a tabela **TestTable** que foi criada `sysadmin` anteriormente. Precisamos fornecer ao utilizador permissões para ler dados a partir da base de dados.

1. Pode verificar a permissão atual que o utilizador tem executando o seguinte comando:

    ```sql
    SELECT * FROM sys.fn_my_permissions('MyMITestDB','DATABASE')
    GO
    ```

### <a name="add-users-to-database-level-roles"></a>Adicionar utilizadores a funções de nível de base de dados

Para que o utilizador veja os dados na base de dados, podemos fornecer [funções de nível de base de dados](/sql/relational-databases/security/authentication-access/database-level-roles) ao utilizador.

1. Inicie sessão na sua instância gerida utilizando uma `sysadmin` conta utilizando o SQL Server Management Studio.

1. No **Object Explorer,** clique com o botão direito no servidor e escolha **Nova Consulta**.

1. Conceda ao utilizador Azure AD a `db_datareader` função de base de dados utilizando a seguinte sintaxe T-SQL:

    ```sql
    Use <Database Name> -- provide your database name
    ALTER ROLE db_datareader ADD MEMBER user_name
    GO
    ```

    O exemplo a seguir fornece ao utilizador bob@aadsqlmi.net e ao _grupo mygroup_ `db_datareader` permissões na base de dados **MyMITestDB:**

    ```sql
    USE MyMITestDB
    GO
    ALTER ROLE db_datareader ADD MEMBER [bob@aadsqlmi.net]
    GO
    ALTER ROLE db_datareader ADD MEMBER [mygroup]
    GO
    ```

1. Verifique se o utilizador Azure AD que foi criado na base de dados existe executando o seguinte comando:

    ```sql
    SELECT * FROM sys.database_principals
    GO
    ```

1. Crie uma nova ligação à instância gerida com o utilizador que foi adicionado à `db_datareader` função.
1. Expanda a base de dados no **Object Explorer** para ver a tabela.

    ![Screenshot do Object Explorer em S M S mostrando a estrutura da pasta para tabelas no MyMITestDB. O dbo. A pasta TestTable é realçada.](./media/aad-security-configure-tutorial/ssms-test-table.png)

1. Abra uma nova janela de consulta e execute a seguinte declaração SELECT:

    ```sql
    SELECT *
    FROM TestTable
    ```

    Consegue ver os dados da mesa? Devia ver as colunas a serem devolvidas.

    ![Screenshot do separador Resultados no S M S Object Explorer mostrando os cabeçalhos da coluna de tabela AccountNum, City, Name e State.](./media/aad-security-configure-tutorial/ssms-test-table-query.png)

## <a name="impersonate-azure-ad-server-level-principals-logins"></a>Personificar os principais do nível do servidor AD do Azure (logins)

SQL Managed Instance suporta a personificação de principais de nível de servidor AD AD (logins).

### <a name="test-impersonation"></a>Imitação de teste

1. Inicie sessão na sua instância gerida utilizando uma `sysadmin` conta utilizando o SQL Server Management Studio.

1. No **Object Explorer,** clique com o botão direito no servidor e escolha **Nova Consulta**.

1. Na janela de consulta, utilize o seguinte comando para criar um novo procedimento armazenado:

    ```sql
    USE MyMITestDB
    GO  
    CREATE PROCEDURE dbo.usp_Demo  
    WITH EXECUTE AS 'bob@aadsqlmi.net'  
    AS  
    SELECT user_name();  
    GO
    ```

1. Utilize o seguinte comando para ver se o utilizador que está a fazer-se passar ao executar o procedimento armazenado é **bob \@ aadsqlmi.net**.

    ```sql
    Exec dbo.usp_Demo
    ```

1. Teste de representação utilizando a declaração DE LOGIN EXECUTE AS:

    ```sql
    EXECUTE AS LOGIN = 'bob@aadsqlmi.net'
    GO
    SELECT SUSER_SNAME()
    REVERT
    GO
    ```

> [!NOTE]
> Apenas os principais de nível de servidor SQL (logins) que fazem parte do `sysadmin` papel podem executar as seguintes operações direcionadas aos principais AD da Azure:
>
> - EXECUTAR COMO UTILIZADOR
> - EXECUTAR COMO LOGIN

## <a name="use-cross-database-queries"></a>Utilize consultas de base de dados cruzadas

As consultas de base de dados cruzadas são suportadas para contas Azure AD com os principais servidores AD do Azure (logins). Para testar uma consulta de base de dados cruzada com um grupo AZure AD, precisamos de criar outra base de dados e tabela. Pode saltar a criação de outra base de dados e tabela se já existir.

1. Inicie sessão na sua instância gerida utilizando uma `sysadmin` conta utilizando o SQL Server Management Studio.
1. No **Object Explorer,** clique com o botão direito no servidor e escolha **Nova Consulta**.
1. Na janela de consulta, utilize o seguinte comando para criar uma base de dados chamada **MyMITestDB2** e tabela denominada **TestTable2**:

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

1. Numa nova janela de consulta, execute o seguinte comando para criar o _mygroup_ do utilizador na nova base de dados **MyMITestDB2,** e conceda permissões SELECT nessa base de dados ao _meu grupo:_

    ```sql
    USE MyMITestDB2
    GO
    CREATE USER [mygroup] FROM LOGIN [mygroup]
    GO
    GRANT SELECT TO [mygroup]
    GO
    ```

1. Inscreva-se na instância gerida usando o SQL Server Management Studio como membro do grupo AZure AD _._ Abra uma nova janela de consulta e execute a declaração SELECT de base cruzada:

    ```sql
    USE MyMITestDB
    SELECT * FROM MyMITestDB2..TestTable2
    GO
    ```

    Deve ver os resultados da tabela do **TestTable2**.

## <a name="additional-supported-scenarios"></a>Cenários apoiados adicionais

- A gestão de agentes SQL e execuções de emprego são suportadas para os principais servidores AD da Azure (logins).
- As operações de backup e restauro da base de dados podem ser executadas pelos principais servidores AD do Azure (logins).
- [Auditoria de](auditing-configure.md) todas as declarações relacionadas com os principais servidores AD do Azure (logins) e eventos de autenticação.
- Ligação de administrador dedicada para os principais do servidor AD do Azure (logins) que são membros da `sysadmin` função do servidor.
- Os principais servidores AD (logins) são suportados com a utilização da [ferramenta sqlcmd utility](/sql/tools/sqlcmd-utility) e [SQL Server Management Studio.](/sql/ssms/download-sql-server-management-studio-ssms)
- Os gatilhos de início de sessão são suportados para eventos de início de sessão provenientes de diretores de servidores AD do Azure (logins).
- O Corretor de Serviços e o correio DB podem ser configurados utilizando os principais servidores AZure AD (logins).

## <a name="next-steps"></a>Passos seguintes

### <a name="enable-security-features"></a>Ativar funcionalidades de segurança

Consulte o artigo [de segurança SQL Managed Instance](sql-managed-instance-paas-overview.md#security-features) para obter uma lista completa de formas de proteger a sua base de dados. São discutidas as seguintes funcionalidades de segurança:

- [Auditoria de casos geridos sql](auditing-configure.md)
- [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [Deteção de ameaças](threat-detection-configure.md)
- [Mascaramento dinâmico de dados](/sql/relational-databases/security/dynamic-data-masking)
- [Segurança ao nível da linha](/sql/relational-databases/security/row-level-security)
- [Encriptação transparente de dados (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="sql-managed-instance-capabilities"></a>Capacidades de instância gerida sql

Para uma visão geral completa das capacidades de exemplo gerida do SQL, consulte:

> [!div class="nextstepaction"]
> [Capacidades de instância gerida sql](sql-managed-instance-paas-overview.md)