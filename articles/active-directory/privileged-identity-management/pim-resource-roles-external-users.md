---
title: Convide convidados e atribua funções de recursos do Azure em PIM-Azure Active Directory | Microsoft Docs
description: Saiba como convidar usuários convidados externos e atribuir funções de recursos do Azure no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3e01b58a2a2fc6f93ae5ccc15e200a0cea69a0c
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804224"
---
# <a name="invite-guest-users-and-assign-azure-resource-roles-in-pim"></a>Convidar usuários convidados e atribuir funções de recursos do Azure no PIM

O Azure Active Directory (Azure AD) Business-to-Business (B2B) é um conjunto de recursos no Azure AD que permite que as organizações colaborem com usuários convidados externos (convidados) e fornecedores usando qualquer conta. Ao combinar B2B com Azure AD Privileged Identity Management (PIM), você pode continuar a aplicar seus requisitos de conformidade e governança aos convidados. Por exemplo, você pode usar esses recursos do PIM para tarefas de identidade do Azure com convidados:

- Atribuir acesso a recursos específicos do Azure
- Habilitar o acesso just-in-time
- Especificar a duração da atribuição e a data de término
- Exigir MFA na atribuição ou ativação ativa
- Executar revisões de acesso
- Utilizar alertas e logs de auditoria

Este artigo descreve como convidar um convidado para sua organização e gerenciar o acesso aos recursos do Azure usando Privileged Identity Management.

## <a name="when-would-you-invite-guests"></a>Quando você convidar convidados?

Aqui estão alguns cenários de exemplo em que você pode convidar convidados para sua organização:

- Permitir que um fornecedor autônomo externo tenha apenas uma conta de email para acessar os recursos do Azure para um projeto.
- Permita que um parceiro externo em uma grande organização que usa o Serviços de Federação do Active Directory (AD FS) local Acesse seu aplicativo de despesas.
- Permita que os engenheiros de suporte que não estejam em sua organização (como o suporte da Microsoft) acessem temporariamente o recurso do Azure para solucionar problemas.

## <a name="how-does-collaboration-using-b2b-guests-work"></a>Como funciona a colaboração usando os convidados B2B?

Ao usar a colaboração B2B, você pode convidar um usuário externo para sua organização como um convidado. O convidado parece estar em sua organização, mas o convidado não tem nenhuma credencial associada a ele. Sempre que um convidado precisa ser autenticado, ele deve ser autenticado em sua organização inicial e não em sua organização. Isso significa que, se o convidado não tiver mais acesso à sua organização inicial, eles também perderão o acesso à sua organização. Por exemplo, se o convidado deixar sua organização, ele perderá automaticamente o acesso a todos os recursos que você compartilhou com eles no Azure AD sem precisar fazer nada. Para obter mais informações sobre o B2B, consulte [o que é o acesso de usuário convidado em Azure Active Directory B2B?](../b2b/what-is-b2b.md).

