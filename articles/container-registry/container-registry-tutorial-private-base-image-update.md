---
title: Tutorial - Trigger imagem construída por atualização de imagem base privada
description: Neste tutorial, configura uma Tarefa de Registo de Contentores Azure para ativar automaticamente a imagem do contentor na nuvem quando uma imagem base em outro registo privado do contentor Azure é atualizada.
ms.topic: tutorial
ms.date: 11/20/2020
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: ce06e792fd5a4582e77d18313052ea91a38121a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98920211"
---
# <a name="tutorial-automate-container-image-builds-when-a-base-image-is-updated-in-another-private-container-registry"></a>Tutorial: Automatizar a imagem do contentor quando uma imagem base é atualizada em outro registo privado de contentores 

[A ACR Tasks](container-registry-tasks-overview.md) suporta a criação de imagem automatizada quando a imagem base de um recipiente [é atualizada](container-registry-tasks-base-images.md), como quando se remeca o SISTEMA ou a estrutura de aplicação numa das suas imagens base. 

Neste tutorial, aprende-se a criar uma tarefa ACR que desencadeie uma construção na nuvem quando a imagem base de um recipiente é empurrada para outro registo de contentores Azure. Também pode experimentar um tutorial para criar uma tarefa ACR que desencadeie uma construção de imagem quando uma imagem base é empurrada para o [mesmo registo de contentores Azure](container-registry-tutorial-base-image-update.md).

Neste tutorial:

> [!div class="checklist"]
> * Construa a imagem base num registo base
> * Criar uma tarefa de construção de aplicações em outro registo para rastrear a imagem base 
> * Atualizar a imagem de base para acionar uma tarefa das imagens da aplicação
> * Apresentar a tarefa acionada
> * Verificar a imagem da aplicação atualizada

## <a name="prerequisites"></a>Pré-requisitos

### <a name="complete-the-previous-tutorials"></a>Concluir os tutoriais anteriores

Este tutorial pressupõe que já configuraste o teu ambiente e completaste os passos nos dois primeiros tutoriais da série, nos quais:

* Criar um registo de contentor do Azure
* Bifurcar um repositório de exemplo
* Clonar um repositório de exemplo
* Criar token de acesso pessoal do GitHub

Se ainda não o fez, complete os seguintes tutoriais antes de prosseguir:

[Compilar imagens de contentor na cloud com o Azure Container Registry Tasks](container-registry-tutorial-quick-task.md)

[Automatizar as compilações da imagem de contentor com o Azure Container Registry Tasks](container-registry-tutorial-build-task.md)

Além do registo de contentores criado para os tutoriais anteriores, é necessário criar um registo para armazenar as imagens base. Se quiser, crie o segundo registo num local diferente do registo original.

### <a name="configure-the-environment"></a>Configurar o ambiente

Preencha estas variáveis de ambiente da shell com os valores adequados para o seu ambiente. Este passo não é estritamente necessário, mas facilita um pouco a execução dos comandos da CLI do Azure com várias linhas neste tutorial. Se não preencher estas variáveis ambientais, deve substituir manualmente cada valor onde quer que apareça nos comandos de exemplo.

```azurecli
BASE_ACR=<base-registry-name>   # The name of your Azure container registry for base images
ACR_NAME=<registry-name>        # The name of your Azure container registry for application images
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the second tutorial
```

### <a name="base-image-update-scenario"></a>Cenário de atualização da imagem de base

Este tutorial orienta-o através de um cenário de atualização da imagem de base. Este cenário reflete um fluxo de trabalho de desenvolvimento para gerir imagens base num registo comum e privado de contentores ao criar imagens de aplicação em outros registos. As imagens base poderiam especificar sistemas operativos comuns e quadros utilizados por uma equipa, ou mesmo componentes de serviço comuns.

Por exemplo, os desenvolvedores que desenvolvem imagens de aplicações nos seus próprios registos podem aceder a um conjunto de imagens base mantidas no registo base comum. O registo base pode ser em outra região ou mesmo geo-replicado.

O [exemplo de código][code-sample] inclui dois Dockerfiles: uma imagem da aplicação e uma imagem que é especificada como a sua base. Nas secções seguintes, cria-se uma tarefa ACR que aciona automaticamente uma construção da imagem da aplicação quando uma nova versão da imagem base é empurrada para um registo de contentores Azure diferente.

