---
title: Criar um diretor de serviço de embarque ativado por Arco Azul (Pré-visualização)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: 'Criar um Diretor de Serviço de Embarque ativado por Arco Azure '
keywords: Kubernetes, Arc, Azure, contentores
ms.openlocfilehash: f9f750980d8a8b5d8190ba0b399fe068f1dd99c7
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680792"
---
# <a name="create-an-azure-arc-enabled-onboarding-service-principal-preview"></a>Criar um diretor de serviço de embarque ativado por Arco Azul (Pré-visualização)

## <a name="overview"></a>Descrição geral

Quando um cluster está a bordo do Azure, os agentes que correm no seu cluster devem autenticar o Gestor de Recursos Azure como parte do registo. A `connectedk8s` extensão Azure CLI tem criação automatizada de principais de serviço. No entanto, pode haver alguns cenários em que a automatização cli não funcione:

* A sua organização geralmente restringe a criação de Diretores de Serviços
* O utilizador que está a embarcar no cluster não tem permissões suficientes para criar Diretores de Serviço

Em vez disso, vamos criar o Diretor de Serviço fora da banda, e depois passar o diretor para a extensão Azure CLI.

## <a name="create-a-new-service-principal"></a>Criar um novo Diretor de Serviço

Crie um novo Diretor de Serviço com um nome informativo. Note que este nome deve ser único para o seu inquilino do Diretório Ativo Azure:

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

Depois de criar o novo Diretor de Serviço, atribuir o papel "Azure Arc for Kubernetes Onboarding" ao recém-criado diretor. Este é um papel azure incorporado com permissões limitadas, que apenas permite ao principal registar clusters para Azure. O principal não pode atualizar, excluir ou modificar quaisquer outros clusters ou recursos dentro da subscrição.

Dadas as capacidades limitadas, os clientes podem facilmente reutilizar este principal para a bordo de vários clusters.

As permissões podem ser ainda limitadas através da aprovação do argumento adequado `--scope` ao atribuir o papel. Isto permite que os clientes restringam o registo do cluster. Os seguintes cenários são suportados por vários `--scope` parâmetros:

| Recurso  | Argumento `scope`| Efeito |
| ------------- | ------------- | ------------- |
| Subscrição | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333` | O diretor de serviço pode registar qualquer cluster num Grupo de Recursos existente na subscrição dada |
| Grupo de Recursos | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333/resourceGroups/myGroup`  | O diretor de serviço __só__ pode registar clusters no Grupo de Recursos`myGroup` |

```console
az role assignment create \
    --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 \      # this is the id for the built-in role
    --assignee 22cc2695-54b9-49c1-9a73-2269592103d8 \  # use the appId from the new SP
    --scope /subscriptions/<<SUBSCRIPTION_ID>>         # apply the apropriate scope
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

## <a name="use-service-principal-with-the-azure-cli"></a>Utilizar o Serviço Principal com o Azure CLI

Referência ao recém-criado Diretor de Serviço:

```console
az login --service-principal -u mySpnClientId -p mySpnClientSecret --tenant myTenantID
az connectedk8s connect -n myConnectedClusterName -g myResoureGroupName
```

## <a name="next-steps"></a>Passos seguintes

* [Use a Política Azure para governar a configuração do cluster](./use-azure-policy.md)
