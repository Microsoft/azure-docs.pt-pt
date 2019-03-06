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
ms.date: 06/05/2018
ms.author: dariagrigoriu;cephalin
ms.custom: seodec18
ms.openlocfilehash: b879036dcd79901cb634fa197932e833cb22d12a
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57405033"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Implementação de Git Local no Serviço de Aplicações do Azure

Este guia de procedimentos mostra como implementar o código para [App Service do Azure](overview.md) partir de um repositório de Git no seu computador local.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para seguir os passos neste guia de procedimentos:

* [Instalar o Git](https://www.git-scm.com/downloads).
* Manter um repositório de Git local com o código de que pretende implementar.

Para utilizar um repositório de exemplos para acompanhar, execute o seguinte comando na janela de terminal local:

```bash
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-with-kudu-builds"></a>Implementar com compilações de Kudu

A maneira mais fácil para ativar a implementação de Git local para a sua aplicação com o servidor de compilação Kudu é utilizar o Cloud Shell.

### <a name="configure-a-deployment-user"></a>Configurar um utilizador de implementação

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Permitem que o Git local com Kudu

Para ativar a implementação de Git local para a sua aplicação com o servidor de compilação Kudu, execute [ `az webapp deployment source config-local-git` ](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) no Cloud Shell.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Para criar uma aplicação com o Git em vez disso, execute [ `az webapp create` ](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) no Cloud Shell com o `--deployment-local-git` parâmetro.

```azurecli-interactive
az webapp create --name <app_name> --resource-group <group_name> --plan <plan_name> --deployment-local-git
```

O `az webapp create` comando deve dar a algo semelhante à seguinte saída:

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="deploy-your-project"></a>Implementar o seu projeto

Regresse à _janela de terminal local_ e adicione um remoto do Azure ao seu repositório Git local. Substitua  _\<url >_ com o URL do Git remoto que recebeu do [ativar o Git para a sua aplicação](#enable-local-git-with-kudu).

```bash
git remote add azure <url>
```

Envie para o remoto do Azure para implementar a sua aplicação com o comando seguinte. Quando lhe for pedida uma palavra-passe, confirme que introduz a palavra-passe que criou em [Configurar um utilizador de implementação](#configure-a-deployment-user) e não a que utilizou para iniciar sessão no portal do Azure.

```bash
git push azure master
```

Poderá ver a automatização de tempo de execução específica na saída, como o MSBuild para o ASP.NET, `npm install` para node. js, e `pip install` para Python. 

Navegue para a sua aplicação para verificar se o conteúdo está implementado.

## <a name="deploy-with-azure-devops-builds"></a>Implementar com compilações de DevOps do Azure

> [!NOTE]
> Para o serviço de aplicações para criar Pipelines do Azure necessários na sua organização de serviços do Azure DevOps, sua conta do Azure tem de ter a função de **proprietário** na sua subscrição do Azure.
>

Para ativar a implementação de Git local para a sua aplicação com o servidor de compilação Kudu, navegue até à sua aplicação no [portal do Azure](https://portal.azure.com).

No painel de navegação esquerdo da página da aplicação, clique em **Deployment Center** > **Local Git** > **continuar**.

![](media/app-service-deploy-local-git/portal-enable.png)

Clique em **Pipelines do Azure (pré-visualização)** > **continuar**.

![](media/app-service-deploy-local-git/pipeline-builds.png)

Na **configurar** página, configurar uma nova organização do Azure DevOps ou especificar uma organização existente. Quando terminar, clique em **continuar**.

> [!NOTE]
> Se pretender utilizar uma organização de DevOps do Azure existente que não esteja listada, terá [associar a organização de serviços de DevOps do Azure à sua subscrição do Azure](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).

Consoante a [escalão de preço](https://azure.microsoft.com/pricing/details/app-service/plans/) do seu plano do serviço de aplicações, também poderá ver um **implementar na transição** página. Escolha se pretende ativar ranhuras de implementação, em seguida, clique em **continuar**.

Na **resumo** página, verifique as suas opções e clique em **concluir**.

Demora alguns minutos para a organização de serviços de DevOps do Azure esteja pronto. Quando estiver pronto, copie o URL do repositório de Git no Centro de implementação.

![](media/app-service-deploy-local-git/vsts-repo-ready.png)

Regresse à _janela de terminal local_ e adicione um remoto do Azure ao seu repositório Git local. Substitua  _\<url >_ com o URL obtido em último passo.

```bash
git remote add vsts <url>
```

Envie para o remoto do Azure para implementar a sua aplicação com o comando seguinte. Quando lhe for pedido pelo Gestor de credenciais do Git, inicie sessão com o seu utilizador visualstudio.com. Para métodos de autenticação adicionais, consulte [descrição geral da autenticação de serviços do Azure DevOps](/vsts/git/auth-overview?view=vsts).

```bash
git push vsts master
```

Assim que a implementação estiver concluída, pode encontrar o progresso da compilação em `https://<vsts_account>.visualstudio.com/<project_name>/_build` e o progresso da implementação em `https://<vsts_account>.visualstudio.com/<project_name>/_release`.

Navegue para a sua aplicação para verificar se o conteúdo está implementado.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshooting-kudu-deployment"></a>Resolução de problemas de implementação do Kudu

Seguem-se erros ou problemas comuns com o Git para publicar uma aplicação de serviço de aplicações no Azure:

---
**Sintoma**: `Unable to access '[siteURL]': Failed to connect to [scmAddress]`

**Motivo**: Este erro pode acontecer se a aplicação não estiver em execução.

**Resolução**: Inicie a aplicação no portal do Azure. Implementação de Git não está disponível quando a aplicação Web está parada.

---
**Sintoma**: `Couldn't resolve host 'hostname'`

**Motivo**: Este erro pode acontecer se as informações de endereço que introduziu durante a criação de "azure" remoto estavam incorretas.

**Resolução**: Utilize o `git remote -v` command para listar todos os remotas, juntamente com o URL associado. Certifique-se de que o URL para o "azure" remoto está correto. Se for necessário, remova e recrie esta remoto utilizando o URL correto.

---
**Sintoma**: `No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`

**Motivo**: Este erro pode acontecer se não especificar um ramo durante `git push`, ou se não tiver definido a `push.default` valor em `.gitconfig`.

**Resolução**: Executar `git push` novamente, especificando o ramo principal. Por exemplo:

```bash
git push azure master
```

---
**Sintoma**: `src refspec [branchname] does not match any.`

**Motivo**: Este erro pode ocorrer se tentar push para um ramo que não seja o mestre no "azure" remoto.

**Resolução**: Executar `git push` novamente, especificando o ramo principal. Por exemplo:

```bash
git push azure master
```

---
**Sintoma**: `RPC failed; result=22, HTTP code = 5xx.`

**Motivo**: Este erro pode ocorrer se tentar enviar um repositório de git de grandes dimensões através de HTTPS.

**Resolução**: Alterar a configuração do git no computador local para tornar o postBuffer maiores

```bash
git config --global http.postBuffer 524288000
```

---
**Sintoma**: `Error - Changes committed to remote repository but your web app not updated.`

**Motivo**: Este erro pode acontecer se implementar uma aplicação node. js com um _Package. JSON_ ficheiro que especifica os módulos necessários adicionais.

**Resolução**: Mensagens adicionais com "npm ERR!" deve ter sessão iniciada antes deste erro e pode fornecer contexto adicional sobre a falha. Os seguintes são conhecidos causas de erro e o correspondente 'npm ERR!" mensagem:

* **Ficheiro Package. JSON com formato incorreto**: npm ERR! Não foi possível ler as dependências.
* **Módulo nativo que não tem uma distribuição binária para Windows**:

  * `npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1`

      OU
  * `npm ERR! [modulename@version] preinstall: \make || gmake\`

## <a name="additional-resources"></a>Recursos Adicionais

* [Documentação de Kudu do projeto](https://github.com/projectkudu/kudu/wiki)
* [Implementação contínua no serviço de aplicações do Azure](deploy-continuous-deployment.md)
* [Exemplo: Criar aplicação Web e implementar código a partir de um repositório de Git local (CLI do Azure)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
* [Exemplo: Criar aplicação Web e implementar código a partir de um repositório de Git local (PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
