---
title: Criar um Node.js no Serviço de Aplicações do Azure no Linux | Microsoft Docs
description: Implemente em minutos o seu primeiro Node.js Hello World no Serviço de Aplicações do Azure no Linux.
services: app-service\web
documentationcenter: ''
author: msangapu
manager: cfowler
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 06/07/2017
ms.author: msangapu
ms.custom: mvc
ms.openlocfilehash: 44c3f8ce05854e993ad551a025eec447d882c326
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38969549"
---
# <a name="create-a-nodejs-web-app-in-azure-app-service-on-linux"></a>Criar uma aplicação Web Node.js no Serviço de Aplicações do Azure no Linux

> [!NOTE]
> Este artigo implementa uma aplicação para o Serviço de Aplicações no Linux. Para implementar um Serviço de Aplicações no _Windows_, consulte [Criar uma aplicação Web Node.js no Azure](../app-service-web-get-started-nodejs.md).
>

O [Serviço de Aplicações no Linux](app-service-linux-intro.md) oferece um serviço de alojamento na Web altamente dimensionável e com correção automática através do sistema operativo Linux. Este guia início rápido mostra como implementar uma aplicação Node.js no Serviço de Aplicações no Linux com o [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).

Este início rápido deverá ser concluído no Cloud Shell, mas também poderá executar estes comandos localmente com a [CLI do Azure](/cli/azure/install-azure-cli).

![Aplicação de exemplo em execução no Azure](media/quickstart-nodejs/hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="install-web-app-extension-for-cloud-shell"></a>Instalar a extensão da aplicação Web para o Cloud Shell

Para concluir este início rápido, terá de adicionar a [extensão da aplicação Web az](https://docs.microsoft.com/cli/azure/extension?view=azure-cli-latest#az-extension-add). Se a extensão já estiver instalada, deverá atualizá-la para a versão mais recente. Para atualizar a extensão da aplicação Web, introduza `az extension update -n webapp`.

Para instalar a extensão da aplicação Web, execute o seguinte comando:

```bash
az extension add -n webapp
```

Quando a extensão estiver instalada, o Cloud Shell mostra as seguintes informações:

```bash
The installed extension 'webapp' is in preview.
```

## <a name="download-the-sample"></a>Transferir o exemplo

No Cloud Shell, crie um diretório de início rápido e, em seguida, altere-o.

```bash
mkdir quickstart

cd quickstart
```

Em seguida, execute o seguinte comando para clonar o repositório da aplicação de exemplo para o seu diretório de início rápido.

```bash
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
```

Ao executar, apresenta informações semelhantes ao exemplo seguinte:

```bash
Cloning into 'nodejs-docs-hello-world'...
remote: Counting objects: 40, done.
remote: Total 40 (delta 0), reused 0 (delta 0), pack-reused 40
Unpacking objects: 100% (40/40), done.
Checking connectivity... done.
````

## <a name="create-a-web-app"></a>Criar uma aplicação Web

Mude para o diretório que contém o código de exemplo e execute o comando `az webapp up`.

No comando a seguir, substitua <nome_aplicação> por um nome de aplicação exclusivo.

```bash
cd nodejs-docs-hello-world

az webapp up -n <app_name>
```

Este comando pode demorar alguns minutos a ser executado. Ao executar, apresenta informações semelhantes ao exemplo seguinte:

```json
Creating Resource group 'appsvc_rg_Linux_CentralUS' ...
Resource group creation complete
Creating App service plan 'appsvc_asp_Linux_CentralUS' ...
App service plan creation complete
Creating app '<app_name>' ....
Webapp creation complete
Updating app settings to enable build after deployment
Creating zip with contents of dir /home/username/quickstart/nodejs-docs-hello-world ...
Preparing to deploy and build contents to app.
Fetching changes.

Generating deployment script.
Generating deployment script.
Generating deployment script.
Running deployment command...
Running deployment command...
Running deployment command...
Deployment successful.
All done.
{
  "app_url": "https://<app_name>.azurewebsites.net",
  "location": "Central US",
  "name": "<app_name>",
  "os": "Linux",
  "resourcegroup": "appsvc_rg_Linux_CentralUS ",
  "serverfarm": "appsvc_asp_Linux_CentralUS",
  "sku": "STANDARD",
  "src_path": "/home/username/quickstart/nodejs-docs-hello-world ",
  "version_detected": "6.9",
  "version_to_create": "node|6.9"
}
```

O comando `az webapp up` executa as seguintes ações:

- Cria um grupo de recursos predefinido.

- Cria um plano do serviço de aplicações predefinido.

- Cria uma aplicação com o nome especificado.

- [Implementa](https://docs.microsoft.com/azure/app-service/app-service-deploy-zip) os ficheiros zip do diretório atual de trabalho para a aplicação Web.

## <a name="browse-to-the-app"></a>Navegar para a aplicação

Utilize o browser para navegar para a aplicação implementada. Substitua <nome_aplicação> pelo nome da sua aplicação Web.

```bash
http://<app_name>.azurewebsites.net
```

O código Node.js de exemplo está em execução numa aplicação Web com imagem incorporada.

![Aplicação de exemplo em execução no Azure](media/quickstart-nodejs/hello-world-in-browser.png)

**Parabéns!** Implementou a sua primeira aplicação Node.js no Serviço de Aplicações no Linux.

## <a name="update-and-redeploy-the-code"></a>Atualizar e voltar a implementar o código

No Cloud Shell, escreva `nano index.js` para abrir o editor de texto nano.

![Nano index.js](media/quickstart-nodejs/nano-indexjs.png)

 Faça uma pequena alteração no texto da chamada de `response.end`:

```nodejs
response.end("Hello Azure!");
```

Guarde as alterações e feche o nano. Utilize o comando `^O` para guardar e `^X` para sair.

Agora vai reimplementar a aplicação. Substitua `<app_name>` pela sua aplicação Web.

```bash
az webapp up -n <app_name>
```

Depois de concluída a implementação, volte para a janela do browser aberta que abriu no passo **Navegar para a aplicação** e atualize a página.

![Aplicação de exemplo atualizada em execução no Azure](media/quickstart-nodejs/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-web-app"></a>Gerir a sua nova aplicação Web do Azure

Aceda ao <a href="https://portal.azure.com" target="_blank">portal do Azure</a> para gerir a aplicação Web que criou.

No menu à esquerda, clique em **Serviços de Aplicações** e clique no nome da sua aplicação Web do Azure.

![Navegação no portal para a aplicação Web do Azure](./media/quickstart-nodejs/nodejs-docs-hello-world-app-service-list.png)

É apresentada a página de descrição geral da sua aplicação Web. Aqui, pode concluir tarefas de gestão básicas, como navegar, parar, iniciar, reiniciar e eliminar.

![Página Serviço de Aplicações no portal do Azure](media/quickstart-nodejs/nodejs-docs-hello-world-app-service-detail.png)

O menu à esquerda fornece diferentes páginas para configurar a sua aplicação.

## <a name="clean-up-resources"></a>Limpar recursos

Nos passos anteriores, criou os recursos do Azure num grupo de recursos. Se achar que não vai precisar desses recursos no futuro, poderá eliminar o grupo de recursos do Cloud Shell. Se tiver modificado a região, atualize o nome do grupo de recursos `appsvc_rg_Linux_CentralUS` para o grupo de recursos específico da sua aplicação.

```azurecli-interactive
az group delete --name appsvc_rg_Linux_CentralUS
```

Este comando pode demorar alguns minutos a ser executado.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Node.js com MongoDB](tutorial-nodejs-mongodb-app.md)
