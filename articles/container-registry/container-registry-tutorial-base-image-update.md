---
title: Tutorial - automatizar compilações de imagem de contentor na atualização da imagem base - tarefas de registo de contentor do Azure
description: Neste tutorial, saiba como configurar uma tarefa de registo de contentor do Azure para acionar automaticamente compilações de imagem de contentor na cloud quando uma imagem de base é atualizada.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: tutorial
ms.date: 06/12/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 27315bf562f7b221b19747aca4809f2be5fd1121
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147463"
---
# <a name="tutorial-automate-container-image-builds-when-a-base-image-is-updated-in-an-azure-container-registry"></a>Tutorial: Automatizar compilações de imagem de contentor quando uma imagem de base é atualizada num Azure container registry 

O ACR Tasks suporta a execução de compilações automatizadas quando a imagem de base de um contentor é atualizada. Por exemplo, quando corrige o SO ou a estrutura de aplicações numa das suas imagens de base. Neste tutorial, vai aprender a criar uma tarefa no ACR Tasks para acionar uma compilação na cloud quando uma imagem de base do contentor tiver sido enviada para o seu registo.

Neste tutorial, a última parte da série:

> [!div class="checklist"]
> * Compilar a imagem de base
> * Criar uma tarefa de compilação das imagens da aplicação
> * Atualizar a imagem de base para acionar uma tarefa das imagens da aplicação
> * Apresentar a tarefa acionada
> * Verificar a imagem da aplicação atualizada

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se quiser utilizar a CLI do Azure localmente, terá de ter a versão **2.0.46** ou posterior da CLI do Azure instalada. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar a CLI, veja [Instalar a CLI do Azure][azure-cli].

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

O Dockerfiles, que define a maioria das imagens de contentor, especifica a imagem principal na qual se baseia a imagem, que é referida, muitas vezes, como a sua *imagem de base*. As imagens de base, normalmente, contêm o sistema operativo, por exemplo [Alpine Linux][base-alpine] ou [Windows Nano Server][base-windows], no qual são aplicadas as restantes camadas do contentor. As imagens de base também podem incluir estruturas de aplicações, tais como [Node.js][base-node] ou [.NET Core][base-dotnet].

### <a name="base-image-updates"></a>Atualizações da imagem de base

Frequentemente, uma imagem de base é atualizada pelo responsável pela manutenção da imagem para incluir as novas funcionalidades ou os melhoramentos no SO ou na estrutura. Os patches de segurança são outra causa comum para a atualização da imagem de base.

Quando uma imagem de base é atualizada, será necessário recompilar todas as imagens de contentor no seu registo baseadas nas mesmas para incluir as novas funcionalidades e correções. O ACR Tasks permite compilar automaticamente as imagens quando é atualizada a imagem de base de um contentor.

### <a name="tasks-triggered-by-a-base-image-update"></a>Tarefas acionadas por uma atualização da imagem base

* Atualmente, para compilações de imagem de um Dockerfile, uma tarefa ACR Deteta dependências em imagens de base no mesmo registo de contentor do Azure, um repositório público do Docker Hub ou um repositório público no registo de contentor do Microsoft. Se a imagem base especificada no `FROM` instrução reside em um desses locais, a tarefa ACR adiciona um gancho para garantir que a imagem é reconstruída sempre que é atualizado a sua base.

* Quando cria uma tarefa ACR com o [az acr tarefa criar] [ az-acr-task-create] comando, por predefinição, a tarefa é *ativada* para o acionador por uma atualização da imagem base. Ou seja, o `base-image-trigger-enabled` propriedade está definida como True. Se pretender desativar esse comportamento numa tarefa, atualize a propriedade como False. Por exemplo, execute o seguinte [atualização de tarefa az acr] [ az-acr-task-update] comando:

  ```azurecli
  az acr task update --myregistry --name mytask --base-image-trigger-enabled False
  ```

* Para ativar uma tarefa do ACR determinar e controlar as dependências de uma imagem de contentor, que incluem sua imagem base, primeiro tem de disparar a tarefa **, pelo menos, uma vez**. Por exemplo, acionar a tarefa manualmente com o [execução da tarefa az acr] [ az-acr-task-run] comando.

