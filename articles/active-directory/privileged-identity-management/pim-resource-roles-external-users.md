---
title: Atribuir funções de recursos azure aos hóspedes na PIM - Azure AD [ Microsoft Docs
description: Saiba como convidar utilizadores externos e atribuir funções de recursos Azure na Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2efcf77d65fa2f9e203ed805cd7d78b9802ee3aa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74021946"
---
# <a name="invite-guest-users-and-assign-azure-resource-roles-in-privileged-identity-management"></a>Convide os utilizadores convidados e atribua funções de recurso Azure na Gestão de Identidade Privilegiada

Os utilizadores convidados do Azure Ative Directory (Azure AD) fazem parte das capacidades de colaboração business-to-business (B2B) dentro do Azure AD para que possa gerir utilizadores e fornecedores externos como hóspedes em Azure AD. Ao combinar a colaboração B2B com a Azure AD Privileged Identity Management (PIM), pode estender os seus requisitos de conformidade e governação aos hóspedes. Por exemplo, pode utilizar estas funcionalidades de Gestão de Identidade Privilegiada para tarefas de identidade Azure com os hóspedes:

- Atribuir acesso a recursos específicos do Azure
- Ativar acesso just-in-time
- Especificar a duração da atribuição e a data final
- Exigir autenticação de vários fatores na atribuição ativa ou ativação
- Realizar avaliações de acesso
- Utilizar alertas e registos de auditoria

Este artigo descreve como convidar um hóspede para a sua organização e gerir o seu acesso aos recursos do Azure usando a Privileged Identity Management.

## <a name="when-would-you-invite-guests"></a>Quando convidaria convidados?

Aqui estão alguns exemplos de quando você pode convidar convidados para a sua organização:

- Permita a um fornecedor externo independente que tenha apenas uma conta de e-mail para aceder aos seus recursos Azure para um projeto.
- Permita a um parceiro externo numa grande organização que utiliza no local serviços da Federação de Directórioactivo ativo para aceder ao seu pedido de despesas.
- Permitir que os engenheiros de suporte que não estão na sua organização (como suporte da Microsoft) acedam temporariamente ao seu recurso Azure para resolver problemas.

## <a name="how-does-collaboration-using-b2b-guests-work"></a>Como funciona a colaboração com os hóspedes B2B?

Quando utilizar a colaboração B2B, pode convidar um utilizador externo para a sua organização como convidado. O hóspede pode ser gerido como utilizador na sua organização, mas um hóspede tem de ser autenticado na sua organização doméstica e não na sua organização Azure AD. Isto significa que se o hóspede já não tiver acesso à sua organização doméstica, também perde acesso à sua organização. Por exemplo, se o hóspede deixar a sua organização, perde automaticamente o acesso a quaisquer recursos que partilhou com eles em Azure AD sem ter de fazer nada. Para obter mais informações sobre a colaboração b2B, consulte [O que é o acesso dos utilizadores convidados no Azure Ative Directory B2B?](../b2b/what-is-b2b.md)

![Diagrama mostrando como um utilizador convidado é autenticado no seu diretório de casa](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-guest-collaboration-settings"></a>Verifique as definições de colaboração dos hóspedes

Para se certificar de que pode convidar os hóspedes para a sua organização, deve verificar as definições de colaboração dos seus hóspedes.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Selecione > **definições**de utilizador de **diretório ativo Azure**.

1. Selecione **Gerir as definições**de colaboração externa .

    ![Página de definições de colaboração externa mostrando permissão, convite e definições de restrição de colaboração](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. Certifique-se de que os **Administradores e utilizadores na função** de convidado podem convidar a switch está definida para **Sim**.

## <a name="invite-a-guest-and-assign-a-role"></a>Convide um hóspede e atribua um papel

Utilizando a Gestão de Identidade Privilegiada, pode convidar um hóspede e torná-lo elegível para um papel de recurso Azure.

1. Inscreva-se no [portal Azure](https://portal.azure.com/) com um utilizador que seja membro [do](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) papel privilegiado administrador ou administrador de [utilizador.](../users-groups-roles/directory-assign-admin-roles.md#user-administrator)

1. Open **Azure AD Privileged Identity Management.**

1. Selecione **recursos Azure**.

1. Utilize o **filtro Derecurso** para filtrar a lista de recursos geridos.

1. Selecione o recurso que pretende gerir, como um grupo de recursos, recursos, subscrição ou grupo de gestão.

    Deve definir o âmbito apenas para o que o hóspede precisa.

1. Em Manage, selecione **Roles** para ver a lista de funções para os recursos Azure.

    ![Lista de funções de recursos do Azure mostrando número de utilizadores ativos e elegíveis](./media/pim-resource-roles-external-users/resources-roles.png)

1. Selecione a função mínima que o utilizador necessitará.

    ![Página de funções selecionada listando os membros atuais dessa função](./media/pim-resource-roles-external-users/selected-role.png)

1. Na página de funções, selecione **Adicionar membro** para abrir o novo painel de atribuição.

1. Clique **em Selecionar um membro ou grupo**.

    ![Nova atribuição - Selecione um membro ou painel de grupo listando utilizadores e grupos juntamente com uma opção Convidar](./media/pim-resource-roles-external-users/select-member-group.png)

1. Para convidar um hóspede, clique **em Convidar.**

    ![Convide uma página de hóspedes com caixas para inserir um endereço de e-mail e especifique uma mensagem pessoal](./media/pim-resource-roles-external-users/invite-guest.png)

1. Depois de ter selecionado um hóspede, clique em **Convidar**.

    O hóspede deve ser adicionado como membro selecionado.

1. No **Select um painel de membro ou grupo,** clique em **Selecionar**.

1. No painel de **definições de Adesão,** selecione o tipo de atribuição e a duração.

    ![Nova atribuição - Página de definições de membrocom opções para especificar o tipo de atribuição, data de início e data de fim](./media/pim-resource-roles-external-users/membership-settings.png)

1. Para completar a atribuição, selecione **Done** **e,** em seguida, Adicionar .

    A atribuição do papel de convidado aparecerá na sua lista de papéis.

    ![Página de funções listando o hóspede como elegível](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-a-guest"></a>Ativar o papel como convidado

Se é um utilizador externo, deve aceitar o convite para ser hóspede na organização da AD Azure e, possivelmente, ativar a sua atribuição de funções.

1. Abra o e-mail com o seu convite. O e-mail será semelhante ao seguinte.

    ![Convite por e-mail com nome de diretório, mensagem pessoal e um link Get Started](./media/pim-resource-roles-external-users/email-invite.png)

1. Selecione o link **Get Started** no e-mail.

1. Depois de rever as permissões, clique em **Aceitar**.

    ![Reveja a página de permissões num navegador com uma lista de permissões que a organização gostaria que você reveja](./media/pim-resource-roles-external-users/invite-accept.png)

1. Pode ser-lhe pedido que aceite um prazo de utilização e especifique se pretende permanecer inscrito. No portal Azure, se for *elegível* para um papel, ainda não terá acesso aos recursos.

1. Para ativar a sua atribuição de funções, abra o e-mail com o seu link de função ativado. O e-mail será semelhante ao seguinte.

    ![E-mail indicando que você elegível para um papel com um link de função Ativar](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. Selecione **a função Activate** para abrir as suas funções elegíveis na Gestão de Identidade Privilegiada.

    ![A minha página de papéis na Privilegiada Gestão de Identidade seleta as suas funções elegíveis](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. Em Ação, selecione o link **Activate.**

    Dependendo das definições de função, terá de especificar algumas informações para ativar a função.

1. Depois de especificar as definições para a função, clique em **Ativar** a função.

    ![Ativar o âmbito e as opções de listagem de página para especificar a hora de início, duração e razão](./media/pim-resource-roles-external-users/activate-role.png)

    A menos que o administrador seja obrigado a aprovar o seu pedido, deverá ter acesso aos recursos especificados.

## <a name="view-activity-for-a-guest"></a>Ver atividade para um hóspede

Pode ver registos de auditoria para acompanhar o que os hóspedes estão a fazer.

1. Como administrador, abra a Gestão de Identidade Privilegiada e selecione o recurso que foi partilhado.

1. Selecione **a auditoria de Recursos** para visualizar a atividade desse recurso. O seguinte mostra um exemplo da atividade de um grupo de recursos.

    ![Recursos Azure - Página de auditoria de recursos listando o tempo, o pedido e a ação](./media/pim-resource-roles-external-users/audit-resource.png)

1. Para ver a atividade do hóspede, selecione O*nome de hóspede*seletiva dos**Utilizadores** >  **de Diretório** > Ativo do Azure .

1. Selecione **registos de auditoria** para ver os registos de auditoria da organização. Se necessário, pode especificar filtros.

    ![Data de listagem de registos de registos de auditoria de diretório, alvo, iniciado por e atividade](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>Passos seguintes

- [Atribuir funções de administrador da AD Azure na Gestão de Identidade Privilegiada](pim-how-to-add-role-to-user.md)
- [O que é o acesso do utilizador convidado na colaboração Azure AD B2B?](../b2b/what-is-b2b.md)
