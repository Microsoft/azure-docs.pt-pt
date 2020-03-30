---
title: Gerir utilizadores excluídos das políticas de Acesso Condicional - Azure AD
description: Saiba como utilizar as avaliações de acesso ao Azure Ative Directory (Azure AD) para gerir utilizadores que tenham sido excluídos das políticas de Acesso Condicional
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422716"
---
# <a name="use-azure-ad-access-reviews-to-manage-users-excluded-from-conditional-access-policies"></a>Utilize avaliações de acesso a Anúncio saqueadas para gerir utilizadores excluídos das políticas de Acesso Condicional

Num mundo ideal, todos os utilizadores seguiriam as polícias de acesso para garantir o acesso aos recursos da sua organização. No entanto, às vezes há casos de negócios que exigem que você alhe exceções. Este artigo descreve alguns exemplos em que podem ser necessárias exclusões e como você, como administrador de TI, pode gerir esta tarefa, evitar a supervisão das exceções políticas, e fornecer aos auditores a prova de que estas exceções são revistas regularmente usando o Azure Avaliações de acesso ao Ative Directory (Azure AD).

> [!NOTE]
> Uma licença de acesso Azure AD Premium P2, Enterprise Mobility + Security E5 paga, ou licença de teste é necessária para utilizar avaliações de acesso Azure AD. Para mais informações, consulte edições de [Diretório Ativo Azure.](../fundamentals/active-directory-whatis.md)

## <a name="why-would-you-exclude-users-from-policies"></a>Por que excluiria os utilizadores das políticas?

Como administrador de TI, poderá utilizar o [Acesso Condicional Azure AD](../conditional-access/overview.md) para exigir que os utilizadores autentiquem utilizando a autenticação de vários fatores (MFA) ou inscrevam-se numa rede ou dispositivo fidedignos. Durante o planeamento da implementação, você descobre que alguns destes requisitos não podem ser cumpridos por todos os utilizadores. Por exemplo, há utilizadores que trabalham a partir de um escritório remoto que não faz parte da sua rede interna ou há um executivo que usa um telefone antigo que não é suportado. A empresa exige que estes utilizadores sejam autorizados a inscreverem-se e a fazerem o seu trabalho, pelo que estão excluídos das políticas de Acesso Condicional.

Como outro exemplo, você pode usar [locais nomeados](../conditional-access/location-condition.md) em Acesso Condicional para configurar um conjunto de condados e regiões a partir dos quais você não quer permitir que os utilizadores acedam aos seus inquilinos.

![Locais nomeados em Acesso Condicional](./media/conditional-access-exclusion/named-locations.png)

No entanto, em alguns casos, os utilizadores podem ter uma razão legítima para se inscreverem a partir destes países/regiões bloqueados. Por exemplo, os utilizadores podem estar a viajar por motivos de trabalho ou pessoais. Neste exemplo, a política de acesso condicional para bloquear estes países/regiões poderia ter um grupo dedicado de segurança na nuvem para os utilizadores excluídos da política. Os utilizadores que precisam de acesso durante a viagem podem adicionar-se ao grupo utilizando a gestão do [Grupo de Autosserviço Azure AD.](../users-groups-roles/groups-self-service-management.md)

Outro exemplo pode ser que tenha uma política de Acesso Condicional que [bloqueie a autenticação do legado para a grande maioria dos seus utilizadores.](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/07/azure-ad-conditional-access-support-for-blocking-legacy-auth-is-in-public-preview/) A Microsoft recomenda vivamente que bloqueie o uso de protocolos legados no seu inquilino para melhorar a sua postura de segurança. No entanto, se tiver alguns utilizadores que precisam absolutamente de utilizar métodos de autenticação legado para aceder aos seus recursos através do Office 2010 ou do IMAP/SMTP/POP, então pode excluir estes utilizadores da política que bloqueia métodos de autenticação do legado.

## <a name="why-are-exclusions-challenging"></a>Por que as exclusões são desafiadoras?

