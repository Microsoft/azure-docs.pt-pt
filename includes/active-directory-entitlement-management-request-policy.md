---
title: incluir ficheiro
description: incluir ficheiro
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 10/15/2019
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: 78a0dafeedc9aac4db69903b9f1193574cbd39c7
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72934574"
---
## <a name="for-users-in-your-directory"></a>Para usuários em seu diretório

Siga estas etapas se desejar permitir que os usuários em seu diretório possam solicitar esse pacote de acesso. Ao definir a política de solicitação, você pode especificar usuários individuais ou grupos de usuários mais comuns. Por exemplo, sua organização pode já ter um grupo como **todos os funcionários**.  Se esse grupo for adicionado na política para usuários que podem solicitar acesso, então qualquer membro desse grupo poderá solicitar acesso.

1. Na seção **usuários que podem solicitar acesso** , clique em **para usuários em seu diretório**.

    Quando você seleciona essa opção, novas opções aparecem para refinar ainda mais quem em seu diretório pode solicitar esse pacote de acesso.

    ![Pacotes de acesso-solicitações-para usuários em seu diretório](./media/active-directory-entitlement-management-request-policy/for-users-in-your-directory.png)

1. Selecione uma das seguintes opções:

    |  |  |
    | --- | --- |
    | **Usuários e grupos específicos** | Escolha esta opção se desejar que apenas os usuários e grupos em seu diretório especificados possam solicitar esse pacote de acesso. |
    | **Todos os membros (exceto convidados)** | Escolha esta opção se desejar que todos os usuários Membros em seu diretório possam solicitar esse pacote de acesso. Essa opção não inclui nenhum usuário convidado que você possa ter convidado em seu diretório. |
    | **Todos os usuários (incluindo convidados)** | Escolha esta opção se desejar que todos os usuários Membros e usuários convidados em seu diretório possam solicitar esse pacote de acesso. |

    Os usuários convidados se referem a usuários externos que foram convidados em seu diretório com o [Azure ad B2B](../articles/active-directory/b2b/what-is-b2b.md). Para obter informações sobre as diferenças entre usuários Membros e usuários convidados, consulte [quais são as permissões de usuário padrão no Azure Active Directory?](../articles/active-directory/fundamentals/users-default-permissions.md).

1. Se você selecionou **usuários e grupos específicos**, clique em **Adicionar usuários e grupos**.

1. No painel Selecionar usuários e grupos, selecione os usuários e grupos que você deseja adicionar.

    ![Pacotes de acesso-solicitações-Selecionar usuários e grupos](./media/active-directory-entitlement-management-request-policy/select-users-groups.png)

1. Clique em **selecionar** para adicionar os usuários e grupos.

