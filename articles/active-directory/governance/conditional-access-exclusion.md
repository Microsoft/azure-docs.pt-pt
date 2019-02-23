---
title: Utilizar as revisões de acesso do Azure AD para gerir utilizadores excluídos da políticas de acesso condicional | Documentos da Microsoft
description: Saiba como utilizar as revisões de acesso do Azure Active Directory (Azure AD) para gerir utilizadores que foram excluídos da políticas de acesso condicional
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 09/25/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75ff08111e96fd4b210dfeb09db521856f4f5e52
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56727507"
---
# <a name="use-azure-ad-access-reviews-to-manage-users-excluded-from-conditional-access-policies"></a>Utilize as revisões de acesso do Azure AD para gerir utilizadores excluídos da políticas de acesso condicional

Num mundo ideal, todos os utilizadores seguir o acesso políticas para proteger o acesso aos recursos da sua organização. No entanto, às vezes, existem casos comerciais que exigem que crie exceções. Este artigo descreve alguns exemplos em que as exclusões poderão ser necessárias e como, como o administrador de TI, pode gerir esta tarefa, evitar supervisão de exceções da política e fornecer auditores prova que essas exceções são revistas regularmente a utilizar o Azure Revisões de acesso do Active Directory (Azure AD).

> [!NOTE]
> As revisões de acesso um válido do Azure AD Premium P2, Enterprise Mobility + Security E5 licença paga ou de avaliação é necessário para utilizar o Azure AD. Para obter mais informações, consulte [Edições do Azure Active Directory](../fundamentals/active-directory-whatis.md).

## <a name="why-would-you-exclude-users-from-policies"></a>Por que faria para excluir utilizadores do políticas?

Como um administrador de TI, poderá usar [acesso condicional do Azure AD](../conditional-access/overview.md) para exigir que os utilizadores autenticar com a autenticação multifator (MFA) ou início de sessão de rede fidedigna ou dispositivos. Durante o planeamento da implementação, descobrir que alguns desses requisitos não puderem ser alcançados por todos os utilizadores. Por exemplo, existem utilizadores que trabalham a partir de um escritório remoto que não faz parte de sua rede interna ou há um executivo de que utiliza um telefone antigo que não é suportado. A empresa precisar que estes utilizadores permissão para iniciar sessão e fazer seu trabalho, portanto, eles são excluídos das políticas de acesso condicional.

Como outro exemplo, poderá usar [localizações com nome](../conditional-access/location-condition.md) no acesso condicional para configurar um conjunto de condados e regiões a partir do qual não quiser permitir que os utilizadores para aceder ao seu inquilino.

![Localizações com nome](./media/conditional-access-exclusion/named-locations.png)

No entanto, em alguns casos, os utilizadores poderão ter um motivo plausível para iniciar sessão nestes países bloqueado. Por exemplo, os utilizadores podem estar em viagem por motivos pessoais ou de trabalho. Neste exemplo, a política de acesso condicional para bloquear esses países poderia ter um grupo de segurança da nuvem dedicada para os utilizadores que são excluídos da política. Os utilizadores que necessitam de aceder ao mesmo tempo em viagem, pode adicionar-se para o grupo com [gestão de grupo self-service do Azure AD](../users-groups-roles/groups-self-service-management.md).

Outro exemplo poderá ser que tem uma política de acesso condicional que [antigos de autenticação de blocos para a grande maioria dos seus utilizadores](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/07/azure-ad-conditional-access-support-for-blocking-legacy-auth-is-in-public-preview/). A Microsoft recomenda vivamente que bloqueia a utilização de protocolos legados no seu inquilino para melhorar a sua postura de segurança. No entanto, se tiver alguns utilizadores que precisam usar métodos antigos de autenticação para aceder aos recursos através do Office 2010 ou IMAP/SMTP/POP com base em clientes, em seguida, pode excluir esses usuários da política que bloquear métodos antigos de autenticação.

## <a name="why-are-exclusions-challenging"></a>Por que as exclusões são um desafio?

