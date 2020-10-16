---
title: Alterar as definições de aprovação para um pacote de acesso na gestão de direitos Azure AD - Azure Ative Directory
description: Saiba como alterar as definições de aprovação e de informação solicitadora para um pacote de acesso na gestão de direitos do Azure Ative Directory.
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
ms.openlocfilehash: 2e36b2d4576b43544bec89efd326363344b35be9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90997087"
---
# <a name="change-approval-and-requestor-information-preview-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Alterar definições de aprovação e informações solicitadoras (pré-visualização) para um pacote de acesso na gestão de direitos AD da Azure

Como gestor de pacotes de acesso, pode alterar as definições de aprovação e solicitação de informação para um pacote de acesso a qualquer momento, editando uma política existente ou adicionando uma nova política.

Este artigo descreve como alterar as definições de aprovação e solicitação de informações para um pacote de acesso existente.

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


## <a name="change-approval-settings-of-an-existing-access-package"></a>Alterar as definições de aprovação de um pacote de acesso existente

Siga estas medidas para especificar as definições de aprovação dos pedidos para o pacote de acesso:

**Papel pré-requisito:** Administrador global, administrador de utilizador, proprietário de catálogo ou gestor de pacotes access

1. No portal Azure, clique no **Diretório Ativo Azure** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, clique em **pacotes de Acesso** e, em seguida, abra o pacote de acesso.

1. Ou selecione uma política para editar ou adicione uma nova política ao pacote de acesso
    1. Clique em **Políticas** e, em seguida, **Adicione a política** se quiser criar uma nova política.
    1. Clique na política que pretende editar e, em seguida, clique em **editar**.

1. Aceda ao **separador Pedido.**

1. Para exigir aprovação para pedidos dos utilizadores selecionados, detete a **aprovação do Requerer** para **Sim**. Ou, para ter pedidos automaticamente aprovados, desaprote o toggle para **o nº**.

1. Para exigir que os utilizadores forneçam uma justificação para solicitar o pacote de acesso, desa estalem a **justificação do pedido de pedido** de aviso para **sim**.
    
1. Agora determine se os pedidos requerem aprovação única ou de 2 estágios. Desace o **Conjunto de Quantas etapas** para **1** para aprovação de uma fase única ou definir o toggle para **2** para aprovação de 2 estágios.

    ![Pacote de acesso - Pedidos - Definições de aprovação](./media/entitlement-management-access-package-approval-policy/approval.png)


Utilize os seguintes passos para adicionar aprovadores depois de selecionar quantas fases necessita: 

### <a name="single-stage-approval"></a>Aprovação de estágio único

1. Adicione o **Primeiro Aprovador:**
    
    Se a política estiver definida para reger o acesso dos utilizadores no seu diretório, pode selecionar **o Gestor como aprovador**. Ou, adicione um utilizador específico clicando em **Adicionar aprovadores** após a seleção Escolha os aprovadores específicos do menu suspenso.
    
    ![Pacote de acesso - Pedidos - Para utilizadores em diretório - Primeiro Aprovador](./media/entitlement-management-access-package-approval-policy/approval-single-stage-first-approver-manager.png)

    Se esta política estiver definida para reger o acesso aos utilizadores que não estão no seu **diretório,** pode selecionar patrocinador externo ou **patrocinador interno.** Ou, adicione um utilizador específico clicando em **Adicionar aprovadores** ou grupos em Escolha aprovadores específicos.
    
    ![Pacote de acesso - Pedidos - Para utilizadores fora do diretório - Primeiro Aprovador](./media/entitlement-management-access-package-approval-policy/out-directory-first-approver.png)
    
1. Se selecionou o **Manager** como o primeiro aprovador, clique em **Adicionar recuo** para selecionar um ou mais utilizadores ou grupos no seu diretório para ser um aprovador de recuo. Os aprovadores de recuo recebem o pedido se a gestão de direitos não encontrar o gestor para o utilizador que solicita o acesso.

    O gestor é encontrado pela gestão de direitos usando o atributo **Manager.** O atributo está no perfil do utilizador no Azure AD. Para obter mais informações, consulte [Adicionar ou atualizar as informações de perfil de um utilizador utilizando o Azure Ative Directory](../fundamentals/active-directory-users-profile-azure-portal.md).

1. Se selecionar **Escolha aprovadores específicos**, clique em **Adicionar aprovadores** para selecionar um ou mais utilizadores ou grupos no seu diretório para serem aprovadores.

1. Na caixa prevista **em decisão deve ser feita em quantos dias?**

    Se um pedido não for aprovado dentro deste período de tempo, será automaticamente negado. O utilizador terá de apresentar outro pedido para o pacote de acesso.

