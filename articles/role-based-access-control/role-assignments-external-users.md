---
title: Adicionar ou remover atribuições de funções para utilizadores externos com RBAC e o portal Azure
description: Saiba como conceder acesso aos recursos do Azure para utilizadores externos a uma organização que utilize o controlo de acesso baseado em funções do Azure (RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: skwan
ms.custom: it-pro
ms.openlocfilehash: 1c440b85f792ac5bb1336f4d20f930aafc38ad7d
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77138384"
---
# <a name="add-or-remove-role-assignments-for-external-guest-users-using-azure-rbac-and-the-azure-portal"></a>Adicione ou remova atribuições de funções para utilizadores externos usando o Azure RBAC e o portal Azure

O [controlo de acesso baseado em funções azure (RBAC)](overview.md) permite uma melhor gestão de segurança para as grandes organizações e para pequenas e médias empresas que trabalham com colaboradores externos, fornecedores ou freelancers que precisam de acesso a recursos específicos no seu ambiente, mas não necessariamente a toda a infraestrutura ou quaisquer âmbitos relacionados com a faturação. Pode utilizar as capacidades do [Azure Ative Directory B2B](../active-directory/b2b/what-is-b2b.md) para colaborar com utilizadores externos e pode utilizar o RBAC para conceder apenas as permissões de que os utilizadores de hóspedes precisam no seu ambiente.

## <a name="prerequisites"></a>Pré-requisitos

Para adicionar ou remover atribuições de função, você deve ter:

- `Microsoft.Authorization/roleAssignments/write` e `Microsoft.Authorization/roleAssignments/delete` permissões, tais como [Administrador de Acesso ao Utilizador](built-in-roles.md#user-access-administrator) ou [Proprietário](built-in-roles.md#owner)

## <a name="when-would-you-invite-guest-users"></a>Quando convidaria os utilizadores convidados?

Aqui estão alguns cenários de exemplo quando você pode convidar utilizadores convidados para a sua organização e conceder permissões:

- Permita a um fornecedor externo independente que tenha apenas uma conta de e-mail para aceder aos seus recursos Azure para um projeto.
- Permitir que um parceiro externo gere certos recursos ou uma subscrição inteira.
- Permitir que os engenheiros de suporte que não estão na sua organização (como suporte da Microsoft) acedam temporariamente ao seu recurso Azure para resolver problemas.

## <a name="permission-differences-between-member-users-and-guest-users"></a>Diferenças de permissões entre utilizadores membros e utilizadores convidados

Os membros nativos de um diretório (utilizadores membros) têm permissões diferentes das dos utilizadores convidados de outro diretório como convidados de colaboração B2B (utilizadores convidados). Por exemplo, o utilizador dos membros pode ler quase todas as informações de diretório, enquanto os utilizadores convidados têm permissões de diretório restritas. Para obter mais informações sobre utilizadores membros e utilizadores convidados, consulte [quais são as permissões padrão dos utilizadores no Diretório Ativo do Azure?](../active-directory/fundamentals/users-default-permissions.md)

## <a name="add-a-guest-user-to-your-directory"></a>Adicionar um utilizador convidado ao seu diretório

Siga estes passos para adicionar um utilizador convidado ao seu diretório utilizando a página do Diretório Ativo Azure.

1. Certifique-se de que as definições de colaboração externa da sua organização estão configuradas de modo a que possa convidar os hóspedes. Para mais informações, consulte a [colaboração externa Enable B2B e gerequem quem pode convidar os hóspedes.](../active-directory/b2b/delegate-invitations.md)

1. No portal Azure, clique em **Azure Ative Directory** > **Utilizadores** > **Novo utilizador convidado.**

    ![Nova funcionalidade de utilizador convidado no portal Azure](./media/role-assignments-external-users/invite-guest-user.png)

1. Siga os passos para adicionar um novo utilizador convidado. Para mais informações, consulte os utilizadores de colaboração Add [Azure Ative Directory B2B no portal Azure](../active-directory/b2b/add-users-administrator.md#add-guest-users-to-the-directory).

Depois de adicionar um utilizador convidado ao diretório, pode enviar ao utilizador convidado um link direto para uma aplicação partilhada, ou o utilizador convidado pode clicar no URL de resgate no e-mail de convite.

![E-mail de convite do utilizador convidado](./media/role-assignments-external-users/invite-email.png)

Para que o utilizador convidado possa aceder ao seu diretório, deve completar o processo de convite.

![O utilizador convidado convida permissões de revisão](./media/role-assignments-external-users/invite-review-permissions.png)

Para mais informações sobre o processo de convite, consulte o convite de [colaboração Azure Ative B2B.](../active-directory/b2b/redemption-experience.md)

## <a name="add-a-role-assignment-for-a-guest-user"></a>Adicione uma atribuição de funções para um utilizador convidado

No RBAC, para conceder acesso, atribui-se um papel. Para adicionar uma atribuição de funções para um utilizador convidado, siga [os mesmos passos](role-assignments-portal.md#add-a-role-assignment) que faria para um utilizador, grupo, diretor de serviço ou identidade gerida. Siga estes passos adicione uma atribuição de funções para um utilizador convidado em diferentes âmbitos.

1. No portal do Azure, clique em **All services** (Todos os serviços).

1.  Selecione o conjunto de recursos a que o acesso se aplica, também conhecido como âmbito. Por exemplo, pode selecionar grupos de **Gestão**, **Subscrições, Grupos** **de Recursos**ou um recurso.

1. Clique no recurso específico.

1. Clique em **Controlo de acesso (IAM)** .

    A imagem que se segue mostra um exemplo da lâmina de controlo de acesso (IAM) para um grupo de recursos. Se fizer alguma alteração no controlo de acesso aqui, aplicar-se-ão apenas ao grupo de recursos.

    ![Lâmina de controlo de acesso (IAM) para um grupo de recursos](./media/role-assignments-external-users/access-control-resource-group.png)

1. Clique no separador de **atribuições de Role** para ver todas as atribuições de funções neste âmbito.

1. Clique em **Adicionar** > **Adicionar atribuição de funções** para abrir o painel de atribuição de funções Adicionar.

    Se não tiver permissão para atribuir funções, a opção de atribuição de funções Add será desativada.

    ![Adicionar menu](./media/role-assignments-external-users/add-menu.png)

1. Na lista pendente **Função**, selecione uma função, como **Contribuidor de Máquina Virtual**.

1. Na lista **Select,** selecione o utilizador convidado. Se não vir o utilizador na lista, pode escrever na caixa **Select** para pesquisar o diretório para identificar nomes de exibição, endereços de e-mail e identificadores de objetos.

   ![Adicionar painel de atribuição de papéis](./media/role-assignments-external-users/add-role-assignment.png)

1. Clique em **Guardar** para atribuir a função no âmbito selecionado.

    ![Atribuição de funções para Colaborador de Máquina Virtual](./media/role-assignments-external-users/access-control-role-assignments.png)

## <a name="add-a-role-assignment-for-a-guest-user-not-yet-in-your-directory"></a>Adicione uma atribuição de funções para um utilizador convidado ainda não no seu diretório

Para adicionar uma atribuição de funções para um utilizador convidado, siga [os mesmos passos](role-assignments-portal.md#add-a-role-assignment) que faria para um utilizador, grupo, diretor de serviço ou identidade gerida.

Se o utilizador convidado ainda não estiver no seu diretório, pode convidar o utilizador diretamente do painel de atribuição de funções Add.

1. No portal do Azure, clique em **All services** (Todos os serviços).

1.  Selecione o conjunto de recursos a que o acesso se aplica, também conhecido como âmbito. Por exemplo, pode selecionar grupos de **Gestão**, **Subscrições, Grupos** **de Recursos**ou um recurso.

1. Clique no recurso específico.

1. Clique em **Controlo de acesso (IAM)** .

1. Clique no separador de **atribuições de Role** para ver todas as atribuições de funções neste âmbito.

1. Clique em **Adicionar** > **Adicionar atribuição de funções** para abrir o painel de atribuição de funções Adicionar.

    ![Adicionar menu](./media/role-assignments-external-users/add-menu.png)

1. Na lista pendente **Função**, selecione uma função, como **Contribuidor de Máquina Virtual**.

1. Na lista **Select,** digite o endereço de e-mail da pessoa que pretende convidar e selecione essa pessoa.

   ![Convide o utilizador convidado em Adicionar painel de atribuição de papéis](./media/role-assignments-external-users/add-role-assignment-new-guest.png)

1. Clique em **Guardar** para adicionar o utilizador convidado ao seu diretório, atribuir o papel e enviar um convite.

    Após alguns momentos, você verá uma notificação da atribuição de papel e informações sobre o convite.

    ![Atribuição de funções e notificação de utilizador convidada](./media/role-assignments-external-users/invited-user-notification.png)

1. Para convidar manualmente o utilizador convidado, clique à direita e copie o link de convite na notificação. Não clique no link de convite porque inicia o processo de convite.

    O link de convite terá o seguinte formato:

    `https://invitations.microsoft.com/redeem/...`

1. Envie o link de convite ao utilizador convidado para completar o processo de convite.

    Para mais informações sobre o processo de convite, consulte o convite de [colaboração Azure Ative B2B.](../active-directory/b2b/redemption-experience.md)

## <a name="remove-a-guest-user-from-your-directory"></a>Remova um utilizador convidado do seu diretório

Antes de remover um utilizador convidado de um diretório, deve primeiro remover quaisquer atribuições de funções para esse utilizador convidado. Siga estes passos para remover um utilizador convidado de um diretório.

1. Controlo de Acesso Aberto **(IAM)** num âmbito, como grupo de gestão, subscrição, grupo de recursos ou recurso, onde o utilizador convidado tem uma atribuição de funções.

1. Clique no separador de **atribuições de Role** para ver todas as atribuições de funções.

1. Na lista de atribuições de funções, adicione uma marca de verificação ao lado do utilizador convidado com a atribuição de funções que pretende remover.

   ![Remover a atribuição de funções](./media/role-assignments-external-users/remove-role-assignment-select.png)

1. Clique em **Remover**.

   ![Mensagem Remover atribuição de função](./media/role-assignments-external-users/remove-role-assignment.png)

1. Na mensagem de atribuição de funções de remoção que aparece, clique **em Sim**.

1. Na barra de navegação à esquerda, clique em **Azure Ative Directory** > **Utilizadores.**

1. Clique no utilizador convidado que pretende remover.

1. Clique em **Eliminar**.

   ![Excluir o utilizador convidado](./media/role-assignments-external-users/delete-guest-user.png)

1. Na mensagem de exclusão que aparece, clique em **Sim**.

## <a name="troubleshoot"></a>Resolução de problemas

### <a name="guest-user-cannot-browse-the-directory"></a>O utilizador convidado não pode navegar no diretório

Os utilizadores convidados têm permissões restritas de diretório. Por exemplo, os utilizadores de hóspedes não podem navegar no diretório e não podem procurar grupos ou aplicações. Para mais informações, consulte [Quais são as permissões padrão dos utilizadores no Diretório Ativo do Azure?](../active-directory/fundamentals/users-default-permissions.md)

![O utilizador convidado não pode navegar nos utilizadores num diretório](./media/role-assignments-external-users/directory-no-users.png)

Se um utilizador convidado precisar de privilégios adicionais no diretório, pode atribuir uma função de diretório ao utilizador convidado. Se realmente quer que um utilizador convidado tenha acesso completo ao seu diretório, pode adicionar o utilizador convidado ao papel de Leitores de [Diretório](../active-directory/users-groups-roles/directory-assign-admin-roles.md) no Azure AD. Para mais informações, consulte [as permissões do Grant aos utilizadores de organizações parceiras do seu inquilino de Diretório Ativo Azure.](../active-directory/b2b/add-guest-to-role.md)

![Atribuir papel de Leitores de Diretório](./media/role-assignments-external-users/directory-roles.png)

### <a name="guest-user-cannot-browse-users-groups-or-service-principals-to-assign-roles"></a>O utilizador hóspede não pode navegar em utilizadores, grupos ou diretores de serviço para atribuir funções

Os utilizadores convidados têm permissões restritas de diretório. Mesmo que um utilizador convidado seja [proprietário](built-in-roles.md#owner) de âmbito, se tentar adicionar uma atribuição de funções para conceder acesso a outra pessoa, não pode navegar na lista de utilizadores, grupos ou diretores de serviço.

![O utilizador convidado não pode navegar nos principais de segurança para atribuir funções](./media/role-assignments-external-users/directory-no-browse.png)

Se o utilizador convidado souber o nome exato de sessão de sessão no diretório, pode conceder acesso. Se realmente quer que um utilizador convidado tenha acesso completo ao seu diretório, pode adicionar o utilizador convidado ao papel de Leitores de [Diretório](../active-directory/users-groups-roles/directory-assign-admin-roles.md) no Azure AD. Para mais informações, consulte [as permissões do Grant aos utilizadores de organizações parceiras do seu inquilino de Diretório Ativo Azure.](../active-directory/b2b/add-guest-to-role.md)

### <a name="guest-user-cannot-register-applications-or-create-service-principals"></a>O utilizador hóspede não pode registar aplicações ou criar diretores de serviço

Os utilizadores convidados têm permissões restritas de diretório. Se um utilizador convidado precisar de ser capaz de registar aplicações ou criar diretores de serviço, pode adicionar o utilizador convidado à função de Desenvolvedor de [Aplicações](../active-directory/users-groups-roles/directory-assign-admin-roles.md) em Azure AD. Para mais informações, consulte [as permissões do Grant aos utilizadores de organizações parceiras do seu inquilino de Diretório Ativo Azure.](../active-directory/b2b/add-guest-to-role.md)

![O utilizador hóspede não pode registar aplicações](./media/role-assignments-external-users/directory-access-denied.png)

### <a name="guest-user-does-not-see-the-new-directory"></a>O utilizador convidado não vê o novo diretório

Se um utilizador convidado tiver acesso a um diretório, mas não vê o novo diretório listado no portal Azure quando tentar mudar no seu painel de **assinatura Sório +,** certifique-se de que o utilizador convidado completou o processo de convite. Para mais informações sobre o processo de convite, consulte o convite de [colaboração Azure Ative B2B.](../active-directory/b2b/redemption-experience.md)

### <a name="guest-user-does-not-see-resources"></a>O utilizador hóspede não vê recursos

Se um utilizador convidado tiver acesso a um diretório, mas não vir os recursos a que foi concedido acesso no portal Azure, certifique-se de que o utilizador convidado selecionou o diretório correto. Um utilizador convidado pode ter acesso a vários diretórios. Para mudar de diretórios, na parte superior esquerda, clique em **Diretório + subscrição,** e, em seguida, clique no diretório apropriado.

![Diretórios + Painel de assinaturas no portal Azure](./media/role-assignments-external-users/directory-subscription.png)

## <a name="next-steps"></a>Passos seguintes

- [Adicione utilizadores de colaboração azure Ative Directory B2B no portal Azure](../active-directory/b2b/add-users-administrator.md)
- [Propriedades de um utilizador de colaboração Azure Ative Directory B2B](../active-directory/b2b/user-properties.md)
- [Os elementos do convite de colaboração B2B - Azure Ative Directory](../active-directory/b2b/invitation-email-elements.md)
- [Adicione um utilizador convidado como Coadministrador](classic-administrators.md#add-a-guest-user-as-a-co-administrator)