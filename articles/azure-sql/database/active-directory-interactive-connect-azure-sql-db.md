---
title: ActiveDirectoryInteractive conecta-se ao SQL
description: C# Exemplo de código, com explicações, para ligar à Base de Dados Azure SQL utilizando o modo SqlAuthenticationMethod.ActiveDirectoryInteractive.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: active directory, has-adal-ref, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: MirekS
ms.reviewer: vanto
ms.date: 04/23/2020
ms.openlocfilehash: bef6e6c5ef795c192a846700fc046aa20274502d
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92673408"
---
# <a name="connect-to-azure-sql-database-with-azure-multi-factor-authentication"></a>Ligue à Base de Dados Azure SQL com autenticação multi-factor Azure
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Este artigo fornece um programa C# que se conecta à Base de Dados Azure SQL. O programa utiliza a autenticação de modo interativo, que suporta [a autenticação multi-factor Azure.](../../active-directory/authentication/concept-mfa-howitworks.md)

Para obter mais informações sobre o suporte à autenticação multi-factor para ferramentas SQL, consulte [o suporte do Diretório Ativo Azure em SqL Server Data Tools (SSDT)](/sql/ssdt/azure-active-directory).

## <a name="multi-factor-authentication-for-azure-sql-database"></a>Autenticação multi-factor para base de dados Azure SQL

A partir da versão 4.7.2 do quadro.NET, o enum [`SqlAuthenticationMethod`](/dotnet/api/system.data.sqlclient.sqlauthenticationmethod) tem um novo valor: `ActiveDirectoryInteractive` . Num programa C# do cliente, o valor enum direciona o sistema para utilizar o modo interativo Azure Ative Directory (Azure AD) que suporta a Autenticação Multi-Factor para ligar à Base de Dados Azure SQL. O utilizador que executa o programa vê as seguintes caixas de diálogo:

* Uma caixa de diálogo que exibe um nome de utilizador Azure AD e pede a palavra-passe do utilizador.

   Se o domínio do utilizador for federado com Azure AD, esta caixa de diálogo não aparece, porque não é necessária nenhuma palavra-passe.

   Se a política AZure AD impor a autenticação multi-factor ao utilizador, serão apresentadas as duas caixas de diálogo seguintes.

* A primeira vez que um utilizador passa pela Autenticação Multi-Factor, o sistema apresenta uma caixa de diálogo que pede um número de telemóvel para enviar mensagens de texto. Cada mensagem fornece o *código de verificação* que o utilizador deve introduzir na caixa de diálogo seguinte.

* Uma caixa de diálogo que pede um código de verificação de autenticação multi-factor, que o sistema enviou para um telemóvel.

Para obter informações sobre como configurar a AD Azure para exigir autenticação multi-factor, consulte [Começar com autenticação multi-factor Azure na nuvem](../../active-directory/authentication/howto-mfa-getstarted.md).

Para imagens destas caixas de diálogo, consulte [a autenticação de vários fatores configurar para o SQL Server Management Studio e AZure AD](authentication-mfa-ssms-configure.md).

> [!TIP]
> Pode pesquisar .NET Framework APIs com a [página de ferramentas .NET API Browser](/dotnet/api/).
>
> Também pode pesquisar diretamente com o [parâmetro opcional ?term= &lt; &gt; valor de pesquisa](/dotnet/api/?term=SqlAuthenticationMethod).

## <a name="configure-your-c-application-in-the-azure-portal"></a>Configure a sua aplicação C# no portal Azure

Antes de começar, deverá ter um [servidor SQL lógico](logical-servers.md) criado e disponível.

### <a name="register-your-app-and-set-permissions"></a>Registe a sua app e descreva permissões

Para utilizar a autenticação Azure AD, o seu programa C# tem de se registar como uma aplicação AD Azure. Para registar uma aplicação, tem de ser um administrador AD Azure ou um utilizador que atribuiu a função *de Desenvolvedor de Aplicações* AD Azure. Para obter mais informações sobre a atribuição de funções, consulte [funções de administrador e não administrador de atribuir aos utilizadores com Diretório Ativo Azure](../../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

O preenchimento de um registo de aplicações gera e exibe um **ID de aplicação.** O seu programa tem de incluir este ID para se ligar.

Para registar e definir as permissões necessárias para a sua candidatura:

