---
title: Gerir a identidade gerida atribuída pelo utilizador - Azure CLI - Azure AD
description: Instruções passo a passo sobre como criar, listar e eliminar uma identidade gerida atribuída pelo utilizador utilizando o Azure CLI.
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
ms.date: 04/17/2020
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: be5defb85547e8750dea9ceaa481217aa40a004e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81639768"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-the-azure-cli"></a>Criar, listar ou eliminar uma identidade gerida atribuída pelo utilizador utilizando o Azure CLI


Identidades geridas para recursos Azure fornecem serviços Azure com uma identidade gerida no Diretório Ativo Azure. Pode utilizar esta identidade para autenticar serviços que suportam a autenticação Azure AD, sem precisar de credenciais no seu código. 

Neste artigo, aprende-se a criar, listar e eliminar uma identidade gerida atribuída pelo utilizador utilizando o Azure CLI.

## <a name="prerequisites"></a>Pré-requisitos

- Se não está familiarizado com as identidades geridas para os recursos do Azure, consulte a [secção de visão geral.](overview.md) **Certifique-se de que revê a [diferença entre uma identidade gerida atribuída](overview.md#how-does-the-managed-identities-for-azure-resources-work)** ao sistema e atribuída ao utilizador.
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para executar os exemplos do script CLI, tem três opções:
    - Utilize a Casca de [Nuvem Azure](../../cloud-shell/overview.md) a partir do portal Azure (ver secção seguinte).
    - Utilize a casca de nuvem azure incorporada através do botão "Try It", localizado no canto superior direito de cada bloco de código.
    - [Instale a versão mais recente do Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 ou mais tarde) se preferir utilizar uma consola CLI local. Faça o instique no Azure utilizando `az login`uma conta que esteja associada à subscrição Do Azure sob a qual deseja implementar a identidade gerida atribuída pelo utilizador.


> [!NOTE]
> Para modificar as permissões dos utilizadores ao utilizar um diretor de servivce de aplicações utilizando o CLI, deve fornecer ao principal serviço permissões adicionais na API do Gráfico AD Azure, uma vez que partes do CLI executam pedidos GET contra a API do gráfico. Caso contrário, poderá acabar por receber uma mensagem "Insuficiente sapateada para completar a operação". Para isso, terá de entrar no registo da App no Azure Ative Directory, selecionar a sua aplicação, clicar em permissões API, deslocar-se para baixo e selecionar o Gráfico de Diretório Ativo do Azure. A partir daí, selecione permissões de pedido e, em seguida, adicione as permissões apropriadas. 



[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerida atribuída pelo utilizador 

Para criar uma identidade gerida atribuída ao utilizador, a sua conta necessita da atribuição de função de [Colaborador de Identidade Gerida.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

Utilize o comando de criação de [identidade az](/cli/azure/identity#az-identity-create) para criar uma identidade gerida atribuída pelo utilizador. O `-g` parâmetro especifica o grupo de recursos onde criar a identidade `-n` gerida atribuída pelo utilizador, e o parâmetro especifica o seu nome. Substitua `<RESOURCE GROUP>` `<USER ASSIGNED IDENTITY NAME>` os valores e parâmetros dos seus próprios valores:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Lista de identidades geridas atribuídas pelo utilizador

Para listar/ler uma identidade gerida atribuída pelo utilizador, a sua conta necessita da função de [Operador de Identidade Gerida](/azure/role-based-access-control/built-in-roles#managed-identity-operator) ou de Contribuição de Identidade [Gerida.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

Para listar as identidades geridas atribuídas pelo utilizador, utilize o comando da [lista de identidade az.](/cli/azure/identity#az-identity-list) Substitua `<RESOURCE GROUP>` o com o seu próprio valor:

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```
Na resposta json, as identidades geridas `"Microsoft.ManagedIdentity/userAssignedIdentities"` atribuídas pelo `type`utilizador têm valor devolvido para a chave, .

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-managed-identity"></a>Eliminar uma identidade gerida atribuída pelo utilizador

Para eliminar uma identidade gerida atribuída pelo utilizador, a sua conta necessita da atribuição de função de [Colaborador de Identidade Gerida.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

Para eliminar uma identidade gerida atribuída pelo utilizador, utilize o comando de eliminação de [identidade az.](/cli/azure/identity#az-identity-delete)  O parâmetro -n especifica o seu nome e o parâmetro-g especifica o grupo de recursos onde foi criada a identidade gerida atribuída pelo utilizador. Substitua `<USER ASSIGNED IDENTITY NAME>` `<RESOURCE GROUP>` os valores e parâmetros com os seus próprios valores:

 ```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> A eliminação de uma identidade gerida atribuída ao utilizador não removerá a referência, de qualquer recurso a que tenha sido atribuído. Por favor, remova os de VM/VMSS usando o `az vm/vmss identity remove` comando

## <a name="next-steps"></a>Passos seguintes

Para obter uma lista completa dos comandos de identidade Azure CLI, consulte a [identidade az](/cli/azure/identity).

Para obter informações sobre como atribuir uma identidade gerida atribuída ao utilizador a um ver Azure VM, [a Configure geriu identidades para os recursos Azure num Azure VM utilizando o Azure CLI](qs-configure-cli-windows-vm.md#user-assigned-managed-identity)


 
