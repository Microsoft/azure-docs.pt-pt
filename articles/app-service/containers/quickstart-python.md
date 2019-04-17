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
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 04f08965d161e35a9ae4423ad5d3cf80cb407b8a
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2019
ms.locfileid: "59607777"
---
# <a name="create-a-python-app-in-azure-app-service-on-linux-preview"></a>Criar uma aplicação Python no serviço de aplicações do Azure no Linux (pré-visualização)

Neste início rápido, vai implementar uma aplicação de Python simples no [serviço de aplicações no Linux](app-service-linux-intro.md), que fornece um serviço de alojamento na web altamente dimensionável e com correção automática. Utilizar a interface de linha de comandos do Azure (a [CLI do Azure](/cli/azure/install-azure-cli)) através do interativo, baseada no browser e do Azure Cloud Shell, pelo que pode seguir os passos utilizam um computador Mac, Linux ou Windows.

![Aplicação de exemplo em execução no Azure](media/quickstart-python/hello-world-in-browser.png)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido:

* <a href="https://www.python.org/downloads/" target="_blank">Instale o Python 3.7</a>
* <a href="https://git-scm.com/" target="_blank">Instalar o Git</a>
* Uma subscrição do Azure. Se ainda não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

## <a name="download-the-sample-locally"></a>Transferir o exemplo localmente

Numa janela de terminal, execute os seguintes comandos para clonar a aplicação de exemplo no seu computador local e navegue para o diretório com o código de exemplo.

```bash
git clone https://github.com/Azure-Samples/python-docs-hello-world
cd python-docs-hello-world
```

O repositório contém um *application.py*, que informa ao serviço de aplicações que o repositório contém uma aplicação Flask. Para obter mais informações, consulte [processo de inicialização de contentor e personalizações](how-to-configure-python.md).

## <a name="run-the-app-locally"></a>Executar a aplicação localmente

Execute a aplicação localmente, para ver que aspeto deveria ter quando a implemente no Azure. Abra uma janela de terminal e utilize os comandos abaixo para instalar as dependências necessárias e iniciar o servidor de desenvolvimento incorporado. 

```bash
# In Bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
FLASK_APP=application.py flask run

# In PowerShell
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
Set-Item Env:FLASK_APP ".\application.py"
flask run
```

Abra um browser e navegue para a aplicação de exemplo em `http://localhost:5000/`.

Pode ver a mensagem **Olá, mundo!** da aplicação de exemplo apresentada na página.

![Aplicação de exemplo em execução localmente](media/quickstart-python/hello-world-in-browser.png)

Na janela do terminal, prima **Ctrl+C** para desligar o servidor Web.

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

No exemplo seguinte, substitua `<app-name>` com um nome de aplicação único.

```bash
cd python-docs-hello-world

az webapp up -n <app-name>
```

Este comando pode demorar alguns minutos a ser executado. Ao executar, apresenta informações semelhantes ao exemplo seguinte:

```json
The behavior of this command has been altered by the following extension: webapp
Creating Resource group 'appsvc_rg_Linux_CentralUS' ...
Resource group creation complete
Creating App service plan 'appsvc_asp_Linux_CentralUS' ...
App service plan creation complete
Creating app '<app-name>' ....
Webapp creation complete
Creating zip with contents of dir /home/username/quickstart/python-docs-hello-world ...
Preparing to deploy contents to app.
All done.
{
  "app_url": "https:/<app-name>.azurewebsites.net",
  "location": "Central US",
  "name": "<app-name>",
  "os": "Linux",
  "resourcegroup": "appsvc_rg_Linux_CentralUS ",
  "serverfarm": "appsvc_asp_Linux_CentralUS",
  "sku": "BASIC",
  "src_path": "/home/username/quickstart/python-docs-hello-world ",
  "version_detected": "-",
  "version_to_create": "python|3.7"
}
```

[!INCLUDE [AZ Webapp Up Note](../../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>Navegar para a aplicação

Utilize o browser para navegar para a aplicação implementada.

```bash
http://<app-name>.azurewebsites.net
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

Voltar a implementar a aplicação com o [ `az webapp up` ](/cli/azure/ext/webapp/webapp?view=azure-cli-latest.md#ext-webapp-az-webapp-up) comando. Substitua o nome da sua aplicação para `<app-name>`e especificar uma localização para `<location-name>` (através de um dos valores mostrados a partir do [ `az account list-locations` ](/cli/azure/appservice?view=azure-cli-latest.md#az-appservice-list-locations) comando).

```bash
az webapp up -n <app-name> -l <location-name>
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
> [Tutorial: Aplicação de Python com PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Configurar a aplicação de Python](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [Tutorial: Executar a aplicação de Python no contentor personalizado](tutorial-custom-docker-image.md)
