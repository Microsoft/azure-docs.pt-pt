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
ms.date: 11/30/2018
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 548cd3de6d2eff9f2077ca66b66d5c60aa84f7e2
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2019
ms.locfileid: "67154210"
---
# <a name="tutorial-secure-azure-sql-database-connection-from-app-service-using-a-managed-identity"></a>Tutorial: Ligação de base de dados do Azure SQL segura do serviço de aplicações com uma identidade gerida

O [Serviço de Aplicações](overview.md) oferece um serviço de alojamento na Web altamente dimensionável e com correção automática no Azure. Também oferece uma [identidade gerida](overview-managed-identity.md) para a sua aplicação, que é uma solução chave na mão para proteger o acesso à [Base de Dados SQL do Azure](/azure/sql-database/) e a outros serviços do Azure. As identidades geridas no Serviço de Aplicações retiram a necessidade de ter segredos nas suas aplicações, como credenciais nas cadeias de ligação, o que as torna mais seguras. Neste tutorial, irá adicionar a identidade gerida para a aplicação web do ASP.NET de exemplo que criou na [Tutorial: Criar uma aplicação ASP.NET no Azure com a base de dados SQL](app-service-web-tutorial-dotnet-sqldatabase.md). Quando tiver terminado, a aplicação de exemplo irá ligar-se à Base de Dados SQL em segurança sem ser preciso o nome de utilizador e a palavras-passe.

