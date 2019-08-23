---
title: Tutorial-automatizar a criação da imagem de contêiner na atualização da imagem de base-tarefas do registro de contêiner do Azure
description: Neste tutorial, você aprenderá a configurar uma tarefa de registro de contêiner do Azure para disparar automaticamente as compilações de imagem de contêiner na nuvem quando uma imagem base for atualizada.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: tutorial
ms.date: 06/12/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 1a6f73841550c64e93fa09fe0a1b033f840c9752
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69971373"
---
# <a name="tutorial-automate-container-image-builds-when-a-base-image-is-updated-in-an-azure-container-registry"></a>Tutorial: Automatizar compilações de imagem de contêiner quando uma imagem base é atualizada em um registro de contêiner do Azure 

O ACR Tasks suporta a execução de compilações automatizadas quando a imagem de base de um contentor é atualizada. Por exemplo, quando corrige o SO ou a estrutura de aplicações numa das suas imagens de base. Neste tutorial, vai aprender a criar uma tarefa no ACR Tasks para acionar uma compilação na cloud quando uma imagem de base do contentor tiver sido enviada para o seu registo.

Neste tutorial, a última parte da série:

> [!div class="checklist"]
> * Compilar a imagem de base
> * Criar uma tarefa de compilação das imagens da aplicação
> * Atualizar a imagem de base para acionar uma tarefa das imagens da aplicação
> * Apresentar a tarefa acionada
> * Verificar a imagem da aplicação atualizada

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se quiser utilizar a CLI do Azure localmente, terá de ter a versão **2.0.46** ou posterior da CLI do Azure instalada. Executar `az --version` para localizar a versão. Se você precisar instalar ou atualizar a CLI, consulte [instalar CLI do Azure][azure-cli].

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

O Dockerfiles, que define a maioria das imagens de contentor, especifica a imagem principal na qual se baseia a imagem, que é referida, muitas vezes, como a sua *imagem de base*. As imagens básicas normalmente contêm o sistema operacional, por exemplo, [Alpine Linux][base-alpine] ou [Windows nano Server][base-windows], no qual o restante das camadas do contêiner é aplicado. Eles também podem incluir estruturas de aplicativo como [node. js][base-node] ou [.NET Core][base-dotnet].

### <a name="base-image-updates"></a>Atualizações da imagem de base

Frequentemente, uma imagem de base é atualizada pelo responsável pela manutenção da imagem para incluir as novas funcionalidades ou os melhoramentos no SO ou na estrutura. Os patches de segurança são outra causa comum para a atualização da imagem de base.

Quando uma imagem de base é atualizada, será necessário recompilar todas as imagens de contentor no seu registo baseadas nas mesmas para incluir as novas funcionalidades e correções. O ACR Tasks permite compilar automaticamente as imagens quando é atualizada a imagem de base de um contentor.

### <a name="tasks-triggered-by-a-base-image-update"></a>Tarefas disparadas por uma atualização de imagem base

* Atualmente, para compilações de imagem de um Dockerfile, uma tarefa ACR detecta dependências em imagens base no mesmo registro de contêiner do Azure, um repositório público do Hub do Docker ou um repositório público no registro de contêiner da Microsoft. Se a imagem base especificada na `FROM` instrução residir em um desses locais, a tarefa ACR adicionará um gancho para garantir que a imagem seja recriada sempre que sua base for atualizada.

* Quando você cria uma tarefa ACR com o comando [AZ ACR Task Create][az-acr-task-create] , por padrão, a tarefa é *habilitada* para o gatilho por uma atualização de imagem de base. Ou seja, a `base-image-trigger-enabled` propriedade é definida como true. Se você quiser desabilitar esse comportamento em uma tarefa, atualize a propriedade para false. Por exemplo, execute o seguinte comando [AZ ACR Task Update][az-acr-task-update] :

  ```azurecli
  az acr task update --myregistry --name mytask --base-image-trigger-enabled False
  ```

* Para habilitar uma tarefa ACR para determinar e acompanhar as dependências de uma imagem de contêiner, que incluem sua imagem base, você deve primeiro disparar a tarefa **pelo menos uma vez**. Por exemplo, dispare a tarefa manualmente usando o comando [AZ ACR Task execute][az-acr-task-run] .

