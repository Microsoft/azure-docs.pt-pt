---
title: Convidar convidados externos e atribuir funções de administrador do Azure AD - Privileged Identity Management | Documentos da Microsoft
description: Saiba como convidar utilizadores e atribuir funções de administrador do Azure Active Directory no Azure AD Privileged Identity Management.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 03/13/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 68e76a4513d94cceb8e856c94ad6eae2bdab9c46
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57853261"
---
# <a name="invite-guest-users-and-assign-azure-resource-access-in-pim"></a>Convidar utilizadores e atribuir acesso a recursos do Azure no PIM

O Azure Active Directory (Azure AD) empresa-empresa (B2B) é um conjunto de recursos no Azure AD que permite que as organizações colaboram com utilizadores convidados externos (convidados) e fornecedores que utilizam qualquer conta. Quando combina B2B com o Azure AD Privileged Identity Management (PIM), pode continuar a aplicar seus requisitos de governação e conformidade aos convidados. Por exemplo, pode utilizar estas funcionalidades PIM para tarefas de identidade do Azure com convidados:

- Atribuir acesso a recursos específicos do Azure
- Ativar o acesso just-in-time
- Especifique a data de duração e de fim da atribuição
- Exigir a MFA a ativação ou atribuição ativa
- Executar revisões de acesso
- Utilizar alertas e registos de auditoria

Este artigo descreve como convidar um convidado para a sua organização e gerir o acesso aos recursos do Azure com o Privileged Identity Management.

## <a name="when-would-you-invite-guests"></a>Quando convidar convidados?

Seguem-se alguns cenários de exemplo quando podem convidar convidados para a sua organização:

- Permitir que um fornecedor externo Self-empregado que tem apenas uma conta de e-mail para aceder aos recursos do Azure para um projeto.
- Permitir que um parceiro externo numa grande organização que utiliza serviços de Federação do Active Directory no local para aceder à sua aplicação de despesas.
- Permitir engenheiros de suporte não na sua organização (por exemplo, o suporte da Microsoft) para aceder temporariamente o seu recurso do Azure para resolver problemas.

## <a name="how-does-collaboration-using-b2b-guests-work"></a>Como funciona a colaboração com o B2B os convidados trabalho?

Quando utilizar a colaboração B2B, pode convidar um utilizador externo à sua organização como convidado. O convidado parece estar na sua organização, mas o convidado não tem quaisquer credenciais associados a ele. Sempre que um convidado tem de ser autenticado, precisa ser autenticados na sua organização da página principal e não na sua organização. Isso significa que se o convidado já não tiver acesso à sua organização da página principal, eles também perderem o acesso à sua organização. Por exemplo, se o convidado deixa a sua organização, eles automaticamente perderem o acesso a quaisquer recursos partilhados com os mesmos no Azure AD sem ter de fazer nada. Para obter mais informações sobre B2B, consulte [o que é o acesso do utilizador convidado no Azure Active Directory B2B?](../b2b/what-is-b2b.md).

