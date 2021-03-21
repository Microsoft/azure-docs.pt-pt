---
title: 'Tutorial: Dados de acesso com identidade gerida'
description: Saiba como tornar a conectividade da base de dados mais segura utilizando uma identidade gerida e também como aplicá-la a outros serviços Azure.
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/27/2020
ms.custom: devx-track-csharp, mvc, cli-validate, devx-track-azurecli
ms.openlocfilehash: 2c19ee2b8e7ec3c695b2c76c46402c118c559b40
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98736242"
---
# <a name="tutorial-secure-azure-sql-database-connection-from-app-service-using-a-managed-identity"></a>Tutorial: Utilizar uma identidade gerida para proteger a ligação da Base de Dados SQL do Azure a partir do Serviço de Aplicações

O [Serviço de Aplicações](overview.md) oferece um serviço de alojamento na Web altamente dimensionável e com correção automática no Azure. Também oferece uma [identidade gerida](overview-managed-identity.md) para a sua aplicação, que é uma solução chave na mão para proteger o acesso à [Base de Dados SQL do Azure](/azure/sql-database/) e a outros serviços do Azure. As identidades geridas no Serviço de Aplicações retiram a necessidade de ter segredos nas suas aplicações, como credenciais nas cadeias de ligação, o que as torna mais seguras. Neste tutorial, irá adicionar identidade gerida à aplicação web de amostra que construiu num dos seguintes tutoriais: 

- [Tutorial: Construir uma app ASP.NET em Azure com Base de Dados Azure SQL](app-service-web-tutorial-dotnet-sqldatabase.md)
- [Tutorial: Construir uma aplicação ASP.NET de base de dados core e Azure SQL em Azure App Service](tutorial-dotnetcore-sqldb-app.md)

Quando tiver terminado, a aplicação de exemplo irá ligar-se à Base de Dados SQL em segurança sem ser preciso o nome de utilizador e a palavras-passe.

> [!NOTE]
> Os passos abrangidos por este tutorial suportam as seguintes versões:
> 
> - .NET Quadro 4.7.2 e superior
> - .NET Core 2.2 e superior
>

O que vai aprender:

> [!div class="checklist"]
> * Ativar identidades geridas
> * Conceder acesso à Base de Dados SQL à identidade gerida
> * Configure o Quadro de Entidades para utilizar a autenticação AZure AD com base de dados SQL
> * Ligue à base de dados SQL do Estúdio Visual utilizando a autenticação AZure AD

> [!NOTE]
>A autenticação AZure AD é _diferente_ da [autenticação integrada](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10)) do Windows no Ative Directy (AD DS). AD DS e Azure AD utilizam protocolos de autenticação completamente diferentes. Para mais informações, consulte [a documentação dos Serviços de Domínio AZure AD](../active-directory-domain-services/index.yml).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Este artigo continua onde paraste em [Tutorial: Construa uma aplicação ASP.NET em Azure com base de dados SQL](app-service-web-tutorial-dotnet-sqldatabase.md) ou [Tutorial: Construa uma aplicação core e SQL Database ASP.NET no Azure App Service](tutorial-dotnetcore-sqldb-app.md). Se ainda não o fez, siga primeiro um dos dois tutoriais. Em alternativa, pode adaptar os passos para a sua própria aplicação .NET com base de dados SQL.

Para depurar a sua aplicação utilizando a BASE de Dados SQL como parte traseira, certifique-se de que permitiu a ligação do cliente a partir do seu computador. Caso contrário, adicione o IP do cliente seguindo os passos nas [regras de firewall IP ao nível do servidor utilizando o portal Azure](../azure-sql/database/firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules).

Prepare o seu ambiente para o Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="grant-database-access-to-azure-ad-user"></a>Conceder acesso à base de dados ao utilizador Azure AD

