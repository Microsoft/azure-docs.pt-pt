---
title: 'Tutorial: Aceder a dados com identidade gerida'
description: Saiba como tornar a conectividade da base de dados mais segura utilizando uma identidade gerida, e também como aplicá-la a outros serviços do Azure.
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/27/2020
ms.custom: mvc, cli-validate
ms.openlocfilehash: 142cd2611e0dcf3227474efadded7bac88a4390a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82207640"
---
# <a name="tutorial-secure-azure-sql-database-connection-from-app-service-using-a-managed-identity"></a>Tutorial: Utilizar uma identidade gerida para proteger a ligação da Base de Dados SQL do Azure a partir do Serviço de Aplicações

O [Serviço de Aplicações](overview.md) oferece um serviço de alojamento na Web altamente dimensionável e com correção automática no Azure. Também oferece uma [identidade gerida](overview-managed-identity.md) para a sua aplicação, que é uma solução chave na mão para proteger o acesso à [Base de Dados SQL do Azure](/azure/sql-database/) e a outros serviços do Azure. As identidades geridas no Serviço de Aplicações retiram a necessidade de ter segredos nas suas aplicações, como credenciais nas cadeias de ligação, o que as torna mais seguras. Neste tutorial, você adicionará identidade gerida à aplicação web da amostra que você construiu em um dos seguintes tutoriais: 

- [Tutorial: Criar uma aplicação ASP.NET no Azure com a Base de Dados SQL](app-service-web-tutorial-dotnet-sqldatabase.md)
- [Tutorial: Construa uma app de base de dados core e SQL ASP.NET no Serviço de Aplicações Azure](app-service-web-tutorial-dotnetcore-sqldb.md)

Quando tiver terminado, a aplicação de exemplo irá ligar-se à Base de Dados SQL em segurança sem ser preciso o nome de utilizador e a palavras-passe.

> [!NOTE]
> Os passos abordados neste tutorial suportam as seguintes versões:
> 
> - .QUADRO LÍQUIDO 4.7.2 e superior
> - .NET Core 2.2 e acima
>

O que vai aprender:

> [!div class="checklist"]
> * Ativar identidades geridas
> * Conceder acesso à Base de Dados SQL à identidade gerida
> * Configure Quadro de Entidade para usar autenticação Azure AD com Base de Dados SQL
> * Ligue-se à Base de Dados SQL do Estúdio Visual utilizando a autenticação Azure AD

