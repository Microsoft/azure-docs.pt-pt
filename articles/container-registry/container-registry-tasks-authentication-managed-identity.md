---
title: Identidade gerida na tarefa ACR
description: Capacitar uma identidade gerida para os Recursos Azure numa tarefa de Registo de Contentores Azure para permitir que a tarefa aceda a outros recursos Azure, incluindo outros registos privados de contentores.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 01/14/2020
ms.author: danlep
ms.openlocfilehash: 19d63861a98884ff2f5103946c19e2226c4b14b7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781168"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>Utilize uma identidade gerida pelo Azure em Tarefas ACR 

Capacitar uma [identidade gerida para os recursos Azure](../active-directory/managed-identities-azure-resources/overview.md) numa tarefa [ACR,](container-registry-tasks-overview.md)para que a tarefa possa aceder a outros recursos Azure, sem necessidade de fornecer ou gerir credenciais. Por exemplo, utilize uma identidade gerida para permitir um passo de tarefa para puxar ou empurrar imagens de contentores para outro registo.

Neste artigo, aprende-se a utilizar o CLI Azure para permitir uma identidade gerida atribuída pelo utilizador ou pelo sistema numa tarefa ACR. Pode utilizar o Azure Cloud Shell ou uma instalação local do Azure CLI. Se quiser usá-lo localmente, é necessário utilizar a versão 2.0.68 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].

Para fins de ilustração, os comandos de exemplo neste artigo usam [a tarefa az acr criar][az-acr-task-create] para criar uma tarefa básica de construção de imagem que permite uma identidade gerida. Para cenários de amostragem para aceder a recursos seguros a partir de uma tarefa ACR utilizando uma identidade gerida, consulte:

* [Autenticação entre registos](container-registry-tasks-cross-registry-authentication.md)
* [Aceda a recursos externos com segredos armazenados no Cofre da Chave Azure](container-registry-tasks-authentication-key-vault.md)

## <a name="why-use-a-managed-identity"></a>Por que usar uma identidade gerida?

Uma identidade gerida para recursos Azure fornece serviços Azure selecionados com uma identidade gerida automaticamente no Azure Ative Directory. Pode configurar uma tarefa ACR com uma identidade gerida para que a tarefa possa aceder a outros recursos Azure seguros, sem passar credenciais nas etapas de tarefa.

As identidades geridas são de dois tipos:

* *Identidades atribuídas ao utilizador*, que pode atribuir a múltiplos recursos e persistir durante o tempo que desejar. As identidades atribuídas ao utilizador estão atualmente em pré-visualização.

* Uma *identidade atribuída ao sistema*, que é única a um recurso específico, como uma tarefa ACR e dura a vida útil desse recurso.

Pode ativar qualquer um ou ambos os tipos de identidade numa tarefa ACR. Concede o acesso à identidade a outro recurso, como qualquer diretor de segurança. Quando a tarefa é executado, utiliza a identidade para aceder ao recurso em quaisquer etapas de tarefa que exijam acesso.

## <a name="steps-to-use-a-managed-identity"></a>Passos para usar uma identidade gerida

Siga estes passos de alto nível para utilizar uma identidade gerida com uma tarefa ACR.

### <a name="1-optional-create-a-user-assigned-identity"></a>1. (Opcional) Criar uma identidade atribuída ao utilizador

Se planeia utilizar uma identidade atribuída ao utilizador, utilize uma identidade existente ou crie a identidade utilizando o CLI Azure ou outras ferramentas Azure. Por exemplo, use o comando [de criação de identidade az.][az-identity-create] 

Se planeia usar apenas uma identidade atribuída ao sistema, ignore este passo. Cria-se uma identidade atribuída ao sistema quando cria a tarefa ACR.

### <a name="2-enable-identity-on-an-acr-task"></a>2. Permitir a identidade numa tarefa de ACR

Quando criar uma tarefa ACR, opcionalmente ative uma identidade atribuída ao utilizador, uma identidade atribuída ao sistema, ou ambas. Por exemplo, passe o `--assign-identity` parâmetro quando executar a tarefa [az acr criar][az-acr-task-create] comando no CLI Azure.