![B2B e convidado](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-guest-collaboration-settings"></a>Verifique as definições de colaboração de convidado

Para se certificar de que o podem convidar convidados em sua organização, deve verificar as definições de colaboração de convidado.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Clique em **do Azure Active Directory** > **definições de utilizador**.

1. Clique em **gerir definições de colaboração externa**.

    ![Definições de colaboração externa](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. Certifique-se de que o **administradores e utilizadores na função de autor podem convidar** parâmetro estiver definido como **Sim**.

## <a name="invite-a-guest-and-assign-a-role"></a>Convidar um convidado e atribuir uma função

Utilizar o PIM, pode convidar um convidado e torná-los elegível para uma função de recursos do Azure como um utilizador de membro.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) com um utilizador que seja um membro a [administrador com função privilegiada](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) ou [utilizador administrador](../users-groups-roles/directory-assign-admin-roles.md#user-administrator) função.

1. Open **do Azure AD Privileged Identity Management**.

1. Clique em **recursos do Azure**.

1. Utilize o **filtro de recursos** para filtrar a lista de recursos geridos.

1. Clique em recursos que pretende gerir, tal como um recurso, grupo de recursos, subscrição ou grupo de gestão.

    Deve definir o âmbito para precisa apenas de que o convidado.

1. Em gerir, clique em **funções** para ver a lista de funções para recursos do Azure.

    ![Funções de recursos do Azure](./media/pim-resource-roles-external-users/resources-roles.png)

1. Clique na função mínima que o usuário precisará.

    ![Função selecionada](./media/pim-resource-roles-external-users/selected-role.png)

1. Na página de função, clique em **Add member** para abrir o painel de atribuição de novo.

1. Clique em **selecione um membro ou um grupo**.

    ![Selecionar um membro ou grupo](./media/pim-resource-roles-external-users/select-member-group.png)

1. Para convidar um convidado, clique em **convidar**.

    ![Fazer um convite](./media/pim-resource-roles-external-users/invite-guest.png)

1. Depois de selecionar um convidado, clique em **convidar**.

    O convidado deve ser adicionado como um membro selecionado.

1. Na **selecione um membro ou grupo** painel, clique em **selecione**.

1. Na **definições de associação** painel, selecione o tipo de atribuição e a duração.

    ![Definições de associação](./media/pim-resource-roles-external-users/membership-settings.png)

1. Para concluir a atribuição, clique em **feito** e, em seguida **Add**.

    A atribuição de função de convidado irá aparecer na sua lista de função.

    ![Atribuição de função para o convidado](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-a-guest"></a>Ativar função como convidado

Como um utilizador externo, primeiro tem de aceitar o convite para a sua organização do Azure AD e, possivelmente, ativar a sua função.

1. Abra a mensagem de e-mail com o seu convite. O e-mail terá um aspeto semelhante ao seguinte.

    ![Convite por e-mail](./media/pim-resource-roles-external-users/email-invite.png)

1. Clique nas **começar** ligação no e-mail.

1. Depois de rever as permissões, clique em **Accept**.

    ![Analisar permissões](./media/pim-resource-roles-external-users/invite-accept.png)

1. Poderá ser-lhe pedido para aceitar os termos de utilização e especificar se pretende manter sessão iniciada.

    É aberto o portal do Azure. Se estiver apenas qualificado para uma função, não terá acesso aos recursos.

1. Para ativar sua função, abra o e-mail com a ativar a ligação de função. O e-mail terá um aspeto semelhante ao seguinte.

    ![Convite por e-mail](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. Clique em **ativar função** para abrir as funções elegíveis no PIM.

    ![As minhas funções - elegíveis](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. Clique em ação, o **Activate** ligação.

    Consoante as definições de função, precisará especificar algumas informações para ativar a função.

1. Depois de especificar as definições para a função, clique em **Activate** para ativar a função.

    ![Ativar função](./media/pim-resource-roles-external-users/activate-role.png)

    A menos que o administrador é necessária para aprovar o pedido, deve ter acesso aos recursos especificados.

## <a name="view-activity-for-a-guest"></a>Ver a atividade para um convidado

Tal como um utilizador de membro, pode ver os registos de auditoria para controlar o que os convidados estão a fazer.

1. Como administrador, abra o PIM e selecione o recurso que foi partilhado.

1. Clique em **auditoria de recurso** para ver a atividade para esse recurso. O código a seguir mostra um exemplo da atividade para um grupo de recursos.

    ![Auditoria de recurso](./media/pim-resource-roles-external-users/audit-resource.png)

1. Para ver a atividade para o convidado, clique em **do Azure Active Directory** > **utilizadores** > nome do convidado.

1. Clique em **registos de auditoria** para ver os registos de auditoria para a organização. Se necessário, pode especificar filtros.

    ![auditoria de organização](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>Passos Seguintes

- [Atribuir funções de administrador do Azure AD no PIM](pim-how-to-add-role-to-user.md)
- [O que é o acesso do utilizador convidado no Azure Active Directory B2B?](../b2b/what-is-b2b.md)
