---
title: incluir ficheiro
description: incluir ficheiro
services: active-directory
author: rolyon
ms.service: active-directory
ms.topic: include
ms.date: 07/31/2019
ms.author: rolyon
ms.custom: include file
ms.openlocfilehash: 154d71c9cbc109834a5854b46c3e6584dcefa7eb
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68968921"
---
### <a name="policy-for-users-in-your-directory"></a>Política: Para usuários em seu diretório

Siga estas etapas se quiser que sua política seja para os usuários em seu diretório que podem solicitar esse pacote de acesso.  Os **usuários em seu diretório** referem-se a usuários internos, bem como a usuários externos que foram convidados anteriormente para o diretório, por meio deles solicitando o gerenciamento de direitos com outro pacote de acesso ou sendo convidado com o Azure ad B2B. Ao definir a política, você pode especificar usuários individuais ou grupos de usuários mais comuns. Por exemplo, sua organização pode já ter um grupo como **todos os funcionários**.  Se esse grupo for adicionado na política para usuários que podem solicitar acesso, então qualquer membro desse grupo poderá solicitar acesso.

1. Na seção **usuários que podem solicitar acesso** , selecione **para usuários em seu diretório**.

    Observe que a configuração **para usuários em seu diretório** inclui usuários Membros e usuários convidados que foram adicionados ao seu diretório. Se você quiser incluir apenas usuários Membros e não usuários convidados, selecione **para usuários em seu diretório** e, em seguida, selecione um grupo de seus usuários membros. Se necessário, você pode criar um grupo dinâmico de seus usuários Membros (User. UserType-EQ "membro"). Para obter mais informações, consulte [regras de associação dinâmica para grupos no Azure Active Directory](../articles/active-directory/users-groups-roles/groups-dynamic-membership.md).

1. Na seção **Selecionar usuários e grupos** , clique em **Adicionar usuários e grupos**.

1. No painel Selecionar usuários e grupos, selecione os usuários e grupos que você deseja adicionar.

    ![Pacotes de acesso-política-Selecionar usuários e grupos](./media/active-directory-entitlement-management-policy/policy-select-users-groups.png)

1. Clique em **selecionar** para adicionar os usuários e grupos.

