---
title: Configurar as credenciais de implementação
description: Saiba que tipos de credenciais de implementação estão no Azure App Service e como configurá-las e usá-las.
ms.topic: article
ms.date: 08/14/2019
ms.reviewer: byvinyal
ms.custom: seodec18
ms.openlocfilehash: 653110b953b6947254d5063a9e389505d45ea4cb
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92149013"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Configure credenciais de implementação para o Azure App Service
[O Azure App Service](./overview.md) suporta dois tipos de credenciais para [a implantação local](deploy-local-git.md) de Git e [implantação ftp/S](deploy-ftp.md). Estas credenciais não são as mesmas que as suas credenciais de subscrição Azure.

[!INCLUDE [app-service-deploy-credentials](../../includes/app-service-deploy-credentials.md)]

## <a name="configure-user-level-credentials"></a><a name="userscope"></a>Configure credenciais de nível de utilizador

Pode configurar as suas credenciais de nível de utilizador na página de [recursos](../azure-resource-manager/management/manage-resources-portal.md#manage-resources)de qualquer aplicação. Independentemente da aplicação que configura estas credenciais, aplica-se a todas as aplicações e a todas as subscrições da sua conta Azure. 

### <a name="in-the-cloud-shell"></a>Na Casca de Nuvem

Para configurar o utilizador de implementação no [Cloud Shell,](https://shell.azure.com)executar o comando [de conjunto de utilizadores de implementação az webapp.](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) Substitua \<username> e por um nome de utilizador de \<password> implementação e senha. 

- O nome de utilizador deve ser único dentro do Azure, e para os pushes git locais, não deve conter o símbolo '@'. 
- A palavra-passe deve ter pelo menos oito caracteres, com dois dos seguintes três elementos: letras, números e símbolos. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

A saída JSON mostra a palavra-passe como `null` . Se obtiver o erro `'Conflict'. Details: 409`, altere o nome de utilizador. Se obtiver o `'Bad Request'. Details: 400` erro, utilize uma palavra-passe mais forte. 

### <a name="in-the-portal"></a>No portal

No portal Azure, deve ter pelo menos uma aplicação antes de poder aceder à página de credenciais de implementação. Para configurar as suas credenciais ao nível do utilizador:

1. No [portal Azure](https://portal.azure.com), a partir do menu esquerdo, selecione **App Services**  >  **\<any_app>**  >  **Deployment center**  >  **FTP**  >  **Dashboard**.

    ![Mostra como pode selecionar o dashboard FTP a partir do centro de implementação em Azure App Services.](./media/app-service-deployment-credentials/access-no-git.png)

    Ou, se já configurar a implementação do Git, selecione Serviços de **Aplicações**  >  ** &lt; any_app>**  >  **Centro de Implementação**  >  **FTP/Credenciais**.

    ![Mostra como pode selecionar o dashboard FTP a partir do centro de implementação em Azure App Services para a sua configuração de implementação do Git.](./media/app-service-deployment-credentials/access-with-git.png)

2. Selecione **Credenciais de Utilizador,** configure o nome de utilizador e a palavra-passe e, em seguida, selecione **Guardar Credenciais**.

Uma vez definidas as suas credenciais de implementação, pode encontrar o nome de utilizador da implementação *do Git* na **página** geral da sua aplicação,

![Mostra como encontrar o nome de utilizador da implementação do Git na página geral da sua aplicação.](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

Se a implementação do Git estiver configurada, a página mostra um **nome de utilizador Git/deployment**; caso contrário, um nome **de utilizador FTP/implementação**.

> [!NOTE]
> O Azure não mostra a sua senha de implementação ao nível do utilizador. Se esquecer a palavra-passe, pode redefinir as suas credenciais seguindo os passos desta secção.
>
> 

## <a name="use-user-level-credentials-with-ftpftps"></a>Utilize credenciais ao nível do utilizador com FTP/FTPS

Autenticação num ponto final FTP/FTPS utilizando credenciais de nível de utilizador requer um nome de utilizador no seguinte formato: `<app-name>\<user-name>`

Uma vez que as credenciais ao nível do utilizador estão ligadas ao utilizador e não a um recurso específico, o nome de utilizador deve estar neste formato para direcionar a ação de entrada para o ponto final da aplicação certa.

## <a name="get-and-reset-app-level-credentials"></a><a name="appscope"></a>Obtenha e reinicie credenciais de nível de aplicativo
Para obter as credenciais de nível de aplicação:

1. No [portal Azure](https://portal.azure.com), a partir do menu esquerdo, selecione Serviços de **Aplicações**  >  ** &lt; any_app>**  >  **Centro de Implementação**  >  **FTP/Credenciais**.

2. Selecione **Credenciais de Aplicação**e selecione o link **Copy** para copiar o nome de utilizador ou palavra-passe.

Para redefinir as credenciais de nível de aplicação, selecione **'Repor credenciais'** no mesmo diálogo.

## <a name="disable-basic-authentication"></a>Desativar a autenticação básica

Algumas organizações precisam de cumprir os requisitos de segurança e preferem desativar o acesso através de FTP ou WebDeploy. Desta forma, os membros da organização só podem aceder aos seus Serviços de Aplicações através de APIs que são controlados pelo Azure Ative Directory (Azure AD).

### <a name="ftp"></a>FTP

Para desativar o acesso FTP ao site, executar o seguinte comando CLI. Substitua os espaços reservados pelo seu grupo de recursos e nome do local. 

```bash
az resource update --resource-group <resource-group> --name ftp --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

Para confirmar que o acesso FTP está bloqueado, pode tentar autenticar usando um cliente FTP, como o FileZilla. Para obter as credenciais de publicação, vá à lâmina de visão geral do seu site e clique em Baixar Perfil de Publicação. Utilize o nome de anfitrião FTP do ficheiro, o nome de utilizador e a palavra-passe para autenticar, e obterá uma resposta de erro 401, indicando que não está autorizado.

### <a name="webdeploy-and-scm"></a>WebDeploy e SCM

Para desativar o acesso básico à porta WebDeploy e ao site SCM, executar o seguinte comando CLI. Substitua os espaços reservados pelo seu grupo de recursos e nome do local. 

```bash
az resource update --resource-group <resource-group> --name scm --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

Para confirmar que as credenciais de perfil de publicação estão bloqueadas na WebDeploy, tente [publicar uma aplicação web utilizando o Visual Studio 2019](/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019).

### <a name="disable-access-to-the-api"></a>Desativar o acesso à API

A API na secção anterior é apoiada pelo controlo de acesso baseado em funções Azure (Azure RBAC), o que significa que você pode [criar uma função personalizada](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role) e atribuir utilizadores de baixos privados para o papel para que eles não possam ativar auth básico em qualquer site. Para configurar o papel personalizado, [siga estas instruções](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#create-a-custom-rbac-role).

Também pode utilizar [o Azure Monitor](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#audit-with-azure-monitor) para auditar quaisquer pedidos de autenticação bem-sucedidos e utilizar a [Azure Policy](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#enforce-compliance-with-azure-policy) para impor esta configuração para todos os sites da sua subscrição.

## <a name="next-steps"></a>Passos seguintes

Descubra como usar estas credenciais para implementar a sua aplicação a partir de [Git local](deploy-local-git.md) ou usando [FTP/S](deploy-ftp.md).