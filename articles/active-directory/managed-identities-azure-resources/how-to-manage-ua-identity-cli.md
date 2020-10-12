---
title: Gerir identidade gerida atribuída ao utilizador - Azure CLI - Azure AD
description: Instruções passo a passo sobre como criar, listar e eliminar uma identidade gerida atribuída pelo utilizador utilizando o CLI Azure.
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
ms.date: 04/17/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: 29a1a991ab79c38dad1a89533091d80406615d35
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90969474"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-the-azure-cli"></a>Criar, listar ou eliminar uma identidade gerida atribuída pelo utilizador utilizando o CLI Azure


Identidades geridas para recursos Azure fornecem aos serviços Azure uma identidade gerida no Azure Ative Directory. Pode utilizar esta identidade para autenticar serviços que suportem a autenticação Azure AD, sem precisar de credenciais no seu código. 

Neste artigo, aprende-se a criar, listar e eliminar uma identidade gerida atribuída pelo utilizador utilizando o Azure CLI.

## <a name="prerequisites"></a>Pré-requisitos

- Se não está familiarizado com as identidades geridas para os recursos da Azure, consulte a [secção de visão geral.](overview.md) **Certifique-se de rever a [diferença entre uma identidade gerida atribuída ao sistema e atribuída ao utilizador](overview.md#managed-identity-types)**.
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para executar os scripts de exemplo, tem duas opções:
    - Utilize o [Azure Cloud Shell,](../../cloud-shell/overview.md)que pode abrir utilizando o botão **Try It** no canto superior direito dos blocos de código.
    - Executar scripts localmente instalando a versão mais recente do [Azure CLI,](/cli/azure/install-azure-cli)em seguida, inicie sessão no Azure usando [login az](/cli/azure/reference-index#az-login). Utilize uma conta associada à subscrição do Azure na qual pretende criar recursos.   

> [!NOTE]
> Para modificar as permissões do utilizador ao utilizar um responsável de serviço de aplicações utilizando o CLI, deve fornecer ao serviço permissões adicionais no Azure AD Graph API, uma vez que porções de CLI realizam pedidos GET contra a API do Gráfico. Caso contrário, poderá acabar por receber uma mensagem "Privilégios insuficientes para completar a operação". Para isso, terá de entrar no registo da App no Azure Ative Directory, selecionar a sua aplicação, clicar nas permissões da API, deslocar-se para baixo e selecionar O Azure Ative Directory Graph. A partir daí, selecione permissões de aplicação e, em seguida, adicione as permissões apropriadas. 

## <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerida atribuída pelo utilizador 

Para criar uma identidade gerida atribuída ao utilizador, a sua conta necessita da atribuição [de função de Contribuinte de Identidade Gerida.](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)

Utilize o comando de criação de [identidade az](/cli/azure/identity#az-identity-create) para criar uma identidade gerida atribuída pelo utilizador. O `-g` parâmetro especifica o grupo de recursos onde criar a identidade gerida atribuída pelo utilizador, e o parâmetro especifica o `-n` seu nome. Substitua os `<RESOURCE GROUP>` valores e `<USER ASSIGNED IDENTITY NAME>` parâmetros pelos seus próprios valores:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Lista de identidades geridas atribuídas pelo utilizador

Para listar/ler uma identidade gerida atribuída pelo utilizador, a sua conta precisa da atribuição de funções de [Colaborador de Identidade Gerida](../../role-based-access-control/built-in-roles.md#managed-identity-operator) ou De Identidade [Gerida.](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)

Para listar identidades geridas atribuídas pelo utilizador, utilize o comando [da lista de identidades AZ.](/cli/azure/identity#az-identity-list) Substitua-a `<RESOURCE GROUP>` pelo seu próprio valor:

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```

Na resposta json, as identidades geridas atribuídas pelo utilizador têm `"Microsoft.ManagedIdentity/userAssignedIdentities"` valor devolvido para chave, `type` .

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-managed-identity"></a>Eliminar uma identidade gerida atribuída pelo utilizador

Para eliminar uma identidade gerida atribuída pelo utilizador, a sua conta necessita da atribuição [de função de Contribuinte de Identidade Gerida.](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)

Para eliminar uma identidade gerida atribuída pelo utilizador, utilize o comando [de eliminação de identidade az.](/cli/azure/identity#az-identity-delete)  O parâmetro -n especifica o seu nome e o parâmetro -g especifica o grupo de recursos onde foi criada a identidade gerida atribuída pelo utilizador. Substitua os `<USER ASSIGNED IDENTITY NAME>` valores e `<RESOURCE GROUP>` parâmetros pelos seus próprios valores:

```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> A eliminação de uma identidade gerida atribuída pelo utilizador não removerá a referência de qualquer recurso a que tenha sido atribuído. Por favor, remova-os de VM/VMSS usando o `az vm/vmss identity remove` comando

## <a name="next-steps"></a>Passos seguintes

Para obter uma lista completa dos comandos de identidade do Azure CLI, consulte [a identidade az](/cli/azure/identity).

Para obter informações sobre como atribuir uma identidade gerida atribuída ao utilizador a um Azure VM ver, [Configure identidades geridas para recursos Azure em um VM Azure usando Azure CLI](qs-configure-cli-windows-vm.md#user-assigned-managed-identity)
