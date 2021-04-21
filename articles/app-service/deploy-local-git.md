---
title: Implantação a partir de git repo local
description: Saiba como permitir a implementação local do Git para o Azure App Service. Uma das formas mais simples de implantar código da sua máquina local.
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.topic: article
ms.date: 02/16/2021
ms.reviewer: dariac
ms.custom: seodec18, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 3196233728bb7f6493bbc06234c62d261ac99254
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832397"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Implantação local de Git para o Serviço de Aplicações Azure

Este guia de como enviar a sua aplicação para [o Azure App Service](overview.md) a partir de um repositório git no seu computador local.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir os passos neste guia:

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [Instale git](https://www.git-scm.com/downloads).

- Tenha um repositório de Git local com código que queira implementar. Para descarregar um repositório de amostras, execute o seguinte comando na janela do terminal local:
  
  ```bash
  git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
  ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="configure-a-deployment-user"></a>Configurar um utilizador de implementação

Consulte [as credenciais de implementação configurar para o Serviço de Aplicações Azure](deploy-configure-credentials.md). Pode utilizar credenciais de alcance do utilizador ou credenciais de âmbito de aplicação.

## <a name="create-a-git-enabled-app"></a>Criar uma aplicação ativada por Git

Se já tem uma aplicação de Serviço de Aplicações e quer configurar a implementação local do Git para a mesmo, consulte [uma aplicação existente.](#configure-an-existing-app)

# <a name="azure-cli"></a>[CLI do Azure](#tab/cli)

Corra [`az webapp create`](/cli/azure/webapp#az_webapp_create) com a `--deployment-local-git` opção. Por exemplo:

```azurecli-interactive
az webapp create --resource-group <group-name> --plan <plan-name> --name <app-name> --runtime "<runtime-flag>" --deployment-local-git
```

A saída contém um URL como: `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git` . Utilize este URL para implementar a sua aplicação no próximo passo.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Executar [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) a partir da raiz do seu repositório Git. Por exemplo:

```azurepowershell-interactive
New-AzWebApp -Name <app-name>
```

Quando você executar este cmdlet a partir de um diretório que é um repositório Git, ele automaticamente cria um comando Git para a sua aplicação de Serviço de Aplicações para si, nomeado `azure` .

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

No portal, é necessário criar uma aplicação primeiro e depois configurar a implementação para a empresa. Consulte [configurar uma aplicação existente.](#configure-an-existing-app)

-----

## <a name="configure-an-existing-app"></a>Configure uma app existente

Se ainda não criou uma aplicação, consulte [uma aplicação ativada](#create-a-git-enabled-app) para Criar o Git.

# <a name="azure-cli"></a>[CLI do Azure](#tab/cli)

[`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config_local_git)Corra. Por exemplo:

```azurecli-interactive
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```

A saída contém um URL como: `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git` . Utilize este URL para implementar a sua aplicação no próximo passo.