1. Para exigir que os aprovadores apresentem uma justificação para a sua decisão, detete a justificação do aprovador para **Sim**.

    A justificação é visível para outros aprovadores e para o solicitador.

### <a name="2-stage-approval"></a>Aprovação em 2 estágios

Se selecionar uma aprovação de 2 estágios, terá de adicionar um segundo aprovador.

1. Adicione o **Segundo Aprovador:** 
    
    Se os utilizadores estiverem no seu diretório, adicione um utilizador específico como o segundo aprovador clicando em **adicionar aprovadores** em Escolha aprovadores específicos.

    ![Pacote de acesso - Pedidos - Para utilizadores em diretório - Segundo Aprovação](./media/entitlement-management-access-package-approval-policy/in-directory-second-approver.png)

    Se os utilizadores não estiverem no seu diretório, selecione **patrocinador interno** ou **patrocinador externo** como segundo aprovador. Depois de selecionar o aprovador, adicione os aprovadores de recuo.

    ![Pacote de acesso - Pedidos - Para utilizadores fora do diretório - Segundo Aprovador](./media/entitlement-management-access-package-approval-policy/out-directory-second-approver.png) 

1. Especificar o número de dias que o segundo aprovante tem de aprovar o pedido na caixa sob **decisão deve ser feito em quantos dias?** 

1. Descode a justificação do aprovante requere para **sim** ou **não**.

### <a name="alternate-approvers"></a>Aprovadores alternativos

Pode especificar aprovadores alternativos, semelhantes a especificar os primeiros e segundos aprovadores que podem aprovar pedidos. Ter aprovadores alternativos ajudará a garantir que os pedidos são aprovados ou negados antes de expirarem (prazo). Pode listar os aprovadores alternativos o primeiro aprovador e o segundo aprovador para aprovação em 2 fases. 

Ao especificar os aprovadores alternativos, no caso de os primeiros ou segundos aprovadores não terem sido capazes de aprovar ou negar o pedido, o pedido pendente é reencaminhado para os aprovadores alternativos, de acordo com o calendário de encaminhamento especificado durante a configuração da apólice. Recebem um e-mail para aprovar ou negar o pedido pendente.

Após o pedido ser reencaminhado para os aprovadores suplentes, os primeiros ou segundos aprovadores podem ainda aprovar ou negar o pedido. Os aprovadores alternativos usam o mesmo site My Access para aprovar ou negar o pedido pendente.

Podemos listar pessoas ou grupos de pessoas para serem aprovadores e aprovadores alternativos. Certifique-se de que lista diferentes conjuntos de pessoas para serem os primeiros, segundos e aprovadores alternativos.
Por exemplo, se listasses Alice e Bob como os Primeiros Approvers, listasse Carol e Dave como os aprovadores alternativos. Utilize os seguintes passos para adicionar aprovadores alternativos a um pacote de acesso:

1. Sob o Primeiro Aprovante, Segundo Aprovador, ou ambos, clique em **Mostrar definições de pedidos avançados**.

    ![Pacote de acesso - Política - Mostrar definições de pedidos avançados](./media/entitlement-management-access-package-approval-policy/alternate-approvers-click-advanced-request.png)

1. Definir **Se não forem tomadas medidas, avançar para os aprovadores alternativos?** **Yes**

1. Clique **em Adicionar aprovadores alternativos** e selecione os aprovadores alternativos da lista.

    ![Pacote de acesso - Política - Adicionar Aprovadores Alternativos](./media/entitlement-management-access-package-approval-policy/alternate-approvers-add.png)

1. No **Forward para os aprovadores alternativos após quantos dias** de caixa, colocar no número de dias os aprovadores têm de aprovar ou negar um pedido. Se nenhum aprovador tiver aprovado ou negado o pedido antes da duração do pedido, o pedido expira (prazo) e o utilizador terá de apresentar outro pedido para o pacote de acesso. 

    Os pedidos só podem ser reencaminhados para aprovadores alternativos um dia após a duração do pedido chegar à meia-vida, e a decisão do(s) principal aprovação tem de ser adiada ao fim de pelo menos 4 dias. Se o prazo de 100 pedido for inferior ou igual a 3, não há tempo suficiente para encaminhar o pedido para os aprovadores alternativos. Neste exemplo, a duração do pedido é de 14 dias. Assim, a duração do pedido atinge a meia-vida no dia 7. Portanto, o pedido não pode ser reencaminhado mais cedo do dia 8. Além disso, os pedidos não podem ser reencaminhados no último dia da duração do pedido. Assim, no exemplo, o último pedido pode ser reencaminhado é o dia 13.

