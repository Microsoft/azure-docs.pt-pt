---
title: Corrida rápida de tarefa com modelo
description: Faça fila com uma tarefa ACR para construir uma imagem usando um modelo de Gestor de Recursos Azure
ms.topic: article
ms.date: 04/22/2020
ms.openlocfilehash: 6e8023c088ac328c2b6e95fccd0230c4d40325c1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98916070"
---
# <a name="run-acr-tasks-using-resource-manager-templates"></a>Executar tarefas ACR usando modelos de gestor de recursos

[ACR Tasks](container-registry-tasks-overview.md) é um conjunto de funcionalidades dentro do Registo do Contentor de Azure para ajudá-lo a gerir e modificar imagens de contentores através do ciclo de vida do contentor. 

Este artigo mostra exemplos de modelo do Azure Resource Manager para fazer fila de tarefas rápidas, semelhantes a uma que pode criar manualmente utilizando o comando [de construção az acr.][az-acr-build]

Um modelo de Gestor de Recursos para fazer fila de tarefas é útil em cenários de automação e alarga a funcionalidade de `az acr build` . Por exemplo:

* Use um modelo para criar um registo de contentores e faça fila imediatamente uma tarefa para construir e empurrar uma imagem de contentor
* Criar ou ativar recursos adicionais que pode utilizar numa tarefa rápida, como uma identidade gerida para recursos Azure

## <a name="limitations"></a>Limitações

