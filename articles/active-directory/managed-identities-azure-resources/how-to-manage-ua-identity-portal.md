---
title: Gerir uma identidade gerida atribuída ao utilizador no portal Azure - Azure AD
description: Instruções passo a passo sobre como criar, listar, excluir e atribuir uma função a uma identidade gerida atribuída pelo utilizador.
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
ms.date: 08/26/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5045c81f280d9d7687a466aed2e918d21eec697
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89266392"
---
# <a name="create-list-delete-or-assign-a-role-to-a-user-assigned-managed-identity-using-the-azure-portal"></a>Criar, listar, excluir ou atribuir uma função a uma identidade gerida atribuída pelo utilizador utilizando o portal Azure

Identidades geridas para recursos Azure fornecem aos serviços Azure uma identidade gerida no Azure Ative Directory. Pode utilizar esta identidade para autenticar serviços que suportem a autenticação Azure AD, sem precisar de credenciais no seu código. 

Neste artigo, aprende-se a criar, listar, excluir ou atribuir uma função a uma identidade gerida atribuída pelo utilizador através do portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Se não está familiarizado com as identidades geridas para os recursos da Azure, consulte a [secção de visão geral.](overview.md) **Certifique-se de rever a [diferença entre uma identidade gerida atribuída ao sistema e atribuída ao utilizador](overview.md#managed-identity-types)**.
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.

## <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerida atribuída pelo utilizador

Para criar uma identidade gerida atribuída ao utilizador, a sua conta necessita da atribuição [de função de Contribuinte de Identidade Gerida.](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta associada à subscrição do Azure para criar a identidade gerida atribuída ao utilizador.
2. Na caixa de pesquisa, *digite Identidades Geridas,* e em **Serviços,** clique em **Identidades Geridas**.
3. Clique em **Adicionar** e introduzir valores nos seguintes campos em Criar painel de identidade **gerido atribuído pelo utilizador:**
    - **Subscrição**: Escolha a subscrição para criar a identidade gerida atribuída pelo utilizador.
    - **Grupo de recursos**: Escolha um grupo de recursos para criar a identidade gerida atribuída pelo utilizador ou clique em **Criar novo** para criar um novo grupo de recursos.
    - **Região**: Escolha uma região para implantar a identidade gerida atribuída pelo utilizador, por **exemplo, os EUA Ocidentais.**
    - **Nome**: Este é o nome da sua identidade gerida atribuída pelo utilizador, por exemplo UAI1.
    ![Criar uma identidade gerida atribuída ao utilizador](./media/how-to-manage-ua-identity-portal/create-user-assigned-managed-identity-portal.png)
4. Clique **em Rever + caixa** para rever as alterações.
5. Clique em **Criar**.

## <a name="list-user-assigned-managed-identities"></a>Lista de identidades geridas atribuídas pelo utilizador

Para listar/ler uma identidade gerida atribuída pelo utilizador, a sua conta precisa da atribuição de funções de [Colaborador de Identidade Gerida](../../role-based-access-control/built-in-roles.md#managed-identity-operator) ou De Identidade [Gerida.](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta associada à subscrição do Azure para listar as identidades geridas atribuídas pelo utilizador.
2. Na caixa de pesquisa, *digite Identidades Geridas,* e em Serviços, clique em **Identidades Geridas**.
3. É devolvida uma lista das identidades geridas atribuídas pelo utilizador para a sua subscrição.  Para ver os detalhes de uma identidade gerida atribuída pelo utilizador clique no seu nome.

![Lista identidade gerida atribuída pelo utilizador](./media/how-to-manage-ua-identity-portal/list-user-assigned-managed-identity-portal.png)

## <a name="delete-a-user-assigned-managed-identity"></a>Eliminar uma identidade gerida atribuída pelo utilizador

Para eliminar uma identidade gerida atribuída pelo utilizador, a sua conta necessita da atribuição [de função de Contribuinte de Identidade Gerida.](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)

A eliminação da identidade atribuída pelo utilizador não a retira do VM nem do recurso a que foi atribuído.  Para remover a identidade atribuída ao utilizador de um VM ver, [Remova uma identidade gerida atribuída pelo utilizador de um VM](./qs-configure-portal-windows-vm.md#remove-a-user-assigned-managed-identity-from-a-vm).

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta associada à subscrição do Azure para eliminar uma identidade gerida atribuída pelo utilizador.
2. Selecione a identidade gerida atribuída pelo utilizador e clique em **Eliminar**.
3. Sob a caixa de confirmação escolha, **Sim**.

![Eliminar identidade gerida atribuída pelo utilizador](./media/how-to-manage-ua-identity-portal/delete-user-assigned-managed-identity-portal.png)

## <a name="assign-a-role-to-a-user-assigned-managed-identity"></a>Atribuir uma função a uma identidade gerida atribuída pelo utilizador 

Para atribuir uma função a uma identidade gerida atribuída pelo utilizador, a sua conta necessita da atribuição de função de Administrador de Acesso ao [Utilizador.](../../role-based-access-control/built-in-roles.md#user-access-administrator)

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta associada à subscrição do Azure para listar as identidades geridas atribuídas pelo utilizador.
2. Na caixa de pesquisa, *digite Identidades Geridas,* e em Serviços, clique em **Identidades Geridas**.
3. É devolvida uma lista das identidades geridas atribuídas pelo utilizador para a sua subscrição.  Selecione a identidade gerida atribuída pelo utilizador que pretende atribuir uma função.
4. Selecione **o controlo de acesso (IAM)** e, em seguida, selecione Adicionar a atribuição de **função**.

   ![Início de identidade gerida atribuída pelo utilizador](./media/how-to-manage-ua-identity-portal/assign-role-screenshot1.png)

5. Na lâmina de atribuição de funções Adicionar, configure os seguintes valores e, em seguida, clique em **Guardar**:
   - **Papel** - o papel a atribuir
   - **Atribuir acesso a**  - o recurso para atribuir a identidade gerida atribuída pelo utilizador
   - **Selecione** - o membro para atribuir acesso
   
   ![IAM de identidade gerida atribuída pelo utilizador](./media/how-to-manage-ua-identity-portal/assign-role-screenshot2.png)