1. No portal Azure, selecione **Azure Ative Directory**  >  **App registra**  >  **Novo registo** .

    ![Registo da aplicação](./media/active-directory-interactive-connect-azure-sql-db/image1.png)

    Após a criação do registo da aplicação, o valor **de ID da aplicação** é gerado e exibido.

    ![ID da aplicação exibido](./media/active-directory-interactive-connect-azure-sql-db/image2.png)

2. Selecione **permissões API**  >  **Adicione uma permissão** .

    ![Permissões de definições para aplicação registada](./media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-c32.png)

3. Selecione **APIs a minha organização utiliza** > tipo **Azure SQL Database** na > de pesquisa e selecione **Azure SQL Database** .

    ![Adicionar acesso à API para Azure SQL Database](./media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-Azure-sql-db-d11.png)

4. Selecione **permissões delegadas**  >  **user_impersonation**  >  **Adicionar permissões** .

    ![Autorizações de delegado à API para Azure SQL Database](./media/active-directory-interactive-connect-azure-sql-db/sshot-add-api-access-azure-sql-db-delegated-permissions-checkbox-e14.png)

### <a name="set-an-azure-ad-admin-for-your-server"></a>Desconfie de um administrador AD Azure para o seu servidor

Para que o seu programa C# possa ser executado, um administrador lógico do [servidor SQL](logical-servers.md) precisa de atribuir um administrador AD AD Azure para o seu servidor.

Na página do **servidor SQL,** selecione **Ative Directory**  >  **admin set** .

