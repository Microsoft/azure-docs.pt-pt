---
title: Conexão segura do banco de dados SQL com identidade gerenciada-serviço de Azure App | Microsoft Docs
description: Saiba como tornar a conectividade do banco de dados mais segura usando uma identidade gerenciada e também como aplicá-la a outros serviços do Azure.
services: app-service\web
documentationcenter: dotnet
author: cephalin
manager: syntaxc4
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 09/16/2019
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: b39c1596dd16f8ec6235878abdbf37492abd1ea8
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177078"
---
# <a name="tutorial-secure-azure-sql-database-connection-from-app-service-using-a-managed-identity"></a>Tutorial: Proteger a conexão do banco de dados SQL do Azure do serviço de aplicativo usando uma identidade gerenciada

O [Serviço de Aplicações](overview.md) oferece um serviço de alojamento na Web altamente dimensionável e com correção automática no Azure. Também oferece uma [identidade gerida](overview-managed-identity.md) para a sua aplicação, que é uma solução chave na mão para proteger o acesso à [Base de Dados SQL do Azure](/azure/sql-database/) e a outros serviços do Azure. As identidades geridas no Serviço de Aplicações retiram a necessidade de ter segredos nas suas aplicações, como credenciais nas cadeias de ligação, o que as torna mais seguras. Neste tutorial, você adicionará identidade gerenciada ao aplicativo Web de exemplo criado em um dos seguintes tutoriais: 