* [Dockerfile-app][dockerfile-app]: uma pequena aplicação Web Node.js que compõe uma página Web estática, que apresenta a versão do Node.js na qual se baseia. A cadeia de versão é simulada: apresenta o conteúdo de uma variável de ambiente, `NODE_VERSION`, definido na imagem de base.

* [Dockerfile-base][dockerfile-base]: a imagem que `Dockerfile-app` especifica como a sua base. A própria baseia-se numa imagem do [Nó][base-node] e inclui a variável de ambiente `NODE_VERSION`.

Nas próximas secções, vai criar uma tarefa, atualizar o valor `NODE_VERSION` no Dockerfile da imagem de base e, em seguida, utilizar o ACR Tasks para compilar a imagem de base. Quando a tarefa do ACR envia a nova imagem de base para o registo, aciona automaticamente uma compilação da imagem da aplicação. Opcionalmente, pode executar a imagem de contentor da aplicação localmente para ver as diferentes cadeias de versão nas imagens da compilação.

Neste tutorial, a sua tarefa ACR constrói e empurra uma imagem de recipiente de aplicação especificada num Dockerfile. As tarefas ACR também podem executar [tarefas em várias etapas,](container-registry-tasks-multi-step.md)utilizando um ficheiro YAML para definir passos para construir, empurrar e testar opcionalmente vários recipientes.

## <a name="build-the-base-image"></a>Compilar a imagem de base

Comece por construir a imagem base com uma *tarefa rápida* ACR Tasks, utilizando [a construção az acr][az-acr-build]. Tal como explicado no [primeiro tutorial](container-registry-tutorial-quick-task.md) da série, este processo não só compila a imagem como a envia para o seu registo de contentor, caso a compilação seja concluída com êxito. Neste exemplo, a imagem é empurrada para o registo de imagem base.

```azurecli
az acr build --registry $BASE_ACR --image baseimages/node:15-alpine --file Dockerfile-base .
```

## <a name="create-a-task-to-track-the-private-base-image"></a>Criar uma tarefa para rastrear a imagem base privada

Em seguida, crie uma tarefa no registo de imagem de aplicação com [a tarefa az acr criar,][az-acr-task-create]permitindo uma [identidade gerida](container-registry-tasks-authentication-managed-identity.md). A identidade gerida é utilizada em etapas posteriores para que a tarefa autense com o registo de imagem base. 

Este exemplo utiliza uma identidade atribuída ao sistema, mas pode criar e ativar uma identidade gerida atribuída pelo utilizador para determinados cenários. Para mais informações, consulte [a autenticação de registo cruzado numa tarefa ACR utilizando uma identidade gerida pelo Azure](container-registry-tasks-cross-registry-authentication.md).

```azurecli
az acr task create \
    --registry $ACR_NAME \
    --name baseexample2 \
    --image helloworld:{{.Run.ID}} \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git#main \
    --file Dockerfile-app \
    --git-access-token $GIT_PAT \
    --arg REGISTRY_NAME=$BASE_ACR.azurecr.io \
    --assign-identity
```

Esta tarefa é semelhante à tarefa criada no [tutorial anterior.](container-registry-tutorial-build-task.md) Dá instruções ao ACR Tasks para acionar uma compilação da imagem quando as consolidações são enviadas por push para o repositório especificado por `--context`. Enquanto o Dockerfile usado para construir a imagem no tutorial anterior especifica uma imagem de base pública `FROM node:15-alpine` (), o Dockerfile nesta tarefa, [Dockerfile-app,][dockerfile-app]especifica uma imagem base no registo de imagem base:

```Dockerfile
FROM ${REGISTRY_NAME}/baseimages/node:15-alpine
```

Esta configuração facilita a simulação de um patch de estrutura na imagem base mais tarde neste tutorial.

## <a name="give-identity-pull-permissions-to-base-registry"></a>Dar permissões de retirada de identidade para registo de base

Para dar permissões de identidade geridas da tarefa para retirar imagens do registo de imagem base, primeiro executar [o programa de tarefas az acr][az-acr-task-show] para obter o iD principal de serviço da identidade. Em [seguida, executar a az acr show][az-acr-show] para obter o documento de recursos do registo base:

