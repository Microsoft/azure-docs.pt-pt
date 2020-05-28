---
title: Segurança de instância gerida sQL com diretores de servidores Azure AD (logins)
description: Aprenda sobre técnicas e funcionalidades para proteger o Azure SQL Managed Instance, e use os principais servidores Azure AD (logins)
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.topic: tutorial
author: GitHubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 11/06/2019
ms.openlocfilehash: 54195652cfdf476f364a31bf710852dab33fb154
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84053484"
---
# <a name="tutorial-security-in-azure-sql-managed-instance-using-azure-ad-server-principals-logins"></a>Tutorial: Segurança em Instância Gerida Azure SQL utilizando os diretores de servidores Azure AD (logins)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

A SQL Managed Instance fornece quase todas as funcionalidades de segurança que o mais recente SQL Server no local (Enterprise Edition) Base de Dados tem:

- Limitação do acesso num ambiente isolado
- Utilize mecanismos de autenticação que exijam identidade (Autenticação Azure AD, SQL)
- Utilize a autorização com membros e permissões baseados em papéis
- Ativar funcionalidades de segurança

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> - Criar um servidor de servidor de Diretório Ativo Azure (AD) (login) para uma instância gerida pela SQL
> - Conceder permissões aos diretores de servidores da AD (logins) numa instância gerida pela SQL
> - Criar utilizadores de Anúncios Azure a partir de diretores de servidores Da Azure (logins)
> - Atribuir permissões aos utilizadores da AD Azure e gerir a segurança da base de dados
> - Use a personificação com utilizadores da AD Azure
> - Utilize consultas de base de dados cruzadas com utilizadores de Anúncios Azure
> - Conheça as funcionalidades de segurança, tais como proteção de ameaças, auditoria, máscara de dados e encriptação

Para saber mais, consulte a visão geral da [Instância Gerida Azure SQL](sql-managed-instance-paas-overview.md). 

## <a name="prerequisites"></a>Pré-requisitos

Para completar o tutorial, certifique-se de que tem os seguintes pré-requisitos:

- [Estúdio de Gestão de Servidores SQL](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)
- Uma instância gerida por SQL
  - Siga este artigo: [Quickstart: Criar uma instância gerida pela SQL](instance-create-quickstart.md)
- Capaz de aceder à sua Instância Gerida SQL e [forprovisionado um administrador azure AD para a Instância Gerida SQL](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance). Para saber mais, consulte:
  - [Ligue a sua aplicação a uma instância gerida pela SQL](connect-application-instance.md)
  - [Arquitetura de conectividade sql gerida por instância](connectivity-architecture-overview.md)
  - [Configure e gerea autenticação de Diretório Ativo Azure com SQL](../database/authentication-aad-configure.md)

## <a name="limit-access"></a>Limitar o acesso 

As Instâncias Geridas sQL podem ser acedidas através de um endereço IP privado. Tal como um ambiente isolado do SQL Server no local, as aplicações ou os utilizadores precisam de ter acesso à rede SQL Managed Instance (VNet) antes de se estabelecer uma ligação. Para mais informações, consulte o seguinte artigo, Ligue a sua aplicação a uma instância gerida pela [SQL](connect-application-instance.md).

Também é possível configurar um ponto final de serviço numa Instância Gerida SQL, que permite ligações públicas, da mesma forma que para a Base de Dados Azure SQL.
Para mais informações, consulte o seguinte artigo, [Configure ponto final público em 1º ponto de vista gerido pelo Azure SQL](public-endpoint-configure.md).

> [!NOTE]
> Mesmo com os pontos finais do serviço ativados, as regras de firewall da Base de [Dados Azure SQL](../database/firewall-configure.md) não se aplicam. A Azure SQL Managed Instance tem a sua própria [firewall incorporada](management-endpoint-verify-built-in-firewall.md) para gerir a conectividade.

## <a name="create-an-azure-ad-server-principal-login-using-ssms"></a>Criar um servidor Azure AD (login) utilizando SSMS

O primeiro servidor Azure AD (login) pode ser criado pela conta de administração Padrão SQL (AD não-azure) que é a `sysadmin` , ou o administrador Azure AD para a Instância Gerida SQL criada durante o processo de provisionamento. Para mais informações, consulte [a Provision um administrador de Diretório Ativo Azure para a sua Instância Gerida SQL](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).

Consulte os seguintes artigos, por exemplo, de ligação à sua Instância Gerida SQL:

