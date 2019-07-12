---
title: Implemente a partir do repositório de Git local - serviço de aplicações do Azure
description: Saiba como ativar a implementação de Git local para o serviço de aplicações do Azure.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2019
ms.author: dariagrigoriu;cephalin
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: fa961e43408fed014be2266c14c7972d39435b97
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836947"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Implementação de Git local para o serviço de aplicações do Azure

Este guia de procedimentos mostra como implementar a sua aplicação para [App Service do Azure](overview.md) partir de um repositório de Git no seu computador local.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir os passos neste guia de procedimentos:

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [Instalar o Git](https://www.git-scm.com/downloads).
  
- Ter um repositório de Git local com o código de que pretende implementar. Para transferir um repositório de exemplos, execute o seguinte comando na janela de terminal local:
  
  ```bash
  git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
  ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-with-kudu-build-server"></a>Implementar com o servidor de compilação Kudu

É a maneira mais fácil para ativar a implementação de Git local para a sua aplicação com o servidor de compilação do serviço de aplicações do Kudu utilizar o Azure Cloud Shell. 

### <a name="configure-a-deployment-user"></a>Configurar um utilizador de implementação

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="get-the-deployment-url"></a>Obter o URL de implementação

Para obter o URL para ativar a implementação de Git local para uma aplicação existente, execute [ `az webapp deployment source config-local-git` ](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) no Cloud Shell. Substitua \<nome da aplicação > e \<nome do grupo > com os nomes da sua aplicação e o respetivo grupo de recursos do Azure.

```azurecli-interactive
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```

Ou, para criar uma nova aplicação de acesso ativado de Git, execute [ `az webapp create` ](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) no Cloud Shell com o `--deployment-local-git` parâmetro. Substitua \<nome da aplicação >, \<nome do grupo >, e \<plano-name > com os nomes para a nova aplicação de Git, o respetivo grupo de recursos do Azure e o seu plano do serviço de aplicações do Azure.

```azurecli-interactive
az webapp create --name <app-name> --resource-group <group-name> --plan <plan-name> --deployment-local-git
```

De qualquer comando retorna uma URL, como: `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git`. Utilize este URL para implementar a sua aplicação no próximo passo.

Em vez de utilizar este URL ao nível da conta, pode também ativar local Git utilizando as credenciais ao nível da aplicação. Serviço de aplicações do Azure gera automaticamente estas credenciais para cada aplicação. 

Obter as credenciais de aplicação ao executar o seguinte comando no Cloud Shell. Substitua \<nome da aplicação > e \<nome do grupo > com o nome da sua aplicação e o nome do grupo de recursos do Azure.

```azurecli-interactive
az webapp deployment list-publishing-credentials --name <app-name> --resource-group <group-name> --query scmUri --output tsv
```

Utilize o URL que devolve ao implementar a sua aplicação no próximo passo.

### <a name="deploy-the-web-app"></a>Implementar a aplicação web

1. Abra uma janela de terminal local para o seu repositório de Git local e adicione um remoto do Azure. No comando seguinte, substitua \<url > com o URL de utilizadores específicos de implementação ou um URL específico de aplicação que obteve no passo anterior.
   
   ```bash
   git remote add azure <url>
   ```
   
1. Envie para o Azure remoto com `git push azure master`. 
   
1. No **Git Credential Manager** janela, introduza o seu [palavra-passe de utilizador de implementação](#configure-a-deployment-user), não sua do Azure início de sessão palavra-passe.
   
1. Reveja a saída. Pode ver automatização específico de tempo de execução, como o MSBuild para o ASP.NET, `npm install` para node. js, e `pip install` para Python. 
   
1. Navegue para a sua aplicação no portal do Azure para verificar que o conteúdo é implementado.

## <a name="deploy-with-azure-pipelines-builds"></a>Implementar com compilações de Pipelines do Azure

Se a sua conta tiver as permissões necessárias, pode configurar Pipelines do Azure (pré-visualização) para ativar a implementação de Git local para a sua aplicação. 

- Sua conta do Azure tem de ter permissões para escrever para o Azure Active Directory e criar um serviço. 
  
- Sua conta do Azure tem de ter o **proprietário** função na sua subscrição do Azure.

- Tem de ser um administrador do projeto de DevOps do Azure que pretende utilizar.

Para ativar a implementação de Git local para a sua aplicação com Pipelines do Azure (pré-visualização):

1. Navegue até à página da aplicação do serviço de aplicações do Azure na [portal do Azure](https://portal.azure.com)e selecione **Deployment Center** no menu à esquerda.
   
1. Sobre o **Deployment Center** página, selecione **Local Git**e, em seguida, selecione **continuar**. 
   
   ![Selecione o Local Git e, em seguida, selecionar continuar](media/app-service-deploy-local-git/portal-enable.png)
   
1. Sobre o **fornecedor de compilação** página, selecione **Pipelines do Azure (pré-visualização)** e, em seguida, selecione **continuar**. 
   
   ![Selecione Pipelines do Azure (pré-visualização) e, em seguida, selecionar continuar.](media/app-service-deploy-local-git/pipeline-builds.png)

1. Sobre o **configurar** página, configurar uma nova organização do Azure DevOps, ou especificar uma organização existente e, em seguida, selecione **continuar**.
   
   > [!NOTE]
   > Se não estiver listada a sua organização de DevOps do Azure existente, terá de ligá-lo à sua subscrição do Azure. Para obter mais informações, consulte [definir o seu pipeline de lançamento do CD](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd).
   
1. Dependendo do seu plano do serviço de aplicações [escalão de preço](https://azure.microsoft.com/pricing/details/app-service/plans/), poderá ver um **implementar na transição** página. Escolha se pretende [ativar ranhuras de implementação](deploy-staging-slots.md)e, em seguida, selecione **continuar**.
   
1. Sobre o **resumo** página, reveja as definições e, em seguida, selecione **concluir**.
   
1. Quando o Pipeline do Azure estiver pronto, copie o URL do repositório de Git do **Deployment Center** página para utilizar no próximo passo. 
   
   ![Copie o URL do repositório de Git](media/app-service-deploy-local-git/vsts-repo-ready.png)

1. Na janela de terminal local, adicione um Azure remoto para o seu repositório de Git local. No comando, substitua \<url > com o URL do repositório Git que obteve no passo anterior.
   
   ```bash
   git remote add azure <url>
   ```
   
1. Envie para o Azure remoto com `git push azure master`. 
   
1. Sobre o **Git Credential Manager** página, inicie sessão com o nome de utilizador do visualstudio.com. Para outros métodos de autenticação, consulte [descrição geral da autenticação de serviços do Azure DevOps](/vsts/git/auth-overview?view=vsts).
   
1. Após a conclusão da implementação, ver o progresso da compilação em `https://<azure_devops_account>.visualstudio.com/<project_name>/_build`e o progresso da implementação em `https://<azure_devops_account>.visualstudio.com/<project_name>/_release`.
   
1. Navegue para a sua aplicação no portal do Azure para verificar que o conteúdo é implementado.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-deployment"></a>Resolver problemas de implementação

Poderá ver as seguintes mensagens de erro comuns ao utilizar o Git para publicar uma aplicação de serviço de aplicações no Azure:

|Message|Causa|Resolução
---|---|---|
|`Unable to access '[siteURL]': Failed to connect to [scmAddress]`|A aplicação não estiver em execução.|Inicie a aplicação no portal do Azure. Implementação de Git não está disponível quando a aplicação web está parada.|
|`Couldn't resolve host 'hostname'`|As informações de endereço para "azure" remoto estão incorretas.|Utilize o `git remote -v` command para listar todos os remotas, juntamente com o URL associado. Certifique-se de que o URL para o "azure" remoto está correto. Se for necessário, remova e recrie esta remoto utilizando o URL correto.|
|`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`|Não especificou um ramo durante `git push`, ou se não tiver definido a `push.default` valor em `.gitconfig`.|Execute `git push` novamente, especificando o ramo principal: `git push azure master`.|
|`src refspec [branchname] does not match any.`|Tentou enviar para um ramo que não seja o mestre no "azure" remoto.|Execute `git push` novamente, especificando o ramo principal: `git push azure master`.|
|`RPC failed; result=22, HTTP code = 5xx.`|Este erro pode ocorrer se tentar enviar um repositório de git de grandes dimensões através de HTTPS.|Alterar a configuração do git no computador local para tornar o `postBuffer` maiores. Por exemplo: `git config --global http.postBuffer 524288000`.|
|`Error - Changes committed to remote repository but your web app not updated.`|Implementou uma aplicação node. js com um _Package. JSON_ ficheiro que especifica os módulos necessários adicionais.|Reveja o `npm ERR!` mensagens de erro antes deste erro para obter mais contexto sobre a falha. Seguem-se as causas conhecidas deste erro e o correspondente `npm ERR!` mensagens:<br /><br />**Ficheiro Package. JSON com formato incorreto**: `npm ERR! Couldn't read dependencies.`<br /><br />**Módulo nativo não tem uma distribuição binária para Windows**:<br />`npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1` <br />ou <br />"npm ERR! [modulename@version] preinstall: \make || gmake\`|

## <a name="additional-resources"></a>Recursos adicionais

- [Documentação de Kudu do projeto](https://github.com/projectkudu/kudu/wiki)
- [Implementação contínua no App Service do Azure](deploy-continuous-deployment.md)
- [Amostra: Criar uma aplicação web e implementar código a partir de um repositório de Git local (CLI do Azure)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
- [Amostra: Criar uma aplicação web e implementar código a partir de um repositório de Git local (PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
