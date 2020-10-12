---
title: Autenticação de registo cruzado da tarefa ACR
description: Configure uma Tarefa de Registo de Contentores Azure (Tarefa ACR) para aceder a outro registo privado de contentores Azure utilizando uma identidade gerida para os recursos da Azure
ms.topic: article
ms.date: 07/06/2020
ms.openlocfilehash: 8b961a2ff6a795f03798cc6f6a7d303391036ef8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86057365"
---
# <a name="cross-registry-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>Autenticação de registo cruzado numa tarefa de ACR utilizando uma identidade gerida pelo Azure 

Numa [tarefa ACR,](container-registry-tasks-overview.md)pode [ativar uma identidade gerida para os recursos Azure](container-registry-tasks-authentication-managed-identity.md). A tarefa pode usar a identidade para aceder a outros recursos Azure, sem necessidade de fornecer ou gerir credenciais. 

Neste artigo, aprende-se a permitir que uma identidade gerida numa tarefa retire uma imagem de um registo diferente daquele usado para executar a tarefa.

Para criar os recursos Azure, este artigo requer que você execute a versão Azure CLI 2.0.68 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli].

## <a name="scenario-overview"></a>Scenario overview (Descrição geral do cenário)

A tarefa de exemplo retira uma imagem base de outro registo de contentores Azure para construir e empurrar uma imagem de aplicação. Para puxar a imagem base, configura a tarefa com uma identidade gerida e atribui-lhe permissões apropriadas. 

Este exemplo mostra passos utilizando uma identidade gerida atribuída pelo utilizador ou pelo sistema. A sua escolha de identidade depende das necessidades da sua organização.

Num cenário real, uma organização pode manter um conjunto de imagens base usadas por todas as equipas de desenvolvimento para construir as suas aplicações. Estas imagens base são armazenadas num registo corporativo, com cada equipa de desenvolvimento a ter apenas direitos de retirada. 

## <a name="prerequisites"></a>Pré-requisitos

Para este artigo, você precisa de dois registos de contentores Azure:

* Utiliza o primeiro registo para criar e executar tarefas ACR. Neste artigo, este registo é nomeado *miogiristria.* 
* O segundo registo acolhe uma imagem base usada para a tarefa de construir uma imagem. Neste artigo, o segundo registo é nomeado *meu registo.* 

Substitua-os por nomes de registo próprios em etapas posteriores.

Se ainda não tiver os registos necessários dos contentores Azure, consulte [Quickstart: Crie um registo de contentores privados utilizando o Azure CLI](container-registry-get-started-azure-cli.md). Ainda não precisas de levar as imagens para o registo.

## <a name="prepare-base-registry"></a>Preparar registo de base

Primeiro, crie um diretório de trabalho e, em seguida, crie um ficheiro chamado Dockerfile com o seguinte conteúdo. Este exemplo simples constrói uma imagem base Node.js a partir de uma imagem pública em Docker Hub.
    
```bash
echo FROM node:9-alpine > Dockerfile
```

No diretório atual, executar o comando [de construção az acr][az-acr-build] para construir e empurrar a imagem base para o registo base. Na prática, outra equipa ou processo na organização pode manter o registo base.
    
```azurecli
az acr build --image baseimages/node:9-alpine --registry mybaseregistry --file Dockerfile .
```

## <a name="define-task-steps-in-yaml-file"></a>Definir passos de tarefa no ficheiro YAML

Os passos para este exemplo [são](container-registry-tasks-multi-step.md) definidos num [ficheiro YAML](container-registry-tasks-reference-yaml.md). Crie um ficheiro nomeado `helloworldtask.yaml` no seu diretório de trabalho local e cole os seguintes conteúdos. Atualize o valor do passo de `REGISTRY_NAME` construção com o nome do servidor do seu registo base.

```yml
version: v1.1.0
steps:
# Replace mybaseregistry with the name of your registry containing the base image
  - build: -t $Registry/hello-world:$ID  https://github.com/Azure-Samples/acr-build-helloworld-node.git -f Dockerfile-app --build-arg REGISTRY_NAME=mybaseregistry.azurecr.io
  - push: ["$Registry/hello-world:$ID"]
```

