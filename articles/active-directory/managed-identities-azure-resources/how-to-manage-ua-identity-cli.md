---
title: Gerenciar identidade gerenciada atribuída pelo usuário-CLI do Azure-Azure AD
description: Instruções passo a passo sobre como criar, listar e excluir uma identidade gerenciada atribuída pelo usuário usando o CLI do Azure.
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
ms.date: 10/15/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1e8587562ff452373fe2ee3b98fa20309e77cc7
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74547499"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-the-azure-cli"></a>Criar, listar ou excluir uma identidade gerenciada atribuída pelo usuário usando o CLI do Azure


Identidades gerenciadas para recursos do Azure fornecem serviços do Azure com uma identidade gerenciada no Azure Active Directory. Você pode usar essa identidade para autenticar em serviços que dão suporte à autenticação do Azure AD, sem precisar de credenciais em seu código. 

Neste artigo, você aprenderá a criar, listar e excluir uma identidade gerenciada atribuída pelo usuário usando CLI do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com identidades gerenciadas para recursos do Azure, confira a [seção visão geral](overview.md). **Certifique-se de examinar a [diferença entre uma identidade gerenciada atribuída pelo sistema e](overview.md#how-does-the-managed-identities-for-azure-resources-work)** atribuída pelo usuário.
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para executar os exemplos de script da CLI, tem três opções:
    - Use [Azure cloud Shell](../../cloud-shell/overview.md) da portal do Azure (consulte a próxima seção).
    - Utilize o embedded Azure Cloud Shell através do "Experimente-lo" botão do, localizado no canto superior direito de cada bloco de código.
    - [Instale a versão mais recente do CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 ou posterior) se preferir usar um console local da CLI. Entre no Azure usando `az login`, usando uma conta associada à assinatura do Azure sob a qual você deseja implantar a identidade gerenciada atribuída pelo usuário.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerida atribuída pelo utilizador 

Para criar uma identidade gerenciada atribuída pelo usuário, sua conta precisa da atribuição de função de [colaborador de identidade gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) .

Use o comando [AZ Identity Create](/cli/azure/identity#az-identity-create) para criar uma identidade gerenciada atribuída pelo usuário. O parâmetro `-g` especifica o grupo de recursos onde criar a identidade gerenciada atribuída pelo usuário e o parâmetro `-n` especifica seu nome. Substitua os valores de parâmetro `<RESOURCE GROUP>` e `<USER ASSIGNED IDENTITY NAME>` pelos seus próprios valores:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Listar identidades gerenciadas atribuídas pelo usuário

Para listar/ler uma identidade gerenciada atribuída pelo usuário, sua conta precisa do [operador de identidade gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-operator) ou da atribuição de função de [colaborador de identidade gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) .

Para listar identidades gerenciadas atribuídas pelo usuário, use o comando [AZ Identity List](/cli/azure/identity#az-identity-list) . Substitua o `<RESOURCE GROUP>` pelo seu próprio valor:

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```
Na resposta JSON, as identidades gerenciadas atribuídas pelo usuário têm `"Microsoft.ManagedIdentity/userAssignedIdentities"` valor retornado para a chave, `type`.

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-managed-identity"></a>Excluir uma identidade gerenciada atribuída pelo usuário

Para excluir uma identidade gerenciada atribuída pelo usuário, sua conta precisa da atribuição de função de [colaborador de identidade gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) .

Para excluir uma identidade gerenciada atribuída pelo usuário, use o comando [AZ Identity Delete](/cli/azure/identity#az-identity-delete) .  O parâmetro-n especifica seu nome e o parâmetro-g especifica o grupo de recursos em que a identidade gerenciada atribuída pelo usuário foi criada. Substitua os valores de parâmetros `<USER ASSIGNED IDENTITY NAME>` e `<RESOURCE GROUP>` pelos seus próprios valores:

 ```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> A exclusão de uma identidade gerenciada atribuída pelo usuário não removerá a referência, de qualquer recurso ao qual ela foi atribuída. Remova-os de VM/VMSS usando o comando `az vm/vmss identity remove`

## <a name="next-steps"></a>Passos seguintes

Para obter uma lista completa de comandos de identidade CLI do Azure, consulte [AZ Identity](/cli/azure/identity).

Para obter informações sobre como atribuir uma identidade gerenciada atribuída pelo usuário a uma VM do Azure, consulte [Configurar identidades gerenciadas para recursos do Azure em uma VM do Azure usando CLI do Azure](qs-configure-cli-windows-vm.md#user-assigned-managed-identity)


 
