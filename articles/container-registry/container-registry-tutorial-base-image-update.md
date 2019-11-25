---
title: Tutorial - Trigger image build on base image update
description: In this tutorial, you learn how to configure an Azure Container Registry Task to automatically trigger container image builds in the cloud when a base image is updated.
ms.topic: tutorial
ms.date: 08/12/2019
ms.custom: seodec18, mvc
ms.openlocfilehash: b89bf0364165822368647b4c5b773bf422902aec
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456137"
---
# <a name="tutorial-automate-container-image-builds-when-a-base-image-is-updated-in-an-azure-container-registry"></a>Tutorial: Automate container image builds when a base image is updated in an Azure container registry 

O ACR Tasks suporta a execução de compilações automatizadas quando a imagem de base de um contentor é atualizada. Por exemplo, quando corrige o SO ou a estrutura de aplicações numa das suas imagens de base. Neste tutorial, vai aprender a criar uma tarefa no ACR Tasks para acionar uma compilação na cloud quando uma imagem de base do contentor tiver sido enviada para o seu registo.

Neste tutorial, a última parte da série:

> [!div class="checklist"]
> * Compilar a imagem de base
> * Criar uma tarefa de compilação das imagens da aplicação
> * Atualizar a imagem de base para acionar uma tarefa das imagens da aplicação
> * Apresentar a tarefa acionada
> * Verificar a imagem da aplicação atualizada

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se quiser utilizar a CLI do Azure localmente, terá de ter a versão **2.0.46** ou posterior da CLI do Azure instalada. Executar `az --version` para localizar a versão. If you need to install or upgrade the CLI, see [Install Azure CLI][azure-cli].

## <a name="prerequisites"></a>Pré-requisitos

### <a name="complete-the-previous-tutorials"></a>Concluir os tutoriais anteriores

Este tutorial pressupõe que já tenha concluído os passos nos dois primeiros tutoriais da série, nos quais pôde:

* Criar um registo de contentor do Azure
* Bifurcar um repositório de exemplo
* Clonar um repositório de exemplo
* Criar token de acesso pessoal do GitHub

Se ainda não o fez, conclua os dois primeiros tutoriais antes de continuar:

[Compilar imagens de contentor na cloud com o Azure Container Registry Tasks](container-registry-tutorial-quick-task.md)

[Automatizar as compilações da imagem de contentor com o Azure Container Registry Tasks](container-registry-tutorial-build-task.md)

### <a name="configure-the-environment"></a>Configurar o ambiente

Preencha estas variáveis de ambiente da shell com os valores adequados para o seu ambiente. Este passo não é estritamente necessário, mas facilita um pouco a execução dos comandos da CLI do Azure com várias linhas neste tutorial. Se não preencher estas variáveis de ambiente, terá de substituir manualmente cada valor sempre que aparecerem nos comandos de exemplo.

```azurecli-interactive
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the second tutorial
```

## <a name="base-images"></a>Imagens de base

O Dockerfiles, que define a maioria das imagens de contentor, especifica a imagem principal na qual se baseia a imagem, que é referida, muitas vezes, como a sua *imagem de base*. Base images typically contain the operating system, for example [Alpine Linux][base-alpine] or [Windows Nano Server][base-windows], on which the rest of the container's layers are applied. They might also include application frameworks such as [Node.js][base-node] or [.NET Core][base-dotnet].

### <a name="base-image-updates"></a>Atualizações da imagem de base

Frequentemente, uma imagem de base é atualizada pelo responsável pela manutenção da imagem para incluir as novas funcionalidades ou os melhoramentos no SO ou na estrutura. Os patches de segurança são outra causa comum para a atualização da imagem de base.

Quando uma imagem de base é atualizada, será necessário recompilar todas as imagens de contentor no seu registo baseadas nas mesmas para incluir as novas funcionalidades e correções. O ACR Tasks permite compilar automaticamente as imagens quando é atualizada a imagem de base de um contentor.

### <a name="tasks-triggered-by-a-base-image-update"></a>Tasks triggered by a base image update

