---
title: Como gerir uma identidade gerida atribuído ao utilizador com a CLI do Azure
description: Instruções passo a passo obter instruções sobre como criar, listar e eliminar um atribuído ao utilizador a identidade com a CLI do Azure gerido.
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
ms.date: 04/16/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28520b3ba5d4e62fd4e1c9b78e68cc7dc2b48c61
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60291008"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-the-azure-cli"></a>Criar, lista ou eliminar uma identidade gerida atribuído ao utilizador com a CLI do Azure

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Identidades geridas para recursos do Azure fornece serviços do Azure com uma identidade gerida no Azure Active Directory. Pode utilizar esta identidade para autenticar para serviços que suportam a autenticação do Azure AD, sem a necessidade de credenciais no seu código. 

Neste artigo, saiba como criar, listar e eliminar uma identidade gerida atribuído ao utilizador com a CLI do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Se não estiver familiarizado com identidades geridas para recursos do Azure, veja a [secção Descrição geral](overview.md). **Certifique-se de que reveja os [diferença entre uma identidade gerida atribuído de sistema e atribuído ao utilizador](overview.md#how-does-it-work)**.
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para executar os exemplos de script da CLI, tem três opções:
    - Uso [Azure Cloud Shell](../../cloud-shell/overview.md) do portal do Azure (consulte a secção seguinte).
    - Utilize o embedded Azure Cloud Shell através do "Experimente-lo" botão do, localizado no canto superior direito de cada bloco de código.
    - [Instalar a versão mais recente da CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 ou posterior) se preferir utilizar uma consola CLI local. Inicie sessão no Azure com `az login`, utilizando uma conta que está associada à subscrição do Azure sob a qual pretende implementar o atribuído ao utilizador a identidade gerida.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerida atribuída pelo utilizador 

Para criar uma identidade gerida atribuído ao utilizador, a conta tem do [Contribuidor de identidade gerida](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) atribuição de função.

Utilize o [identidade az criar](/cli/azure/identity#az-identity-create) identidade gerida de comando para criar um atribuído ao utilizador. O `-g` parâmetro especifica o grupo de recursos onde criar a identidade gerida atribuído ao utilizador e o `-n` parâmetro especifica o respetivo nome. Substitua a `<RESOURCE GROUP>` e `<USER ASSIGNED IDENTITY NAME>` valores de parâmetros com seus próprios valores:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Lista de identidades geridas atribuído ao utilizador

A lista/leitura uma identidade gerida atribuído ao utilizador, a conta tem do [operador de identidade gerida](/azure/role-based-access-control/built-in-roles#managed-identity-operator) ou [Contribuidor de identidade gerida](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) atribuição de função.

Lista de identidades geridas atribuído ao utilizador, utilize o [lista de identidade az](/cli/azure/identity#az-identity-list) comando. Substitua o `<RESOURCE GROUP>` com seu próprio valor:

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```
Na resposta json, tem atribuído ao utilizador identidades geridas `"Microsoft.ManagedIdentity/userAssignedIdentities"` valor retornado para a chave, `type`.

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-managed-identity"></a>Eliminar uma identidade gerida atribuído ao utilizador

Para eliminar uma identidade gerida atribuído ao utilizador, a conta tem do [Contribuidor de identidade gerida](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) atribuição de função.

Para eliminar uma identidade gerida atribuído ao utilizador, utilize o [identidade az eliminar](/cli/azure/identity#az-identity-delete) comando.  O parâmetro - n Especifica o nome e o parâmetro -g Especifica o grupo de recursos em que a identidade gerida atribuído ao utilizador foi criada. Substitua a `<USER ASSIGNED IDENTITY NAME>` e `<RESOURCE GROUP>` valores de parâmetros com seus próprios valores:

 ```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> A eliminar uma identidade gerida atribuído ao utilizador não remover a referência de qualquer recurso que foi atribuído a. Remova os da VM/VMSS usando o `az vm/vmss identity remove` comando

## <a name="next-steps"></a>Passos Seguintes

Para obter uma lista completa dos comandos de identidade da CLI do Azure, consulte [identidade az](/cli/azure/identity).

Para obter informações sobre como atribuir um atribuído ao utilizador a identidade de uma veja de VM do Azure gerido [configurar geridos identidades para recursos do Azure na VM do Azure com a CLI do Azure](qs-configure-cli-windows-vm.md#user-assigned-managed-identity)


 
