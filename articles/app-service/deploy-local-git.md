---
title: Implantar do repositório git local
description: Saiba como habilitar a implantação do git local no serviço Azure App. Uma das maneiras mais simples de implantar o código de seu computador local.
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.topic: article
ms.date: 06/18/2019
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 2ae8b71a7d48949cd82765112752192aba54521f
ms.sourcegitcommit: a100e3d8b0697768e15cbec11242e3f4b0e156d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2020
ms.locfileid: "75680958"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Implantação do git local no serviço Azure App

Este guia de instruções mostra como implantar seu aplicativo no [serviço Azure app](overview.md) de um repositório git em seu computador local.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir as etapas neste guia de instruções:

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [Instale o Git](https://www.git-scm.com/downloads).
  
- Ter um repositório git local com código que você deseja implantar. Para baixar um repositório de exemplo, execute o seguinte comando na janela do seu terminal local:
  
  ```bash
  git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
  ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-with-kudu-build-server"></a>Implantar com o servidor de Build do kudu

A maneira mais fácil de habilitar a implantação do git local para seu aplicativo com o servidor de Build do serviço de aplicativo do kudu é usar Azure Cloud Shell. 

### <a name="configure-a-deployment-user"></a>Configurar um utilizador de implementação

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="get-the-deployment-url"></a>Obter a URL de implantação

Para obter a URL para habilitar a implantação do git local para um aplicativo existente, execute [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) no Cloud Shell. Substitua \<> de nome de aplicativo e \<> de nome de grupo pelos nomes de seu aplicativo e seu grupo de recursos do Azure.

```azurecli-interactive
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```

Ou, para criar um novo aplicativo habilitado para git, execute [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) no Cloud shell com o parâmetro `--deployment-local-git`. Substitua \<> de nome de aplicativo, \<> de nome de grupo e \<nome do plano > com os nomes de seu novo aplicativo git, seu grupo de recursos do Azure e seu plano de serviço do Azure App.

```azurecli-interactive
az webapp create --name <app-name> --resource-group <group-name> --plan <plan-name> --deployment-local-git
```

Um dos comandos retorna uma URL como: `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git`. Use essa URL para implantar seu aplicativo na próxima etapa.

Em vez de usar essa URL de nível de conta, você também pode habilitar o Git local usando credenciais de nível de aplicativo. Azure App serviço gera automaticamente essas credenciais para cada aplicativo. 

Obtenha as credenciais do aplicativo executando o comando a seguir no Cloud Shell. Substitua \<nome do aplicativo > e \<> do nome do grupo com o nome do seu aplicativo e o nome do grupo de recursos do Azure.

```azurecli-interactive
az webapp deployment list-publishing-credentials --name <app-name> --resource-group <group-name> --query scmUri --output tsv
```

Use a URL que retorna para implantar seu aplicativo na próxima etapa.

### <a name="deploy-the-web-app"></a>Implantar o aplicativo Web

1. Abra uma janela de terminal local para seu repositório git local e adicione um Azure remoto. No comando a seguir, substitua \<URL > com a URL específica do usuário de implantação ou URL específica do aplicativo obtida da etapa anterior.
   
   ```bash
   git remote add azure <url>
   ```
   
1. Envie por push para o remoto do Azure com o `git push azure master`. 
   
1. Na janela **Gerenciador de credenciais do git** , insira sua [senha de usuário de implantação](#configure-a-deployment-user), não sua senha de logon do Azure.
   
1. Reveja a saída. Você pode ver a automação específica do tempo de execução, como o MSBuild para ASP.NET, `npm install` para node. js e `pip install` para Python. 
   
1. Navegue até seu aplicativo no portal do Azure para verificar se o conteúdo está implantado.

## <a name="deploy-with-azure-pipelines-builds"></a>Implantar com Builds Azure Pipelines

Se sua conta tiver as permissões necessárias, você poderá configurar Azure Pipelines (versão prévia) para habilitar a implantação do git local para seu aplicativo. 

- Sua conta do Azure deve ter permissões para gravar em Azure Active Directory e criar um serviço. 
  
- Sua conta do Azure deve ter a função de **proprietário** em sua assinatura do Azure.

- Você deve ser um administrador no projeto DevOps do Azure que deseja usar.

Para habilitar a implantação do git local para seu aplicativo com Azure Pipelines (versão prévia):

1. Na [portal do Azure](https://portal.azure.com), procure e selecione serviços de **aplicativos**. 

1. Selecione seu aplicativo de serviço Azure App e selecione **central de implantação** no menu à esquerda.
   
1. Na página **centro de implantação** , selecione **git local**e, em seguida, selecione **continuar**. 
   
   ![Selecione git local e, em seguida, selecione continuar](media/app-service-deploy-local-git/portal-enable.png)
   
1. Na página **provedor de compilação** , selecione **Azure pipelines (versão prévia)** e, em seguida, selecione **continuar**. 
   
   ![Selecione Azure Pipelines (versão prévia) e, em seguida, selecione continuar.](media/app-service-deploy-local-git/pipeline-builds.png)

1. Na página **Configurar** , configure uma nova organização de DevOps do Azure ou especifique uma organização existente e, em seguida, selecione **continuar**.
   
   > [!NOTE]
   > Se sua organização do Azure DevOps existente não estiver listada, talvez seja necessário vinculá-la à sua assinatura do Azure. Para obter mais informações, consulte [definir o pipeline de liberação de CD](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd).
   
1. Dependendo do [tipo de preço](https://azure.microsoft.com/pricing/details/app-service/plans/)do plano do serviço de aplicativo, você poderá ver uma página **implantar na preparação** . Escolha se deseja [habilitar os slots de implantação](deploy-staging-slots.md)e, em seguida, selecione **continuar**.
   
1. Na página **Resumo** , examine as configurações e selecione **concluir**.
   
1. Quando o pipeline do Azure estiver pronto, copie a URL do repositório git da página **centro de implantação** para usar na próxima etapa. 
   
   ![Copiar a URL do repositório git](media/app-service-deploy-local-git/vsts-repo-ready.png)

1. Na janela do terminal local, adicione um remoto do Azure ao repositório git local. No comando, substitua \<URL > pela URL do repositório git que você obteve da etapa anterior.
   
   ```bash
   git remote add azure <url>
   ```
   
1. Envie por push para o remoto do Azure com o `git push azure master`. 
   
1. Na página **Gerenciador de credenciais do git** , entre com seu nome de usuário do VisualStudio.com. Para obter outros métodos de autenticação, consulte [visão geral da autenticação de Azure DevOps Services](/vsts/git/auth-overview?view=vsts).
   
1. Depois que a implantação for concluída, exiba o progresso da compilação em `https://<azure_devops_account>.visualstudio.com/<project_name>/_build`e o progresso da implantação em `https://<azure_devops_account>.visualstudio.com/<project_name>/_release`.
   
1. Navegue até seu aplicativo no portal do Azure para verificar se o conteúdo está implantado.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-deployment"></a>Solucionar problemas de implantação

Você poderá ver as seguintes mensagens de erro comuns ao usar o Git para publicar em um aplicativo do serviço de aplicativo no Azure:

|Mensagem|Causa|Resolução
---|---|---|
|`Unable to access '[siteURL]': Failed to connect to [scmAddress]`|O aplicativo não está em funcionamento.|Inicie o aplicativo no portal do Azure. A implantação do git não está disponível quando o aplicativo Web é interrompido.|
|`Couldn't resolve host 'hostname'`|As informações de endereço para o remoto ' Azure ' estão incorretas.|Use o comando `git remote -v` para listar todas as remotas, juntamente com a URL associada. Verifique se a URL para o remoto ' Azure ' está correta. Se necessário, remova e recrie esse controle remoto usando a URL correta.|
|`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`|Você não especificou uma ramificação durante `git push`ou não definiu o valor de `push.default` no `.gitconfig`.|Execute `git push` novamente, especificando o Branch mestre: `git push azure master`.|
|`src refspec [branchname] does not match any.`|Você tentou enviar por push para um Branch que não seja o mestre no remoto ' Azure '.|Execute `git push` novamente, especificando o Branch mestre: `git push azure master`.|
|`RPC failed; result=22, HTTP code = 5xx.`|Esse erro pode ocorrer se você tentar enviar por push um repositório git grande por HTTPS.|Altere a configuração do git no computador local para tornar o `postBuffer` maior. Por exemplo: `git config --global http.postBuffer 524288000`.|
|`Error - Changes committed to remote repository but your web app not updated.`|Você implantou um aplicativo node. js com um arquivo _Package. JSON_ que especifica módulos adicionais necessários.|Examine as `npm ERR!` mensagens de erro antes desse erro para obter mais contexto sobre a falha. Veja a seguir as causas conhecidas desse erro e as mensagens de `npm ERR!` correspondentes:<br /><br />**Arquivo Package. JSON malformado**: `npm ERR! Couldn't read dependencies.`<br /><br />**O módulo nativo não tem uma distribuição binária para o Windows**:<br />`npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1` <br />ou <br />`npm ERR! [modulename@version] preinstall: \make || gmake\ `|

## <a name="additional-resources"></a>Recursos adicionais

- [Documentação do Project kudu](https://github.com/projectkudu/kudu/wiki)
- [Implantação contínua no serviço Azure App](deploy-continuous-deployment.md)
- [Exemplo: criar um aplicativo Web e implantar o código de um repositório git local (CLI do Azure)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
- [Exemplo: criar um aplicativo Web e implantar o código de um repositório git local (PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
