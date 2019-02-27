---
title: Os administradores de subscrição clássica do Azure | Documentos da Microsoft
description: Descreve como adicionar ou alterar as funções de Coadministrador do Azure e o administrador de serviços e como visualizar o administrador de conta.
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
ms.date: 02/19/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: a19153677e691d2681c131df67100a43ef9532a0
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56889329"
---
# <a name="azure-classic-subscription-administrators"></a>Administradores de subscrição clássica do Azure

A Microsoft recomenda que gerencie o acesso aos recursos do Azure com o controlo de acesso baseado em funções (RBAC). No entanto, se ainda estiver a utilizar o modelo de implementação clássica, terá de utilizar uma função de administrador de subscrição clássica: Administrador de serviços e Coadministrador. Para obter mais informações, consulte [vs. de implementação clássica do Azure Resource Manager](../azure-resource-manager/resource-manager-deployment-model.md).

Este artigo descreve como adicionar ou alterar as funções de Coadministrador e administrador de serviços e como visualizar o administrador de conta.

## <a name="add-a-co-administrator"></a>Adicionar um Coadministrador

> [!TIP]
> Só tem de adicionar um Coadministrador se o utilizador precisar gerir implementações clássicas do Azure, utilizando [módulo do PowerShell de gestão de serviço de Azure](https://docs.microsoft.com/en-us/powershell/module/servicemanagement/azure). Se o utilizador utiliza apenas o portal do Azure para gerir os recursos clássicos, não terá de adicionar o administrador clássico para o utilizador.

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) como um administrador de serviços.

1. Open [subscrições](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) e selecione uma subscrição.

    Coadministradores só podem ser atribuídos ao âmbito da subscrição.

1. Clique em **controlo de acesso (IAM)**.

1. Clique nas **administradores clássico** separador.

    ![Captura de ecrã que é aberta a administradores de clássico](./media/classic-administrators/classic-administrators.png)

1. Clique em **Add** > **adicionar coadministrador** para abrir o painel de coadministradores de adicionar.

    Se a opção de coadministrador adicionar estiver desativada, não tem permissões.

1. Selecione o utilizador que pretende adicionar e clique em **adicionar**.

    ![Captura de ecrã que adiciona coadministrador](./media/classic-administrators/add-coadmin.png)

### <a name="adding-a-guest-user-as-a-co-administrator"></a>Adicionar um utilizador convidado como um Coadministrador

[Os utilizadores convidados](../active-directory/b2b/b2b-quickstart-add-guest-users-portal.md) que tenha recebido o Coadministrador função poderá ver algumas diferenças em comparação com os utilizadores de membro com a função de Coadministrador. Considere o seguinte cenário:

- O utilizador A com uma conta escolar ou profissional do Azure AD é um administrador de serviços para uma subscrição do Azure.
- O utilizador B tem uma conta Microsoft.
- O utilizador A atribui a função de Coadministrador ao utilizador B.
- O utilizador B pode fazer quase tudo, mas não consegue registar aplicações ou procurar os utilizadores no diretório do Azure AD.

Poderia esperar que o utilizador B pode gerir tudo. O motivo para essa diferença é que a conta da Microsoft é adicionada à subscrição como um utilizador convidado, em vez de um utilizador de membro. Os utilizadores convidados têm diferentes permissões predefinidas no Azure AD em comparação com os utilizadores membros. Por exemplo, os utilizadores membros podem ler a outros utilizadores no Azure AD e os utilizadores convidados não é possível. Utilizadores membros podem registar novos principais de serviço no Azure AD e os utilizadores convidados não podem. Se um utilizador convidado tem de ser capaz de executar estas tarefas, uma solução possível é específica de atribuir funções de administrador do Azure AD tem do utilizador convidado. Por exemplo, no cenário anterior, pode atribuir a [leitores de diretório](../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) função ler outros utilizadores e atribuir a [programador da aplicação](../active-directory/users-groups-roles/directory-assign-admin-roles.md#application-developer) função para poder criar principais de serviço. Para obter mais informações sobre o membro e os utilizadores convidados e as respetivas permissões, consulte [quais são as permissões de usuário padrão no Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md).

Tenha em atenção que o [funções incorporadas para recursos do Azure](../role-based-access-control/built-in-roles.md) são diferentes do que o [funções de administrador do Azure AD](../active-directory/users-groups-roles/directory-assign-admin-roles.md). As funções incorporadas não concedem acesso ao Azure AD. Para obter mais informações, consulte [compreender as diferentes funções](../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="remove-a-co-administrator"></a>Remover Coadministrador

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) como um administrador de serviços.

1. Open [subscrições](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) e selecione uma subscrição.

1. Clique em **controlo de acesso (IAM)**.

1. Clique nas **administradores clássico** separador.

1. Adicione uma marca de verificação junto a Coadministrador que pretende remover.

1. Clique em **remover**.

1. Na caixa de mensagem que aparece, clique em **Sim**.

    ![Captura de ecrã que remove o coadministrador](./media/classic-administrators/remove-coadmin.png)

## <a name="change-the-service-administrator"></a>Alterar o Administrador de Serviços

Apenas o administrador de conta pode alterar o administrador de serviços para uma subscrição. Por predefinição, quando se inscreve para uma subscrição do Azure, o administrador de serviços é o mesmo como o administrador de conta. O utilizador com a função de administrador de conta não tem acesso ao portal do Azure. O utilizador com a função de administrador de serviço tem acesso total ao portal do Azure. Se o administrador de conta e o administrador de serviço são o mesmo utilizador e alterar o administrador de serviço para um utilizador diferente, em seguida, o administrador de conta perde o acesso ao portal do Azure. No entanto, o administrador de conta, pode utilizar Centro de contas sempre para alterar o administrador de serviços para si.

Existem duas formas de alterar o administrador de serviço. Pode alterar o **portal do Azure** ou **Centro de contas**.

### <a name="azure-portal"></a>Portal do Azure

1. Certifique-se de que o seu cenário é suportado, verificando as limitações para alterar os administradores de serviços.

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) como o administrador de conta.

