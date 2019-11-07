---
title: ActiveDirectoryInteractive conecta-se ao SQL
description: C#Exemplo de código, com explicações, para se conectar ao banco de dados SQL do Azure usando o modo SqlAuthenticationMethod. ActiveDirectoryInteractive.
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
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692297"
---
# <a name="connect-to-azure-sql-database-with-azure-multi-factor-authentication"></a>Conectar-se ao banco de dados SQL do Azure com a autenticação multifator do Azure

Este artigo fornece um C# programa que se conecta ao banco de dados SQL do Azure. O programa usa a autenticação de modo interativo, que dá suporte à [autenticação multifator do Azure](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).

Para obter mais informações sobre o suporte à autenticação multifator para ferramentas SQL, consulte [suporte a Azure Active Directory no SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/azure-active-directory).

## <a name="multi-factor-authentication-for-azure-sql-database"></a>Autenticação multifator para o banco de dados SQL do Azure

A partir do .NET Framework versão 4.7.2, o [`SqlAuthenticationMethod`](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlauthenticationmethod) de enumeração tem um novo valor: `ActiveDirectoryInteractive`. Em um programa C# cliente, o valor de enumeração direciona o sistema para usar o modo interativo Azure Active Directory (AD do Azure) que dá suporte à autenticação multifator para se conectar a um banco de dados SQL do Azure. O usuário que executa o programa vê as seguintes caixas de diálogo:

* Uma caixa de diálogo que exibe um nome de usuário do Azure AD e solicita a senha do usuário.

   Se o domínio do usuário for federado com o Azure AD, essa caixa de diálogo não aparecerá, porque nenhuma senha é necessária.

   Se a política do Azure AD impõe a autenticação multifator no usuário, as próximas duas caixas de diálogo são exibidas.

* Na primeira vez que um usuário passa pela autenticação multifator, o sistema exibe uma caixa de diálogo que solicita um número de telefone celular para o qual enviar mensagens de texto. Cada mensagem fornece o *código de verificação* que o usuário deve inserir na próxima caixa de diálogo.

* Uma caixa de diálogo que solicita um código de verificação de autenticação multifator, que o sistema enviou para um telefone celular.

Para obter informações sobre como configurar o Azure AD para exigir a autenticação multifator, consulte [introdução à autenticação multifator do Azure na nuvem](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud).

Para capturas de tela dessas caixas de diálogo, consulte [configurar autenticação multifator para SQL Server Management Studio e Azure ad](sql-database-ssms-mfa-authentication-configure.md).

