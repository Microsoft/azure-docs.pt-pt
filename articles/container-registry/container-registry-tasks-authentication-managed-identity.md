---
title: Identidade gerenciada na tarefa ACR
description: Habilite uma identidade gerenciada para recursos do Azure em uma tarefa de registro de contêiner do Azure para permitir que a tarefa acesse outros recursos do Azure, incluindo outros registros de contêiner privado.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 01/14/2020
ms.author: danlep
ms.openlocfilehash: f3294698f6973437a23fab798e8daf5642cc9b49
ms.sourcegitcommit: 323c3f2e518caed5ca4dd31151e5dee95b8a1578
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/10/2020
ms.locfileid: "77111762"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>Usar uma identidade gerenciada pelo Azure em tarefas ACR 

Habilitar uma [identidade gerida para os recursos Azure](../active-directory/managed-identities-azure-resources/overview.md) numa tarefa [ACR,](container-registry-tasks-overview.md)para que a tarefa possa aceder a outros recursos Azure, sem necessidade de fornecer ou gerir credenciais. Por exemplo, use uma identidade gerenciada para habilitar uma etapa de tarefa para efetuar pull ou enviar por push imagens de contêiner para outro registro.

Neste artigo, você aprenderá a usar o CLI do Azure para habilitar uma identidade gerenciada atribuída pelo usuário ou pelo sistema em uma tarefa ACR. Você pode usar o Azure Cloud Shell ou uma instalação local do CLI do Azure. Se você quiser usá-lo localmente, a versão 2.0.68 ou posterior será necessária. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure][azure-cli-install].

Para fins de ilustração, os comandos de exemplo neste artigo usam a [tarefa az acr criar][az-acr-task-create] para criar uma tarefa básica de construção de imagem que permita uma identidade gerida. Para cenários de exemplo para acessar recursos protegidos de uma tarefa ACR usando uma identidade gerenciada, consulte:

* [Autenticação de registo seleto](container-registry-tasks-cross-registry-authentication.md)
* [Aceder a recursos externos com segredos armazenados no Cofre chave azure](container-registry-tasks-authentication-key-vault.md)

## <a name="why-use-a-managed-identity"></a>Por que usar uma identidade gerenciada?

Uma identidade gerenciada para recursos do Azure fornece serviços do Azure selecionados com uma identidade gerenciada automaticamente no Azure Active Directory. Você pode configurar uma tarefa ACR com uma identidade gerenciada para que a tarefa possa acessar outros recursos protegidos do Azure, sem passar credenciais nas etapas da tarefa.

Identidades gerenciadas são de dois tipos:

* *Identidades atribuídas*ao utilizador, que pode atribuir a vários recursos e persistir durante o tempo que quiser. Atualmente, as identidades atribuídas ao usuário estão em versão prévia.

* Uma *identidade atribuída ao sistema,* que é única para um recurso específico, como uma tarefa ACR e dura a vida útil desse recurso.

Você pode habilitar um ou ambos os tipos de identidade em uma tarefa ACR. Conceda acesso de identidade a outro recurso, assim como qualquer entidade de segurança. Quando a tarefa é executada, ela usa a identidade para acessar o recurso em qualquer etapa de tarefa que exija acesso.

## <a name="steps-to-use-a-managed-identity"></a>Etapas para usar uma identidade gerenciada

Siga estas etapas de alto nível para usar uma identidade gerenciada com uma tarefa ACR.

### <a name="1-optional-create-a-user-assigned-identity"></a>1. (opcional) criar uma identidade atribuída pelo usuário

Se você planeja usar uma identidade atribuída pelo usuário, use uma identidade existente ou crie a identidade usando o CLI do Azure ou outras ferramentas do Azure. Por exemplo, use a [identidade az criar][az-identity-create] comando. 

Se você planeja usar apenas uma identidade atribuída pelo sistema, ignore esta etapa. Você cria uma identidade atribuída pelo sistema ao criar a tarefa ACR.

### <a name="2-enable-identity-on-an-acr-task"></a>2. habilitar a identidade em uma tarefa ACR

