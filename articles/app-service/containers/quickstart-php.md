---
title: Criar aplicativo PHP no serviço Linux-Azure App | Microsoft Docs
description: Implemente em minutos a sua primeira aplicação PHP Hello World no Serviço de Aplicações no Linux.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: 6feac128-c728-4491-8b79-962da9a40788
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 7976dcafe8bb3f787465e72b6a61bbc48daf4086
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849554"
---
# <a name="create-a-php-app-in-app-service-on-linux"></a>Criar um aplicativo PHP no serviço de aplicativo no Linux

> [!NOTE]
> Este artigo implementa uma aplicação para o Serviço de Aplicações no Linux. Para implantar o serviço de aplicativo no _Windows_, consulte [criar um aplicativo PHP no Azure](../app-service-web-get-started-php.md).
>

O [Serviço de Aplicações no Linux](app-service-linux-intro.md) oferece um serviço de alojamento na Web altamente dimensionável e com correção automática através do sistema operativo Linux. Este tutorial de início rápido mostra como implantar um aplicativo PHP no serviço Azure App no Linux usando o [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).

![Aplicação de exemplo em execução no Azure](media/quickstart-php/hello-world-in-browser.png)

Pode seguir os passos deste artigo num computador Mac, Windows ou Linux.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido:

* <a href="https://git-scm.com/" target="_blank">Instale o Git</a>.
* <a href="https://php.net" target="_blank">Instalar o PHP</a>

## <a name="download-the-sample"></a>Transferir o exemplo

Numa janela do terminal, execute os seguintes comandos para clonar o exemplo de aplicação no seu computador local e é navegue para o diretório que contém o código de exemplo.

```bash
git clone https://github.com/Azure-Samples/php-docs-hello-world
cd php-docs-hello-world
```

## <a name="run-the-app-locally"></a>Executar a aplicação localmente

Execute a aplicação localmente, para ver que aspeto deveria ter quando a implemente no Azure. Abra uma janela do terminal e use o comando `php` para iniciar o servidor Web PHP incorporado.

```bash
php -S localhost:8080
```

Abra um browser e navegue para a aplicação de exemplo em `http://localhost:8080`.

Pode ver a mensagem **Olá, mundo!** da aplicação de exemplo apresentada na página.

![Aplicação de exemplo em execução localmente](media/quickstart-php/localhost-hello-world-in-browser.png)

Na janela do terminal, prima **Ctrl+C** para desligar o servidor Web.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Criar uma aplicação Web

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-php-linux-no-h.md)] 

Navegue até o site para ver seu aplicativo criado recentemente com a imagem interna. Substitua o nome do  _aplicativo>pelonomedoaplicativo&lt;_ .

```bash
http://<app_name>.azurewebsites.net
```

Veja como deve ser o seu novo aplicativo:

![Página de aplicativo vazia](media/quickstart-php/app-service-web-service-created.png)

[!INCLUDE [Push to Azure](../../../includes/app-service-web-git-push-to-azure.md)] 

```bash
Counting objects: 2, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (2/2), 352 bytes | 0 bytes/s, done.
Total 2 (delta 1), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '25f18051e9'.
remote: Generating deployment script.
remote: Running deployment command...
remote: Handling Basic Web Site deployment.
remote: Kudu sync from: '/home/site/repository' to: '/home/site/wwwroot'
remote: Copying file: '.gitignore'
remote: Copying file: 'LICENSE'
remote: Copying file: 'README.md'
remote: Copying file: 'index.php'
remote: Ignoring: .git
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
   cc39b1e..25f1805  master -> master
```

## <a name="browse-to-the-app"></a>Navegar para a aplicação

Utilize o browser para navegar para a aplicação implementada.

```bash
http://<app_name>.azurewebsites.net
```

O código de exemplo do PHP está em execução no serviço de aplicativo no Linux com imagem interna.

![Aplicação de exemplo em execução no Azure](media/quickstart-php/hello-world-in-browser.png)

**Parabéns!** Implementou a sua primeira aplicação PHP no Serviço de Aplicações no Linux.

## <a name="update-locally-and-redeploy-the-code"></a>Atualizar localmente e reimplementar o código

No diretório local, abra o ficheiro `index.php` na aplicação PHP e faça uma pequena alteração ao texto na cadeia junto a `echo`:

```php
echo "Hello Azure!";
```

Consolide as suas alterações no Git e envie as alterações ao código para o Azure.

```bash
git commit -am "updated output"
git push azure master
```

Depois de concluída a implementação, volte para a janela do browser aberta que abriu no passo **Navegar para a aplicação** e atualize a página.

![Aplicação de exemplo atualizada em execução no Azure](media/quickstart-php/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-app"></a>Gerenciar seu novo aplicativo do Azure

Vá para o <a href="https://portal.azure.com" target="_blank">portal do Azure</a> para gerenciar o aplicativo que você criou.

No menu à esquerda, clique em **serviços de aplicativos**e, em seguida, clique no nome do seu aplicativo do Azure.

![Navegação do portal para a aplicação do Azure](./media/quickstart-php/php-docs-hello-world-app-service-list.png)

Você vê a página de visão geral do seu aplicativo. Aqui, pode realizar tarefas de gestão básicas, como navegar, parar, iniciar, reiniciar e eliminar.

![Página Serviço de Aplicações no portal do Azure](media/quickstart-php/php-docs-hello-world-app-service-detail.png)

O menu à esquerda fornece diferentes páginas para configurar a sua aplicação. 

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Tutorial: Aplicativo PHP com MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [Configurar aplicativo PHP](configure-language-php.md)