* For image builds from a Dockerfile, an ACR task detects dependencies on base images in the following locations:

  * The same Azure container registry where the task runs
  * Another Azure container registry in the same region 
  * A public repo in Docker Hub 
  * A public repo in Microsoft Container Registry

   If the base image specified in the `FROM` statement resides in one of these locations, the ACR task adds a hook to ensure the image is rebuilt any time its base is updated.

* Currently, an ACR tasks only tracks base image updates for application (*runtime*) images. It doesn't track base image updates for intermediate (*buildtime*) images used in multi-stage Dockerfiles.  

* When you create an ACR task with the [az acr task create][az-acr-task-create] command, by default the task is *enabled* for trigger by a base image update. That is, the `base-image-trigger-enabled` property is set to True. If you want to disable this behavior in a task, update the property to False. For example, run the following [az acr task update][az-acr-task-update] command:

  ```azurecli
  az acr task update --myregistry --name mytask --base-image-trigger-enabled False
  ```

* To enable an ACR task to determine and track a container image's dependencies -- which include its base image -- you must first trigger the task **at least once**. For example, trigger the task manually using the [az acr task run][az-acr-task-run] command.

* To trigger a task on base image update, the base image must have a *stable* tag, such as `node:9-alpine`. This tagging is typical for a base image that is updated with OS and framework patches to a latest stable release. If the base image is updated with a new version tag, it does not trigger a task. For more information about image tagging, see the [best practices guidance](container-registry-image-tag-version.md). 

### <a name="base-image-update-scenario"></a>Cenário de atualização da imagem de base

Este tutorial orienta-o através de um cenário de atualização da imagem de base. The [code sample][code-sample] includes two Dockerfiles: an application image, and an image it specifies as its base. In the following sections, you create an ACR task that automatically triggers a build of the application image when a new version of the base image is pushed to the same container registry.

[Dockerfile-app][dockerfile-app]: A small Node.js web application that renders a static web page displaying the Node.js version on which it's based. A cadeia de versão é simulada: apresenta o conteúdo de uma variável de ambiente, `NODE_VERSION`, definido na imagem de base.

[Dockerfile-base][dockerfile-base]: The image that `Dockerfile-app` specifies as its base. It is itself based on a [Node][base-node] image, and includes the `NODE_VERSION` environment variable.

Nas próximas secções, vai criar uma tarefa, atualizar o valor `NODE_VERSION` no Dockerfile da imagem de base e, em seguida, utilizar o ACR Tasks para compilar a imagem de base. Quando a tarefa do ACR envia a nova imagem de base para o registo, aciona automaticamente uma compilação da imagem da aplicação. Opcionalmente, pode executar a imagem de contentor da aplicação localmente para ver as diferentes cadeias de versão nas imagens da compilação.

In this tutorial, your ACR task builds and pushes an application container image specified in a Dockerfile. ACR Tasks can also run [multi-step tasks](container-registry-tasks-multi-step.md), using a YAML file to define steps to build, push, and optionally test multiple containers.

## <a name="build-the-base-image"></a>Compilar a imagem de base

Comece por compilar a imagem de base com uma *tarefa rápida* do ACR Tasks. Tal como explicado no [primeiro tutorial](container-registry-tutorial-quick-task.md) da série, este processo não só compila a imagem como a envia para o seu registo de contentor, caso a compilação seja concluída com êxito.

```azurecli-interactive
az acr build --registry $ACR_NAME --image baseimages/node:9-alpine --file Dockerfile-base .
```

## <a name="create-a-task"></a>Criar uma tarefa

Next, create a task with [az acr task create][az-acr-task-create]:

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name taskhelloworld \
    --image helloworld:{{.Run.ID}} \
    --arg REGISTRY_NAME=$ACR_NAME.azurecr.io \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file Dockerfile-app \
    --git-access-token $GIT_PAT