* Para acionar uma tarefa na atualização da imagem base, a imagem base tem de ter uma *estável* marcar, tais como `node:9-alpine`. Este tipo de etiquetagem é normal para uma imagem de base que é atualizada com o sistema operacional e o framework patches para a versão estável mais recente. Se a imagem base é atualizada com uma nova marca de versão, a mesma não aciona uma tarefa. Para obter mais informações sobre a marcação de imagem, consulte a [práticas recomendadas](https://blogs.msdn.microsoft.com/stevelasker/2018/03/01/docker-tagging-best-practices-for-tagging-and-versioning-docker-images/). 

### <a name="base-image-update-scenario"></a>Cenário de atualização da imagem de base

Este tutorial orienta-o através de um cenário de atualização da imagem de base. O [exemplo de código][code-sample] inclui dois Dockerfiles: uma imagem da aplicação e uma imagem que é especificada como a sua base. As secções seguintes, vai criar uma tarefa ACR que aciona automaticamente uma compilação da imagem de aplicação quando uma nova versão da imagem base é emitida para o mesmo registo de contentor.

[Dockerfile-app][dockerfile-app]: Um pequeno aplicativo de web do node. js, que renderiza uma página web estática, exibindo a versão do node. js em que se baseia. A cadeia de versão é simulada: apresenta o conteúdo de uma variável de ambiente, `NODE_VERSION`, definido na imagem de base.

[Dockerfile-base][dockerfile-base]: A imagem que `Dockerfile-app` Especifica como base. A própria baseia-se numa imagem do [Nó][base-node] e inclui a variável de ambiente `NODE_VERSION`.

Nas próximas secções, vai criar uma tarefa, atualizar o valor `NODE_VERSION` no Dockerfile da imagem de base e, em seguida, utilizar o ACR Tasks para compilar a imagem de base. Quando a tarefa do ACR envia a nova imagem de base para o registo, aciona automaticamente uma compilação da imagem da aplicação. Opcionalmente, pode executar a imagem de contentor da aplicação localmente para ver as diferentes cadeias de versão nas imagens da compilação.

Neste tutorial, o sua tarefa ACR cria e envia uma imagem de contentor do aplicativo especificada no Dockerfile. Também pode executar tarefas de ACR [tarefas de vários passos](container-registry-tasks-multi-step.md), com um ficheiro YAML para definir os passos para criar, emitir e, opcionalmente, pode testar vários contentores.

## <a name="build-the-base-image"></a>Compilar a imagem de base

Comece por compilar a imagem de base com uma *tarefa rápida* do ACR Tasks. Tal como explicado no [primeiro tutorial](container-registry-tutorial-quick-task.md) da série, este processo não só compila a imagem como a envia para o seu registo de contentor, caso a compilação seja concluída com êxito.

```azurecli-interactive
az acr build --registry $ACR_NAME --image baseimages/node:9-alpine --file Dockerfile-base .
```

## <a name="create-a-task"></a>Criar uma tarefa

Em seguida, crie uma tarefa com [az acr task create][az-acr-task-create]:

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name taskhelloworld \
    --image helloworld:{{.Run.ID}} \
    --arg REGISTRY_NAME=$ACR_NAME.azurecr.io \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file Dockerfile-app \
    --branch master \
    --git-access-token $GIT_PAT
```

> [!IMPORTANT]
> Se anteriormente tiver criado tarefas durante a pré-visualização com o comando `az acr build-task`, essas tarefas têm de ser recriadas com o comando [az acr task][az-acr-task].

Esta tarefa é semelhante à tarefa rápida criada no [tutorial anterior](container-registry-tutorial-build-task.md). Dá instruções ao ACR Tasks para acionar uma compilação da imagem quando as consolidações são enviadas por push para o repositório especificado por `--context`. Enquanto o Dockerfile utilizado para criar a imagem no tutorial anterior Especifica uma imagem de base de pública (`FROM node:9-alpine`), o Dockerfile nesta tarefa, [Dockerfile-app][dockerfile-app], especifica uma imagem de base da mesma registo:

```Dockerfile
FROM ${REGISTRY_NAME}/baseimages/node:9-alpine
```

Esta configuração torna mais fácil simular um patch de framework na imagem base mais tarde neste tutorial.

## <a name="build-the-application-container"></a>Compilar o contentor de aplicação

Uso [execução da tarefa az acr] [ az-acr-task-run] manualmente disparar a tarefa e criar a imagem de aplicação. Este passo garante que a tarefa controla a dependência da imagem de aplicação na imagem base.

```azurecli-interactive
az acr task run --registry $ACR_NAME --name taskhelloworld
```

Depois de concluída a tarefa, tome nota do **ID de Execução** (por exemplo, "da6") se quiser concluir o passo opcional seguinte.

### <a name="optional-run-application-container-locally"></a>Opcional: Executar localmente o contentor da aplicação

Se estiver a trabalhar localmente (não estiver no Cloud Shell) e tiver o Docker instalado, execute o contentor para ver a aplicação composta num browser, antes de recompilar a imagem de base. Se estiver a utilizar o Cloud Shell, ignore esta secção (o Cloud Shell não suporta `az acr login` nem `docker run`).

Primeiro, autenticar para o seu registo de contentor com o [início de sessão az acr][az-acr-login]:

```azurecli
az acr login --name $ACR_NAME
```

Agora, execute o contentor localmente com `docker run`. Substitua **\<run-id\>** pelo ID de Execução encontrado no resultado do passo anterior (por exemplo, "da6"). Este exemplo atribui o contentor `myapp` e inclui o `--rm` parâmetro para remover o contentor quando, impedi-lo.

```bash
docker run -d -p 8080:80 --name myapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

Navegue para `http://localhost:8080` no browser, deverá ver o número de versão do Node.js composto na página Web, semelhante ao que se segue. Num passo posterior, pode efetuar o bump da versão ao adicionar um “a” na cadeia de versão.

![Captura de ecrã da aplicação de exemplo composta no browser][base-update-01]

Para parar e remover o contentor, execute o seguinte comando:

```bash
docker stop myapp
```

## <a name="list-the-builds"></a>Listar as compilações

Em seguida, liste as execuções de tarefas que o ACR Tasks concluiu para o seu registo com o comando [az acr task list-runs][az-acr-task-list-runs]:

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
$ az acr task list-builds --registry $ACR_NAME --output table

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

### <a name="optional-run-newly-built-image"></a>Opcional: Execute a imagem recentemente criada

Se estiver a trabalhar localmente (não estiver no Cloud Shell) e tiver o Docker instalado, execute a nova imagem da aplicação depois de concluída a sua compilação. Substitua `<run-id>` pelo ID DE EXECUÇÃO que obteve no passo anterior. Se estiver a utilizar o Cloud Shell, ignore esta secção (o Cloud Shell não suporta `docker run`).

```bash
docker run -d -p 8081:80 --name updatedapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

Navegue para http://localhost:8081 no browser, deverá ver o número de versão do Node.js atualizado (com o “a”) na página Web:

![Captura de ecrã da aplicação de exemplo composta no browser][base-update-02]

O que é importante ter em atenção é que atualizou a imagem de **base** com um novo número de versão, mas a imagem da **aplicação** da última compilação apresenta a nova versão. O ACR Tasks captou a alteração para a imagem de base e recompilou automaticamente a imagem da aplicação.

Para parar e remover o contentor, execute o seguinte comando:

```bash
docker stop updatedapp
```

## <a name="clean-up-resources"></a>Limpar recursos

Para remover todos os recursos que criou nesta série de tutoriais, incluindo o registo de contentor, a instância de contentor, o cofre de chaves e o principal de serviço, emita os comandos seguintes:

```azurecli-interactive
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu a utilizar uma tarefa para acionar automaticamente compilações da imagem de contentor quando a imagem de base é atualizada. Agora, pode avançar para saber mais sobre a autenticação do registo do contentor.

> [!div class="nextstepaction"]
> [Autenticação no Azure Container Registry](container-registry-authentication.md)

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
