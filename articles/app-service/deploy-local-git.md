---
title: Implantação a partir de git repo local
description: Saiba como permitir a implementação local do Git para o Azure App Service. Uma das formas mais simples de implantar código da sua máquina local.
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.topic: article
ms.date: 06/18/2019
ms.reviewer: dariac
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 9650633e1eaffdb588b3a31cd5a2f305c36e7a25
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92741300"
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

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-with-kudu-build-server"></a>Implementar com o servidor de construção kudu

A forma mais fácil de permitir a implementação local do Git para a sua aplicação com o servidor de construção do Kudu App Service é utilizar o Azure Cloud Shell. 

### <a name="configure-a-deployment-user"></a>Configurar um utilizador de implementação

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="get-the-deployment-url"></a>Obtenha o URL de implementação

Para obter o URL para permitir a implementação local de Git para uma aplicação existente, corra [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) na Cloud Shell. Substitua \<app-name> e \<group-name> pelos nomes da sua app e do seu grupo de recursos Azure.

```azurecli-interactive
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```
> [!NOTE]
> Se estiver a utilizar um plano de serviço de aplicações linux, tem de adicionar este parâmetro: --runtime python /3.7


Ou, para criar uma nova aplicação ativada pelo Git, correr [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) na Cloud Shell com o `--deployment-local-git` parâmetro. Substitua \<app-name> , e com os \<group-name> \<plan-name> nomes da sua nova app Git, o seu grupo de recursos Azure e o seu plano de Serviço de Aplicações Azure.

```azurecli-interactive
az webapp create --name <app-name> --resource-group <group-name> --plan <plan-name> --deployment-local-git
```

Qualquer um dos comandos devolve uma URL como: `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git` . Utilize este URL para implementar a sua aplicação no próximo passo.

Em vez de usar este URL de nível de conta, também pode ativar o Git local usando credenciais de nível de aplicação. O Azure App Service gera automaticamente estas credenciais para cada aplicação. 

Obtenha as credenciais de aplicação executando o seguinte comando na Cloud Shell. Substitua \<app-name> e pelo nome do seu aplicativo e nome do grupo de recursos \<group-name> Azure.

```azurecli-interactive
az webapp deployment list-publishing-credentials --name <app-name> --resource-group <group-name> --query scmUri --output tsv
```

Utilize o URL que retorna para implementar a sua aplicação no passo seguinte.

### <a name="deploy-the-web-app"></a>Implementar a aplicação web

1. Abra uma janela de terminal local para o seu repositório git local, e adicione um remoto Azure. No seguinte comando, \<url> substitua-o pelo URL específico do utilizador de implementação ou URL específico da aplicação que obteve do passo anterior.
   
   ```bash
   git remote add azure <url>
   ```
   
1. Empurre para o comando Azure com `git push azure master` . 
   
