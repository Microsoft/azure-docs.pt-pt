---
title: Gerenciar usuários excluídos de políticas de acesso condicional – Azure AD
description: Saiba como usar as revisões de acesso do Azure Active Directory (AD do Azure) para gerenciar usuários que foram excluídos das políticas de acesso condicional
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 09/25/2018
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1d7bce940f2b614c239e8b5e5719d96da10a6c0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75422716"
---
# <a name="use-azure-ad-access-reviews-to-manage-users-excluded-from-conditional-access-policies"></a>Usar as revisões de acesso do Azure AD para gerenciar usuários excluídos das políticas de acesso condicional

Em um mundo ideal, todos os usuários seguirão as políticas de acesso para proteger o acesso aos recursos da sua organização. No entanto, às vezes há casos comerciais que exigem que você faça exceções. Este artigo descreve alguns exemplos em que as exclusões podem ser necessárias e como você, como administrador de ti, pode gerenciar essa tarefa, evitar a supervisão de exceções de política e fornecer aos auditores uma prova de que essas exceções são revisadas regularmente usando o Azure Revisões de acesso do Active Directory (Azure AD).

> [!NOTE]
> Uma licença válida Azure AD Premium P2, Enterprise Mobility + Security E5 paga ou de avaliação é necessária para usar as revisões de acesso do Azure AD. Para obter mais informações, consulte [Edições do Azure Active Directory](../fundamentals/active-directory-whatis.md).

## <a name="why-would-you-exclude-users-from-policies"></a>Por que você excluiria os usuários das políticas?

Como um administrador de ti, você pode usar o [acesso condicional do Azure ad](../conditional-access/overview.md) para exigir que os usuários se autentiquem usando a MFA (autenticação multifator) ou entrem de uma rede ou dispositivo confiável. Durante o planejamento da implantação, você descobre que alguns desses requisitos não podem ser atendidos por todos os usuários. Por exemplo, há usuários que trabalham em um escritório remoto que não fazem parte da sua rede interna ou que há um executivo que usa um telefone antigo que não tem suporte. A empresa exige que esses usuários tenham permissão para entrar e realizar seu trabalho, portanto, eles são excluídos das políticas de acesso condicional.

Como outro exemplo, você pode usar [locais nomeados](../conditional-access/location-condition.md) em acesso condicional para configurar um conjunto de regiões e localizações das quais você não deseja permitir que os usuários acessem seu locatário.

![Locais nomeados no acesso condicional](./media/conditional-access-exclusion/named-locations.png)

No entanto, em alguns casos, os usuários podem ter um motivo legítimo para entrar nesses países/regiões bloqueados. Por exemplo, os usuários podem estar viajando por motivos de trabalho ou pessoais. Neste exemplo, a política de acesso condicional para bloquear esses países/regiões pode ter um grupo de segurança de nuvem dedicado para os usuários que foram excluídos da política. Os usuários que precisam de acesso durante a viagem podem se adicionar ao grupo usando o [Gerenciamento de grupo de autoatendimento do Azure ad](../users-groups-roles/groups-self-service-management.md).

Outro exemplo seria que você tem uma política de acesso condicional que [bloqueia a autenticação herdada para a grande maioria dos seus usuários](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/07/azure-ad-conditional-access-support-for-blocking-legacy-auth-is-in-public-preview/). A Microsoft recomenda enfaticamente que você bloqueie o uso de protocolos herdados em seu locatário para melhorar sua postura de segurança. No entanto, se você tiver alguns usuários que absolutamente precisam usar métodos de autenticação herdados para acessar seus recursos por meio de clientes baseados no Office 2010 ou IMAP/SMTP/POP, você poderá excluir esses usuários da política que bloqueia métodos de autenticação herdados.

## <a name="why-are-exclusions-challenging"></a>Por que as exclusões são desafiadoras?

