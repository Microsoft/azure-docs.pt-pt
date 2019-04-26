---
title: Rever acesso para si próprio para grupos ou aplicações nas revisões de acesso - Azure Active Directory | Documentos da Microsoft
description: Saiba como rever o seu próprio acesso a grupos ou aplicações nas revisões de acesso do Azure Active Directory.
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
ms.openlocfilehash: 3fe2013ff84dd0451fed7d108539606520cb9403
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60384552"
---
# <a name="review-access-for-yourself-to-groups-or-applications-in-azure-ad-access-reviews"></a>Rever acesso para si próprio para grupos ou as revisões de acesso a aplicações no Azure AD

Azure Active Directory (Azure AD) simplifica como as empresas a gerir o acesso a grupos ou aplicações no Azure AD e revisões de outros serviços Online da Microsoft com um recurso chamado acesso do Azure AD.

Este artigo descreve como rever o seu próprio acesso a um grupo ou uma aplicação.

## <a name="open-the-access-review"></a>Abra a revisão de acesso

É o primeiro passo para realizar uma revisão de acesso encontrar e abrir a revisão de acesso.

1. Procure uma mensagem de e-mail da Microsoft que lhe pede para rever o acesso. Eis um e-mail de exemplo para rever o acesso a um grupo.

    ![E-mail de acesso de revisão](./media/review-your-access/access-review-email.png)

1. Clique nas **rever acesso** link para abrir a revisão de acesso.

Se não tiver o e-mail, pode encontrar o que seu acesso pendente revisões seguindo estes passos.

1. Inicie sessão no portal do My Apps em [ https://myapps.microsoft.com ](https://myapps.microsoft.com).

    ![MyApps portal](./media/review-your-access/myapps-access-panel.png)

1. No canto superior direito da página, clique no símbolo de utilizador, que mostra o seu nome e a sua organização predefinida. Se aparecer mais de uma organização, selecione aquela que pediu a revisão de acesso.

1. No lado direito da página, clique nas **as revisões de acesso** mosaico para ver uma lista das revisões de acesso pendente.

    Se o mosaico não estiver visível, não há revisões de acesso a realizar nessa organização e não é necessária nenhuma ação nesse momento.

    ![Lista de revisões de acesso](./media/review-your-access/access-reviews-list.png)

1. Clique nas **iniciar revisão** ligação para a revisão de acesso que pretende efetuar.

## <a name="perform-the-access-review"></a>Realizar a revisão de acesso

Ao abrir a revisão de acesso, pode ver o acesso do utilizador.

1. Rever o seu acesso e decidir se ainda precisam de acesso.

    Se o pedido rever o acesso para outras pessoas, a página terá uma aparência diferente. Para obter mais informações, consulte [rever o acesso a grupos ou aplicações](perform-access-review.md).

    ![Executar revisão de acesso](./media/review-your-access/perform-access-review.png)

1. Clique em **Sim** para manter o seu ou clique em **não** para remover o acesso do utilizador.

1. Se clicar **Sim**, poderá ter de especificar uma justificativa no **motivo** caixa.

    ![Executar revisão de acesso](./media/review-your-access/perform-access-review-submit.png)

1. Clique em **Submit** (Submeter).

    A seleção é submetida e retornado para o portal MyApps.

    Se pretender alterar a sua resposta, volte a abrir a página de revisões de acesso e Atualize sua resposta. Pode alterar a sua resposta em qualquer altura até a revisão de acesso terminou.

    > [!NOTE]
    > Se tivesse indicado que já não precisa de acesso, não são removidos imediatamente. São removidas quando a revisão terminou ou quando um administrador para a revisão.

## <a name="next-steps"></a>Passos Seguintes

- [Concluir uma revisão de acesso de grupos ou aplicações](complete-access-review.md)
