---
title: Execução rápida de tarefacom modelo
description: Faça fila com uma tarefa ACR para construir uma imagem usando um modelo de Gestor de Recursos Azure
ms.topic: article
ms.date: 04/22/2020
ms.openlocfilehash: 6e85e4a3d10ea34d71efde959aa7d179eba69e3b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82196533"
---
# <a name="run-acr-tasks-using-resource-manager-templates"></a>Executar tarefas ACR usando modelos de gestor de recursos

[A ACR Tasks](container-registry-tasks-overview.md) é um conjunto de funcionalidades dentro do Registo de Contentores Azure para ajudá-lo a gerir e modificar imagens de contentores ao longo do ciclo de vida do contentor. 

Este artigo mostra exemplos de modelo seletiva seletiva do Gestor de Recursos azure para fazer fila para uma execução rápida de tarefas, semelhante à que pode criar manualmente utilizando o comando de [construção az acr.][az-acr-build]

Um modelo de Gestor de Recursos para fazer fila de uma `az acr build`execução de tarefa é útil em cenários de automação e alarga a funcionalidade de . Por exemplo:

* Use um modelo para criar um registo de contentores e faça imediatamente fila de uma tarefa para construir e empurrar uma imagem de recipiente
* Crie ou permita recursos adicionais que pode utilizar numa corrida rápida de tarefas, como uma identidade gerida para os recursos Do Azure

## <a name="limitations"></a>Limitações