- [Tutorial: Criar um aplicativo ASP.NET no Azure com o banco de dados SQL @ no__t-0
- [Tutorial: Criar um ASP.NET Core e um aplicativo de banco de dados SQL no Azure App serviço @ no__t-0

Quando tiver terminado, a aplicação de exemplo irá ligar-se à Base de Dados SQL em segurança sem ser preciso o nome de utilizador e a palavras-passe.

> [!NOTE]
> As etapas abordadas neste tutorial dão suporte às seguintes versões:
> 
> - .NET Framework 4.7.2 e acima.
> - .NET Core 2,2 e posterior.
>

O que você aprenderá:

> [!div class="checklist"]
> * Ativar identidades geridas
> * Conceder acesso à Base de Dados SQL à identidade gerida
> * Configurar Entity Framework para usar a autenticação do Azure AD com o banco de dados SQL
> * Conectar-se ao banco de dados SQL do Visual Studio usando a autenticação do Azure AD

> [!NOTE]
>A autenticação do Azure AD é _diferente_ da [autenticação integrada do Windows](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10)) no Active Directory local (AD DS). AD DS e o AD do Azure usam protocolos de autenticação completamente diferentes. Para obter mais informações, consulte [documentação do Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Este artigo continua de onde você parou em [Tutorial: Crie um aplicativo ASP.NET no Azure com o banco de dados SQL @ no__t-0 ou [Tutorial: Crie um ASP.NET Core e um aplicativo de banco de dados SQL no Azure App serviço @ no__t-0. Se você ainda não fez isso, siga um dos dois tutoriais primeiro. Como alternativa, você pode adaptar as etapas para seu próprio aplicativo .NET com o banco de dados SQL.

Para depurar seu aplicativo usando o banco de dados SQL como o back-end, certifique-se de que você tenha permitido a conexão de cliente do seu computador. Caso contrário, adicione o IP do cliente seguindo as etapas em [gerenciar regras de firewall de IP de nível de servidor usando o portal do Azure](../sql-database/sql-database-firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="grant-database-access-to-azure-ad-user"></a>Conceder acesso de banco de dados ao usuário do Azure AD

Primeiro, habilite a autenticação do Azure AD para o banco de dados SQL atribuindo um usuário do Azure AD como o administrador do Active Directory do servidor do banco de dados SQL. Esse usuário é diferente do conta Microsoft que você usou para se inscrever em sua assinatura do Azure. Ele deve ser um usuário que você criou, importou, sincronizou ou convidou para o Azure AD. Para obter mais informações sobre os usuários do Azure AD permitidos, consulte [limitações e recursos do Azure AD no banco de dados SQL](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations).

Se o seu locatário do Azure AD ainda não tiver um usuário, crie um seguindo as etapas em [Adicionar ou excluir usuários usando Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md).

Localize a ID de objeto do usuário do Azure AD usando o [`az ad user list`](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-list) e substitua *\<user-principal-Name >* . O resultado é salvo em uma variável.

```azurecli-interactive
azureaduser=$(az ad user list --filter "userPrincipalName eq '<user-principal-name>'" --query [].objectId --output tsv)
```
> [!TIP]
> Para ver a lista de todos os nomes de entidade de usuário no Azure AD, execute `az ad user list --query [].userPrincipalName`.
>

Adicione este usuário do Azure AD como um administrador de Active Directory usando o comando [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin?view=azure-cli-latest#az-sql-server-ad-admin-create) no Cloud Shell. No comando a seguir, substitua *\<server-name >* pelo nome do servidor do banco de dados SQL (sem o sufixo `.database.windows.net`).

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server-name> --display-name ADMIN --object-id $azureaduser
```

Para obter mais informações sobre como adicionar um administrador de Active Directory, consulte [provisionar um administrador de Azure Active Directory para seu servidor de banco de dados SQL do Azure](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)

## <a name="set-up-visual-studio"></a>Configurar o Visual Studio

### <a name="windows"></a>Windows
O Visual Studio para Windows é integrado à autenticação do Azure AD. Para habilitar o desenvolvimento e a depuração no Visual Studio, adicione o usuário do Azure AD ao Visual Studio selecionando **arquivo** > **configurações de conta** no menu e clique em **Adicionar uma conta**.

Para definir o usuário do Azure AD para a autenticação de serviço do Azure, selecione **ferramentas**@no__t **-1 no** menu e selecione autenticação de **serviço do Azure** > **seleção de conta**. Selecione o usuário do Azure AD que você adicionou e clique em **OK**.

Agora você está pronto para desenvolver e depurar seu aplicativo com o banco de dados SQL como o back-end, usando a autenticação do Azure AD.

### <a name="macos"></a>MacOS

O Visual Studio para Mac não está integrado à autenticação do Azure AD. No entanto, a biblioteca [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) , que você usará posteriormente, poderá usar tokens de CLI do Azure. Para habilitar o desenvolvimento e a depuração no Visual Studio, primeiro você precisa [instalar CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) no computador local.

Quando CLI do Azure estiver instalado no computador local, entre no CLI do Azure com o seguinte comando usando o usuário do Azure AD:

```bash
az login --allow-no-subscriptions
```
Agora você está pronto para desenvolver e depurar seu aplicativo com o banco de dados SQL como o back-end, usando a autenticação do Azure AD.

## <a name="modify-your-project"></a>Modificar seu projeto

As etapas que você seguir para o seu projeto dependem de ser um projeto ASP.NET ou um projeto ASP.NET Core.

- [Modificar ASP.NET](#modify-aspnet)
- [Modificar ASP.NET Core](#modify-aspnet-core)

### <a name="modify-aspnet"></a>Modificar ASP.NET

No Visual Studio, abra o console do Gerenciador de pacotes e adicione o pacote NuGet [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication):

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.3.0
```

Em *Web. config*, trabalhando na parte superior do arquivo e faça as seguintes alterações:

- No `<configSections>`, adicione a seguinte declaração de seção a ela:

    ```xml
    <section name="SqlAuthenticationProviders" type="System.Data.SqlClient.SqlAuthenticationProviderConfigurationSection, System.Data, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
    ```

- abaixo da marca de fechamento `</configSections>`, adicione o código XML a seguir para `<SqlAuthenticationProviders>`.

    ```xml
    <SqlAuthenticationProviders>
      <providers>
        <add name="Active Directory Interactive" type="Microsoft.Azure.Services.AppAuthentication.SqlAppAuthenticationProvider, Microsoft.Azure.Services.AppAuthentication" />
      </providers>
    </SqlAuthenticationProviders>
    ```    

- Localize a cadeia de conexão chamada `MyDbConnection` e substitua seu valor de `connectionString` por `"server=tcp:<server-name>.database.windows.net;database=<db-name>;UID=AnyString;Authentication=Active Directory Interactive"`. Substitua _\<server-name >_ e _\<db-Name >_ pelo nome do servidor e pelo nome do banco de dados.

Isso é tudo o que você precisa para se conectar ao banco de dados SQL. Ao depurar no Visual Studio, seu código usa o usuário do Azure AD que você configurou em [Configurar o Visual Studio](#set-up-visual-studio). Você configurará o servidor do banco de dados SQL posteriormente para permitir a conexão da identidade gerenciada do seu aplicativo do serviço de aplicativo.

Digite `Ctrl+F5` para executar o aplicativo novamente. O mesmo aplicativo CRUD em seu navegador agora está se conectando ao banco de dados SQL do Azure diretamente, usando a autenticação do Azure AD. Essa configuração permite que você execute migrações de banco de dados do Visual Studio.

### <a name="modify-aspnet-core"></a>Modificar ASP.NET Core

No Visual Studio, abra o console do Gerenciador de pacotes e adicione o pacote NuGet [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication):

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.3.0
```

No [tutorial ASP.NET Core e banco de dados SQL](app-service-web-tutorial-dotnetcore-sqldb.md), a cadeia de conexão `MyDbConnection` não é usada porque o ambiente de desenvolvimento local usa um arquivo de banco de dados SQLite e o ambiente de produção do Azure usa uma cadeia de conexão do serviço de aplicativo. Com a autenticação Active Directory, você deseja que ambos os ambientes usem a mesma cadeia de conexão. Em *appSettings. JSON*, substitua o valor da cadeia de conexão `MyDbConnection` por:

```json
"Server=tcp:<server-name>.database.windows.net,1433;Database=<database-name>;"
```

No *Startup.cs*, remova a seção de código que você adicionou antes:

```csharp
// Use SQL Database if in Azure, otherwise, use SQLite
if (Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") == "Production")
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
else
    services.AddDbContext<MyDatabaseContext>(options =>
            options.UseSqlite("Data Source=localdatabase.db"));

// Automatically perform database migration
services.BuildServiceProvider().GetService<MyDatabaseContext>().Database.Migrate();
```

E substitua-o pelo código a seguir:

```csharp
services.AddDbContext<MyDatabaseContext>(options => {
    options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection"));
});
```

Em seguida, você fornece o contexto de banco de dados Entity Framework com o token de acesso para o banco de dados SQL. No *Data\MyDatabaseContext.cs*, adicione o seguinte código dentro das chaves do Construtor `MyDatabaseContext (DbContextOptions<MyDatabaseContext> options)` vazio:

```csharp
var conn = (System.Data.SqlClient.SqlConnection)Database.GetDbConnection();
conn.AccessToken = (new Microsoft.Azure.Services.AppAuthentication.AzureServiceTokenProvider()).GetAccessTokenAsync("https://database.windows.net/").Result;
```

> [!TIP]
> Este código de demonstração é síncrono para fins de clareza. Para obter mais informações, consulte [guia assíncrono para construtores](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#constructors).

Isso é tudo o que você precisa para se conectar ao banco de dados SQL. Ao depurar no Visual Studio, seu código usa o usuário do Azure AD que você configurou em [Configurar o Visual Studio](#set-up-visual-studio). Você configurará o servidor do banco de dados SQL posteriormente para permitir a conexão da identidade gerenciada do seu aplicativo do serviço de aplicativo. A classe `AzureServiceTokenProvider` armazena em cache o token na memória e o recupera do Azure AD logo antes da expiração. Você não precisa de nenhum código personalizado para atualizar o token.

> [!TIP]
> Se o usuário do Azure AD que você configurou tiver acesso a vários locatários, chame `GetAccessTokenAsync("https://database.windows.net/", tenantid)` com a ID de locatário desejada para recuperar o token de acesso apropriado.

Digite `Ctrl+F5` para executar o aplicativo novamente. O mesmo aplicativo CRUD em seu navegador agora está se conectando ao banco de dados SQL do Azure diretamente, usando a autenticação do Azure AD. Essa configuração permite que você execute migrações de banco de dados do Visual Studio.

## <a name="use-managed-identity-connectivity"></a>Usar conectividade de identidade gerenciada

Em seguida, configure seu aplicativo do serviço de aplicativo para se conectar ao banco de dados SQL com uma identidade gerenciada atribuída pelo sistema.

### <a name="enable-managed-identity-on-app"></a>Habilitar identidade gerenciada no aplicativo

Para ativar uma identidade gerida na sua aplicação do Azure, utilize o comando [az webapp identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) no Cloud Shell. No comando a seguir, substitua *\<app-name >* .

```azurecli-interactive
az webapp identity assign --resource-group myResourceGroup --name <app-name>
```

Veja um exemplo da saída:

```json
{
  "additionalProperties": {},
  "principalId": "21dfa71c-9e6f-4d17-9e90-1d28801c9735",
  "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
  "type": "SystemAssigned"
}
```

### <a name="add-managed-identity-to-an-azure-ad-group"></a>Adicionar identidade gerenciada a um grupo do Azure AD

Para conceder esse acesso de identidade ao banco de dados SQL, você precisa adicioná-lo a um [grupo do Azure ad](../active-directory/fundamentals/active-directory-manage-groups.md). No Cloud Shell, adicione-o a um novo grupo chamado _myAzureSQLDBAccessGroup_, mostrado no script a seguir:

```azurecli-interactive
groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
msiobjectid=$(az webapp identity show --resource-group myResourceGroup --name <app-name> --query principalId --output tsv)
az ad group member add --group $groupid --member-id $msiobjectid
az ad group member list -g $groupid
```

Se quiser ver a saída JSON completa de cada comando, remova os parâmetros `--query objectId --output tsv`.

### <a name="grant-permissions-to-azure-ad-group"></a>Conceder permissões ao grupo do Azure AD

No Cloud Shell, utilize o comando SQLCMD para iniciar sessão na Base de Dados SQL. Substitua _\<server-name >_ com o nome do servidor do banco de dados SQL, _\<db-Name >_ com o nome do banco de dados usado por seu aplicativo e _\<aad-user-name >_ e _\<aad-password >_ com o seu usuário do Azure AD fornecidas.

```azurecli-interactive
sqlcmd -S <server-name>.database.windows.net -d <db-name> -U <aad-user-name> -P "<aad-password>" -G -l 30
```

No prompt SQL do banco de dados desejado, execute os comandos a seguir para adicionar o grupo do Azure AD e conceder as permissões de que seu aplicativo precisa. Por exemplo, 

```sql
CREATE USER [myAzureSQLDBAccessGroup] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_datawriter ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_ddladmin ADD MEMBER [myAzureSQLDBAccessGroup];
GO
```

Escreva `EXIT` para regressar à linha de comandos do Cloud Shell.

### <a name="modify-connection-string"></a>Modificar a cadeia de ligação

Lembre-se de que as mesmas alterações feitas em *Web. config* ou *appSettings. JSON* funcionam com a identidade gerenciada, portanto, a única coisa a fazer é remover a cadeia de conexão existente no serviço de aplicativo, que o Visual Studio criou em primeiro lugar o aplicativo. momento. Use o comando a seguir, mas substitua *\<app-name >* pelo nome do seu aplicativo.

```azurecli-interactive
az webapp config connection-string delete --resource-group myResourceGroup --name <app-name> --setting-names MyDbConnection
```

## <a name="publish-your-changes"></a>Publicar as alterações

Agora, só falta publicar as alterações no Azure.

**If que você veio de [Tutorial: Crie um aplicativo ASP.NET no Azure com o banco de dados SQL @ no__t-0 @ no__t-1, Publique suas alterações no Visual Studio. No **Explorador de Soluções**, clique com o botão direito do rato no projeto **DotNetAppSqlDb** e selecione **Publicar**.

![Publicar a partir do Explorador de Soluções](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

Na página de publicação, clique em **Publicar**. 

**If que você veio de [Tutorial: Crie um ASP.NET Core e um aplicativo de banco de dados SQL no Azure App serviço @ no__t-0 @ no__t-1, Publique suas alterações usando o Git, com os seguintes comandos:

```bash
git commit -am "configure managed identity"
git push azure master
```

Quando a página Web nova mostrar a lista de tarefas, significa que a aplicação se está a ligar à base de dados com a identidade gerida.

![Aplicativo do Azure após Code First migração](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

Agora, deverá conseguir editar a lista de tarefas como antes.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Passos seguintes

O que aprendeu:

> [!div class="checklist"]
> * Ativar identidades geridas
> * Conceder acesso à Base de Dados SQL à identidade gerida
> * Configurar Entity Framework para usar a autenticação do Azure AD com o banco de dados SQL
> * Conectar-se ao banco de dados SQL do Visual Studio usando a autenticação do Azure AD

Avance para o próximo tutorial para saber como mapear um nome DNS personalizado para a aplicação Web.

> [!div class="nextstepaction"]
> [Mapear um nome DNS personalizado existente para Azure App serviço](app-service-web-tutorial-custom-domain.md)
