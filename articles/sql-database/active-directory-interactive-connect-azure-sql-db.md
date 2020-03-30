---
title: ActiveDirectoryInteractive liga-se ao SQL
description: C# Exemplo de código, com explicações, para a ligação à Base de Dados Azure SQL utilizando o modo SqlAuthenticationMethod.ActiveDirectoryInteractive.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: active directory
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: MirekS
ms.reviewer: GeneMi, vanto
ms.date: 10/11/2019
ms.openlocfilehash: 5e7d58e5e0fc79e407e77ae9d73314a1d5d22666
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73692297"
---
# <a name="connect-to-azure-sql-database-with-azure-multi-factor-authentication"></a>Ligue-se à base de dados Azure SQL com autenticação de vários fatores Azure

Este artigo fornece um programa C# que se conecta à Base de Dados Azure SQL. O programa utiliza a autenticação do modo interativo, que suporta a [autenticação de multifactor do Azure.](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)

Para obter mais informações sobre o suporte de autenticação multi-factor para ferramentas SQL, consulte o [suporte do Diretório Ativo Azure nas Ferramentas de Dados do Servidor SQL (SSDT)](https://docs.microsoft.com/sql/ssdt/azure-active-directory).

## <a name="multi-factor-authentication-for-azure-sql-database"></a>Autenticação multi-factor para base de dados Azure SQL

A partir da versão 4.7.2 do [`SqlAuthenticationMethod`](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlauthenticationmethod) quadro .NET, `ActiveDirectoryInteractive`o enum tem um novo valor: . Num programa C# do cliente, o valor enum direciona o sistema para a utilização do modo interativo Azure Ative Directory (Azure AD) que suporta a Autenticação Multi-Factor para se ligar a uma base de dados Azure SQL. O utilizador que executa o programa vê as seguintes caixas de diálogo:

* Uma caixa de diálogo que exibe um nome de utilizador da AD Azure e pede a palavra-passe do utilizador.

   Se o domínio do utilizador for federado com AD Azure, esta caixa de diálogo não aparece, porque não é necessária nenhuma palavra-passe.

   Se a política de Anúncios Azure impor a autenticação multi-factor ao utilizador, as próximas duas caixas de diálogo são apresentadas.

* Na primeira vez que um utilizador passa pela Autenticação Multi-Factor, o sistema apresenta uma caixa de diálogo que pede um número de telemóvel para enviar mensagens de texto. Cada mensagem fornece o código de *verificação* que o utilizador deve introduzir na caixa de diálogo seguinte.

* Uma caixa de diálogo que pede um código de verificação de autenticação multi-factor, que o sistema enviou para um telemóvel.

Para obter informações sobre como configurar o Azure AD para exigir a autenticação multi-factor, consulte [Iniciar-se com autenticação de multi-factor sinuoso na nuvem](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud).

Para obter imagens destas caixas de diálogo, consulte [a autenticação multifactor Configure para o Estúdio de Gestão de Servidores SQL e a AD Azure](sql-database-ssms-mfa-authentication-configure.md).

> [!TIP]
> Pode pesquisar .NET Framework APIs com a [página de ferramentas do navegador .NET API](https://docs.microsoft.com/dotnet/api/).
>
> Também pode pesquisar diretamente com o [opcional ?term=&lt;parâmetro&gt; ](https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod)de valor de pesquisa .

## <a name="configure-your-c-application-in-the-azure-portal"></a>Configure a sua aplicação C# no portal Azure

Antes de começar, deverá ter um servidor de base de [dados Azure SQL](sql-database-get-started-portal.md) criado e disponível.

### <a name="register-your-app-and-set-permissions"></a>Registe a sua aplicação e detete idas às permissões

Para utilizar a autenticação Azure AD, o seu programa C# tem de se registar como uma aplicação Azure AD. Para registar uma aplicação, tem de ser um administrador da AD Azure ou um utilizador atribuído ao papel de *Desenvolvedor de Aplicações* AD Azure. Para obter mais informações sobre funções de atribuição, consulte funções de [administrador e não administrador para utilizadores com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

O preenchimento de uma inscrição na aplicação gera e exibe um ID de **aplicação**. O seu programa tem de incluir este ID para se ligar.

Para registar e definir as permissões necessárias para a sua aplicação:

1. No portal Azure, selecione Registos de Aplicações de **Diretório** > Ativo Azure Novas**inscrições.** > **New registration**

    ![Registo da aplicação](media/active-directory-interactive-connect-azure-sql-db/image1.png)

    Após a criação do registo da aplicação, o valor de ID da **aplicação** é gerado e exibido.

    ![Id da aplicação apresentado](media/active-directory-interactive-connect-azure-sql-db/image2.png)

2. Selecione **permissões** > API**Adicione uma permissão**.

    ![Configurações de permissões para aplicação registada](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-c32.png)

3. Selecione **APIs a minha organização utiliza** > tipo **Debase Azure SQL** na > de pesquisa e selecione **Azure SQL Database**.

    ![Adicione acesso à API para base de dados Azure SQL](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-Azure-sql-db-d11.png)

4. Selecione **permissões** > delegadas**user_impersonation** > **Adicionar permissões**.

    ![Delegado permissões à API para base de dados Azure SQL](media/active-directory-interactive-connect-azure-sql-db/sshot-add-api-access-azure-sql-db-delegated-permissions-checkbox-e14.png)

### <a name="set-an-azure-ad-admin-for-your-sql-database-server"></a>Detete um administrador Azure AD para o seu servidor de base de dados SQL

Para que o seu programa C# seja executado, um servidor Azure SQL precisa de atribuir um administrador Azure AD para o seu servidor de base de dados SQL. 

Na página **do Servidor SQL,** selecione **administração** > de diretório ativo**Configuração**de administrador .

Para obter mais informações sobre os administradores da AD Azure e utilizadores para a Base de Dados Azure SQL, consulte as imagens em Configure e gere a [autenticação do Diretório Ativo Azure com base de dados SQL](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server).

### <a name="add-a-non-admin-user-to-a-specific-database-optional"></a>Adicione um utilizador não administrativo a uma base de dados específica (opcional)

Um administrador da AD Azure para um servidor de base de dados SQL pode executar o programa de exemplo C#. Um utilizador da AD Azure pode executar o programa se estiver na base de dados. Um administrador Azure AD SQL ou um utilizador da AD Azure que já existe na base de dados e tem a `ALTER ANY USER` permissão na base de dados pode adicionar um utilizador.

Pode adicionar um utilizador à base de [`Create User`](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql) dados com o comando SQL. Um exemplo é `CREATE USER [<username>] FROM EXTERNAL PROVIDER`.

Para mais informações, consulte [A autenticação de diretório ativo Azure para autenticação com base de dados SQL, Instância Gerida ou Armazém de Dados SQL](sql-database-aad-authentication.md).

## <a name="new-authentication-enum-value"></a>Novo valor enum de autenticação

O exemplo C# baseia-se no espaço de [`System.Data.SqlClient`](https://docs.microsoft.com/dotnet/api/system.data.sqlclient) nome. De especial interesse para a autenticação `SqlAuthenticationMethod`multi-factor é o enum, que tem os seguintes valores:

- `SqlAuthenticationMethod.ActiveDirectoryInteractive`

   Utilize este valor com um nome de utilizador da AD Azure para implementar a autenticação multi-factor. Este valor é o foco do presente artigo. Produz uma experiência interativa exibindo caixas de diálogo para a palavra-passe do utilizador e, em seguida, para validação de autenticação multi-factor se a autenticação multi-factor for imposta a este utilizador. Este valor está disponível a partir da versão .NET Framework 4.7.2.

- `SqlAuthenticationMethod.ActiveDirectoryIntegrated`

  Use este valor para uma conta *federada.* Para uma conta federada, o nome de utilizador é conhecido do domínio Windows. Este método de autenticação não suporta a autenticação multi-factor.

- `SqlAuthenticationMethod.ActiveDirectoryPassword`

  Utilize este valor para autenticação que requer um nome de utilizador e senha de utilizador da AD Azure. A Base de Dados Azure SQL faz a autenticação. Este método não suporta a autenticação multi-factor.

## <a name="set-c-parameter-values-from-the-azure-portal"></a>Definir valores de parâmetro C# do portal Azure

Para que o programa C# seja executado com sucesso, é necessário atribuir valores adequados a campos estáticos. Aqui são mostrados campos com valores de exemplo. Também são mostrados os locais do portal Azure onde pode obter os valores necessários.

| Nome de campo estático | Valor de exemplo | Onde no portal Azure |
| :---------------- | :------------ | :-------------------- |
| Az_SQLDB_svrName | "my-sqldb-svr.database.windows.net" | **Filtro** > de servidores SQL**pelo nome** |
| AzureAD_UserID | "auser\@abc.onmicrosoft.com" | **Utilizador ativo do Diretório** > **User** > Azure**Novo utilizador** |
| Initial_DatabaseName | "myDatabase" | **Bases** > de**dados SQL** servidores SQL |
| ClientApplicationID | "a94f9c62-97fe-4d19-b06d-1111111111111111111" | **Azure Active Directory** > **Registos** > de aplicativos de diretório ativo Azure**Pesquisa por nome** > **Id aplicação** |
| RedirecionamentoUri | novo Uri("https://mywebserver.com/") | **Azure Active Directory** > **Registos** > de aplicativos de diretório ativo Azure**Search by name** > *[Your-App-registration]* > **Definições** > **RedirectURIs**<br /><br />Para este artigo, qualquer valor válido é bom para a RedirectUri, porque não é usado aqui. |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="verify-with-sql-server-management-studio"></a>Verifique com o Estúdio de Gestão de Servidores SQL

Antes de executar o programa C#, é uma boa ideia verificar se a sua configuração e configurações estão corretas no SQL Server Management Studio (SSMS). Qualquer falha do programa C# pode então ser reduzida ao código fonte.

### <a name="verify-sql-database-firewall-ip-addresses"></a>Verifique os endereços IP de firewall da Base de Dados SQL

Execute SSMS a partir do mesmo computador, no mesmo edifício, onde planeia executar o programa C#. Para este teste, qualquer modo de **autenticação** está ok. Se houver alguma indicação de que a firewall do servidor de base de dados não está a aceitar o seu endereço IP, consulte as regras de firewall de nível de servidor de [base de dados Azure SQL e](sql-database-firewall-configure.md) de nível de base de dados para obter ajuda.

### <a name="verify-azure-active-directory-multi-factor-authentication"></a>Verifique a autenticação de vários fatores de diretório ativo do Azure

Executar sSMS novamente, desta vez com **Autenticação** definida para **Diretório Ativo - Universal com suporte MFA**. Esta opção requer a versão SSMS 17.5 ou posterior.

Para mais informações, consulte [Configure Aautenticação multi-factor para SSMS e Azure AD](sql-database-ssms-mfa-authentication-configure.md).

> [!NOTE]
> Se for um utilizador convidado na base de dados, também precisa de fornecer o nome de domínio Azure AD para a base de dados: Selecione O nome de**domínio da AD de** **Opções** > ou ID do inquilino . Para encontrar o nome de domínio no portal Azure, selecione nomes de > **domínio personalizados**de **diretório Ativo Azure**. No programa de exemplo C#, não é necessário fornecer um nome de domínio.

## <a name="c-code-example"></a>C# exemplo de código

O exemplo C# programa baseia-se na montagem [*Microsoft.IdentityModel.Clients.ActiveDirectory*](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory) DLL.

Para instalar este pacote, no Estúdio Visual, selecione **Project** > **Manage NuGet Packages**. Procure e instale **Microsoft.IdentityModel.Clients.ActiveDirectory**.

Este é um exemplo de código fonte C#.

```csharp

using System;

// Reference to Azure AD authentication assembly
using Microsoft.IdentityModel.Clients.ActiveDirectory;

using DA = System.Data;
using SC = System.Data.SqlClient;
using AD = Microsoft.IdentityModel.Clients.ActiveDirectory;
using TX = System.Text;
using TT = System.Threading.Tasks;

namespace ADInteractive5
{
    class Program
    {
        // ASSIGN YOUR VALUES TO THESE STATIC FIELDS !!
        static public string Az_SQLDB_svrName = "<Your SQL DB server>";
        static public string AzureAD_UserID = "<Your User ID>";
        static public string Initial_DatabaseName = "<Your Database>";
        // Some scenarios do not need values for the following two fields:
        static public readonly string ClientApplicationID = "<Your App ID>";
        static public readonly Uri RedirectUri = new Uri("<Your URI>");

        public static void Main(string[] args)
        {
            var provider = new ActiveDirectoryAuthProvider();

            SC.SqlAuthenticationProvider.SetProvider(
                SC.SqlAuthenticationMethod.ActiveDirectoryInteractive,
                //SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated,  // Alternatives.
                //SC.SqlAuthenticationMethod.ActiveDirectoryPassword,
                provider);

            Program.Connection();
        }

        public static void Connection()
        {
            SC.SqlConnectionStringBuilder builder = new SC.SqlConnectionStringBuilder();

            // Program._  static values that you set earlier.
            builder["Data Source"] = Program.Az_SQLDB_svrName;
            builder.UserID = Program.AzureAD_UserID;
            builder["Initial Catalog"] = Program.Initial_DatabaseName;

            // This "Password" is not used with .ActiveDirectoryInteractive.
            //builder["Password"] = "<YOUR PASSWORD HERE>";

            builder["Connect Timeout"] = 15;
            builder["TrustServerCertificate"] = true;
            builder.Pooling = false;

            // Assigned enum value must match the enum given to .SetProvider().
            builder.Authentication = SC.SqlAuthenticationMethod.ActiveDirectoryInteractive;
            SC.SqlConnection sqlConnection = new SC.SqlConnection(builder.ConnectionString);

            SC.SqlCommand cmd = new SC.SqlCommand(
                "SELECT '******** MY QUERY RAN SUCCESSFULLY!! ********';",
                sqlConnection);

            try
            {
                sqlConnection.Open();
                if (sqlConnection.State == DA.ConnectionState.Open)
                {
                    var rdr = cmd.ExecuteReader();
                    var msg = new TX.StringBuilder();
                    while (rdr.Read())
                    {
                        msg.AppendLine(rdr.GetString(0));
                    }
                    Console.WriteLine(msg.ToString());
                    Console.WriteLine(":Success");
                }
                else
                {
                    Console.WriteLine(":Failed");
                }
                sqlConnection.Close();
            }
            catch (Exception ex)
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine("Connection failed with the following exception...");
                Console.WriteLine(ex.ToString());
                Console.ResetColor();
            }
        }
    } // EOClass Program.

    /// <summary>
    /// SqlAuthenticationProvider - Is a public class that defines 3 different Azure AD
    /// authentication methods.  The methods are supported in the new .NET 4.7.2.
    ///  . 
    /// 1. Interactive,  2. Integrated,  3. Password
    ///  . 
    /// All 3 authentication methods are based on the Azure
    /// Active Directory Authentication Library (ADAL) managed library.
    /// </summary>
    public class ActiveDirectoryAuthProvider : SC.SqlAuthenticationProvider
    {
        // Program._ more static values that you set!
        private readonly string _clientId = Program.ClientApplicationID;
        private readonly Uri _redirectUri = Program.RedirectUri;

        public override async TT.Task<SC.SqlAuthenticationToken>
            AcquireTokenAsync(SC.SqlAuthenticationParameters parameters)
        {
            AD.AuthenticationContext authContext =
                new AD.AuthenticationContext(parameters.Authority);
            authContext.CorrelationId = parameters.ConnectionId;
            AD.AuthenticationResult result;

            switch (parameters.AuthenticationMethod)
            {
                case SC.SqlAuthenticationMethod.ActiveDirectoryInteractive:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,  // "https://database.windows.net/"
                        _clientId,
                        _redirectUri,
                        new AD.PlatformParameters(AD.PromptBehavior.Auto),
                        new AD.UserIdentifier(
                            parameters.UserId,
                            AD.UserIdentifierType.RequiredDisplayableId));
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_1 == '.ActiveDirectoryIntegrated'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserCredential());
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryPassword:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_2 == '.ActiveDirectoryPassword'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserPasswordCredential(
                            parameters.UserId,
                            parameters.Password));
                    break;

                default: throw new InvalidOperationException();
            }
            return new SC.SqlAuthenticationToken(result.AccessToken, result.ExpiresOn);
        }

        public override bool IsSupported(SC.SqlAuthenticationMethod authenticationMethod)
        {
            return authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryInteractive
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryPassword;
        }
    } // EOClass ActiveDirectoryAuthProvider.
} // EONamespace.  End of entire program source code.

```

&nbsp;

Este é um exemplo da saída de teste C#.

```
[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>> ADInteractive5.exe
In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.
******** MY QUERY RAN SUCCESSFULLY!! ********

:Success

[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>>
```

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado pela Base de Dados Azure SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para estes cmdlets, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

- [Get-AzSqlServerActiveDirectoryAdministrator](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator)
