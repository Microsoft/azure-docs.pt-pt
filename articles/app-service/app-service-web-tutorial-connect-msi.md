---
title: Proteger a ligação de base de dados SQL com a identidade gerida - serviço de aplicações do Azure | Documentos da Microsoft
description: Saiba como tornar a conectividade da base de dados mais segura com uma identidade gerida e como aplicá-la a outros serviços do Azure.
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
ms.date: 06/21/2019
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 31535642526c608ad0ae29e5c0e3c93368e184ad
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67481022"
---
# <a name="tutorial-secure-azure-sql-database-connection-from-app-service-using-a-managed-identity"></a>Tutorial: Ligação de base de dados do Azure SQL segura do serviço de aplicações com uma identidade gerida

O [Serviço de Aplicações](overview.md) oferece um serviço de alojamento na Web altamente dimensionável e com correção automática no Azure. Também oferece uma [identidade gerida](overview-managed-identity.md) para a sua aplicação, que é uma solução chave na mão para proteger o acesso à [Base de Dados SQL do Azure](/azure/sql-database/) e a outros serviços do Azure. As identidades geridas no Serviço de Aplicações retiram a necessidade de ter segredos nas suas aplicações, como credenciais nas cadeias de ligação, o que as torna mais seguras. Neste tutorial, irá adicionar a identidade gerida para a aplicação web do ASP.NET de exemplo que criou na [Tutorial: Criar uma aplicação ASP.NET no Azure com a base de dados SQL](app-service-web-tutorial-dotnet-sqldatabase.md). Quando tiver terminado, a aplicação de exemplo irá ligar-se à Base de Dados SQL em segurança sem ser preciso o nome de utilizador e a palavras-passe.