> [!TIP]
> Você pode pesquisar .NET Framework APIs com a [página de ferramentas do navegador de API do .net](https://docs.microsoft.com/dotnet/api/).
>
> Você também pode pesquisar diretamente com o [parâmetro opcional? Term =&lt;valor de pesquisa&gt;](https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod).

## <a name="configure-your-c-application-in-the-azure-portal"></a>Configurar seu C# aplicativo no portal do Azure

Antes de começar, você deve ter um [servidor de banco de dados SQL do Azure](sql-database-get-started-portal.md) criado e disponível.

### <a name="register-your-app-and-set-permissions"></a>Registrar seu aplicativo e definir permissões

Para usar a autenticação do Azure AD C# , seu programa deve se registrar como um aplicativo do Azure AD. Para registrar um aplicativo, você precisa ser um administrador do Azure AD ou um usuário atribuído à função de *desenvolvedor de aplicativos* do Azure AD. Para obter mais informações sobre a atribuição de funções, consulte [atribuir funções de administrador e não administrador a usuários com Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

A conclusão de um registro de aplicativo gera e exibe uma **ID de aplicativo**. Seu programa precisa incluir essa ID para se conectar.

Para registrar e definir as permissões necessárias para seu aplicativo:

1. Na portal do Azure, selecione **Azure Active Directory** > **registros de aplicativo** > **novo registro**.

    ![Registo da aplicação](media/active-directory-interactive-connect-azure-sql-db/image1.png)

    Depois que o registro do aplicativo é criado, o valor da **ID do aplicativo** é gerado e exibido.

    ![ID do aplicativo exibida](media/active-directory-interactive-connect-azure-sql-db/image2.png)

2. Selecione **permissões de API** > **Adicionar uma permissão**.

    ![Configurações de permissões para o aplicativo registrado](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-c32.png)

3. Selecione **APIs que minha organização usa** > tipo **banco de dados SQL do Azure** no > de pesquisa e selecione **banco de dados SQL do Azure**.

    ![Adicionar acesso à API para o banco de dados SQL do Azure](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-Azure-sql-db-d11.png)

4. Selecione **permissões delegadas** > **user_impersonation** > **adicionar permissões**.

    ![Delegar permissões para a API para o banco de dados SQL do Azure](media/active-directory-interactive-connect-azure-sql-db/sshot-add-api-access-azure-sql-db-delegated-permissions-checkbox-e14.png)

### <a name="set-an-azure-ad-admin-for-your-sql-database-server"></a>Definir um administrador do Azure AD para o servidor do banco de dados SQL

Para que C# o programa seja executado, um administrador do SQL Server do Azure precisa atribuir um administrador do Azure AD ao servidor do banco de dados SQL. 

Na página **SQL Server** , selecione **Active Directory admin** > **definir administrador**.

Para obter mais informações sobre administradores e usuários do Azure AD para o banco de dados SQL do Azure, consulte as capturas de tela em [configurar e gerenciar a autenticação de Azure Active Directory com o banco de dados SQL](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server).

### <a name="add-a-non-admin-user-to-a-specific-database-optional"></a>Adicionar um usuário não administrador a um banco de dados específico (opcional)

Um administrador do Azure AD para um servidor de banco de dados C# SQL pode executar o programa de exemplo. Um usuário do Azure AD pode executar o programa se ele estiver no banco de dados. Um administrador do SQL do Azure AD ou um usuário do Azure AD que já existe no banco de dados e tem a permissão `ALTER ANY USER` no banco de dados pode adicionar um usuário.

Você pode adicionar um usuário ao banco de dados com o comando SQL [`Create User`](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql) . Um exemplo é `CREATE USER [<username>] FROM EXTERNAL PROVIDER`.

Para obter mais informações, consulte [usar a autenticação Azure Active Directory para autenticação com o banco de dados SQL, instância gerenciada ou SQL data warehouse](sql-database-aad-authentication.md).

## <a name="new-authentication-enum-value"></a>Novo valor de enumeração de autenticação

O C# exemplo se baseia no namespace [`System.Data.SqlClient`](https://docs.microsoft.com/dotnet/api/system.data.sqlclient) . De interesse especial para a autenticação multifator é o `SqlAuthenticationMethod`de enumeração, que tem os seguintes valores:

- `SqlAuthenticationMethod.ActiveDirectoryInteractive`

   Use esse valor com um nome de usuário do Azure AD para implementar a autenticação multifator. Esse valor é o foco do artigo atual. Ele produz uma experiência interativa exibindo caixas de diálogo para a senha do usuário e, em seguida, para a validação da autenticação multifator se a autenticação multifator for imposta a esse usuário. Esse valor está disponível começando com .NET Framework versão 4.7.2.

- `SqlAuthenticationMethod.ActiveDirectoryIntegrated`

  Use esse valor para uma conta *federada* . Para uma conta federada, o nome de usuário é conhecido pelo domínio do Windows. Esse método de autenticação não dá suporte à autenticação multifator.

- `SqlAuthenticationMethod.ActiveDirectoryPassword`

  Use esse valor para autenticação que requer um nome de usuário e uma senha do Azure AD. O banco de dados SQL do Azure faz a autenticação. Esse método não dá suporte à autenticação multifator.

## <a name="set-c-parameter-values-from-the-azure-portal"></a>Definir C# valores de parâmetro do portal do Azure

Para que C# o programa seja executado com êxito, você precisa atribuir valores adequados a campos estáticos. Aqui estão exibidos campos com valores de exemplo. Também são mostrados os locais de portal do Azure onde você pode obter os valores necessários.

| Nome do campo estático | Valor de exemplo | Onde em portal do Azure |
| :---------------- | :------------ | :-------------------- |
| Az_SQLDB_svrName | "my-sqldb-svr.database.windows.net" | **SQL servers** > **Filtrar por nome** |
| AzureAD_UserID | "auser\@abc.onmicrosoft.com" | **Azure Active Directory** > **usuário** > **novo usuário convidado** |
| Initial_DatabaseName | MyDatabase | **SQL servers** > **bancos de dados SQL** |
| ClientApplicationID | "a94f9c62-97fe-4d19-b06d-111111111111" | **Azure Active Directory** > **registros de aplicativo** > **pesquisa por nome** > **ID do aplicativo** |
| RedirectUri | novo URI ("https://mywebserver.com/") | **Azure Active Directory** > **registros de aplicativo** > **Pesquisar por nome** >  *[seu-app-Registration]*  > **configurações** > **RedirectURIs**<br /><br />Para este artigo, qualquer valor válido é adequado para RedirectUri, pois ele não é usado aqui. |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="verify-with-sql-server-management-studio"></a>Verificar com SQL Server Management Studio

Antes de executar o C# programa, é uma boa ideia verificar se a configuração e as configurações estão corretas no SQL Server Management Studio (SSMS). Qualquer C# falha do programa pode ser limitada ao código-fonte.

### <a name="verify-sql-database-firewall-ip-addresses"></a>Verificar endereços IP do firewall do banco de dados SQL

Execute o SSMS no mesmo computador, no mesmo prédio, no qual você planeja executar o C# programa. Para esse teste, qualquer modo de **autenticação** é ok. Se houver alguma indicação de que o Firewall do servidor de banco de dados não está aceitando seu endereço IP, consulte regras de firewall no nível de [banco de dados e nível de servidor do Azure SQL Database](sql-database-firewall-configure.md) para obter ajuda.

### <a name="verify-azure-active-directory-multi-factor-authentication"></a>Verificar Azure Active Directory a autenticação multifator

Execute o SSMS novamente, desta vez com a **autenticação** definida como **Active Directory-universal com suporte a MFA**. Essa opção requer o SSMS versão 17,5 ou posterior.

Para obter mais informações, consulte [Configurar a autenticação multifator para o SSMS e o Azure ad](sql-database-ssms-mfa-authentication-configure.md).

> [!NOTE]
> Se você for um usuário convidado no banco de dados, também precisará fornecer o nome de domínio do Azure AD para o banco de dados: selecione **opções** > **nome de domínio do AD ou ID do locatário**. Para localizar o nome de domínio no portal do Azure, selecione **Azure Active Directory** > **nomes de domínio personalizados**. No programa C# de exemplo, não é necessário fornecer um nome de domínio.

## <a name="c-code-example"></a>C#exemplo de código

O programa C# de exemplo se baseia no assembly da dll [*Microsoft. IdentityModel. clients. ActiveDirectory*](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory) .

Para instalar esse pacote, no Visual Studio, selecione **projeto** > **gerenciar pacotes NuGet**. Procure e instale **o Microsoft. IdentityModel. clients. ActiveDirectory**.

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

Este é um exemplo da saída C# do teste.

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
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do banco de dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. Para esses cmdlets, consulte [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo AZ e nos módulos AzureRm são substancialmente idênticos.

- [Get-AzSqlServerActiveDirectoryAdministrator](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator)
