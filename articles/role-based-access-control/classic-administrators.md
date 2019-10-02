---
title: Administradores de assinatura clássica do Azure | Microsoft Docs
description: Descreve como adicionar ou alterar as funções de administrador de serviços e coadministrador do Azure e como exibir o administrador da conta.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/01/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 9c5e87d8d6fe49302bee2b2248f84ba98a650533
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802319"
---
# <a name="azure-classic-subscription-administrators"></a>Administradores de assinatura clássica do Azure

A Microsoft recomenda que você gerencie o acesso aos recursos do Azure usando o RBAC (controle de acesso baseado em função). No entanto, se você ainda estiver usando o modelo de implantação clássico, precisará usar uma função de administrador de assinatura clássica: Administrador de serviços e coadministrador. Para obter mais informações, consulte [Azure Resource Manager vs. implantação clássica](../azure-resource-manager/resource-manager-deployment-model.md).

Este artigo descreve como adicionar ou alterar as funções de coadministrador e administrador de serviços e como exibir o administrador da conta.

## <a name="add-a-co-administrator"></a>Adicionar um coadministrador

> [!TIP]
> Você só precisa adicionar um coadministrador se o usuário precisar gerenciar implantações clássicas do Azure usando o [módulo do PowerShell de gerenciamento de serviços do Azure](https://docs.microsoft.com/powershell/module/servicemanagement/azure). Se o usuário usar apenas o portal do Azure para gerenciar os recursos clássicos, você não precisará adicionar o administrador clássico do usuário.

1. Entre no [portal do Azure](https://portal.azure.com) como um administrador de serviço.

1. Abra [Subscrições](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) e selecione uma subscrição.

    Os coadministradores só podem ser atribuídos no escopo da assinatura.

1. Clique em **controlo de acesso (IAM)** .

1. Clique na guia **Administradores clássicos** .

    ![Captura de tela que abre os administradores clássicos](./media/classic-administrators/classic-administrators.png)

1. Clique em **adicionar** > **Adicionar coadministrador** para abrir o painel Adicionar co-administradores.

    Se a opção Adicionar coadministrador estiver desabilitada, você não terá permissões.

1. Selecione o usuário que você deseja adicionar e clique em **Adicionar**.

    ![Captura de tela que adiciona o coadministrador](./media/classic-administrators/add-coadmin.png)

### <a name="adding-a-guest-user-as-a-co-administrator"></a>Adicionando um usuário convidado como um coadministrador

[Os usuários convidados](../active-directory/b2b/b2b-quickstart-add-guest-users-portal.md) que receberam a função de coadministrador podem ver algumas diferenças em comparação com os usuários Membros com a função de coadministrador. Considere o seguinte cenário:

- O usuário A com uma conta do Azure AD (conta corporativa ou de estudante) é um administrador de serviço para uma assinatura do Azure.
- O usuário B tem um conta Microsoft.
- O usuário A atribui a função de coadministrador ao usuário B.
- O usuário B pode fazer quase tudo, mas não é possível registrar aplicativos ou Pesquisar usuários no diretório do AD do Azure.

Você esperaria que o usuário B pudesse gerenciar tudo. O motivo dessa diferença é que a conta Microsoft é adicionada à assinatura como um usuário convidado, em vez de um usuário membro. Os usuários convidados têm permissões padrão diferentes no Azure AD, em comparação com os usuários membros. Por exemplo, os usuários Membros podem ler outros usuários no Azure AD e os usuários convidados não podem. Os usuários Membros podem registrar novas entidades de serviço no Azure AD e os usuários convidados não podem.

Se um usuário convidado precisar executar essas tarefas, uma solução possível é atribuir as funções de administrador específicas do Azure AD de que o usuário convidado precisa. Por exemplo, no cenário anterior, você pode atribuir a função de [leitores de diretório](../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) para ler outros usuários e atribuir a função de desenvolvedor de [aplicativo](../active-directory/users-groups-roles/directory-assign-admin-roles.md#application-developer) para poder criar entidades de serviço. Para obter mais informações sobre usuários Membros e convidados e suas permissões, consulte [quais são as permissões de usuário padrão no Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md).

Observe que as [funções internas para recursos do Azure](../role-based-access-control/built-in-roles.md) são diferentes das funções de [administrador do Azure ad](../active-directory/users-groups-roles/directory-assign-admin-roles.md). As funções internas não concedem nenhum acesso ao Azure AD. Para obter mais informações, consulte [entender as diferentes funções](../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="remove-a-co-administrator"></a>Remover um coadministrador

1. Entre no [portal do Azure](https://portal.azure.com) como um administrador de serviço.

1. Abra [Subscrições](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) e selecione uma subscrição.

1. Clique em **controlo de acesso (IAM)** .

1. Clique na guia **Administradores clássicos** .

1. Adicione uma marca de seleção ao lado do coadministrador que você deseja remover.

1. Clique em **remover**.

1. Na caixa de mensagem exibida, clique em **Sim**.

    ![Captura de tela que remove o coadministrador](./media/classic-administrators/remove-coadmin.png)

## <a name="change-the-service-administrator"></a>Alterar o Administrador de Serviços

Somente o administrador da conta pode alterar o administrador de serviço para uma assinatura. Por padrão, quando você se inscreve para uma assinatura do Azure, o administrador de serviço é o mesmo que o administrador da conta. O usuário com a função de administrador de conta não tem acesso ao portal do Azure. O usuário com a função de administrador de serviços tem acesso completo ao portal do Azure. Se o administrador da conta e o administrador de serviços forem o mesmo usuário e você alterar o administrador de serviços para um usuário diferente, o administrador da conta perderá o acesso a portal do Azure. No entanto, o administrador da conta sempre pode usar o centro de contas para alterar o administrador de serviço para si mesmo.

Existem duas formas de alterar o Administrador de Serviços. Você pode alterar na **portal do Azure** ou no **centro de contas**.

### <a name="azure-portal"></a>Portal do Azure

1. Verifique se o cenário tem suporte verificando as limitações para alterar os administradores de serviço.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como Administrador de Conta.

1. Abra [Subscrições](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) e selecione uma subscrição.

1. Clique em **Propriedades**.

    ![Captura de tela mostrando o administrador da conta](./media/classic-administrators/account-admin.png)

1. Na parte superior, clique em **administrador de serviços** para abrir o painel administrador de serviços.

    Se o botão administrador de serviço estiver desabilitado, você não terá permissões. Somente o usuário que é o administrador da conta pode alterar o administrador do serviço.

1. Selecione um novo administrador de serviços e clique em **salvar**.

### <a name="account-center"></a>Centro de contas

1. Verifique se o cenário tem suporte verificando as limitações para alterar os administradores de serviço.

1. Entre no [centro de contas](https://account.windowsazure.com/subscriptions) como o administrador da conta.

1. Clique em uma assinatura.

1. No lado direito, clique em **Editar detalhes da assinatura**.

    ![Captura de tela mostrando o botão Editar assinatura no centro de contas](./media/classic-administrators/editsub.png)

1. Na caixa **administrador de serviços** , insira o endereço de email do novo administrador de serviços.

    ![Captura de tela mostrando a caixa para alterar o email do administrador do serviço](./media/classic-administrators/change-service-admin.png)

1. Clique na marca de seleção para salvar a alteração.

### <a name="limitations-for-changing-the-service-administrator"></a>Limitações para alterar o administrador de serviços

Só pode haver um administrador de serviços por assinatura do Azure. A alteração do administrador de serviços se comportará de maneira diferente, dependendo se o administrador da conta for um conta Microsoft ou se é uma conta do Azure AD (conta corporativa ou de estudante).

| Conta de administrador da conta | Pode alterar o administrador de serviços para um conta Microsoft diferente? | Pode alterar o administrador de serviço para uma conta do Azure AD no mesmo diretório? | Pode alterar o administrador de serviço para uma conta do Azure AD em um diretório diferente? |
| --- | --- | --- | --- |
| Conta Microsoft | Sim | Não | Não |
| Conta do Azure AD | Sim | Sim | Não |

Se o administrador da conta for uma conta do Azure AD, você poderá alterar o administrador do serviço para uma conta do Azure AD no mesmo diretório, mas não em um diretório diferente. Por exemplo, abby@contoso.com pode alterar o administrador de serviço para bob@contoso.com, mas não pode alterar o administrador de serviços para john@notcontoso.com, a menos que john@notcontoso.com tenha uma presença no diretório contoso.com.

Para obter mais informações sobre contas da Microsoft e contas do Azure AD, consulte [o que é Azure Active Directory?](../active-directory/fundamentals/active-directory-whatis.md).

## <a name="view-the-account-administrator"></a>Exibir o administrador da conta

O administrador da conta é o usuário que se inscreveu inicialmente para a assinatura do Azure e é responsável como o proprietário da cobrança da assinatura. Para alterar o administrador da conta de uma assinatura, consulte [transferir a propriedade de uma assinatura do Azure para outra conta](../billing/billing-subscription-transfer.md).

Siga estas etapas para exibir o administrador da conta.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Abra [Subscrições](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) e selecione uma subscrição.

1. Clique em **Propriedades**.

    O administrador da conta da assinatura é exibido na caixa **administrador da conta** .

    ![Captura de tela mostrando o administrador da conta](./media/classic-administrators/account-admin.png)

## <a name="next-steps"></a>Passos seguintes

* [Compreender as diferentes funções no Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Manage access to Azure resources using RBAC and the Azure portal](../role-based-access-control/role-assignments-portal.md) (Gerir o acesso a recursos do Azure com RBAC e o portal do Azure)
* [Adicionar ou alterar administradores de subscrição do Azure](../billing/billing-add-change-azure-subscription-administrator.md)
