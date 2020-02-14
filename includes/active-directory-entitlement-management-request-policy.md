---
title: incluir ficheiro
description: incluir ficheiro
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 11/11/2019
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: c73a62b2d5feeae42a5ea35c88073dd5fcc0d78c
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77193084"
---
## <a name="for-users-in-your-directory"></a>Para utilizadores no seu diretório

Siga estas etapas se pretender permitir que os utilizadores do seu diretório possam solicitar este pacote de acesso. Ao definir a política de pedido, pode especificar utilizadores individuais ou mais frequentemente grupos de utilizadores. Por exemplo, a sua organização pode já ter um grupo como **todos os colaboradores.**  Se esse grupo for adicionado na política para utilizadores que possam solicitar acesso, então qualquer membro desse grupo pode então solicitar acesso.

1. Nos **Utilizadores que podem solicitar** a secção de acesso, clique **para utilizadores no seu diretório**.

    Ao selecionar esta opção, novas opções parecem aperfeiçoar ainda mais quem no seu diretório pode solicitar este pacote de acesso.

    ![Pacote de acesso - Pedidos - Para utilizadores no seu diretório](./media/active-directory-entitlement-management-request-policy/for-users-in-your-directory.png)

1. Selecione uma das seguintes opções:

    |  |  |
    | --- | --- |
    | **Utilizadores e grupos específicos** | Escolha esta opção se pretender apenas os utilizadores e grupos no seu diretório que especifice para poder solicitar este pacote de acesso. |
    | **Todos os membros (excluindo convidados)** | Escolha esta opção se quiser que todos os utilizadores membros do seu diretório possam solicitar este pacote de acesso. Esta opção não inclui utilizadores convidados que possa ter convidado para o seu diretório. |
    | **Todos os utilizadores (incluindo convidados)** | Escolha esta opção se quiser que todos os utilizadores membros e utilizadores convidados do seu diretório possam solicitar este pacote de acesso. |

    Os utilizadores convidados referem-se a utilizadores externos que tenham sido convidados para o seu diretório com [o Azure AD B2B](../articles/active-directory/b2b/what-is-b2b.md). Para obter mais informações sobre as diferenças entre utilizadores membros e utilizadores convidados, consulte [as permissões padrão dos utilizadores no Diretório Ativo do Azure?](../articles/active-directory/fundamentals/users-default-permissions.md)

1. Se tiver selecionado **utilizadores e grupos Específicos,** clique em **Adicionar utilizadores e grupos**.

1. No painel De utilizadores e grupos Select, selecione os utilizadores e grupos que pretende adicionar.

    ![Pacote de acesso - Pedidos - Selecione utilizadores e grupos](./media/active-directory-entitlement-management-request-policy/select-users-groups.png)

1. Clique em **Selecionar** para adicionar os utilizadores e grupos.

