---
title: Segurança de instância gerida com diretores de servidores DaD Azure (logins)
description: Aprenda sobre técnicas e funcionalidades para garantir uma instância gerida na Base de Dados Azure SQL, e use os principais servidores DaD Azure (logins)
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: GitHubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 11/06/2019
ms.openlocfilehash: bd65a21c2aa21643c76966410931949db7d17ad6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "73822794"
---
# <a name="tutorial-managed-instance-security-in-azure-sql-database-using-azure-ad-server-principals-logins"></a>Tutorial: Segurança de instância gerida na Base de Dados Azure SQL utilizando os diretores de servidores Azure AD (logins)

A instância gerida fornece quase todas as funcionalidades de segurança que o mais recente SQL Server no local (Enterprise Edition) Base de Dados tem:

- Limitação do acesso num ambiente isolado
- Utilize mecanismos de autenticação que exijam identidade (Autenticação Azure AD, SQL)
- Utilize a autorização com membros e permissões baseados em papéis
- Ativar funcionalidades de segurança

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> - Criar um servidor de servidor de Diretório Ativo Azure (AD) (login) para uma instância gerida
> - Conceder permissões aos diretores de servidores da AD (logins) em caso gerido
> - Criar utilizadores de Anúncios Azure a partir de diretores de servidores Da Azure (logins)
> - Atribuir permissões aos utilizadores da AD Azure e gerir a segurança da base de dados
> - Use a personificação com utilizadores da AD Azure
> - Utilize consultas de base de dados cruzadas com utilizadores de Anúncios Azure
> - Conheça as funcionalidades de segurança, tais como proteção de ameaças, auditoria, máscara de dados e encriptação

Para saber mais, consulte a Base de [Dados Azure SQL gerida](sql-database-managed-instance-index.yml) em termos gerais e artigos de [capacidades.](sql-database-managed-instance.md)

## <a name="prerequisites"></a>Pré-requisitos

Para completar o tutorial, certifique-se de que tem os seguintes pré-requisitos:

- [Estúdio de Gestão de Servidores SQL](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)
- Uma base de dados Azure SQL gerida
  - Siga este artigo: [Quickstart: Criar uma base de dados Azure SQL gerida](sql-database-managed-instance-get-started.md)
- Capaz de aceder à sua instância gerida e [fornecer um administrador da AD Azure para a instância gerida](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance). Para saber mais, consulte:
    - [Ligue a sua aplicação a uma instância gerida](sql-database-managed-instance-connect-app.md) 
    - [Arquitetura de conectividade de instância gerida](sql-database-managed-instance-connectivity-architecture.md)
    - [Configure e gerea autenticação de Diretório Ativo Azure com SQL](sql-database-aad-authentication-configure.md)

## <a name="limiting-access-to-your-managed-instance"></a>Limitando o acesso à sua instância gerida

As instâncias geridas podem ser acedidas através de um endereço IP privado. Tal como um ambiente isolado do SQL Server no local, as aplicações ou os utilizadores precisam de ter acesso à rede de instâncias geridas (VNet) antes de se estabelecer uma ligação. Para mais informações, consulte o seguinte artigo, [Ligue a sua aplicação a uma instância gerida](sql-database-managed-instance-connect-app.md).

Também é possível configurar um ponto final de serviço na instância gerida, que permite ligações públicas, da mesma forma que a Base de Dados Azure SQL. Para mais informações, consulte o seguinte artigo, [Configure ponto final público na base de dados Azure SQL gerida](sql-database-managed-instance-public-endpoint-configure.md).

> [!NOTE] 
> Mesmo com os pontos finais do serviço ativados, as regras de firewall da Base de [Dados SQL](sql-database-firewall-configure.md) não se aplicam. O caso gerido tem a sua própria [firewall incorporada](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md) para gerir a conectividade.

## <a name="create-an-azure-ad-server-principal-login-for-a-managed-instance-using-ssms"></a>Criar um servidor Azure AD (login) para uma instância gerida usando SSMS