> [!NOTE]
>A autenticação Azure AD é _diferente_ da [autenticação integrada](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10)) do Windows no diretório ativo no local (AD DS). A AD DS e a AD Azure utilizam protocolos de autenticação completamente diferentes. Para mais informações, consulte a documentação dos Serviços de [Domínio da AD Azure.](https://docs.microsoft.com/azure/active-directory-domain-services/)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Este artigo continua onde deixou no [Tutorial: Construa uma aplicação ASP.NET em Azure com Base de Dados SQL](app-service-web-tutorial-dotnet-sqldatabase.md) ou [Tutorial: Construa uma aplicação de base de dados core e SQL ASP.NET no Serviço de Aplicações Azure.](app-service-web-tutorial-dotnetcore-sqldb.md) Se ainda não o fez, siga primeiro um dos dois tutoriais. Em alternativa, pode adaptar os passos para a sua própria aplicação .NET com base de dados SQL.

Para desinserir a sua aplicação utilizando a Base de Dados SQL como parte de trás, certifique-se de que permitiu a ligação ao cliente a partir do seu computador. Caso contrário, adicione o IP do cliente seguindo os passos das regras de [firewall IP de nível de servidor manage utilizando o portal Azure](../sql-database/sql-database-firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="grant-database-access-to-azure-ad-user"></a>Conceder acesso à base de dados ao utilizador da AD Azure

Primeiro, ativa a autenticação adigenante da AD Azure na Base de Dados SQL, atribuindo um utilizador de Anúncio SQE como administrador ative directory do servidor de base de dados SQL. Este utilizador é diferente da conta microsoft que usou para se inscrever para a sua subscrição do Azure. Deve ser um utilizador que criou, importou, sincronizou ou convidou para a AD Azure. Para obter mais informações sobre os utilizadores de Anúncios Azure autorizados, consulte [as funcionalidades e limitações da AD Azure na Base de Dados SQL](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations).

Se o seu inquilino Azure AD ainda não tiver um utilizador, crie um seguindo os passos em Add ou elimine os utilizadores usando o [Diretório Ativo Azure](../active-directory/fundamentals/add-users-azure-active-directory.md).

Encontre o ID do objeto do utilizador [`az ad user list`](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-list) DaD Azure utilizando o>de * \<nome principal do utilizador *. O resultado é guardado para uma variável.

```azurecli-interactive
azureaduser=$(az ad user list --filter "userPrincipalName eq '<user-principal-name>'" --query [].objectId --output tsv)
```
> [!TIP]
> Para ver a lista de todos os nomes `az ad user list --query [].userPrincipalName`principais do utilizador em Azure AD, corra .
>

Adicione este utilizador Azure AD como um [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin?view=azure-cli-latest#az-sql-server-ad-admin-create) administrador ative diretório utilizando o comando na Cloud Shell. No comando seguinte, * \<* substitua o nome do servidor>pelo `.database.windows.net` nome do servidor SQL Database (sem o sufixo).

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server-name> --display-name ADMIN --object-id $azureaduser
```

Para obter mais informações sobre a adição de um administrador de Diretório Ativo, consulte Provision um administrador de Diretório Ativo Azure para o seu Servidor de Base de [Dados Azure SQL](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)

## <a name="set-up-visual-studio"></a>Configurar o Visual Studio

### <a name="windows"></a>Windows
O Visual Studio for Windows está integrado com a autenticação Azure AD. Para permitir o desenvolvimento e depuração no Estúdio Visual, adicione o seu utilizador Azure AD no Estúdio Visual, selecionando**definições** de conta de **ficheiro** > sacar do menu e clique em **Adicionar uma conta**.

Para definir o utilizador Azure AD para autenticação do serviço Azure, selecione **Tools** > **Options** do menu e, em seguida, selecione A**Seleção**de Conta de Autenticação > de **Serviço Azure**. Selecione o utilizador Azure AD que adicionou e clique **em OK**.

Está agora pronto para desenvolver e depurar a sua aplicação com a Base de Dados SQL como parte de trás, utilizando a autenticação Azure AD.

### <a name="macos"></a>MacOS

O Visual Studio for Mac não está integrado com a autenticação Azure AD. No entanto, a biblioteca [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) que utilizará mais tarde pode utilizar tokens do Azure CLI. Para permitir o desenvolvimento e depuração no Estúdio Visual, primeiro precisa de [instalar o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) na sua máquina local.

Assim que o Azure CLI estiver instalado na sua máquina local, inscreva-se no Azure CLI com o seguinte comando utilizando o utilizador Azure AD:

```bash
az login --allow-no-subscriptions
```
Está agora pronto para desenvolver e depurar a sua aplicação com a Base de Dados SQL como parte de trás, utilizando a autenticação Azure AD.

## <a name="modify-your-project"></a>Modifique o seu projeto

Os passos que segue para o seu projeto dependem se é um projeto ASP.NET ou um projeto ASP.NET Core.

- [Modificar ASP.NET](#modify-aspnet)
- [Modificar ASP.NET Núcleo](#modify-aspnet-core)

### <a name="modify-aspnet"></a>Modificar ASP.NET

No Estúdio Visual, abra a Consola de Gestor de Pacotes e adicione o pacote NuGet [Microsoft.Azure.Services.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication):

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.4.0
```

Em *Web.config,* trabalhando a partir da parte superior do ficheiro e efetuando as seguintes alterações:

- In, `<configSections>`adicione a seguinte declaração de secção:

    ```xml
    <section name="SqlAuthenticationProviders" type="System.Data.SqlClient.SqlAuthenticationProviderConfigurationSection, System.Data, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
    ```

- abaixo da `</configSections>` etiqueta de fecho, adicione `<SqlAuthenticationProviders>`o seguinte código XML para .

    ```xml
    <SqlAuthenticationProviders>
      <providers>
        <add name="Active Directory Interactive" type="Microsoft.Azure.Services.AppAuthentication.SqlAppAuthenticationProvider, Microsoft.Azure.Services.AppAuthentication" />
      </providers>
    </SqlAuthenticationProviders>
    ```    

- Encontre a cadeia `MyDbConnection` de `connectionString` ligação `"server=tcp:<server-name>.database.windows.net;database=<db-name>;UID=AnyString;Authentication=Active Directory Interactive"`chamada e substitua o seu valor por . Substitua _ \<o nome do servidor>_ e _ \<o nome db>_ pelo nome do servidor e nome da base de dados.

> [!NOTE]
> O SqlAuthenticationProvider que acabou de registar baseia-se na biblioteca appAuthentication que instalou anteriormente. Por padrão, utiliza uma identidade atribuída ao sistema. Para alavancar uma identidade atribuída ao utilizador, terá de fornecer uma configuração adicional. Consulte o suporte de cordas de [ligação](../key-vault/general/service-to-service-authentication.md#connection-string-support) para a biblioteca AppAuthentication.

É tudo o que precisas para ligar à Base de Dados SQL. Ao depurar-se no Estúdio Visual, o seu código utiliza o utilizador Azure AD configurado no [set up Visual Studio](#set-up-visual-studio). Mais tarde, irá configurar o servidor De base de dados SQL para permitir a ligação a partir da identidade gerida da sua aplicação App Service.

Digite `Ctrl+F5` para executar a aplicação novamente. A mesma aplicação CRUD no seu navegador está agora a ligar-se diretamente à Base de Dados Azure SQL, utilizando a autenticação Azure AD. Esta configuração permite-lhe executar migrações de base de dados do Estúdio Visual.

### <a name="modify-aspnet-core"></a>Modificar ASP.NET Núcleo

No Estúdio Visual, abra a Consola de Gestor de Pacotes e adicione o pacote NuGet [Microsoft.Azure.Services.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication):

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.4.0
```

No tutorial de base de dados `MyDbConnection` Core e [SQL ASP.NET,](app-service-web-tutorial-dotnetcore-sqldb.md)a cadeia de ligação não é utilizada porque o ambiente de desenvolvimento local utiliza um ficheiro de base de dados Sqlite, e o ambiente de produção do Azure usa uma cadeia de ligação do App Service. Com a autenticação de Diretório Ativo, pretende que ambos os ambientes utilizem a mesma cadeia de ligação. Em *aplicações.json,* substitua `MyDbConnection` o valor da cadeia de ligação com:

```json
"Server=tcp:<server-name>.database.windows.net,1433;Database=<database-name>;"
```

Em seguida, fornece o contexto da base de dados 'Quadro de Entidades' com o token de acesso para a Base de Dados SQL. Em *Dados\MyDatabaseContext.cs,* adicione o seguinte código dentro dos `MyDatabaseContext (DbContextOptions<MyDatabaseContext> options)` aparelhos encaracolados do construtor vazio:

```csharp
var conn = (Microsoft.Data.SqlClient.SqlConnection)Database.GetDbConnection();
conn.AccessToken = (new Microsoft.Azure.Services.AppAuthentication.AzureServiceTokenProvider()).GetAccessTokenAsync("https://database.windows.net/").Result;
```

> [!NOTE]
> Este código de demonstração é sincronizado para a clareza e simplicidade.

É tudo o que precisas para ligar à Base de Dados SQL. Ao depurar-se no Estúdio Visual, o seu código utiliza o utilizador Azure AD configurado no [set up Visual Studio](#set-up-visual-studio). Mais tarde, irá configurar o servidor De base de dados SQL para permitir a ligação a partir da identidade gerida da sua aplicação App Service. A `AzureServiceTokenProvider` classe cacheo o símbolo na memória e o recupera de Azure AD pouco antes de expirar. Não precisa de nenhum código personalizado para refrescar o símbolo.

> [!TIP]
> Se o utilizador da AD Azure configurado tiver `GetAccessTokenAsync("https://database.windows.net/", tenantid)` acesso a vários inquilinos, ligue com o ID do inquilino pretendido para recuperar o sinal de acesso adequado.

Digite `Ctrl+F5` para executar a aplicação novamente. A mesma aplicação CRUD no seu navegador está agora a ligar-se diretamente à Base de Dados Azure SQL, utilizando a autenticação Azure AD. Esta configuração permite-lhe executar migrações de base de dados do Estúdio Visual.

## <a name="use-managed-identity-connectivity"></a>Utilizar conectividade de identidade gerida

Em seguida, configura a sua aplicação App Service para se ligar à Base de Dados SQL com uma identidade gerida atribuída pelo sistema.

> [!NOTE]
> Embora as instruções nesta secção sejam para uma identidade atribuída ao sistema, uma identidade atribuída ao utilizador pode facilmente ser utilizada. Para fazer isto. precisaria da alteração `az webapp identity assign command` para atribuir a identidade atribuída ao utilizador pretendido. Em seguida, ao criar o utilizador SQL, certifique-se de usar o nome do recurso de identidade atribuído ao utilizador em vez do nome do site.

### <a name="enable-managed-identity-on-app"></a>Ativar identidade gerida na app

Para ativar uma identidade gerida na sua aplicação do Azure, utilize o comando [az webapp identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) no Cloud Shell. No comando seguinte, substitua * \<o nome da aplicação>*.

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

### <a name="grant-permissions-to-managed-identity"></a>Conceder permissões à identidade gerida

> [!NOTE]
> Se quiser, pode adicionar a identidade a um [grupo Azure AD,](../active-directory/fundamentals/active-directory-manage-groups.md)em seguida, conceder acesso à Base de Dados SQL ao grupo Azure AD em vez da identidade. Por exemplo, os seguintes comandos adicionam a identidade gerida do passo anterior a um novo grupo chamado _myAzureSQLDBAccessGroup:_
> 
> ```azurecli-interactive
> groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
> msiobjectid=$(az webapp identity show --resource-group myResourceGroup --name <app-name> --query principalId --output tsv)
> az ad group member add --group $groupid --member-id $msiobjectid
> az ad group member list -g $groupid
> ```
>

No Cloud Shell, utilize o comando SQLCMD para iniciar sessão na Base de Dados SQL. Substitua _ \<o nome do servidor>_ pelo nome do seu servidor de base de dados SQL, _ \<nome db>_ com o nome de base de dados que a sua aplicação utiliza e _ \<>_ de nome de utilizador ad e _ \<aad-password>_ com as credenciais do utilizador do Seu AD Azure.

```azurecli-interactive
sqlcmd -S <server-name>.database.windows.net -d <db-name> -U <aad-user-name> -P "<aad-password>" -G -l 30
```

No pedido SQL para a base de dados que deseja, execute os seguintes comandos para conceder as permissões de que a sua aplicação necessita. Por exemplo, 

```sql
CREATE USER [<identity-name>] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [<identity-name>];
ALTER ROLE db_datawriter ADD MEMBER [<identity-name>];
ALTER ROLE db_ddladmin ADD MEMBER [<identity-name>];
GO
```

nome de identidade>é o nome da identidade gerida em Azure AD. * \<* Se a identidade for atribuída ao sistema, o nome é sempre o mesmo que o nome da sua aplicação App Service. Para conceder permissões a um grupo Azure AD, utilize o nome de exibição do grupo (por exemplo, *myAzureSQLDBAccessGroup*).

Escreva `EXIT` para regressar à linha de comandos do Cloud Shell.

> [!NOTE]
> Os serviços de back-end de identidades geridas também [mantêm uma cache simbólica](overview-managed-identity.md#obtain-tokens-for-azure-resources) que atualiza o símbolo para um recurso-alvo apenas quando expira. Se cometer um erro ao configurar as suas permissões de Base de Dados SQL e tentar modificar as permissões *depois* de tentar obter um sinal com a sua aplicação, não obtém um novo símbolo com as permissões atualizadas até que o token em cache expire.

### <a name="modify-connection-string"></a>Modificar a cadeia de ligação

Lembre-se que as mesmas alterações que fez em *Web.config* ou *appsettings.json* trabalha com a identidade gerida, pelo que a única coisa a fazer é remover a cadeia de ligação existente no App Service, que o Visual Studio criou implementando a sua aplicação pela primeira vez. Utilize o seguinte comando, mas substitua * \<* o nome da aplicação>pelo nome da sua aplicação.

```azurecli-interactive
az webapp config connection-string delete --resource-group myResourceGroup --name <app-name> --setting-names MyDbConnection
```

## <a name="publish-your-changes"></a>Publicar as alterações

Agora, só falta publicar as alterações no Azure.

**Se veio do [Tutorial: Construa uma aplicação ASP.NET em Azure com base de dados SQL,](app-service-web-tutorial-dotnet-sqldatabase.md)** publique as suas alterações no Estúdio Visual. No **Explorador de Soluções**, clique com o botão direito do rato no projeto **DotNetAppSqlDb** e selecione **Publicar**.

![Publicar a partir do Explorador de Soluções](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

Na página de publicação, clique em **Publicar**. 

**Se veio do [Tutorial: Construa uma aplicação de base de dados Core e SQL ASP.NET no Serviço de Aplicações Azure,](app-service-web-tutorial-dotnetcore-sqldb.md)** publique as suas alterações utilizando git, com os seguintes comandos:

```bash
git commit -am "configure managed identity"
git push azure master
```

Quando a página Web nova mostrar a lista de tarefas, significa que a aplicação se está a ligar à base de dados com a identidade gerida.

![Aplicação Azure após código primeira migração](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

Agora, deverá conseguir editar a lista de tarefas como antes.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Passos seguintes

O que aprendeu:

> [!div class="checklist"]
> * Ativar identidades geridas
> * Conceder acesso à Base de Dados SQL à identidade gerida
> * Configure Quadro de Entidade para usar autenticação Azure AD com Base de Dados SQL
> * Ligue-se à Base de Dados SQL do Estúdio Visual utilizando a autenticação Azure AD

Avance para o próximo tutorial para saber como mapear um nome DNS personalizado para a aplicação Web.

> [!div class="nextstepaction"]
> [Mapeie um nome dNS personalizado existente para o Serviço de Aplicações Azure](app-service-web-tutorial-custom-domain.md)