![Diagrama mostrando como um usuário convidado aparece em seu diretório, mas é autenticado em seu diretório base](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-guest-collaboration-settings"></a>Verificar as configurações de colaboração do convidado

Para garantir que você possa convidar convidados para sua organização, verifique suas configurações de colaboração de convidado.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Clique em **Azure Active Directory** > **configurações de usuário**.

1. Clique em **gerenciar configurações de colaboração externas**.

    ![Página Configurações de colaboração externa mostrando permissão, convite e configurações de restrição de colaboração](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. Verifique se os **Administradores e usuários na função de convite do emissor de convidado podem convidar** o comutador está definido como **Sim**.

## <a name="invite-a-guest-and-assign-a-role"></a>Convidar um convidado e atribuir uma função

Usando o PIM, você pode convidar um convidado e torná-lo qualificado para uma função de recurso do Azure, assim como um usuário membro.

1. Entre no [portal do Azure](https://portal.azure.com/) com um usuário que seja membro da função Administrador de [função privilegiada](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) ou [administrador de usuário](../users-groups-roles/directory-assign-admin-roles.md#user-administrator) .

1. Abra **Azure ad Privileged Identity Management**.

1. Clique em **recursos do Azure**.

1. Use o **filtro de recursos** para filtrar a lista de recursos gerenciados.

1. Clique no recurso que você deseja gerenciar, como um recurso, grupo de recursos, assinatura ou grupo de gerenciamento.

    Você deve definir o escopo para apenas o que o convidado precisa.

1. Em gerenciar, clique em **funções** para ver a lista de funções dos recursos do Azure.

    ![Lista de funções de recursos do Azure mostrando o número de usuários que estão ativos e qualificados](./media/pim-resource-roles-external-users/resources-roles.png)

1. Clique na função mínima que o usuário precisará.

    ![Página de função selecionada listando os membros atuais dessa função](./media/pim-resource-roles-external-users/selected-role.png)

1. Na página função, clique em **Adicionar membro** para abrir o painel nova atribuição.

1. Clique em **selecionar um membro ou grupo**.

    ![Nova atribuição – selecione um painel membro ou grupo listando usuários e grupos junto com uma opção de convite](./media/pim-resource-roles-external-users/select-member-group.png)

1. Para convidar um convidado, clique em **convidar**.

    ![Convide uma página de convidado com caixas para inserir um endereço de email e especificar uma mensagem pessoal](./media/pim-resource-roles-external-users/invite-guest.png)

1. Depois de selecionar um convidado, clique em **convidar**.

    O convidado deve ser adicionado como um membro selecionado.

1. No painel **selecionar um membro ou grupo** , clique em **selecionar**.

1. No painel **configurações de associação** , selecione o tipo de atribuição e a duração.

    ![Nova atribuição – página Configurações de associação com opções para especificar tipo de atribuição, data de início e data de término](./media/pim-resource-roles-external-users/membership-settings.png)

1. Para concluir a atribuição, clique em **concluído** e em **Adicionar**.

    A atribuição de função de convidado aparecerá na sua lista de funções.

    ![Página de função que lista o convidado como qualificado](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-a-guest"></a>Ativar a função como um convidado

Como um usuário externo, primeiro você precisa aceitar o convite para sua organização do Azure AD e possivelmente ativar sua função.

1. Abra o email com seu convite. O email será semelhante ao seguinte.

    ![Convite por email com nome de diretório, mensagem pessoal e um link de introdução](./media/pim-resource-roles-external-users/email-invite.png)

1. Clique no link **introdução** no email.

1. Depois de revisar as permissões, clique em **aceitar**.

    ![Página examinar permissões em um navegador com uma lista de permissões que a organização gostaria de examinar](./media/pim-resource-roles-external-users/invite-accept.png)

1. Você pode ser solicitado a aceitar os termos de uso e especificar se deseja permanecer conectado.

    O portal do Azure é aberto. Se você estiver apenas qualificado para uma função, não terá acesso aos recursos.

1. Para ativar sua função, abra o email com o link ativar função. O email será semelhante ao seguinte.

    ![Mensagem de email do PIM que indica que você está qualificado para uma função com um link de função de ativação](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. Clique em **Ativar função** para abrir suas funções qualificadas no PIM.

    ![Minha página de funções no PIM listando suas funções qualificadas](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. Em ação, clique no link **Ativar** .

    Dependendo das configurações de função, você precisará especificar algumas informações para ativar a função.

1. Depois de especificar as configurações para a função, clique em **Ativar** para ativar a função.

    ![Ativar o escopo e as opções de listagem de páginas para especificar a hora de início, a duração e o motivo](./media/pim-resource-roles-external-users/activate-role.png)

    A menos que o administrador precise aprovar sua solicitação, você deve ter acesso aos recursos especificados.

## <a name="view-activity-for-a-guest"></a>Exibir atividade para um convidado

Assim como um usuário membro, você pode exibir os logs de auditoria para controlar o que os convidados estão fazendo.

1. Como administrador, abra o PIM e selecione o recurso que foi compartilhado.

1. Clique em **auditoria de recurso** para exibir a atividade para esse recurso. Veja a seguir um exemplo da atividade de um grupo de recursos.

    ![Recursos do Azure – página de auditoria de recursos listando a hora, o solicitante e a ação](./media/pim-resource-roles-external-users/audit-resource.png)

1. Para exibir a atividade do convidado, clique em **Azure Active Directory** > **usuários** > nome do convidado.

1. Clique em **logs de auditoria** para ver os logs de auditoria da organização. Se necessário, você pode especificar filtros.

    ![Logs de auditoria de diretório listando data, destino, iniciado por e atividade](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>Passos seguintes

- [Atribuir funções de administrador do Azure AD no PIM](pim-how-to-add-role-to-user.md)
- [O que é acesso de usuário convidado em Azure Active Directory B2B?](../b2b/what-is-b2b.md)
