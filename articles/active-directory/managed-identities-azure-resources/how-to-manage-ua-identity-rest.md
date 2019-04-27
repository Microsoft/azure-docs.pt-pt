---
title: Como gerir identidades do Azure atribuído ao utilizador geridas através de REST
description: Instruções passo a passo obter instruções sobre como criar, listar e eliminar um atribuído ao utilizador gerido identidade para fazer chamadas de REST API.
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
ms.openlocfilehash: 75867242358881c963ab4470bdb7963d0ea4671c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60440186"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-rest-api-calls"></a>Criar, lista ou eliminar uma identidade gerida atribuído ao utilizador através de chamadas à REST API

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Gerido identidades para recursos do Azure fornece serviços do Azure a capacidade de autenticar nos serviços de que a autenticação de suporte do Azure AD, sem a necessidade de credenciais no seu código. 

Neste artigo, saiba como criar, listar e eliminar uma identidade gerida atribuído ao utilizador com o CURL para fazer chamadas de REST API.

## <a name="prerequisites"></a>Pré-requisitos

- Se não estiver familiarizado com identidades geridas para recursos do Azure, veja a [secção Descrição geral](overview.md). **Certifique-se de que reveja os [diferença entre uma identidade gerida atribuído de sistema e atribuído ao utilizador](overview.md#how-does-it-work)**.
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Se estiver a utilizar o Windows, instale o [subsistema Windows para Linux](https://msdn.microsoft.com/commandline/wsl/about) ou utilizar o [Azure Cloud Shell](../../cloud-shell/overview.md) no portal do Azure.
- Se utilizar o [subsistema Windows para Linux](https://msdn.microsoft.com/commandline/wsl/about) ou uma [SO de distribuição de Linux](/cli/azure/install-azure-cli-apt?view=azure-cli-latest), [instalar a consola local da CLI do Azure](/cli/azure/install-azure-cli).
- Se estiver a utilizar consola local da CLI do Azure, inicie sessão no Azure com `az login` com uma conta que está associada à subscrição do Azure que pretende implementar ou obter as informações de identidade gerida atribuído ao utilizador.
- Obter um portador acesso token utilizando `az account get-access-token` para realizar as seguintes operações de identidade gerida atribuído ao utilizador.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerida atribuída pelo utilizador 

Para criar uma identidade gerida atribuído ao utilizador, a conta tem do [Contribuidor de identidade gerida](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) atribuição de função.

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

**Cabeçalhos de pedido**

|Cabeçalho do pedido  |Descrição  |
|---------|---------|
|*Content-Type*     | Necessário. Definido como `application/json`.        |
|*Autorização*     | Necessário. Definido como válido `Bearer` token de acesso.        |

**Corpo do pedido**

|Name  |Descrição  |
|---------|---------|
|localização     | Necessário. Localização do recurso.        |

## <a name="list-user-assigned-managed-identities"></a>Lista de identidades geridas atribuído ao utilizador

A lista/leitura uma identidade gerida atribuído ao utilizador, a conta tem do [operador de identidade gerida](/azure/role-based-access-control/built-in-roles#managed-identity-operator) ou [Contribuidor de identidade gerida](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) atribuição de função.

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview' -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview HTTP/1.1
```

|Cabeçalho do pedido  |Descrição  |
|---------|---------|
|*Content-Type*     | Necessário. Definido como `application/json`.        |
|*Autorização*     | Necessário. Definido como válido `Bearer` token de acesso.        |

## <a name="delete-a-user-assigned-managed-identity"></a>Eliminar uma identidade gerida atribuído ao utilizador

Para eliminar uma identidade gerida atribuído ao utilizador, a conta tem do [Contribuidor de identidade gerida](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) atribuição de função.

> [!NOTE]
> A eliminar uma identidade gerida atribuído ao utilizador não remover a referência de qualquer recurso que foi atribuído a. Para remover um utilizador atribuído geridos ver identidade a partir de uma VM com o CURL [remover uma identidade de utilizador atribuído a uma VM do Azure](qs-configure-rest-vm.md#remove-a-user-assigned identity-from-an-azure-vm).

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X DELETE -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
DELETE https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```
|Cabeçalho do pedido  |Descrição  |
|---------|---------|
|*Content-Type*     | Necessário. Definido como `application/json`.        |
|*Autorização*     | Necessário. Definido como válido `Bearer` token de acesso.        |

## <a name="next-steps"></a>Passos Seguintes

Para obter informações sobre como atribuir uma identidade gerida atribuído ao utilizador para um Azure VM/VMSS com o CURL, consulte [configurar geridos identidades para recursos do Azure numa VM do Azure através de chamadas à REST API](qs-configure-rest-vm.md#user-assigned-managed-identity) e [configurar gerido identidades para recursos do Azure num conjunto de dimensionamento através de chamadas à REST API](qs-configure-rest-vmss.md#user-assigned-managed-identity).