O passo de construção utiliza `Dockerfile-app` o ficheiro no [Azure-Samples/acr-build-helloworld-node](https://github.com/Azure-Samples/acr-build-helloworld-node.git) repo para construir uma imagem. As `--build-arg` referências ao registo base para puxar a imagem base. Quando construída com sucesso, a imagem é empurrada para o registo utilizado para executar a tarefa.

## <a name="option-1-create-task-with-user-assigned-identity"></a>Opção 1: Criar tarefa com identidade atribuída ao utilizador

Os passos nesta secção criam uma tarefa e permitem uma identidade atribuída ao utilizador. Se pretender ativar uma identidade atribuída ao sistema, consulte [a Opção 2: Criar tarefa com identidade atribuída ao sistema](#option-2-create-task-with-system-assigned-identity). 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>Criar tarefa

Crie a tarefa *helloworldtask* executando a [seguinte tarefa az acr criar][az-acr-task-create] comando. A tarefa é executado sem um contexto de código fonte, e o comando faz referência ao ficheiro `helloworldtask.yaml` no diretório de trabalho. O `--assign-identity` parâmetro transmite o ID de recursos da identidade atribuída ao utilizador. 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]

### <a name="give-identity-pull-permissions-to-the-base-registry"></a>Dar permissões de retirada de identidade ao registo base

Nesta secção, forneça as permissões de identidade geridas para retirar do registo base, *o meu registo.*

Utilize o comando [az acr show][az-acr-show] para obter o ID de recurso do registo base e armazená-lo numa variável:

```azurecli
baseregID=$(az acr show --name mybaseregistry --query id --output tsv)
```

Utilize [a atribuição de funções az criar][az-role-assignment-create] comando para atribuir a identidade o `acrpull` papel ao registo base. Esta função tem permissões apenas para extrair imagens do registo.

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

Continue a [adicionar credenciais de registo-alvo à tarefa](#add-target-registry-credentials-to-task).

## <a name="option-2-create-task-with-system-assigned-identity"></a>Opção 2: Criar tarefa com identidade atribuída ao sistema

Os passos nesta secção criam uma tarefa e permitem uma identidade atribuída ao sistema. Se pretender ativar uma identidade atribuída ao utilizador, consulte [a Opção 1: Criar tarefa com identidade atribuída ao utilizador](#option-1-create-task-with-user-assigned-identity). 

### <a name="create-task"></a>Criar tarefa

Crie a tarefa *helloworldtask* executando a [seguinte tarefa az acr criar][az-acr-task-create] comando. A tarefa é executado sem um contexto de código fonte, e o comando faz referência ao ficheiro `helloworldtask.yaml` no diretório de trabalho. O `--assign-identity` parâmetro sem valor permite a identidade atribuída ao sistema na tarefa. 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity 
```
[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

### <a name="give-identity-pull-permissions-to-the-base-registry"></a>Dar permissões de retirada de identidade ao registo base

Nesta secção, forneça as permissões de identidade geridas para retirar do registo base, *o meu registo.*

Utilize o comando [az acr show][az-acr-show] para obter o ID de recurso do registo base e armazená-lo numa variável:

```azurecli
baseregID=$(az acr show --name mybaseregistry --query id --output tsv)
```

Utilize [a atribuição de funções az criar][az-role-assignment-create] comando para atribuir a identidade o `acrpull` papel ao registo base. Esta função tem permissões apenas para extrair imagens do registo.

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

## <a name="add-target-registry-credentials-to-task"></a>Adicione credenciais de registo-alvo à tarefa

Agora use o comando [de adicionar a az acr][az-acr-task-credential-add] para permitir que a tarefa autense com o registo base utilizando as credenciais da identidade. Executar o comando correspondente ao tipo de identidade gerida que ativou na tarefa. Se tiver ativado uma identidade atribuída ao utilizador, passe `--use-identity` com a identificação do cliente da identidade. Se ativou uma identidade atribuída ao sistema, passe `--use-identity [system]` .

```azurecli
# Add credentials for user-assigned identity to the task
az acr task credential add \
  --name helloworldtask \
  --registry myregistry \
  --login-server mybaseregistry.azurecr.io \
  --use-identity $clientID

# Add credentials for system-assigned identity to the task
az acr task credential add \
  --name helloworldtask \
  --registry myregistry \
  --login-server mybaseregistry.azurecr.io \
  --use-identity [system]
```

## <a name="manually-run-the-task"></a>Executar manualmente a tarefa

Para verificar se a tarefa em que ativou uma identidade gerida funciona com sucesso, desencadeie manualmente a tarefa com o comando de execução de [tarefas az acr.][az-acr-task-run] 

```azurecli
az acr task run \
  --name helloworldtask \
  --registry myregistry
```

Se a tarefa for executado com sucesso, a saída é semelhante a:

```
Queued a run with ID: cf10
Waiting for an agent...
2019/06/14 22:47:32 Using acb_vol_dbfbe232-fd76-4ca3-bd4a-687e84cb4ce2 as the home volume
2019/06/14 22:47:39 Creating Docker network: acb_default_network, driver: 'bridge'
2019/06/14 22:47:40 Successfully set up Docker network: acb_default_network
2019/06/14 22:47:40 Setting up Docker configuration...
2019/06/14 22:47:41 Successfully set up Docker configuration
2019/06/14 22:47:41 Logging in to registry: myregistry.azurecr.io
2019/06/14 22:47:42 Successfully logged into myregistry.azurecr.io
2019/06/14 22:47:42 Logging in to registry: mybaseregistry.azurecr.io
2019/06/14 22:47:43 Successfully logged into mybaseregistry.azurecr.io
2019/06/14 22:47:43 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/14 22:47:43 Scanning for dependencies...
2019/06/14 22:47:45 Successfully scanned dependencies
2019/06/14 22:47:45 Launching container with name: acb_step_0
Sending build context to Docker daemon   25.6kB
Step 1/6 : ARG REGISTRY_NAME
Step 2/6 : FROM ${REGISTRY_NAME}/baseimages/node:9-alpine
9-alpine: Pulling from baseimages/node
[...]
Successfully built 41b49a112663
Successfully tagged myregistry.azurecr.io/hello-world:cf10
2019/06/14 22:47:56 Successfully executed container: acb_step_0
2019/06/14 22:47:56 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/14 22:47:56 Pushing image: myregistry.azurecr.io/hello-world:cf10, attempt 1
The push refers to repository [myregistry.azurecr.io/hello-world]
[...]
2019/06/14 22:48:00 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.517011)
2019/06/14 22:48:00 The following dependencies were found:
2019/06/14 22:48:00
- image:
    registry: myregistry.azurecr.io
    repository: hello-world
    tag: cf10
    digest: sha256:611cf6e3ae3cb99b23fadcd89fa144e18aa1b1c9171ad4a0da4b62b31b4e38d1
  runtime-dependency:
    registry: mybaseregistry.azurecr.io
    repository: baseimages/node
    tag: 9-alpine
    digest: sha256:e8e92cffd464fce3be9a3eefd1b65dc9cbe2484da31c11e813a4effc6105c00f
  git:
    git-head-revision: 0f988779c97fe0bfc7f2f74b88531617f4421643

Run ID: cf10 was successful after 32s
```

Executar o comando [de show-tags de repositório az acr][az-acr-repository-show-tags] para verificar se a imagem foi construída e foi empurrada com sucesso para *o miogristry*:

```azurecli
az acr repository show-tags --name myregistry --repository hello-world --output tsv
```

Exemplo de saída:

```console
cf10
```

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [como permitir uma identidade gerida numa tarefa ACR](container-registry-tasks-authentication-managed-identity.md).
* Consulte a [referência ACR Tasks YAML](container-registry-tasks-reference-yaml.md)

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[az-group-create]: /cli/azure/group?#az-group-create
