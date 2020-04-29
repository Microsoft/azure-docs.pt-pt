---
title: Administradores de subscrição clássicos do Azure Microsoft Docs
description: Descreve como adicionar ou alterar as funções de Coadministrador e Administrador de Serviço seleções do Azure e como visualizar o Administrador de Conta.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79243487"
---
# <a name="azure-classic-subscription-administrators"></a>Administradores de subscrição clássicos do Azure

A Microsoft recomenda que gere o acesso aos recursos do Azure utilizando o controlo de acesso baseado em funções (RBAC). No entanto, se ainda estiver a utilizar o modelo de implementação clássico, terá de utilizar uma função clássica de administrador de subscrição: Administrador de Serviço e Coadministrador. Para mais informações, consulte [O Gestor de Recursos Azure vs. implantação clássica](../azure-resource-manager/management/deployment-models.md).

Este artigo descreve como adicionar ou alterar as funções de Coadministrador e Administrador de Serviço, e como visualizar o Administrador de Conta.

## <a name="add-a-co-administrator"></a>Adicione um Coadministrador

> [!TIP]
> Só precisa de adicionar um Coadministrador se o utilizador precisar de gerir as implementações clássicas do Azure utilizando o Módulo PowerShell de Gestão de [Serviços Azure](https://docs.microsoft.com/powershell/module/servicemanagement/azure). Se o utilizador utilizar apenas o portal Azure para gerir os recursos clássicos, não precisará de adicionar o administrador clássico para o utilizador.

1. Inscreva-se no [portal Azure](https://portal.azure.com) como Administrador de Serviço ou Coadministrador.

1. Abra [Subscrições](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) e selecione uma subscrição.

    Os coadministradores só podem ser atribuídos no âmbito da subscrição.

1. Clique em **Controlo de acesso (IAM)**.

1. Clique no separador de **administradores clássicos.**

    ![Screenshot que abre administradores clássicos](./media/classic-administrators/classic-administrators.png)

1. Clique em **Adicionar** > **coadministrador** para abrir o painel de coadministradores adicionar.

    Se a opção de coadministrador adicionar estiver desativada, não tem permissões.

1. Selecione o utilizador que pretende adicionar e clique em **Adicionar**.

    ![Screenshot que adiciona coadministrador](./media/classic-administrators/add-coadmin.png)

## <a name="add-a-guest-user-as-a-co-administrator"></a>Adicione um utilizador convidado como Coadministrador

Para adicionar um utilizador convidado como Coadministrador, siga os mesmos passos que na secção Anterior [Adicionar um Coadministrador.](#add-a-co-administrator) O utilizador convidado deve cumprir os seguintes critérios:

- O utilizador convidado deve ter uma presença no seu diretório. Isto significa que o utilizador foi convidado para o seu diretório e aceitou o convite.

Para mais informações, sobre como adicionar um utilizador convidado ao seu diretório, consulte os utilizadores de [colaboração Add Azure Ative Directory B2B no portal Azure](../active-directory/b2b/add-users-administrator.md).

### <a name="differences-for-guest-users"></a>Diferenças para os utilizadores convidados

Os utilizadores convidados que tenham sido atribuídos ao papel de Coadministrador podem ver algumas diferenças em comparação com os utilizadores membros com a função de Coadministrador. Considere o seguinte cenário:

- User A com uma conta Azure AD (conta de trabalho ou escola) é um Administrador de Serviço para uma subscrição Azure.
- O utilizador B tem uma conta Microsoft.
- Utilizador A atribui a função de Coadministrador ao utilizador B.
- O utilizador B pode fazer quase tudo, mas não consegue registar aplicações ou procurar utilizadores no diretório da AD Azure.

Seria de esperar que o utilizador B conseguisse gerir tudo. A razão para esta diferença é que a conta Microsoft é adicionada à subscrição como utilizador convidado em vez de um utilizador membro. Os utilizadores de hóspedes têm diferentes permissões padrão em AD Azure em comparação com os utilizadores membros. Por exemplo, os utilizadores membros podem ler outros utilizadores em AD Azure e os utilizadores de hóspedes não podem. Os utilizadores membros podem registar novos diretores de serviço em Azure AD e os utilizadores convidados não podem.

Se um utilizador convidado precisar de ser capaz de executar estas tarefas, uma solução possível é atribuir as funções específicas de administrador da AD Azure que o utilizador convidado necessita. Por exemplo, no cenário anterior, poderia atribuir a função de Leitores de [Diretório](../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) para ler outros utilizadores e atribuir a função de Desenvolvedor de [Aplicações](../active-directory/users-groups-roles/directory-assign-admin-roles.md#application-developer) para poder criar diretores de serviço. Para obter mais informações sobre os utilizadores membros e convidados e as suas permissões, consulte [as permissões padrão dos utilizadores no Diretório Ativo do Azure?](../active-directory/fundamentals/users-default-permissions.md) Para obter mais informações sobre a concessão de acesso aos utilizadores convidados, consulte [Gerir o acesso aos recursos do Azure para utilizadores externos que utilizem o RBAC.](role-assignments-external-users.md)

Note que as [funções incorporadas para os recursos Azure](../role-based-access-control/built-in-roles.md) são diferentes das funções de [administrador da AD Azure.](../active-directory/users-groups-roles/directory-assign-admin-roles.md) As funções incorporadas não concedem acesso a Azure AD. Para mais informações, consulte [Compreender os diferentes papéis.](../role-based-access-control/rbac-and-directory-admin-roles.md)

Para obter informações que comparem utilizadores membros e utilizadores convidados, consulte [as permissões padrão dos utilizadores no Diretório Ativo do Azure?](../active-directory/fundamentals/users-default-permissions.md)

## <a name="remove-a-co-administrator"></a>Remover um Coadministrador

1. Inscreva-se no [portal Azure](https://portal.azure.com) como Administrador de Serviço ou Coadministrador.

1. Abra [Subscrições](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) e selecione uma subscrição.

1. Clique em **Controlo de acesso (IAM)**.

1. Clique no separador de **administradores clássicos.**

1. Adicione uma marca de verificação ao lado do Coadministrador que pretende remover.

1. Clique em **Remover**.

1. Na caixa de mensagens que aparece, clique **em Sim**.

    ![Screenshot que remove coadministrador](./media/classic-administrators/remove-coadmin.png)

## <a name="change-the-service-administrator"></a>Alterar o Administrador de Serviços

Só o Administrador de Conta pode alterar o Administrador de Serviço para uma subscrição. Por predefinição, quando se inscreve para uma subscrição do Azure, o Administrador de Serviço é o mesmo que o Administrador de Conta. O utilizador com a função de Administrador de Conta não tem acesso ao portal Azure. O utilizador com a função de Administrador de Serviço tem acesso total ao portal Azure. Se o Administrador de Conta e administrador de serviço forem o mesmo utilizador e mudar o Administrador de Serviço para um utilizador diferente, então o Administrador de Conta perde o acesso ao portal Azure. No entanto, o Administrador de Conta pode sempre utilizar o Centro de Contas para mudar o Administrador de Serviço de volta para si.

Siga estes passos para alterar o Administrador de Serviço no **Centro de Conta**.

### <a name="account-center"></a>Centro de Contas

1. Certifique-se de que o seu cenário é suportado verificando as [limitações para alterar o Administrador de Serviço](#limitations-for-changing-the-service-administrator).

1. Inscreva-se no Centro de [Contas](https://account.windowsazure.com/subscriptions) como Administrador de Conta.

1. Clique numa subscrição.

1. No lado direito, clique em Editar detalhes de **subscrição**.

    ![Screenshot mostrando o botão de subscrição editar no Centro de Conta](./media/classic-administrators/editsub.png)

1. Na caixa **DE SERVIÇO ADMINISTRADOR,** insira o endereço de e-mail do novo Administrador de Serviço.

    ![Screenshot mostrando a caixa para alterar o e-mail do Service Admin](./media/classic-administrators/change-service-admin.png)

1. Clique na marca de verificação para guardar a alteração.

### <a name="limitations-for-changing-the-service-administrator"></a>Limitações para alterar o Administrador de Serviço

Só pode haver um Administrador de Serviço por subscrição azure. A alteração do Administrador de Serviço sairá de forma diferente, dependendo se o Administrador de Conta é uma conta Microsoft ou se se trata de uma conta AD Azure (conta de trabalho ou escolar).

| Conta administradora de conta | Pode alterar o Administrador de Serviço para uma conta Microsoft diferente? | Pode alterar o Administrador de Serviço para uma conta Azure AD no mesmo diretório? | Pode alterar o Administrador de Serviço para uma conta Azure AD num diretório diferente? |
| --- | --- | --- | --- |
| Conta Microsoft | Sim | Não | Não |
| Conta do Azure AD | Sim | Sim | Não |

Se o Administrador de Conta for uma conta Azure AD, pode alterar o Administrador de Serviço para uma conta Azure AD no mesmo diretório, mas não num diretório diferente. Por abby@contoso.com exemplo, pode alterar bob@contoso.como Administrador de Serviço john@notcontoso.com para, mas não pode alterar o Administrador de Serviço para a menos que john@notcontoso.com tenha uma presença no diretório contoso.com.

Para obter mais informações sobre as contas da Microsoft e as contas da AD Azure, consulte [o what is Azure Ative Directory?](../active-directory/fundamentals/active-directory-whatis.md)

## <a name="view-the-account-administrator"></a>Ver o Administrador de Conta

O Administrador de Conta é o utilizador que inicialmente se inscreveu para a subscrição do Azure, sendo responsável como o proprietário da faturação da subscrição. Para alterar o Administrador de Conta de uma subscrição, consulte a [transferência de uma subscrição Do Azure para outra conta](../cost-management-billing/manage/billing-subscription-transfer.md).

Siga estes passos para visualizar o Administrador de Conta.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Abra [Subscrições](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) e selecione uma subscrição.

1. Clique em **Propriedades**.

    O Administrador de Conta da subscrição é apresentado na caixa de Administrador de **Conta.**

    ![Screenshot mostrando o Administrador de Conta](./media/classic-administrators/account-admin.png)

## <a name="next-steps"></a>Passos seguintes

* [Compreender as diferentes funções no Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Manage access to Azure resources using RBAC and the Azure portal](../role-based-access-control/role-assignments-portal.md) (Gerir o acesso a recursos do Azure com RBAC e o portal do Azure)
* [Adicionar ou alterar administradores de subscrição do Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)
