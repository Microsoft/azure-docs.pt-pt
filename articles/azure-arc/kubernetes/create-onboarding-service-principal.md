---
title: Criar um Arco Azure habilitado a bordo principal (Pré-visualização)
services: azure-arc
ms.service: azure-arc
ms.date: 02/09/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: 'Criar um Arco Azure habilitado a bordo principal '
keywords: Kubernetes, Arc, Azure, contentores
ms.openlocfilehash: 8772cf7634d9a833af120784e3e7868b41d202c4
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100390492"
---
# <a name="create-an-azure-arc-enabled-onboarding-service-principal-preview"></a>Criar um Arco Azure habilitado a bordo principal (Pré-visualização)

## <a name="overview"></a>Descrição Geral

Você pode a bordo de clusters Kubernetes para Azure Arc usando diretores de serviço com atribuições de funções de privilégio limitado. Esta capacidade é útil em gasodutos de integração contínua e implementação contínua (CI/CD), como Azure Pipelines e GitHub Actions.

Caminhe pelos seguintes passos para aprender a usar os principais de serviço para embarcar clusters Kubernetes para Azure Arc.

## <a name="create-a-new-service-principal"></a>Criar um novo diretor de serviços

Crie um novo diretor de serviços com um nome informativo único para o seu inquilino Azure Ative Directory.

```console
az ad sp create-for-RBAC --skip-assignment --name "https://azure-arc-for-k8s-onboarding"
```

**Saída:**

```console
{
  "appId": "22cc2695-54b9-49c1-9a73-2269592103d8",
  "displayName": "azure-arc-for-k8s-onboarding",
  "name": "https://azure-arc-for-k8s-onboarding",
  "password": "09d3a928-b223-4dfe-80e8-fed13baa3b3d",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

## <a name="assign-permissions"></a>Atribuir permissões

Atribua o papel "Kubernetes Cluster - Azure Arc Onboarding" ao recém-criado diretor de serviços. Esta função incorporada do Azure com permissões limitadas só permite ao diretor registar clusters para a Azure. O principal com esta função atribuída não pode atualizar, eliminar ou modificar quaisquer outros clusters ou recursos dentro da subscrição.

Dadas as capacidades limitadas, os clientes podem facilmente reutilizar este principal para embarcar em vários clusters.

Pode limitar ainda mais as permissões, passando o argumento apropriado `--scope` ao atribuir o papel. Isto permite que os clientes restringam o registo de cluster. Os seguintes cenários são suportados por `--scope` vários parâmetros:

| Recurso  | Argumento `scope`| Efeito |
| ------------- | ------------- | ------------- |
| Subscrição | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333` | O diretor de serviço pode registar qualquer cluster num Grupo de Recursos existente na subscrição dada. |
| Grupo de Recursos | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333/resourceGroups/myGroup`  | O diretor de serviço __só__ pode registar clusters no Grupo de `myGroup` Recursos. |

```console
az role assignment create \
    --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 \      # this is the id for the built-in role
    --assignee 22cc2695-54b9-49c1-9a73-2269592103d8 \  # use the appId from the new SP
    --scope /subscriptions/<<SUBSCRIPTION_ID>>         # apply the appropriate scope
```

**Saída:**

```console
{
  "canDelegate": null,
  "id": "/subscriptions/<<SUBSCRIPTION_ID>>/providers/Microsoft.Authorization/roleAssignments/fbd819a9-01e8-486b-9eb9-f177ba400ba6",
  "name": "fbd819a9-01e8-486b-9eb9-f177ba400ba6",
  "principalId": "ddb0ddb4-ba84-4cde-b936-affc272a4b90",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<<SUBSCRIPTION_ID>>/providers/Microsoft.Authorization/roleDefinitions/34e09817-6cbe-4d01-b1a2-e0eac5743d41",
  "scope": "/subscriptions/<<SUBSCRIPTION_ID>>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="use-service-principal-with-the-azure-cli"></a>Use o principal de serviço com o Azure CLI

Consulte o novo chefe de serviço com os seguintes comandos:

```azurecli
az login --service-principal -u mySpnClientId -p mySpnClientSecret --tenant myTenantID
az connectedk8s connect -n myConnectedClusterName -g myResoureGroupName
```

## <a name="next-steps"></a>Passos seguintes

* [Use a política do Azure para governar a configuração do cluster](./use-azure-policy.md)
