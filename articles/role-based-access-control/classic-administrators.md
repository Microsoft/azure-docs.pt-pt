---
title: Administradores de subscrição clássicos do Azure
description: Descreve como adicionar ou alterar as funções de administrador de Co-Administrator e de serviço do Azure e como ver o Administrador de Conta.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/30/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: b61636d6048f63ae962d4f755a29c02e6785d5e1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100557538"
---
# <a name="azure-classic-subscription-administrators"></a>Administradores de subscrição clássicos do Azure

A Microsoft recomenda que você gere o acesso aos recursos Azure usando o controlo de acesso baseado em funções Azure (Azure RBAC). No entanto, se ainda estiver a utilizar o modelo clássico de implementação, terá de utilizar uma função clássica de administrador de subscrição: Administrador de Serviço e Coadministrador. Para obter mais informações, consulte [Azure Resource Manager vs. design clássico](../azure-resource-manager/management/deployment-models.md).

Este artigo descreve como adicionar ou alterar as funções de administrador de Co-Administrator e de serviço e como ver o Administrador de Conta.

## <a name="add-a-co-administrator"></a>Adicionar um Coadministrador

> [!TIP]
> Só precisa de adicionar um Co-Administrator se o utilizador precisar de gerir as implementações clássicas do Azure utilizando o [Módulo PowerShell de Gestão de Serviços Azure](/powershell/module/servicemanagement/azure.service). Se o utilizador utilizar apenas o portal Azure para gerir os recursos clássicos, não necessitará de adicionar o administrador clássico para o utilizador.