Em Azure AD, pode definir uma política de Acesso Condicional a um conjunto de utilizadores. Também pode excluir alguns destes utilizadores selecionando funções de AD Azure, utilizadores individuais ou hóspedes de utilizadores. É importante lembrar que quando estas exclusões são configuradas, a intenção política não pode ser aplicada para esses utilizadores. Se estas exclusões foram configuradas como uma lista de utilizadores individuais ou através de um grupo de segurança no local, então limita a visibilidade desta lista de exclusão (os utilizadores podem não saber da sua existência) e o controlo do administrador de TI sobre ele (os utilizadores podem aderir ao grupo de segurança para passar a apólice). Além disso, os utilizadores que se qualificaram para a exclusão de uma só vez podem deixar de precisar ou ser elegíveis para o mesmo.

No início de uma exclusão, há uma pequena lista de utilizadores que ignoram a política. Com o tempo, cada vez mais utilizadores são excluídos e a lista cresce. A dada altura, é necessário rever a lista e confirmar que cada um destes utilizadores deve ainda ser excluído. Gerir a lista do ponto de vista técnico pode ser relativamente fácil, mas quem toma as decisões do negócio e como se certifica de que tudo é auditável?

No entanto, se configurar a exclusão à política de Acesso Condicional utilizando um grupo DeA Azure, então pode utilizar as avaliações de acesso como um controlo compensador, para impulsionar a visibilidade e reduzir o número de utilizadores que têm uma exceção.

## <a name="how-to-create-an-exclusion-group-in-a-conditional-access-policy"></a>Como criar um grupo de exclusão numa política de acesso condicional

Siga estes passos para criar um novo grupo De Azure AD e uma política de Acesso Condicional que não se aplique a esse grupo.

### <a name="create-an-exclusion-group"></a>Criar um grupo de exclusão

1. Inicie sessão no Portal do Azure.

1. Na navegação à esquerda, clique em **Azure Ative Directory** e, em seguida, clique em **Grupos**.

1. No menu superior, clique em **Novo Grupo** para abrir o painel de grupo.

1. Na lista do **tipo grupo,** selecione **Segurança**. Especifique um nome e descrição.

1. Certifique-se de que define o tipo **de Adesão** ao **Atribuído**.

1. Selecione os utilizadores que devem fazer parte deste grupo de exclusão e, em seguida, clique em **Criar**.

    ![Novo painel de grupo no Diretório Ativo do Azure](./media/conditional-access-exclusion/new-group.png)

### <a name="create-a-conditional-access-policy-that-excludes-the-group"></a>Criar uma política de Acesso Condicional que exclua o grupo

Agora pode criar uma política de Acesso Condicional que utilize este grupo de exclusão.

1. Na navegação à esquerda, clique em **Azure Ative Directory** e, em seguida, clique em **Acesso Condicional** para abrir a lâmina **Políticas.**

1. Clique em **Nova política** para abrir o **novo** painel.

1. Especifique um nome.

1. Em Atribuições clique em **Utilizadores e grupos**.

1. No separador **Incluir,** selecione **Todos os Utilizadores**.

1. No separador **Excluir,** adicione uma marca de verificação aos **Utilizadores e grupos** e, em seguida, clique em **Selecionar utilizadores excluídos**.

1. Selecione o grupo de exclusão que criou.

    > [!NOTE]
    > Como uma boa prática, recomenda-se excluir pelo menos uma conta de administrador da apólice ao testar para garantir que não está trancado fora do seu inquilino.

1. Continue a configurar a política de Acesso Condicional com base nos seus requisitos organizacionais.

    ![Selecione painel de utilizadores excluídos no Acesso Condicional](./media/conditional-access-exclusion/select-excluded-users.png)

Vamos cobrir dois exemplos em que pode usar avaliações de acesso para gerir exclusões nas políticas de Acesso Condicional.

## <a name="example-1-access-review-for-users-accessing-from-blocked-countriesregions"></a>Exemplo 1: Revisão do acesso aos utilizadores que acedem a partir de países/regiões bloqueados