```

> [!IMPORTANT]
> If you previously created tasks during the preview with the `az acr build-task` command, those tasks need to be re-created using the [az acr task][az-acr-task] command.

Esta tarefa é semelhante à tarefa rápida criada no [tutorial anterior](container-registry-tutorial-build-task.md). Dá instruções ao ACR Tasks para acionar uma compilação da imagem quando as consolidações são enviadas por push para o repositório especificado por `--context`. While the Dockerfile used to build the image in the previous tutorial specifies a public base image (`FROM node:9-alpine`), the Dockerfile in this task, [Dockerfile-app][dockerfile-app], specifies a base image in the same registry:

```Dockerfile
FROM ${REGISTRY_NAME}/baseimages/node:9-alpine
```

This configuration makes it easy to simulate a framework patch in the base image later in this tutorial.

## <a name="build-the-application-container"></a>Compilar o contentor de aplicação

Use [az acr task run][az-acr-task-run] to manually trigger the task and build the application image. This step ensures that the task tracks the application image's dependency on the base image.

```azurecli-interactive
az acr task run --registry $ACR_NAME --name taskhelloworld
```

Depois de concluída a tarefa, tome nota do **ID de Execução** (por exemplo, "da6") se quiser concluir o passo opcional seguinte.

### <a name="optional-run-application-container-locally"></a>Opcional: executar o contentor da aplicação localmente

Se estiver a trabalhar localmente (não estiver no Cloud Shell) e tiver o Docker instalado, execute o contentor para ver a aplicação composta num browser, antes de recompilar a imagem de base. Se estiver a utilizar o Cloud Shell, ignore esta secção (o Cloud Shell não suporta `az acr login` nem `docker run`).

First, authenticate to your container registry with [az acr login][az-acr-login]:

```azurecli
az acr login --name $ACR_NAME
```

Agora, execute o contentor localmente com `docker run`. Substitua **\<run-id\>** pelo ID de Execução encontrado no resultado do passo anterior (por exemplo, "da6"). This example names the container `myapp` and includes the `--rm` parameter to remove the container when you stop it.

```bash
docker run -d -p 8080:80 --name myapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

Navegue para `http://localhost:8080` no browser, deverá ver o número de versão do Node.js composto na página Web, semelhante ao que se segue. Num passo posterior, pode efetuar o bump da versão ao adicionar um “a” na cadeia de versão.

![Captura de ecrã da aplicação de exemplo composta no browser][base-update-01]

To stop and remove the container, run the following command:

```bash
docker stop myapp
```

## <a name="list-the-builds"></a>Listar as compilações

Next, list the task runs that ACR Tasks has completed for your registry using the [az acr task list-runs][az-acr-task-list-runs] command:

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

Se tiver concluído o tutorial anterior (e não tiver eliminado o registo), deverá ver um resultado semelhante ao seguinte. Tome nota do número de execuções da tarefa e do ID DE EXECUÇÃO mais recente para poder comparar o resultado depois de atualizar a imagem de base na próxima secção.

```console
$ az acr task list-runs --registry $ACR_NAME --output table

RUN ID    TASK            PLATFORM    STATUS     TRIGGER     STARTED               DURATION
--------  --------------  ----------  ---------  ----------  --------------------  ----------
da6       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T23:07:22Z  00:00:38
da5                       Linux       Succeeded  Manual      2018-09-17T23:06:33Z  00:00:31
da4       taskhelloworld  Linux       Succeeded  Git Commit  2018-09-17T23:03:45Z  00:00:44
da3       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T22:55:35Z  00:00:35
da2       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T22:50:59Z  00:00:32
da1                       Linux       Succeeded  Manual      2018-09-17T22:29:59Z  00:00:57
```

## <a name="update-the-base-image"></a>Atualizar a imagem de base

Nesta secção, vai simular uma correção da estrutura na imagem de base. Edite **Dockerfile-base** e adicione um “a” depois do número de versão definido em `NODE_VERSION`:

```Dockerfile
ENV NODE_VERSION 9.11.2a
```

Execute uma tarefa rápida para compilar a imagem de base modificada. Tome nota do **ID de Execução** no resultado.

```azurecli-interactive
az acr build --registry $ACR_NAME --image baseimages/node:9-alpine --file Dockerfile-base .
```

Depois de concluída a compilação e a nova imagem de base ter sido enviada para o registo, a tarefa do ACR aciona uma compilação da imagem da aplicação. Pode demorar algum tempo para que a tarefa criada anteriormente acione a compilação da imagem da aplicação, porque tem de detetar a imagem de base recentemente compilada e enviada.

