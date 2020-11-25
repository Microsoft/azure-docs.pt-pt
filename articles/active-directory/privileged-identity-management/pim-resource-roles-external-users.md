---
title: Atribuir funções de recursos da Azure aos hóspedes da PIM - Azure AD ! Microsoft Docs
description: Saiba como convidar utilizadores convidados externos e atribuir funções de recursos Azure na Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4e2e18f3bb9d1c972d805a60493897d605921e4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010644"
---
# <a name="invite-guest-users-and-assign-azure-resource-roles-in-privileged-identity-management"></a>Convide os utilizadores convidados e atribua funções de recursos da Azure na Gestão de Identidade Privilegiada

Os utilizadores convidados do Azure Ative Directory (Azure AD) fazem parte das capacidades de colaboração business-to-business (B2B) dentro da Azure AD para que possa gerir utilizadores e fornecedores externos como hóspedes em Azure AD. Ao combinar a colaboração B2B com a Azure AD Privileged Identity Management (PIM), pode alargar os seus requisitos de conformidade e governação aos hóspedes. Por exemplo, pode utilizar estas funcionalidades de Gestão de Identidade Privilegiada para tarefas de identidade Azure com os hóspedes:

- Atribuir acesso a recursos específicos do Azure
- Ativar o acesso just-in-time
- Especificar a duração da atribuição e a data de fim
- Requerer a autenticação de vários fatores na atribuição ativa ou ativação
- Realizar comentários de acesso
- Utilizar alertas e registos de auditoria

Este artigo descreve como convidar um hóspede para a sua organização e gerir o seu acesso aos recursos da Azure usando a Gestão de Identidade Privilegiada.

## <a name="when-would-you-invite-guests"></a>Quando convidaria convidados?

Aqui estão alguns exemplos de quando você pode convidar convidados para a sua organização:

- Permitir que um fornecedor independente externo que tenha apenas uma conta de e-mail aceda aos seus recursos Azure para um projeto.
- Permitir que um parceiro externo numa grande organização que utilize os Serviços da Federação De Diretórios Ativos no local aceda à sua aplicação de despesas.
- Permita que os engenheiros de suporte que não estão na sua organização (como o suporte da Microsoft) acedam temporariamente ao seu recurso Azure para resolver problemas.

## <a name="how-does-collaboration-using-b2b-guests-work"></a>Como funciona a colaboração com os hóspedes B2B?

Quando utilizar a colaboração B2B, pode convidar um utilizador externo para a sua organização como convidado. O hóspede pode ser gerido como um utilizador na sua organização, mas um hóspede tem de ser autenticado na sua organização doméstica e não na sua organização Azure AD. Isto significa que se o hóspede já não tiver acesso à sua organização doméstica, também perde acesso à sua organização. Por exemplo, se o hóspede deixar a sua organização, eles automaticamente perdem o acesso a quaisquer recursos que partilhou com eles em Azure AD sem que você tenha que fazer nada. Para obter mais informações sobre a colaboração B2B, consulte [o que é o acesso do utilizador convidado no Azure Ative Directory B2B?](../external-identities/what-is-b2b.md)

