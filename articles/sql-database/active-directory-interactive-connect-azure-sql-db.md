---
title: ActiveDirectoryInteractive se conecta ao SQL | Documentos da Microsoft
description: Exemplo de código c#, com explicações, para ligar à base de dados do Azure SQL com o modo de SqlAuthenticationMethod.ActiveDirectoryInteractive.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: active directory
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: MirekS
ms.reviewer: GeneMi
ms.date: 03/12/2019
manager: craigg
ms.openlocfilehash: bc7274308b8a349d16866f107eac4a57e115be9e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66160881"
---
# <a name="connect-to-azure-sql-database-with-azure-multi-factor-authentication"></a>Ligar à base de dados SQL do Azure com o Azure multi-factor Authentication

Este artigo fornece um C# programa que se liga à base de dados do Azure SQL. O programa utiliza a autenticação de modo interativo, que suporta [multi-factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).

Para obter mais informações sobre o suporte de multi-factor Authentication para ferramentas SQL, consulte [suporte do Azure Active Directory no SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/azure-active-directory).

## <a name="multi-factor-authentication-for-azure-sql-database"></a>Autenticação Multifator para a base de dados SQL do Azure

A partir do .NET Framework versão 4.7.2, a enumeração [ `SqlAuthenticationMethod` ](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlauthenticationmethod) tem um novo valor: `ActiveDirectoryInteractive`. Num cliente de C# programa, o valor de enumeração direciona o sistema a utilizar o modo interativo do Azure Active Directory (Azure AD) que suporta o multi-factor Authentication para ligar a uma base de dados SQL do Azure. O utilizador que executa o programa vê as caixas de diálogo seguinte:

* Uma caixa de diálogo que apresenta um nome de utilizador do Azure AD e pede-lhe a senha do usuário.

   Se o domínio do utilizador está Federado com o Azure AD, esta caixa de diálogo não aparece, porque não é necessária nenhuma palavra-passe.

   Se a política do Azure AD impõe multi-factor Authentication no utilizador, são apresentadas as caixas de diálogo de dois.

* Na primeira vez que um utilizador passa pelo multi-factor Authentication, o sistema apresenta uma caixa de diálogo que pede-lhe enviar mensagens de texto para um número de telemóvel. Cada mensagem fornece a *código de verificação* que o usuário deve digitar na caixa de diálogo seguinte.

* Uma caixa de diálogo que pede-lhe um código de verificação de multi-factor Authentication, o que o sistema tenha enviado para um telefone celular.

Para obter informações sobre como configurar o Azure AD para exigir autenticação Multifator, consulte [introdução ao multi-factor Authentication na cloud](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud).

Para capturas de ecrã dessas caixas de diálogo, consulte [configurar a autenticação multifator para o SQL Server Management Studio e o Azure AD](sql-database-ssms-mfa-authentication-configure.md).