Para ativar uma identidade atribuída ao sistema, passe `--assign-identity` sem valor ou `assign-identity [system]` . O seguinte exemplo de comando cria uma tarefa Linux a partir de um repositório público do GitHub que constrói a `hello-world` imagem e permite uma identidade gerida atribuída pelo sistema:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git#main \
    --file Dockerfile \
    --commit-trigger-enabled false \
    --assign-identity
```

Para ativar uma identidade atribuída ao utilizador, passe `--assign-identity` com um valor do *ID* de recurso da identidade. O seguinte exemplo de comando cria uma tarefa Linux a partir de um repositório público do GitHub que constrói a `hello-world` imagem e permite uma identidade gerida atribuída pelo utilizador:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git#main \
    --file Dockerfile \
    --commit-trigger-enabled false
    --assign-identity <resourceID>
```

Você pode obter o documento de identificação de recursos da identidade executando o comando [de show de identidade az.][az-identity-show] O ID de recursos para o *ID myUserAssignedIdentity* no grupo de recursos *myResourceGroup* é do formulário: 

```
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

### <a name="3-grant-the-identity-permissions-to-access-other-azure-resources"></a>3. Conceder as permissões de identidade para aceder a outros recursos da Azure

Dependendo dos requisitos da sua tarefa, conceda as permissões de identidade para aceder a outros recursos da Azure. Os exemplos incluem:

* Atribua à identidade gerida um papel com puxar, empurrar e puxar, ou outras permissões para um registo de contentores-alvo em Azure. Para obter uma lista completa das funções de registo, consulte [as funções e permissões do Registo do Contentor de Azure](container-registry-roles.md). 
* Atribua à identidade gerida um papel para ler segredos num cofre de chaves Azure.

Utilize o [CLI Azure](../role-based-access-control/role-assignments-cli.md) ou outras ferramentas Azure para gerir o acesso baseado em funções aos recursos. Por exemplo, executar a [atribuição de função az criar][az-role-assignment-create] comando para atribuir a identidade um papel ao recurso. 

O exemplo a seguir atribui a uma identidade gerida as permissões para retirar de um registo de contentores. O comando especifica o *ID principal* da identidade da tarefa e a *identificação* de recursos do registo-alvo.


```azurecli
az role assignment create \
  --assignee <principalID> \
  --scope <registryID> \
  --role acrpull
```

### <a name="4-optional-add-credentials-to-the-task"></a>4. (Opcional) Adicionar credenciais à tarefa

Se a sua tarefa precisar de credenciais para puxar ou empurrar imagens para outro registo personalizado, ou para aceder a outros recursos, adicione credenciais à tarefa. Executar a [credencial de tarefa az acr adicionar][az-acr-task-credential-add] comando para adicionar credenciais, e passar o `--use-identity` parâmetro para indicar que a identidade pode aceder às credenciais. 

Por exemplo, para adicionar credenciais para uma identidade atribuída ao sistema para autenticar com o registo de registo de *contentores* Azure, `use-identity [system]` passe:

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity [system]
```

Para adicionar credenciais para uma identidade atribuída ao utilizador para autenticar com o *targetregistry* do registo, passe `use-identity` com o valor da *identificação* do cliente da identidade. Por exemplo:

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity <clientID>
```

Pode obter a identificação do cliente da identidade, executando o comando [de exibição de identidade az.][az-identity-show] O ID do cliente é um GUID do `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` formulário.

### <a name="5-run-the-task"></a>5. Executar a tarefa

Depois de configurar uma tarefa com uma identidade gerida, executar a tarefa. Por exemplo, para testar uma das tarefas criadas neste artigo, desencadeie-a manualmente utilizando o comando de execução de [tarefas az acr.][az-acr-task-run] Se configurar gatilhos de tarefas adicionais e automatizados, a tarefa é executada quando ativada automaticamente.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a ativar e a utilizar uma identidade gerida atribuída pelo utilizador ou atribuída ao sistema numa tarefa ACR. Para cenários de acesso a recursos seguros a partir de uma tarefa ACR utilizando uma identidade gerida, consulte:

* [Autenticação entre registos](container-registry-tasks-cross-registry-authentication.md)
* [Aceda a recursos externos com segredos armazenados no Cofre da Chave Azure](container-registry-tasks-authentication-key-vault.md)


<!-- LINKS - Internal -->
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-identity-show]: /cli/azure/identity#az_identity_show
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az_acr_task_credential_add
[azure-cli-install]: /cli/azure/install-azure-cli
