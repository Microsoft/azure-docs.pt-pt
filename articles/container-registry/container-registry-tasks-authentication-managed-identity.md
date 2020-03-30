---
title: Identidade gerida na tarefa ACR
description: Permitir uma identidade gerida para os Recursos Azure numa tarefa de Registo de Contentores Azure para permitir o acesso a outros recursos Azure, incluindo outros registos privados de contentores.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 01/14/2020
ms.author: danlep
ms.openlocfilehash: f3294698f6973437a23fab798e8daf5642cc9b49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77111762"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>Utilize uma identidade gerida pelo Azure em Tarefas ACR 

Habilitar uma [identidade gerida para os recursos Azure](../active-directory/managed-identities-azure-resources/overview.md) numa tarefa [ACR,](container-registry-tasks-overview.md)para que a tarefa possa aceder a outros recursos Azure, sem necessidade de fornecer ou gerir credenciais. Por exemplo, utilize uma identidade gerida para permitir um passo de tarefa para puxar ou empurrar imagens de contentores para outro registo.

Neste artigo, aprende-se a utilizar o AZURE CLI para permitir uma identidade gerida atribuída ao utilizador ou atribuída ao sistema numa tarefa ACR. Pode utilizar a Casca de Nuvem Azure ou uma instalação local do Azure CLI. Se quiser usá-lo localmente, a versão 2.0.68 ou posterior é necessária. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].

Para fins de ilustração, os comandos de exemplo neste artigo usam a [tarefa az acr criar][az-acr-task-create] para criar uma tarefa básica de construção de imagem que permita uma identidade gerida. Para que os cenários de amostras acedam a recursos garantidos a partir de uma tarefa ACR utilizando uma identidade gerida, consulte:

* [Autenticação entre registos](container-registry-tasks-cross-registry-authentication.md)
* [Aceder a recursos externos com segredos armazenados no Cofre chave azure](container-registry-tasks-authentication-key-vault.md)

## <a name="why-use-a-managed-identity"></a>Por que usar uma identidade gerida?

Uma identidade gerida para os recursos Azure fornece serviços Azure selecionados com uma identidade gerida automaticamente no Diretório Ativo Azure. Pode configurar uma tarefa ACR com uma identidade gerida para que a tarefa possa aceder a outros recursos Azure seguros, sem passar credenciais nos passos de tarefa.

As identidades geridas são de dois tipos:

* *Identidades atribuídas*ao utilizador, que pode atribuir a vários recursos e persistir durante o tempo que quiser. As identidades atribuídas ao utilizador estão atualmente em pré-visualização.

* Uma *identidade atribuída ao sistema,* que é única para um recurso específico, como uma tarefa ACR e dura a vida útil desse recurso.

Pode ativar ambos os tipos de identidade numa tarefa ACR. Conceder o acesso de identidade a outro recurso, como qualquer diretor de segurança. Quando a tarefa é executado, utiliza a identidade para aceder ao recurso em quaisquer passos de tarefa que exijam acesso.

## <a name="steps-to-use-a-managed-identity"></a>Passos para usar uma identidade gerida

Siga estes passos de alto nível para usar uma identidade gerida com uma tarefa ACR.

### <a name="1-optional-create-a-user-assigned-identity"></a>1. (Opcional) Criar uma identidade atribuída ao utilizador

Se pretender utilizar uma identidade atribuída ao utilizador, utilize uma identidade existente ou crie a identidade utilizando o Azure CLI ou outras ferramentas Azure. Por exemplo, use a [identidade az criar][az-identity-create] comando. 

Se planeia usar apenas uma identidade atribuída ao sistema, ignore este passo. Cria-se uma identidade atribuída ao sistema quando cria a tarefa ACR.

### <a name="2-enable-identity-on-an-acr-task"></a>2. Ativar a identidade numa tarefa ACR

Quando criar uma tarefa ACR, faculte opcionalmente uma identidade atribuída ao utilizador, uma identidade atribuída ao sistema, ou ambas. Por exemplo, `--assign-identity` passe o parâmetro quando executar a [tarefa az acr criar][az-acr-task-create] comando no ClI Azure.

