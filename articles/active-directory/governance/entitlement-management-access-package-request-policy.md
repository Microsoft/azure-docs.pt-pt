---
title: Alterar as definições de pedido para um pacote de acesso na gestão de direitos Azure AD - Azure Ative Directory
description: Saiba como alterar as definições de pedido para um pacote de acesso na gestão de direitos do Azure Ative Directory.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 09/16/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b6bc00af8d54c8748dd82b934974282e0e8da0e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102426965"
---
# <a name="change-request-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Alterar as definições de pedido para um pacote de acesso na gestão de direitos Azure AD

Como gestor de pacotes de acesso, pode alterar os utilizadores que podem solicitar um pacote de acesso a qualquer momento, editando a política ou adicionando uma nova política. Este artigo descreve como alterar as definições de pedido para um pacote de acesso existente.

## <a name="choose-between-one-or-multiple-policies"></a>Escolha entre uma ou múltiplas políticas

A forma como especifica quem pode solicitar um pacote de acesso é com uma apólice. Antes de criar uma nova política ou editar uma política existente num pacote de acesso, é necessário determinar quantas políticas o pacote de acesso necessita. 

Quando cria um pacote de acesso, especifica a definição de pedido que cria uma política. A maioria dos pacotes de acesso terá uma única política, mas um único pacote de acesso pode ter várias políticas. Criaria várias políticas para um pacote de acesso se quiser permitir que diferentes conjuntos de utilizadores recebam atribuições com diferentes configurações de pedido e aprovação. 

Por exemplo, não se pode utilizar uma única política para atribuir utilizadores internos e externos ao mesmo pacote de acesso. No entanto, pode criar duas políticas no mesmo pacote de acesso, uma para utilizadores internos e outra para utilizadores externos. Se existirem múltiplas políticas aplicáveis a um utilizador, estas serão solicitadas no momento do seu pedido para selecionar a política a que gostariam de ser atribuídas. O diagrama que se segue mostra um pacote de acesso com duas políticas.

![Múltiplas políticas num pacote de acesso](./media/entitlement-management-access-package-request-policy/access-package-policy.png)

### <a name="how-many-policies-will-i-need"></a>De quantas políticas vou precisar?

| Scenario | Número de políticas |
| --- | --- |
| Quero que todos os utilizadores do meu diretório tenham as mesmas definições de pedido e aprovação para um pacote de acesso | Um |
| Quero que todos os utilizadores de certas organizações conectadas possam solicitar um pacote de acesso | Um |
| Quero permitir que os utilizadores do meu diretório e também utilizadores fora do meu diretório solicitem um pacote de acesso | Vários |
| Quero especificar diferentes definições de aprovação para alguns utilizadores | Vários |
| Quero que alguns utilizadores acedam a atribuição de pacotes enquanto outros utilizadores podem alargar o seu acesso | Vários |

Para obter informações sobre a lógica prioritária que é utilizada quando se aplicam várias políticas, consulte [várias políticas](entitlement-management-troubleshoot.md#multiple-policies
).

## <a name="open-an-existing-access-package-and-add-a-new-policy-of-request-settings"></a>Abra um pacote de acesso existente e adicione uma nova política de definições de pedidos

Se tiver um conjunto de utilizadores que deverão ter diferentes configurações de pedido e aprovação, provavelmente terá de criar uma nova política. Siga estes passos para começar a adicionar uma nova política a um pacote de acesso existente:

**Papel pré-requisito:** Administrador global, administrador de utilizador, proprietário de catálogo ou gestor de pacotes access

1. No portal Azure, clique no **Diretório Ativo Azure** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, clique em **pacotes de Acesso** e, em seguida, abra o pacote de acesso.

1. Clique **em Políticas** e, em seguida, Adicione a **política**.

1. Começará no **separador Básico.** Escreva um nome e uma descrição para a apólice.

    ![Criar política com nome e descrição](./media/entitlement-management-access-package-request-policy/policy-name-description.png)

1. Clique **ao lado** para abrir o separador **Pedidos.**