1. Pule para a [política: Seção](#policy-request) de solicitação.

### <a name="policy-for-users-not-in-your-directory"></a>Política: Para usuários que não estão em seu diretório

Siga estas etapas se quiser que sua política seja para os usuários que não estão em seu diretório que podem solicitar esse pacote de acesso. Os **usuários que não estão em seu diretório** referem-se aos usuários que estão em outro diretório do Azure AD e podem ainda não ter sido convidados em seu diretório. No momento, você só pode adicionar usuários de organizações que têm o Azure AD. Os diretórios devem ser configurados para serem permitidos nas configurações de **restrições de colaboração de relações organizacionais** .

> [!NOTE]
> Uma conta de usuário externo convidado será criada para um usuário que ainda não está em seu diretório cuja solicitação foi aprovada ou aprovada automaticamente. O convidado será convidado, mas não receberá um email de convite. Em vez disso, eles receberão um email quando sua atribuição de pacote de acesso for entregue. Por padrão, mais tarde, quando o usuário convidado não tiver mais nenhuma atribuição de pacote de acesso, porque sua última atribuição expirou ou foi cancelada, essa conta de usuário convidado será bloqueada de entrar e excluída subsequentemente. Se você quiser que os usuários convidados permaneçam em seu diretório indefinidamente, mesmo que eles não tenham atribuições de pacote de acesso, você poderá alterar as configurações de sua configuração de gerenciamento de direitos.

1. Na seção **usuários que podem solicitar acesso** , selecione **para os usuários que não estão em seu diretório**.

1. Na seção **Selecionar diretório externo do AD do Azure** , clique em **adicionar diretórios**.

1. Insira um nome de domínio e procure um diretório do Azure AD com esse nome de domínio.

1. Verifique se ele é o diretório correto pelo nome do diretório fornecido e pelo domínio inicial.

    > [!NOTE]
    > Todos os usuários do diretório poderão solicitar esse pacote de acesso. Isso inclui os usuários de todos os subdomínios associados ao diretório, não apenas o domínio usado na pesquisa.

    ![Pacotes de acesso-política-selecionar diretórios](./media/active-directory-entitlement-management-policy/policy-select-directories.png)

1. Clique em **Adicionar** para adicionar o diretório.

1. Repita essa etapa para adicionar mais diretórios.

1. Depois de adicionar todos os diretórios que você deseja incluir na política, clique em **selecionar**.

1. Pule para a [política: Seção](#policy-request) de solicitação.

### <a name="policy-none-administrator-direct-assignments-only"></a>Política: Nenhum (somente atribuições diretas do administrador)

Siga estas etapas se desejar que a política ignore as solicitações de acesso e permita que os administradores atribuam diretamente usuários específicos ao pacote de acesso. Os usuários não precisarão solicitar o pacote de acesso. Você ainda pode definir as configurações de expiração, mas não há configurações de solicitação.

1. Na seção **usuários que podem solicitar acesso** , selecione **nenhum (somente atribuições diretas do administrador**.

    Depois de criar o pacote do Access, você pode atribuir diretamente usuários internos e externos específicos ao pacote do Access. Se você especificar um usuário externo, uma conta de usuário convidado será criada em seu diretório.

1. Pule para a [política: Seção](#policy-expiration) de expiração.

### <a name="policy-request"></a>Política: Pedir

Na seção solicitação, você especifica as configurações de aprovação quando os usuários solicitam o pacote de acesso.

1. Para exigir aprovação para solicitações dos usuários selecionados, defina a opção **exigir aprovação** para **Sim**. Para que as solicitações sejam aprovadas automaticamente, defina a alternância para **não**.

1. Se você precisar de aprovação, na seção **selecionar aprovadores** , clique em **Adicionar aprovadores**.

1. No painel Selecionar Aprovadores, selecione um ou mais usuários e/ou grupos a serem aprovadores.

    Somente um dos aprovadores selecionados precisa aprovar uma solicitação. A aprovação de todos os aprovadores não é necessária. A decisão de aprovação se baseia em qualquer aprovador que examine a solicitação primeiro.

    ![Pacote de acesso-política-selecionar aprovadores](./media/active-directory-entitlement-management-policy/policy-select-approvers.png)

1. Clique em **selecionar** para adicionar os aprovadores.

1. Clique em **Mostrar configurações avançadas de solicitação** para mostrar configurações adicionais.

    ![Pacotes de acesso-política-selecionar diretórios](./media/active-directory-entitlement-management-policy/policy-advanced-request.png)

1. Para exigir que os usuários forneçam uma justificativa para solicitar o pacote de acesso, defina **exigir justificação** como **Sim**.

1. Para exigir que o aprovador forneça uma justificativa para aprovar uma solicitação para o pacote de acesso, defina **exigir justificação** do aprovador como **Sim**.

1. Na caixa **tempo limite da solicitação de aprovação (dias)** , especifique a quantidade de tempo que os aprovadores precisam para examinar uma solicitação. Se nenhum aprovador revisar esse número de dias, a solicitação expirará e o usuário precisará enviar outra solicitação para o pacote de acesso.

### <a name="policy-expiration"></a>Política: Expiração

Na seção expiração, você especifica quando a atribuição de um usuário para o pacote de acesso expira.

1. Na seção **expiração** , definir o **pacote de acesso expira** em **Data**, **número de dias**ou **nunca**.

    Para **na data**, selecione uma data de validade no futuro.

    Para **número de dias**, especifique um número entre 0 e 3660 dias.

    Com base na sua seleção, a atribuição de um usuário ao pacote de acesso expira em uma determinada data, um determinado número de dias depois que eles são aprovados ou nunca.

1. Clique em **Mostrar configurações de expiração avançadas** para mostrar configurações adicionais.

1. Para permitir que o usuário Estenda suas atribuições, defina **permitir que os usuários** estendam o acesso para **Sim**.

    Se as extensões forem permitidas na política, o usuário receberá um email 14 dias e também um dia antes que sua atribuição de pacote de acesso seja definida para expirar solicitando que estendam a atribuição.

    ![Pacotes de acesso-configurações de expiração de política](./media/active-directory-entitlement-management-policy/policy-expiration.png)

### <a name="policy-enable-policy"></a>Política: Ativar política

1. Se você quiser que o pacote de acesso seja disponibilizado imediatamente para os usuários na política, clique em **Sim** para habilitar a política.

    Você sempre poderá habilitá-lo no futuro depois de concluir a criação do pacote de acesso.

    ![Pacote de acesso-configuração de política de habilitação de política](./media/active-directory-entitlement-management-policy/policy-enable.png)

1. Clique em **Avançar** ou **criar**.