No Azure AD, você pode definir o escopo de uma política de acesso condicional para um conjunto de usuários. Você também pode excluir alguns desses usuários selecionando funções do Azure AD, usuários individuais ou convidados de usuários. É importante lembrar que, quando essas exclusões são configuradas, a intenção de diretiva não pode ser imposta para esses usuários. Se essas exclusões foram configuradas como uma lista de usuários individuais ou por meio de um grupo de segurança local herdado, ela limita a visibilidade dessa lista de exclusão (os usuários podem não saber sua existência) e o controle do administrador de ti sobre ele (os usuários podem ingressar no grupo de segurança a passar a política). Além disso, os usuários qualificados para a exclusão de uma só vez podem não precisar mais dele ou ser qualificados para ele.

No início de uma exclusão, há uma pequena lista de usuários que ignoram a política. Ao longo do tempo, cada vez mais usuários são excluídos e a lista aumenta. Em algum momento, é necessário examinar a lista e confirmar se cada um desses usuários ainda deve ser excluído. Gerenciar a lista de um ponto de vista técnico pode ser relativamente fácil, mas quem toma as decisões de negócios e como você garante que ela seja auditada?

No entanto, se você configurar a exclusão para a política de acesso condicional usando um grupo do Azure AD, poderá usar as revisões de acesso como um controle de compensação, para impulsionar a visibilidade e reduzir o número de usuários que têm uma exceção.

## <a name="how-to-create-an-exclusion-group-in-a-conditional-access-policy"></a>Como criar um grupo de exclusão em uma política de acesso condicional

Siga estas etapas para criar um novo grupo do Azure AD e uma política de acesso condicional que não se aplica a esse grupo.

### <a name="create-an-exclusion-group"></a>Criar um grupo de exclusão

1. Inicie sessão no Portal do Azure.

1. No painel de navegação esquerdo, clique em **Azure Active Directory** e em **grupos**.

1. No menu superior, clique em **novo grupo** para abrir o painel grupo.

1. Na lista **tipo de grupo** , selecione **segurança**. Especifique um nome e uma descrição.

1. Certifique-se de definir o tipo de **Associação** como **atribuído**.

1. Selecione os usuários que devem fazer parte desse grupo de exclusão e, em seguida, clique em **criar**.

    ![Painel novo grupo no Azure Active Directory](./media/conditional-access-exclusion/new-group.png)

### <a name="create-a-conditional-access-policy-that-excludes-the-group"></a>Criar uma política de acesso condicional que exclui o grupo

Agora você pode criar uma política de acesso condicional que usa esse grupo de exclusão.

1. No painel de navegação esquerdo, clique em **Azure Active Directory** e, em seguida, clique em **acesso condicional** para abrir a folha **políticas** .

1. Clique em **nova política** para abrir o **novo** painel.

1. Especifique um nome.

1. Em atribuições **, clique em usuários e grupos**.

1. Na guia **incluir** , selecione **todos os usuários**.

1. Na guia **excluir** , adicione uma marca de seleção a **usuários e grupos** e clique em **Selecionar usuários excluídos**.

1. Selecione o grupo de exclusão que você criou.

    > [!NOTE]
    > Como prática recomendada, é recomendável excluir pelo menos uma conta de administrador da política ao testar para certificar-se de que você não está bloqueado do seu locatário.

1. Continue com a configuração da política de acesso condicional com base em seus requisitos organizacionais.

    ![Selecionar o painel usuários excluídos em acesso condicional](./media/conditional-access-exclusion/select-excluded-users.png)

Vamos abordar dois exemplos em que você pode usar as revisões de acesso para gerenciar exclusões em políticas de acesso condicional.

## <a name="example-1-access-review-for-users-accessing-from-blocked-countriesregions"></a>Exemplo 1: revisão de acesso para usuários que acessam de países/regiões bloqueados

Digamos que você tenha uma política de acesso condicional que bloqueie o acesso de determinados países/regiões. Ele inclui um grupo que é excluído da política. Aqui está uma revisão de acesso recomendada onde os membros do grupo são revisados.