Para obter mais informações sobre os administradores e utilizadores da Azure AD para a Base de Dados Azure SQL, consulte as imagens em [Configurar e gerencie a autenticação do Azure Ative Directory com base de dados SQL](authentication-aad-configure.md#provision-azure-ad-admin-sql-database).

### <a name="add-a-non-admin-user-to-a-specific-database-optional"></a>Adicione um utilizador não administrativo a uma base de dados específica (opcional)

Um administrador AD AD Azure para um [servidor lógico SQL](logical-servers.md) pode executar o programa de exemplo C#. Um utilizador AZURE AD pode executar o programa se estiver na base de dados. Um administrador AD SQL Azure ou um utilizador Azure AD que já exista na base de dados e tenha a `ALTER ANY USER` permissão na base de dados pode adicionar um utilizador.

Pode adicionar um utilizador à base de dados com o [`Create User`](/sql/t-sql/statements/create-user-transact-sql) comando SQL. Um exemplo é `CREATE USER [<username>] FROM EXTERNAL PROVIDER`.

Para obter mais informações, consulte a autenticação do [Diretório Ativo Azure para autenticação com base de dados SQL, Instância Gerida ou Azure Synapse Analytics](authentication-aad-overview.md).

## <a name="new-authentication-enum-value"></a>Novo valor enum de autenticação

O exemplo C# baseia-se no espaço de [`System.Data.SqlClient`](/dotnet/api/system.data.sqlclient) nomes. De especial interesse para a autenticação multi-factor é o `SqlAuthenticationMethod` enum, que tem os seguintes valores:

* `SqlAuthenticationMethod.ActiveDirectoryInteractive`

   Utilize este valor com um nome de utilizador Azure AD para implementar a Autenticação Multi-Factor. Este valor é o foco do presente artigo. Produz uma experiência interativa exibindo caixas de diálogo para a palavra-passe do utilizador e, em seguida, para validação de autenticação multi-factor se a autenticação multi-factor for imposta a este utilizador. Este valor está disponível a partir da versão .NET Framework 4.7.2.

* `SqlAuthenticationMethod.ActiveDirectoryIntegrated`

  Utilize este valor para uma conta *federada.* Para uma conta federada, o nome de utilizador é conhecido do domínio Windows. Este método de autenticação não suporta a autenticação multi-factor.

* `SqlAuthenticationMethod.ActiveDirectoryPassword`

  Utilize este valor para a autenticação que requer um nome de utilizador ED Azure e senha. A Base de Dados Azure SQL faz a autenticação. Este método não suporta a autenticação multi-factor.

> [!NOTE]
> Se estiver a utilizar o .NET Core, irá querer utilizar o espaço [de nomes Microsoft.Data.SqlClient.](/dotnet/api/microsoft.data.sqlclient?view=sqlclient-dotnet-core-1.1) Para mais informações, consulte o [seguinte blog.](https://devblogs.microsoft.com/dotnet/introducing-the-new-microsoftdatasqlclient/)

## <a name="set-c-parameter-values-from-the-azure-portal"></a>Definir valores de parâmetro C# do portal Azure

Para que o programa C# seja executado com sucesso, é necessário atribuir valores adequados a campos estáticos. Aqui são apresentados campos com valores de exemplo. Também são mostradas as localizações do portal Azure onde pode obter os valores necessários.

| Nome de campo estático | Valor de exemplo | Onde no portal Azure |
| :---------------- | :------------ | :-------------------- |
| Az_SQLDB_svrName | "my-sqldb-svr.database.windows.net" | **Servidores**  >  SQL **Filtro pelo nome** |
| AzureAD_UserID | "auser \@ abc.onmicrosoft.com" | Diretório Ativo **Azure**  >  **Utilizador**  >  **Novo utilizador convidado** |
| Initial_DatabaseName | "myDatabase" | **Servidores**  >  SQL **Bases de dados SQL** |
| ClientApplicationID | "a94f9c62-97fe-4d19-b06d-111111111111" | Diretório Ativo **Azure**  >  **Inscrições de aplicativos**  >  **Pesquisar pelo nome**  >  **ID de aplicação** |
| RedirectUri | novo Uri(" https://mywebserver.com/ ") | Diretório Ativo **Azure**  >  **Inscrições de aplicativos**  >  **Pesquisar pelo nome**  >  *[O seu registo de aplicações]*  >  **Configurações**  >  **RedirectURIs**<br /><br />Para este artigo, qualquer valor válido é bom para a RedirectUri, porque não é usado aqui. |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="verify-with-sql-server-management-studio"></a>Verifique com o SQL Server Management Studio

Antes de executar o programa C#, é uma boa ideia verificar se a sua configuração e configurações estão corretas no SQL Server Management Studio (SSMS). Qualquer falha do programa C# pode então ser reduzida ao código de origem.

### <a name="verify-server-level-firewall-ip-addresses"></a>Verifique endereços IP de firewall ao nível do servidor

Executar SSMS a partir do mesmo computador, no mesmo edifício, onde planeia executar o programa C#. Para este teste, qualquer modo **de autenticação** está ok. Se houver alguma indicação de que o servidor não está a aceitar o seu endereço IP, consulte [as regras de firewall ao nível do servidor e da base de dados](firewall-configure.md) para obter ajuda.

### <a name="verify-azure-active-directory-multi-factor-authentication"></a>Verifique a autenticação multi-factor do Diretório Ativo Azure

Executar SSMS novamente, desta vez com **autenticação** definida para **Azure Ative Diretório - Universal com MFA** . Esta opção requer a versão SSMS 17.5 ou posterior.

Para obter mais informações, consulte [a autenticação multi-factor configure para SSMS e AD AZure](authentication-mfa-ssms-configure.md).

> [!NOTE]
> Se for um utilizador convidado na base de dados, também precisa de fornecer **Options** o nome de domínio AZure AD para a base de dados: Selecione  >  **Opções Nome de domínio AD ou ID do inquilino** . Para encontrar o nome de domínio no portal Azure, selecione **Azure Ative Directory**  >  **Custom nomes de domínio** . No programa de exemplo C#, não é necessário fornecer um nome de domínio.

## <a name="c-code-example"></a>Exemplo de código C#

> [!NOTE]
> Se estiver a utilizar o .NET Core, irá querer utilizar o espaço [de nomes Microsoft.Data.SqlClient.](/dotnet/api/microsoft.data.sqlclient?view=sqlclient-dotnet-core-1.1) Para mais informações, consulte o [seguinte blog.](https://devblogs.microsoft.com/dotnet/introducing-the-new-microsoftdatasqlclient/)

O programa exemplo C# baseia-se na montagem [*Microsoft.IdentityModel.Clients.ActiveDirectory*](/dotnet/api/microsoft.identitymodel.clients.activedirectory) DLL.

Para instalar este pacote, no Estúdio Visual, selecione **Project**  >  **Manage NuGet Packages** . Procure e instale **Microsoft.IdentityModel.Clients.ActiveDirectory** .

Este é um exemplo do código fonte C#.

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
        static public string Az_SQLDB_svrName = "<Your server>";
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

```C#
[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>> ADInteractive5.exe
In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.
******** MY QUERY RAN SUCCESSFULLY!! ********

:Success

[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>>
```

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado pela Base de Dados Azure SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para estes cmdlets, consulte [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

& [Get-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator)