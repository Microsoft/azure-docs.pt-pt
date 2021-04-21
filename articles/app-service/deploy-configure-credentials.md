---
title: Configurar as credenciais de implementação
description: Saiba que tipos de credenciais de implementação estão no Azure App Service e como configurá-las e usá-las.
ms.topic: article
ms.date: 02/11/2021
ms.reviewer: byvinyal
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: b77a26f61e1168846156de990806bbed2f7c41e3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789542"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Configure credenciais de implementação para o Azure App Service
Para garantir a implementação de aplicações a partir de um computador local, [o Azure App Service](./overview.md) suporta dois tipos de credenciais para a [implementação local](deploy-local-git.md) do Git e para a [implementação ftp/S](deploy-ftp.md). Estas credenciais não são as mesmas que as suas credenciais de subscrição Azure.

[!INCLUDE [app-service-deploy-credentials](../../includes/app-service-deploy-credentials.md)]

> [!NOTE]
> A página **do Centro de Desenvolvimento (Clássico)** no portal Azure, que é a antiga experiência de implantação, será depretada em março de 2021. Esta alteração não afetará quaisquer definições de implementação existentes na sua aplicação, podendo continuar a gerir a implementação de aplicações na página **do Centro de Implementação.**

## <a name="configure-user-scope-credentials"></a><a name="userscope"></a>Configure credenciais de alcance do utilizador

# <a name="azure-cli"></a>[CLI do Azure](#tab/cli)

