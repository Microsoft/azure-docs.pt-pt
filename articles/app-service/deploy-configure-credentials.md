---
title: Configurar as credenciais de implementação
description: Saiba quais tipos de credenciais de implantação estão no serviço Azure App e como configurá-las e usá-las.
ms.topic: article
ms.date: 08/14/2019
ms.reviewer: byvinyal
ms.custom: seodec18
ms.openlocfilehash: 15b556781f8ba620ab61c502b6f55e55e98df83c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75430536"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Configurar credenciais de implantação para o serviço Azure App
[Azure app serviço](https://go.microsoft.com/fwlink/?LinkId=529714) dá suporte a dois tipos de credenciais para implantação do [git local](deploy-local-git.md) e [implantação de FTP/S](deploy-ftp.md). Essas credenciais não são as mesmas que suas credenciais de assinatura do Azure.

* **Credenciais de nível de usuário**: um conjunto de credenciais para toda a conta do Azure. Ele pode ser usado para implantar o serviço de aplicativo para qualquer aplicativo, em qualquer assinatura, que a conta do Azure tenha permissão para acessar. É o conjunto padrão que é exibido na GUI do portal (como a **visão geral** e **as propriedades** da [página de recursos](../azure-resource-manager/management/manage-resources-portal.md#manage-resources)do aplicativo). Quando um usuário recebe acesso de aplicativo via RBAC (controle de acesso baseado em função) ou permissões de coadministrador, esse usuário pode usar suas próprias credenciais de nível de usuário até que o acesso seja revogado. Não compartilhe essas credenciais com outros usuários do Azure.

* **Credenciais de nível de aplicativo**: um conjunto de credenciais para cada aplicativo. Ele pode ser usado para implantar somente nesse aplicativo. As credenciais para cada aplicativo são geradas automaticamente na criação do aplicativo. Eles não podem ser configurados manualmente, mas podem ser redefinidos a qualquer momento. Para que um usuário receba acesso às credenciais de nível de aplicativo via (RBAC), esse usuário deve ser colaborador ou superior no aplicativo. Os leitores não têm permissão para publicar e não podem acessar essas credenciais.

## <a name="userscope"></a>Configurar credenciais de nível de usuário

Você pode configurar suas credenciais no nível do usuário na página de [recursos](../azure-resource-manager/management/manage-resources-portal.md#manage-resources)de qualquer aplicativo. Independentemente de qual aplicativo você configurar essas credenciais, ele se aplica a todos os aplicativos e a todas as assinaturas em sua conta do Azure. 

### <a name="in-the-cloud-shell"></a>No Cloud Shell

Para configurar o usuário de implantação no [Cloud Shell](https://shell.azure.com), execute o comando [AZ webapp Deployment User Set](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) . Substitua \<nome de usuário > e \<senha > com um nome de usuário de implantação e senha. 

- O nome de usuário deve ser exclusivo no Azure e, para Pushes git locais, não deve conter o símbolo ' @ '. 
- A senha deve ter pelo menos oito caracteres de comprimento, com dois dos três elementos a seguir: letras, números e símbolos. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

A saída JSON mostra a senha como `null`. Se obtiver o erro `'Conflict'. Details: 409`, altere o nome de utilizador. Se obtiver o `'Bad Request'. Details: 400` erro, utilize uma palavra-passe mais forte. 

### <a name="in-the-portal"></a>No portal

No portal do Azure, você deve ter pelo menos um aplicativo para poder acessar a página de credenciais de implantação. Para configurar suas credenciais de nível de usuário:

1. No [portal do Azure](https://portal.azure.com), no menu à esquerda, selecione **serviços de aplicativos** >  **\<Any_app >**  > **central de implantação** > **painel**de > de **FTP** .

    ![](./media/app-service-deployment-credentials/access-no-git.png)

    Ou, se você já tiver configurado a implantação do git, selecione **serviços de aplicativos** >  **&lt;any_app >**  > **central de implantação** > **FTP/credenciais**.

    ![](./media/app-service-deployment-credentials/access-with-git.png)

2. Selecione **credenciais de usuário**, configure o nome de usuário e a senha e, em seguida, selecione **salvar credenciais**.

Depois de definir suas credenciais de implantação, você pode encontrar o nome de usuário da implantação do *git* na página de **visão geral** do seu aplicativo,

![](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

Se a implantação do git estiver configurada, a página mostrará um **nome de usuário do git/implantação**; caso contrário, um **nome de usuário de FTP/implantação**.

> [!NOTE]
> O Azure não mostra sua senha de implantação no nível do usuário. Se você esquecer a senha, poderá redefinir suas credenciais seguindo as etapas nesta seção.
>
> 

## <a name="use-user-level-credentials-with-ftpftps"></a>Usar credenciais de nível de usuário com FTP/FTPS

A autenticação em um ponto de extremidade de FTP/FTPS usando credenciais de nível de usuário exige um nome de usuário no seguinte formato: `<app-name>\<user-name>`

Como as credenciais de nível de usuário são vinculadas ao usuário e não a um recurso específico, o nome de usuários deve estar nesse formato para direcionar a ação de entrada para o ponto de extremidade do aplicativo certo.

## <a name="appscope"></a>Obter e redefinir credenciais de nível de aplicativo
Para obter as credenciais de nível de aplicativo:

1. No [portal do Azure](https://portal.azure.com), no menu à esquerda, selecione **serviços de aplicativos** >  **&lt;Any_app >**  > **central de implantação** > **FTP/credenciais**.

2. Selecione **credenciais do aplicativo**e selecione o link **copiar** para copiar o nome de usuário ou a senha.

Para redefinir as credenciais de nível de aplicativo, selecione **Redefinir credenciais** na mesma caixa de diálogo.

## <a name="next-steps"></a>Passos seguintes

Descubra como usar essas credenciais para implantar seu aplicativo do [git local](deploy-local-git.md) ou usando [FTP/S](deploy-ftp.md).
