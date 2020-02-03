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
ms.date: 01/22/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 2493e893f9afda0642bd838c94538dd0b984bce5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720661"
---
# <a name="azure-classic-subscription-administrators"></a>Administradores de assinatura clássica do Azure

A Microsoft recomenda que você gerencie o acesso aos recursos do Azure usando o RBAC (controle de acesso baseado em função). No entanto, se você ainda estiver usando o modelo de implantação clássico, precisará usar uma função de administrador de assinatura clássica: administrador de serviços e coadministrador. Para mais informações, consulte [O Gestor de Recursos Azure vs. implantação clássica](../azure-resource-manager/management/deployment-models.md).

Este artigo descreve como adicionar ou alterar as funções de coadministrador e administrador de serviços e como exibir o administrador da conta.

## <a name="add-a-co-administrator"></a>Adicionar um coadministrador

> [!TIP]
> Só precisa de adicionar um Coadministrador se o utilizador precisar de gerir as implementações clássicas do Azure utilizando o Módulo PowerShell de Gestão de [Serviços Azure](https://docs.microsoft.com/powershell/module/servicemanagement/azure). Se o usuário usar apenas o portal do Azure para gerenciar os recursos clássicos, você não precisará adicionar o administrador clássico do usuário.

1. Inscreva-se no [portal Azure](https://portal.azure.com) como Administrador de Serviço ou Coadministrador.

1. Abra [Subscrições](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) e selecione uma subscrição.

    Os coadministradores só podem ser atribuídos no escopo da assinatura.

1. Clique em **Controlo de acesso (IAM)** .

1. Clique no separador de **administradores clássicos.**

    ![Captura de tela que abre os administradores clássicos](./media/classic-administrators/classic-administrators.png)

1. Clique em **Adicionar** > **Adicionar coadministrador** para abrir o painel de coadministradores adicionar.

    Se a opção Adicionar coadministrador estiver desabilitada, você não terá permissões.

1. Selecione o utilizador que pretende adicionar e clique em **Adicionar**.

    ![Captura de tela que adiciona o coadministrador](./media/classic-administrators/add-coadmin.png)

## <a name="add-a-guest-user-as-a-co-administrator"></a>Adicionar um usuário convidado como um coadministrador

Para adicionar um utilizador convidado como Coadministrador, siga os mesmos passos que na secção Anterior [Adicionar um Coadministrador.](#add-a-co-administrator) O usuário convidado deve atender aos seguintes critérios:

- O usuário convidado deve ter uma presença em seu diretório. Isso significa que o usuário foi convidado para seu diretório e aceitou o convite.

Para mais informações, sobre como adicionar um utilizador convidado ao seu diretório, consulte os utilizadores de [colaboração Add Azure Ative Directory B2B no portal Azure](../active-directory/b2b/add-users-administrator.md).

### <a name="differences-for-guest-users"></a>Diferenças para usuários convidados

Os usuários convidados que receberam a função de coadministrador podem ver algumas diferenças em comparação com os usuários Membros com a função de coadministrador. Considere o seguinte cenário:

- O usuário A com uma conta do Azure AD (conta corporativa ou de estudante) é um administrador de serviço para uma assinatura do Azure.
- O usuário B tem um conta Microsoft.
- O usuário A atribui a função de coadministrador ao usuário B.
- O usuário B pode fazer quase tudo, mas não é possível registrar aplicativos ou Pesquisar usuários no diretório do AD do Azure.

Você esperaria que o usuário B pudesse gerenciar tudo. O motivo dessa diferença é que a conta Microsoft é adicionada à assinatura como um usuário convidado, em vez de um usuário membro. Os usuários convidados têm permissões padrão diferentes no Azure AD, em comparação com os usuários membros. Por exemplo, os usuários Membros podem ler outros usuários no Azure AD e os usuários convidados não podem. Os usuários Membros podem registrar novas entidades de serviço no Azure AD e os usuários convidados não podem.

Se um usuário convidado precisar executar essas tarefas, uma solução possível é atribuir as funções de administrador específicas do Azure AD de que o usuário convidado precisa. Por exemplo, no cenário anterior, poderia atribuir a função de Leitores de [Diretório](../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) para ler outros utilizadores e atribuir a função de Desenvolvedor de [Aplicações](../active-directory/users-groups-roles/directory-assign-admin-roles.md#application-developer) para poder criar diretores de serviço. Para obter mais informações sobre os utilizadores membros e convidados e as suas permissões, consulte [as permissões padrão dos utilizadores no Diretório Ativo do Azure?](../active-directory/fundamentals/users-default-permissions.md) Para obter mais informações sobre a concessão de acesso aos utilizadores convidados, consulte [Gerir o acesso aos recursos do Azure para utilizadores externos que utilizem o RBAC.](role-assignments-external-users.md)

Note que as [funções incorporadas para os recursos Azure](../role-based-access-control/built-in-roles.md) são diferentes das funções de [administrador da AD Azure.](../active-directory/users-groups-roles/directory-assign-admin-roles.md) As funções internas não concedem nenhum acesso ao Azure AD. Para mais informações, consulte [Compreender os diferentes papéis.](../role-based-access-control/rbac-and-directory-admin-roles.md)

Para obter informações que comparem utilizadores membros e utilizadores convidados, consulte [as permissões padrão dos utilizadores no Diretório Ativo do Azure?](../active-directory/fundamentals/users-default-permissions.md)

## <a name="remove-a-co-administrator"></a>Remover um coadministrador

1. Inscreva-se no [portal Azure](https://portal.azure.com) como Administrador de Serviço ou Coadministrador.

1. Abra [Subscrições](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) e selecione uma subscrição.

1. Clique em **Controlo de acesso (IAM)** .

1. Clique no separador de **administradores clássicos.**

1. Adicione uma marca de seleção ao lado do coadministrador que você deseja remover.

1. Clique em **Remover**.

1. Na caixa de mensagens que aparece, clique **em Sim**.

    ![Captura de tela que remove o coadministrador](./media/classic-administrators/remove-coadmin.png)

## <a name="change-the-service-administrator"></a>Alterar o Administrador de Serviços

Somente o administrador da conta pode alterar o administrador de serviço para uma assinatura. Por padrão, quando você se inscreve para uma assinatura do Azure, o administrador de serviço é o mesmo que o administrador da conta. O usuário com a função de administrador de conta não tem acesso ao portal do Azure. O usuário com a função de administrador de serviços tem acesso completo ao portal do Azure. Se o administrador da conta e o administrador de serviços forem o mesmo usuário e você alterar o administrador de serviços para um usuário diferente, o administrador da conta perderá o acesso a portal do Azure. No entanto, o administrador da conta sempre pode usar o centro de contas para alterar o administrador de serviço para si mesmo.

Siga estes passos para alterar o Administrador de Serviço no **Centro de Conta**.

### <a name="account-center"></a>Centro de contas

1. Certifique-se de que o seu cenário é suportado verificando as [limitações para alterar o Administrador de Serviço](#limitations-for-changing-the-service-administrator).

1. Inscreva-se no Centro de [Contas](https://account.windowsazure.com/subscriptions) como Administrador de Conta.

1. Clique em uma assinatura.

1. No lado direito, clique em Editar detalhes de **subscrição**.

    ![Captura de tela mostrando o botão Editar assinatura no centro de contas](./media/classic-administrators/editsub.png)

1. Na caixa **DE SERVIÇO ADMINISTRADOR,** insira o endereço de e-mail do novo Administrador de Serviço.

    ![Captura de tela mostrando a caixa para alterar o email do administrador do serviço](./media/classic-administrators/change-service-admin.png)

1. Clique na marca de seleção para salvar a alteração.

### <a name="limitations-for-changing-the-service-administrator"></a>Limitações para alterar o administrador de serviços

Só pode haver um administrador de serviços por assinatura do Azure. A alteração do administrador de serviços se comportará de maneira diferente, dependendo se o administrador da conta for um conta Microsoft ou se é uma conta do Azure AD (conta corporativa ou de estudante).

| Conta de administrador da conta | Pode alterar o administrador de serviços para um conta Microsoft diferente? | Pode alterar o administrador de serviço para uma conta do Azure AD no mesmo diretório? | Pode alterar o administrador de serviço para uma conta do Azure AD em um diretório diferente? |
| --- | --- | --- | --- |
| Conta Microsoft | Sim | Não | Não |
| Conta do Azure AD | Sim | Sim | Não |

Se o administrador da conta for uma conta do Azure AD, você poderá alterar o administrador do serviço para uma conta do Azure AD no mesmo diretório, mas não em um diretório diferente. Por exemplo, abby@contoso.com podem mudar o Administrador de Serviço para bob@contoso.com, mas não podem mudar o Administrador de Serviço para john@notcontoso.com a menos que john@notcontoso.com tenha presença no diretório contoso.com.

Para obter mais informações sobre as contas da Microsoft e as contas da AD Azure, consulte [o what is Azure Ative Directory?](../active-directory/fundamentals/active-directory-whatis.md)

## <a name="view-the-account-administrator"></a>Exibir o administrador da conta

O administrador da conta é o usuário que se inscreveu inicialmente para a assinatura do Azure e é responsável como o proprietário da cobrança da assinatura. Para alterar o Administrador de Conta de uma subscrição, consulte a [transferência de uma subscrição Do Azure para outra conta](../cost-management-billing/manage/billing-subscription-transfer.md).

Siga estas etapas para exibir o administrador da conta.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Abra [Subscrições](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) e selecione uma subscrição.

1. Clique em **Propriedades**.

    O Administrador de Conta da subscrição é apresentado na caixa de Administrador de **Conta.**

    ![Captura de tela mostrando o administrador da conta](./media/classic-administrators/account-admin.png)

## <a name="next-steps"></a>Passos Seguintes

* [Compreender as diferentes funções no Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Manage access to Azure resources using RBAC and the Azure portal](../role-based-access-control/role-assignments-portal.md) (Gerir o acesso a recursos do Azure com RBAC e o portal do Azure)
* [Adicionar ou alterar administradores de subscrição do Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)