## <a name="list-updated-build"></a>Listar a compilação atualizada

Agora que atualizou a imagem de base, liste as execuções de tarefas novamente para compará-las à lista anterior. Se, à primeira, o resultado não for diferente, execute periodicamente o comando para ver a nova execução de tarefa aparecer na lista.

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

O resultado é semelhante ao seguinte. O ACIONADOR da última compilação executada deve ser "Atualizar Imagem", que indica que a tarefa foi iniciada pela tarefa rápida da imagem de base.

```console
$ az acr task list-runs --registry $ACR_NAME --output table

Run ID    TASK            PLATFORM    STATUS     TRIGGER       STARTED               DURATION
--------  --------------  ----------  ---------  ------------  --------------------  ----------
da8       taskhelloworld  Linux       Succeeded  Image Update  2018-09-17T23:11:50Z  00:00:33
da7                       Linux       Succeeded  Manual        2018-09-17T23:11:27Z  00:00:35
da6       taskhelloworld  Linux       Succeeded  Manual        2018-09-17T23:07:22Z  00:00:38
da5                       Linux       Succeeded  Manual        2018-09-17T23:06:33Z  00:00:31
da4       taskhelloworld  Linux       Succeeded  Git Commit    2018-09-17T23:03:45Z  00:00:44
da3       taskhelloworld  Linux       Succeeded  Manual        2018-09-17T22:55:35Z  00:00:35
da2       taskhelloworld  Linux       Succeeded  Manual        2018-09-17T22:50:59Z  00:00:32
da1                       Linux       Succeeded  Manual        2018-09-17T22:29:59Z  00:00:57
```

Se quiser executar o passo seguinte opcional de execução do contentor recentemente compilado para ver o número da versão atualizada, tome nota do valor do **ID DE EXECUÇÃO** da compilação acionada pela Atualização da Imagem (no resultado anterior, é "da8").

### <a name="optional-run-newly-built-image"></a>Opcional: executar a imagem recém-compilada

Se estiver a trabalhar localmente (não estiver no Cloud Shell) e tiver o Docker instalado, execute a nova imagem da aplicação depois de concluída a sua compilação. Substitua `<run-id>` pelo ID DE EXECUÇÃO que obteve no passo anterior. Se estiver a utilizar o Cloud Shell, ignore esta secção (o Cloud Shell não suporta `docker run`).

```bash
docker run -d -p 8081:80 --name updatedapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

Navegue para http://localhost:8081 no browser, deverá ver o número de versão do Node.js atualizado (com o “a”) na página Web:

![Captura de ecrã da aplicação de exemplo composta no browser][base-update-02]

O que é importante ter em atenção é que atualizou a imagem de **base** com um novo número de versão, mas a imagem da **aplicação** da última compilação apresenta a nova versão. O ACR Tasks captou a alteração para a imagem de base e recompilou automaticamente a imagem da aplicação.

To stop and remove the container, run the following command:

```bash
docker stop updatedapp
```

## <a name="clean-up-resources"></a>Limpar recursos

Para remover todos os recursos que criou nesta série de tutoriais, incluindo o registo de contentor, a instância de contentor, o cofre de chaves e o principal de serviço, emita os comandos seguintes:

```azurecli-interactive
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a utilizar uma tarefa para acionar automaticamente compilações da imagem de contentor quando a imagem de base é atualizada. Now, move on to the next tutorial to learn how to trigger tasks on a defined schedule.

> [!div class="nextstepaction"]
> [Run a task on a schedule](container-registry-tasks-scheduled.md)

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[code-sample]: https://github.com/Azure-Samples/acr-build-helloworld-node
[dockerfile-app]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-app
[dockerfile-base]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-base

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-update]: /cli/azure/acr/task#az-acr-task-update
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-task-list-runs]: /cli/azure/acr
[az-acr-task]: /cli/azure/acr

<!-- IMAGES -->
[base-update-01]: ./media/container-registry-tutorial-base-image-update/base-update-01.png
[base-update-02]: ./media/container-registry-tutorial-base-image-update/base-update-02.png
