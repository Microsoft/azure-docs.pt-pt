---
title: Configure credenciais de implementação
description: Saiba quais os tipos de credenciais de implementação no Azure App Service e como configurá-las e usá-las.
ms.topic: article
ms.date: 08/14/2019
ms.reviewer: byvinyal
ms.custom: seodec18
ms.openlocfilehash: a9d875e2c3899fa91b9cc41c0ee3b5a93ec5b8c8
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78372083"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Configure credenciais de implementação para o Serviço de Aplicações Azure
[O Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) suporta dois tipos de credenciais para a [implantação local](deploy-local-git.md) de Git e [implantação ftp/s](deploy-ftp.md). Estas credenciais não são as mesmas que as suas credenciais de subscrição Azure.

* **Credenciais ao nível do utilizador:** um conjunto de credenciais para toda a conta Azure. Pode ser usado para implementar no App Service para qualquer aplicação, em qualquer subscrição, que a conta Azure tenha permissão para aceder. É o conjunto padrão que é surgido no portal GUI (como a **visão geral** e **propriedades** da página de [recursos](../azure-resource-manager/management/manage-resources-portal.md#manage-resources)da app). Quando um utilizador tem acesso à aplicação através de permissões de Controlo de Acesso Baseados em Funções (RBAC) ou de coadmino, esse utilizador pode utilizar as suas próprias credenciais de nível de utilizador até que o acesso seja revogado. Não partilhe estas credenciais com outros utilizadores do Azure.

* **Credenciais de nível de aplicações**: um conjunto de credenciais para cada aplicação. Pode ser usado para implantar apenas nessa aplicação. As credenciais para cada aplicação são geradas automaticamente na criação de apps. Não podem ser configurados manualmente, mas podem ser reiniciados a qualquer momento. Para que um utilizador tenha acesso a credenciais de nível de aplicações através de RBAC, esse utilizador deve ser contribuinte ou superior na aplicação (incluindo a função incorporada do Colaborador do Website). Os leitores não estão autorizados a publicar, e não podem aceder a essas credenciais.

## <a name="userscope"></a>Configure credenciais de nível de utilizador

Pode configurar as suas credenciais de nível de utilizador na página de [recursos](../azure-resource-manager/management/manage-resources-portal.md#manage-resources)de qualquer aplicação . Independentemente da aplicação que configura estas credenciais, aplica-se a todas as aplicações e a todas as subscrições na sua conta Azure. 

### <a name="in-the-cloud-shell"></a>Na Nuvem Shell

Para configurar o utilizador de implementação na [Cloud Shell,](https://shell.azure.com)execute o comando de conjunto de utilizadores de [implementação de webapp az.](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) Substitua \<username> e \<password> com um nome de utilizador e palavra-passe de utilizador de implementação. 

- O nome de utilizador deve ser único dentro do Azure e, para os pushs git locais, não deve conter o símbolo '@'. 
- A palavra-passe deve ter pelo menos oito caracteres de comprimento, com dois dos seguintes três elementos: letras, números e símbolos. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

A saída JSON mostra a palavra-passe como `null`. Se obtiver o erro `'Conflict'. Details: 409`, altere o nome de utilizador. Se obtiver o `'Bad Request'. Details: 400` erro, utilize uma palavra-passe mais forte. 

### <a name="in-the-portal"></a>No portal

No portal Azure, deve ter pelo menos uma aplicação antes de poder aceder à página de credenciais de implementação. Para configurar as suas credenciais de nível de utilizador:

1. No [portal Azure,](https://portal.azure.com)a partir do menu esquerdo, selecione **Serviços de Aplicações** >  **\<any_app>** centro de **implantação** >  > **FTP** > **Dashboard.**

    ![](./media/app-service-deployment-credentials/access-no-git.png)

    Ou, se já configurar a implementação do Git, selecione **App Services** >  **&lt;any_app>** centro de **implantação** de >  > **FTP/Credenciais**.

    ![](./media/app-service-deployment-credentials/access-with-git.png)

2. Selecione credenciais de **utilizador,** configure o nome do utilizador e a palavra-passe e, em seguida, selecione **'Guardar Credenciais '** .

Depois de definir as suas credenciais de implementação, pode encontrar o nome de utilizador da implementação *git* na página **de visão geral** da sua aplicação,

![](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

Se a implementação de Git estiver configurada, a página mostra um nome de **utilizador Git/implementação;** caso contrário, um **nome de utilizador FTP/implantação**.

> [!NOTE]
> O Azure não mostra a sua senha de implementação ao nível do utilizador. Se esquecer a palavra-passe, pode redefinir as suas credenciais seguindo os passos nesta secção.
>
> 

## <a name="use-user-level-credentials-with-ftpftps"></a>Utilize credenciais de nível de utilizador com FTP/FTPS

Autenticar para um ponto final FTP/FTPS utilizando credenciais de nível de utilizador exige um nome de utilizador no seguinte formato: `<app-name>\<user-name>`

Uma vez que as credenciais de nível de utilizador estão ligadas ao utilizador e não a um recurso específico, o nome de utilizador deve estar neste formato para direcionar a ação de início de sessão para o ponto final da aplicação certa.

## <a name="appscope"></a>Obter e redefinir credenciais de nível de aplicações
Para obter as credenciais de nível de aplicações:

1. No [portal Azure,](https://portal.azure.com)a partir do menu esquerdo, selecione **Serviços de Aplicações** >  **&lt;any_app>** centro de **implantação** >  > **FTP/Credenciais.**

2. Selecione Credenciais de **Aplicações**e selecione o link **Copy** para copiar o nome de utilizador ou palavra-passe.

Para redefinir as credenciais de nível de aplicação, selecione **Reset Credenciais** no mesmo diálogo.

## <a name="next-steps"></a>Passos seguintes

Descubra como usar estas credenciais para implementar a sua aplicação a partir de [Git local](deploy-local-git.md) ou usando [FTP/S](deploy-ftp.md).
