---
title: Tutorial - Web app acede ao Microsoft Graph como a app| Rio Azure
description: Neste tutorial, aprende-se a aceder aos dados no Microsoft Graph utilizando identidades geridas.
services: microsoft-graph, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 01/28/2021
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1
ms.openlocfilehash: 06837ab0f4685787f8d2615e81d0405fdb8ec711
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99062564"
---
# <a name="tutorial-access-microsoft-graph-from-a-secured-app-as-the-app"></a>Tutorial: Aceda ao Microsoft Graph a partir de uma aplicação segura como a aplicação

Saiba como aceder ao Microsoft Graph a partir de uma aplicação web em execução no Azure App Service.

:::image type="content" alt-text="Diagrama que mostra o acesso ao Microsoft Graph." source="./media/scenario-secure-app-access-microsoft-graph/web-app-access-graph.svg" border="false":::

Pretende ligar para o Microsoft Graph para a aplicação web. Uma forma segura de dar acesso à sua aplicação web aos dados é utilizar uma [identidade gerida atribuída pelo sistema.](../active-directory/managed-identities-azure-resources/overview.md) Uma identidade gerida a partir do Azure Ative Directory permite ao Serviço de Aplicações aceder a recursos através do controlo de acesso baseado em funções (RBAC), sem exigir credenciais de aplicações. Depois de atribuir uma identidade gerida à sua aplicação web, a Azure cuida da criação e distribuição de um certificado. Não tem que se preocupar em gerir segredos ou credenciais de aplicativos.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Crie uma identidade gerida atribuída pelo sistema numa aplicação web.
> * Adicione permissões API do Microsoft Graph a uma identidade gerida.
> * Ligue para o Microsoft Graph a partir de uma aplicação web utilizando identidades geridas.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Uma aplicação web em execução no Azure App Service que tem o [módulo de autenticação/autorização do Serviço de Aplicações ativado.](scenario-secure-app-authentication-app-service.md)

## <a name="enable-managed-identity-on-app"></a>Ativar a identidade gerida na app

Se criar e publicar a sua aplicação web através do Visual Studio, a identidade gerida foi ativada na sua aplicação para si. No seu serviço de aplicações, selecione **Identidade** no painel esquerdo e, em seguida, selecione **Sistema atribuído**. Verifique se **o estado** está definido para **On**. Caso contrário, **selecione Save** e, em seguida, selecione **Sim** para ativar a identidade gerida atribuída pelo sistema. Quando a identidade gerida está ativada, o estado é definido para **On** e o ID do objeto está disponível.

Tome nota do valor de ID do **objeto,** que você precisará no próximo passo.

:::image type="content" alt-text="Screenshot que mostra a identidade atribuída ao sistema." source="./media/scenario-secure-app-access-microsoft-graph/create-system-assigned-identity.png":::

## <a name="grant-access-to-microsoft-graph"></a>Conceder acesso ao Microsoft Graph

Ao aceder ao Gráfico do Microsoft, a identidade gerida necessita de ter permissões adequadas para a operação que pretende realizar. Atualmente, não existe opção de atribuir tais permissões através do portal Azure. O seguinte script adicionará as permissões solicitadas da Microsoft Graph API ao objeto principal do serviço de identidade gerido.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
# Install the module. (You need admin on the machine.)
# Install-Module AzureAD.

# Your tenant ID (in the Azure portal, under Azure Active Directory > Overview).
$TenantID="<tenant-id>"
$resourceGroup = "securewebappresourcegroup"
$webAppName="SecureWebApp-20201102125811"

# Get the ID of the managed identity for the web app.
$spID = (Get-AzWebApp -ResourceGroupName $resourceGroup -Name $webAppName).identity.principalid

# Check the Microsoft Graph documentation for the permission you need for the operation.
$PermissionName = "User.Read.All"

Connect-AzureAD -TenantId $TenantID

# Get the service principal for Microsoft Graph.
# First result should be AppId 00000003-0000-0000-c000-000000000000
$GraphServicePrincipal = Get-AzureADServicePrincipal -SearchString "Microsoft Graph" | Select-Object -first 1

# Assign permissions to the managed identity service principal.
$AppRole = $GraphServicePrincipal.AppRoles | `
Where-Object {$_.Value -eq $PermissionName -and $_.AllowedMemberTypes -contains "Application"}

New-AzureAdServiceAppRoleAssignment -ObjectId $spID -PrincipalId $spID `
-ResourceId $GraphServicePrincipal.ObjectId -Id $AppRole.Id
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az login

webAppName="SecureWebApp-20201106120003"

spId=$(az resource list -n $webAppName --query [*].identity.principalId --out tsv)

graphResourceId=$(az ad sp list --display-name "Microsoft Graph" --query [0].objectId --out tsv)

appRoleId=$(az ad sp list --display-name "Microsoft Graph" --query "[0].appRoles[?value=='User.Read.All' && contains(allowedMemberTypes, 'Application')].id" --output tsv)

uri=https://graph.microsoft.com/v1.0/servicePrincipals/$spId/appRoleAssignments

body="{'principalId':'$spId','resourceId':'$graphResourceId','appRoleId':'$appRoleId'}"

