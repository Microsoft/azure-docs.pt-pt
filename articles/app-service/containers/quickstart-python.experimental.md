---
title: Criar aplicação Python no Linux - serviço de aplicações do Azure | Documentos da Microsoft
description: Implemente em minutos a sua primeira aplicação Hello World Python no Serviço de Aplicações do Azure no Linux.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 02/14/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: d0c51f5d4757c35319cc3f80d09c9fb1a0e1cf69
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/15/2019
ms.locfileid: "56314152"
---
# <a name="create-a-python-app-in-azure-app-service-on-linux-preview"></a>Criar uma aplicação Python no serviço de aplicações do Azure no Linux (pré-visualização)

O [Serviço de Aplicações no Linux](app-service-linux-intro.md) oferece um serviço de alojamento na Web altamente dimensionável e com correção automática através do sistema operativo Linux. Este início rápido mostra como implementar uma aplicação Python a parte superior da imagem de Python (pré-visualização) incorporada no serviço de aplicações no Linux com o [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).

Pode seguir os passos deste artigo num computador Mac, Windows ou Linux.

![Aplicação de exemplo em execução no Azure](media/quickstart-python/hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>Transferir o exemplo

No Cloud Shell, crie um diretório de início rápido e, em seguida, altere-o.

```bash
mkdir quickstart

cd quickstart
```

Em seguida, execute o seguinte comando para clonar o repositório da aplicação de exemplo para o seu diretório de início rápido.

```bash
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

Ao executar, apresenta informações semelhantes ao exemplo seguinte:

```bash
Cloning into 'python-docs-hello-world'...
remote: Enumerating objects: 43, done.
remote: Total 43 (delta 0), reused 0 (delta 0), pack-reused 43
Unpacking objects: 100% (43/43), done.
Checking connectivity... done.
```

## <a name="create-a-web-app"></a>Criar uma aplicação Web

Mude para o diretório que contém o código de exemplo e execute o comando `az webapp up`.

No comando a seguir, substitua <nome_aplicação> por um nome de aplicação exclusivo.

```bash
cd python-docs-hello-world

az webapp up -n <app_name>
```

Este comando pode demorar alguns minutos a ser executado. Ao executar, apresenta informações semelhantes ao exemplo seguinte:

```json
The behavior of this command has been altered by the following extension: webapp
Creating Resource group 'appsvc_rg_Linux_CentralUS' ...
Resource group creation complete
Creating App service plan 'appsvc_asp_Linux_CentralUS' ...
App service plan creation complete
Creating app '<app_name>' ....
Webapp creation complete
Creating zip with contents of dir /home/username/quickstart/python-docs-hello-world ...
Preparing to deploy contents to app.
All done.
{
  "app_url": "https:/<app_name>.azurewebsites.net",
  "location": "Central US",
  "name": "<app_name>",
  "os": "Linux",
  "resourcegroup": "appsvc_rg_Linux_CentralUS ",
  "serverfarm": "appsvc_asp_Linux_CentralUS",
  "sku": "BASIC",
  "src_path": "/home/username/quickstart/python-docs-hello-world ",
  "version_detected": "-",
  "version_to_create": "python|3.7"
}
```

O comando `az webapp up` executa as seguintes ações:

- Cria um grupo de recursos predefinido.

- Cria um plano do serviço de aplicações predefinido.

- Cria uma aplicação com o nome especificado.

- [Implementar Zip](https://docs.microsoft.com/azure/app-service/deploy-zip) ficheiros do diretório de trabalho atual para a aplicação.

## <a name="browse-to-the-app"></a>Navegar para a aplicação

Utilize o browser para navegar para a aplicação implementada.

```bash
http://<app_name>.azurewebsites.net
```

O código de exemplo do Python está a executar no serviço de aplicações no Linux com uma imagem incorporada.

![Aplicação de exemplo em execução no Azure](media/quickstart-python/hello-world-in-browser.png)

**Parabéns!** Implementou a sua primeira aplicação Python no Serviço de Aplicações no Linux.

## <a name="update-locally-and-redeploy-the-code"></a>Atualizar localmente e reimplementar o código

No Cloud Shell, escreva `code application.py` para abrir o editor do Cloud Shell.

![Application.py de código](media/quickstart-python/code-applicationpy.png)

 Faça uma pequena alteração no texto da chamada de `return`:

```python
return "Hello Azure!"
```

Guardar as alterações e sair do editor. Utilize o comando `^S` para guardar e `^Q` para sair.

Agora vai reimplementar a aplicação. Substitute `<app_name>` com a sua aplicação.

```bash
az webapp up -n <app_name>
```

Depois de concluída a implementação, volte para a janela do browser aberta que abriu no passo **Navegar para a aplicação** e atualize a página.

![Aplicação de exemplo atualizada em execução no Azure](media/quickstart-python/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-app"></a>Gerir a sua nova aplicação do Azure

Vá para o <a href="https://portal.azure.com" target="_blank">portal do Azure</a> para gerir a aplicação que criou.

No menu à esquerda, clique em **dos serviços de aplicações**e, em seguida, clique no nome da sua aplicação do Azure.

![Navegação do portal para a aplicação do Azure](./media/quickstart-python/app-service-list.png)

Ver página de descrição geral da sua aplicação. Aqui, pode realizar tarefas de gestão básicas, como navegar, parar, iniciar, reiniciar e eliminar.

![Página Serviço de Aplicações no portal do Azure](media/quickstart-python/app-service-detail.png)

O menu à esquerda fornece diferentes páginas para configurar a sua aplicação. 

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Passos Seguintes

A imagem do Python incorporada no Serviço de Aplicações do Linux encontra-se em pré-visualização, sendo que pode personalizar o comando que serve para iniciar a sua aplicação. Em alternativa, também pode criar aplicações Python de produção com um contentor personalizado.

> [!div class="nextstepaction"]
> [Python com PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Configurar um comando de arranque personalizado](how-to-configure-python.md#customize-startup-command)

> [!div class="nextstepaction"]
> [Resolução de problemas](how-to-configure-python.md#troubleshooting)

> [!div class="nextstepaction"]
> [Utilizar imagens personalizadas](tutorial-custom-docker-image.md)
