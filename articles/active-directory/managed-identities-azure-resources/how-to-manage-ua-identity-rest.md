---
title: Gerir identidades geridas atribuídas pelo utilizador usando REST - Azure AD
description: Instruções passo a passo sobre como criar, listar e eliminar uma identidade gerida atribuída pelo utilizador para fazer chamadas de API REST.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 117f9a1c173f2083dd4621f4f3f41b6e83d1d46b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96546697"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-rest-api-calls"></a>Criar, listar ou eliminar uma identidade gerida atribuída pelo utilizador usando chamadas REST API

Identidades geridas para recursos Azure fornecem aos serviços Azure a capacidade de autenticar serviços que suportem a autenticação AZure AD, sem necessidade de credenciais no seu código. 

Neste artigo, aprende-se a criar, listar e eliminar uma identidade gerida atribuída pelo utilizador utilizando o CURL para fazer chamadas de API REST.

## <a name="prerequisites"></a>Pré-requisitos

- Se não está familiarizado com as identidades geridas para os recursos da Azure, consulte a [secção de visão geral.](overview.md) **Certifique-se de rever a [diferença entre uma identidade gerida atribuída ao sistema e atribuída ao utilizador](overview.md#managed-identity-types)**.
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Pode executar todos os comandos deste artigo na nuvem ou localmente:
    - Para correr na nuvem, use a [Azure Cloud Shell](../../cloud-shell/overview.md).
    - Para funcionar localmente, instale [o curl](https://curl.haxx.se/download.html) e o [Azure CLI](/cli/azure/install-azure-cli).

## <a name="obtain-a-bearer-access-token"></a>Obter um token de acesso ao portador

1. Se correr localmente, inscreva-se em Azure através do Azure CLI:

    ```
    az login
    ```

1. Obtenha um token de acesso usando [a conta az obter acesso-token](/cli/azure/account#az_account_get_access_token)

    ```azurecli-interactive
    az account get-access-token
    ```

## <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerida atribuída pelo utilizador 

Para criar uma identidade gerida atribuída ao utilizador, a sua conta necessita da atribuição [de função de Contribuinte de Identidade Gerida.](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)

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

**Cabeçalhos do pedido**

|Cabeçalho do pedido  |Descrição  |
|---------|---------|
|*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
|*Autorização*     | Obrigatório. Definir para um `Bearer` token de acesso válido.        |

**Corpo de pedido**

|Nome  |Descrição  |
|---------|---------|
|localização     | Obrigatório. Localização de recursos.        |

## <a name="list-user-assigned-managed-identities"></a>Lista de identidades geridas atribuídas pelo utilizador

Para listar/ler uma identidade gerida atribuída pelo utilizador, a sua conta precisa da atribuição de funções de [Colaborador de Identidade Gerida](../../role-based-access-control/built-in-roles.md#managed-identity-operator) ou De Identidade [Gerida.](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview' -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview HTTP/1.1
```

|Cabeçalho do pedido  |Descrição  |
|---------|---------|
|*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
|*Autorização*     | Obrigatório. Definir para um `Bearer` token de acesso válido.        |

## <a name="delete-a-user-assigned-managed-identity"></a>Eliminar uma identidade gerida atribuída pelo utilizador

Para eliminar uma identidade gerida atribuída pelo utilizador, a sua conta necessita da atribuição [de função de Contribuinte de Identidade Gerida.](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)

> [!NOTE]
> A eliminação de uma identidade gerida atribuída pelo utilizador não removerá a referência de qualquer recurso a que tenha sido atribuído. Para remover uma identidade gerida atribuída pelo utilizador a partir de um VM utilizando o CURL consulte [Remover uma identidade atribuída ao utilizador de um VM Azure](qs-configure-rest-vm.md#remove-a-user-assigned-managed-identity-from-an-azure-vm).

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
|*Autorização*     | Obrigatório. Definir para um `Bearer` token de acesso válido.        |

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre como atribuir uma identidade gerida atribuída ao utilizador a um VM/VMSS Azure usando o CURL ver, [Configure identidades geridas para recursos Azure em um VM Azure usando chamadas REST API](qs-configure-rest-vm.md#user-assigned-managed-identity) e [Configurar identidades geridas para recursos Azure numa escala de máquina virtual definida usando chamadas REST API](qs-configure-rest-vmss.md#user-assigned-managed-identity).
