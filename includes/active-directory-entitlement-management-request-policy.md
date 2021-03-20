---
title: incluir ficheiro
description: incluir ficheiro
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 03/30/2020
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: 61858a478ead93b3709bb3e05c26335c9acbc24d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98697974"
---
## <a name="for-users-in-your-directory"></a>Para utilizadores no seu diretório

Siga estes passos se pretender permitir que os utilizadores do seu diretório possam solicitar este pacote de acesso. Ao definir a política de pedidos, pode especificar utilizadores individuais ou grupos de utilizadores mais comum. Por exemplo, a sua organização pode já ter um grupo como **Todos os colaboradores.**  Se esse grupo for adicionado na política para utilizadores que possam solicitar acesso, então qualquer membro desse grupo pode então solicitar acesso.

1. Nos **Utilizadores que podem solicitar a** secção de acesso, clique em Para **utilizadores no seu diretório**.

    Ao selecionar esta opção, novas opções parecem refinar ainda mais quem no seu diretório pode solicitar este pacote de acesso.

    ![Pacote de acesso - Pedidos - Para utilizadores no seu diretório](./media/active-directory-entitlement-management-request-policy/for-users-in-your-directory.png)

1. Selecione uma das seguintes opções:

    |  |  |
    | --- | --- |
    | **Utilizadores e grupos específicos** | Escolha esta opção se quiser apenas os utilizadores e grupos no seu diretório que especifique para poder solicitar este pacote de acesso. |
    | **Todos os membros (excluindo convidados)** | Escolha esta opção se quiser que todos os utilizadores membros do seu diretório possam solicitar este pacote de acesso. Esta opção não inclui quaisquer utilizadores convidados que possa ter convidado para o seu diretório. |
    | **Todos os utilizadores (incluindo convidados)** | Escolha esta opção se quiser que todos os utilizadores membros e utilizadores convidados do seu diretório possam solicitar este pacote de acesso. |

    Os utilizadores convidados referem-se a utilizadores externos que foram convidados para o seu diretório com [a Azure AD B2B](../articles/active-directory/external-identities/what-is-b2b.md). Para obter mais informações sobre as diferenças entre utilizadores membros e utilizadores [convidados, consulte quais são as permissões padrão do utilizador no Azure Ative Directory?](../articles/active-directory/fundamentals/users-default-permissions.md)

1. Se selecionar **utilizadores e grupos específicos,** clique em **Adicionar utilizadores e grupos**.

1. No painel de grupos Select, selecione os utilizadores e grupos que pretende adicionar.

    ![Pacote de acesso - Pedidos - Selecione utilizadores e grupos](./media/active-directory-entitlement-management-request-policy/select-users-groups.png)

1. Clique **em Selecionar** para adicionar os utilizadores e grupos.