- [Quickstart: Configure Azure VM para ligar a uma instância gerida pela SQL](connect-vm-instance-configure.md)
- [Quickstart: Configure uma ligação ponto-a-local a uma instância gerida pela SQL a partir do local](point-to-site-p2s-configure.md)

1. Inicie sessão na sua Instância Gerida SQL utilizando uma conta padrão de login SQL (AD não azure) que é um `sysadmin` administrador Azure AD para MI, utilizando o [SQL Server Management Studio](point-to-site-p2s-configure.md#connect-with-ssms).

2. No **Object Explorer,** clique no servidor e escolha **New Query**.

3. Na janela de consulta, utilize a seguinte sintaxe para criar um login para uma conta Azure AD local:

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    Este exemplo cria um login para a conta nativeuser@aadsqlmi.onmicrosoft.com .

    ```sql
    USE master
    GO
    CREATE LOGIN [nativeuser@aadsqlmi.onmicrosoft.com] FROM EXTERNAL PROVIDER
    GO
    ```

4. Na barra de ferramentas, selecione **Executar** para criar o login.

5. Verifique o login recém-adicionado, executando o seguinte comando T-SQL:

    ```sql
    SELECT *  
    FROM sys.server_principals;  
    GO
    ```

    ![nativo-login.png](./media/aad-security-configure-tutorial/native-login.png)

Para mais informações, consulte [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current).

## <a name="grant-permissions-to-create-logins"></a>Conceder permissões para criar logins

Para criar outros diretores de servidores Azure AD (logins), as funções ou permissões do Servidor SQL devem ser concedidas ao principal (SQL ou Azure AD).

### <a name="sql-authentication"></a>Autenticação do SQL

- Se o login for um SQL Principal, apenas os logins que fazem parte da `sysadmin` função podem usar o comando de criação para criar logins para uma conta Azure AD.

### <a name="azure-ad-authentication"></a>Autenticação do Azure AD

- Para permitir ao recém-criado diretor de servidor da AD Azure (login) a capacidade de criar outros logins para outros utilizadores, grupos ou aplicações do Azure AD, conceder a função de login `sysadmin` ou `securityadmin` servidor.
- No mínimo, **altere qualquer** permissão de LOGIN deve ser concedida ao diretor do servidor Azure AD (login) para criar outros diretores de servidores Azure AD (logins).
- Por predefinição, a permissão padrão concedida aos recém-criados diretores de servidores Azure AD (logins) em mestrado é: **CONNECT SQL** e **VIEW ANY DATABASE**.
- A função do servidor pode ser concedida a muitos diretores de `sysadmin` servidores Azure AD (logins) dentro de uma Instância Gerida SQL.

Para adicionar o login à função do `sysadmin` servidor:

1. Volte a entrar na Instância Gerida SQL ou utilize a ligação existente com o administrador Azure AD ou o SQL Principal que é `sysadmin` a .

1. No **Object Explorer,** clique no servidor e escolha **New Query**.

1. Conceda ao servidor Azure AD (login) a função do `sysadmin` servidor utilizando a seguinte sintaxe T-SQL:

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER login_name
    GO
    ```

    O exemplo seguinte concede a função do `sysadmin` servidor ao loginnativeuser@aadsqlmi.onmicrosoft.com

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER [nativeuser@aadsqlmi.onmicrosoft.com]
    GO
    ```

## <a name="create-additional-azure-ad-server-principals-logins-using-ssms"></a>Criar diretores adicionais de servidores Azure AD (logins) usando SSMS

Uma vez criado o diretor do servidor Azure AD (login) e fornecido com `sysadmin` privilégios, esse login pode criar logins adicionais utilizando a cláusula **de FORNECEDOR EXTERNO** com CREATE **LOGIN**.

1. Ligue-se à Instância Gerida SQL com o diretor do servidor Azure AD (login), utilizando o Estúdio de Gestão de Servidores SQL. Insira o nome do anfitrião SQL Managed Instance. Para autenticação em SSMS, existem três opções para escolher ao iniciar sessão com uma conta Azure AD:

   - Diretório Ativo - Universal com suporte mFA
   - Diretório Ativo - Palavra-passe
   - Diretório Ativo - Integrado </br>

     ![ssms-login-prompt.png](./media/aad-security-configure-tutorial/ssms-login-prompt.png)

     Para mais informações, consulte o seguinte artigo: [Autenticação Universal (suporte sMMS para MFA)](../database/authentication-mfa-ssms-overview.md)

1. Selecione **Ative Directy - Universal com suporte mFA**. Isto traz uma janela de login de autenticação multi-factor (MFA). Inscreva-se com a sua senha de ad.ida e resumida.

    ![mfa-login-prompt.png](./media/aad-security-configure-tutorial/mfa-login-prompt.png)

1. No SSMS **Object Explorer,** clique no servidor e escolha **New Query**.
1. Na janela de consulta, utilize a seguinte sintaxe para criar um login para outra conta Azure AD:

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    Este exemplo cria um login para o utilizador da AD bob@aadsqlmi.net Azure, cujo domínio aadsqlmi.net é federado com o Aadsqlmi.onmicrosoft.com Azure AD.

    Execute o seguinte comando T-SQL. As contas AD Federated Azure são as substituições sQL Managed Instance para logins e utilizadores do Windows no local.

    ```sql
    USE master
    GO
    CREATE LOGIN [bob@aadsqlmi.net] FROM EXTERNAL PROVIDER
    GO
    ```

1. Crie uma base de dados no SQL Managed Instance utilizando a sintaxe [CREATE DATABASE.](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current) Esta base de dados será utilizada para testar os logins dos utilizadores na secção seguinte.
    1. No **Object Explorer,** clique no servidor e escolha **New Query**.
    1. Na janela de consulta, utilize a seguinte sintaxe para criar uma base de dados chamada **MyMITestDB**.

        ```sql
        CREATE DATABASE MyMITestDB;
        GO
        ```

1. Crie um login SQL Managed Instance para um grupo em Azure AD. O grupo terá de existir em Azure AD antes de poder adicionar o login à Instância Gerida SQL. Consulte [criar um grupo básico e adicione membros utilizando o Diretório Ativo Azure](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Crie um _grupo mygroup_ e adicione membros a este grupo.

1. Abra uma nova janela de consulta no Estúdio de Gestão de Servidores SQL.

    Este exemplo pressupõe que existe um grupo chamado _mygroup_ no Azure AD. Execute o seguinte comando:

    ```sql
    USE master
    GO
    CREATE LOGIN [mygroup] FROM EXTERNAL PROVIDER
    GO
    ```

1. Como teste, inicie sessão na Instância Gerida SQL com o recém-criado login ou grupo. Abra uma nova ligação com a Instância Gerida SQL e utilize o novo login quando autenticar.
1. No **Object Explorer,** clique no servidor e escolha **New Question ry** para a nova ligação.
1. Verifique as permissões do servidor do recém-criado diretor de servidor da AD (login) executando o seguinte comando:

      ```sql
      SELECT * FROM sys.fn_my_permissions (NULL, 'DATABASE')
      GO
      ```

> [!NOTE]
> Os utilizadores de hóspedes da Azure AD são suportados para logins SQL Managed Instance, apenas quando adicionados como parte de um Grupo Azure AD. Um utilizador convidado da Azure AD é uma conta que é convidada para o Azure AD a que pertence a SQL Managed Instance, de outro Anúncio Azure. Por exemplo, joe@contoso.com (Conta AD Azure) ou steve@outlook.com (Conta MSA) pode ser adicionado a um grupo no AD Adsqlmi Azure. Uma vez adicionados os utilizadores a um grupo, pode ser criado um login na base de dados **master** de Instância Gerida SQL para o grupo utilizando a sintaxe **CREATE LOGIN.** Os utilizadores convidados que sejam membros deste grupo podem ligar-se à Instância Gerida SQL utilizando os seus logins atuais (por exemplo, joe@contoso.com ou steve@outlook.com ).

## <a name="create-an-azure-ad-user-from-the-azure-ad-server-principal-login"></a>Criar um utilizador de Anúncio Sino Do IA (login)

A autorização para bases de dados individuais funciona da mesma forma na Instância Gerida SQL do que com o SQL Server no local. Um utilizador pode ser criado a partir de um login existente numa base de dados, e ser fornecido com permissões nessa base de dados, ou adicionado a uma função de base de dados.

Agora que criámos uma base de dados chamada **MyMITestDB,** e um login que só tem permissões predefinidas, o próximo passo é criar um utilizador a partir desse login. De momento, o login pode ligar-se à Instância Gerida sQL e ver todas as bases de dados, mas não pode interagir com as bases de dados. Se iniciar sessão com a conta AD Azure que tem as permissões predefinidas e tentar expandir a base de dados recém-criada, verá o seguinte erro:

![ssms-db-não-acessível.png](./media/aad-security-configure-tutorial/ssms-db-not-accessible.png)

Para obter mais informações sobre a concessão de permissões na base de dados, consulte [Iniciar-se com permissões](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions)de motor de base de dados .

### <a name="create-an-azure-ad-user-and-create-a-sample-table"></a>Criar um utilizador de Anúncios Azure e criar uma tabela de amostras

1. Inicie sessão na sua Instância Gerida SQL utilizando uma conta utilizando o `sysadmin` Estúdio de Gestão de Servidores SQL.
1. No **Object Explorer,** clique no servidor e escolha **New Query**.
1. Na janela de consulta, utilize a seguinte sintaxe para criar um utilizador Azure AD a partir de um servidor Azure AD (login):

    ```sql
    USE <Database Name> -- provide your database name
    GO
    CREATE USER user_name FROM LOGIN login_name
    GO
    ```

    O exemplo seguinte cria um utilizador bob@aadsqlmi.net a partir do loginbob@aadsqlmi.net:

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [bob@aadsqlmi.net] FROM LOGIN [bob@aadsqlmi.net]
    GO
    ```

1. Também é suportado para criar um utilizador Azure AD a partir de um servidor Azure AD (login) que é um grupo.

    O exemplo seguinte cria um login para o grupo Azure AD _mygroup_ que existe no seu Azure AD.

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [mygroup] FROM LOGIN [mygroup]
    GO
    ```

    Todos os utilizadores que pertencem ao **mygroup** podem aceder à base de dados **MyMITestDB.**

    > [!IMPORTANT]
    > Ao criar um **UTILIZADOR** a partir de um servidor Azure AD (login), especifique o user_name como o mesmo login_name do **LOGIN**.

    Para mais informações, consulte [CREATE USER](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current).

1. Numa nova janela de consulta, crie uma tabela de teste utilizando o seguinte comando T-SQL:

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

1. Criar uma ligação em SSMS com o utilizador que foi criado. Vai notar que não pode ver a mesa **TestTable** que foi criada pelo `sysadmin` anterior. Precisamos de fornecer ao utilizador permissões para ler dados da base de dados.

1. Pode verificar a permissão atual que o utilizador tem executando o seguinte comando:

    ```sql
    SELECT * FROM sys.fn_my_permissions('MyMITestDB','DATABASE')
    GO
    ```

### <a name="add-users-to-database-level-roles"></a>Adicionar utilizadores a funções de nível de base de dados

Para que o utilizador veja os dados na base de dados, podemos fornecer funções ao [nível da base de dados](/sql/relational-databases/security/authentication-access/database-level-roles) ao utilizador.

1. Inicie sessão na sua Instância Gerida SQL utilizando uma conta utilizando o `sysadmin` Estúdio de Gestão de Servidores SQL.

1. No **Object Explorer,** clique no servidor e escolha **New Query**.

1. Conceda ao utilizador da AD Azure a função de base de `db_datareader` dados utilizando a seguinte sintaxe T-SQL:

    ```sql
    Use <Database Name> -- provide your database name
    ALTER ROLE db_datareader ADD MEMBER user_name
    GO
    ```

    O exemplo que se segue fornece ao utilizador bob@aadsqlmi.net e ao _grupo míperes_ permissões na base de dados `db_datareader` **MyMITestDB:**

    ```sql
    USE MyMITestDB
    GO
    ALTER ROLE db_datareader ADD MEMBER [bob@aadsqlmi.net]
    GO
    ALTER ROLE db_datareader ADD MEMBER [mygroup]
    GO
    ```

1. Verifique se o utilizador da AD Azure que foi criado na base de dados existe executando o seguinte comando:

    ```sql
    SELECT * FROM sys.database_principals
    GO
    ```

1. Crie uma nova ligação à Instância Gerida SQL com o utilizador que foi adicionada à `db_datareader` função.
1. Expanda a base de dados no **Object Explorer** para ver a tabela.

    ![ssms-test-table.png](./media/aad-security-configure-tutorial/ssms-test-table.png)

1. Abra uma nova janela de consulta e execute a seguinte declaração SELECT:

    ```sql
    SELECT *
    FROM TestTable
    ```

    Consegue ver dados da mesa? Devia ver as colunas a serem devolvidas.

    ![ssms-test-table-query.png](./media/aad-security-configure-tutorial/ssms-test-table-query.png)

## <a name="impersonate-azure-ad-server-level-principals-logins"></a>Personime os diretores de servidor estoque do Servidor Azure (logins)

A Instância Gerida sQL suporta a personificação dos diretores de nível de servidor da AD Azure (logins).

### <a name="test-impersonation"></a>Imitação de teste

1. Inicie sessão na sua Instância Gerida SQL utilizando uma conta utilizando o `sysadmin` Estúdio de Gestão de Servidores SQL.

1. No **Object Explorer,** clique no servidor e escolha **New Query**.

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

1. Utilize o seguinte comando para ver se o utilizador que se está a fazer passar ao executar o procedimento armazenado é ** \@ bob aadsqlmi.net**.

    ```sql
    Exec dbo.usp_Demo
    ```

1. Teste a personificação utilizando a declaração EXECUTE AS LOGIN:

    ```sql
    EXECUTE AS LOGIN = 'bob@aadsqlmi.net'
    GO
    SELECT SUSER_SNAME()
    REVERT
    GO
    ```

> [!NOTE]
> Apenas os principais de nível de servidor SQL (logins) que fazem parte da `sysadmin` função podem executar as seguintes operações dirigidas aos principais da AD Azure:
>
> - EXECUTAR COMO UTILIZADOR
> - EXECUTAR COMO LOGIN

## <a name="use-cross-database-queries"></a>Utilize consultas de base de dados cruzadas

As consultas de base de dados cruzadas são suportadas para contas AD Azure com diretores de servidores DaD Azure (logins). Para testar uma consulta de base de dados cruzada com um grupo de Anúncios Azure, precisamos de criar outra base de dados e tabela. Pode não criar outra base de dados e tabela se já existir uma.

1. Inicie sessão na sua Instância Gerida SQL utilizando uma conta utilizando o `sysadmin` Estúdio de Gestão de Servidores SQL.
1. No **Object Explorer,** clique no servidor e escolha **New Query**.
1. Na janela de consulta, utilize o seguinte comando para criar uma base de dados chamada **MyMITestDB2** e tabela chamada **TestTable2:**

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

1. Numa nova janela de consulta, execute o seguinte comando para criar o _mygroup_ do utilizador na nova base de dados **MyMITestDB2**, e conceda permissões SELECT nessa base de dados para _o mygroup:_

    ```sql
    USE MyMITestDB2
    GO
    CREATE USER [mygroup] FROM LOGIN [mygroup]
    GO
    GRANT SELECT TO [mygroup]
    GO
    ```

1. Assine a SQL Managed Instance usando o SQL Server Management Studio como membro do grupo Azure AD _mygroup_. Abra uma nova janela de consulta e execute a declaração SELECT de base de dados cruzada:

    ```sql
    USE MyMITestDB
    SELECT * FROM MyMITestDB2..TestTable2
    GO
    ```

    Deve ver os resultados da tabela do **TestTable2**.

## <a name="additional-supported-scenarios"></a>Cenários apoiados adicionais

- A gestão de agentes Da SQL e as execuções de emprego são suportadas para os diretores de servidores da AD Azure (logins).
- As operações de backup e restauro da base de dados podem ser executadas pelos diretores do servidor Azure AD (logins).
- [Auditoria](auditing-configure.md) de todas as declarações relacionadas com os diretores de servidores da AD Azure (logins) e eventos de autenticação.
- Ligação de administrador dedicada para os diretores de servidores DaAzure AD (logins) que são membros da `sysadmin` função de servidor.
- Os diretores de servidores Azure AD (logins) são suportados com a utilização da ferramenta [Sqlcmd Utility](/sql/tools/sqlcmd-utility) e [SQL Server Management Studio.](/sql/ssms/download-sql-server-management-studio-ssms)
- Os gatilhos de logon são suportados para eventos de logon provenientes de diretores de servidores DaAzure AD (logins).
- O Corretor de Serviços e o correio DB podem ser configurados utilizando os diretores do servidor Azure AD (logins).

## <a name="next-steps"></a>Próximos passos

### <a name="enable-security-features"></a>Ativar funcionalidades de segurança

Consulte o seguinte artigo de funcionalidades de segurança de funcionalidades de [segurança sQL Managed Instance](sql-managed-instance-paas-overview.md#security-features) para obter uma lista completa de formas de proteger a sua base de dados. São discutidos os seguintes recursos de segurança:

- [Auditoria de instância gerida pela SQL](auditing-configure.md)
- [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [Deteção de ameaças](threat-detection-configure.md)
- [Máscara de dados dinâmica](/sql/relational-databases/security/dynamic-data-masking)
- [Segurança ao Nível da Linha](/sql/relational-databases/security/row-level-security)
- [Encriptação transparente de dados (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="managed-instance-capabilities"></a>Capacidades de instância gerida

Para obter uma visão geral completa das capacidades de instância gerida sQL, consulte:

> [!div class="nextstepaction"]
> [Capacidades de instância gerida sQL](sql-managed-instance-paas-overview.md)