> [!TIP]
> Pode pesquisar APIs do .NET Framework com o [page da ferramenta de Browser de API do .NET](https://docs.microsoft.com/dotnet/api/).
>
> Também pode pesquisar diretamente com o [opcional? termo =&lt;pesquisar o valor&gt; parâmetro](https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod).

## <a name="configure-your-c-application-in-the-azure-portal"></a>Configurar o C# aplicação no portal do Azure

Antes de começar, deve ter uma [servidor de base de dados do Azure SQL](sql-database-get-started-portal.md) criado e está disponível.

### <a name="register-your-app-and-set-permissions"></a>Registar a sua aplicação e definir permissões

Para utilizar a autenticação do Azure AD, o C# programa tem de registar-se como uma aplicação do Azure AD. Para registar uma aplicação, tem de ser administrador do Azure AD ou um utilizador atribuído o Azure AD *programador da aplicação* função. Para obter mais informações sobre a atribuição de funções, consulte [atribuir funções de administrador e não-administrador aos utilizadores no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

Concluir o registo de uma aplicação gera e exibe uma **ID da aplicação**. Seu programa tem de incluir essa identificação para se ligar.

Para registar e definir as permissões necessárias para a sua aplicação:

1. No portal do Azure, selecione **do Azure Active Directory** > **registos das aplicações** > **novo registo de aplicação**.

    ![Registo da aplicação](media/active-directory-interactive-connect-azure-sql-db/image1.png)

    Depois de criar o registo de aplicações, o **ID da aplicação** valor é gerado e apresentado.

    ![ID da aplicação apresentado](media/active-directory-interactive-connect-azure-sql-db/image2.png)

2. Selecione **aplicação registada** > **definições** > **permissões obrigatórias** > **adicionar**.

    ![Definições de permissões para a aplicação registada](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-c32.png)

3. Selecione **permissões obrigatórias** > **Add** > **selecionar uma API** > **base de dados do Azure SQL**.

    ![Adicionar acesso à API para a base de dados do Azure SQL](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-Azure-sql-db-d11.png)

4. Selecione **acesso à API** > **selecionar permissões** > **permissões delegadas**.

    ![Permissões de delegado à API para a base de dados do Azure SQL](media/active-directory-interactive-connect-azure-sql-db/sshot-add-api-access-azure-sql-db-delegated-permissions-checkbox-e14.png)

### <a name="set-an-azure-ad-admin-for-your-sql-database-server"></a>Definir um administrador do Azure AD para o seu servidor de base de dados SQL

Para o C# programa para ser executado, um administrador do servidor SQL do Azure tem de atribuir um administrador do Azure AD para o seu servidor de base de dados SQL. 

Sobre o **SQL Server** página, selecione **administrador do Active Directory** > **definir administrador**.

Para obter mais informações sobre os administradores do Azure AD e os utilizadores para a base de dados do Azure SQL, consulte as capturas de ecrã [configurar e gerir a autenticação do Azure Active Directory com a base de dados SQL](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server).

### <a name="add-a-non-admin-user-to-a-specific-database-optional"></a>Adicionar um utilizador não administrador para uma base de dados específico (opcional)

Um administrador do Azure AD para um servidor de base de dados SQL pode executar o C# programa de exemplo. Um utilizador pode executar o programa que estejam na base de dados. Um administrador de SQL do Azure AD ou um utilizador do Azure AD que já existe na base de dados e tem o `ALTER ANY USER` permissão na base de dados pode adicionar um utilizador.

Pode adicionar um utilizador na base de dados com o SQL [ `Create User` ](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql) comando. Um exemplo é `CREATE USER [<username>] FROM EXTERNAL PROVIDER`.

Para obter mais informações, consulte [utilizar do Azure Active Directory a autenticação para a autenticação com base de dados SQL, instância gerida ou SQL Data Warehouse](sql-database-aad-authentication.md).

## <a name="new-authentication-enum-value"></a>Novo valor de enumeração de autenticação

O C# exemplo depende da [ `System.Data.SqlClient` ](https://docs.microsoft.com/dotnet/api/system.data.sqlclient) espaço de nomes. De especial interesse para multi-factor Authentication é a enumeração `SqlAuthenticationMethod`, que tem os seguintes valores:

- `SqlAuthenticationMethod.ActiveDirectoryInteractive`

   Utilize este valor com um nome de utilizador do Azure AD para implementar o multi-factor Authentication. Este valor é o foco deste artigo presente. Produz uma experiência interativa ao apresentar caixas de diálogo para a palavra-passe de utilizador e, em seguida, para a validação de multi-factor Authentication se o multi-factor Authentication é imposta este utilizador. Este valor está disponível a partir do .NET Framework versão 4.7.2.

- `SqlAuthenticationMethod.ActiveDirectoryIntegrated`

  Utilize este valor para um *federado* conta. Para uma conta federada, o nome de utilizador é conhecido para o domínio do Windows. Este método de autenticação não suporta o multi-factor Authentication.

- `SqlAuthenticationMethod.ActiveDirectoryPassword`

  Utilize este valor para a autenticação que requer um nome de utilizador do Azure AD e a palavra-passe. Base de dados SQL do Azure faz a autenticação. Este método não suporta o multi-factor Authentication.

## <a name="set-c-parameter-values-from-the-azure-portal"></a>Definir C# valores de parâmetro do portal do Azure

Para o C# programa para ser executado com êxito, terá de atribuir valores adequados para campos estáticos. Mostrado a seguir são campos com valores de exemplo. Mostra também são os locais de portais do Azure, onde pode obter os valores necessários.

| Nome do campo estático | Valor de exemplo | Onde no portal do Azure |
| :---------------- | :------------ | :-------------------- |
| Az_SQLDB_svrName | "my-sqldb-svr.database.windows.net" | **Servidores SQL** > **filtrar por nome** |
| AzureAD_UserID | "auser\@abc.onmicrosoft.com" | **O Azure Active Directory** > **utilizador** > **novo utilizador convidado** |
| Initial_DatabaseName | "myDatabase" | **Servidores SQL** > **bases de dados SQL** |
| ClientApplicationID | "a94f9c62-97fe-4d19-b06d-111111111111" | **O Azure Active Directory** > **registos das aplicações** > **procurar por nome** > **ID da aplicação** |
| RedirectUri | novo Uri ("https://mywebserver.com/") | **O Azure Active Directory** > **registos das aplicações** > **procurar por nome** >  *[Your--registo de aplicações]*  >  **Definições** > **RedirectURIs**<br /><br />Neste artigo, qualquer valor válido é bom para RedirectUri, porque não está a ser utilizada aqui. |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="verify-with-sql-server-management-studio"></a>Certifique-se com o SQL Server Management Studio

Antes de executar o C# programa, é uma boa idéia verificar se a configuração e as configurações estão corretas no SQL Server Management Studio (SSMS). Qualquer C# falha de programa, em seguida, pode ser reduzida ao código-fonte.

### <a name="verify-sql-database-firewall-ip-addresses"></a>Certifique-se de endereços IP de firewall de base de dados SQL

Execute o SSMS no mesmo computador, no mesmo prédio, onde que pretende executar o C# programa. Para este teste, qualquer **autenticação** modo está OK. Se há qualquer indicação de que a firewall do servidor de base de dados não está aceitando seu endereço IP, consulte [regras de firewall ao nível do servidor e ao nível da base de dados de base de dados do Azure SQL](sql-database-firewall-configure.md) para obter ajuda.

### <a name="verify-azure-active-directory-multi-factor-authentication"></a>Certifique-se de multi-factor Authentication do Azure Active Directory

Executar novamente o SSMS, desta vez com **autenticação** definida como **Active Directory - Universal com o suporte MFA**. Esta opção requer que o SSMS versão 17,5 ou posterior.

Para obter mais informações, consulte [configurar o multi-factor Authentication para o SSMS e o Azure AD](sql-database-ssms-mfa-authentication-configure.md).

> [!NOTE]
> Se for um utilizador convidado na base de dados, terá também de fornecer o nome de domínio do Azure AD para a base de dados: Selecione **opções** > **ID de inquilino ou nome de domínio do AD**. Para localizar o nome de domínio no portal do Azure, selecione **do Azure Active Directory** > **nomes de domínio personalizado**. Na C# programa de exemplo, fornecendo um nome de domínio não é necessário.

## <a name="c-code-example"></a>Exemplo de código do c#

O exemplo C# depende de programa do [ *ActiveDirectory* ](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory) assembly DLL.

Para instalar este pacote, no Visual Studio, selecione **Project** > **gerir pacotes NuGet**. Procure e instale **ActiveDirectory**.

Este é um exemplo de C# código-fonte.

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

Este é um exemplo do C# resultado do teste.

```
[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>> ADInteractive5.exe
In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.
******** MY QUERY RAN SUCCESSFULLY!! ********

:Success

[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>>
```

## <a name="next-steps"></a>Passos Seguintes

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo do PowerShell do Azure Resource Manager ainda é suportado pelo SQL Database do Azure, mas todo o desenvolvimento futuro é para o módulo de Az.Sql. Para estes cmdlets, consulte [azurerm. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

- [Get-AzSqlServerActiveDirectoryAdministrator](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator)