* Para disparar uma tarefa na atualização da imagem base, a imagem base deve ter uma marca *estável* , como `node:9-alpine`. Essa marcação é típica para uma imagem de base que é atualizada com OS patches do sistema operacional e da estrutura para uma versão estável mais recente. Se a imagem base for atualizada com uma nova marca de versão, ela não disparará uma tarefa. Para obter mais informações sobre a marcação de imagem, consulte as [diretrizes de práticas recomendadas](https://stevelasker.blog/2018/03/01/docker-tagging-best-practices-for-tagging-and-versioning-docker-images/). 

### <a name="base-image-update-scenario"></a>Cenário de atualização da imagem de base

Este tutorial orienta-o através de um cenário de atualização da imagem de base. O [exemplo de código][code-sample] inclui dois Dockerfiles: uma imagem de aplicativo e uma imagem que ela especifica como sua base. Nas seções a seguir, você cria uma tarefa ACR que dispara automaticamente uma compilação da imagem do aplicativo quando uma nova versão da imagem base é enviada para o mesmo registro de contêiner.

[Dockerfile-aplicativo][dockerfile-app]: Um pequeno aplicativo Web node. js que renderiza uma página da Web estática exibindo a versão do node. js na qual ela se baseia. A cadeia de versão é simulada: apresenta o conteúdo de uma variável de ambiente, `NODE_VERSION`, definido na imagem de base.

[Dockerfile-base][dockerfile-base]: A imagem que `Dockerfile-app` especifica como sua base. Ele se baseia em uma imagem de [nó][base-node] e inclui a variável `NODE_VERSION` de ambiente.

Nas próximas secções, vai criar uma tarefa, atualizar o valor `NODE_VERSION` no Dockerfile da imagem de base e, em seguida, utilizar o ACR Tasks para compilar a imagem de base. Quando a tarefa do ACR envia a nova imagem de base para o registo, aciona automaticamente uma compilação da imagem da aplicação. Opcionalmente, pode executar a imagem de contentor da aplicação localmente para ver as diferentes cadeias de versão nas imagens da compilação.

Neste tutorial, a tarefa ACR cria e envia por push uma imagem de contêiner de aplicativo especificada em um Dockerfile. As tarefas ACR também podem executar [tarefas de várias etapas](container-registry-tasks-multi-step.md), usando um arquivo YAML para definir etapas para compilar, enviar por push e, opcionalmente, testar vários contêineres.

## <a name="build-the-base-image"></a>Compilar a imagem de base

Comece por compilar a imagem de base com uma *tarefa rápida* do ACR Tasks. Tal como explicado no [primeiro tutorial](container-registry-tutorial-quick-task.md) da série, este processo não só compila a imagem como a envia para o seu registo de contentor, caso a compilação seja concluída com êxito.

```azurecli-interactive
az acr build --registry $ACR_NAME --image baseimages/node:9-alpine --file Dockerfile-base .
```

## <a name="create-a-task"></a>Criar uma tarefa

Em seguida, crie uma tarefa com [AZ ACR tarefa Create][az-acr-task-create]:

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
> Se você tiver criado previamente tarefas durante a visualização com `az acr build-task` o comando, essas tarefas precisarão ser recriadas usando o comando [AZ ACR Task][az-acr-task] .

Esta tarefa é semelhante à tarefa rápida criada no [tutorial anterior](container-registry-tutorial-build-task.md). Dá instruções ao ACR Tasks para acionar uma compilação da imagem quando as consolidações são enviadas por push para o repositório especificado por `--context`. Embora o Dockerfile usado para criar a imagem no tutorial anterior especifique uma imagem de base pública (`FROM node:9-alpine`), a Dockerfile nesta tarefa, [Dockerfile-app][dockerfile-app], especifica uma imagem base no mesmo registro:

```Dockerfile
FROM ${REGISTRY_NAME}/baseimages/node:9-alpine
```

Essa configuração facilita a simulação de um patch de estrutura na imagem base posteriormente neste tutorial.

## <a name="build-the-application-container"></a>Compilar o contentor de aplicação

Use a [tarefa AZ ACR Run][az-acr-task-run] para disparar a tarefa manualmente e criar a imagem do aplicativo. Essa etapa garante que a tarefa acompanhe a dependência da imagem do aplicativo na imagem base.

```azurecli-interactive
az acr task run --registry $ACR_NAME --name taskhelloworld
```

Depois de concluída a tarefa, tome nota do **ID de Execução** (por exemplo, "da6") se quiser concluir o passo opcional seguinte.

### <a name="optional-run-application-container-locally"></a>Opcional: Executar o contêiner de aplicativo localmente

Se estiver a trabalhar localmente (não estiver no Cloud Shell) e tiver o Docker instalado, execute o contentor para ver a aplicação composta num browser, antes de recompilar a imagem de base. Se estiver a utilizar o Cloud Shell, ignore esta secção (o Cloud Shell não suporta `az acr login` nem `docker run`).

Primeiro, autentique no registro de contêiner com [AZ ACR login][az-acr-login]:

```azurecli
az acr login --name $ACR_NAME
```

Agora, execute o contentor localmente com `docker run`. Substitua **\<run-id\>** pelo ID de Execução encontrado no resultado do passo anterior (por exemplo, "da6"). Este exemplo nomeia o contêiner `myapp` e inclui o `--rm` parâmetro para remover o contêiner quando você o interrompe.

```bash
docker run -d -p 8080:80 --name myapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

Navegue para `http://localhost:8080` no browser, deverá ver o número de versão do Node.js composto na página Web, semelhante ao que se segue. Num passo posterior, pode efetuar o bump da versão ao adicionar um “a” na cadeia de versão.

![Captura de ecrã da aplicação de exemplo composta no browser][base-update-01]

Para parar e remover o contêiner, execute o seguinte comando:

```bash
docker stop myapp
```

## <a name="list-the-builds"></a>Listar as compilações

Em seguida, liste a tarefa executada que as tarefas de ACR foram concluídas para o registro usando o comando [AZ ACR Task List-executes][az-acr-task-list-runs] :

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

### <a name="optional-run-newly-built-image"></a>Opcional: Executar imagem criada recentemente

Se estiver a trabalhar localmente (não estiver no Cloud Shell) e tiver o Docker instalado, execute a nova imagem da aplicação depois de concluída a sua compilação. Substitua `<run-id>` pelo ID DE EXECUÇÃO que obteve no passo anterior. Se estiver a utilizar o Cloud Shell, ignore esta secção (o Cloud Shell não suporta `docker run`).

```bash
docker run -d -p 8081:80 --name updatedapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

Navegue para http://localhost:8081 no browser, deverá ver o número de versão do Node.js atualizado (com o “a”) na página Web:

![Captura de ecrã da aplicação de exemplo composta no browser][base-update-02]

O que é importante ter em atenção é que atualizou a imagem de **base** com um novo número de versão, mas a imagem da **aplicação** da última compilação apresenta a nova versão. O ACR Tasks captou a alteração para a imagem de base e recompilou automaticamente a imagem da aplicação.

Para parar e remover o contêiner, execute o seguinte comando:

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