Ao criar uma tarefa ACR, opcionalmente, habilite uma identidade atribuída pelo usuário, uma identidade atribuída pelo sistema ou ambas. Por exemplo, passe o parâmetro `--assign-identity` quando executar a [tarefa az acr criar][az-acr-task-create] comando no Azure CLI.

Para permitir uma identidade atribuída ao sistema, passe `--assign-identity` sem qualquer valor ou `assign-identity [system]`. O seguinte comando de exemplo cria uma tarefa Linux a partir de um repositório público gitHub que constrói a imagem `hello-world` e permite uma identidade gerida atribuída pelo sistema:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --file Dockerfile \
    --commit-trigger-enabled false \
    --assign-identity
```

Para permitir uma identidade atribuída ao utilizador, passe `--assign-identity` com um valor da *identificação* de recursos da identidade. O seguinte comando de exemplo cria uma tarefa Linux a partir de um repositório público gitHub que constrói a imagem `hello-world` e permite uma identidade gerida atribuída pelo utilizador:

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

### <a name="3-grant-the-identity-permissions-to-access-other-azure-resources"></a>3. conceder as permissões de identidade para acessar outros recursos do Azure

Dependendo dos requisitos da sua tarefa, conceda permissões de identidade para acessar outros recursos do Azure. Os exemplos incluem:

* Atribua a identidade gerenciada uma função com pull, push e pull ou outras permissões a um registro de contêiner de destino no Azure. Para obter uma lista completa das funções de registo, consulte [as funções e permissões](container-registry-roles.md)do Registo de Contentores de Azure . 
* Atribua à identidade gerenciada uma função para ler segredos em um cofre de chaves do Azure.

Utilize o [Azure CLI](../role-based-access-control/role-assignments-cli.md) ou outras ferramentas Azure para gerir o acesso baseado em papéis aos recursos. Por exemplo, executar a atribuição de [funções az criar][az-role-assignment-create] comando para atribuir a identidade um papel ao recurso. 

O exemplo a seguir atribui uma identidade gerenciada às permissões para efetuar pull de um registro de contêiner. O comando especifica a *identificação principal* da identidade de tarefa e a *identificação* do recurso do registo-alvo.


```azurecli
az role assignment create \
  --assignee <principalID> \
  --scope <registryID> \
  --role acrpull
```

### <a name="4-optional-add-credentials-to-the-task"></a>4. (opcional) adicionar credenciais à tarefa

Se sua tarefa precisar de credenciais para efetuar pull ou enviar imagens por push para outro registro personalizado, ou para acessar outros recursos, adicione as credenciais à tarefa. Executar a credencial de [tarefa az acr adicionar][az-acr-task-credential-add] comando para adicionar credenciais, e passar o parâmetro `--use-identity` para indicar que a identidade pode aceder às credenciais. 

Por exemplo, adicionar credenciais para uma identidade atribuída ao sistema para autenticar com o *registo-alvo*do registo de contentores Azure, passe `use-identity [system]`:

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity [system]
```

Para adicionar credenciais para uma identidade atribuída ao utilizador para autenticar com o *registo-alvo*do registo, passe `use-identity` com um valor da *identificação* do cliente da identidade. Por exemplo:

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity <clientID>
```

Pode obter a identificação do cliente da identidade executando o comando de [show de identidade az.][az-identity-show] O ID do cliente é um GUIA do formulário `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

### <a name="5-run-the-task"></a>5. executar a tarefa

Depois de configurar uma tarefa com uma identidade gerenciada, execute a tarefa. Por exemplo, para testar uma das tarefas criadas neste artigo, desencadeie-a manualmente utilizando o comando de execução de [tarefas az acr.][az-acr-task-run] Se você tiver configurado gatilhos de tarefa automatizados adicionais, a tarefa será executada quando disparado automaticamente.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu como habilitar e usar uma identidade gerenciada atribuída pelo usuário ou pelo sistema em uma tarefa ACR. Para cenários para acessar recursos protegidos de uma tarefa ACR usando uma identidade gerenciada, consulte:

* [Autenticação de registo seleto](container-registry-tasks-cross-registry-authentication.md)
* [Aceder a recursos externos com segredos armazenados no Cofre chave azure](container-registry-tasks-authentication-key-vault.md)


<!-- LINKS - Internal -->
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[azure-cli-install]: /cli/azure/install-azure-cli