1. Na janela **Git Credential Manager,** introduza a [palavra-passe do utilizador de implementação,](#configure-a-deployment-user)não a sua palavra-passe de entrada de Azure.
   
1. Reveja a saída. Você pode ver automação específica do tempo de execução, como MSBuild para ASP.NET, `npm install` para Node.js, e `pip install` para Python. 
   
1. Navegue na sua aplicação no portal Azure para verificar se o conteúdo está implantado.

## <a name="deploy-with-azure-pipelines-builds"></a>Implementar com a Azure Pipelines constrói

Se a sua conta tiver as permissões necessárias, pode configurar a Azure Pipelines (Preview) para permitir a implementação local do Git para a sua aplicação. 

- A sua conta Azure deve ter permissões para escrever ao Azure Ative Directory e criar um serviço. 
  
- A sua conta Azure deve ter a função **de Proprietário** na sua subscrição Azure.

- Deve ser administrador no projeto Azure DevOps que pretende utilizar.

Para ativar a implementação local do Git para a sua aplicação com Azure Pipelines (Preview):

1. No [portal Azure,](https://portal.azure.com)procure e selecione **Serviços de Aplicações.** 

1. Selecione a sua aplicação Azure App Service e selecione **o Centro de Implementação** no menu esquerdo.
   
1. Na página **'Centro de Implantação',** selecione **Local Git** e, em seguida, selecione **Continue** . 
   
   ![Selecione Git local e, em seguida, selecione Continue](media/app-service-deploy-local-git/portal-enable.png)
   
1. Na página do **fornecedor Build,** selecione **Azure Pipelines (Preview)** e, em seguida, selecione **Continue** . 
   
   ![Selecione Azure Pipelines (Preview) e, em seguida, selecione Continue.](media/app-service-deploy-local-git/pipeline-builds.png)

1. Na página **Configure,** configurar uma nova organização Azure DevOps, ou especificar uma organização existente, e, em seguida, selecionar **Continue** .
   
   > [!NOTE]
   > Se a sua organização Azure DevOps existente não estiver listada, poderá ter de ligá-la à sua subscrição Azure. Para obter mais informações, consulte [Definir o seu pipeline de desbloqueio de CD](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd).
   
1. Dependendo do nível de preços do seu [plano de aplicação,](https://azure.microsoft.com/pricing/details/app-service/plans/)poderá ver uma **página de paragem para a paragem.** Escolha se [ativar as ranhuras de implantação](deploy-staging-slots.md)e, em seguida, selecione **Continue** .
   
1. Na página **Resumo,** reveja as definições e, em seguida, **selecione Terminar** .
   
1. Quando o Gasoduto Azure estiver pronto, copie o URL do repositório Git da página **do Centro de Implantação** para utilizar no passo seguinte. 
   
   ![Copiar o URL do repositório de Git](media/app-service-deploy-local-git/vsts-repo-ready.png)

1. Na janela do terminal local, adicione um telecomando Azure ao seu repositório git local. No comando, \<url> substitua-o pelo URL do repositório git que obteve do passo anterior.
   
   ```bash
   git remote add azure <url>
   ```
   
1. Empurre para o comando Azure com `git push azure master` . 
   
1. Na página **Git Credential Manager,** inscreva-se com o seu nome de utilizador visualstudio.com. Para outros métodos de autenticação, consulte [a visão geral da autenticação dos Serviços Azure DevOps](/vsts/git/auth-overview?view=vsts).
   
1. Uma vez concluída a implantação, veja o progresso da construção em `https://<azure_devops_account>.visualstudio.com/<project_name>/_build` , e o progresso da implantação em `https://<azure_devops_account>.visualstudio.com/<project_name>/_release` .
   
1. Navegue na sua aplicação no portal Azure para verificar se o conteúdo está implantado.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-deployment"></a>Implementação de resolução de problemas

Pode ver as seguintes mensagens de erro comuns quando utiliza o Git para publicar numa aplicação do Serviço de Aplicações em Azure:

|Mensagem|Causa|Resolução
---|---|---|
|`Unable to access '[siteURL]': Failed to connect to [scmAddress]`|A aplicação não está a funcionar.|Inicie a aplicação no portal Azure. A implementação do Git não está disponível quando a aplicação web é interrompida.|
|`Couldn't resolve host 'hostname'`|As informações de endereço do telecomando "azul" estão incorretas.|Utilize o `git remote -v` comando para listar todos os telecomandos, juntamente com o URL associado. Verifique se o URL do telecomando 'azul' está correto. Se necessário, remova e recrie este telecomando utilizando o URL correto.|
|`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`|Não especificou um ramo `git push` durante, ou não definiu o `push.default` valor em `.gitconfig` .|Corra `git push` novamente, especificando o ramo principal: `git push azure master` .|
|`src refspec [branchname] does not match any.`|Tentaste empurrar para um ramo que não o mestre no controlo remoto do "azul".|Corra `git push` novamente, especificando o ramo principal: `git push azure master` .|
|`RPC failed; result=22, HTTP code = 5xx.`|Este erro pode ocorrer se tentar empurrar um repositório de git grande sobre HTTPS.|Mude a configuração do git na máquina local para fazer o `postBuffer` maior. Por exemplo: `git config --global http.postBuffer 524288000`.|
|`Error - Changes committed to remote repository but your web app not updated.`|Implementou uma aplicação Node.js com uma _package.jsno_ ficheiro que especifica módulos adicionais necessários.|Reveja as `npm ERR!` mensagens de erro antes deste erro para mais contexto na falha. São as causas conhecidas deste erro e as `npm ERR!` respetivas mensagens:<br /><br />**package.jsmal formados no ficheiro:**`npm ERR! Couldn't read dependencies.`<br /><br />**O módulo nativo não tem uma distribuição binária para o Windows:**<br />`npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1` <br />ou <br />`npm ERR! [modulename@version] preinstall: \make || gmake\ `|

## <a name="additional-resources"></a>Recursos adicionais

- [Documentação do Projeto Kudu](https://github.com/projectkudu/kudu/wiki)
- [Implementação contínua para o Serviço de Aplicações Azure](deploy-continuous-deployment.md)
- [Amostra: Criar uma aplicação web e implementar código a partir de um repositório local de Git (Azure CLI)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
- [Amostra: Criar uma aplicação web e implementar código a partir de um repositório local de Git (PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