No Azure AD, pode definir o âmbito uma política de acesso condicional para um conjunto de utilizadores. Também pode excluir alguns desses usuários ao selecionar funções de diretório, utilizadores individuais ou convidados de utilizadores. É importante lembrar-se de que, quando essas exclusões são configuradas, a intenção de política não pode ser imposta para esses utilizadores. Se estas exclusões foram configuradas como uma lista de utilizadores individuais ou através de um grupo de segurança legados no local, em seguida, limita a visibilidade desta lista de exclusão (talvez não saibam os usuários de sua existência) e controle o administrador de TI sobre ele (os utilizadores podem associar o grupo de segurança para ignorar a política). Além disso, os utilizadores que qualificados para a exclusão de uma só vez podem já não precisar dele ou ser elegível para o mesmo.

No início de uma exclusão, há uma pequena lista de utilizadores que se ignorar a política. Ao longo do tempo, mais usuários são excluídos e lista cresce. Em algum momento, há a necessidade de consultar a lista e confirme que cada um destes utilizadores ainda deve ser excluída. Gerir a lista a partir de um ponto de vista técnico, pode ser relativamente fácil, mas quem toma as decisões de negócios e como se cria-se de que está tudo auditável?

No entanto, se configurar a exclusão para a política de acesso condicional a utilizar um grupo do Azure AD, em seguida, pode utilizar as revisões de acesso como um controle de compensação, a visibilidade da unidade e reduzir o número de utilizadores que têm uma exceção.

## <a name="how-to-create-an-exclusion-group-in-a-conditional-access-policy"></a>Como criar um grupo de exclusão de uma política de acesso condicional

Siga estes passos para criar um novo grupo do Azure AD e uma política de acesso condicional que não se aplica a esse grupo.

### <a name="create-an-exclusion-group"></a>Criar um grupo de exclusão

1. Inicie sessão no Portal do Azure.

1. No painel de navegação esquerdo, clique em **do Azure Active Directory** e, em seguida, clique em **grupos**.

1. No menu superior, clique em **novo grupo** para abrir o painel de grupo.

1. Na **tipo de grupo** lista, selecione **segurança**. Especifique um nome e descrição.

1. Certifique-se de definir o **associação** escreva para **atribuído**.

1. Selecione os utilizadores que devem fazer parte deste grupo de exclusão e, em seguida, clique em **criar**.

    ![Novo painel de grupo](./media/conditional-access-exclusion/new-group.png)

### <a name="create-a-conditional-access-policy-that-excludes-the-group"></a>Criar uma política de acesso condicional que exclui o grupo

Agora, pode criar uma política de acesso condicional que utiliza este grupo de exclusão.

1. No painel de navegação esquerdo, clique em **do Azure Active Directory** e, em seguida, clique em **acesso condicional** para abrir o **políticas** painel.

1. Clique em **nova política** para abrir o **New** painel.

1. Especifique um nome.

1. Clique em atribuições **utilizadores e grupos**.

1. Sobre o **inclusão** separador, selecione **todos os utilizadores**.

1. Sobre o **excluir** separador, adicione uma marca de verificação para **utilizadores e grupos** e, em seguida, clique em **selecionar utilizadores excluídos**.

1. Selecione o grupo de exclusão que criou.

    > [!NOTE]
    > Como melhor prática, é recomendado para excluir, pelo menos, uma conta de administrador da política durante o teste para se certificar de que não sejam bloqueados fora do seu inquilino.

1. Continue com a configuração de política de acesso condicional com base nos requisitos organizacionais.

    ![Selecionar utilizadores excluídos](./media/conditional-access-exclusion/select-excluded-users.png)

Vamos abordar dois exemplos em que pode utilizar as revisões de acesso para gerir exclusões nas políticas de acesso condicional.

## <a name="example-1-access-review-for-users-accessing-from-blocked-countries"></a>Exemplo 1: Revisão de acesso para usuários que acessam de países bloqueados