1. Open [subscrições](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) e selecione uma subscrição.

1. Clique em **Propriedades**.

    ![Captura de ecrã que mostra o administrador de conta](./media/classic-administrators/account-admin.png)

1. Na parte superior, clique em **Admin de serviço** para abrir o painel de administrador de serviço.

    Se o botão de administrador de serviço estiver desativado, não tem permissões. Apenas o utilizador que é o administrador de conta pode alterar o administrador de serviços.

1. Selecione um novo administrador de serviço e, em seguida, clique em **guardar**.

### <a name="account-center"></a>Centro de contas

1. Certifique-se de que o seu cenário é suportado, verificando as limitações para alterar os administradores de serviços.

1. Inicie sessão no [Centro de contas](https://account.windowsazure.com/subscriptions) como o administrador de conta.

1. Clique numa subscrição.

1. No lado direito, clique em **editar detalhes da subscrição**.

    ![Captura de ecrã que mostra o botão de subscrição de edição no Centro de contas](./media/classic-administrators/editsub.png)

1. Na **administrador de serviços** , introduza o endereço de e-mail do administrador de serviços novos.

    ![Captura de ecrã que mostra a caixa para alterar o e-mail de administrador de serviço](./media/classic-administrators/change-service-admin.png)

1. Clique na marca de verificação para guardar a alteração.

### <a name="limitations-for-changing-the-service-administrator"></a>Limitações para alterar o administrador de serviços

Cada subscrição está associada um diretório do Azure AD. Para localizar o diretório da subscrição está associada, abra **subscrições** no portal do Azure e, em seguida, selecione uma subscrição para ver o diretório.

Se tiver entrado com uma conta escolar ou profissional, pode adicionar outras contas na sua organização como administrador de serviço. Por exemplo, abby@contoso.com pode adicionar bob@contoso.com como o administrador de serviços, mas não é possível adicionar john@notcontoso.com , a menos que john@notcontoso.com tiver uma presença no diretório de contoso.com. Os utilizadores com sessão iniciada com o trabalho ou contas de instituição de ensino podem continuar a adicionar utilizadores com contas Microsoft como o administrador de serviço.

  | Método de início de sessão | Adicionar como um administrador de serviços de utilizador da conta Microsoft? | Adicionar conta escolar ou profissional na mesma organização como um administrador de serviços? | Adicionar conta escolar ou profissional na outra organização como um administrador de serviços? |
  | --- | --- | --- | --- |
  |  Conta Microsoft |Sim |Não |Não |
  |  Conta escolar ou profissional |Sim |Sim |Não |

## <a name="view-the-account-administrator"></a>Ver o administrador de conta

O administrador de conta é o utilizador que inicialmente se inscreveu na subscrição do Azure e é responsável como o proprietário de faturação da subscrição. Para alterar o administrador de conta de uma subscrição, consulte [transferir a propriedade de uma subscrição do Azure para outra conta](../billing/billing-subscription-transfer.md).

Siga estes passos para ver o administrador de conta.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Open [subscrições](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) e selecione uma subscrição.

1. Clique em **Propriedades**.

    O administrador de conta da subscrição é apresentado na **administrador de conta** caixa.

    ![Captura de ecrã que mostra o administrador de conta](./media/classic-administrators/account-admin.png)

## <a name="next-steps"></a>Passos Seguintes

* [Compreender as diferentes funções no Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Gerir o acesso aos recursos do Azure através do RBAC e o portal do Azure](../role-based-access-control/role-assignments-portal.md)
* [Adicionar ou alterar administradores de subscrição do Azure](../billing/billing-add-change-azure-subscription-administrator.md)