![Diagrama mostrando como um utilizador convidado é autenticado no seu diretório de casa](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-guest-collaboration-settings"></a>Verifique as definições de colaboração dos hóspedes

Para se certificar de que pode convidar os hóspedes para a sua organização, deverá verificar as definições de colaboração dos seus hóspedes.

1. Inscreva-se no [portal Azure](https://portal.azure.com/).

1. Selecione as definições do Utilizador **do Diretório Ativo Azure**  >  **User settings**.

1. **Selecione Gerir as definições de colaboração externa**.

    ![Página de definições de colaboração externa mostrando definições de permissão, convite e restrição de colaboração](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. Certifique-se de que os **Administradores e utilizadores na função de convidado convidado podem convidar** a switch está definido para **Sim**.

## <a name="invite-a-guest-and-assign-a-role"></a>Convide um convidado e atribua um papel

Utilizando a Gestão de Identidade Privilegiada, pode convidar um hóspede e torná-lo elegível para um papel de recurso Azure.

1. Inscreva-se no [portal Azure](https://portal.azure.com/) com um utilizador que seja membro da função de Administrador de [Função Privilegiada](../roles/permissions-reference.md#privileged-role-administrator) ou [administrador de utilizador.](../roles/permissions-reference.md#user-administrator)

1. Open **Azure AD Gestão de Identidade Privilegiada.**

1. Selecione **recursos Azure**.

1. Utilize o **filtro De recurso** para filtrar a lista de recursos geridos.

1. Selecione o recurso que pretende gerir, como um grupo de recursos, grupo de recursos, subscrição ou grupo de gestão.

    Deve definir o âmbito apenas para o que o hóspede precisa.

1. Em Gestão, selecione **Roles** para ver a lista de papéis para recursos Azure.

    ![Lista de funções de recursos Azure mostrando o número de utilizadores ativos e elegíveis](./media/pim-resource-roles-external-users/resources-roles.png)

1. Selecione a função mínima de que o utilizador necessitará.

    ![Página de função selecionada listando os atuais membros dessa função](./media/pim-resource-roles-external-users/selected-role.png)

1. Na página de funções, **selecione Adicionar membro** para abrir o painel de atribuição Nova.

1. Clique **em Selecionar um membro ou grupo.**

    ![Nova atribuição - Selecione um painel de membros ou grupos listando utilizadores e grupos juntamente com uma opção de Convite](./media/pim-resource-roles-external-users/select-member-group.png)

1. Para convidar um convidado, clique em **Convidar.**

    ![Convide uma página de hóspedes com caixas para inserir um endereço de e-mail e especifique uma mensagem pessoal](./media/pim-resource-roles-external-users/invite-guest.png)

1. Depois de ter selecionado um convidado, clique em **Convidar**.

    O hóspede deve ser adicionado como membro selecionado.

1. No **painel Select a member or group,** clique em **Select**.

1. No painel **de definições de adesão,** selecione o tipo de atribuição e a duração.

    ![Nova atribuição - Página de definições de adesão com opções para especificar o tipo de atribuição, data de início e data de fim](./media/pim-resource-roles-external-users/membership-settings.png)

1. Para completar a atribuição, selecione **'Fazer'** e, em seguida, **Adicione**.

    A atribuição de funções de convidado aparecerá na sua lista de papéis.

    ![Página de função listando o hóspede como elegível](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-a-guest"></a>Ativar o papel como convidado

Se você é um utilizador externo, você deve aceitar o convite para ser um hóspede na organização AZure AD e possivelmente ativar a sua atribuição de papel.

1. Abra o e-mail com o seu convite. O e-mail será semelhante ao seguinte.

    ![E-mail convidar com nome de diretório, mensagem pessoal e um link Get Started](./media/pim-resource-roles-external-users/email-invite.png)

1. Selecione o link **Get Started** no e-mail.

1. Depois de rever as permissões, clique em **Aceitar**.

    ![Reveja a página de permissões num browser com uma lista de permissões que a organização gostaria que você revisse](./media/pim-resource-roles-external-users/invite-accept.png)

1. Pode ser-lhe pedido que aceite um termos de uso e especifique se pretende manter-se inscrito. No portal Azure, se for *elegível* para um papel, ainda não terá acesso aos recursos.

1. Para ativar a sua atribuição de funções, abra o e-mail com o seu link de função ativa. O e-mail será semelhante ao seguinte.

    ![E-mail indicando que você elegível para um papel com um link de função Ativa](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. Selecione **Ativer função** para abrir as suas funções elegíveis em Gestão de Identidade Privilegiada.

    ![A minha página de funções em Gestão de Identidade Privilegiada listando as suas funções elegíveis](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. Em Ação, selecione o link **Activate.**

    Dependendo das definições de função, terá de especificar algumas informações para ativar a função.

1. Uma vez especificadas as definições para a função, clique em **Ativar** para ativar a função.

    ![Ativar o âmbito de listagem da página e as opções para especificar a hora de início, duração e razão](./media/pim-resource-roles-external-users/activate-role.png)

    A menos que o administrador seja obrigado a aprovar o seu pedido, deverá ter acesso aos recursos especificados.

## <a name="view-activity-for-a-guest"></a>Ver atividade para um hóspede

Pode ver os registos de auditoria para acompanhar o que os hóspedes estão a fazer.

1. Como administrador, abra a Gestão de Identidade Privilegiada e selecione o recurso que foi partilhado.

1. Selecione **a auditoria de recursos** para visualizar a atividade para esse recurso. O que se segue mostra um exemplo da atividade de um grupo de recursos.

    ![Recursos Azure - Página de auditoria de recursos que enumera o tempo, o solicitador e a ação](./media/pim-resource-roles-external-users/audit-resource.png)

1. Para ver a atividade para o hóspede, selecione o nome de convidado dos Utilizadores **do Diretório Ativo Azure**  >  **Users**  >  *guest name*.

1. Selecione **registos de auditoria** para ver os registos de auditoria para a organização. Se necessário, pode especificar filtros.

    ![Data de listagem de registos de auditoria de diretório, alvo, iniciado por, e atividade](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>Passos seguintes

- [Atribuir funções de administrador da Azure AD em Gestão de Identidade Privilegiada](pim-how-to-add-role-to-user.md)
- [O que é o acesso do utilizador convidado na colaboração Azure AD B2B?](../external-identities/what-is-b2b.md)