> [!TIP]
> Este URL contém o nome de utilizador da implementação do âmbito do utilizador. Se quiser, pode [usar as credenciais de aplicação.](deploy-configure-credentials.md#appscope) 

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Desaprote o `scmType` da sua aplicação executando o [cmdlet Set-AzResource.](/powershell/module/az.resources/set-azresource)

```powershell-interactive
$PropertiesObject = @{
    scmType = "LocalGit";
}

Set-AzResource -PropertyObject $PropertiesObject -ResourceGroupName <group-name> `
-ResourceType Microsoft.Web/sites/config -ResourceName <app-name>/web `
-ApiVersion 2015-08-01 -Force
```

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

1. No [portal Azure,](https://portal.azure.com)navegue para a página de gestão da sua aplicação.

1. A partir do menu esquerdo, selecione **Definições do Centro de**  >  **Implementação**. Selecione **Git local** na **fonte** e, em seguida, clique em **Guardar**.

    ![Mostra como permitir a implantação local do Git para o Serviço de Aplicações no portal Azure](./media/deploy-local-git/enable-portal.png)

1. Na secção Git Local, copie o **Git Clone Uri** para mais tarde. Este Uri não contém credenciais.

-----

## <a name="deploy-the-web-app"></a>Implementar a aplicação web

1. Numa janela de terminal local, mude o diretório para a raiz do seu repositório Git e adicione um comando Git utilizando o URL que obteve da sua aplicação. Se o seu método escolhido não lhe der um URL, use `https://<app-name>.scm.azurewebsites.net/<app-name>.git` com o nome da sua aplicação em `<app-name>` .
   
   ```bash
   git remote add azure <url>
   ```

    > [!NOTE]
    > Se [criou uma aplicação ativada pelo Git no PowerShell utilizando o New-AzWebApp,](#create-a-git-enabled-app)o controlo remoto já está criado para si.
   
1. Empurre para o comando Azure com `git push azure master` . 
   
1. Na janela **Git Credential Manager,** insira as [suas credenciais de âmbito de utilizador ou de âmbito de aplicação](#configure-a-deployment-user), e não as suas credenciais de entrada de Azure.

    Se o seu URL remoto Git já contiver o nome de utilizador e a palavra-passe, não será solicitado. 
   
1. Reveja a saída. Você pode ver automação específica do tempo de execução, como MSBuild para ASP.NET, `npm install` para Node.js, e `pip install` para Python. 
   
1. Navegue na sua aplicação no portal Azure para verificar se o conteúdo está implantado.

## <a name="troubleshoot-deployment"></a>Implementação de resolução de problemas

Pode ver as seguintes mensagens de erro comuns quando utiliza o Git para publicar numa aplicação do Serviço de Aplicações em Azure:

|Mensagem|Causa|Resolução
---|---|---|
|`Unable to access '[siteURL]': Failed to connect to [scmAddress]`|A aplicação não está a funcionar.|Inicie a aplicação no portal Azure. A implementação do Git não está disponível quando a aplicação web é interrompida.|
|`Couldn't resolve host 'hostname'`|As informações de endereço do telecomando "azul" estão incorretas.|Utilize o `git remote -v` comando para listar todos os telecomandos, juntamente com o URL associado. Verifique se o URL do telecomando 'azul' está correto. Se necessário, remova e recrie este telecomando utilizando o URL correto.|
|`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'main'.`|Não especificou um ramo `git push` durante, ou não definiu o `push.default` valor em `.gitconfig` .|Corra `git push` novamente, especificando o ramo principal: `git push azure main` .|
|`Error - Changes committed to remote repository but deployment to website failed.`|Empurrou uma filial local que não corresponde à sucursal de implementação de aplicações em 'azure'.|Verifique se o ramo atual está `master` . Para alterar o ramo predefinido, utilize a `DEPLOYMENT_BRANCH` definição de aplicação.|
|`src refspec [branchname] does not match any.`|Tentou empurrar para um ramo diferente do principal no controlo remoto do "azul".|Corra `git push` novamente, especificando o ramo principal: `git push azure main` .|
|`RPC failed; result=22, HTTP code = 5xx.`|Este erro pode ocorrer se tentar empurrar um repositório de git grande sobre HTTPS.|Mude a configuração do git na máquina local para fazer o `postBuffer` maior. Por exemplo: `git config --global http.postBuffer 524288000`.|
|`Error - Changes committed to remote repository but your web app not updated.`|Implementou uma aplicação Node.js com uma _package.jsno_ ficheiro que especifica módulos adicionais necessários.|Reveja as `npm ERR!` mensagens de erro antes deste erro para mais contexto na falha. São as causas conhecidas deste erro e as `npm ERR!` respetivas mensagens:<br /><br />**package.jsmal formados no ficheiro:**`npm ERR! Couldn't read dependencies.`<br /><br />**O módulo nativo não tem uma distribuição binária para o Windows:**<br />`npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1` <br />ou <br />`npm ERR! [modulename@version] preinstall: \make || gmake\ `|

## <a name="additional-resources"></a>Recursos adicionais

- [Servidor de construção de aplicativos (documentação do Projeto Kudu)](https://github.com/projectkudu/kudu/wiki)
- [Implementação contínua no Serviço de Aplicações do Azure](deploy-continuous-deployment.md)
- [Amostra: Criar uma aplicação web e implementar código a partir de um repositório local de Git (Azure CLI)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
- [Amostra: Criar uma aplicação web e implementar código a partir de um repositório local de Git (PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