* Deve especificar um contexto remoto, como um repo GitHub como o [local de origem](container-registry-tasks-overview.md#context-locations) para a sua tarefa executada. Não pode usar um contexto de origem local.
* Para execuções de tarefas utilizando uma identidade gerida, apenas é permitida uma identidade gerida *atribuída pelo utilizador.*

## <a name="prerequisites"></a>Pré-requisitos

* **Conta GitHub** - Crie uma conta https://github.com se ainda não tiver uma. 
* **Repositório de amostras de garfo** - Para os exemplos de tarefa aqui mostrados, utilize o GitHub UI para depositar o seguinte repositório de amostras na sua conta GitHub: https://github.com/Azure-Samples/acr-build-helloworld-node . Este repo contém amostras Dockerfiles e código fonte para construir pequenas imagens de contentores.

## <a name="example-create-registry-and-queue-task-run"></a>Exemplo: Criar registo e execução de tarefas de fila

Este exemplo usa um [modelo de amostra](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment/quickdockerbuild) para criar um registo de contentores e fazer fila de tarefas que constrói e empurra uma imagem. 

### <a name="template-parameters"></a>Parâmetros do modelo

Para este exemplo, forneça valores para os seguintes parâmetros do modelo:

|Parâmetro  |Valor  |
|---------|---------|
|nome de registo     |Nome único do registo que é criado         |
|repository     |Repositório-alvo para tarefa de construção        |
|taskRunName     |Nome da execução de tarefas, que especifica a etiqueta de imagem |
|origemLocalização     |Contexto remoto para a tarefa de construção, por exemplo, https://github.com/Azure-Samples/acr-build-helloworld-node . O Dockerfile na raiz do repo constrói uma imagem de recipiente para uma pequena aplicação web Node.js. Se desejar, use o seu garfo do repo como o contexto de construção.         |

### <a name="deploy-the-template"></a>Implementar o modelo

Implementar o modelo com o [grupo de implementação az criar][az-deployment-group-create] comando. Este exemplo constrói e empurra a imagem *helloworld-node:testrun* para um registo chamado *mycontainerregistry*.

```azurecli
az deployment group create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/acr/master/docs/tasks/run-as-deployment/quickdockerbuild/azuredeploy.json \
  --parameters \
    registryName=mycontainerregistry \
    repository=helloworld-node \
    taskRunName=testrun \
    sourceLocation=https://github.com/Azure-Samples/acr-build-helloworld-node.git#main
 ```

O comando anterior passa os parâmetros na linha de comando. Se desejar, passe-os num [ficheiro de parâmetros](../azure-resource-manager/templates/parameter-files.md).

### <a name="verify-deployment"></a>Verificar a implementação

Após a implementação concluída com sucesso, verifique se a imagem é construída executando [az acr repository show-tags][az-acr-repository-show-tags]:

```azurecli
az acr repository show-tags \
  --name mycontainerregistry \
  --repository helloworld-node --output table
```

Resultado:

```console
Result
--------
testrun
```

### <a name="view-run-log"></a>Ver registo de execução

Para ver detalhes sobre a execução da tarefa, veja o registo de execução.

Primeiro, obtenha o ID de execução com [az acr lista de tarefas-runs][az-acr-task-list-runs]
```azurecli
az acr task list-runs \
  --registry mycontainerregistry --output table
```

A saída é semelhante a:

```console
RUN ID    TASK    PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  ------  ----------  ---------  ---------  --------------------  ----------
ca1               linux       Succeeded  Manual     2020-03-23T17:54:28Z  00:00:48
```

Executar [registos de tarefas az acr][az-acr-task-logs] para visualizar registos de execução de tarefas para o ID de execução, neste caso *ca1*:

```azurecli
az acr task logs \
  --registry mycontainerregistry \
  --run-id ca1
```

A saída mostra o registo de execução de tarefas.

Também pode visualizar o registo de execução de tarefas no portal Azure. 

1. Navegue para o seu registo de contentores
2. Em **Serviços**, selecione **Tasks**  >  **Runs**.
3. Selecione o ID de execução, neste caso *ca1*. 

O portal mostra o registo de execução de tarefas.

## <a name="example-task-run-with-managed-identity"></a>Exemplo: Tarefa executada com identidade gerida

Utilize um [modelo de amostra](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment/quickdockerbuildwithidentity) para fazer fila de tarefas que permita uma identidade gerida atribuída pelo utilizador. Durante a execução da tarefa, a identidade autentica-se para retirar uma imagem de outro registo de contentores Azure. 

Este cenário é semelhante à [autenticação de registo cruzado numa tarefa ACR utilizando uma identidade gerida pelo Azure](container-registry-tasks-cross-registry-authentication.md). Por exemplo, uma organização pode manter um registo centralizado com imagens base acedidas por várias equipas de desenvolvimento.

### <a name="prepare-base-registry"></a>Preparar registo de base

Para fins de demonstração, crie um registo de contentores separado como registo base e empurre uma imagem de base Node.js retirada do Docker Hub.

1. Crie um segundo registo de contentores, por *exemplo, a minha base,* para armazenar imagens base.
1. Puxe a `node:9-alpine` imagem do Docker Hub, marque-a para o seu registo base e empurre-a para o registo base:

  ```azurecli
  docker pull node:9-alpine
  docker tag node:9-alpine mybaseregistry.azurecr.io/baseimages/node:9-alpine
  az acr login -n mybaseregistry
  docker push mybaseregistry.azurecr.io/baseimages/node:9-alpine
  ```

### <a name="create-new-dockerfile"></a>Criar novo Dockerfile

Crie um Dockerfile que retire a imagem base do seu registo base. Execute os seguintes passos no seu garfo local do gitHub repo, por exemplo, `https://github.com/myGitHubID/acr-build-helloworld-node.git` .

1. No GitHub UI, selecione **Criar novo ficheiro**.
1. Nomeie o seu ficheiro *Dockerfile-test* e cole o seguinte conteúdo. Substitua o seu nome de registo para *a minha baseregistria*.
    ```
    FROM mybaseregistry.azurecr.io/baseimages/node:9-alpine
    COPY . /src
    RUN cd /src && npm install
    EXPOSE 80
    CMD ["node", "/src/server.js"]
    ```
 1. **Selecione Cometer novo ficheiro**.

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="give-identity-pull-permissions-to-the-base-registry"></a>Dar permissões de retirada de identidade ao registo base

Dê as permissões de identidade geridas para retirar do registo base, *meu registo.*

Utilize o comando [az acr show][az-acr-show] para obter o ID de recurso do registo base e armazená-lo numa variável:

```azurecli
baseregID=$(az acr show \
  --name mybaseregistry \
  --query id --output tsv)
```

Utilize [a atribuição de funções az criar][az-role-assignment-create] comando para atribuir a identidade a função Acrpull ao registo base. Esta função tem permissões apenas para extrair imagens do registo.

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

### <a name="template-parameters"></a>Parâmetros do modelo

Para este exemplo, forneça valores para os seguintes parâmetros do modelo:

|Parâmetro  |Valor  |
|---------|---------|
|nome de registo     |Nome do registo onde a imagem é construída  |
|repository     |Repositório-alvo para tarefa de construção        |
|taskRunName     |Nome da execução de tarefas, que especifica a etiqueta de imagem |
|utilizadorAssignedIdentity |ID de recursos de identidade atribuída ao utilizador habilitado na tarefa|
|customRegistryDentity | ID do cliente de identidade atribuída ao utilizador habilitado na tarefa, usada para autenticar com registo personalizado |
|customRegistry |O nome do servidor de login do registo personalizado acedido na tarefa, por exemplo, *mybaseregistry.azurecr.io*|
|origemLocalização     |Contexto remoto para a tarefa de construção, por exemplo, *https://github.com/ \<your-GitHub-ID\> /acr-build-helloworld-node.* |
|DockerFilePath | Caminho para o Dockerfile no contexto remoto, usado para construir a imagem. |

### <a name="deploy-the-template"></a>Implementar o modelo

Implementar o modelo com o [grupo de implementação az criar][az-deployment-group-create] comando. Este exemplo constrói e empurra a imagem *helloworld-node:testrun* para um registo chamado *mycontainerregistry*. A imagem base é retirada de *mybaseregistry.azurecr.io*.

```azurecli
az deployment group create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/acr/master/docs/tasks/run-as-deployment/quickdockerbuildwithidentity/azuredeploy.json \
  --parameters \
    registryName=mycontainerregistry \
    repository=helloworld-node \
    taskRunName=basetask \
    userAssignedIdentity=$resourceID \
    customRegistryIdentity=$clientID \
    sourceLocation=https://github.com/<your-GitHub-ID>/acr-build-helloworld-node.git#main \
    dockerFilePath=Dockerfile-test \
    customRegistry=mybaseregistry.azurecr.io
```

O comando anterior passa os parâmetros na linha de comando. Se desejar, passe-os num [ficheiro de parâmetros](../azure-resource-manager/templates/parameter-files.md).

### <a name="verify-deployment"></a>Verificar a implementação

Após a implementação concluída com sucesso, verifique se a imagem é construída executando [az acr repository show-tags][az-acr-repository-show-tags]:

```azurecli
az acr repository show-tags \
  --name mycontainerregistry \
  --repository helloworld-node --output table
```

Resultado:

```console
Result
--------
basetask
```

### <a name="view-run-log"></a>Ver registo de execução

Para visualizar o registo de execução, consulte os passos na [secção anterior](#view-run-log).

## <a name="next-steps"></a>Passos seguintes

 * Veja mais exemplos de modelo no [repo ACR GitHub](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment).
 * Para obter detalhes sobre as propriedades do modelo, consulte a referência do modelo para execuções de [tarefas](/azure/templates/microsoft.containerregistry/2019-06-01-preview/registries/taskruns) e [tarefas](/azure/templates/microsoft.containerregistry/2019-06-01-preview/registries/tasks).


<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-logs]: /cli/azure/acr/task#az-acr-task-logs
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