> [!NOTE]
> Este cenário é atualmente suportado pelo .NET Framework 4.7.2 e superior. O [.NET core 2.2](https://www.microsoft.com/net/download/dotnet-core/2.2) suporta o cenário, mas ainda não está incluído nas imagens predefinidas no Serviço de Aplicações. 
>

O que aprende com o saiba como:

> [!div class="checklist"]
> * Ativar identidades geridas
> * Conceder acesso à Base de Dados SQL à identidade gerida
> * Configurar o Entity Framework para utilizar a autenticação do Azure AD com base de dados SQL
> * Ligar à base de dados SQL a partir do Visual Studio, utilizando a autenticação do Azure AD

> [!NOTE]
>Autenticação do Azure AD é _diferentes_ partir [autenticação integrada Windows](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10)) em locais do Active Directory (AD DS). AD DS e AD do Azure utilizam protocolos de autenticação completamente diferente. Para obter mais informações, consulte [documentação do Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Este artigo continua onde ficou [Tutorial: Criar uma aplicação ASP.NET no Azure com a base de dados SQL](app-service-web-tutorial-dotnet-sqldatabase.md). Se ainda não o fez, siga este tutorial primeiro. Em alternativa, pode adaptar os passos para a sua própria aplicação ASP.NET com a Base de Dados SQL.

Para depurar a sua aplicação utilizar a base de dados SQL como o back-end, certifique-se de que [permitida ligação de cliente do computador](app-service-web-tutorial-dotnet-sqldatabase.md#allow-client-connection-from-your-computer).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="grant-azure-ad-user-access-to-database"></a>Utilizador do AD Azure de concessão de acesso à base de dados

Primeiro ative autenticação do Azure AD para a base de dados SQL ao atribuir um utilizador do Azure AD como o administrador do Active Directory do servidor de base de dados SQL. Este utilizador é diferente da conta Microsoft que utilizou para se inscrever para a sua subscrição do Azure. Tem de ser um utilizador que criou, importados, sincronizado ou convidado para o Azure AD. Para obter mais informações sobre permitido a utilizadores do Azure AD, consulte [funcionalidades do Azure AD e as limitações na base de dados SQL](../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations). 

Localizar o ID de objeto do utilizador do Azure AD com o [ `az ad user list` ](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-list) e substitua  *\<nome de principal de utilizador >* . O resultado é salvo numa variável.

```azurecli-interactive
azureaduser=$(az ad user list --filter "userPrincipalName eq '<user-principal-name>'" --query [].objectId --output tsv)
```
> [!TIP]
> Para ver a lista de todos os nomes de principal de utilizador no Azure AD, execute `az ad user list --query [].userPrincipalName`.
>

Adicionar este utilizador do Azure AD como um administrador do Active Directory usando [ `az sql server ad-admin create` ](/cli/azure/sql/server/ad-admin?view=azure-cli-latest#az-sql-server-ad-admin-create) comando no Cloud Shell. No comando seguinte, substitua  *\<nome do servidor >* .

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server-name> --display-name ADMIN --object-id $azureaduser
```

Para obter mais informações sobre como adicionar um administrador do Active Directory, consulte [aprovisionar um administrador do Azure Active Directory para o servidor de base de dados SQL do Azure](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)

## <a name="set-up-visual-studio"></a>Configurar o Visual Studio

Para ativar o desenvolvimento e depuração no Visual Studio, adicione o utilizador do Azure AD no Visual Studio selecionando **arquivo** > **definições de conta** no menu e clique em **adicionar um conta**.

Para definir o utilizador do Azure AD para autenticação de serviço do Azure, selecione **ferramentas** > **opções** no menu, em seguida, selecione **autenticação do serviço Azure**  >  **Seleção de contas**. Selecione o utilizador do Azure AD que adicionou e clique em **OK**.

Agora, está pronto para desenvolver e depurar a sua aplicação com a base de dados SQL como o back-end, usando autenticação do Azure AD.

## <a name="modify-aspnet-project"></a>Modificar o projeto ASP.NET

No Visual Studio, abra a consola do Gestor de pacotes e adicionar o pacote NuGet [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication):

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.2.0
```

Na *Web. config*, trabalhar a partir da parte superior do ficheiro e efetue as seguintes alterações:

- No `<configSections>`, adicione a seguinte declaração de seção no mesmo:

    ```xml
    <section name="SqlAuthenticationProviders" type="System.Data.SqlClient.SqlAuthenticationProviderConfigurationSection, System.Data, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
    ```

- abaixo de fechamento `</configSections>` Etiquetar, adicione o seguinte código XML para `<SqlAuthenticationProviders>`.

    ```xml
    <SqlAuthenticationProviders>
      <providers>
        <add name="Active Directory Interactive" type="Microsoft.Azure.Services.AppAuthentication.SqlAppAuthenticationProvider, Microsoft.Azure.Services.AppAuthentication" />
      </providers>
    </SqlAuthenticationProviders>
    ```    

- Encontrar a cadeia de ligação denominada `MyDbConnection` e substitua seu `connectionString` valor com `"server=tcp:<server-name>.database.windows.net;database=<db-name>;UID=AnyString;Authentication=Active Directory Interactive"`. Substitua  _\<nome do servidor >_ e  _\<db-name >_ com o nome do servidor e o nome de base de dados.

Tipo de `Ctrl+F5` para executar a aplicação novamente. A mesma aplicação CRUD no seu browser é agora a ligação à base de dados do SQL do Azure diretamente, usando autenticação do Azure AD. Esta configuração permite-lhe executar migrações de base de dados. Mais tarde quando implementar as suas alterações no serviço de aplicações, o mesmo trabalho definições com a identidade da aplicação gerida.

## <a name="use-managed-identity-connectivity"></a>Utilize a conectividade de identidade gerida

Em seguida, configure o seu serviço de aplicações para ligar à base de dados SQL com uma identidade gerida atribuído de sistema.

### <a name="enable-managed-identity-on-app"></a>Ativar a identidade gerida na aplicação

Para ativar uma identidade gerida na sua aplicação do Azure, utilize o comando [az webapp identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) no Cloud Shell. No comando seguinte, substitua  *\<nome da aplicação >* .

```azurecli-interactive
az webapp identity assign --resource-group myResourceGroup --name <app-name>
```

Eis um exemplo da saída:

```json
{
  "additionalProperties": {},
  "principalId": "21dfa71c-9e6f-4d17-9e90-1d28801c9735",
  "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
  "type": "SystemAssigned"
}
```

### <a name="add-managed-identity-to-an-azure-ad-group"></a>Adicionar identidade gerida a um grupo do Azure AD

Para conceder o acesso identidade para a base de dados SQL, tem de adicioná-lo para um [grupo do Azure AD](../active-directory/fundamentals/active-directory-manage-groups.md). No Cloud Shell, adicioná-lo para um novo grupo denominado _myAzureSQLDBAccessGroup_, conforme apresentado no seguinte script:

```azurecli-interactive
groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
msiobjectid=$(az webapp identity show --resource-group myResourceGroup --name <app-name> --query principalId --output tsv)
az ad group member add --group $groupid --member-id $msiobjectid
az ad group member list -g $groupid
```

Se quiser ver a saída JSON completa de cada comando, remova os parâmetros `--query objectId --output tsv`.

### <a name="grant-permissions-to-azure-ad-group"></a>Conceder permissões ao grupo do Azure AD

No Cloud Shell, utilize o comando SQLCMD para iniciar sessão na Base de Dados SQL. Substitua  _\<nome do servidor >_ com o nome de servidor de base de dados SQL  _\<db-name >_ com a base de dados, nomeie a sua aplicação utiliza, e  _\< nome de utilizador aad >_ e  _\<aad-palavra-passe >_ com credenciais de utilizador do Azure AD.

```azurecli-interactive
sqlcmd -S <server-name>.database.windows.net -d <db-name> -U <aad-user-name> -P "<aad-password>" -G -l 30
```

Na linha de comandos SQL para a base de dados, tem de executar os comandos seguintes para adicionar o Azure AD de grupo e concedem as permissões de seu aplicativo. Por exemplo, 

```sql
CREATE USER [myAzureSQLDBAccessGroup] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_datawriter ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_ddladmin ADD MEMBER [myAzureSQLDBAccessGroup];
GO
```

Escreva `EXIT` para regressar à linha de comandos do Cloud Shell.

### <a name="modify-connection-string"></a>Modificar a cadeia de ligação

Lembre-se de que o mesmo as modificações realizadas no `Web.config` funciona com a identidade gerida, para que a única coisa a fazer é remover a cadeia de ligação existente na sua aplicação, o Visual Studio criou implementar a sua aplicação pela primeira vez. Utilize o seguinte comando, mas substitua  *\<nome da aplicação >* com o nome da sua aplicação.

```azurecli-interactive
az webapp config connection-string delete --resource-group myResourceGroup --name <app-name> --setting-names MyDbConnection
```

## <a name="publish-your-changes"></a>Publicar as alterações

Agora, só falta publicar as alterações no Azure.

No **Explorador de Soluções**, clique com o botão direito do rato no projeto **DotNetAppSqlDb** e selecione **Publicar**.

![Publicar a partir do Explorador de Soluções](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

Na página de publicação, clique em **Publicar**. Quando a página Web nova mostrar a lista de tarefas, significa que a aplicação se está a ligar à base de dados com a identidade gerida.

![Aplicação do Azure após a primeira migração de código](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

Agora, deverá conseguir editar a lista de tarefas como antes.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Passos Seguintes

O que aprendeu:

> [!div class="checklist"]
> * Ativar identidades geridas
> * Conceder acesso à Base de Dados SQL à identidade gerida
> * Configurar o Entity Framework para utilizar a autenticação do Azure AD com base de dados SQL
> * Ligar à base de dados SQL a partir do Visual Studio, utilizando a autenticação do Azure AD

Avance para o próximo tutorial para saber como mapear um nome DNS personalizado para a aplicação Web.

> [!div class="nextstepaction"]
> [Mapear um nome DNS existente personalizado para o serviço de aplicações do Azure](app-service-web-tutorial-custom-domain.md)