Executar o comando [de conjunto de utilizadores de implementação az webapp.](/cli/azure/webapp/deployment/user#az_webapp_deployment_user_set) Substitua \<username> e por um nome de utilizador de \<password> implementação e senha. 

- O nome de utilizador deve ser único dentro do Azure, e para os pushes git locais, não deve conter o símbolo '@'. 
- A palavra-passe deve ter pelo menos oito caracteres, com dois dos seguintes três elementos: letras, números e símbolos. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

A saída JSON mostra a palavra-passe como `null` .

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Não é possível configurar as credenciais de alcance do utilizador com a Azure PowerShell. Utilize um método diferente, ou considere [a utilização de credenciais de âmbito de aplicação](#appscope). 

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Pode configurar as suas credenciais de alcance de utilizador na página de [recursos](../azure-resource-manager/management/manage-resources-portal.md#manage-resources)de qualquer aplicação. Independentemente da aplicação que configura estas credenciais, aplica-se a todas as aplicações para todas as subscrições da sua conta Azure. 

No [portal Azure,](https://portal.azure.com)tem de ter pelo menos uma aplicação antes de poder aceder à página de credenciais de implementação. Para configurar as suas credenciais de âmbito de utilizador:

1. A partir do menu esquerdo da sua aplicação, selecione credenciais ftps **do centro de implantação**> ou  >   **credenciais Locais Git/FTPS**.

    ![Mostra como pode selecionar o dashboard FTP a partir do centro de implementação em Azure App Services.](./media/app-service-deployment-credentials/access-no-git.png)

2. Desloque-se até ao **âmbito do Utilizador,** configuure o **nome de utilizador** e a **palavra-passe** e, em seguida, selecione **Guardar**.

Uma vez definidas as suas credenciais de implementação, pode encontrar o nome de utilizador da implementação *do Git* na **página** geral da sua aplicação,

![Mostra como encontrar o nome de utilizador da implementação do Git na página geral da sua aplicação.](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

Se a implementação do Git estiver configurada, a página mostra um **nome de utilizador Git/deployment**; caso contrário, um nome **de utilizador FTP/implementação**.

> [!NOTE]
> O Azure não mostra a sua senha de implementação de âmbito de utilizador. Se esquecer a palavra-passe, pode redefinir as suas credenciais seguindo os passos desta secção.
>
> 

-----

## <a name="use-user-scope-credentials-with-ftpftps"></a>Utilize credenciais de alcance do utilizador com FTP/FTPS

A autenticação num ponto final FTP/FTPS utilizando credenciais de alcance do utilizador requer um nome de utilizador no seguinte formato: `<app-name>\<user-name>`

Uma vez que as credenciais de alcance do utilizador estão ligadas ao utilizador e não a um recurso específico, o nome de utilizador deve estar neste formato para direcionar a ação de entrada para o ponto final da aplicação certa.

## <a name="get-application-scope-credentials"></a><a name="appscope"></a>Obtenha credenciais de aplicação-âmbito

# <a name="azure-cli"></a>[CLI do Azure](#tab/cli)

Obtenha as credenciais de aplicação-âmbito usando o comando [de perfis de lista de publicação de listas de publicação da webapp az.](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) Por exemplo:

```azurecli-interactive
az webapp deployment list-publishing-profiles --resource-group <group-name> --name <app-name>
```

Para [a implementação local do Git](deploy-local-git.md), também pode utilizar o comando de [credenciais de publicação de listas de publicação de webapp az](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_credentials) para obter um URI remoto Git para a sua aplicação, com as credenciais de âmbito de aplicação já incorporadas. Por exemplo:

```azurecli-interactive
az webapp deployment list-publishing-credentials --resource-group <group-name> --name <app-name> --query scmUri
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Obtenha as credenciais de âmbito de aplicação usando o comando [Get-AzWebAppPublishingProfile.](/powershell/module/az.websites/get-azwebapppublishingprofile) Por exemplo:

```azurepowershell-interactive
Get-AzWebAppPublishingProfile -ResourceGroupName <group-name> -Name <app-name>
```

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

1. A partir do menu esquerdo da sua aplicação, selecione credenciais FTPS **do centro de implantação** ou  >   **credenciais Locais Git/FTPS**.

    ![Mostra como pode selecionar o dashboard FTP a partir do centro de implementação em Azure App Services.](./media/app-service-deployment-credentials/access-no-git.png)

2. Na secção **de âmbito de aplicação,** selecione o link **Copiar** para copiar o nome de utilizador ou palavra-passe.

-----

## <a name="reset-application-scope-credentials"></a>Redefinir credenciais de âmbito de aplicação

# <a name="azure-cli"></a>[CLI do Azure](#tab/cli)

Redefinir as credenciais de âmbito de aplicação utilizando o comando [de ação de invocação de recursos az:](/cli/azure/resource#az_resource_invoke_action)

```azurecli-interactive
az resource invoke-action --action newpassword --resource-group <group-name> --name <app-name> --resource-type Microsoft.Web/sites
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Redefinir as credenciais de âmbito de aplicação utilizando o comando [Invoke-AzResourceAction:](/powershell/module/az.resources/invoke-azresourceaction)

```azurepowershell-interactive
Invoke-AzResourceAction -ResourceGroupName <group-name> -ResourceType Microsoft.Web/sites -ResourceName <app-name> -Action newpassword
```

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

1. A partir do menu esquerdo da sua aplicação, selecione credenciais FTPS **do centro de implantação** ou  >   **credenciais Locais Git/FTPS**.

    ![Mostra como pode selecionar o dashboard FTP a partir do centro de implementação em Azure App Services.](./media/app-service-deployment-credentials/access-no-git.png)

2. Na secção **de âmbito de aplicação,** selecione **Reset**.

-----

## <a name="disable-basic-authentication"></a>Desativar a autenticação básica

Algumas organizações precisam de cumprir os requisitos de segurança e preferem desativar o acesso através de FTP ou WebDeploy. Desta forma, os membros da organização só podem aceder aos seus Serviços de Aplicações através de APIs que são controlados pelo Azure Ative Directory (Azure AD).

### <a name="ftp"></a>FTP

Para desativar o acesso FTP ao site, executar o seguinte comando CLI. Substitua os espaços reservados pelo seu grupo de recursos e nome do local. 

```azurecli-interactive
az resource update --resource-group <resource-group> --name ftp --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

Para confirmar que o acesso FTP está bloqueado, pode tentar autenticar usando um cliente FTP, como o FileZilla. Para obter as credenciais de publicação, vá à lâmina de visão geral do seu site e clique em Baixar Perfil de Publicação. Utilize o nome de anfitrião FTP do ficheiro, o nome de utilizador e a palavra-passe para autenticar, e obterá uma resposta de erro 401, indicando que não está autorizado.

### <a name="webdeploy-and-scm"></a>WebDeploy e SCM

Para desativar o acesso básico à porta WebDeploy e ao site SCM, executar o seguinte comando CLI. Substitua os espaços reservados pelo seu grupo de recursos e nome do local. 

```azurecli-interactive
az resource update --resource-group <resource-group> --name scm --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

Para confirmar que as credenciais de perfil de publicação estão bloqueadas na WebDeploy, tente [publicar uma aplicação web utilizando o Visual Studio 2019](/visualstudio/deployment/quickstart-deploy-to-azure).

### <a name="disable-access-to-the-api"></a>Desativar o acesso à API

A API na secção anterior é apoiada pelo controlo de acesso baseado em funções Azure (Azure RBAC), o que significa que você pode [criar uma função personalizada](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role) e atribuir utilizadores de baixos privados para o papel para que eles não possam ativar auth básico em qualquer site. Para configurar o papel personalizado, [siga estas instruções](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#create-a-custom-rbac-role).

Também pode utilizar [o Azure Monitor](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#audit-with-azure-monitor) para auditar quaisquer pedidos de autenticação bem-sucedidos e utilizar a [Azure Policy](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#enforce-compliance-with-azure-policy) para impor esta configuração para todos os sites da sua subscrição.

## <a name="next-steps"></a>Passos seguintes

Descubra como usar estas credenciais para implementar a sua aplicação a partir de [Git local](deploy-local-git.md) ou usando [FTP/S](deploy-ftp.md).
