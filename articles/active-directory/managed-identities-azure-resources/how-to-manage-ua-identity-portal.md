---
title: Gerenciar uma identidade gerenciada atribuída pelo usuário no portal do Azure-Azure AD
description: Instruções passo a passo sobre como criar, listar, excluir e atribuir uma função a uma identidade gerenciada atribuída pelo usuário.
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
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74547448"
---
# <a name="create-list-delete-or-assign-a-role-to-a-user-assigned-managed-identity-using-the-azure-portal"></a>Criar, listar, excluir ou atribuir uma função a uma identidade gerenciada atribuída pelo usuário usando o portal do Azure

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Identidades gerenciadas para recursos do Azure fornecem serviços do Azure com uma identidade gerenciada no Azure Active Directory. Você pode usar essa identidade para autenticar em serviços que dão suporte à autenticação do Azure AD, sem precisar de credenciais em seu código. 

Neste artigo, você aprende a criar, listar, excluir ou atribuir uma função a uma identidade gerenciada atribuída pelo usuário usando o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com identidades gerenciadas para recursos do Azure, confira a [seção visão geral](overview.md). **Certifique-se de examinar a [diferença entre uma identidade gerenciada atribuída pelo sistema e](overview.md#how-does-the-managed-identities-for-azure-resources-work)** atribuída pelo usuário.
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.

## <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerida atribuída pelo utilizador

Para criar uma identidade gerenciada atribuída pelo usuário, sua conta precisa da atribuição de função de [colaborador de identidade gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) .

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta associada à assinatura do Azure para criar a identidade gerenciada atribuída pelo usuário.
2. Na caixa de pesquisa, digite *identidades gerenciadas*e, em **Serviços**, clique em **identidades gerenciadas**.
3. Clique em **Adicionar** e insira valores nos campos a seguir em criar painel de identidade **gerenciada atribuída pelo usuário** :
   - **Nome do recurso**: esse é o nome da identidade gerenciada atribuída pelo usuário, por exemplo, UAI1.
   - **Assinatura**: escolha a assinatura para criar a identidade gerenciada atribuída pelo usuário em
   - **Grupo de recursos**: Crie um novo grupo de recursos para conter sua identidade gerenciada atribuída pelo usuário ou escolha **usar existente** para criar a identidade gerenciada atribuída pelo usuário em um grupo de recursos existente.
   - **Local**: escolha um local para implantar a identidade gerenciada atribuída pelo usuário, por exemplo, **oeste dos EUA**.
4. Clique em **Criar**.

![Criar uma identidade gerida atribuída pelo utilizador](./media/how-to-manage-ua-identity-portal/create-user-assigned-managed-identity-portal.png)

## <a name="list-user-assigned-managed-identities"></a>Listar identidades gerenciadas atribuídas pelo usuário

Para listar/ler uma identidade gerenciada atribuída pelo usuário, sua conta precisa do [operador de identidade gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-operator) ou da atribuição de função de [colaborador de identidade gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) .

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta associada à assinatura do Azure para listar as identidades gerenciadas atribuídas pelo usuário.
2. Na caixa de pesquisa, digite *identidades gerenciadas*e, em serviços, clique em **identidades gerenciadas**.
3. É retornada uma lista das identidades gerenciadas atribuídas pelo usuário para sua assinatura.  Para ver os detalhes de uma identidade gerenciada atribuída pelo usuário, clique em seu nome.

![Listar identidade gerenciada atribuída pelo usuário](./media/how-to-manage-ua-identity-portal/list-user-assigned-managed-identity-portal.png)

## <a name="delete-a-user-assigned-managed-identity"></a>Excluir uma identidade gerenciada atribuída pelo usuário

Para excluir uma identidade gerenciada atribuída pelo usuário, sua conta precisa da atribuição de função de [colaborador de identidade gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) .

A exclusão de uma identidade atribuída pelo usuário não a remove da VM ou do recurso ao qual ela foi atribuída.  Para remover a identidade atribuída pelo usuário de uma VM, consulte [remover uma identidade gerenciada atribuída pelo usuário de uma VM](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#remove-a-user-assigned-managed-identity-from-a-vm).

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta associada à assinatura do Azure para excluir uma identidade gerenciada atribuída pelo usuário.
2. Selecione a identidade gerenciada atribuída pelo usuário e clique em **excluir**.
3. Na caixa de confirmação, escolha **Sim**.

![Excluir identidade gerenciada atribuída pelo usuário](./media/how-to-manage-ua-identity-portal/delete-user-assigned-managed-identity-portal.png)

## <a name="assign-a-role-to-a-user-assigned-managed-identity"></a>Atribuir uma função a uma identidade gerenciada atribuída pelo usuário 

Para atribuir uma função a uma identidade gerenciada atribuída pelo usuário, sua conta precisa da atribuição de função de [administrador de acesso do usuário](/azure/role-based-access-control/built-in-roles#user-access-administrator) .

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta associada à assinatura do Azure para listar as identidades gerenciadas atribuídas pelo usuário.
2. Na caixa de pesquisa, digite *identidades gerenciadas*e, em serviços, clique em **identidades gerenciadas**.
3. É retornada uma lista das identidades gerenciadas atribuídas pelo usuário para sua assinatura.  Selecione a identidade gerenciada atribuída pelo usuário para a qual você deseja atribuir uma função.
4. Selecione **controle de acesso (iam)** e, em seguida, selecione **Adicionar atribuição de função**.

   ![Início de identidade gerenciada atribuída pelo usuário](./media/how-to-manage-ua-identity-portal/assign-role-screenshot1.png)

5. Na folha adicionar atribuição de função, configure os seguintes valores e, em seguida, clique em **salvar**:
   - **Função** -a função a ser atribuída
   - **Atribuir acesso ao** -o recurso para atribuir a identidade gerenciada atribuída pelo usuário
   - **Select** -o membro para atribuir acesso
   
   ![IAM de identidade gerenciada atribuída pelo usuário](./media/how-to-manage-ua-identity-portal/assign-role-screenshot2.png)  
