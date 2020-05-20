---
title: Configurar as credenciais de implementação
description: Saiba quais os tipos de credenciais de implementação no Azure App Service e como configurá-las e usá-las.
ms.topic: article
ms.date: 08/14/2019
ms.reviewer: byvinyal
ms.custom: seodec18
ms.openlocfilehash: c6f7c2422e043da6df498fe81da938576687b916
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83649140"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Configure credenciais de implementação para o Serviço de Aplicações Azure
[O Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) suporta dois tipos de credenciais para a [implantação local](deploy-local-git.md) de Git e [implantação ftp/s](deploy-ftp.md). Estas credenciais não são as mesmas que as suas credenciais de subscrição Azure.

[!INCLUDE [app-service-deploy-credentials](../../includes/app-service-deploy-credentials.md)]

## <a name="configure-user-level-credentials"></a><a name="userscope"></a>Configure credenciais de nível de utilizador

Pode configurar as suas credenciais de nível de utilizador na página de [recursos](../azure-resource-manager/management/manage-resources-portal.md#manage-resources)de qualquer aplicação . Independentemente da aplicação que configura estas credenciais, aplica-se a todas as aplicações e a todas as subscrições na sua conta Azure. 

### <a name="in-the-cloud-shell"></a>Na Nuvem Shell

Para configurar o utilizador de implementação na [Cloud Shell,](https://shell.azure.com)execute o comando de conjunto de utilizadores de [implementação de webapp az.](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) Substitua \< o nome de utilizador> e a \< palavra-passe> por um nome de utilizador e palavra-passe de implementação. 

- O nome de utilizador deve ser único dentro do Azure e, para os pushs git locais, não deve conter o símbolo '@'. 
- A palavra-passe deve ter pelo menos oito caracteres de comprimento, com dois dos seguintes três elementos: letras, números e símbolos. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

A saída JSON mostra a palavra-passe como `null` . Se obtiver o erro `'Conflict'. Details: 409`, altere o nome de utilizador. Se obtiver o `'Bad Request'. Details: 400` erro, utilize uma palavra-passe mais forte. 

### <a name="in-the-portal"></a>No portal

No portal Azure, deve ter pelo menos uma aplicação antes de poder aceder à página de credenciais de implementação. Para configurar as suas credenciais de nível de utilizador:

1. No [portal Azure](https://portal.azure.com), a partir do menu esquerdo, selecione **Serviços de Aplicação**  >  ** \< any_app>** Centro de  >  **Implantação**  >  **FTP**  >  **Dashboard**.

    ![](./media/app-service-deployment-credentials/access-no-git.png)

    Ou, se já configurar a implementação do Git, selecione **App Services**  >  ** &lt; any_app>**  >  **Deployment center**  >  **FTP/Credentials**.

    ![](./media/app-service-deployment-credentials/access-with-git.png)

2. Selecione credenciais de **utilizador,** configure o nome do utilizador e a palavra-passe e, em seguida, selecione **'Guardar Credenciais '**.

Depois de definir as suas credenciais de implementação, pode encontrar o nome de utilizador da implementação *git* na página **de visão geral** da sua aplicação,

![](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

Se a implementação de Git estiver configurada, a página mostra um nome de **utilizador Git/implementação;** caso contrário, um **nome de utilizador FTP/implantação**.

> [!NOTE]
> O Azure não mostra a sua senha de implementação ao nível do utilizador. Se esquecer a palavra-passe, pode redefinir as suas credenciais seguindo os passos nesta secção.
>
> 

## <a name="use-user-level-credentials-with-ftpftps"></a>Utilize credenciais de nível de utilizador com FTP/FTPS

Autenticar para um ponto final FTP/FTPS utilizando credenciais de nível de utilizador requer um nome de utilizador no seguinte formato:`<app-name>\<user-name>`

Uma vez que as credenciais de nível de utilizador estão ligadas ao utilizador e não a um recurso específico, o nome de utilizador deve estar neste formato para direcionar a ação de início de sessão para o ponto final da aplicação certa.

## <a name="get-and-reset-app-level-credentials"></a><a name="appscope"></a>Obter e redefinir credenciais de nível de aplicações
Para obter as credenciais de nível de aplicações:

1. No [portal Azure,](https://portal.azure.com)a partir do menu esquerdo, selecione **Serviços de Aplicação**  >  ** &lt; any_app>** Centro de  >  **Implantação**  >  **FTP/Credenciais**.

2. Selecione Credenciais de **Aplicações**e selecione o link **Copy** para copiar o nome de utilizador ou palavra-passe.

Para redefinir as credenciais de nível de aplicação, selecione **Reset Credenciais** no mesmo diálogo.

## <a name="next-steps"></a>Passos seguintes

Descubra como usar estas credenciais para implementar a sua aplicação a partir de [Git local](deploy-local-git.md) ou usando [FTP/S](deploy-ftp.md).