Para permitir uma identidade atribuída `--assign-identity` ao sistema, `assign-identity [system]`passe sem valor ou . O seguinte comando de exemplo cria uma tarefa Linux a partir `hello-world` de um repositório público gitHub que constrói a imagem e permite uma identidade gerida atribuída pelo sistema:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --file Dockerfile \
    --commit-trigger-enabled false \
    --assign-identity
```

Para permitir uma identidade atribuída `--assign-identity` ao utilizador, passe com um valor da *identificação* de recurso da identidade. O seguinte comando de exemplo cria uma tarefa Linux a partir `hello-world` de um repositório público gitHub que constrói a imagem e permite uma identidade gerida atribuída pelo utilizador:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --file Dockerfile \
    --commit-trigger-enabled false
    --assign-identity <resourceID>
```

Pode obter a identificação de recursos da identidade executando o comando de [show de identidade az.][az-identity-show] O ID de recurso para o ID *myUserAssignedIdentity* no grupo de recursos *myResourceGroup* é do formulário: 

```
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

### <a name="3-grant-the-identity-permissions-to-access-other-azure-resources"></a>3. Conceder as permissões de identidade para aceder a outros recursos do Azure

Dependendo dos requisitos da sua tarefa, conceda as permissões de identidade para aceder a outros recursos do Azure. Os exemplos incluem:

* Atribuir à identidade gerida um papel com puxão, impulso e puxão, ou outras permissões para um registo de contentores-alvo em Azure. Para obter uma lista completa das funções de registo, consulte [as funções e permissões](container-registry-roles.md)do Registo de Contentores de Azure . 
* Atribuir à identidade gerida um papel para ler segredos num cofre de chaves Azure.

Utilize o [Azure CLI](../role-based-access-control/role-assignments-cli.md) ou outras ferramentas Azure para gerir o acesso baseado em papéis aos recursos. Por exemplo, executar a atribuição de [funções az criar][az-role-assignment-create] comando para atribuir a identidade um papel ao recurso. 

O exemplo seguinte atribui uma identidade gerida às permissões para retirar de um registo de contentores. O comando especifica a *identificação principal* da identidade de tarefa e a *identificação* do recurso do registo-alvo.


```azurecli
az role assignment create \
  --assignee <principalID> \
  --scope <registryID> \
  --role acrpull
```

### <a name="4-optional-add-credentials-to-the-task"></a>4. (Opcional) Adicionar credenciais à tarefa

Se a sua tarefa precisar de credenciais para puxar ou empurrar imagens para outro registo personalizado, ou para aceder a outros recursos, adicione credenciais à tarefa. Executar a credencial de [tarefa az acr adicionar][az-acr-task-credential-add] `--use-identity` comando para adicionar credenciais, e passar o parâmetro para indicar que a identidade pode aceder às credenciais. 

Por exemplo, adicionar credenciais para uma identidade atribuída ao sistema para autenticar com `use-identity [system]`o *registo-alvo*do registo de contentores Azure, passe:

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity [system]
```

Para adicionar credenciais para uma identidade atribuída ao utilizador para autenticar `use-identity` com o *registo-alvo*do registo, passe com um valor da *identificação* do cliente da identidade. Por exemplo:

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity <clientID>
```

Pode obter a identificação do cliente da identidade executando o comando de [show de identidade az.][az-identity-show] O ID do cliente é `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`um GUID do formulário.

### <a name="5-run-the-task"></a>5. Executar a tarefa

Depois de configurar uma tarefa com uma identidade gerida, executar a tarefa. Por exemplo, para testar uma das tarefas criadas neste artigo, desencadeie-a manualmente utilizando o comando de execução de [tarefas az acr.][az-acr-task-run] Se configurar gatilhos de tarefa seletos adicionais e automatizados, a tarefa é executado quando ativado automaticamente.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a ativar e utilizar uma identidade gerida atribuída ao utilizador ou atribuída ao sistema numa tarefa ACR. Para que os cenários acedam a recursos garantidos a partir de uma tarefa ACR utilizando uma identidade gerida, consulte:

* [Autenticação entre registos](container-registry-tasks-cross-registry-authentication.md)
* [Aceder a recursos externos com segredos armazenados no Cofre chave azure](container-registry-tasks-authentication-key-vault.md)


<!-- LINKS - Internal -->
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[azure-cli-install]: /cli/azure/install-azure-cli