* Deve especificar um contexto remoto, como um repo GitHub como a [localização de origem](container-registry-tasks-overview.md#context-locations) para a sua execução de tarefas. Não pode usar um contexto local.
* Para executações de tarefas utilizando uma identidade gerida, apenas é permitida uma identidade gerida atribuída pelo *utilizador.*

## <a name="prerequisites"></a>Pré-requisitos

* **Conta GitHub** - Crie uma conta se https://github.com ainda não tiver uma. 
* **Repositório** de amostras de garfo - Para os exemplos de tarefa saem aqui, utilize o GitHub UI para bifurcar o seguinte repositório de amostras na sua conta GitHub: https://github.com/Azure-Samples/acr-build-helloworld-node. Este repo contém amostras Dockerfiles e código fonte para construir pequenas imagens de recipientes.

## <a name="example-create-registry-and-queue-task-run"></a>Exemplo: Criar o registo e a execução de tarefas de fila

Este exemplo usa um modelo de [amostra](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment/quickdockerbuild) para criar um registo de contentores e fazer fila de uma execução de tarefa que constrói e empurra uma imagem. 

### <a name="template-parameters"></a>Parâmetros do modelo

Para este exemplo, forneça valores para os seguintes parâmetros de modelo:

|Parâmetro  |Valor  |
|---------|---------|
|registryName     |Nome único do registo que é criado         |
|repositório     |Repositório-alvo para tarefa de construção        |
|taskRunName     |Nome da execução de tarefas, que especifica etiqueta de imagem |
|origemLocalização     |Contexto remoto para a tarefa https://github.com/Azure-Samples/acr-build-helloworld-nodede construção, por exemplo, . O Dockerfile na raiz do repo constrói uma imagem de recipiente para uma pequena aplicação web Node.js. Se desejar, use o garfo do repo como contexto de construção.         |

### <a name="deploy-the-template"></a>Implementar o modelo

Desloque o modelo com o grupo de [implantação az criar][az-deployment-group-create] comando. Este exemplo constrói e empurra a imagem *helloworld-node:testrun* para um registo chamado *mycontainerregistry*.

```azurecli
az deployment group create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/acr/master/docs/tasks/run-as-deployment/quickdockerbuild/azuredeploy.json \
  --parameters \
    registryName=mycontainerregistry \
    repository=helloworld-node \
    taskRunName=testrun \
    sourceLocation=https://github.com/Azure-Samples/acr-build-helloworld-node.git
 ```

O comando anterior passa os parâmetros da linha de comando. Se desejar, passe-os num ficheiro de [parâmetros](../azure-resource-manager/templates/parameter-files.md).

### <a name="verify-deployment"></a>Verificar a implementação

Após a implementação concluída com sucesso, verifique se a imagem é construída executando [az acr repositório show-tags:][az-acr-repository-show-tags]

```azurecli
az acr repository show-tags \
  --name mycontainerregistry \
  --repository helloworld-node --output table
```

Saída:

```console
Result
--------
testrun
```

### <a name="view-run-log"></a>Ver registo de execução

Para ver detalhes sobre a execução da tarefa, consulte o registo de execução.

Primeiro, obtenha o ID de execução com [az acr task-runs][az-acr-task-list-runs]
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

A saída mostra o registo de execução da tarefa.

Também pode visualizar o registo de execução de tarefas no portal Azure. 

1. Navegue para o seu registo de contentores
2. Em **Serviços,** selecione**Executações** **de Tarefas** > .
3. Selecione o ID de execução, neste caso *ca1*. 

O portal mostra o registo de execução de tarefas.

## <a name="example-task-run-with-managed-identity"></a>Exemplo: Execução de tarefa com identidade gerida

Utilize um modelo de [amostra](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment/quickdockerbuildwithidentity) para fazer fila com uma execução de tarefa que permita uma identidade gerida atribuída pelo utilizador. Durante a execução da tarefa, a identidade autentica-se para retirar uma imagem de outro registo de contentores Azure. 

Este cenário é semelhante à autenticação de [registo cruzado numa tarefa ACR utilizando uma identidade gerida pelo Azure.](container-registry-tasks-cross-registry-authentication.md) Por exemplo, uma organização pode manter um registo centralizado com imagens base acedidas por várias equipas de desenvolvimento.

### <a name="prepare-base-registry"></a>Preparar o registo base

Para efeitos de demonstração, crie um registo de contentores separado como registo base e empurre uma imagem de base nonó.js retirada do Docker Hub.

1. Crie um segundo registo de contentores, por *exemplo, registo mybase,* para armazenar imagens base.
1. Puxe `node:9-alpine` a imagem do Docker Hub, marque-a para o seu registo base e empurre-a para o registo base:

  ```azurecli
  docker pull node:9-alpine
  docker tag node:9-alpine mybaseregistry.azurecr.io/baseimages/node:9-alpine
  az acr login -n mybaseregistry
  docker push mybaseregistry.azurecr.io/baseimages/node:9-alpine
  ```

### <a name="create-new-dockerfile"></a>Criar novo Dockerfile

Crie um Dockerfile que retire a imagem base do registo base. Execute os seguintes passos no seu garfo local do https://github.com/myGitHubID/acr-build-helloworld-node.gitrepo GitHub, por exemplo, *.

1. No GitHub UI, selecione **Criar novo ficheiro**.
1. Nomeie o seu ficheiro *Teste de Ficheiros E* colhe o seguinte conteúdo. Substitua o seu nome de registo por *registo mybase.*
    ```
    FROM mybaseregistry.azurecr.io/baseimages/node:9-alpine
    COPY . /src
    RUN cd /src && npm install
    EXPOSE 80
    CMD ["node", "/src/server.js"]
    ```
 1. Selecione **Cometer novo ficheiro**.

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="give-identity-pull-permissions-to-the-base-registry"></a>Dê permissões de retirada de identidade para o registo base

Dê permissões de identidade geridas para retirar do registo base, *registo mybase.*

Utilize o comando do [az acr show][az-acr-show] para obter a identificação de recursos do registo base e armazená-lo numa variável:

```azurecli
baseregID=$(az acr show \
  --name mybaseregistry \
  --query id --output tsv)
```

Use a atribuição de [funções az criar][az-role-assignment-create] comando para atribuir a identidade a função Acrpull ao registo base. Este papel tem permissões apenas para retirar imagens do registo.

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

### <a name="template-parameters"></a>Parâmetros do modelo

Para este exemplo, forneça valores para os seguintes parâmetros de modelo:

|Parâmetro  |Valor  |
|---------|---------|
|registryName     |Nome do registo onde a imagem é construída  |
|repositório     |Repositório-alvo para tarefa de construção        |
|taskRunName     |Nome da execução de tarefas, que especifica etiqueta de imagem |
|userAssignedIdentity |Identificação de recurso da identidade atribuída ao utilizador ativada na tarefa|
|identidade personalizadaRegistry | ID do cliente da identidade atribuída ao utilizador ativada na tarefa, usado para autenticar com registo personalizado |
|registo personalizado |Nome do servidor de login do registo personalizado acedido na tarefa, por exemplo, *mybaseregistry.azurecr.io*|
|origemLocalização     |Contexto remoto para a tarefa de construção, por exemplo, * https://github.com/\<your-GitHub-ID\>/acr-build-helloworld-node.* |
|dockerFilePath | Caminho para o Dockerfile no contexto remoto, usado para construir a imagem. |

### <a name="deploy-the-template"></a>Implementar o modelo

Desloque o modelo com o grupo de [implantação az criar][az-deployment-group-create] comando. Este exemplo constrói e empurra a imagem *helloworld-node:testrun* para um registo chamado *mycontainerregistry*. A imagem base é retirada de *mybaseregistry.azurecr.io.*

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
    sourceLocation=https://github.com/<your-GitHub-ID>/acr-build-helloworld-node.git \
    dockerFilePath=Dockerfile-test \
    customRegistry=mybaseregistry.azurecr.io
```

O comando anterior passa os parâmetros da linha de comando. Se desejar, passe-os num ficheiro de [parâmetros](../azure-resource-manager/templates/parameter-files.md).

### <a name="verify-deployment"></a>Verificar a implementação

Após a implementação concluída com sucesso, verifique se a imagem é construída executando [az acr repositório show-tags:][az-acr-repository-show-tags]

```azurecli
az acr repository show-tags \
  --name mycontainerregistry \
  --repository helloworld-node --output table
```

Saída:

```console
Result
--------
basetask
```

### <a name="view-run-log"></a>Ver registo de execução

Para ver o registo de execução, consulte os passos na [secção anterior](#view-run-log).

## <a name="next-steps"></a>Passos seguintes

 * Veja mais exemplos de modelo no [repo ACR GitHub](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment).
 * Para mais detalhes sobre as propriedades do modelo, consulte a referência do modelo para executações de [tarefas](/azure/templates/microsoft.containerregistry/2019-06-01-preview/registries/taskruns) e [tarefas](/azure/templates/microsoft.containerregistry/2019-06-01-preview/registries/tasks).


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
