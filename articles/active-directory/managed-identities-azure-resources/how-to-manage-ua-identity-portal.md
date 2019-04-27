---
title: Como gerir uma identidade gerida atribuído ao utilizador com o portal do Azure
description: Instruções passo a passo instruções sobre como criar, listar, eliminam e atribuir uma função para uma identidade gerida atribuído ao utilizador.
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
ms.openlocfilehash: 18a15b8039322fc5e43a2b9dfed8a9bd3fc8b5fb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60441647"
---
# <a name="create-list-delete-or-assign-a-role-to-a-user-assigned-managed-identity-using-the-azure-portal"></a>Criar, listar, eliminar ou atribuir uma função para uma identidade gerida atribuído ao utilizador com o portal do Azure

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Identidades geridas para recursos do Azure fornece serviços do Azure com uma identidade gerida no Azure Active Directory. Pode utilizar esta identidade para autenticar para serviços que suportam a autenticação do Azure AD, sem a necessidade de credenciais no seu código. 

Neste artigo, irá aprender a criar, listar, eliminar ou atribuir uma função para uma identidade gerida atribuído ao utilizador através do Portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Se não estiver familiarizado com identidades geridas para recursos do Azure, veja a [secção Descrição geral](overview.md). **Certifique-se de que reveja os [diferença entre uma identidade gerida atribuído de sistema e atribuído ao utilizador](overview.md#how-does-it-work)**.
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.

## <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerida atribuída pelo utilizador

Para criar uma identidade gerida atribuído ao utilizador, a conta tem do [Contribuidor de identidade gerida](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) atribuição de função.

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) usando uma conta associada à subscrição do Azure para criar a identidade gerida atribuído ao utilizador.
2. Na caixa de pesquisa, escreva *identidades geridas por*e, em **serviços**, clique em **identidades geridas por**.
3. Clique em **Add** e introduza os valores nos campos a seguir sob **atribuída ao utilizador criar geridos** painel de identidade:
   - **Nome do recurso**: Este é o nome para o seu utilizador atribuído identidade gerida, por exemplo UAI1.
   - **Subscrição**: Escolha a subscrição para criar a identidade gerida atribuído ao utilizador em
   - **Grupo de recursos**: Criar um novo grupo de recursos para conter a sua identidade gerida atribuído ao utilizador ou escolha **utilizar existente** para criar a identidade gerida atribuído ao utilizador no grupo de recursos existente.
   - **Localização**: Escolha uma localização para implementar a identidade gerida atribuído ao utilizador, por exemplo **E.U.A. oeste**.
4. Clique em **Criar**.

![Criar uma identidade gerida atribuída pelo utilizador](./media/how-to-manage-ua-identity-portal/create-user-assigned-managed-identity-portal.png)

## <a name="list-user-assigned-managed-identities"></a>Lista de identidades geridas atribuído ao utilizador

A lista/leitura uma identidade gerida atribuído ao utilizador, a conta tem do [operador de identidade gerida](/azure/role-based-access-control/built-in-roles#managed-identity-operator) ou [Contribuidor de identidade gerida](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) atribuição de função.

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) através de uma conta associada à subscrição do Azure para listar as identidades geridas atribuído ao utilizador.
2. Na caixa de pesquisa, escreva *identidades geridas por*e em serviços, clique em **identidades geridas por**.
3. Devolve uma lista de identidades geridas atribuído ao utilizador para a sua subscrição.  Para ver os detalhes de uma identidade gerida atribuído ao utilizador, clique no respetivo nome.

![Lista atribuído ao utilizador com identidade gerida](./media/how-to-manage-ua-identity-portal/list-user-assigned-managed-identity-portal.png)

## <a name="delete-a-user-assigned-managed-identity"></a>Eliminar uma identidade gerida atribuído ao utilizador

Para eliminar uma identidade gerida atribuído ao utilizador, a conta tem do [Contribuidor de identidade gerida](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) atribuição de função.

A eliminar um identidade atribuída ao utilizador não o remove da VM ou recurso que foi atribuído a.  Para remover a identidade atribuída ao utilizador a uma veja VM [remover uma identidade gerida atribuído ao utilizador a partir de uma VM](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#remove-a-user-assigned-managed-identity-from-a-vm).

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) através de uma conta associada à subscrição do Azure para eliminar uma identidade gerida atribuído ao utilizador.
2. Selecionar a identidade gerida atribuído ao utilizador e clique em **eliminar**.
3. Na caixa de confirmação escolha, **Sim**.

![Eliminar utilizador atribuído a identidade gerida](./media/how-to-manage-ua-identity-portal/delete-user-assigned-managed-identity-portal.png)

## <a name="assign-a-role-to-a-user-assigned-managed-identity"></a>Atribuir uma função para uma identidade gerida atribuído ao utilizador 

Para atribuir uma função a uma identidade gerida atribuído ao utilizador, a conta tem do [administrador de acesso de utilizador](/azure/role-based-access-control/built-in-roles#user-access-administrator) atribuição de função.

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) através de uma conta associada à subscrição do Azure para listar as identidades geridas atribuído ao utilizador.
2. Na caixa de pesquisa, escreva *identidades geridas por*e em serviços, clique em **identidades geridas por**.
3. Devolve uma lista de identidades geridas atribuído ao utilizador para a sua subscrição.  Selecione a identidade de gerido atribuído ao utilizador que pretende atribuir uma função.
4. Selecione **controlo de acesso (IAM)** e, em seguida, selecione **adicionar atribuição de função**.

   ![Início de atribuído ao utilizador a identidade gerida](./media/how-to-manage-ua-identity-portal/assign-role-screenshot1.png)

5. No painel Adicionar atribuição de função, configure os seguintes valores e, em seguida, clique em **guardar**:
   - **Função** -a função para atribuir
   - **Atribuir acesso a** -o recurso para atribuir o atribuído ao utilizador de identidade gerido
   - **Selecione** -o membro atribuir acesso
   
   ![Identidade gerida atribuído ao utilizador IAM](./media/how-to-manage-ua-identity-portal/assign-role-screenshot2.png)  