Digamos que tem uma política de acesso condicional que bloqueia o acesso de determinados países. Ele inclui um grupo que está excluído da política. Eis uma revisão de acesso recomendada, em que os membros do grupo são revistos.

> [!NOTE]
> Uma função de Administrador Global ou administrador de conta de utilizador é necessário para criar as revisões de acesso.

1. A revisão será ocorra novamente todas as semanas.

2. Nunca vai terminar para certificar-se de que a manter este grupo de exclusão mais atualizadas.

3. Todos os membros deste grupo serão-se no âmbito para a revisão.

4. Cada utilizador terá de Self-atestar que ainda precisam de ter acesso a partir destes países bloqueados, portanto, ainda precisam de ser membro do grupo.

5. Se o utilizador não responder à solicitação de revisão, eles serão automaticamente removidos do grupo e, portanto, já não consegue aceder ao inquilino quando estiverem viajando para estes países.

6. Ative as notificações de correio para que os utilizadores são notificados sobre o início e de conclusão da revisão de acesso.

    ![Criar uma revisão de acesso](./media/conditional-access-exclusion/create-access-review-1.png)

## <a name="example-2-access-review-for-users-accessing-with-legacy-authentication"></a>Exemplo 2: Revisão de acesso para usuários que acessam com a autenticação de legado

Digamos que tem uma política de acesso condicional que bloqueia o acesso para utilizadores que utilizam a autenticação e as versões mais antigas do cliente. Ele inclui um grupo que está excluído da política. Eis uma revisão de acesso recomendada, em que os membros do grupo são revistos.

1. Esta revisão têm de ser uma revisão periódica.

2. Todos os utilizadores no grupo têm de ser revisto.

3. Ele pode ser configurado para listar os proprietários de unidade de negócio como os revisores selecionados.

4. Aplicar automaticamente os resultados e remover utilizadores que não foram aprovados para continuar a utilizar métodos antigos de autenticação.

5. Poderá ser vantajoso ativar as recomendações para que os revisores de grupos grandes podem facilmente fazer suas decisões.

6. Ative as notificações de correio para que os utilizadores são notificados sobre o início e de conclusão da revisão de acesso.

    ![Criar uma revisão de acesso](./media/conditional-access-exclusion/create-access-review-2.png)

**Sugestão Pro**: Se tiver muitos grupos de exclusão e, por conseguinte, tem de criar várias revisões de acesso, agora temos uma API no ponto de extremidade do beta Microsoft Graph permite-lhe criar e gerir de forma programática. Para começar, consulte a [referência da API de revisões de acesso do Azure AD](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/accessreviews_root) e [revisões de exemplo de obtenção de acesso do Azure AD através do Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/td-p/236096).

## <a name="access-review-results-and-audit-logs"></a>Resultados de revisão de acesso e registos de auditoria

Agora que tem tudo no local, grupo, a política de acesso condicional e as revisões de acesso, chegou a hora para monitorizar e controlar os resultados dessas revisões.

1. No portal do Azure, abra a **as revisões de acesso** painel.

1. Abra o controle e um programa que criou para a gestão de grupo de exclusão.

1. Clique em **resultados** para ver o que foi aprovada para se manter na lista e que foi removido.

    ![Resultados de revisões de acesso](./media/conditional-access-exclusion/access-reviews-results.png)

1. Em seguida, clique em **registos de auditoria** para ver as ações que foram executadas durante esta revisão.

    ![Registos de auditoria de revisões de acesso](./media/conditional-access-exclusion/access-reviews-audit-logs.png)

Como um administrador de TI, sabe que gerir grupos de exclusão para as suas políticas, às vezes, é inevitável. No entanto, a manter estes grupos, revisar-los em intervalos regulares, o proprietário da empresa ou pelos utilizadores propriamente ditas e auditoria que estas alterações podem facilitada com acesso ao Azure AD revisões.

## <a name="next-steps"></a>Passos Seguintes

- [Criar uma revisão de acesso de grupos ou aplicações](create-access-review.md)
- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)
