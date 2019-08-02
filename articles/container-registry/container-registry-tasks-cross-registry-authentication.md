---
title: Autenticação entre registros em uma tarefa de registro de contêiner do Azure
description: Habilite uma identidade gerenciada para recursos do Azure em uma tarefa ACR (registro de contêiner do Azure) para permitir que a tarefa acesse outro registro de contêiner privado.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 07/12/2019
ms.author: danlep
ms.openlocfilehash: 8fac70e7e5125ae86b2b5ce13041bbf1fd067bbe
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68642075"
---
# <a name="cross-registry-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>Autenticação entre registros em uma tarefa ACR usando uma identidade gerenciada pelo Azure 

Em uma [tarefa ACR](container-registry-tasks-overview.md), você pode [habilitar uma identidade gerenciada para recursos do Azure](container-registry-tasks-authentication-managed-identity.md). A tarefa pode usar a identidade para acessar outros recursos do Azure, sem a necessidade de fornecer ou gerenciar credenciais. 

Neste artigo, você aprenderá a habilitar uma identidade gerenciada em uma tarefa que extrai uma imagem de um registro diferente daquele usado para executar a tarefa.

Para criar os recursos do Azure, este artigo requer que você execute o CLI do Azure versão 2.0.68 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure][azure-cli].

## <a name="scenario-overview"></a>Descrição geral do cenário

A tarefa de exemplo efetua pull de uma imagem base de outro registro de contêiner do Azure para criar e enviar por push uma imagem de aplicativo. Para efetuar pull da imagem base, você configura a tarefa com uma identidade gerenciada e atribui as permissões apropriadas a ela. 

Este exemplo mostra etapas usando uma identidade gerenciada atribuída pelo usuário ou pelo sistema. Sua escolha de identidade depende das necessidades da sua organização.

Em um cenário do mundo real, uma organização pode manter um conjunto de imagens base usadas por todas as equipes de desenvolvimento para criar seus aplicativos. Essas imagens base são armazenadas em um registro corporativo, com cada equipe de desenvolvimento que tem apenas direitos de pull. 

## <a name="prerequisites"></a>Pré-requisitos

Para este artigo, você precisa de dois registros de contêiner do Azure:

* Você usa o primeiro registro para criar e executar tarefas ACR. Neste artigo, esse registro é denominado *myregistry*. 
* O segundo registro hospeda uma imagem base usada para a tarefa criar uma imagem. Neste artigo, o segundo registro é denominado *mybaseregistry*. 

Substitua pelos seus próprios nomes de registro em etapas posteriores.

Se você ainda não tiver os registros de contêiner do Azure necessários, [consulte início rápido: Crie um registro de contêiner privado usando o](container-registry-get-started-azure-cli.md)CLI do Azure. Você ainda não precisa enviar imagens por push ao registro.

## <a name="prepare-base-registry"></a>Preparar registro base

Primeiro, crie um diretório de trabalho e, em seguida, crie um arquivo chamado Dockerfile com o conteúdo a seguir. Este exemplo simples cria uma imagem base do node. js de uma imagem pública no Hub do Docker.
    
```bash
echo FROM node:9-alpine > Dockerfile
```
No diretório atual, execute o comando [AZ ACR Build][az-acr-build] para criar e enviar por push a imagem base para o registro base. Na prática, outra equipe ou processo na organização pode manter o registro base.
    
```azurecli
az acr build --image baseimages/node:9-alpine --registry mybaseregistry --file Dockerfile .
```

## <a name="define-task-steps-in-yaml-file"></a>Definir etapas de tarefa no arquivo YAML

As etapas para este exemplo de [tarefa de várias etapas](container-registry-tasks-multi-step.md) são definidas em um [arquivo YAML](container-registry-tasks-reference-yaml.md). Crie um arquivo chamado `helloworldtask.yaml` em seu diretório de trabalho local e cole o conteúdo a seguir. Atualize o valor de `REGISTRY_NAME` na etapa de compilação com o nome do servidor do registro base.

```yml
version: v1.0.0
steps:
# Replace mybaseregistry with the name of your registry containing the base image
  - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}}  https://github.com/Azure-Samples/acr-build-helloworld-node.git -f Dockerfile-app --build-arg REGISTRY_NAME=mybaseregistry.azurecr.io
  - push: ["{{.Run.Registry}}/hello-world:{{.Run.ID}}"]
```