1. Passe para a secção [de aprovação.](#approval)

## <a name="for-users-not-in-your-directory"></a>Para utilizadores que não estão no seu diretório

 **Os utilizadores que não estão no seu diretório** referem-se a utilizadores que se encontram noutro diretório ou domínio AZure AD. Estes utilizadores podem ainda não ter sido convidados para o seu diretório. Os diretórios AD da Azure devem ser configurados para permitir convites em **restrições de colaboração.** Para mais informações, consulte [a colaboração externa do B2B e gere quem pode convidar os hóspedes.](../articles/active-directory/external-identities/delegate-invitations.md)

> [!NOTE]
> Será criada uma conta de utilizador para um utilizador ainda não no seu diretório cujo pedido seja aprovado ou aprovado automaticamente. O convidado será convidado, mas não receberá um e-mail de convite. Em vez disso, receberão um e-mail quando a sua atribuição de pacote de acesso for entregue. Por padrão, mais tarde, quando esse utilizador convidado já não tiver quaisquer atribuições de pacotes de acesso, porque a sua última atribuição expirou ou foi cancelada, essa conta de utilizador do hóspede será bloqueada de iniciar sessão e posteriormente eliminada. Se pretender que os utilizadores convidados permaneçam indefinidamente no seu diretório, mesmo que não tenham atribuição de pacotes de acesso, pode alterar as definições para a sua configuração de gestão de direitos. Para obter mais informações sobre o objeto do utilizador convidado, consulte [propriedades de um utilizador de colaboração Azure Ative Directory B2B](../articles/active-directory/external-identities/user-properties.md).

Siga estes passos se pretender permitir que os utilizadores que não estão no seu diretório solicitem este pacote de acesso:

1. Nos **Utilizadores que podem solicitar a** secção de acesso, clique em Para **utilizadores que não estão no seu diretório**.

    Ao selecionar esta opção, surgem novas opções.

    ![Pacote de acesso - Pedidos - Para utilizadores que não estão no seu diretório](./media/active-directory-entitlement-management-request-policy/for-users-not-in-your-directory.png)

1. Selecione uma das seguintes opções:

    |  |  |
    | --- | --- |
    | **Organizações conectadas específicas** | Escolha esta opção se quiser selecionar a partir de uma lista de organizações que o seu administrador anteriormente acrescentou. Todos os utilizadores das organizações selecionadas podem solicitar este pacote de acesso. |
    | **Todas as organizações ligadas** | Escolha esta opção se todos os utilizadores de todas as suas organizações ligadas puderem solicitar este pacote de acesso. |
    | **Todos os utilizadores (Todas as organizações conectadas + quaisquer novos utilizadores externos)** | Escolha esta opção se todos os utilizadores de todas as suas organizações ligadas puderem solicitar este pacote de acesso e que as definições da lista B2B permitam ou neguem as definições da lista devem ter precedência para qualquer novo utilizador externo. |

    Uma organização conectada é um diretório ou domínio AD Azure externo com o qual você tem uma relação.

1. Se selecionou **organizações conectadas específicas,** clique em **Adicionar diretórios** para selecionar a partir de uma lista de organizações conectadas que o seu administrador anteriormente acrescentou.

1. Digite o nome ou o nome de domínio para procurar uma organização previamente ligada.

    ![Pacote de acesso - Pedidos - Selecione diretórios](./media/active-directory-entitlement-management-request-policy/select-directories.png)

    Se a organização com quem pretende colaborar não estiver na lista, pode pedir ao seu administrador que o adicione como uma organização conectada. Para obter mais informações, consulte [Adicionar uma organização conectada.](../articles/active-directory/governance/entitlement-management-organization.md)

1. Uma vez selecionadas todas as organizações ligadas, clique em **Select**.

    > [!NOTE]
    > Todos os utilizadores das organizações conectadas selecionadas poderão solicitar este pacote de acesso. Isto inclui utilizadores em Azure AD de todos os subdomínios associados à organização, a menos que esses domínios sejam bloqueados pela lista Azure B2B permitir ou negar. Para obter mais informações, consulte [Permitir ou bloquear convites a utilizadores B2B de organizações específicas.](../articles/active-directory/external-identities/allow-deny-list.md)

1. Passe para a secção [de aprovação.](#approval)

## <a name="none-administrator-direct-assignments-only"></a>Nenhum (apenas atribuições diretas de administrador)

Siga estes passos se pretender contornar os pedidos de acesso e permitir que os administradores atribuam diretamente utilizadores específicos a este pacote de acesso. Os utilizadores não terão de solicitar o pacote de acesso. Ainda é possível definir as definições do ciclo de vida, mas não existem definições de pedido.

1. Nos **Utilizadores que podem solicitar** a secção de acesso, clique em **Nenhum (apenas as atribuições diretas do administrador**.

    ![Pacote de acesso - Pedidos - Nenhum administrador atribui apenas atribuições diretas](./media/active-directory-entitlement-management-request-policy/none-admin-direct-assignments-only.png)

    Depois de criar o pacote de acesso, pode atribuir diretamente utilizadores internos e externos específicos ao pacote de acesso. Se especificar um utilizador externo, será criada uma conta de utilizador convidada no seu diretório. Para obter informações sobre a atribuição direta de um utilizador, consulte [Ver, adicionar e remover as atribuições de um pacote de acesso](../articles/active-directory/governance/entitlement-management-access-package-assignments.md).

1. Salte para baixo para a secção [de pedidos de ativação.](#enable-requests)

## <a name="approval"></a>Aprovação

Na secção aprovação, especifica se é necessária uma aprovação quando os utilizadores solicitam este pacote de acesso. As definições de aprovação funcionam da seguinte forma:

- Apenas um dos aprovadores ou aprovadores de recuo selecionados precisa de aprovar um pedido de aprovação de uma fase única. 
- Apenas um dos aprovadores selecionados de cada fase precisa de aprovar um pedido de aprovação em 2 fases.
- O aprovador pode ser um Gestor, Patrocinador Interno ou Patrocinador Externo dependendo de quem a política está a reger o acesso.
- A aprovação de todos os aprovadores selecionados não é necessária para aprovação individual ou de 2 estágios.
- A decisão de aprovação baseia-se em qualquer aprovação que reveja o pedido em primeiro lugar.

Para uma demonstração de como adicionar aprovadores a uma política de pedido, veja o seguinte vídeo:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4cZfg]

Para uma demonstração de como adicionar uma aprovação em várias fases a uma política de pedido, veja o seguinte vídeo:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4d1Jw]

Siga estas medidas para especificar as definições de aprovação dos pedidos para o pacote de acesso:

1. Para exigir aprovação para pedidos dos utilizadores selecionados, detete a **aprovação do Requerer** para **Sim**. Ou, para ter pedidos automaticamente aprovados, desaprote o toggle para **o nº**.

1. Para exigir que os utilizadores forneçam uma justificação para solicitar o pacote de acesso, desa estalem a **justificação do pedido de pedido** de aviso para **sim**.
    
1. Agora determine se os pedidos requerem aprovação única ou de 2 estágios. Desace o **Conjunto de Quantas etapas** para **1** para aprovação de uma fase única ou definir o toggle para **2** para aprovação de 2 estágios.

    ![Pacote de acesso - Pedidos - Definições de aprovação](./media/active-directory-entitlement-management-request-policy/approval.png)

Utilize os seguintes passos para adicionar aprovadores depois de selecionar quantas fases necessita: 

### <a name="single-stage-approval"></a>Aprovação de estágio único

1. Adicione o **Primeiro Aprovador:**
    
    Se a política estiver definida para reger o acesso dos utilizadores no seu diretório, pode selecionar **o Gestor como aprovador**. Ou, adicione um utilizador específico clicando em **Adicionar aprovadores** após a seleção Escolha os aprovadores específicos do menu suspenso.
    
    ![Pacote de acesso - Pedidos - Para utilizadores em diretório - Primeiro Aprovador](./media/active-directory-entitlement-management-request-policy/approval-single-stage-first-approver-manager.png)

    Se esta política estiver definida para reger o acesso aos utilizadores que não estão no seu **diretório,** pode selecionar patrocinador externo ou **patrocinador interno.** Ou, adicione um utilizador específico clicando em **Adicionar aprovadores** ou grupos em Escolha aprovadores específicos.
    
    ![Pacote de acesso - Pedidos - Para utilizadores fora do diretório - Primeiro Aprovador](./media/active-directory-entitlement-management-request-policy/out-directory-first-approver.png)
    
1. Se selecionou o **Manager** como o primeiro aprovador, clique em **Adicionar recuo** para selecionar um ou mais utilizadores ou grupos no seu diretório para ser um aprovador de recuo. Os aprovadores de recuo recebem o pedido se a gestão de direitos não encontrar o gestor para o utilizador que solicita o acesso.

    O gestor é encontrado pela gestão de direitos usando o atributo **Manager.** O atributo está no perfil do utilizador no Azure AD. Para obter mais informações, consulte [Adicionar ou atualizar as informações de perfil de um utilizador utilizando o Azure Ative Directory](../articles/active-directory/fundamentals/active-directory-users-profile-azure-portal.md).

1. Se selecionar **Escolha aprovadores específicos**, clique em **Adicionar aprovadores** para selecionar um ou mais utilizadores ou grupos no seu diretório para serem aprovadores.

1. Na caixa prevista **em decisão deve ser feita em quantos dias?**

    Se um pedido não for aprovado dentro deste período de tempo, será automaticamente negado. O utilizador terá de apresentar outro pedido para o pacote de acesso.

1. Para exigir que os aprovadores apresentem uma justificação para a sua decisão, detete a justificação do aprovador para **Sim**.

    A justificação é visível para outros aprovadores e para o solicitador.

### <a name="2-stage-approval"></a>Aprovação em 2 estágios

Se selecionar uma aprovação de 2 estágios, terá de adicionar um segundo aprovador.

1. Adicione o **Segundo Aprovador:** 
    
    Se os utilizadores estiverem no seu diretório, adicione um utilizador específico como o segundo aprovador clicando em **adicionar aprovadores** em Escolha aprovadores específicos.

    ![Pacote de acesso - Pedidos - Para utilizadores em diretório - Segundo Aprovação](./media/active-directory-entitlement-management-request-policy/in-directory-second-approver.png)

    Se os utilizadores não estiverem no seu diretório, selecione **patrocinador interno** ou **patrocinador externo** como segundo aprovador. Depois de selecionar o aprovador, adicione os aprovadores de recuo.

    ![Pacote de acesso - Pedidos - Para utilizadores fora do diretório - Segundo Aprovador](./media/active-directory-entitlement-management-request-policy/out-directory-second-approver.png) 

1. Especificar o número de dias que o segundo aprovante tem de aprovar o pedido na caixa sob **decisão deve ser feito em quantos dias?** 

1. Descode a justificação do aprovante requere para **sim** ou **não**.

### <a name="alternate-approvers"></a>Aprovadores alternativos

Pode especificar aprovadores alternativos, semelhantes a especificar os primeiros e segundos aprovadores que podem aprovar pedidos. Ter aprovadores alternativos ajudará a garantir que os pedidos são aprovados ou negados antes de expirarem (prazo). Pode listar os aprovadores alternativos o primeiro aprovador e o segundo aprovador para aprovação em 2 fases. 

Ao especificar os aprovadores alternativos, no caso de os primeiros ou segundos aprovadores não terem sido capazes de aprovar ou negar o pedido, o pedido pendente é reencaminhado para os aprovadores alternativos, de acordo com o calendário de encaminhamento especificado durante a configuração da apólice. Recebem um e-mail para aprovar ou negar o pedido pendente.

Após o pedido ser reencaminhado para os aprovadores suplentes, os primeiros ou segundos aprovadores podem ainda aprovar ou negar o pedido. Os aprovadores alternativos usam o mesmo site My Access para aprovar ou negar o pedido pendente.

Podemos listar pessoas ou grupos de pessoas para serem aprovadores e aprovadores alternativos. Certifique-se de que lista diferentes conjuntos de pessoas para serem os primeiros, segundos e aprovadores alternativos.
Por exemplo, se listasses Alice e Bob como os Primeiros Approvers, listasse Carol e Dave como os aprovadores alternativos. Utilize os seguintes passos para adicionar aprovadores alternativos a um pacote de acesso:

1. Sob o Primeiro Aprovante, Segundo Aprovador, ou ambos, clique em **Mostrar definições de pedidos avançados**.

    ![Pacote de acesso - Política - Mostrar definições de pedidos avançados](./media/active-directory-entitlement-management-request-policy/alternate-approvers-click-advanced-request.png)

1. Definir **Se não forem tomadas medidas, avançar para os aprovadores alternativos?** 

1. Clique **em Adicionar aprovadores alternativos** e selecione os aprovadores alternativos da lista.

    ![Pacote de acesso - Política - Adicionar Aprovadores Alternativos](./media/active-directory-entitlement-management-request-policy/alternate-approvers-add.png)

    Se selecionar o Manager como aprovador para o Primeiro Aprovação, terá uma opção adicional, **gestor de segundo nível como aprovador alternativo,** disponível para escolher no campo de aprovação alternativa. Se selecionar esta opção, tem de adicionar um aprovador de recuo para encaminhar o pedido para o caso de o sistema não encontrar o gestor de segundo nível.

1. No **Forward para os aprovadores alternativos após quantos dias** de caixa, colocar no número de dias os aprovadores têm de aprovar ou negar um pedido. Se nenhum aprovador tiver aprovado ou negado o pedido antes da duração do pedido, o pedido expira (prazo) e o utilizador terá de apresentar outro pedido para o pacote de acesso. 

    Os pedidos só podem ser reencaminhados para aprovadores alternativos um dia após a duração do pedido chegar à meia-vida, e a decisão do(s) principal aprovação tem de ser adiada ao fim de pelo menos 4 dias. Se o prazo de 100 pedido for inferior ou igual a 3, não há tempo suficiente para encaminhar o pedido para os aprovadores alternativos. Neste exemplo, a duração do pedido é de 14 dias. Assim, a duração do pedido atinge a meia-vida no dia 7. Portanto, o pedido não pode ser reencaminhado mais cedo do dia 8. Além disso, os pedidos não podem ser reencaminhados no último dia da duração do pedido. Assim, no exemplo, o último pedido pode ser reencaminhado é o dia 13.

## <a name="enable-requests"></a>Ativar pedidos

1. Se pretender que o pacote de acesso seja disponibilizado imediatamente para os utilizadores na política de pedidos a solicitar, mova o Enable toggle para **Sim**.

    Pode sempre aditá-lo no futuro depois de ter terminado de criar o pacote de acesso.

    Se selecionou **Nenhum (apenas atribuições diretas de administrador)** e definiu ativar para **Nº,** então os administradores não podem atribuir diretamente este pacote de acesso.

    ![Screenshot que mostra a opção de ativar novos pedidos e atribuições.](./media/active-directory-entitlement-management-request-policy/enable-requests.png)

1. Clique em **Seguinte**.

## <a name="add-requestor-information-preview-to-an-access-package"></a>Adicione informações do Solicitador (pré-visualização) a um pacote de acesso

1. Aceda ao **separador informações do Requestor** e clique no sub-separador **'Perguntas'.**
 
1. Digite o que pretende perguntar ao solicitador, também conhecido como a cadeia de visualização, para a pergunta na caixa **de perguntas.**

    ![Pacote de acesso - Política- Ativar a definição de informações do Requestor](./media/active-directory-entitlement-management-request-policy/add-requestor-info-question.png)

1. Se quiser adicionar as suas próprias opções de localização, clique em **adicionar localização**.
    1. Uma vez no **Add localizações para** painel de perguntas, selecione o **código de idioma** para o idioma em que está a localização da questão.
    1. No idioma que configura, digite a pergunta na caixa **de Texto Localizado.**
    1. Depois de ter adicionado todas as localizações necessárias, clique em **Guardar**.

    ![Pacote de acesso - Política-Configurar texto localizado](./media/active-directory-entitlement-management-request-policy/add-localization-question.png)

1. Selecione o **formato Resposta** no qual gostaria que os solicitadores respondessem. Os formatos de resposta incluem: *texto curto,* *escolha múltipla* e texto *longo*.
 
    ![Pacote de acesso - Política- Selecione a vista e edite o formato de resposta de escolha múltipla](./media/active-directory-entitlement-management-request-policy/answer-format-view-edit.png)
 
1. Se selecionar várias opções, clique no botão **de visualização e edite** para configurar as opções de resposta.
    1. Depois de selecionar a vista e editar o painel **de perguntas 'Visualizar/editar'** será aberto.
    1. Digite as opções de resposta que deseja dar ao solicitador ao responder à pergunta nas caixas **de valores de resposta.**
    1. Digite as respostas necessárias e clique em **Guardar**.
    
    ![Pacote de acesso - Política- Introduza várias opções de escolha](./media/active-directory-entitlement-management-request-policy/answer-multiple-choice.png)
  
1. Para exigir que os solicitadores respondam a esta pergunta ao solicitar o acesso a um pacote de acesso, clique na caixa de verificação em **requerido**.

1. Clique em Seguinte