```azurecli
# Get service principal ID of the task
principalID=$(az acr task show --name baseexample2 --registry $ACR_NAME --query identity.principalId --output tsv) 

# Get resource ID of the base registry
baseregID=$(az acr show --name $BASE_ACR --query id --output tsv) 
```
 
Atribua as permissões de retirada de identidade geridas ao registo executando [a az fun assignment assignment create][az-role-assignment-create]:

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID --role acrpull 
```

## <a name="add-target-registry-credentials-to-the-task"></a>Adicione credenciais de registo-alvo à tarefa

Executar [a az acr credencial de tarefa adicionar][az-acr-task-credential-add] para adicionar credenciais à tarefa. Passe o `--use-identity [system]` parâmetro para indicar que a identidade gerida atribuída pelo sistema da tarefa pode aceder às credenciais.

```azurecli
az acr task credential add \
  --name baseexample2 \
  --registry $ACR_NAME \
  --login-server $BASE_ACR.azurecr.io \
  --use-identity [system] 
```

## <a name="manually-run-the-task"></a>Executar manualmente a tarefa

Utilize [a execução de tarefas az acr][az-acr-task-run] para desencadear manualmente a tarefa e construir a imagem da aplicação. Este passo é necessário para que a tarefa rastreie a dependência da imagem da aplicação na imagem base.

```azurecli
az acr task run --registry $ACR_NAME --name baseexample2
```

Depois de concluída a tarefa, tome nota do **ID de Execução** (por exemplo, "da6") se quiser concluir o passo opcional seguinte.

### <a name="optional-run-application-container-locally"></a>Opcional: executar o contentor da aplicação localmente

Se estiver a trabalhar localmente (não estiver no Cloud Shell) e tiver o Docker instalado, execute o contentor para ver a aplicação composta num browser, antes de recompilar a imagem de base. Se estiver a utilizar o Cloud Shell, ignore esta secção (o Cloud Shell não suporta `az acr login` nem `docker run`).

Em primeiro lugar, autente no seu registo de contentores com [início az acr][az-acr-login]:

```azurecli
az acr login --name $ACR_NAME
```

Agora, execute o contentor localmente com `docker run`. **\<run-id\>** Substitua-a ID de execução encontrada na saída do passo anterior (por exemplo, "da6"). Este exemplo dá nomes ao recipiente `myapp` e inclui o parâmetro para remover o recipiente quando o `--rm` parar.

```bash
docker run -d -p 8080:80 --name myapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

Navegue para `http://localhost:8080` no browser, deverá ver o número de versão do Node.js composto na página Web, semelhante ao que se segue. Num passo posterior, pode efetuar o bump da versão ao adicionar um “a” na cadeia de versão.

:::image type="content" source="media/container-registry-tutorial-base-image-update/base-update-01.png" alt-text="Screenshot da aplicação da amostra no navegador":::

Para parar e remover o recipiente, executar o seguinte comando:

```bash
docker stop myapp
```

## <a name="list-the-builds"></a>Listar as compilações

Em seguida, liste as execuções de tarefas que o ACR Tasks concluiu para o seu registo com o comando [az acr task list-runs][az-acr-task-list-runs]:

```azurecli
az acr task list-runs --registry $ACR_NAME --output table
```

Se tiver concluído o tutorial anterior (e não tiver eliminado o registo), deverá ver um resultado semelhante ao seguinte. Tome nota do número de execuções da tarefa e do ID DE EXECUÇÃO mais recente para poder comparar o resultado depois de atualizar a imagem de base na próxima secção.

```console
$ az acr task list-runs --registry $ACR_NAME --output table

UN ID    TASK            PLATFORM    STATUS     TRIGGER       STARTED               DURATION
--------  --------------  ----------  ---------  ------------  --------------------  ----------
ca12      baseexample2    linux       Succeeded  Manual        2020-11-21T00:00:56Z  00:00:36
ca11      baseexample1    linux       Succeeded  Image Update  2020-11-20T23:38:24Z  00:00:34
ca10      taskhelloworld  linux       Succeeded  Image Update  2020-11-20T23:38:24Z  00:00:24
cay                       linux       Succeeded  Manual        2020-11-20T23:38:08Z  00:00:22
cax       baseexample1    linux       Succeeded  Manual        2020-11-20T23:33:12Z  00:00:30
caw       taskhelloworld  linux       Succeeded  Commit        2020-11-20T23:16:07Z  00:00:29
```

