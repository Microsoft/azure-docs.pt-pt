---
title: Desdobre-se a partir de Git repo local
description: Saiba como permitir a implantação local de Git para o Serviço de Aplicações Azure. Uma das formas mais simples de implementar código da sua máquina local.
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.topic: article
ms.date: 06/18/2019
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: efe4c07a6231e0b2c95b049db056a4e5d055db98
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77152997"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Implantação local de Git para o Serviço de Aplicações Azure

Este guia de como fazer mostra como implementar a sua aplicação para o [Azure App Service](overview.md) a partir de um repositório Git no seu computador local.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir os passos neste guia:

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [Instale Git](https://www.git-scm.com/downloads).
  
- Tenha um repositório git local com código que queira implementar. Para descarregar um repositório de amostras, faça o seguinte comando na janela do terminal local:
  
  ```bash
  git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
  ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-with-kudu-build-server"></a>Implementar com o servidor de construção Kudu

A forma mais fácil de permitir a implementação local de Git para a sua aplicação com o servidor de construção kudu App Service é utilizar o Azure Cloud Shell. 

### <a name="configure-a-deployment-user"></a>Configurar um utilizador de implementação

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="get-the-deployment-url"></a>Obtenha o URL de implementação

Para obter o URL para permitir a implementação [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) local de Git para uma aplicação existente, executar na Cloud Shell. Substitua \<o nome \<da aplicação> e o nome de grupo> pelos nomes da sua aplicação e do seu grupo de recursos Azure.

```azurecli-interactive
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```
> [!NOTE]
> Se estiver a utilizar um plano de serviço de aplicações linux, tem de adicionar este parâmetro: --pitão de execução;3.7


Ou, para criar uma nova aplicação [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) ativada por Git, executada na Cloud Shell com o `--deployment-local-git` parâmetro. Substitua \<> de \<nome de aplicativo,> de nome de grupo e \<> de nome de plano com os nomes da sua nova app Git, do seu grupo de recursos Azure e do seu plano de Serviço de Aplicações Azure.

```azurecli-interactive
az webapp create --name <app-name> --resource-group <group-name> --plan <plan-name> --deployment-local-git
```

Qualquer comando devolve um `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git`URL como: . Utilize este URL para implementar a sua aplicação no próximo passo.

Em vez de utilizar este URL de nível de conta, também pode ativar o Git local utilizando credenciais de nível de aplicações. O Azure App Service gera automaticamente estas credenciais para cada aplicação. 

Obtenha as credenciais da aplicação executando o seguinte comando na Cloud Shell. Substitua \<> de \<nome de aplicativo e> de nome de grupo com o nome da sua aplicação e o nome do grupo de recursos Azure.

```azurecli-interactive
az webapp deployment list-publishing-credentials --name <app-name> --resource-group <group-name> --query scmUri --output tsv
```

Utilize o URL que regressa para implementar a sua aplicação no próximo passo.

### <a name="deploy-the-web-app"></a>Implementar a aplicação web

1. Abra uma janela terminal local para o seu repositório git local e adicione um telecomando Azure. No comando seguinte, \<substitua o url> pelo URL específico do utilizador de implementação ou URL específico da aplicação que obteve do passo anterior.
   
   ```bash
   git remote add azure <url>
   ```
   
1. Empurre para o `git push azure master`comando Azure com . 
   
1. Na janela **Git Credential Manager,** introduza a [sua palavra-passe](#configure-a-deployment-user)de utilizador de implementação , e não a sua senha de entrada do Azure.
   
1. Reveja a saída. Você pode ver automatização específica de tempo de `npm install` execução, como MSBuild para ASP.NET, para Node.js, e `pip install` para Python. 
   
1. Navegue na sua aplicação no portal Azure para verificar se o conteúdo está implementado.

## <a name="deploy-with-azure-pipelines-builds"></a>Implantação com pipelines Azure constrói

Se a sua conta tiver as permissões necessárias, pode configurar o Azure Pipelines (Preview) para permitir a implementação local da Git para a sua aplicação. 

- A sua conta Azure deve ter permissões para escrever ao Azure Ative Directory e criar um serviço. 
  
- A sua conta Azure deve ter o papel **de Proprietário** na sua subscrição Azure.

- Deve ser administrador no projeto Azure DevOps que pretende utilizar.

Para permitir a implementação local da Git para a sua aplicação com Pipelines Azure (Pré-visualização):

1. No [portal Azure,](https://portal.azure.com)procure e selecione Serviços de **Aplicações.** 

1. Selecione a sua aplicação Azure App Service e selecione **Deployment Center** no menu esquerdo.
   
1. Na página do **Centro de Implantação,** selecione **Git Local**, e, em seguida, selecione **Continuar**. 
   
   ![Selecione Git Local e, em seguida, selecione Continuar](media/app-service-deploy-local-git/portal-enable.png)
   
1. Na página do **fornecedor Build,** selecione **Pipelines Azure (Pré-visualização)**, e, em seguida, selecione **Continuar**. 
   
   ![Selecione Pipelines Azure (Pré-visualização) e, em seguida, selecione Continue.](media/app-service-deploy-local-git/pipeline-builds.png)

1. Na página **Configure,** configure uma nova organização Azure DevOps, ou especifique uma organização existente, e, em seguida, selecione **Continuar**.
   
   > [!NOTE]
   > Se a sua organização Azure DevOps existente não estiver listada, poderá ter de ligá-la à sua subscrição Azure. Para mais informações, consulte [Defina o seu pipeline](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd)de lançamento de CD .
   
1. Dependendo do seu [nível](https://azure.microsoft.com/pricing/details/app-service/plans/)de preços do plano de app service, pode ver uma página **de implementação.** Escolha se [ativa as ranhuras](deploy-staging-slots.md)de implementação e, em seguida, selecione **Continue**.
   
1. Na página **Resumo,** reveja as definições e, em seguida, selecione **Terminar**.
   
1. Quando o Gasoduto Azure estiver pronto, copie o URL de repositório Git da página do Centro de **Implantação** para utilizar no próximo passo. 
   
   ![Copiar o URL de repositório Git](media/app-service-deploy-local-git/vsts-repo-ready.png)

1. Na janela do terminal local, adicione um telecomando Azure ao seu repositório git local. No comando, \<substitua a url> com o URL do repositório Git que obteve do passo anterior.
   
   ```bash
   git remote add azure <url>
   ```
   
1. Empurre para o `git push azure master`comando Azure com . 
   
1. Na página **git Credential Manager,** inscreva-se com o seu nome de utilizador visualstudio.com. Para outros métodos de autenticação, consulte a visão geral da autenticação dos [Serviços Azure DevOps](/vsts/git/auth-overview?view=vsts).
   
1. Uma vez terminada a implantação, veja o progresso da construção em `https://<azure_devops_account>.visualstudio.com/<project_name>/_build`, e o progresso da implantação em `https://<azure_devops_account>.visualstudio.com/<project_name>/_release`.
   
1. Navegue na sua aplicação no portal Azure para verificar se o conteúdo está implementado.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-deployment"></a>Implantação de problemas

Pode ver as seguintes mensagens de erro comuns quando utilizar o Git para publicar numa aplicação do App Service em Azure:

|Mensagem|Causa|Resolução
---|---|---|
|`Unable to access '[siteURL]': Failed to connect to [scmAddress]`|A aplicação não está a funcionar.|Inicie a aplicação no portal Azure. A implementação da Git não está disponível quando a aplicação web é interrompida.|
|`Couldn't resolve host 'hostname'`|A informação de endereço para o telecomando 'azul' está incorreta.|Utilize `git remote -v` o comando para listar todos os telecomandos, juntamente com o URL associado. Verifique se o URL para o telecomando 'azul' está correto. Se necessário, remova e recrie este controlo remoto utilizando o URL correto.|
|`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`|Não especificou uma `git push`sucursal durante, ou `push.default` não `.gitconfig`definiu o valor em .|Volte `git push` a correr, especificando `git push azure master`o ramo principal: .|
|`src refspec [branchname] does not match any.`|Tentaste empurrar para um ramo que não seja o mestre no controlo remoto azul.|Volte `git push` a correr, especificando `git push azure master`o ramo principal: .|
|`RPC failed; result=22, HTTP code = 5xx.`|Este erro pode acontecer se tentar empurrar um repositório de git grande sobre HTTPS.|Mude a configuração git na `postBuffer` máquina local para fazer o maior. Por exemplo: `git config --global http.postBuffer 524288000`.|
|`Error - Changes committed to remote repository but your web app not updated.`|Implementou uma aplicação Node.js com um ficheiro _package.json_ que especifica módulos adicionais necessários.|Reveja `npm ERR!` as mensagens de erro antes deste erro para obter mais contexto sobre a falha. Seguem-se as causas conhecidas `npm ERR!` deste erro e as mensagens correspondentes:<br /><br />**Ficheiro pacote.json mal formado:**`npm ERR! Couldn't read dependencies.`<br /><br />**O módulo nativo não tem uma distribuição binária para o Windows:**<br />`npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1` <br />ou <br />`npm ERR! [modulename@version] preinstall: \make || gmake\ `|

## <a name="additional-resources"></a>Recursos adicionais

- [Documentação do Projeto Kudu](https://github.com/projectkudu/kudu/wiki)
- [Implantação contínua para o Serviço de Aplicações Azure](deploy-continuous-deployment.md)
- [Amostra: Criar uma aplicação web e implementar código a partir de um repositório git local (Azure CLI)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
- [Amostra: Criar uma aplicação web e implementar código a partir de um repositório git local (PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
