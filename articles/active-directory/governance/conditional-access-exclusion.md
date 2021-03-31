---
title: Gerir utilizadores excluídos das políticas de Acesso Condicional - Azure AD
description: Saiba como utilizar avaliações de acesso do Azure Ative (Azure AD) para gerir utilizadores que tenham sido excluídos das políticas de Acesso Condicional
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 426e28048ae370919529ea710717a3a3867d999d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97746257"
---
# <a name="use-azure-ad-access-reviews-to-manage-users-excluded-from-conditional-access-policies"></a>Utilize avaliações de acesso Azure AD para gerir utilizadores excluídos das políticas de acesso condicional

Num mundo ideal, todos os utilizadores seguem as políticas de acesso para garantir o acesso aos recursos da sua organização. No entanto, às vezes há casos de negócios que exigem que asse exceções. Este artigo passa por alguns exemplos de situações em que podem ser necessárias exclusões. Você, como administrador de TI, pode gerir esta tarefa, evitar a supervisão de exceções de política, e fornecer aos auditores comprovativo de que estas exceções são revistas regularmente utilizando revisões de acesso do Azure Ative Directory (Azure AD).

>[!NOTE]
> Um Azure AD Premium P2 válido, Mobilidade Empresarial + Segurança E5 paga, ou licença de julgamento é necessária para usar avaliações de acesso Azure AD. Para mais informações, consulte [as edições do Azure Ative Directory.](../fundamentals/active-directory-whatis.md)

## <a name="why-would-you-exclude-users-from-policies"></a>Por que excluiria os utilizadores das políticas?

Digamos que, como administrador, decide utilizar o [Acesso Condicionado Azure AD](../conditional-access/concept-conditional-access-policy-common.md) para exigir autenticação multi-factor (MFA) e limitar pedidos de autenticação a redes ou dispositivos específicos. Durante o planeamento da implementação, você percebe que nem todos os utilizadores podem cumprir estes requisitos. Por exemplo, pode ter utilizadores que trabalham a partir de escritórios remotos, e não parte da sua rede interna. Pode também ter de acomodar os utilizadores que se conectem utilizando dispositivos não suportados enquanto aguardam a substituição desses dispositivos. Em suma, a empresa precisa que estes utilizadores assinem e façam o seu trabalho para que os excluam das políticas de Acesso Condicional.

Como outro exemplo, você pode estar usando [localizações nomeadas](../conditional-access/location-condition.md) no Acesso Condicional para especificar um conjunto de países e regiões a partir dos quais você não quer permitir que os utilizadores acedam ao seu inquilino.

![Locais nomeados em Acesso Condicional](./media/conditional-access-exclusion/named-locations.png)

Infelizmente, alguns utilizadores podem ainda ter uma razão válida para se inscreverem nestes países/regiões bloqueados. Por exemplo, os utilizadores podem estar a viajar para trabalhar e precisam de aceder aos recursos corporativos. Neste caso, a política de acesso condicional para bloquear estes países/regiões poderia utilizar um grupo de segurança na nuvem para os utilizadores excluídos da política. Os utilizadores que precisam de acesso durante a viagem, podem adicionar-se ao grupo utilizando a [gestão do Grupo de Self-Service Azure AD](../enterprise-users/groups-self-service-management.md).

Outro exemplo pode ser o facto de ter uma política de Acesso Condicional [que bloqueia a autenticação de legados para a grande maioria dos seus utilizadores.](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/07/azure-ad-conditional-access-support-for-blocking-legacy-auth-is-in-public-preview/) No entanto, se tiver alguns utilizadores que necessitem de utilizar métodos de autenticação legado para aceder aos seus recursos através do Office 2010 ou clientes baseados no IMAP/SMTP/POP, então pode excluir estes utilizadores da política que bloqueia métodos de autenticação legado.

>[!NOTE]
>A Microsoft recomenda vivamente que bloqueie o uso de protocolos antigos no seu inquilino para melhorar a sua postura de segurança.

## <a name="why-are-exclusions-challenging"></a>Porque é que as exclusões são desafiantes?