1. Pule para a seção [aprovação](#approval) .

## <a name="for-users-not-in-your-directory"></a>Para usuários que não estão em seu diretório

Siga estas etapas se desejar permitir que os usuários que não estão em seu diretório possam solicitar esse pacote de acesso. Os **usuários que não estão em seu diretório** referem-se aos usuários que estão em outro diretório ou domínio do Azure AD e podem ainda não ter sido convidados em seu diretório. Os diretórios do AD do Azure devem ser configurados para permitir convites em **restrições de colaboração**. Para obter mais informações, consulte [habilitar colaboração externa B2B e gerenciar quem pode convidar convidados](../articles/active-directory/b2b/delegate-invitations.md).

> [!NOTE]
> Uma conta de usuário convidado será criada para um usuário que ainda não está em seu diretório cuja solicitação foi aprovada ou aprovada automaticamente. O convidado será convidado, mas não receberá um email de convite. Em vez disso, eles receberão um email quando sua atribuição de pacote de acesso for entregue. Por padrão, mais tarde, quando o usuário convidado não tiver mais nenhuma atribuição de pacote de acesso, porque sua última atribuição expirou ou foi cancelada, essa conta de usuário convidado será bloqueada de entrar e excluída subsequentemente. Se você quiser que os usuários convidados permaneçam em seu diretório indefinidamente, mesmo que eles não tenham atribuições de pacote de acesso, você poderá alterar as configurações de sua configuração de gerenciamento de direitos. Para obter mais informações sobre o objeto de usuário convidado, consulte [Propriedades de um usuário de colaboração Azure Active Directory B2B](../articles/active-directory/b2b/user-properties.md).

1. Na seção **usuários que podem solicitar acesso** , clique em **para usuários que não estão em seu diretório**.

    Quando você seleciona essa opção, novas opções são exibidas.

    ![Pacotes de acesso-solicitações-para usuários que não estão em seu diretório](./media/active-directory-entitlement-management-request-policy/for-users-not-in-your-directory.png)

1. Selecione uma das seguintes opções:

    |  |  |
    | --- | --- |
    | **Organizações conectadas específicas** | Escolha esta opção se desejar selecionar em uma lista de organizações que o administrador adicionou anteriormente. Todos os usuários das organizações selecionadas poderão solicitar esse pacote de acesso. |
    | **Todas as organizações conectadas** | Escolha esta opção se desejar que todos os usuários de todas as suas organizações conectadas possam solicitar este pacote de acesso. |

    Uma organização conectada é um diretório ou domínio externo do Azure AD com o qual você tem uma relação.

1. Se você selecionou **organizações conectadas específicas**, clique em **adicionar diretórios** para selecionar em uma lista de organizações conectadas que seu administrador adicionou anteriormente.

1. Digite o nome ou nome de domínio para procurar uma organização conectada anteriormente.

    ![Pacotes de acesso-solicitações-selecionar diretórios](./media/active-directory-entitlement-management-request-policy/select-directories.png)

    Se a organização com a qual você deseja colaborar não estiver na lista, você poderá pedir ao administrador para adicioná-la como uma organização conectada. Para obter mais informações, consulte [Adicionar uma organização conectada](../articles/active-directory/governance/entitlement-management-organization.md).

1. Depois de selecionar todas as suas organizações conectadas, clique em **selecionar**.

    > [!NOTE]
    > Todos os usuários das organizações conectadas selecionadas poderão solicitar esse pacote de acesso. Isso inclui os usuários no Azure AD de todos os subdomínios associados à organização, a menos que esses domínios sejam bloqueados pela lista de permissão ou negação B2B do Azure. Para obter mais informações, consulte [permitir ou bloquear convites para usuários B2B de organizações específicas](../articles/active-directory/b2b/allow-deny-list.md).

1. Pule para a seção [aprovação](#approval) .

## <a name="none-administrator-direct-assignments-only"></a>Nenhum (somente atribuições diretas do administrador)

Siga estas etapas se desejar ignorar as solicitações de acesso e permitir que os administradores atribuam usuários específicos diretamente a esse pacote de acesso. Os usuários não precisarão solicitar o pacote de acesso. Você ainda pode definir as configurações do ciclo de vida, mas não há configurações de solicitação.

1. Na seção **usuários que podem solicitar acesso** , clique em **nenhum (somente atribuições diretas do administrador**.

    ![Pacotes de acesso-solicitações-nenhuma somente atribuições diretas de administrador](./media/active-directory-entitlement-management-request-policy/none-admin-direct-assignments-only.png)

    Depois de criar o pacote do Access, você pode atribuir diretamente usuários internos e externos específicos ao pacote do Access. Se você especificar um usuário externo, uma conta de usuário convidado será criada em seu diretório. Para obter informações sobre como atribuir um usuário diretamente, consulte [Exibir, adicionar e remover atribuições para um pacote de acesso](../articles/active-directory/governance/entitlement-management-access-package-assignments.md).

1. Pule para a seção [habilitar solicitações](#enable-requests) .

## <a name="approval"></a>Aprovação

Na seção aprovação, você especifica se uma aprovação é necessária quando os usuários solicitam esse pacote de acesso. As configurações de aprovação funcionam da seguinte maneira:

- Somente um dos aprovadores ou aprovadores de fallback selecionados precisa aprovar uma solicitação. A aprovação de todos os aprovadores não é necessária.
- A decisão de aprovação se baseia em qualquer aprovador que examine a solicitação primeiro.

Siga estas etapas para especificar as configurações de aprovação para os usuários selecionados anteriormente.

1. Para exigir aprovação para solicitações dos usuários selecionados, defina a opção **exigir aprovação** para **Sim**. Para que as solicitações sejam aprovadas automaticamente, defina a alternância para **não**.

    ![Pacotes de acesso-solicitações-configurações de aprovação](./media/active-directory-entitlement-management-request-policy/approval.png)

1. Para exigir que os usuários forneçam uma justificativa para solicitar o pacote de acesso, defina a alternância de **justificação exigir solicitante** como **Sim**.

1. Determine se a solicitação exigirá uma aprovação única ou de vários estágios. Defina o **número de estágios** de alternância para **1** para um único estágio.

1. Para Aprovadores, selecione **gerente como Aprovador** ou **escolha aprovadores específicos**.

    O gerente é determinado pelo atributo **Manager** no perfil do usuário do Azure AD. Para obter mais informações, consulte [Adicionar ou atualizar as informações de perfil de um usuário usando Azure Active Directory](../articles/active-directory/fundamentals/active-directory-users-profile-azure-portal.md).

    ![Azure Active Directory atributo User Profile-Manager](./media/active-directory-entitlement-management-request-policy/profile-manager.png)

1. Se você selecionou gerente como Aprovador, clique em **Adicionar fallback** para selecionar um ou mais usuários ou grupos em seu diretório para ser um Aprovador de fallback no caso de gerenciamento de direitos não conseguir localizar o Gerenciador.

1. Se você selecionou escolher aprovadores específicos, clique em **Adicionar aprovadores** para selecionar um ou mais usuários ou grupos em seu diretório para serem aprovadores.

1. Em **decisão deve ser feita em quantos dias?** , especifique o número de dias que um aprovador precisa examinar uma solicitação para esse pacote de acesso.

    Se uma solicitação não for aprovada nesse período de tempo, ela será negada automaticamente. O usuário precisará enviar outra solicitação para o pacote de acesso.

1. Para exigir que os usuários forneçam uma justificativa para solicitar o pacote de acesso, defina **exigir justificação** como **Sim**.

    Uma justificativa é visível para outros Aprovadores e o solicitante.

## <a name="enable-requests"></a>Habilitar solicitações

1. Se você quiser que o pacote de acesso seja disponibilizado imediatamente para usuários na política de solicitação para solicitar, clique em **Sim** para habilitar.

    Você sempre poderá habilitá-lo no futuro depois de concluir a criação do pacote de acesso.

    Se você selecionou **nenhum (somente atribuições diretas do administrador)** e definir habilitar como **não**, os administradores não poderão atribuir diretamente este pacote de acesso.

    ![Pacote de acesso-configuração de política de habilitação de política](./media/active-directory-entitlement-management-request-policy/enable-requests.png)

1. Clique em **Seguinte**.
