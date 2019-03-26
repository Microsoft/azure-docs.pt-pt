---
title: Criar as funções do Azure no Linux com uma imagem personalizada
description: Saiba como criar Funções do Azure em execução numa imagem personalizada do Linux.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 02/25/2019
ms.topic: tutorial
ms.service: azure-functions
ms.custom: mvc
ms.devlang: azure-cli
manager: jeconnoc
ms.openlocfilehash: 92811110ef44676de487bca1ad2022cb63315c75
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58418062"
---
# <a name="create-a-function-on-linux-using-a-custom-image"></a>Criar uma função no Linux com uma imagem personalizada

As Funções do Azure permitem-lhe alojar as suas funções no Linux no seu próprio contentor personalizado. Também pode [alojar num contentor predefinido do Serviço de Aplicações do Azure](functions-create-first-azure-function-azure-cli-linux.md). Esta funcionalidade requer [o tempo de execução de 2.x funções](functions-versions.md).

Neste tutorial, irá aprender a implementar as suas funções no Azure como uma imagem personalizada do Docker. Este padrão é útil se precisar de personalizar a imagem de contentor do Serviço de Aplicações incorporada. Pode querer utilizar uma imagem personalizada quando as suas funções precisarem de uma versão de idioma ou dependência específicas, ou de uma configuração não fornecida na imagem incorporada. Imagens de base é suportado para as funções do Azure encontram-se no [repositório de imagens de base de funções do Azure](https://hub.docker.com/_/microsoft-azure-functions-base). [Podpora Pythonu](functions-reference-python.md) está em pré-visualização neste momento.

Este tutorial explica como utilizar o Azure Functions Core Tools para criar uma função numa imagem do Linux personalizada. Publique esta imagem numa aplicação de funções no Azure, que foi criada com a CLI do Azure.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie uma aplicação de funções e o Dockerfile com Ferramentas de Núcleo.
> * Criar uma imagem personalizada com o Docker.
> * Publicar uma imagem personalizada num registo de contentor.
> * Criar uma conta de Armazenamento do Azure.
> * Criar um plano do Serviço de Aplicações do Linux.
> * Implementar uma aplicação de funções a partir do Docker Hub.
> * Adicionar as definições de aplicação à aplicação de funções.
> * Ativar a implementação contínua

São suportados os seguintes passos num computador Mac, Windows ou Linux.  

## <a name="prerequisites"></a>Pré-requisitos

Antes de executar este exemplo, tem de ter o seguinte:

* Instale a [versão 2.x das Ferramentas Base do Azure](functions-run-local.md#v2).

* Instale a [CLI do Azure]( /cli/azure/install-azure-cli). Este artigo requer a versão 2.0 ou posterior da CLI do Azure. Execute `az --version` para localizar a versão atual.  
Pode também utilizar o [Azure Cloud Shell](https://shell.azure.com/bash).

* Uma subscrição ativa do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-local-function-app-project"></a>Criar o projeto de aplicação de funções local

Execute o seguinte comando na linha de comandos para criar um projeto de aplicação de funções na pasta `MyFunctionProj` do diretório local atual.

```bash
func init MyFunctionProj --docker
```

Quando inclui a opção `--docker`, é gerado um dockerfile para o projeto. Este ficheiro é utilizado para criar um contentor personalizado para executar o projeto. A imagem base utilizada depende do idioma do runtime do trabalho escolhido.  

Quando lhe for pedido, escolha um runtime do trabalho entre os seguintes idiomas:

* `dotnet`: cria um projeto de biblioteca de classes .NET (.csproj).
* `node`: cria um projeto do JavaScript.
* `python`: cria um projeto de Python.

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

Quando o comando for executado, verá algo parecido com o seguinte resultado:

```output
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing Dockerfile
```

Utilize o comando seguinte para navegar para a nova pasta do projeto `MyFunctionProj`.

```bash
cd MyFunctionProj
```

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-update-function-code](../../includes/functions-update-function-code.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

## <a name="build-the-image-from-the-docker-file"></a>Compilar a imagem a partir do Dockerfile

Veja o _Dockerfile_ no diretório de raiz do projeto. Este ficheiro descreve o ambiente que é necessário para executar a aplicação de funções no Linux. O exemplo seguinte é um Dockerfile que cria um contentor que executa uma aplicação de funções no runtime do trabalho do JavaScript (Node.js): 

```Dockerfile
FROM mcr.microsoft.com/azure-functions/node:2.0

ENV AzureWebJobsScriptRoot=/home/site/wwwroot
COPY . /home/site/wwwroot
```

> [!NOTE]
> A lista completa de imagens de base suportadas para as funções do Azure pode ser encontrada no [página de imagem de base de funções do Azure](https://hub.docker.com/_/microsoft-azure-functions-base).

### <a name="run-the-build-command"></a>Execute o comando `build`
Na pasta raiz, execute o comando [docker build](https://docs.docker.com/engine/reference/commandline/build/) e indique um nome, `mydockerimage`, e uma etiqueta, `v1.0.0`. Substitua `<docker-id>` pelo ID da sua conta do Docker Hub. Este comando cria a imagem do Docker para o contentor.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

Quando o comando for executado, verá algo parecido com o seguinte resultado, que é, neste caso, para um runtime de trabalho do JavaScript:

```bash
Sending build context to Docker daemon  17.41kB
Step 1/3 : FROM mcr.microsoft.com/azure-functions/node:2.0
2.0: Pulling from azure-functions/node
802b00ed6f79: Pull complete
44580ea7a636: Pull complete
73eebe8d57f9: Pull complete
3d82a67477c2: Pull complete
8bd51cd50290: Pull complete
7bd755353966: Pull complete
Digest: sha256:480e969821e9befe7c61dda353f63298f2c4b109e13032df5518e92540ea1d08
Status: Downloaded newer image for mcr.microsoft.com/azure-functions/node:2.0
 ---> 7c71671b838f
Step 2/3 : ENV AzureWebJobsScriptRoot=/home/site/wwwroot
 ---> Running in ed1e5809f0b7
Removing intermediate container ed1e5809f0b7
 ---> 39d9c341368a
Step 3/3 : COPY . /home/site/wwwroot
 ---> 5e196215935a
Successfully built 5e196215935a
Successfully tagged <docker-id>/mydockerimage:v1.0.0
```

### <a name="test-the-image-locally"></a>Testar a imagem localmente
Verifique se a imagem que incorpora funciona ao executar a imagem do Docker num contentor local. Emita o comando [docker run](https://docs.docker.com/engine/reference/commandline/run/) e transmita o nome e a etiqueta da imagem ao mesmo. Certifique-se de que utiliza o argumento `-p` para especificar a porta.

```bash
docker run -p 8080:80 -it <docker-ID>/mydockerimage:v1.0.0
```

Com a imagem personalizada em execução num contentor local do Docker, certifique-se de que a aplicação de funções e o contentor estão a funcionar corretamente ao navegar para <http://localhost:8080>.

![Teste a aplicação de funções localmente.](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

Opcionalmente, pode testar a sua função novamente, desta vez no contentor local com o seguinte URL:

`http://localhost:8080/api/myhttptrigger?name=<yourname>`

Depois de ter verificado a aplicação de funções no contentor, pare a execução. Agora, pode enviar a imagem personalizada para a sua conta do Docker Hub.

## <a name="push-the-custom-image-to-docker-hub"></a>Enviar a imagem personalizada para o Docker Hub

Um registo de contentor é uma aplicação que aloja imagens e disponibiliza serviços de imagens e contentores. Para partilhar a sua imagem, tem de enviá-la para um registo. O Docker Hub é um registo de contentores para imagens do Docker que lhe permite alojar os seus próprios repositórios, sejam públicos ou privados.

Antes de poder enviar uma imagem, tem de iniciar sessão no Docker Hub com o comando [docker login](https://docs.docker.com/engine/reference/commandline/login/). Substitua `<docker-id>` pelo nome da sua conta e introduza a palavra-passe na consola, quando lhe for pedido. Para outras opções de palavra-passe do Docker Hub, veja a [documentação do comando docker login](https://docs.docker.com/engine/reference/commandline/login/).

```bash
docker login --username <docker-id>
```

A mensagem "início de sessão bem-sucedido" confirma que tem sessão iniciada. Depois de iniciar sessão, envie a imagem para o Docker Hub com o comando [docker push](https://docs.docker.com/engine/reference/commandline/push/).

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

Veja o resultado do comando para confirmar se o envio foi bem-sucedido.

```bash
The push refers to a repository [docker.io/<docker-id>/mydockerimage:v1.0.0]
24d81eb139bf: Pushed
fd9e998161c9: Mounted from <docker-id>/mydockerimage
e7796c35add2: Mounted from <docker-id>/mydockerimage
ae9a05b85848: Mounted from <docker-id>/mydockerimage
45c86e20670d: Mounted from <docker-id>/mydockerimage
v1.0.0: digest: sha256:be080d80770df71234eb893fbe4d... size: 1796
```

Agora, pode utilizar esta imagem como origem de implementação para uma nova aplicação de funções no Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-app-service-plan"></a>Criar um plano do Serviço de Aplicações do Linux

O alojamento do Linux para Funções não é atualmente suportado em planos de consumo. Tem de alojar as aplicações de contentor do Linux num plano do Serviço de Aplicações do Linux. Para saber mais sobre o alojamento, veja [Azure Functions hosting plans comparison (Comparação dos planos de alojamento das Funções do Azure)](functions-scale.md).

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

## <a name="create-and-deploy-the-custom-image"></a>Criar e implementar a imagem personalizada

Uma aplicação de funções aloja a execução das suas funções. Utilize o comando [az functionapp create](/cli/azure/functionapp#az-functionapp-create) para criar uma aplicação de funções a partir de uma imagem do Docker Hub.

No comando seguinte, substitua o nome da sua aplicação de funções exclusivo onde vir o marcador de posição `<app_name>` e o nome da conta de armazenamento para `<storage_name>`. O `<app_name>` vai ser utilizado como o domínio DNS predefinido para a aplicação Function App, daí que o nome tenha de ser exclusivo em todas as aplicações no Azure. Como anteriormente, `<docker-id>` é o nome da sua conta do Docker.

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--plan myAppServicePlan --deployment-container-image-name <docker-id>/mydockerimage:v1.0.0
```

Depois de a aplicação Function App ter sido criada, a CLI do Azure mostra informações semelhantes às do exemplo seguinte:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

O parâmetro _deployment-container-image-name_ indica a imagem alojada no Docker Hub a utilizar para criar a aplicação de funções. Utilize o [show de contentor do az functionapp config](/cli/azure/functionapp/config/container#az-functionapp-config-container-show) comando para ver informações sobre a imagem utilizada para a implementação. Utilize o [az functionapp config container set](/cli/azure/functionapp/config/container#az-functionapp-config-container-set) comando para implementar a partir de uma imagem diferente.

## <a name="configure-the-function-app"></a>Configurar a aplicação de funções

A função precisa da cadeia de ligação para ligar à conta de armazenamento predefinida. Quando estiver a publicar a sua imagem personalizada numa conta de contentor privada, deve definir estas definições de aplicação como variáveis de ambiente no Dockerfile com a [instrução ENV](https://docs.docker.com/engine/reference/builder/#env) ou algo semelhante.

Neste caso, `<storage_name>` é o nome da conta de armazenamento que criou. Obtenha a cadeia de ligação com o comando [az storage account show-connection-string](/cli/azure/storage/account). Adicione estas definições de aplicação na aplicação de funções com o comando [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set).

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string \
--resource-group myResourceGroup --name <storage_name> \
--query connectionString --output tsv)

az functionapp config appsettings set --name <app_name> \
--resource-group myResourceGroup \
--settings AzureWebJobsDashboard=$storageConnectionString \
AzureWebJobsStorage=$storageConnectionString
```

> [!NOTE]
> Se o seu contentor é privado, terá de especificar também as seguintes definições de aplicação  
> - DOCKER_REGISTRY_SERVER_USERNAME  
> - DOCKER_REGISTRY_SERVER_PASSWORD  
>
> Terá de parar e, em seguida, iniciar a aplicação de funções para estes valores a ser escolhidas

Agora, pode testar as suas funções em execução no Linux no Azure.

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

## <a name="enable-continuous-deployment"></a>Ativar a implementação contínua

Uma das vantagens da utilização de contentores é a capacidade de implementar automaticamente atualizações quando os contentores são atualizados no Registro. Ativar a implementação contínua com o [az functionapp deployment contentor config](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config) comando.

```azurecli-interactive
az functionapp deployment container config --enable-cd \
--query CI_CD_URL --output tsv \
--name <app_name> --resource-group myResourceGroup
```

Este comando devolve o URL do webhook de implementação após a implementação contínua está ativada. Também pode utilizar o [az functionapp deployment show-cd-url do contentor](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url) comando para devolver este URL. 

Copie o URL de implementação e navegue até ao seu repositório de DockerHub, escolha o **Webhooks** separador, escreva um **nome do Webhook** para o webhook, cole o URL na **URL do Webhook**e, em seguida, escolha o sinal de adição (**+**).

![Adicionar o webhook no seu repositório de DockerHub](media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

Com o conjunto de webhook, todas as atualizações para a imagem ligada no DockerHub resultam na function app baixar e instalar a imagem mais recente.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Crie uma aplicação de funções e o Dockerfile com Ferramentas de Núcleo.
> * Criar uma imagem personalizada com o Docker.
> * Publicar uma imagem personalizada num registo de contentor.
> * Criar uma conta de Armazenamento do Azure.
> * Criar um plano do Serviço de Aplicações do Linux.
> * Implementar uma aplicação de funções a partir do Docker Hub.
> * Adicionar as definições de aplicação à aplicação de funções.

Saiba como ativar a funcionalidade de integração contínua integrada na plataforma principal do Serviço de Aplicações. Pode configurar a sua aplicação de funções para que o contentor seja novamente implementado ao atualizar a imagem no Hub do Docker.

> [!div class="nextstepaction"] 
> [Implementação contínua com a Aplicação Web para Contentores](../app-service/containers/app-service-linux-ci-cd.md)