## <a name="enable-requests"></a>Ativar pedidos

1. Se pretender que o pacote de acesso seja disponibilizado imediatamente para os utilizadores na política de pedidos a solicitar, mova o Enable toggle para **Sim**.

    Pode sempre aditá-lo no futuro depois de ter terminado de criar o pacote de acesso.

    Se selecionou **Nenhum (apenas atribuições diretas de administrador)** e definiu ativar para **Nº,** então os administradores não podem atribuir diretamente este pacote de acesso.

    ![Pacote de acesso - Política- Permitir a definição de políticas](./media/entitlement-management-access-package-approval-policy/enable-requests.png)

1. Clique em **Seguinte**.

## <a name="collect-additional-requestor-information-for-approval-preview"></a>Recolher informações adicionais do solicitador para aprovação (pré-visualização)

Para garantir que os utilizadores estão a ter acesso aos pacotes de acesso certos, pode exigir que os solicitadores respondam a questões de texto personalizadas ou a perguntas de escolha múltipla no momento do pedido. Há um limite de 20 perguntas por política e um limite de 25 respostas para perguntas de escolha múltipla. As perguntas serão então apresentadas aos aprovadores para ajudá-los a tomar uma decisão.

1. Aceda ao **separador informações do Requestor** e clique no sub-separador **'Perguntas'.**
 
1. Digite o que pretende perguntar ao solicitador, também conhecido como a cadeia de visualização, para a pergunta na caixa **de perguntas.**

    ![Pacote de acesso - Política- Ativar a definição de informações do Requestor](./media/entitlement-management-access-package-approval-policy/add-requestor-info-question.png)

1. Se a comunidade de utilizadores que precisarão de acesso ao pacote de acesso não tiver todos uma língua preferida comum, então pode melhorar a experiência para os utilizadores que solicitam acesso no myaccess.microsoft.com. Para melhorar a experiência, pode fornecer cordas de exibição alternativas para diferentes idiomas. Por exemplo, se o navegador web de um utilizador estiver definido para espanhol, e tiver as cordas de exibição espanhola configuradas, então essas cordas serão exibidas ao utilizador que solicita. Para configurar a localização para pedidos, clique **em adicionar localização**.
    1. Uma vez no **Add localizações para** painel de perguntas, selecione o **código de idioma** para o idioma em que está a localização da questão.
    1. No idioma que configura, digite a pergunta na caixa **de Texto Localizado.**
    1. Depois de ter adicionado todas as localizações necessárias, clique em **Guardar**.

    ![Pacote de acesso - Política-Configurar texto localizado](./media/entitlement-management-access-package-approval-policy/add-localization-question.png)

1. Selecione o **formato Resposta** no qual gostaria que os solicitadores respondessem. Os formatos de resposta incluem: *texto curto,* *escolha múltipla*e texto *longo*.
 
    ![Pacote de acesso - Política- Selecione a vista e edite o formato de resposta de escolha múltipla](./media/entitlement-management-access-package-approval-policy/answer-format-view-edit.png)
 
1. Se selecionar várias opções, clique no botão **de visualização e edite** para configurar as opções de resposta.
    1. Depois de selecionar a vista e editar o painel **de perguntas 'Visualizar/editar'** será aberto.
    1. Digite as opções de resposta que deseja dar ao solicitador ao responder à pergunta nas caixas **de valores de resposta.**
    1. Digite as respostas necessárias e clique em **Guardar**.
    
    ![Pacote de acesso - Política- Introduza várias opções de escolha](./media/entitlement-management-access-package-approval-policy/answer-multiple-choice.png)
  
1. Para exigir que os solicitadores respondam a esta pergunta ao solicitar o acesso a um pacote de acesso, clique na caixa de verificação em **requerido**.

1. Preencha os separadores restantes (por exemplo, Ciclo de Vida) com base nas suas necessidades.

Depois de configurar informações solicitadas na sua política de pacotes de acesso, pode visualizar as respostas do solicitador às perguntas. Para obter orientações sobre a visualização de informações [solicitadas, consulte as respostas do solicitador às perguntas (Pré-visualização)](entitlement-management-request-approve.md#view-requestors-answers-to-questions-preview).

## <a name="next-steps"></a>Passos seguintes
- [Alterar as definições do ciclo de vida para um pacote de acesso](entitlement-management-access-package-lifecycle-policy.md)
- [Ver pedidos de um pacote de acesso](entitlement-management-access-package-requests.md)