> [!NOTE]
> Uma função de administrador global ou de administrador de usuários é necessária para criar revisões de acesso.

1. A revisão ocorrerá novamente a cada semana.

2. Nunca terminará para garantir que você esteja mantendo esse grupo de exclusão o mais atualizado.

3. Todos os membros deste grupo estarão no escopo da revisão.

4. Cada usuário terá que atestar automaticamente que eles ainda precisam ter acesso a partir desses países/regiões bloqueados, portanto, eles ainda precisam ser membros do grupo.

5. Se o usuário não responder à solicitação de revisão, ele será removido automaticamente do grupo e, portanto, não poderá mais acessar o locatário ao viajar para esses países/regiões.

6. Habilitar notificações por email para que os usuários sejam notificados sobre o início e a conclusão da revisão de acesso.

    ![Criar um painel de revisão de acesso, por exemplo 1](./media/conditional-access-exclusion/create-access-review-1.png)

## <a name="example-2-access-review-for-users-accessing-with-legacy-authentication"></a>Exemplo 2: revisão de acesso para usuários que acessam com autenticação herdada

Digamos que você tenha uma política de acesso condicional que bloqueie o acesso para usuários usando autenticação herdada e versões de cliente mais antigas. Ele inclui um grupo que é excluído da política. Aqui está uma revisão de acesso recomendada onde os membros do grupo são revisados.

1. Essa análise precisaria ser uma revisão recorrente.

2. Todos no grupo precisariam ser revisados.

3. Ele pode ser configurado para listar os proprietários da unidade de negócios como os revisores selecionados.

4. Aplicar automaticamente os resultados e remover usuários que não foram aprovados para continuar usando métodos de autenticação herdados.

5. Pode ser benéfico habilitar as recomendações para que os revisores de grupos grandes possam tomar suas decisões facilmente.

6. Habilitar notificações por email para que os usuários sejam notificados sobre o início e a conclusão da revisão de acesso.

    ![Criar um painel de revisão de acesso, por exemplo 2](./media/conditional-access-exclusion/create-access-review-2.png)

**Dica de pro**: se você tiver muitos grupos de exclusão e, portanto, precisar criar várias revisões de acesso, agora temos uma API no ponto de extremidade Microsoft Graph beta que permite criá-las e gerenciá-las programaticamente. Para começar, consulte a [referência da API de revisões de acesso do Azure ad](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/accessreviews_root) e o [exemplo de recuperação de revisões de acesso do Azure ad por meio de Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/td-p/236096).

## <a name="access-review-results-and-audit-logs"></a>Resultados da revisão de acesso e logs de auditoria

Agora que você tem tudo em vigor, grupo, política de acesso condicional e revisões de acesso, é hora de monitorar e acompanhar os resultados dessas revisões.

1. Na portal do Azure, abra a folha **revisões de acesso** .

1. Abra o controle e o programa que você criou para gerenciar o grupo de exclusão.

1. Clique em **resultados** para ver quem foi aprovado para permanecer na lista e que foi removido.

    ![Resultados de revisões de acesso mostram quem foi aprovado](./media/conditional-access-exclusion/access-reviews-results.png)

1. Em seguida, clique em **logs de auditoria** para ver as ações que foram executadas durante essa revisão.

    ![Acesso examina logs de auditoria listando ações](./media/conditional-access-exclusion/access-reviews-audit-logs.png)

Como administrador de ti, você sabe que o gerenciamento de grupos de exclusão para suas políticas, às vezes, é inevitável. No entanto, manter esses grupos, analisá-los regularmente pelo proprietário da empresa ou pelos próprios usuários e auditar essas alterações pode facilitar com as revisões de acesso do Azure AD.

## <a name="next-steps"></a>Passos seguintes

- [Criar uma revisão de acesso de grupos ou aplicativos](create-access-review.md)
- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)