1. Inscreva-se no [portal Azure](https://portal.azure.com) como Administrador de Serviço ou Coadministrador.

1. Abra [Subscrições](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) e selecione uma subscrição.

    Co-Administrators só podem ser atribuídos no âmbito da subscrição.

1. Clique em **Controlo de acesso (IAM)**.

1. Clique no separador **Administradores Clássicos.**

    ![Screenshot que abre administradores clássicos](./media/classic-administrators/classic-administrators.png)

1. Clique **em Adicionar** Adicionar  >  **coadministrador** para abrir o painel de coadministradores Add.

    Se a opção de coadministrador add estiver desativada, não tem permissões.

1. Selecione o utilizador que pretende adicionar e clique **em Adicionar**.

    ![Screenshot que adiciona coadministrador](./media/classic-administrators/add-coadmin.png)

## <a name="add-a-guest-user-as-a-co-administrator"></a>Adicione um utilizador convidado como Co-Administrator

Para adicionar um utilizador convidado como Coadministrador, siga os mesmos passos que na secção anterior [adicionar um Coadministrador.](#add-a-co-administrator) O utilizador convidado deve cumprir os seguintes critérios:

- O utilizador convidado deve ter uma presença no seu diretório. Isto significa que o utilizador foi convidado para o seu diretório e aceitou o convite.

Para obter mais informações sobre como adicionar um utilizador convidado ao seu diretório, consulte [os utilizadores de colaboração Do Diretório B2B do Add Azure Ative No portal Azure](../active-directory/external-identities/add-users-administrator.md).

### <a name="differences-for-guest-users"></a>Diferenças para os utilizadores convidados

Os utilizadores convidados que tenham sido designados Co-Administrator papel podem ver algumas diferenças em comparação com os utilizadores membros com o papel Co-Administrator. Considere o seguinte cenário:

- O Utilizador A com uma conta AD Azure (trabalho ou conta escolar) é um Administrador de Serviço para uma subscrição do Azure.
- O utilizador B tem uma conta Microsoft.
- O utilizador A atribui a função Co-Administrator ao utilizador B.
- O utilizador B pode fazer quase tudo, mas não consegue registar aplicações ou procurar utilizadores no diretório AZure AD.

Seria de esperar que o utilizador B conseguisse tudo. A razão para esta diferença é que a conta Microsoft é adicionada à subscrição como utilizador convidado em vez de um utilizador membro. Os utilizadores convidados têm diferentes permissões por defeito em Azure AD em comparação com os utilizadores membros. Por exemplo, os utilizadores de membros podem ler outros utilizadores em AZure AD e os utilizadores convidados não podem. Os utilizadores membros podem registar novos principais serviços em Azure AD e os utilizadores convidados não podem.

Se um utilizador convidado precisar de ser capaz de executar estas tarefas, uma solução possível é atribuir as funções específicas de Azure AD que o utilizador convidado necessita. Por exemplo, no cenário anterior, poderia atribuir o papel de [Leitores](../active-directory/roles/permissions-reference.md#directory-readers) de Diretório para ler outros utilizadores e atribuir o papel [de Desenvolvedor de Aplicações](../active-directory/roles/permissions-reference.md#application-developer) para poder criar diretores de serviço. Para obter mais informações sobre os utilizadores de membros e convidados e as suas permissões, consulte [quais são as permissões padrão do utilizador no Azure Ative Directory?](../active-directory/fundamentals/users-default-permissions.md) Para obter mais informações sobre a concessão de acesso aos utilizadores convidados, consulte [as funções De Atribuir Azure a utilizadores convidados externos utilizando o portal Azure](role-assignments-external-users.md).

Note que os [papéis incorporados do Azure](../role-based-access-control/built-in-roles.md) são diferentes dos papéis da [AD Azure](../active-directory/roles/permissions-reference.md). Os papéis incorporados não dão acesso ao Azure AD. Para mais informações, consulte [Compreender os diferentes papéis.](../role-based-access-control/rbac-and-directory-admin-roles.md)

Para obter informações que comparem utilizadores de membros e utilizadores [convidados, consulte quais são as permissões padrão do utilizador no Azure Ative Directory?](../active-directory/fundamentals/users-default-permissions.md)

## <a name="remove-a-co-administrator"></a>Remova um Co-Administrator

1. Inscreva-se no [portal Azure](https://portal.azure.com) como Administrador de Serviço ou Coadministrador.

1. Abra [Subscrições](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) e selecione uma subscrição.

1. Clique em **Controlo de acesso (IAM)**.

1. Clique no separador **Administradores Clássicos.**

1. Adicione uma marca de verificação ao lado da Co-Administrator pretende remover.

1. Clique em **Remover**.

1. Na caixa de mensagens que aparece, clique em **Sim**.

    ![Screenshot que remove coadministrador](./media/classic-administrators/remove-coadmin.png)

## <a name="change-the-service-administrator"></a>Alterar o Administrador de Serviços

Apenas o Administrador de Conta pode alterar o Administrador de Serviço para uma subscrição. Por predefinição, quando se inscreve para uma subscrição do Azure, o Administrador de Serviço é o mesmo que o Administrador de Conta. O utilizador com a função Administrador de Conta não tem acesso ao portal Azure. O utilizador com a função Deduda de Serviço tem acesso total ao portal Azure. Se o Administrador de Conta e o Administrador de Serviço forem o mesmo utilizador e alterar o Administrador de Serviço para um utilizador diferente, então o Administrador de Conta perde acesso ao portal Azure. No entanto, o Administrador de Conta pode sempre utilizar o Centro de Contas para alterar o Administrador de Serviço de volta para si próprio.

Siga estes passos para alterar o Administrador de Serviço no portal Azure.

1. Certifique-se de que o seu cenário é suportado verificando as [limitações para alterar o Administrador de Serviço](#limitations-for-changing-the-service-administrator).

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como Administrador de Conta.

1. Open **Cost Management + Billing** e selecione uma subscrição.

1. Na navegação à esquerda, clique em **Propriedades.**

1. Clique **em Administração de Serviço**.

    ![Screenshot mostrando as propriedades de subscrição no portal Azure](./media/classic-administrators/service-admin.png)

1. Na página **de administração** do serviço Editar, insira o endereço de e-mail para o novo Administrador de Serviço.

    ![Screenshot mostrando a página de administração de serviço de edição](./media/classic-administrators/service-admin-edit.png)

1. Clique em **OK** para guardar a alteração.

### <a name="limitations-for-changing-the-service-administrator"></a>Limitações para alterar o Administrador de Serviço

Só pode haver um Administrador de Serviço por Subscrição Azure. A alteração do Administrador de Serviço comportar-se-á de forma diferente consoante o Administrador de Conta seja uma conta Microsoft ou se se trata de uma conta AD Azure (trabalho ou conta escolar).

| Conta de Administrador de Conta | Pode alterar o Administrador de Serviço para uma conta Microsoft diferente? | Pode alterar o Administrador de Serviço para uma conta AD Azure no mesmo diretório? | Pode alterar o Administrador de Serviço para uma conta AD Azure num diretório diferente? |
| --- | --- | --- | --- |
| Conta Microsoft | Yes | No | No |
| Conta do Azure AD | Yes | Yes | No |

Se o Administrador de Conta for uma conta AD Azure, pode alterar o Administrador de Serviço para uma conta AD Azure no mesmo diretório, mas não num diretório diferente. Por exemplo, abby@contoso.com pode alterar o Administrador de Serviço para , mas não pode alterar o Administrador de Serviço para a bob@contoso.com menos que tenha uma presença no john@notcontoso.com john@notcontoso.com diretório contoso.com.

Para obter mais informações sobre as contas da Microsoft e da AD Azure, veja [o que é o Azure Ative Directory?](../active-directory/fundamentals/active-directory-whatis.md)

## <a name="view-the-account-administrator"></a>Ver o Administrador de Conta

O Administrador de Conta é o utilizador que inicialmente se inscreveu para a subscrição do Azure, e é responsável como o proprietário da faturação da subscrição. Para alterar o Administrador de Conta de uma subscrição, consulte [a propriedade de transferência de uma subscrição do Azure para outra conta](../cost-management-billing/manage/billing-subscription-transfer.md).

Siga estes passos para ver o Administrador de Conta.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Open **Cost Management + Billing** e selecione uma subscrição.

1. Na navegação à esquerda, clique em **Propriedades.**

    O Administrador de Conta da subscrição é apresentado na caixa **de Administração** da Conta.

    ![Screenshot mostrando o administrador de conta](./media/classic-administrators/account-admin.png)

## <a name="next-steps"></a>Passos seguintes

* [Compreender as diferentes funções](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Atribuir funções Azure usando o portal Azure](../role-based-access-control/role-assignments-portal.md)
* [Adicionar ou alterar administradores de subscrição do Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)