1. Passe para a secção [de aprovação.](#approval)

## <a name="for-users-not-in-your-directory"></a>Para utilizadores que não estão no seu diretório

 **Os utilizadores que não estão no seu diretório** referem-se a utilizadores que se encontram noutro diretório ou domínio da AD Azure. Estes utilizadores podem ainda não ter sido convidados para o seu diretório. Os diretórios da AD Azure devem ser configurados para serem permitidos convites em restrições de **Colaboração**. Para mais informações, consulte a [colaboração externa Enable B2B e gerequem quem pode convidar os hóspedes.](../articles/active-directory/b2b/delegate-invitations.md)

> [!NOTE]
> Será criada uma conta de utilizador convidado para um utilizador que ainda não esteja no seu diretório cujo pedido seja aprovado ou aprovado automaticamente. O hóspede será convidado, mas não receberá um e-mail convidado. Em vez disso, receberão um e-mail quando a atribuição do pacote de acesso for entregue. Por predefinição, mais tarde, quando esse utilizador deixar de ter quaisquer atribuições de pacotede acesso, uma vez que a sua última atribuição expirou ou foi cancelada, essa conta de utilizador convidado será bloqueada do registo e posteriormente eliminada. Se pretender que os utilizadores de hóspedes permaneçam no seu diretório indefinidamente, mesmo que não tenham atribuição de pacotes de acesso, pode alterar as definições para a sua configuração de gestão de direitos. Para obter mais informações sobre o objeto do utilizador convidado, consulte [Propriedades de um utilizador de colaboração Azure Ative Directory B2B](../articles/active-directory/b2b/user-properties.md).

Siga estas etapas se pretender permitir que os utilizadores que não estão no seu diretório solicitem este pacote de acesso:

1. Nos **Utilizadores que podem solicitar** a secção de acesso, clique **para utilizadores que não estão no seu diretório**.

    Quando seleciona esta opção, surgem novas opções.

    ![Pacote de acesso - Pedidos - Para utilizadores que não estão no seu diretório](./media/active-directory-entitlement-management-request-policy/for-users-not-in-your-directory.png)

1. Selecione uma das seguintes opções:

    |  |  |
    | --- | --- |
    | **Organizações específicas ligadas** | Escolha esta opção se pretender selecionar de uma lista de organizações que o seu administrador adicionou anteriormente. Todos os utilizadores das organizações selecionadas podem solicitar este pacote de acesso. |
    | **Todas as organizações conectadas** | Escolha esta opção se todos os utilizadores de todas as suas organizações conectadas puderem solicitar este pacote de acesso. |
    | **Todos os utilizadores (Todas as organizações conectadas + quaisquer novos utilizadores externos)** | Escolha esta opção se todos os utilizadores de todas as suas organizações conectadas puderem solicitar este pacote de acesso e se o B2B permitir ou negar as definições da lista deve ter precedência para qualquer novo utilizador externo. |

    Uma organização conectada é um diretório ou domínio externo da AD Azure com o que tem uma relação.

1. Se selecionar **organizações específicas ligadas,** clique em **Adicionar diretórios** para selecionar a partir de uma lista de organizações conectadas que o seu administrador anteriormente acrescentou.

1. Digite o nome ou nome de domínio para procurar uma organização anteriormente ligada.

    ![Pacote de acesso - Pedidos - Selecione diretórios](./media/active-directory-entitlement-management-request-policy/select-directories.png)

    Se a organização com quem pretende colaborar não estiver na lista, pode pedir ao seu administrador que a adicione como uma organização conectada. Para mais informações, consulte [Adicionar uma organização conectada](../articles/active-directory/governance/entitlement-management-organization.md).

1. Depois de selecionar todas as suas organizações conectadas, clique em **Select**.

    > [!NOTE]
    > Todos os utilizadores das organizações conectadas selecionadas poderão solicitar este pacote de acesso. Isto inclui utilizadores em Azure AD de todos os subdomínios associados à organização, a menos que esses domínios sejam bloqueados pela lista de permitir ou negar do Azure B2B. Para mais informações, consulte [Permitir ou bloquear convites a utilizadores B2B de organizações específicas](../articles/active-directory/b2b/allow-deny-list.md).

1. Passe para a secção [de aprovação.](#approval)

## <a name="none-administrator-direct-assignments-only"></a>Nenhuma (apenas atribuições diretas de administrador)

Siga estas medidas se pretender contornar os pedidos de acesso e permitir que os administradores atribuam diretamente utilizadores específicos a este pacote de acesso. Os utilizadores não terão de solicitar o pacote de acesso. Ainda é possível definir as definições do ciclo de vida, mas não existem definições de pedido.

1. Nos **Utilizadores que podem solicitar** a secção de acesso, clique **em Nenhum (apenas em atribuições diretas**do administrador .

    ![Pacote de acesso - Pedidos - Nenhuma atribuição direta de administrador apenas](./media/active-directory-entitlement-management-request-policy/none-admin-direct-assignments-only.png)

    Depois de criar o pacote de acesso, pode atribuir diretamente utilizadores internos e externos específicos ao pacote de acesso. Se especificar um utilizador externo, será criada uma conta de utilizador convidado no seu diretório. Para obter informações sobre a atribuição direta de um utilizador, consulte ['Ver', adicionar e remover atribuições para um pacote](../articles/active-directory/governance/entitlement-management-access-package-assignments.md)de acesso .

1. Salte para a secção [de pedidos enable.](#enable-requests)

## <a name="approval"></a>Aprovação

Na secção aprovação, especifica se é necessária uma aprovação quando os utilizadores solicitam este pacote de acesso. As definições de aprovação funcionam da seguinte forma:

- Apenas um dos aprovadores selecionados ou aprovadores de recuos tem de aprovar um pedido de aprovação de uma fase única. 
- Apenas um dos aprovadores selecionados de cada fase precisa de aprovar um pedido de aprovação em duas fases.
- O aprovador pode ser um Gestor, patrocinador interno ou patrocinador externo dependendo de quem a política está a governar o acesso.
- A aprovação de todos os aprovadores selecionados não é necessária para aprovação de uma única ou duas fases.
- A decisão de aprovação baseia-se em qualquer aprovação que reveja primeiro o pedido.

Para uma demonstração de como adicionar aprovadores a uma política de pedido, veja o seguinte vídeo:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4cZfg]

Para uma demonstração de como adicionar uma aprovação em várias fases a uma política de pedidos, veja o seguinte vídeo:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4d1Jw]

Siga estas medidas para especificar as definições de aprovação para pedidos para o pacote de acesso:

1. Para exigir a aprovação dos pedidos dos utilizadores selecionados, detete o realterno de **aprovação Require** para **Sim**. Ou, para ter pedidos automaticamente aprovados, detete o toggle para **No**.

1. Para exigir que os utilizadores forneçam uma justificação para solicitar o pacote de acesso, detete o **pedido de justificação para** **sim**.
    
1. Agora determine se os pedidos requerem aprovação única ou de 2 estágios. Detete o **número de estágios** alternar para **1** para aprovação de uma fase única ou definir o toggle para **2** para aprovação em 2 fases.

    ![Pacote de acesso - Pedidos - Definições de aprovação](./media/active-directory-entitlement-management-request-policy/approval.png)

Utilize os seguintes passos para adicionar os aprovadores depois de selecionar quantas fases necessita: 

### <a name="single-stage-approval"></a>Aprovação de uma fase única

1. Adicione o **Primeiro Aprovador:**
    
    Se a política estiver definida para reger o acesso dos utilizadores no seu diretório, pode selecionar **o Manager como aprovador**. Ou, adicione um utilizador específico clicando em **Adicionar aprovadores** depois de selecionar Escolha aprovadores específicos do menu dropdown.
    
    ![Pacote de acesso - Pedidos - Para utilizadores em diretório - Primeiro Aprovador](./media/active-directory-entitlement-management-request-policy/approval-single-stage-first-approver-manager.png)

    Se esta política estiver definida para reger o acesso a utilizadores que não estão no seu diretório, pode selecionar **patrocinador externo** ou **patrocinador interno.** Ou, adicione um utilizador específico clicando em **Adicionar aprovadores** ou grupos sob a escolha de aprovadores específicos.
    
    ![Pacote de acesso - Pedidos - Para utilizadores fora do diretório - Primeiro Aprovador](./media/active-directory-entitlement-management-request-policy/out-directory-first-approver.png)
    
1. Se selecionou **o Manager** como o primeiro aprovador, clique em **Adicionar backback** para selecionar um ou mais utilizadores ou grupos no seu diretório para ser um aprovador de recuo. Os aprovadores de fallback recebem o pedido se a gestão de direitos não conseguir encontrar o gestor para o utilizador que solicita acesso.

    O gestor é encontrado pela gestão de direitos utilizando o atributo **do Gestor.** O atributo está no perfil do utilizador em Azure AD. Para mais informações, consulte [Adicionar ou atualizar as informações de perfil de um utilizador utilizando o Diretório Ativo do Azure](../articles/active-directory/fundamentals/active-directory-users-profile-azure-portal.md).

1. Se tiver selecionado **Escolher aprovadores específicos,** clique em **Adicionar aprovadores** para selecionar um ou mais utilizadores ou grupos no seu diretório para serem aprovadores.

1. Na caixa de **decisão deve ser feita em quantos dias?**

    Se um pedido não for aprovado dentro deste período de tempo, será automaticamente negado. O utilizador terá de apresentar outro pedido para o pacote de acesso.

1. Para exigir que os aprovadores forneçam uma justificação para a sua decisão, detetere exigir justificação para **sim**.

    A justificação é visível para outros aprovadores e para o solicitador.

### <a name="2-stage-approval-preview"></a>Aprovação em 2 fases (Pré-visualização)

Se selecionou uma aprovação de 2 fases, terá de adicionar um segundo aprovador.

1. Adicione o **Segundo Aprovador:** 
    
    Se os utilizadores estiverem no seu diretório, adicione um utilizador específico como segundo aprovador clicando em **Adicionar aprovadores** em 'Escolha', aprovadores específicos.

    ![Pacote de acesso - Pedidos - Para utilizadores em diretório - Segundo Aprovador](./media/active-directory-entitlement-management-request-policy/in-directory-second-approver.png)

    Se os utilizadores não estiverem no seu diretório, selecione **patrocinador interno** ou **patrocinador externo** como o segundo aprovador. Depois de selecionar o aprovador, adicione os aprovadores de recuo.

    ![Pacote de acesso - Pedidos - Para utilizadores fora do diretório - Segundo Aprovador](./media/active-directory-entitlement-management-request-policy/out-directory-second-approver.png) 

1. Especificar o número de dias em que o segundo aprovador tem de aprovar o pedido na caixa ao abrigo da **Decisão deve ser feito em quantos dias?** 

1. Defino a justificação do aprovador exigir alternar para **Sim** ou **Não**.

### <a name="alternate-approvers"></a>Aprovadores alternativos

Pode especificar aprovadores alternativos, semelhantes à especificação dos primeiros e segundos aprovadores que podem aprovar pedidos. Ter aprovadores alternativos ajudará a garantir que os pedidos são aprovados ou negados antes de expirarem (prazo de validade). Pode listar os aprovadores alternativos o primeiro aprovador e segundo aprovador para aprovação em duas fases. 

Especificando os aprovadores alternativos, no caso de os primeiros ou segundos aprovadores não terem podido aprovar ou negar o pedido, o pedido pendente é reencaminhado para os aprovadores alternativos, de acordo com o calendário de encaminhamento especificado durante a configuração da apólice. Recebem um e-mail para aprovar ou negar o pedido pendente.

Após o reexame do pedido aos aprovadores alternativos, os primeiros ou segundos aprovadores podem ainda aprovar ou negar o pedido. Os aprovadores alternativos usam o mesmo site My Access para aprovar ou negar o pedido pendente.

Podemos listar pessoas ou grupos de pessoas para serem aprovadores e aprovadores alternativos. Por favor, certifique-se de que lista diferentes conjuntos de pessoas para serem os primeiros, segundos e suplentes.
Por exemplo, se listasse alice e Bob como os Primeiros Aprovadores, listasse Carol e Dave como os aprovadores alternativos. Utilize os seguintes passos para adicionar aprovadores alternativos a um pacote de acesso:

1. No primeiro aprovador, segundo aprovador, ou ambos, clique em **Mostrar definições avançadas de pedido**.

    ![Pacote de acesso - Política - Mostrar definições avançadas de pedidos](./media/active-directory-entitlement-management-request-policy/alternate-approvers-click-advanced-request.png)

1. Definir **Se não forem tomadas medidas, avance para os aprovadores alternativos?**

1. Clique **em Adicionar aprovadores alternativos** e selecione os aprovadores alternativos da lista.

    ![Pacote de acesso - Política - Adicionar Aprovadores Alternativos](./media/active-directory-entitlement-management-request-policy/alternate-approvers-add.png)

1. No **Forward para alternar aprovadores(s) após quantos dias** de caixa, coloque no número de dias que os aprovadores têm de aprovar ou negar um pedido. Se nenhum aprovador tiver aprovado ou negado o pedido antes da duração do pedido, o pedido expira (timeout), e o utilizador terá de apresentar outro pedido para o pacote de acesso. 

    Os pedidos só podem ser encaminhados para aprovadores alternativos um dia após a duração do pedido chegar à meia-vida. Neste exemplo, a duração do pedido é de 14 dias. Assim, a duração do pedido atinge a meia-vida no dia 7. Então o pedido não pode ser reencaminhado antes do 8º dia. Além disso, os pedidos não podem ser reencaminhados no último dia da duração do pedido. Assim, no exemplo, o mais recente que o pedido pode ser reencaminhado é o dia 13.

## <a name="enable-requests"></a>Ativar pedidos

1. Se pretender que o pacote de acesso seja disponibilizado imediatamente para os utilizadores na política de pedido solicitar, mude o Toggle Enable para **Sim**.

    Pode sempre permitir no futuro depois de ter terminado de criar o pacote de acesso.

    Se selecionou **Nenhuma (apenas atribuições diretas** do administrador) e definiu o activato para **Não,** então os administradores não podem atribuir diretamente este pacote de acesso.

    ![Pacote de acesso - Política- Ativar a definição de políticas](./media/active-directory-entitlement-management-request-policy/enable-requests.png)

1. Clique em **Seguinte**.
