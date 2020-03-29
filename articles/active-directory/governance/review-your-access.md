---
title: Reveja o seu acesso a grupos & aplicações em comentários de acesso - Azure AD
description: Saiba como rever o seu próprio acesso a grupos ou aplicações nas avaliações de acesso ao Diretório Ativo do Azure.
services: active-directory
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/21/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d75d7ffe28fa126ee5e359cba19e66e3c0f36e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422406"
---
# <a name="review-access-for-yourself-to-groups-or-applications-in-azure-ad-access-reviews"></a>Reveja o acesso a grupos ou aplicações em avaliações de acesso a Anúncio si mesmo

O Azure Ative Directory (Azure AD) simplifica a forma como as empresas gerem o acesso a grupos ou aplicações em Azure AD e outros Serviços Online da Microsoft com uma funcionalidade chamada avaliações de acesso a AD Azure.

Este artigo descreve como rever o seu próprio acesso a um grupo ou a uma aplicação.

## <a name="open-the-access-review"></a>Abra a revisão de acesso

O primeiro passo para realizar uma revisão de acesso é encontrar e abrir a revisão de acesso.

1. Procure um e-mail da Microsoft que lhe peça para rever o acesso. Aqui está um e-mail de exemplo para rever o seu acesso a um grupo.

    ![Exemplo de e-mail da Microsoft para rever o seu acesso a um grupo](./media/review-your-access/access-review-email.png)

1. Clique no link **de acesso rever** para abrir a revisão de acesso.

Se não tiver o e-mail, pode encontrar as suas avaliações de acesso pendentes seguindo estes passos.

1. Inscreva-se no portal [https://myapps.microsoft.com](https://myapps.microsoft.com)MyApps em .

    ![Aplicações de listagem de portais MyApps que tem permissões](./media/review-your-access/myapps-access-panel.png)

1. No canto superior direito da página, clique no símbolo de utilizador, que mostra o seu nome e a sua organização predefinida. Se aparecer mais de uma organização, selecione aquela que pediu a revisão de acesso.

1. No lado direito da página, clique no azulejo de comentários de **Acesso** para ver uma lista das avaliações de acesso pendentes.

    Se o mosaico não estiver visível, não há revisões de acesso a realizar nessa organização e não é necessária nenhuma ação nesse momento.

    ![Lista de avaliações de acesso pendentes para as suas apps e grupos](./media/review-your-access/access-reviews-list.png)

1. Clique no link **de revisão Iniciar** para a revisão de acesso que pretende realizar.

## <a name="perform-the-access-review"></a>Realizar a revisão de acesso

Uma vez aberta a revisão de acesso, pode ver o seu acesso.

1. Reveja o seu acesso e decida se ainda precisa de acesso.

    Se o pedido for para rever o acesso a outros, a página será diferente. Para mais informações, consulte [Rever o acesso a grupos ou aplicações](perform-access-review.md).

    ![Revisão de acesso aberto perguntando se você ainda precisa de acesso a um grupo](./media/review-your-access/perform-access-review.png)

1. Clique em **Sim** para manter o seu acesso ou clique **em Não** para remover o seu acesso.

1. Se clicar em **Sim,** poderá ter de especificar uma justificação na caixa **Reason.**

    ![Revisão de acesso concluída perguntando se ainda precisa de acesso a um grupo](./media/review-your-access/perform-access-review-submit.png)

1. Clique em **Submeter**.

    A sua seleção é submetida e regressou ao portal MyApps.

    Se quiser alterar a sua resposta, reabra a página de comentários de acesso e atualize a sua resposta. Pode alterar a sua resposta a qualquer momento até que a revisão de acesso termine.

    > [!NOTE]
    > Se indicou que já não precisa de acesso, não será removido imediatamente. É removido quando a revisão terminar ou quando um administrador para a revisão.

## <a name="next-steps"></a>Passos seguintes

- [Concluir uma revisão de acesso de grupos ou aplicações](complete-access-review.md)
