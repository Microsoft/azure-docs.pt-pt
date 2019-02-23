---
title: Rever o acesso a grupos ou aplicações nas revisões de acesso do Azure AD | Documentos da Microsoft
description: Saiba como rever o acesso dos membros do grupo ou de acesso de aplicação nas revisões de acesso do Azure Active Directory.
services: active-directory
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 02/20/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 097d230e919e6d4b56e6c677364610bda6630f75
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56728391"
---
# <a name="review-access-to-groups-or-applications-in-azure-ad-access-reviews"></a>Rever o acesso a grupos ou aplicações nas revisões de acesso do Azure AD

Azure Active Directory (Azure AD) simplifica como as empresas a gerir o acesso a grupos e aplicações no Azure AD e outros serviços Online da Microsoft com um recurso chamado revisões de acesso do Azure AD.

Este artigo descreve como um revisor designado executa uma revisão de acesso para membros de um grupo ou os utilizadores com acesso a uma aplicação.

## <a name="open-the-access-review"></a>Abra a revisão de acesso

É o primeiro passo para realizar uma revisão de acesso encontrar e abrir a revisão de acesso.

1. Procure uma mensagem de e-mail da Microsoft que lhe pede para rever o acesso. Eis um e-mail de exemplo para rever o acesso a um grupo.

    ![E-mail de acesso de revisão](./media/perform-access-review/access-review-email.png)

1. Clique nas **iniciar revisão** link para abrir a revisão de acesso.

Se não tiver o e-mail, pode encontrar o que seu acesso pendente revisões seguindo estes passos.

1. Inicie sessão no portal do My Apps em [ https://myapps.microsoft.com ](https://myapps.microsoft.com).

    ![MyApps portal](./media/perform-access-review/myapps-access-panel.png)

1. No canto superior direito da página, clique no símbolo de utilizador, que exibe a sua organização nome e a predefinição. Se mais do que uma organização estiver listada, selecione a organização que solicitou uma revisão de acesso.

1. No lado direito da página, clique nas **as revisões de acesso** mosaico para ver uma lista das revisões de acesso pendente.

    Se o mosaico não estiver visível, existem não existem revisões de acesso para efetuar dessa organização e é necessária nenhuma ação neste momento.

    ![Lista de revisões de acesso](./media/perform-access-review/access-reviews-list.png)

1. Clique nas **iniciar revisão** ligação para a revisão de acesso que pretende efetuar.

## <a name="perform-the-access-review"></a>Realizar a revisão de acesso

Ao abrir a revisão de acesso, verá os nomes de utilizadores que precisam ser examinadas.

Se o pedido rever o seu próprio acesso, a página terá uma aparência diferente. Para obter mais informações, consulte [rever acesso para si próprio para grupos ou aplicações](review-your-access.md).

![Executar revisão de acesso](./media/perform-access-review/perform-access-review.png)

Existem duas formas que pode aprovar ou negar o acesso:

- Pode aprovar ou recusar cada solicitação individualmente, ou
- Pode aceitar as recomendações de sistema, que é a forma mais fácil e rápida.

### <a name="approve-or-deny-access-for-each-request"></a>Aprovar ou negar o acesso de cada pedido

1. Reveja a lista de utilizadores para decidir se deve aprovar ou negar o acesso contínuo.

1. Para aprovar ou recusar cada pedido, clique na linha para abrir a janela para especificar a ação a tomar.

1. Clique em **aprovar** ou **negar**. Se não souber, pode clicar em **não sabe**. Se o fizer, irá resultar no utilizador mantém o seu acesso, mas a seleção será refletida nos registos de auditoria.

    ![Executar revisão de acesso](./media/perform-access-review/approve-deny.png)

    O administrador da revisão de acesso poderá exigir que forneça um motivo para aprovação de acesso contínuo ou associação de grupo.

1. Depois de especificar a ação a tomar, clique em **guardar**.

    Se pretender alterar a sua resposta, selecione a linha e atualize a resposta. Por exemplo, pode aprovar um utilizador anteriormente recusado ou negar um utilizador que se aprovado anteriormente. Pode alterar a sua resposta em qualquer altura até a revisão de acesso terminou.

    Se existirem vários revisores, a última resposta submetida é registrada. Considere um exemplo em que um administrador designa dois revisores – Alice e Bob. Alice é aberta a revisão de acesso pela primeira vez e aprova o acesso. Antes da revisão terminar, o João abre-se a revisão de acesso e nega o acesso. A última negar a resposta é o que é registrado.

    > [!NOTE]
    > Se um utilizador é negado o acesso, eles não são removidos imediatamente. Eles são removidos quando a revisão terminou ou quando um administrador para a revisão.

### <a name="approve-or-deny-access-based-on-recommendations"></a>Aprovar ou negar o acesso com base nas recomendações

Para tornar as revisões de acesso mais fácil e rápida para, também fornecemos recomendações que pode aceitar com um único clique. As recomendações são geradas com base na atividade de início de sessão dos utilizadores.

1. Na barra azul na parte inferior da página, clique em **aceitar recomendações**.

    ![Aceitar recomendações](./media/perform-access-review/accept-recommendations.png)

    Verá um resumo das ações recomendadas.

    ![Aceitar recomendações de resumidas](./media/perform-access-review/accept-recommendations-summary.png)

1. Clique em **Ok** para aceitar as recomendações.

## <a name="next-steps"></a>Passos Seguintes

- [Concluir uma revisão de acesso de grupos ou aplicações](complete-access-review.md)