Em Azure AD, pode estender uma política de Acesso Condicional a um conjunto de utilizadores. Também pode configurar exclusões selecionando funções AD AZure, utilizadores individuais ou hóspedes. Deve ter em mente que quando as exclusões são configuradas, a intenção política não pode ser aplicada aos utilizadores excluídos. Se as exclusões forem configuradas usando uma lista de utilizadores ou usando grupos de segurança legados no local, terá uma visibilidade limitada nas exclusões. Como resultado:

- Os utilizadores podem não saber que estão excluídos.

- Os utilizadores podem juntar-se ao grupo de segurança para contornar a política.

- Os utilizadores excluídos podem ter-se qualificado para a exclusão antes, mas podem deixar de se qualificar para a exclusão.

Frequentemente, quando se configura uma exclusão pela primeira vez, há uma shortlist de utilizadores que contornam a política. Com o tempo, cada vez mais utilizadores são adicionados à exclusão, e a lista cresce. Em algum momento, você precisa rever a lista e confirmar que cada um destes utilizadores ainda é elegível para exclusão. Gerir a lista de exclusão, do ponto de vista técnico, pode ser relativamente fácil, mas quem toma as decisões empresariais, e como se assegura que tudo é auditável? No entanto, se configurar a exclusão utilizando um grupo AZure AD, pode utilizar as avaliações de acesso como um controlo compensativo, para impulsionar a visibilidade e reduzir o número de utilizadores excluídos.

## <a name="how-to-create-an-exclusion-group-in-a-conditional-access-policy"></a>Como criar um grupo de exclusão numa política de acesso condicional

Siga estes passos para criar um novo grupo AZure AD e uma política de acesso condicional que não se aplica a esse grupo.

### <a name="create-an-exclusion-group"></a>Criar um grupo de exclusão

1. Inicie sessão no portal do Azure.

2. Na navegação à esquerda, clique em **Azure Ative Directory** e, em seguida, clique em **Grupos**.

3. No menu superior, clique em **New Group** para abrir o painel de grupo.

4. Na lista **de tipos do Grupo,** selecione **Security**. Especifique um nome e descrição.

5. Certifique-se de definir o tipo **de Membro** para **Atribuído**.

6. Selecione os utilizadores que devem fazer parte deste grupo de exclusão e, em seguida, clique em **Criar**.

![Novo painel de grupo em Azure Ative Directory](./media/conditional-access-exclusion/new-group.png)

### <a name="create-a-conditional-access-policy-that-excludes-the-group"></a>Criar uma política de acesso condicional que exclua o grupo

Agora pode criar uma política de Acesso Condicional que utilize este grupo de exclusão.

1. Na navegação à esquerda, clique em **Azure Ative Directory** e, em seguida, clique em **Acesso Condicional** para abrir a lâmina **Políticas.**

2. Clique **em Nova política** para abrir o Painel **Novo.**

3. Especifique um nome.

4. Em Atribuições clique em **Utilizadores e grupos**.

5. No **separador Incluir,** selecione **Todos os Utilizadores**.

6. No **separador Excluir,** adicione uma marca de verificação aos **Utilizadores e grupos e,** em seguida, clique **em Selecionar utilizadores excluídos**.

7. Selecione o grupo de exclusão que criou.

   > [!NOTE] 
   > Como uma boa prática, recomenda-se excluir pelo menos uma conta de administrador da apólice ao testar para se certificar de que não está trancado fora do seu inquilino.

8. Continue a configurar a política de Acesso Condicional com base nos seus requisitos organizacionais.

![Selecione painel de utilizadores excluídos no Acesso Condicional](./media/conditional-access-exclusion/select-excluded-users.png)
  
Vamos cobrir dois exemplos onde você pode usar comentários de acesso para gerir exclusões em políticas de Acesso Condicional.

## <a name="example-1-access-review-for-users-accessing-from-blocked-countriesregions"></a>Exemplo 1: Revisão do acesso dos utilizadores que acedam a países/regiões bloqueados

