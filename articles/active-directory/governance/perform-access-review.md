---
title: Rever o acesso a grupos & aplicações em avaliações de acesso - Azure AD
description: Saiba como rever o acesso dos membros do grupo ou o acesso à aplicação nas avaliações de acesso ao Diretório Ativo do Azure.
services: active-directory
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee4125e82dd5176f01de294011e22a1d66005094
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80128456"
---
# <a name="review-access-to-groups-and-applications-in-azure-ad-access-reviews"></a>Rever o acesso a grupos e aplicações nas avaliações de acesso à AD Azure

O Azure Ative Directory (Azure AD) simplifica a forma como as empresas gerem o acesso a grupos e aplicações em Azure AD e outros Serviços Online da Microsoft com uma funcionalidade chamada avaliações de acesso a AD Azure.

Este artigo descreve como um revisor designado realiza uma revisão de acesso para membros de um grupo ou utilizadores com acesso a uma aplicação.

## <a name="open-the-access-review"></a>Abra a revisão de acesso

O primeiro passo para realizar uma revisão de acesso é encontrar e abrir a revisão de acesso.

1. Procure um e-mail da Microsoft que lhe peça para rever o acesso. Aqui está um e-mail de exemplo para rever o acesso de um grupo.

    ![Email da Microsoft para rever o acesso a um grupo](./media/perform-access-review/access-review-email.png)

1. Clique no link **de revisão Iniciar** para abrir a revisão de acesso.

Se não tiver o e-mail, pode encontrar as suas avaliações de acesso pendentes seguindo estes passos.

1. Inscreva-se no portal [https://myapps.microsoft.com](https://myapps.microsoft.com)MyApps em .

    ![Aplicações de listagem de portais MyApps que tem permissões](./media/perform-access-review/myapps-access-panel.png)

1. No canto superior direito da página, clique no símbolo de utilizador, que mostra o seu nome e a sua organização predefinida. Se aparecer mais de uma organização, selecione aquela que pediu a revisão de acesso.

1. Clique no azulejo de comentários do **Access** para ver uma lista das avaliações de acesso pendentes.

    Se o mosaico não estiver visível, não há revisões de acesso a realizar nessa organização e não é necessária nenhuma ação nesse momento.

    ![Lista de avaliações de acesso pendentes para apps e grupos](./media/perform-access-review/access-reviews-list.png)

1. Clique no link **de revisão Iniciar** para a revisão de acesso que pretende realizar.

## <a name="perform-the-access-review"></a>Realizar a revisão de acesso

Uma vez aberta a revisão de acesso, vê os nomes dos utilizadores que precisam de ser revistos.

Se o pedido for para rever o seu próprio acesso, a página será diferente. Para mais informações, consulte [o acesso do Reexame a grupos ou aplicações.](review-your-access.md)

![Revisão de acesso aberto listando os utilizadores que precisam de ser revistos](./media/perform-access-review/perform-access-review.png)

Há duas formas de aprovar ou negar o acesso:

- Pode aprovar ou negar o acesso a um ou mais utilizadores, ou
- Pode aceitar as recomendações do sistema, que é a forma mais fácil e rápida.

### <a name="approve-or-deny-access-for-one-or-more-users"></a>Aprovar ou negar o acesso a um ou mais utilizadores

1. Reveja a lista de utilizadores para decidir se aprova ou nega o seu acesso continuado.

1. Para aprovar ou negar o acesso a um único utilizador, clique na linha para abrir uma janela para especificar a ação a tomar. Para aprovar ou negar o acesso a vários utilizadores, adicione marcas de verificação ao lado dos utilizadores e, em seguida, clique no botão **do utilizador(s) do Review X** para abrir uma janela para especificar a ação a tomar.

1. Clique em **Aprovar** ou **Negar**. Se não tiver a certeza, pode clicar **em Não saber**. Ao fazê-lo, o utilizador manterá o seu acesso, mas a seleção refletir-se-á nos registos de auditoria.

    ![Janela de ação que inclui Aprovar, Negar e Não Sei opções](./media/perform-access-review/approve-deny.png)

1. Se necessário, insira uma razão na caixa **Reason.**

    O administrador da revisão de acesso pode exigir que forneça uma razão para aprovar o acesso continuado ou a adesão ao grupo.

1. Depois de especificar a ação a tomar, clique em **Guardar**.

    Se quiser alterar a sua resposta, selecione a linha e atualize a resposta. Por exemplo, pode aprovar um utilizador previamente negado ou negar um utilizador previamente aprovado. Pode alterar a sua resposta a qualquer momento até que a revisão de acesso termine.

    Se houver vários revisores, a última resposta submetida é gravada. Considere um exemplo onde um administrador designa dois revisores - Alice e Bob. Alice abre a revisão de acesso primeiro e aprova o acesso. Antes do fim da revisão, Bob abre a revisão de acesso e nega o acesso. A última resposta de negação é o que é gravado.

    > [!NOTE]
    > Se um utilizador não tiver acesso, não é removido imediatamente. São removidos quando a revisão termina ou quando um administrador para a revisão.

### <a name="approve-or-deny-access-based-on-recommendations"></a>Aprovar ou negar o acesso com base em recomendações

Para tornar as avaliações de acesso mais fáceis e rápidas para si, também fornecemos recomendações que pode aceitar com um único clique. As recomendações são geradas com base na atividade de inscrição do utilizador.

1. Na barra azul na parte inferior da página, clique em **Aceitar recomendações.**

    ![Lista de revisão de acesso aberto mostrando o botão de recomendações Aceitar](./media/perform-access-review/accept-recommendations.png)

    Vê um resumo das ações recomendadas.

    ![Janela que exibe um resumo das ações recomendadas](./media/perform-access-review/accept-recommendations-summary.png)

1. Clique **em Ok** para aceitar as recomendações.

## <a name="next-steps"></a>Passos seguintes

- [Concluir uma revisão de acesso de grupos ou aplicações](complete-access-review.md)