A etapa de compilação usa `Dockerfile-app` o arquivo no repositório [Azure-Samples/ACR-Build-HelloWorld-node](https://github.com/Azure-Samples/acr-build-helloworld-node.git) para criar uma imagem. O `--build-arg` faz referência ao registro base para efetuar pull da imagem base. Quando compilada com êxito, a imagem é enviada por push para o registro usado para executar a tarefa.

## <a name="option-1-create-task-with-user-assigned-identity"></a>Opção 1: Criar tarefa com identidade atribuída pelo usuário

As etapas nesta seção criam uma tarefa e habilitam uma identidade atribuída pelo usuário. Se você quiser habilitar uma identidade atribuída pelo sistema, consulte [a opção 2: Crie uma tarefa com a identidade](#option-2-create-task-with-system-assigned-identity)atribuída pelo sistema. 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>Criar tarefa

Crie a tarefa *helloworldtask* executando o comando [AZ ACR Task Create][az-acr-task-create] a seguir. O contexto da tarefa é o sistema local e o comando faz referência ao `helloworldtask.yaml` arquivo no diretório de trabalho. O `--assign-identity` parâmetro passa a ID de recurso da identidade atribuída pelo usuário. 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]

## <a name="option-2-create-task-with-system-assigned-identity"></a>Opção 2: Criar tarefa com identidade atribuída pelo sistema

As etapas nesta seção criam uma tarefa e habilitam uma identidade atribuída pelo sistema. Se você quiser habilitar uma identidade atribuída pelo usuário, consulte [a opção 1: Criar tarefa com identidade](#option-1-create-task-with-user-assigned-identity)atribuída pelo usuário. 

### <a name="create-task"></a>Criar tarefa

Crie a tarefa *helloworldtask* executando o comando [AZ ACR Task Create][az-acr-task-create] a seguir. O contexto da tarefa é o sistema local e o comando faz referência ao `helloworldtask.yaml` arquivo no diretório de trabalho. O `--assign-identity` parâmetro sem valor habilita a identidade atribuída pelo sistema na tarefa. 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity 
```
[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

## <a name="give-identity-pull-permissions-to-the-base-registry"></a>Conceder permissões de pull de identidade para o registro de base

Nesta seção, forneça as permissões de identidade gerenciadas para efetuar pull do registro de base, *mybaseregistry*.

Use o comando [AZ ACR show][az-acr-show] para obter a ID de recurso do registro base e armazená-la em uma variável:

```azurecli
baseregID=$(az acr show --name mybaseregistry --query id --output tsv)
```

Use o comando [AZ role Assignment Create][az-role-assignment-create] para atribuir a identidade `acrpull` à função ao registro base. Essa função tem permissões apenas para efetuar pull de imagens do registro.

```azurecli
az role assignment create --assignee $principalID --scope $baseregID --role acrpull
```

## <a name="add-target-registry-credentials-to-task"></a>Adicionar credenciais do registro de destino à tarefa

Agora, use o comando [AZ ACR Task Credential Add][az-acr-task-credential-add] para adicionar as credenciais da identidade à tarefa para que ela possa ser autenticada com o registro base. Execute o comando correspondente ao tipo de identidade gerenciada que você habilitou na tarefa. Se você habilitou uma identidade atribuída pelo usuário, `--use-identity` passe com a ID do cliente da identidade. Se você tiver habilitado uma identidade atribuída pelo sistema, `--use-identity [system]`passe.

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

## <a name="manually-run-the-task"></a>Executar a tarefa manualmente

Para verificar se a tarefa na qual você habilitou uma identidade gerenciada é executada com êxito, acione manualmente a tarefa com o comando [AZ ACR Task execute][az-acr-task-run] . 

```azurecli
az acr task run \
  --name helloworldtask \
  --registry myregistry
```

Se a tarefa for executada com êxito, a saída será semelhante a:

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

Execute o comando [AZ ACR Repository show-Tags][az-acr-repository-show-tags] para verificar se a imagem foi criada e enviada com êxito para *myregistry*:

```azurecli
az acr repository show-tags --name myregistry --repository hello-world --output tsv
```

Exemplo de saída:

```console
cf10
```

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre como [habilitar uma identidade gerenciada em uma tarefa ACR](container-registry-tasks-authentication-managed-identity.md).
* Consulte a [referência de YAML de tarefas do ACR](container-registry-tasks-reference-yaml.md)

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