O primeiro servidor Azure AD (login) pode ser criado pela conta padrão do SQL `sysadmin`Server (AD não-azure) que é a , ou o administrador azure AD para a instância gerida criada durante o processo de provisionamento. Para mais informações, consulte [a Provision um administrador de Diretório Ativo Azure para a sua instância gerida](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance). Esta funcionalidade mudou desde os [diretores de servidores da AD Azure.](sql-database-aad-authentication-configure.md#new-azure-ad-admin-functionality-for-mi)

Consulte os seguintes artigos, por exemplo, de ligação à sua instância gerida:

- [Quickstart: Configure Azure VM para ligar a uma instância gerida](sql-database-managed-instance-configure-vm.md)
- [Quickstart: Configure uma ligação ponto-a-local a uma instância gerida a partir do local](sql-database-managed-instance-configure-p2s.md)

1. Inicie sessão na sua instância gerida utilizando uma conta Padrão SQL `sysadmin` Server (AD não azure) que é um administrador Azure AD para MI, utilizando o [SQL Server Management Studio](sql-database-managed-instance-configure-p2s.md#use-ssms-to-connect-to-the-managed-instance).

2. No **Object Explorer,** clique no servidor e escolha **New Query**.

3. Na janela de consulta, utilize a seguinte sintaxe para criar um login para uma conta Azure AD local:

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    Este exemplo cria um login para a conta nativeuser@aadsqlmi.onmicrosoft.com.

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

    ![nativo-login.png](media/sql-database-managed-instance-security-tutorial/native-login.png)

Para mais informações, consulte [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current).

## <a name="granting-permissions-to-allow-the-creation-of-managed-instance-logins"></a>Concessão de permissões para permitir a criação de logins de instância geridas

Para criar outros diretores de servidores Azure AD (logins), as funções ou permissões do Servidor SQL devem ser concedidas ao principal (SQL ou Azure AD).

### <a name="sql-authentication"></a>Autenticação do SQL

- Se o login for um SQL Principal, apenas `sysadmin` os logins que fazem parte da função podem usar o comando de criação para criar logins para uma conta Azure AD.

### <a name="azure-ad-authentication"></a>Autenticação do Azure AD

- Para permitir ao recém-criado diretor de servidor da AD Azure (login) a capacidade de criar outros logins para outros utilizadores, grupos ou aplicações do Azure AD, conceder a função de login `sysadmin` ou `securityadmin` servidor. 
- No mínimo, **altere qualquer** permissão de LOGIN deve ser concedida ao diretor do servidor Azure AD (login) para criar outros diretores de servidores Azure AD (logins). 
- Por predefinição, a permissão padrão concedida aos recém-criados diretores de servidores Azure AD (logins) em mestrado é: **CONNECT SQL** e **VIEW ANY DATABASE**.
- A `sysadmin` função do servidor pode ser concedida a muitos diretores de servidores DaD Azure (logins) dentro de uma instância gerida.

Para adicionar o login à função do `sysadmin` servidor:

1. Volte a entrar na instância gerida ou utilize a ligação existente com o administrador da `sysadmin`AD Azure ou com o Diretor SQL que seja a .

1. No **Object Explorer,** clique no servidor e escolha **New Query**.

1. Conceda ao servidor Azure AD `sysadmin` (login) a função do servidor utilizando a seguinte sintaxe T-SQL:

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER login_name
    GO
    ```

    O exemplo seguinte `sysadmin` concede a função do servidor ao loginnativeuser@aadsqlmi.onmicrosoft.com

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER [nativeuser@aadsqlmi.onmicrosoft.com]
    GO
    ```

## <a name="create-additional-azure-ad-server-principals-logins-using-ssms"></a>Criar diretores adicionais de servidores Azure AD (logins) usando SSMS

Uma vez criado o diretor do servidor Azure AD `sysadmin` (login) e fornecido com privilégios, esse login pode criar logins adicionais utilizando a cláusula **de FORNECEDOR EXTERNO** com CREATE **LOGIN**.

1. Ligue-se à instância gerida com o diretor do servidor Azure AD (login), utilizando o Estúdio de Gestão de Servidores SQL. Insira o nome do anfitrião da sua instância gerida. Para autenticação em SSMS, existem três opções para escolher ao iniciar sessão com uma conta Azure AD:

   - Diretório Ativo - Universal com suporte mFA
   - Diretório Ativo - Palavra-passe
   - Diretório Ativo - Integrado </br>

     ![ssms-login-prompt.png](media/sql-database-managed-instance-security-tutorial/ssms-login-prompt.png)

     Para mais informações, consulte o seguinte artigo: Autenticação Universal com Base de [Dados SQL e Armazém de Dados SQL (suporte SSMS para MFA)](sql-database-ssms-mfa-authentication.md)

1. Selecione **Ative Directy - Universal com suporte mFA**. Isto traz uma janela de login de autenticação multi-factor (MFA). Inscreva-se com a sua senha de ad.ida e resumida.

    ![mfa-login-prompt.png](media/sql-database-managed-instance-security-tutorial/mfa-login-prompt.png)

1. No SSMS **Object Explorer,** clique no servidor e escolha **New Query**.
1. Na janela de consulta, utilize a seguinte sintaxe para criar um login para outra conta Azure AD:

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    Este exemplo cria um login para bob@aadsqlmi.neto utilizador da AD Azure, cujo domínio aadsqlmi.net é federado com o aadsqlmi.onmicrosoft.com Azure AD.

    Execute o seguinte comando T-SQL. As contas ad's Federated Azure são as substituições de instância geridas para logins e utilizadores do Windows no local.

    ```sql
    USE master
    GO
    CREATE LOGIN [bob@aadsqlmi.net] FROM EXTERNAL PROVIDER
    GO
    ```

1. Crie uma base de dados na instância gerida utilizando a sintaxe [CREATE DATABASE.](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current) Esta base de dados será utilizada para testar os logins dos utilizadores na secção seguinte.
    1. No **Object Explorer,** clique no servidor e escolha **New Query**.
    1. Na janela de consulta, utilize a seguinte sintaxe para criar uma base de dados chamada **MyMITestDB**.

        ```sql
        CREATE DATABASE MyMITestDB;
        GO
        ```

1. Crie um login de instância gerido para um grupo em Azure AD. O grupo terá de existir em Azure AD antes de poder adicionar o login à instância gerida. Consulte [criar um grupo básico e adicione membros utilizando o Diretório Ativo Azure](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Crie um _grupo mygroup_ e adicione membros a este grupo.

1. Abra uma nova janela de consulta no Estúdio de Gestão de Servidores SQL.

    Este exemplo pressupõe que existe um grupo chamado _mygroup_ no Azure AD. Execute o seguinte comando:

    ```sql
    USE master
    GO
    CREATE LOGIN [mygroup] FROM EXTERNAL PROVIDER
    GO
    ```

1. Como teste, inicie sessão na instância gerida com o recém-criado login ou grupo. Abra uma nova ligação à instância gerida e utilize o novo login quando autenticar.
1. No **Object Explorer,** clique no servidor e escolha **New Question ry** para a nova ligação.
1. Verifique as permissões do servidor do recém-criado diretor de servidor da AD (login) executando o seguinte comando:

      ```sql
      SELECT * FROM sys.fn_my_permissions (NULL, 'DATABASE')
      GO
      ```

> [!NOTE]
> Os utilizadores de hóspedes da Azure AD são suportados para logins de instância geridos, apenas quando adicionados como parte de um Grupo Azure AD. Um utilizador convidado da Azure AD é uma conta que é convidada para o Azure AD a que a instância gerida pertence, a partir de outro Anúncio Azure. Por exemplo, joe@contoso.com (Conta AD steve@outlook.com Azure) ou (Conta MSA) pode ser adicionado a um grupo no AD Adsqlmi Azure. Uma vez adicionados os utilizadores a um grupo, pode ser criado um login na base de dados **principal** de instância gerida para o grupo utilizando a sintaxe **CREATE LOGIN.** Os utilizadores convidados que sejam membros deste grupo podem ligar-se à joe@contoso.com steve@outlook.cominstância gerida utilizando os seus logins atuais (por exemplo, ou ).

## <a name="create-an-azure-ad-user-from-the-azure-ad-server-principal-login-and-give-permissions"></a>Criar um utilizador de Anúncio Sino Do Azure AD (login) e dar permissões

A autorização para bases de dados individuais funciona da mesma forma em caso gerido como faz com o SQL Server no local. Um utilizador pode ser criado a partir de um login existente numa base de dados, e ser fornecido com permissões nessa base de dados, ou adicionado a uma função de base de dados.

Agora que criámos uma base de dados chamada **MyMITestDB,** e um login que só tem permissões predefinidas, o próximo passo é criar um utilizador a partir desse login. De momento, o login pode ligar-se à instância gerida e ver todas as bases de dados, mas não pode interagir com as bases de dados. Se iniciar sessão com a conta AD Azure que tem as permissões predefinidas e tentar expandir a base de dados recém-criada, verá o seguinte erro:

![ssms-db-não-acessível.png](media/sql-database-managed-instance-security-tutorial/ssms-db-not-accessible.png)

Para obter mais informações sobre a concessão de permissões na base de dados, consulte [Iniciar-se com permissões](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions)de motor de base de dados .

### <a name="create-an-azure-ad-user-and-create-a-sample-table"></a>Criar um utilizador de Anúncios Azure e criar uma tabela de amostras

1. Inicie sessão na sua `sysadmin` instância gerida utilizando uma conta utilizando o Estúdio de Gestão de Servidores SQL.
1. No **Object Explorer,** clique no servidor e escolha **New Query**.
1. Na janela de consulta, utilize a seguinte sintaxe para criar um utilizador Azure AD a partir de um servidor Azure AD (login):

    ```sql
    USE <Database Name> -- provide your database name
    GO
    CREATE USER user_name FROM LOGIN login_name
    GO
    ```

    O exemplo seguinte bob@aadsqlmi.net cria um utilizador a partir do loginbob@aadsqlmi.net:

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

1. Inicie sessão na sua `sysadmin` instância gerida utilizando uma conta utilizando o Estúdio de Gestão de Servidores SQL.

1. No **Object Explorer,** clique no servidor e escolha **New Query**.

1. Conceda ao utilizador da `db_datareader` AD Azure a função de base de dados utilizando a seguinte sintaxe T-SQL:

    ```sql
    Use <Database Name> -- provide your database name
    ALTER ROLE db_datareader ADD MEMBER user_name
    GO
    ```

    O exemplo que se bob@aadsqlmi.net segue fornece ao `db_datareader` utilizador e ao _grupo míperes_ permissões na base de dados **MyMITestDB:**

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

1. Crie uma nova ligação à instância gerida com `db_datareader` o utilizador que foi adicionada à função.
1. Expanda a base de dados no **Object Explorer** para ver a tabela.

    ![ssms-test-table.png](media/sql-database-managed-instance-security-tutorial/ssms-test-table.png)

1. Abra uma nova janela de consulta e execute a seguinte declaração SELECT:

    ```sql
    SELECT *
    FROM TestTable
    ```

    Consegue ver dados da mesa? Devia ver as colunas a serem devolvidas.

    ![ssms-test-table-query.png](media/sql-database-managed-instance-security-tutorial/ssms-test-table-query.png)

## <a name="impersonating-azure-ad-server-level-principals-logins"></a>Personificando os principais do servidor Azure AD (logins)

A instância gerida suporta a personificação dos diretores de nível de servidor da AD Azure (logins).

### <a name="test-impersonation"></a>Imitação de teste

1. Inicie sessão na sua `sysadmin` instância gerida utilizando uma conta utilizando o Estúdio de Gestão de Servidores SQL.

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

1. Utilize o seguinte comando para ver se o utilizador que se está a fazer passar ao executar o procedimento armazenado é **bob\@aadsqlmi.net**.

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
> Apenas os principais de nível de servidor SQL `sysadmin` (logins) que fazem parte da função podem executar as seguintes operações dirigidas aos principais da AD Azure: 
> - EXECUTAR COMO UTILIZADOR
> - EXECUTAR COMO LOGIN

## <a name="using-cross-database-queries-in-managed-instances"></a>Utilização de consultas de base de dados cruzadas em casos geridos

As consultas de base de dados cruzadas são suportadas para contas AD Azure com diretores de servidores DaD Azure (logins). Para testar uma consulta de base de dados cruzada com um grupo de Anúncios Azure, precisamos de criar outra base de dados e tabela. Pode não criar outra base de dados e tabela se já existir uma.

1. Inicie sessão na sua `sysadmin` instância gerida utilizando uma conta utilizando o Estúdio de Gestão de Servidores SQL.
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

1. Assine a instância gerida usando o SQL Server Management Studio como membro do grupo Azure AD _mygroup_. Abra uma nova janela de consulta e execute a declaração SELECT de base de dados cruzada:

    ```sql
    USE MyMITestDB
    SELECT * FROM MyMITestDB2..TestTable2
    GO
    ```

    Deve ver os resultados da tabela do **TestTable2**.

## <a name="additional-scenarios-supported-for-azure-ad-server-principals-logins"></a>Cenários adicionais suportados para os diretores de servidores da AD Azure (logins)

- A gestão de agentes Da SQL e as execuções de emprego são suportadas para os diretores de servidores da AD Azure (logins).
- As operações de backup e restauro da base de dados podem ser executadas pelos diretores do servidor Azure AD (logins).
- [Auditoria](sql-database-managed-instance-auditing.md) de todas as declarações relacionadas com os diretores de servidores da AD Azure (logins) e eventos de autenticação.
- Ligação de administrador dedicada para os diretores de servidores DaAzure AD (logins) que são membros da `sysadmin` função de servidor.
- Os diretores de servidores Azure AD (logins) são suportados com a utilização da ferramenta [Sqlcmd Utility](/sql/tools/sqlcmd-utility) e [SQL Server Management Studio.](/sql/ssms/download-sql-server-management-studio-ssms)
- Os gatilhos de logon são suportados para eventos de logon provenientes de diretores de servidores DaAzure AD (logins).
- O Corretor de Serviços e o correio DB podem ser configurados utilizando os diretores do servidor Azure AD (logins).


## <a name="next-steps"></a>Passos seguintes

### <a name="enable-security-features"></a>Ativar funcionalidades de segurança

Consulte o seguinte artigo de funcionalidades de segurança de funcionalidades de segurança de recursos de instância [geridas](sql-database-managed-instance.md#azure-sql-database-security-features) para uma lista completa de formas de proteger a sua base de dados. São discutidos os seguintes recursos de segurança:

- [Auditoria de instância gerida](sql-database-managed-instance-auditing.md) 
- [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [Deteção de ameaças](sql-database-managed-instance-threat-detection.md) 
- [Máscara de dados dinâmica](/sql/relational-databases/security/dynamic-data-masking)
- [Segurança ao Nível da Linha](/sql/relational-databases/security/row-level-security) 
- [Encriptação transparente de dados (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="managed-instance-capabilities"></a>Funcionalidades de instância gerida

Para uma visão geral completa das capacidades de instância geridas, consulte:

> [!div class="nextstepaction"]
> [Funcionalidades de instância gerida](sql-database-managed-instance.md)
