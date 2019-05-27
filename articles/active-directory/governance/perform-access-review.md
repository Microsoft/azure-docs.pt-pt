---
title: Rever o acesso a grupos ou aplicações nas revisões de acesso - Azure Active Directory | Documentos da Microsoft
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
ms.date: 05/21/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6cd5bbba681acaa0c32e681f7cb4809142fe11f9
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66113247"
---
# <a name="review-access-to-groups-or-applications-in-azure-ad-access-reviews"></a>Rever o acesso a grupos ou as revisões de acesso a aplicações no Azure AD

Azure Active Directory (Azure AD) simplifica como as empresas a gerir o acesso a grupos e aplicações no Azure AD e revisões de outros serviços Online da Microsoft com um recurso chamado acesso do Azure AD.

Este artigo descreve como um revisor designado executa uma revisão de acesso para membros de um grupo ou os utilizadores com acesso a uma aplicação.

## <a name="prerequisites"></a>Pré-requisitos

- Azure AD Premium P2

Para obter mais informações, consulte [os utilizadores que têm de ter licenças?](access-reviews-overview.md#which-users-must-have-licenses).

## <a name="open-the-access-review"></a>Abra a revisão de acesso

É o primeiro passo para realizar uma revisão de acesso encontrar e abrir a revisão de acesso.

1. Procure uma mensagem de e-mail da Microsoft que lhe pede para rever o acesso. Eis um e-mail de exemplo para rever o acesso a um grupo.

    ![E-mail de acesso de revisão](./media/perform-access-review/access-review-email.png)

1. Clique nas **iniciar revisão** link para abrir a revisão de acesso.

Se não tiver o e-mail, pode encontrar o que seu acesso pendente revisões seguindo estes passos.

1. Inicie sessão no portal do My Apps em [ https://myapps.microsoft.com ](https://myapps.microsoft.com).

    ![MyApps portal](./media/perform-access-review/myapps-access-panel.png)

1. No canto superior direito da página, clique no símbolo de utilizador, que mostra o seu nome e a sua organização predefinida. Se aparecer mais de uma organização, selecione aquela que pediu a revisão de acesso.

1. Clique nas **as revisões de acesso** mosaico para ver uma lista das revisões de acesso pendente.

    Se o mosaico não estiver visível, não há revisões de acesso a realizar nessa organização e não é necessária nenhuma ação nesse momento.

    ![Lista de revisões de acesso](./media/perform-access-review/access-reviews-list.png)

1. Clique nas **iniciar revisão** ligação para a revisão de acesso que pretende efetuar.

## <a name="perform-the-access-review"></a>Realizar a revisão de acesso

Ao abrir a revisão de acesso, verá os nomes de utilizadores que precisam ser examinadas.

Se o pedido rever o seu próprio acesso, a página terá uma aparência diferente. Para obter mais informações, consulte [rever acesso para si próprio para grupos ou aplicações](review-your-access.md).

![Executar revisão de acesso](./media/perform-access-review/perform-access-review.png)

Existem duas formas que pode aprovar ou negar o acesso:

- Pode aprovar ou negar o acesso de um ou mais utilizadores, ou
- Pode aceitar as recomendações de sistema, que é a forma mais fácil e rápida.

### <a name="approve-or-deny-access-for-one-or-more-users"></a>Aprovar ou negar o acesso de um ou mais utilizadores

1. Reveja a lista de utilizadores para decidir se deve aprovar ou negar o acesso contínuo.

1. Para aprovar ou negar o acesso de um único utilizador, clique na linha para abrir uma janela para especificar a ação a tomar. Para aprovar ou negar o acesso de vários utilizadores, adicionar marcas de verificação junto aos utilizadores e, em seguida, clique nas **utilizador (es) de revisão X** botão para abrir uma janela para especificar a ação a tomar.

1. Clique em **aprovar** ou **negar**. Se não souber, pode clicar em **não sabe**. Se o fizer, irá resultar no utilizador mantém o acesso, mas a seleção será refletida nos registos de auditoria.

    ![Executar revisão de acesso](./media/perform-access-review/approve-deny.png)

1. Se necessário, introduza um motivo do **motivo** caixa.

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