## <a name="update-the-base-image"></a>Atualizar a imagem de base

Nesta secção, vai simular uma correção da estrutura na imagem de base. Edite **Dockerfile-base** e adicione um “a” depois do número de versão definido em `NODE_VERSION`:

```Dockerfile
ENV NODE_VERSION 15.2.1a
```

Execute uma tarefa rápida para compilar a imagem de base modificada. Tome nota do **ID de Execução** no resultado.

```azurecli
az acr build --registry $BASE_ACR --image baseimages/node:15-alpine --file Dockerfile-base .
```

Depois de concluída a compilação e a nova imagem de base ter sido enviada para o registo, a tarefa do ACR aciona uma compilação da imagem da aplicação. Pode demorar algum tempo para que a tarefa criada anteriormente acione a compilação da imagem da aplicação, porque tem de detetar a imagem de base recentemente compilada e enviada.

## <a name="list-updated-build"></a>Listar a compilação atualizada

Agora que atualizou a imagem de base, liste as execuções de tarefas novamente para compará-las à lista anterior. Se, à primeira, o resultado não for diferente, execute periodicamente o comando para ver a nova execução de tarefa aparecer na lista.

```azurecli
az acr task list-runs --registry $ACR_NAME --output table
```

O resultado é semelhante ao seguinte. O ACIONADOR da última compilação executada deve ser "Atualizar Imagem", que indica que a tarefa foi iniciada pela tarefa rápida da imagem de base.

```console
$ az acr task list-runs --registry $ACR_NAME --output table

         PLATFORM    STATUS     TRIGGER       STARTED               DURATION
--------  --------------  ----------  ---------  ------------  --------------------  ----------
ca13      baseexample2    linux       Succeeded  Image Update  2020-11-21T00:06:00Z  00:00:43
ca12      baseexample2    linux       Succeeded  Manual        2020-11-21T00:00:56Z  00:00:36
ca11      baseexample1    linux       Succeeded  Image Update  2020-11-20T23:38:24Z  00:00:34
ca10      taskhelloworld  linux       Succeeded  Image Update  2020-11-20T23:38:24Z  00:00:24
cay                       linux       Succeeded  Manual        2020-11-20T23:38:08Z  00:00:22
cax       baseexample1    linux       Succeeded  Manual        2020-11-20T23:33:12Z  00:00:30
caw       taskhelloworld  linux       Succeeded  Commit        2020-11-20T23:16:07Z  00:00:29
```

Se pretender executar o seguinte passo opcional de funcionamento do recipiente recém-construído para ver o número de versão atualizado, tome nota do valor **run id** para a construção desencadeada por Atualização de Imagem (na saída anterior, é "ca13").

### <a name="optional-run-newly-built-image"></a>Opcional: executar a imagem recém-compilada

Se estiver a trabalhar localmente (não estiver no Cloud Shell) e tiver o Docker instalado, execute a nova imagem da aplicação depois de concluída a sua compilação. Substitua `<run-id>` pelo ID DE EXECUÇÃO que obteve no passo anterior. Se estiver a utilizar o Cloud Shell, ignore esta secção (o Cloud Shell não suporta `docker run`).

```bash
docker run -d -p 8081:80 --name updatedapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

Navegue para http://localhost:8081 no browser, deverá ver o número de versão do Node.js atualizado (com o “a”) na página Web:

:::image type="content" source="media/container-registry-tutorial-base-image-update/base-update-02.png" alt-text="Screenshot da aplicação de amostra atualizada no navegador":::

O que é importante ter em atenção é que atualizou a imagem de **base** com um novo número de versão, mas a imagem da **aplicação** da última compilação apresenta a nova versão. O ACR Tasks captou a alteração para a imagem de base e recompilou automaticamente a imagem da aplicação.

Para parar e remover o recipiente, executar o seguinte comando:

```bash
docker stop updatedapp
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a utilizar uma tarefa para acionar automaticamente compilações da imagem de contentor quando a imagem de base é atualizada. Agora, passe para o próximo tutorial para aprender a desencadear tarefas num horário definido.

> [!div class="nextstepaction"]
> [Executar uma tarefa em um horário](container-registry-tasks-scheduled.md)

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
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-update]: /cli/azure/acr/task#az-acr-task-update
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task]: /cli/azure/acr#az-acr-task
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create