Digamos que tem uma política de acesso condicional que bloqueia o acesso de certos países/regiões. Inclui um grupo que está excluído da política. Aqui está uma revisão de acesso recomendada onde os membros do grupo são revistos.

> [!NOTE]
> Um administrador global ou papel de administrador de utilizador é necessário para criar avaliações de acesso.

1. A revisão reocorrerá todas as semanas.

2. Nunca terminará para ter certeza de que mantém este grupo de exclusão o mais atualizado.

3. Todos os membros deste grupo estarão no âmbito da revisão.

4. Cada utilizador terá de se auto-atestar que ainda precisa de ter acesso a partir destes países/regiões bloqueados, pelo que ainda precisa de ser membro do grupo.

5. Se o utilizador não responder ao pedido de revisão, será automaticamente removido do grupo e, portanto, já não poderá aceder ao inquilino durante a viagem a esses países/regiões.

6. Ativar notificações de correio para que os utilizadores sejam notificados sobre o início e conclusão da revisão de acesso.

    ![Criar um painel de revisão de acesso, por exemplo 1](./media/conditional-access-exclusion/create-access-review-1.png)

## <a name="example-2-access-review-for-users-accessing-with-legacy-authentication"></a>Exemplo 2: Revisão de acesso para utilizadores que acedam com autenticação legado

Digamos que tem uma política de Acesso Condicional que bloqueia o acesso aos utilizadores usando a autenticação antiga e versões de clientes mais antigas. Inclui um grupo que está excluído da política. Aqui está uma revisão de acesso recomendada onde os membros do grupo são revistos.

1. Esta revisão teria de ser uma revisão recorrente.

2. Todos no grupo teriam de ser revistos.

3. Pode ser configurado para listar os proprietários da unidade de negócio como os revisores selecionados.

4. Aplique automaticamente os resultados e remova os utilizadores que não tenham sido aprovados para continuar a utilizar métodos de autenticação legado.

5. Pode ser benéfico permitir recomendações para que os revisores de grandes grupos possam facilmente tomar as suas decisões.

6. Ativar notificações de correio para que os utilizadores sejam notificados sobre o início e conclusão da revisão de acesso.

    ![Criar um painel de revisão de acesso, por exemplo, 2](./media/conditional-access-exclusion/create-access-review-2.png)

**Dica Pro**: Se tiver muitos grupos de exclusão e, portanto, precisar de criar múltiplas análises de acesso, temos agora uma API no ponto final beta do Microsoft Graph que lhe permite criá-los e geri-los programáticamente. Para começar, consulte o acesso da [AD Azure a referência sílpse](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/accessreviews_root) a api e [exemplo de recuperação de avaliações de acesso a AD Azure via Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/td-p/236096).

## <a name="access-review-results-and-audit-logs"></a>Resultados de revisão de acesso e registos de auditoria

Agora que tem tudo em prática, grupo, política de acesso condicional e avaliações de acesso, é hora de monitorizar e acompanhar os resultados destas avaliações.

1. No portal Azure, abra a lâmina de comentários de **acesso.**

1. Abra o controlo e o programa que criou para gerir o grupo de exclusão.

1. Clique em **Resultados** para ver quem foi aprovado para ficar na lista e quem foi removido.

    ![Resultados das avaliações de acesso mostram quem foi aprovado](./media/conditional-access-exclusion/access-reviews-results.png)

1. Em seguida, clique em **registos de auditoria** para ver as ações que foram tomadas durante esta revisão.

    ![Acesso avalia ações de listagem de registos de auditoria](./media/conditional-access-exclusion/access-reviews-audit-logs.png)

Como administrador de TI, sabe que gerir grupos de exclusão às suas políticas é, por vezes, inevitável. No entanto, a manutenção destes grupos, a sua revisão regular pelo empresário ou pelos próprios utilizadores, e a auditoria destas alterações podem facilitar com as avaliações de acesso da Azure AD.

## <a name="next-steps"></a>Passos seguintes

- [Criar uma revisão de acesso de grupos ou aplicações](create-access-review.md)
- [O que é o Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)