> [!NOTE]
> Este cenário é atualmente suportado pelo .NET Framework 4.6 e acima, mas não pelo [.NET Core 2.1](https://www.microsoft.com/net/learn/get-started/windows). O [.NET core 2.2](https://www.microsoft.com/net/download/dotnet-core/2.2) suporta o cenário, mas ainda não está incluído nas imagens predefinidas no Serviço de Aplicações. 
>

O que aprende com o saiba como:

> [!div class="checklist"]
> * Ativar identidades geridas
> * Conceder acesso à Base de Dados SQL à identidade gerida
> * Configurar o código da aplicação para autenticar com a Base de Dados SQL através da autenticação do Azure Active Directory
> * Conceder privilégios mínimos à identidade gerida na Base de Dados SQL

> [!NOTE]
>A autenticação do Azure Active Directory é _diferente_ da [Autenticação integrada do Windows](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10)) no Active Directory (AD DS) no local. O AD DS e o Azure Active Directory utilizam protocolos de autenticação completamente diferentes. Para obter mais informações, consulte [documentação do Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Este artigo continua onde ficou [Tutorial: Criar uma aplicação ASP.NET no Azure com a base de dados SQL](app-service-web-tutorial-dotnet-sqldatabase.md). Se ainda não o fez, siga este tutorial primeiro. Em alternativa, pode adaptar os passos para a sua própria aplicação ASP.NET com a Base de Dados SQL.

<!-- ![app running in App Service](./media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png) -->

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="enable-managed-identities"></a>Ativar identidades geridas

Para ativar uma identidade gerida na sua aplicação do Azure, utilize o comando [az webapp identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) no Cloud Shell. No comando seguinte, substitua *\<nome da aplicação>* .

```azurecli-interactive
az webapp identity assign --resource-group myResourceGroup --name <app name>
```

Eis um exemplo da saída depois de a identidade ser criada no Azure Active Directory:

```json
{
  "additionalProperties": {},
  "principalId": "21dfa71c-9e6f-4d17-9e90-1d28801c9735",
  "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
  "type": "SystemAssigned"
}
```

Vai utilizar o valor de `principalId` no próximo passo. Se quiser ver os detalhes da identidade nova no Azure Active Directory, execute o seguinte comando opcional com o valor de `principalId`:

```azurecli-interactive
az ad sp show --id <principalid>
```

## <a name="grant-database-access-to-identity"></a>Conceder acesso à base de dados à identidade

Em seguida, vai conceder acesso à base de dados à identidade gerida da sua aplicação com o comando [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin?view=azure-cli-latest) no Cloud Shell. No comando seguinte, substitua *\<server_name>* e <principalid_from_last_step>. Escreva um nome de administrador em *\<admin_user>* .

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server_name> --display-name <admin_user> --object-id <principalid_from_last_step>
```

A identidade gerida tem agora acesso ao servidor da Base de Dados SQL do Azure.

> [!IMPORTANT]
> Para simplificar, este passo configura a identidade do Azure AD gerido como o administrador de base de dados SQL. O método tem as seguintes limitações:
>
> - Acesso administrativo da aplicação não seguir as melhores práticas de segurança.
> - Uma vez que a identidade gerida é específica de aplicação, é possível utilizar a mesma identidade gerida para ligar à base de dados SQL, de outra aplicação.
> - A identidade gerida não pode iniciar sessão na base de dados do SQL interativamente, portanto, é impossível conceder acesso a identidades geridas de aplicações adicionais. 
>
> Para melhorar a segurança e para administrar contas do Azure AD na base de dados SQL, siga os passos indicados em [conceder privilégios mínimos à identidade](#grant-minimal-privileges-to-identity).

## <a name="modify-connection-string"></a>Modificar a cadeia de ligação

Modifique a ligação que definiu anteriormente para a aplicação com o comando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) no Cloud Shell. No comando seguinte, substitua *\<app name>* pelo nome da aplicação e *\<server_name>* e *\<db_name>* pelos nomes relativos à Base de Dados SQL.

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app name> --settings MyDbConnection='Server=tcp:<server_name>.database.windows.net,1433;Database=<db_name>;' --connection-string-type SQLAzure
```

## <a name="modify-aspnet-code"></a>Modificar o código ASP.NET

No Visual Studio, abra a consola do Gestor de pacotes e adicionar o pacote NuGet [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication):

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.1.0-preview
```

Abra _Models\MyDatabaseContext.cs_ e adicione as seguintes instruções `using` à parte superior do ficheiro.

```csharp
using System.Data.SqlClient;
using Microsoft.Azure.Services.AppAuthentication;
using System.Web.Configuration;
```

Na classe `MyDatabaseContext`, adicione o construtor seguinte:

```csharp
public MyDatabaseContext(SqlConnection conn) : base(conn, true)
{
    conn.ConnectionString = WebConfigurationManager.ConnectionStrings["MyDbConnection"].ConnectionString;
    // DataSource != LocalDB means app is running in Azure with the SQLDB connection string you configured
    if(conn.DataSource != "(localdb)\\MSSQLLocalDB")
        conn.AccessToken = (new AzureServiceTokenProvider()).GetAccessTokenAsync("https://database.windows.net/").Result;

    Database.SetInitializer<MyDatabaseContext>(null);
}
```

Este construtor configura um objeto SqlConnection personalizado para utilizar um token de acesso para a Base de Dados SQL do Azure a partir do Serviço de Aplicações. Com o token de acesso, a aplicação do Serviço de Aplicações autentica-se com a Base de Dados SQL do Azure com a respetiva identidade gerida. Para obter mais informações, veja [Obtaining tokens for Azure resources](overview-managed-identity.md#obtaining-tokens-for-azure-resources) (Obter tokens para recursos do Azure). A instrução `if` permite-lhe continuar a testar a aplicação localmente com LocalDB.

> [!NOTE]
> `SqlConnection.AccessToken` só é suportado atualmente em .NET Framework 4.6 e superior, bem como no [.NET Core 2.2](https://www.microsoft.com/net/download/dotnet-core/2.2), e não no [.NET Core 2.1](https://www.microsoft.com/net/learn/get-started/windows).
>

Para utilizar este construtor novo, abra `Controllers\TodosController.cs` e localize a linha `private MyDatabaseContext db = new MyDatabaseContext();`. O código existente utiliza o controlador `MyDatabaseContext` predefinido para criar uma base de dados com a cadeia de ligação padrão, que tinha o nome de utilizador e a palavra-passe em texto não encriptado antes de os ter [alterado](#modify-connection-string).

Substitua a linha anterior pelo código abaixo:

```csharp
private MyDatabaseContext db = new MyDatabaseContext(new System.Data.SqlClient.SqlConnection());
```

### <a name="publish-your-changes"></a>Publicar as alterações

Agora, só falta publicar as alterações no Azure.

No **Explorador de Soluções**, clique com o botão direito do rato no projeto **DotNetAppSqlDb** e selecione **Publicar**.

![Publicar a partir do Explorador de Soluções](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

Na página de publicação, clique em **Publicar**. Quando a página Web nova mostrar a lista de tarefas, significa que a aplicação se está a ligar à base de dados com a identidade gerida.

![Aplicação do Azure após a primeira migração de código](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

Agora, deverá conseguir editar a lista de tarefas como antes.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="grant-minimal-privileges-to-identity"></a>Conceder privilégios mínimos à identidade

Durante os passos anteriores, reparou provavelmente que a identidade gerida está ligada ao SQL Server como a administradora do Azure AD. Para conceder privilégios mínimos à sua identidade gerida, tem de iniciar sessão no servidor da Base de Dados SQL do Azure como o administrador do Azure AD e, em seguida, adicionar um grupo do Azure Active Directory que contenha a identidade gerida. 

### <a name="add-managed-identity-to-an-azure-active-directory-group"></a>Adicionar a identidade gerida a um grupo do Azure Active Directory

No Cloud Shell, adicione a identidade gerida da aplicação num grupo do Azure Active Directory novo, chamado _myAzureSQLDBAccessGroup_, mostrado no seguinte script:

```azurecli-interactive
groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
msiobjectid=$(az webapp identity show --resource-group <group_name> --name <app_name> --query principalId --output tsv)
az ad group member add --group $groupid --member-id $msiobjectid
az ad group member list -g $groupid
```

Se quiser ver a saída JSON completa de cada comando, remova os parâmetros `--query objectId --output tsv`.

### <a name="reconfigure-azure-ad-administrator"></a>Reconfigurar o administrador do Azure AD

Anteriormente, atribuiu a identidade gerida como o administrador do Azure AD da sua Base de Dados SQL. Não pode utilizar esta identidade para início de sessão interativo (para adicionar utilizadores da base de dados), pelo que tem de utilizar o seu utilizador do Azure AD real. Para adicionar o utilizador do Azure AD, siga os passos em [Provision an Azure Active Directory administrator for your Azure SQL Database Server](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) (Aprovisionar um administrador do Azure Active Directory para o servidor da Base de Dados SQL do Azure). 

> [!IMPORTANT]
> Uma vez adicionado, não remova este administrador do Azure AD para a base de dados SQL, a menos que pretende desativar o acesso do Azure AD para a base de dados SQL totalmente (de todas as contas do Azure AD).
> 

### <a name="grant-permissions-to-azure-active-directory-group"></a>Conceder permissões ao grupo do Azure Active Directory

No Cloud Shell, utilize o comando SQLCMD para iniciar sessão na Base de Dados SQL. Substitua _\<server\_name>_ pelo seu nome de servidor da Base de Dados SQL, _\<db\_name>_ pelo nome da base de dados que a sua aplicação utiliza e _\<AADuser\_name>_ e _\<AADpassword>_ pelas credenciais do utilizador do Azure AD.

```azurecli-interactive
sqlcmd -S <server_name>.database.windows.net -d <db_name> -U <AADuser_name> -P "<AADpassword>" -G -l 30
```

Na linha de comandos do SQL da base de dados que pretende, execute os seguintes comandos para adicionar o grupo do Azure Active Directory que criou anteriormente e conceda as permissões que a sua aplicação precisa. Por exemplo, 

```sql
CREATE USER [myAzureSQLDBAccessGroup] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_datawriter ADD MEMBER [myAzureSQLDBAccessGroup];
ALTER ROLE db_ddladmin ADD MEMBER [myAzureSQLDBAccessGroup];
GO
```

Escreva `EXIT` para regressar à linha de comandos do Cloud Shell. 

## <a name="next-steps"></a>Passos Seguintes

O que aprendeu:

> [!div class="checklist"]
> * Ativar identidades geridas
> * Conceder acesso à Base de Dados SQL à identidade gerida
> * Configurar o código da aplicação para autenticar com a Base de Dados SQL através da autenticação do Azure Active Directory
> * Conceder privilégios mínimos à identidade gerida na Base de Dados SQL

Avance para o próximo tutorial para saber como mapear um nome DNS personalizado para a aplicação Web.

> [!div class="nextstepaction"]
> [Mapear um nome DNS existente personalizado para o serviço de aplicações do Azure](app-service-web-tutorial-custom-domain.md)