az rest --method post --uri $uri --body $body --headers "Content-Type=application/json"
```

---

Após a execução do script, pode verificar no [portal Azure](https://portal.azure.com) que as permissões solicitadas da API são atribuídas à identidade gerida.

Vá ao **Azure Ative Directory** e, em seguida, selecione **aplicações Enterprise**. Este painel exibe todos os diretores de serviço do seu inquilino. Em **Todas as Aplicações,** selecione o principal de serviço para a identidade gerida. 

Se estiver a seguir este tutorial, existem dois principais de serviço com o mesmo nome de ecrã (SecureWebApp2020094113531, por exemplo). O diretor de serviço que tem um **URL de página inicial** representa a aplicação web no seu inquilino. O principal de serviço sem o **URL homepage** representa a identidade gerida atribuída pelo sistema para a sua aplicação web. O valor **de ID do objeto** para a identidade gerida corresponde ao ID do objeto da identidade gerida que criou anteriormente.

Selecione o principal de serviço para a identidade gerida.

:::image type="content" alt-text="Screenshot que mostra a opção Todas as aplicações." source="./media/scenario-secure-app-access-microsoft-graph/enterprise-apps-all-applications.png":::

Em **Overview**, selecione **Permissões**, e verá as permissões adicionais para o Microsoft Graph.

:::image type="content" alt-text="Screenshot que mostra o painel permissões." source="./media/scenario-secure-app-access-microsoft-graph/enterprise-apps-permissions.png":::

## <a name="call-microsoft-graph-net"></a>Ligue para o Microsoft Graph (.NET)

A classe [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) é usada para obter uma credencial simbólica para o seu código para autorizar pedidos ao Microsoft Graph. Crie uma instância da classe [DefaultAzureCredential,](/dotnet/api/azure.identity.defaultazurecredential) que utiliza a identidade gerida para ir buscar fichas e anexá-las ao cliente de serviço. O seguinte exemplo de código obtém a credencial de token autenticada e usa-a para criar um objeto de cliente de serviço, que recebe os utilizadores do grupo.

Para ver este código como parte de uma aplicação de amostra, consulte a [amostra no GitHub](https://github.com/Azure-Samples/ms-identity-easyauth-dotnet-storage-graphapi/tree/main/3-WebApp-graphapi-managed-identity).

### <a name="install-the-microsoftidentitywebmicrosoftgraph-client-library-package"></a>Instale o pacote de biblioteca cliente Microsoft.Identity.Web.MicrosoftGraph

Instale o [pacote Microsoft.Identity.Web.MicrosoftGraph NuGet](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) no seu projeto utilizando a interface de linha de comando .NET Core ou a consola de gestores de pacotes no Estúdio Visual.

# <a name="command-line"></a>[Linha de comandos](#tab/command-line)

Abra uma linha de comando e mude para o diretório que contém o seu ficheiro de projeto.

Executar os comandos de instalação.

```dotnetcli
dotnet add package Microsoft.Identity.Web.MicrosoftGraph
```

# <a name="package-manager"></a>[Gestor de pacotes](#tab/package-manager)

Abra o projeto/solução no Estúdio Visual e abra a consola utilizando o comando de gestor de **pacotes Tools**  >  **NuGet Package**  >  **Manager.**

Executar os comandos de instalação.
```powershell
Install-Package Microsoft.Identity.Web.MicrosoftGraph
```

---

### <a name="example"></a>Exemplo

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc.RazorPages;
using Azure.Identity;
using Microsoft.Graph.Core;
using System.Net.Http.Headers;

...

public IList<MSGraphUser> Users { get; set; }

public async Task OnGetAsync()
{
    // Create the Microsoft Graph service client with a DefaultAzureCredential class, which gets an access token by using the available Managed Identity.
    var credential = new DefaultAzureCredential();
    var token = credential.GetToken(
        new Azure.Core.TokenRequestContext(
            new[] { "https://graph.microsoft.com/.default" }));

    var accessToken = token.Token;
    var graphServiceClient = new GraphServiceClient(
        new DelegateAuthenticationProvider((requestMessage) =>
        {
            requestMessage
            .Headers
            .Authorization = new AuthenticationHeaderValue("bearer", accessToken);

            return Task.CompletedTask;
        }));

    List<MSGraphUser> msGraphUsers = new List<MSGraphUser>();
    try
    {
        var users =await graphServiceClient.Users.Request().GetAsync();
        foreach(var u in users)
        {
            MSGraphUser user = new MSGraphUser();
            user.userPrincipalName = u.UserPrincipalName;
            user.displayName = u.DisplayName;
            user.mail = u.Mail;
            user.jobTitle = u.JobTitle;

            msGraphUsers.Add(user);
        }
    }
    catch(Exception ex)
    {
        string msg = ex.Message;
    }

    Users = msGraphUsers;
}
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se terminou este tutorial e já não precisa da aplicação web ou dos recursos associados, [limpe os recursos que criou.](scenario-secure-app-clean-up-resources.md)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
>
> * Crie uma identidade gerida atribuída pelo sistema numa aplicação web.
> * Adicione permissões API do Microsoft Graph a uma identidade gerida.
> * Ligue para o Microsoft Graph a partir de uma aplicação web utilizando identidades geridas.

Saiba como ligar uma [aplicação .NET Core](tutorial-dotnetcore-sqldb-app.md), [app Python,](tutorial-python-postgresql-app.md) [Java](tutorial-java-spring-cosmosdb.md)app ou [Node.js app](tutorial-nodejs-mongodb-app.md) a uma base de dados.