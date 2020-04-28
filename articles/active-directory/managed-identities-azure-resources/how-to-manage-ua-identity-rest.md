---
title: Gerir identidades geridas atribuídas ao utilizador utilizando o REST - Azure AD
description: Instruções passo a passo sobre como criar, listar e eliminar uma identidade gerida atribuída pelo utilizador para fazer chamadas DEAPI REST.
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
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "74547420"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-rest-api-calls"></a>Criar, listar ou eliminar uma identidade gerida atribuída pelo utilizador utilizando chamadas REST API

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Identidades geridas para recursos Azure fornecem aos serviços Azure a capacidade de autenticação de serviços que suportam a autenticação Azure AD, sem precisar de credenciais no seu código. 

Neste artigo, aprende-se a criar, listar e eliminar uma identidade gerida atribuída pelo utilizador utilizando o CURL para fazer chamadas REST API.

## <a name="prerequisites"></a>Pré-requisitos

- Se não está familiarizado com as identidades geridas para os recursos do Azure, consulte a [secção de visão geral.](overview.md) **Certifique-se de que revê a [diferença entre uma identidade gerida atribuída](overview.md#how-does-the-managed-identities-for-azure-resources-work)** ao sistema e atribuída ao utilizador.
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Se estiver a utilizar o Windows, instale o [Subsistema Windows para o Linux](https://msdn.microsoft.com/commandline/wsl/about) ou utilize a [Casca de Nuvem Azure](../../cloud-shell/overview.md) no portal Azure.
- Se utilizar o [Subsistema Windows para o Linux](https://msdn.microsoft.com/commandline/wsl/about) ou um SISTEMA de [distribuição Linux,](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) [instale a consola local Azure CLI](/cli/azure/install-azure-cli).
- Se estiver a utilizar a consola local Azure `az login` CLI, inscreva-se no Azure utilizando uma conta associada à subscrição Azure que deseja implementar ou recuperar informações de identidade geridas atribuídas pelo utilizador.
- Recuperar um token `az account get-access-token` de acesso ao Portador utilizando para realizar as seguintes operações de identidade geridas atribuídas pelo utilizador.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerida atribuída pelo utilizador 

Para criar uma identidade gerida atribuída ao utilizador, a sua conta necessita da atribuição de função de [Colaborador de Identidade Gerida.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

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

**Pedir cabeçalhos**

|Cabeçalho do pedido  |Descrição  |
|---------|---------|
|*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
|*Autorização*     | Necessário. Desemparado com um sinal de acesso válido. `Bearer`        |

**Solicitar corpo**

|Nome  |Descrição  |
|---------|---------|
|localização     | Necessário. Localização de recursos.        |

## <a name="list-user-assigned-managed-identities"></a>Lista de identidades geridas atribuídas pelo utilizador

Para listar/ler uma identidade gerida atribuída pelo utilizador, a sua conta necessita da função de [Operador de Identidade Gerida](/azure/role-based-access-control/built-in-roles#managed-identity-operator) ou de Contribuição de Identidade [Gerida.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview' -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview HTTP/1.1
```

|Cabeçalho do pedido  |Descrição  |
|---------|---------|
|*Tipo de conteúdo*     | Necessário. Definido como `application/json`.        |
|*Autorização*     | Necessário. Desemparado com um sinal de acesso válido. `Bearer`        |

## <a name="delete-a-user-assigned-managed-identity"></a>Eliminar uma identidade gerida atribuída pelo utilizador

Para eliminar uma identidade gerida atribuída pelo utilizador, a sua conta necessita da atribuição de função de [Colaborador de Identidade Gerida.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

> [!NOTE]
> A eliminação de uma identidade gerida atribuída ao utilizador não removerá a referência de qualquer recurso a que tenha sido atribuído. Para remover uma identidade gerida atribuída pelo utilizador a partir de um VM utilizando o CURL ver [Remover uma identidade atribuída ao utilizador de um VM Azure](qs-configure-rest-vm.md#remove-a-user-assigned identity-from-an-azure-vm).

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
|*Autorização*     | Necessário. Desemparado com um sinal de acesso válido. `Bearer`        |

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre como atribuir uma identidade gerida atribuída ao utilizador a um Azure VM/VMSS utilizando a CURL see, [configure geriu identidades para recursos Azure num Azure VM utilizando chamadas API REST](qs-configure-rest-vm.md#user-assigned-managed-identity) e [Configurar identidades geridas para recursos Azure numa escala de máquina virtual utilizando chamadas REST API](qs-configure-rest-vmss.md#user-assigned-managed-identity).
