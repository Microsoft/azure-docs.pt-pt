---
title: Gerenciar identidades gerenciadas atribuídas pelo usuário usando REST-Azure AD
description: Instruções passo a passo sobre como criar, listar e excluir uma identidade gerenciada atribuída pelo usuário para fazer chamadas à API REST.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/26/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39e108451e4c19e77e01b5bcc5d8dd21e86ad73a
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74547420"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-rest-api-calls"></a>Criar, listar ou excluir uma identidade gerenciada atribuída pelo usuário usando chamadas à API REST

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Identidades gerenciadas para recursos do Azure fornece aos serviços do Azure a capacidade de autenticar para serviços que dão suporte à autenticação do Azure AD, sem precisar de credenciais em seu código. 

Neste artigo, você aprende a criar, listar e excluir uma identidade gerenciada atribuída pelo usuário usando a rotação para fazer chamadas à API REST.

## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com identidades gerenciadas para recursos do Azure, confira a [seção visão geral](overview.md). **Certifique-se de examinar a [diferença entre uma identidade gerenciada atribuída pelo sistema e](overview.md#how-does-the-managed-identities-for-azure-resources-work)** atribuída pelo usuário.
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Se você estiver usando o Windows, instale o [subsistema do Windows para Linux](https://msdn.microsoft.com/commandline/wsl/about) ou use o [Azure cloud Shell](../../cloud-shell/overview.md) no portal do Azure.
- Se você usar o [subsistema do Windows para Linux](https://msdn.microsoft.com/commandline/wsl/about) ou um [so de distribuição do Linux](/cli/azure/install-azure-cli-apt?view=azure-cli-latest), [Instale o CLI do Azure console local](/cli/azure/install-azure-cli).
- Se você estiver usando CLI do Azure console local, entre no Azure usando `az login` com uma conta que esteja associada à assinatura do Azure que deseja implantar ou recuperar informações de identidade gerenciadas atribuídas pelo usuário.
- Recupere um token de acesso de portador usando `az account get-access-token` para executar as seguintes operações de identidade gerenciadas atribuídas pelo usuário.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerida atribuída pelo utilizador 

Para criar uma identidade gerenciada atribuída pelo usuário, sua conta precisa da atribuição de função de [colaborador de identidade gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) .

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X PUT -d '{"loc
ation": "<LOCATION>"}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```

**Cabeçalhos de solicitação**

|Cabeçalho do pedido  |Descrição  |
|---------|---------|
|*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
|*Autorização*     | Necessário. Defina como um token de acesso de `Bearer` válido.        |

**Corpo da solicitação**

|Nome  |Descrição  |
|---------|---------|
|localização     | Necessário. Local do recurso.        |

## <a name="list-user-assigned-managed-identities"></a>Listar identidades gerenciadas atribuídas pelo usuário

Para listar/ler uma identidade gerenciada atribuída pelo usuário, sua conta precisa do [operador de identidade gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-operator) ou da atribuição de função de [colaborador de identidade gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) .

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview' -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview HTTP/1.1
```

|Cabeçalho do pedido  |Descrição  |
|---------|---------|
|*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
|*Autorização*     | Necessário. Defina como um token de acesso de `Bearer` válido.        |

## <a name="delete-a-user-assigned-managed-identity"></a>Excluir uma identidade gerenciada atribuída pelo usuário

Para excluir uma identidade gerenciada atribuída pelo usuário, sua conta precisa da atribuição de função de [colaborador de identidade gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) .

> [!NOTE]
> A exclusão de uma identidade gerenciada atribuída pelo usuário não removerá a referência de nenhum recurso ao qual foi atribuída. Para remover uma identidade gerenciada atribuída pelo usuário de uma VM usando a ONDULAção, consulte [remover uma identidade atribuída pelo usuário de uma VM do Azure](qs-configure-rest-vm.md#remove-a-user-assigned identity-from-an-azure-vm).

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X DELETE -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
DELETE https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```
|Cabeçalho do pedido  |Descrição  |
|---------|---------|
|*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
|*Autorização*     | Necessário. Defina como um token de acesso de `Bearer` válido.        |

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre como atribuir uma identidade gerenciada atribuída pelo usuário a uma VM/VMSS do Azure usando a rotação, confira [Configurar identidades gerenciadas para recursos do Azure em uma VM do Azure usando chamadas à API REST](qs-configure-rest-vm.md#user-assigned-managed-identity) e [Configurar identidades gerenciadas para recursos do Azure em um conjunto de dimensionamento de máquinas virtuais usando chamadas à API REST](qs-configure-rest-vmss.md#user-assigned-managed-identity).