Digamos que tem uma política de acesso condicional que bloqueia o acesso de certos países/regiões. Inclui um grupo que está excluído da política. Aqui está uma revisão de acesso recomendada onde os membros do grupo são revistos.

> [!NOTE] 
> Um administrador global ou função de administrador de utilizador é necessário para criar revisões de acesso.

1. A revisão vai acontecer todas as semanas.

2. Nunca terminará para ter certeza de que mantém este grupo de exclusão mais atualizado.

3. Todos os membros deste grupo estarão no âmbito da revisão.

4. Cada utilizador terá de se auto-atestar que ainda precisa de ter acesso a partir destes países/regiões bloqueados, pelo que ainda tem de ser membro do grupo.

5. Se o utilizador não responder ao pedido de revisão, será automaticamente removido do grupo e deixará de ter acesso ao arrendatário durante a viagem a esses países/regiões.

6. Habilitar notificações de e-mail para informar os utilizadores sobre o início e a conclusão da revisão de acesso.

    ![Criar um painel de revisão de acesso, por exemplo, 1](./media/conditional-access-exclusion/create-access-review-1.png)

## <a name="example-2-access-review-for-users-accessing-with-legacy-authentication"></a>Exemplo 2: Revisão de acesso para utilizadores que acedam com autenticação legado

Digamos que tem uma política de Acesso Condicional que bloqueia o acesso dos utilizadores usando a autenticação antiga e versões de clientes mais antigas e inclui um grupo que está excluído da política. Aqui está uma revisão de acesso recomendada onde os membros do grupo são revistos.

1. Esta revisão teria de ser uma revisão recorrente.

2. Todos no grupo teriam de ser revistos.

3. Poderia ser configurado para listar os proprietários da unidade de negócio como os revisores selecionados.

4. Aplique automaticamente os resultados e remova os utilizadores que não tenham sido aprovados para continuar a utilizar métodos de autenticação legado.

5. Pode ser benéfico permitir recomendações para que os revisores de grandes grupos possam facilmente tomar as suas decisões.

6. Ativar notificações de correio para que os utilizadores sejam notificados sobre o início e conclusão da revisão de acesso.

    ![Criar um painel de revisão de acesso, por exemplo, 2](./media/conditional-access-exclusion/create-access-review-2.png)

>[!IMPORTANT] 
>Se tiver muitos grupos de exclusão e, portanto, precisar de criar várias avaliações de acesso, temos agora uma API no ponto final beta do Microsoft Graph que lhe permite criar e gerir programáticamente. Para começar, consulte o acesso a [AD Azure analisa referência API](/graph/api/resources/accessreviews-root?view=graph-rest-beta) e [Exemplo de recuperar comentários de acesso a AD Azure através do Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/td-p/236096).

## <a name="access-review-results-and-audit-logs"></a>Aceder aos resultados da revisão e aos registos de auditoria

Agora que tem tudo no lugar, política de grupo, acesso condicional e avaliações de acesso, é hora de monitorizar e acompanhar os resultados destas avaliações.

1. No portal Azure, abra a lâmina **de avaliação access.**

2. Abra o controlo e o programa que criou para gerir o grupo de exclusão.

3. Clique **em Resultados** para ver quem foi aprovado para permanecer na lista e quem foi removido.

    ![Os resultados das análises de acesso mostram quem foi aprovado](./media/conditional-access-exclusion/access-reviews-results.png)

4. Em seguida, clique em **registos de auditoria** para ver as ações que foram tomadas durante esta revisão.

    ![Aceder a comentários de auditoria regista ações de listagem](./media/conditional-access-exclusion/access-reviews-audit-logs.png)

Como administrador de TI, sabe que gerir grupos de exclusão às suas políticas é, por vezes, inevitável. No entanto, a manutenção destes grupos, a sua revisão regular pelo empresário ou pelos próprios utilizadores, e a auditoria destas alterações podem ser facilitadas com revisões de acesso a AD do Azure.

## <a name="next-steps"></a>Passos seguintes

- [Criar uma revisão de acesso de grupos ou aplicações](create-access-review.md)
- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)