1. Altere os **Utilizadores que podem solicitar a** definição de acesso. Utilize os passos nas seguintes secções para alterar a definição para uma das seguintes opções: 
    - [Para utilizadores no seu diretório](#for-users-in-your-directory) 
    - [Para utilizadores que não estão no seu diretório](#for-users-not-in-your-directory)
    - [Nenhum (apenas atribuições diretas de administrador)](#none-administrator-direct-assignments-only)

## <a name="for-users-in-your-directory"></a>Para utilizadores no seu diretório

Siga estes passos se pretender permitir que os utilizadores do seu diretório possam solicitar este pacote de acesso. Ao definir a política de pedidos, pode especificar utilizadores individuais ou grupos de utilizadores mais comum. Por exemplo, a sua organização pode já ter um grupo como **Todos os colaboradores.**  Se esse grupo for adicionado na política para utilizadores que possam solicitar acesso, então qualquer membro desse grupo pode então solicitar acesso.

1. Nos **Utilizadores que podem solicitar a** secção de acesso, clique em Para **utilizadores no seu diretório**.

    Ao selecionar esta opção, novas opções parecem refinar ainda mais quem no seu diretório pode solicitar este pacote de acesso.

    ![Pacote de acesso - Pedidos - Para utilizadores no seu diretório](./media/entitlement-management-access-package-request-policy/for-users-in-your-directory.png)

1. Selecione uma das seguintes opções:

    |  |  |
    | --- | --- |
    | **Utilizadores e grupos específicos** | Escolha esta opção se quiser apenas os utilizadores e grupos no seu diretório que especifique para poder solicitar este pacote de acesso. |
    | **Todos os membros (excluindo convidados)** | Escolha esta opção se quiser que todos os utilizadores membros do seu diretório possam solicitar este pacote de acesso. Esta opção não inclui quaisquer utilizadores convidados que possa ter convidado para o seu diretório. |
    | **Todos os utilizadores (incluindo convidados)** | Escolha esta opção se quiser que todos os utilizadores membros e utilizadores convidados do seu diretório possam solicitar este pacote de acesso. |

    Os utilizadores convidados referem-se a utilizadores externos que foram convidados para o seu diretório com [a Azure AD B2B](../external-identities/what-is-b2b.md). Para obter mais informações sobre as diferenças entre utilizadores membros e utilizadores [convidados, consulte quais são as permissões padrão do utilizador no Azure Ative Directory?](../fundamentals/users-default-permissions.md)

1. Se selecionar **utilizadores e grupos específicos,** clique em **Adicionar utilizadores e grupos**.

1. No painel de grupos Select, selecione os utilizadores e grupos que pretende adicionar.

    ![Pacote de acesso - Pedidos - Selecione utilizadores e grupos](./media/entitlement-management-access-package-request-policy/select-users-groups.png)

1. Clique **em Selecionar** para adicionar os utilizadores e grupos.

1. Se quiser necessitar de aprovação, utilize as definições de [aprovação change para um pacote de acesso na gestão de direitos AD Azure](entitlement-management-access-package-approval-policy.md) para configurar as definições de aprovação.

1. Aceda à secção [de pedidos de ativação.](#enable-requests)
 
## <a name="for-users-not-in-your-directory"></a>Para utilizadores que não estão no seu diretório

 **Os utilizadores que não estão no seu diretório** referem-se a utilizadores que se encontram noutro diretório ou domínio AZure AD. Estes utilizadores podem ainda não ter sido convidados para o seu diretório. Os diretórios AD da Azure devem ser configurados para permitir convites em **restrições de colaboração.** Para mais informações, consulte [a colaboração externa do B2B e gere quem pode convidar os hóspedes.](../external-identities/delegate-invitations.md)

> [!NOTE]
> Será criada uma conta de utilizador para um utilizador ainda não no seu diretório cujo pedido seja aprovado ou aprovado automaticamente. O convidado será convidado, mas não receberá um e-mail de convite. Em vez disso, receberão um e-mail quando a sua atribuição de pacote de acesso for entregue. Por padrão, mais tarde, quando esse utilizador convidado já não tiver quaisquer atribuições de pacotes de acesso, porque a sua última atribuição expirou ou foi cancelada, essa conta de utilizador do hóspede será bloqueada de iniciar sessão e posteriormente eliminada. Se pretender que os utilizadores convidados permaneçam indefinidamente no seu diretório, mesmo que não tenham atribuição de pacotes de acesso, pode alterar as definições para a sua configuração de gestão de direitos. Para obter mais informações sobre o objeto do utilizador convidado, consulte [propriedades de um utilizador de colaboração Azure Ative Directory B2B](../external-identities/user-properties.md).

Siga estes passos se pretender permitir que os utilizadores que não estão no seu diretório solicitem este pacote de acesso:

1. Nos **Utilizadores que podem solicitar a** secção de acesso, clique em Para **utilizadores que não estão no seu diretório**.

    Ao selecionar esta opção, surgem novas opções.

    ![Pacote de acesso - Pedidos - Para utilizadores que não estão no seu diretório](./media/entitlement-management-access-package-request-policy/for-users-not-in-your-directory.png)

1. Selecione uma das seguintes opções:

    |  |  |
    | --- | --- |
    | **Organizações conectadas específicas** | Escolha esta opção se quiser selecionar a partir de uma lista de organizações que o seu administrador anteriormente acrescentou. Todos os utilizadores das organizações selecionadas podem solicitar este pacote de acesso. |
    | **Todas as organizações conectadas configuradas** | Escolha esta opção se todos os utilizadores de todas as suas organizações conectadas configuradas puderem solicitar este pacote de acesso. Apenas os utilizadores de organizações conectadas configuradas podem solicitar pacotes de acesso que sejam mostrados aos utilizadores de todas as organizações configuradas. |
    | **Todos os utilizadores (Todas as organizações conectadas + quaisquer novos utilizadores externos)** | Escolha esta opção se algum utilizador na internet puder solicitar este pacote de acesso.  Se não pertencerem a uma organização conectada no seu diretório, uma organização conectada será automaticamente criada para eles quando solicitarem o pacote. A organização conectada criada automaticamente estará num estado **proposto.** Para obter mais informações sobre o estado proposto, consulte [as propriedades estatais de organizações conectadas.](entitlement-management-organization.md#state-properties-of-connected-organizations) |

    Uma organização conectada é um diretório ou domínio AD Azure externo com o qual você tem uma relação.

1. Se selecionou **organizações conectadas específicas,** clique em **Adicionar diretórios** para selecionar a partir de uma lista de organizações conectadas que o seu administrador anteriormente acrescentou.

1. Digite o nome ou o nome de domínio para procurar uma organização previamente ligada.

    ![Pacote de acesso - Pedidos - Selecione diretórios](./media/entitlement-management-access-package-request-policy/select-directories.png)

    Se a organização com quem pretende colaborar não estiver na lista, pode pedir ao seu administrador que o adicione como uma organização conectada. Para obter mais informações, consulte [Adicionar uma organização conectada.](entitlement-management-organization.md)

1. Uma vez selecionadas todas as organizações ligadas, clique em **Select**.

    > [!NOTE]
    > Todos os utilizadores das organizações conectadas selecionadas poderão solicitar este pacote de acesso. Isto inclui utilizadores em Azure AD de todos os subdomínios associados à organização, a menos que esses domínios sejam bloqueados pela lista Azure B2B permitir ou negar. Para obter mais informações, consulte [Permitir ou bloquear convites a utilizadores B2B de organizações específicas.](../external-identities/allow-deny-list.md)

1. Se quiser necessitar de aprovação, utilize as definições de [aprovação change para um pacote de acesso na gestão de direitos AD Azure](entitlement-management-access-package-approval-policy.md) para configurar as definições de aprovação.
 
1. Aceda à secção [de pedidos de ativação.](#enable-requests)

## <a name="none-administrator-direct-assignments-only"></a>Nenhum (apenas atribuições diretas de administrador)

Siga estes passos se pretender contornar os pedidos de acesso e permitir que os administradores atribuam diretamente utilizadores específicos a este pacote de acesso. Os utilizadores não terão de solicitar o pacote de acesso. Ainda é possível definir as definições do ciclo de vida, mas não existem definições de pedido.

1. Nos **Utilizadores que podem solicitar** a secção de acesso, clique em **Nenhum (apenas as atribuições diretas do administrador**.

    ![Pacote de acesso - Pedidos - Nenhum administrador atribui apenas atribuições diretas](./media/entitlement-management-access-package-request-policy/none-admin-direct-assignments-only.png)

    Depois de criar o pacote de acesso, pode atribuir diretamente utilizadores internos e externos específicos ao pacote de acesso. Se especificar um utilizador externo, será criada uma conta de utilizador convidada no seu diretório. Para obter informações sobre a atribuição direta de um utilizador, consulte [Ver, adicionar e remover as atribuições de um pacote de acesso](entitlement-management-access-package-assignments.md).

1. Salte para a secção [de pedidos de ativação.](#enable-requests)


## <a name="open-and-edit-an-existing-policy-of-request-settings"></a>Abrir e editar uma política existente de definições de pedidos

Para alterar as definições de pedido e aprovação de um pacote de acesso, é necessário abrir a política correspondente. Siga estes passos para abrir e editar as definições de pedido para um pacote de acesso:

**Papel pré-requisito:** Administrador global, administrador de utilizador, proprietário de catálogo ou gestor de pacotes access

1. No portal Azure, clique no **Diretório Ativo Azure** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, clique em **pacotes de Acesso** e, em seguida, abra o pacote de acesso.

1. Clique em **Políticas** e, em seguida, clique na política que pretende editar.

    O painel de detalhes da Política abre na parte inferior da página.

    ![Pacote de acesso - Painel de detalhes de política](./media/entitlement-management-shared/policy-details.png)

1. Clique **em Editar** para editar a política.

    ![Pacote de acesso - Política de edição](./media/entitlement-management-shared/policy-edit.png)

1. Clique no separador **Pedidos** para abrir as definições de pedido.

1. Utilize os passos nas secções anteriores para alterar as definições de pedido conforme necessário.

1. Aceda à secção [de pedidos de ativação.](#enable-requests)

## <a name="enable-requests"></a>Ativar pedidos

1. Se pretender que o pacote de acesso seja disponibilizado imediatamente para os utilizadores na política de pedidos a solicitar, mova o Enable toggle para **Sim**.

    Pode sempre aditá-lo no futuro depois de ter terminado de criar o pacote de acesso.

    Se selecionou **Nenhum (apenas atribuições diretas de administrador)** e definiu ativar para **Nº,** então os administradores não podem atribuir diretamente este pacote de acesso.

    ![Pacote de acesso - Política- Permitir a definição de políticas](./media/entitlement-management-access-package-approval-policy/enable-requests.png)

1. Clique em **Seguinte**.

1. Se pretender que os solicitadores forneçam informações adicionais ao solicitar o acesso a um pacote de acesso, utilize as definições de [aprovação e informações do solicitador (pré-visualização) para um pacote de acesso na gestão de direitos AD da Azure](entitlement-management-access-package-approval-policy.md#collect-additional-requestor-information-for-approval-preview) para configurar informações do solicitador (pré-visualização).

1. Configure as definições do ciclo de vida.

1. Se estiver a editar uma política clique em **Update**. Se estiver a adicionar uma nova política, clique em **Criar**.

## <a name="next-steps"></a>Passos seguintes

- [Alterar as definições de aprovação para um pacote de acesso](entitlement-management-access-package-approval-policy.md)
- [Alterar as definições do ciclo de vida para um pacote de acesso](entitlement-management-access-package-lifecycle-policy.md)
- [Ver pedidos de um pacote de acesso](entitlement-management-access-package-requests.md)