Primeiro, ativar a autenticação Azure AD à Base de Dados SQL, atribuindo um utilizador AZure AD como administrador ativo do servidor. Este utilizador é diferente da conta microsoft que usou para se inscrever para a sua subscrição Azure. Deve ser um utilizador que criou, importou, sincronizou ou convidou para a Azure AD. Para obter mais informações sobre os utilizadores AD autorizados, consulte [as funcionalidades e limitações da AD Azure na Base de Dados SQL](../azure-sql/database/authentication-aad-overview.md#azure-ad-features-and-limitations).

Se o seu inquilino AZURE AD ainda não tiver um utilizador, crie um seguindo os passos no [Add ou elimine os utilizadores usando o Azure Ative Directory](../active-directory/fundamentals/add-users-azure-active-directory.md).

Encontre o ID do objeto do utilizador Azure AD utilizando o [`az ad user list`](/cli/azure/ad/user#az-ad-user-list) e substitua *\<user-principal-name>* . O resultado é guardado para uma variável.

```azurecli-interactive
azureaduser=$(az ad user list --filter "userPrincipalName eq '<user-principal-name>'" --query [].objectId --output tsv)
```
> [!TIP]
> Para ver a lista de todos os nomes principais do utilizador em Azure AD, corra `az ad user list --query [].userPrincipalName` .
>

Adicione este utilizador Azure AD como administrador do Ative Directory utilizando [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-create) o comando na Cloud Shell. No comando seguinte, *\<server-name>* substitua-o pelo nome do servidor (sem o `.database.windows.net` sufixo).

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server-name> --display-name ADMIN --object-id $azureaduser
```

Para obter mais informações sobre a adição de um administrador ative directory, consulte [Provision a Azure Ative Directory administrador para o seu servidor](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)

## <a name="set-up-visual-studio"></a>Configurar o Visual Studio

### <a name="windows-client"></a>Cliente Windows
O Visual Studio for Windows está integrado com a autenticação Azure AD. Para permitir o desenvolvimento e depuração no Estúdio Visual, adicione o seu utilizador AD AZure no Estúdio Visual selecionando  >  **Definições** de Conta de Ficheiros a partir do menu e clique em **Adicionar uma conta**.

Para definir o utilizador Azure AD para a autenticação do serviço Azure, selecione **Opções** de Ferramentas  >   no menu e, em seguida, selecione **Azure Service Authentication** Account  >  **Selection**. Selecione o utilizador AD Azure que adicionou e clique em **OK**.

Está agora pronto para desenvolver e depurar a sua aplicação com a Base de Dados SQL como a parte de trás, utilizando a autenticação AD AZure.

### <a name="macos-client"></a>Cliente macOS

O Visual Studio for Mac não está integrado com a autenticação Azure AD. No entanto, a biblioteca [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) que utilizará mais tarde pode utilizar fichas da Azure CLI. Para ativar o desenvolvimento e depuração no Visual Studio, primeiro precisa [instalar o Azure CLI](/cli/azure/install-azure-cli) na sua máquina local.

Assim que o Azure CLI estiver instalado na sua máquina local, inscreva-se no Azure CLI com o seguinte comando utilizando o seu utilizador Azure AD:

```azurecli
az login --allow-no-subscriptions
```
Está agora pronto para desenvolver e depurar a sua aplicação com a Base de Dados SQL como a parte de trás, utilizando a autenticação AD AZure.

## <a name="modify-your-project"></a>Modifique o seu projeto

Os passos que segue para o seu projeto dependem se é um projeto ASP.NET ou um projeto ASP.NET Core.

- [Modificar ASP.NET](#modify-aspnet)
- [Modificar ASP.NET Núcleo](#modify-aspnet-core)

### <a name="modify-aspnet"></a>Modificar ASP.NET

No Visual Studio, abra a consola de gestor de pacotes e adicione o pacote NuGet [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication):

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.4.0
```

Em *Web.config,* trabalhar a partir do topo do ficheiro e fazer as seguintes alterações:

- Em `<configSections>` , adicione a seguinte declaração da secção:

    ```xml
    <section name="SqlAuthenticationProviders" type="System.Data.SqlClient.SqlAuthenticationProviderConfigurationSection, System.Data, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
    ```

- abaixo da etiqueta de `</configSections>` fecho, adicione o seguinte código XML para `<SqlAuthenticationProviders>` .

    ```xml
    <SqlAuthenticationProviders>
      <providers>
        <add name="Active Directory Interactive" type="Microsoft.Azure.Services.AppAuthentication.SqlAppAuthenticationProvider, Microsoft.Azure.Services.AppAuthentication" />
      </providers>
    </SqlAuthenticationProviders>
    ```    

- Encontre a cadeia de ligação chamada `MyDbConnection` e substitua o seu `connectionString` valor por `"server=tcp:<server-name>.database.windows.net;database=<db-name>;UID=AnyString;Authentication=Active Directory Interactive"` . Substitua _\<server-name>_ e pelo nome do seu servidor e nome da base de _\<db-name>_ dados.

> [!NOTE]
> O SqlAuthenticationProvider que acabou de registar-se baseia-se no topo da biblioteca appAuthentication que instalou anteriormente. Por padrão, utiliza uma identidade atribuída ao sistema. Para alavancar uma identidade atribuída ao utilizador, terá de fornecer uma configuração adicional. Consulte o [suporte de cadeia de ligação](/dotnet/api/overview/azure/service-to-service-authentication#connection-string-support) para a biblioteca appAuthentication.

É tudo o que precisas de ligar à Base de Dados SQL. Ao depurar no Visual Studio, o seu código utiliza o utilizador AZure AD que configurado no [set up Visual Studio](#set-up-visual-studio). Mais tarde, irá configurar a SQL Database para permitir a ligação a partir da identidade gerida da sua aplicação de Serviço de Aplicações.

Escreva `Ctrl+F5` para executar a aplicação novamente. A mesma aplicação CRUD no seu navegador está agora conectada diretamente à Base de Dados Azure SQL, utilizando a autenticação AZure AD. Esta configuração permite executar migrações de base de dados a partir do Visual Studio.

### <a name="modify-aspnet-core"></a>Modificar ASP.NET Núcleo

No Visual Studio, abra a consola de gestor de pacotes e adicione o pacote NuGet [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication):

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.4.0
```

No [tutorial ASP.NET Core e SQL Database,](tutorial-dotnetcore-sqldb-app.md)a cadeia de `MyDbConnection` ligação não é utilizada porque o ambiente de desenvolvimento local utiliza um ficheiro de base de dados Sqlite, e o ambiente de produção Azure usa uma cadeia de conexão do Serviço de Aplicações. Com a autenticação ative directy, pretende que ambos os ambientes utilizem a mesma cadeia de ligação. Em *appsettings.js,* substitua o valor da cadeia de `MyDbConnection` ligação com:

```json
"Server=tcp:<server-name>.database.windows.net,1433;Database=<database-name>;"
```

Em seguida, você fornece o contexto da base de dados Entity Framework com o token de acesso para a Base de Dados SQL. Em *Dados\MyDatabaseContext.cs*, adicione o seguinte código dentro dos aparelhos encaracolados do `MyDatabaseContext (DbContextOptions<MyDatabaseContext> options)` construtor vazio:

```csharp
var connection = (SqlConnection)Database.GetDbConnection();
connection.AccessToken = (new Microsoft.Azure.Services.AppAuthentication.AzureServiceTokenProvider()).GetAccessTokenAsync("https://database.windows.net/").Result;
```

> [!NOTE]
> Este código de demonstração é sincronizado para a clareza e simplicidade.

É tudo o que precisas de ligar à Base de Dados SQL. Ao depurar no Visual Studio, o seu código utiliza o utilizador AZure AD que configurado no [set up Visual Studio](#set-up-visual-studio). Mais tarde, irá configurar a SQL Database para permitir a ligação a partir da identidade gerida da sua aplicação de Serviço de Aplicações. A `AzureServiceTokenProvider` classe cache o símbolo na memória e o recupera do Azure AD pouco antes de expirar. Não precisa de nenhum código personalizado para refrescar o símbolo.

> [!TIP]
> Se o utilizador Azure AD que configuraste tiver acesso a vários inquilinos, ligue `GetAccessTokenAsync("https://database.windows.net/", tenantid)` para o iD do inquilino desejado para recuperar o sinal de acesso adequado.

Escreva `Ctrl+F5` para executar a aplicação novamente. A mesma aplicação CRUD no seu navegador está agora conectada diretamente à Base de Dados Azure SQL, utilizando a autenticação AZure AD. Esta configuração permite executar migrações de base de dados a partir do Visual Studio.

## <a name="use-managed-identity-connectivity"></a>Utilizar conectividade de identidade gerida

Em seguida, configura a sua aplicação de Serviço de Aplicações para ligar à BASE de Dados SQL com uma identidade gerida atribuída ao sistema.

> [!NOTE]
> Embora as instruções nesta secção sejam para uma identidade atribuída ao sistema, uma identidade atribuída ao utilizador pode igualmente ser utilizada. Para fazer isto. necessitaria da alteração `az webapp identity assign command` para atribuir a identidade pretendida pelo utilizador. Em seguida, ao criar o utilizador SQL, certifique-se de que utiliza o nome do recurso de identidade atribuído pelo utilizador em vez do nome do site.

### <a name="enable-managed-identity-on-app"></a>Ativar a identidade gerida na app

Para ativar uma identidade gerida na sua aplicação do Azure, utilize o comando [az webapp identity assign](/cli/azure/webapp/identity#az-webapp-identity-assign) no Cloud Shell. No comando seguinte, *\<app-name>* substitua.

```azurecli-interactive
az webapp identity assign --resource-group myResourceGroup --name <app-name>
```

Aqui está um exemplo da saída:

<pre>
{
  "additionalProperties": {},
  "principalId": "21dfa71c-9e6f-4d17-9e90-1d28801c9735",
  "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
  "type": "SystemAssigned"
}
</pre>

### <a name="grant-permissions-to-managed-identity"></a>Conceder permissões para identidade gerida

> [!NOTE]
> Se quiser, pode adicionar a identidade a um [grupo AZure AD,](../active-directory/fundamentals/active-directory-manage-groups.md)em seguida, conceder acesso à Base de Dados SQL ao grupo AZure AD em vez da identidade. Por exemplo, os seguintes comandos adicionam a identidade gerida do passo anterior a um novo grupo chamado _myAzureSQLDBAccessGroup:_
> 
> ```azurecli-interactive
> groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
> msiobjectid=$(az webapp identity show --resource-group myResourceGroup --name <app-name> --query principalId --output tsv)
> az ad group member add --group $groupid --member-id $msiobjectid
> az ad group member list -g $groupid
> ```
>

No Cloud Shell, utilize o comando SQLCMD para iniciar sessão na Base de Dados SQL. Substitua _\<server-name>_ pelo nome do seu servidor, pelo nome da base de _\<db-name>_ dados que a sua aplicação utiliza e _\<aad-user-name>_ pelas _\<aad-password>_ credenciais do utilizador AD do Azure.

```bash
sqlcmd -S <server-name>.database.windows.net -d <db-name> -U <aad-user-name> -P "<aad-password>" -G -l 30
```

Na solicitação SQL para a base de dados que pretende, execute os seguintes comandos para conceder as permissões de que a sua aplicação necessita. Por exemplo, 

```sql
CREATE USER [<identity-name>] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [<identity-name>];
ALTER ROLE db_datawriter ADD MEMBER [<identity-name>];
ALTER ROLE db_ddladmin ADD MEMBER [<identity-name>];
GO
```

*\<identity-name>* é o nome da identidade gerida em Azure AD. Se a identidade for atribuída ao sistema, o nome é sempre o mesmo que o nome da sua aplicação App Service. Para conceder permissões a um grupo AD Azure, utilize o nome de exibição do grupo (por exemplo, *myAzureSQLDBAccesssGroup).*

Escreva `EXIT` para regressar à linha de comandos do Cloud Shell.

> [!NOTE]
> Os [serviços](overview-managed-identity.md#obtain-tokens-for-azure-resources) back-end de identidades geridas também mantém uma cache simbólica que atualiza o símbolo de um recurso-alvo apenas quando expira. Se cometer um erro ao configurar as permissões da Base de Dados SQL e tentar modificar as permissões *depois de* tentar obter um token com a sua app, não obtém realmente um novo token com as permissões atualizadas até que o token em cache expire.

> [!NOTE]
> O AAD não é suportado para o SqL Server on-prem, e isto inclui MSIs. 

### <a name="modify-connection-string"></a>Modificar a cadeia de ligação

Lembre-se que as mesmas alterações que fez em *Web.config* ou *appsettings.jsem* obras com a identidade gerida, por isso a única coisa a fazer é remover a cadeia de conexão existente no Serviço de Aplicações, que o Visual Studio criou ao implementar a sua app pela primeira vez. Utilize o seguinte comando, mas *\<app-name>* substitua-o pelo nome da sua aplicação.

```azurecli-interactive
az webapp config connection-string delete --resource-group myResourceGroup --name <app-name> --setting-names MyDbConnection
```

## <a name="publish-your-changes"></a>Publicar as alterações

Agora, só falta publicar as alterações no Azure.

**Se veio do [Tutorial: Construa uma aplicação ASP.NET em Azure com base de dados SQL,](app-service-web-tutorial-dotnet-sqldatabase.md)** publique as suas alterações no Visual Studio. No **Explorador de Soluções**, clique com o botão direito do rato no projeto **DotNetAppSqlDb** e selecione **Publicar**.

![Publicar a partir do Explorador de Soluções](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

Na página de publicação, clique em **Publicar**. 

**Se veio do [Tutorial: Construa uma aplicação core e SQL Database ASP.NET no Azure App Service,](tutorial-dotnetcore-sqldb-app.md)** publique as suas alterações utilizando o Git, com os seguintes comandos:

```bash
git commit -am "configure managed identity"
git push azure main
```

Quando a página Web nova mostrar a lista de tarefas, significa que a aplicação se está a ligar à base de dados com a identidade gerida.

![App Azure após Código Primeira Migração](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

Agora, deverá conseguir editar a lista de tarefas como antes.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Passos seguintes

O que aprendeu:

> [!div class="checklist"]
> * Ativar identidades geridas
> * Conceder acesso à Base de Dados SQL à identidade gerida
> * Configure o Quadro de Entidades para utilizar a autenticação AZure AD com base de dados SQL
> * Ligue à base de dados SQL do Estúdio Visual utilizando a autenticação AZure AD

Avance para o próximo tutorial para saber como mapear um nome DNS personalizado para a aplicação Web.

> [!div class="nextstepaction"]
> [Mapeie um nome DNS personalizado existente para o Azure App Service](app-service-web-tutorial-custom-domain.md)