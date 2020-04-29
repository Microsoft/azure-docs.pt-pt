---
title: Gerir uma identidade gerida atribuída ao utilizador no portal Azure - Azure AD
description: Instruções passo a passo sobre como criar, listar, excluir e atribuir uma função a uma identidade gerida atribuída pelo utilizador.
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
ms.openlocfilehash: 10caa9e5e61dc1dd0c1062583f55a7357c643ce5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79244137"
---
# <a name="create-list-delete-or-assign-a-role-to-a-user-assigned-managed-identity-using-the-azure-portal"></a>Criar, listar, excluir ou atribuir uma função a uma identidade gerida atribuída pelo utilizador através do portal Azure

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Identidades geridas para recursos Azure fornecem serviços Azure com uma identidade gerida no Diretório Ativo Azure. Pode utilizar esta identidade para autenticar serviços que suportam a autenticação Azure AD, sem precisar de credenciais no seu código. 

Neste artigo, aprende-se a criar, listar, excluir ou atribuir uma função a uma identidade gerida atribuída pelo utilizador através do Portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Se não está familiarizado com as identidades geridas para os recursos do Azure, consulte a [secção de visão geral.](overview.md) **Certifique-se de que revê a [diferença entre uma identidade gerida atribuída](overview.md#how-does-the-managed-identities-for-azure-resources-work)** ao sistema e atribuída ao utilizador.
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.

## <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerida atribuída pelo utilizador

Para criar uma identidade gerida atribuída ao utilizador, a sua conta necessita da atribuição de função de [Colaborador de Identidade Gerida.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta associada à subscrição do Azure para criar a identidade gerida atribuída pelo utilizador.
2. Na caixa de pesquisa, digite *Identidades Geridas,* e em **Serviços,** clique em **Identidades Geridas**.
3. Clique em **Adicionar** e introduza valores nos seguintes campos sob criar painel de identidade **gerido pelo utilizador:**
   - **Nome do Recurso**: Este é o nome da sua identidade gerida atribuída pelo utilizador, por exemplo, UAI1.
   - **Subscrição**: Escolha a subscrição para criar a identidade gerida atribuída pelo utilizador em
   - **Grupo de Recursos**: Crie um novo grupo de recursos para conter a sua identidade gerida atribuída pelo utilizador ou escolha **utilizar a utilização existente** para criar a identidade gerida atribuída pelo utilizador num grupo de recursos existente.
   - **Localização**: Escolha um local para implementar a identidade gerida atribuída pelo utilizador, por **exemplo, west us**.
4. Clique em **Criar**.

![Criar uma identidade gerida atribuída pelo utilizador](./media/how-to-manage-ua-identity-portal/create-user-assigned-managed-identity-portal.png)

## <a name="list-user-assigned-managed-identities"></a>Lista de identidades geridas atribuídas pelo utilizador

Para listar/ler uma identidade gerida atribuída pelo utilizador, a sua conta necessita da função de [Operador de Identidade Gerida](/azure/role-based-access-control/built-in-roles#managed-identity-operator) ou de Contribuição de Identidade [Gerida.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta associada à subscrição do Azure para listar as identidades geridas atribuídas pelo utilizador.
2. Na caixa de pesquisa, digite *Identidades Geridas,* e em Serviços, clique em **Identidades Geridas**.
3. É devolvida uma lista das identidades geridas atribuídas ao utilizador para a sua subscrição.  Para ver os detalhes de uma identidade gerida atribuída pelo utilizador clique no seu nome.

![Lista identidade gerida atribuída pelo utilizador](./media/how-to-manage-ua-identity-portal/list-user-assigned-managed-identity-portal.png)

## <a name="delete-a-user-assigned-managed-identity"></a>Eliminar uma identidade gerida atribuída pelo utilizador

Para eliminar uma identidade gerida atribuída pelo utilizador, a sua conta necessita da atribuição de função de [Colaborador de Identidade Gerida.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

A eliminação de uma identidade atribuída ao utilizador não a remove do VM ou do recurso a que foi atribuído.  Para remover a identidade atribuída ao utilizador de uma ver VM, remova uma identidade gerida atribuída pelo [utilizador a partir de um VM](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#remove-a-user-assigned-managed-identity-from-a-vm).

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta associada à subscrição do Azure para eliminar uma identidade gerida atribuída pelo utilizador.
2. Selecione a identidade gerida atribuída pelo utilizador e clique em **Eliminar**.
3. Sob a caixa de confirmação escolha, **sim**.

![Eliminar identidade gerida atribuída ao utilizador](./media/how-to-manage-ua-identity-portal/delete-user-assigned-managed-identity-portal.png)

## <a name="assign-a-role-to-a-user-assigned-managed-identity"></a>Atribuir uma função a uma identidade gerida atribuída pelo utilizador 

Para atribuir uma função a uma identidade gerida atribuída pelo utilizador, a sua conta necessita da atribuição da função de Administrador de Acesso ao [Utilizador.](/azure/role-based-access-control/built-in-roles#user-access-administrator)

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta associada à subscrição do Azure para listar as identidades geridas atribuídas pelo utilizador.
2. Na caixa de pesquisa, digite *Identidades Geridas,* e em Serviços, clique em **Identidades Geridas**.
3. É devolvida uma lista das identidades geridas atribuídas ao utilizador para a sua subscrição.  Selecione a identidade gerida atribuída pelo utilizador que pretende atribuir uma função.
4. Selecione **o controlo de acesso (IAM)** e, em seguida, selecione Adicionar a atribuição de **funções**.

   ![Início de identidade gerida atribuído ao utilizador](./media/how-to-manage-ua-identity-portal/assign-role-screenshot1.png)

5. Na lâmina de atribuição de funções Adicionar, configure os seguintes valores e, em seguida, clique em **Guardar:**
   - **Papel** - o papel a atribuir
   - **Atribuir acesso a** - o recurso para atribuir a identidade gerida atribuída pelo utilizador
   - **Selecione** - o membro para atribuir acesso
   
   ![Identidade gerida atribuída ao utilizador IAM](./media/how-to-manage-ua-identity-portal/assign-role-screenshot2.png)  
