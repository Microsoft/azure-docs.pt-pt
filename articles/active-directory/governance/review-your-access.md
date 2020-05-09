---
title: Reveja o seu acesso a grupos & aplicações em comentários de acesso - Azure AD
description: Saiba como rever o seu próprio acesso a grupos ou aplicações nas avaliações de acesso ao Diretório Ativo do Azure.
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
ms.date: 04/30/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0de10534cb00c66ad914b2c64ac2547b0e3fe03b
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611118"
---
# <a name="review-access-for-yourself-to-groups-or-applications-in-azure-ad-access-reviews"></a>Reveja o acesso a grupos ou aplicações em avaliações de acesso a Anúncio si mesmo

O Azure Ative Directory (Azure AD) simplifica a forma como as empresas gerem o acesso a grupos ou aplicações em Azure AD e outros Serviços Online da Microsoft com uma funcionalidade chamada avaliações de acesso a AD Azure.

Este artigo descreve como rever o seu próprio acesso a um grupo ou a uma aplicação.

## <a name="review-your-access-using-my-apps"></a>Reveja o seu acesso usando as Minhas Apps

O primeiro passo para realizar uma revisão de acesso é encontrar e abrir a revisão de acesso.

>[!IMPORTANT]
> Pode haver atrasos na receção de e-mails e em alguns casos pode demorar até 24 horas. Whitelist azure-noreply@microsoft.com para ter certeza de que está a receber todos os e-mails.

1. Procure um e-mail da Microsoft que lhe peça para rever o acesso. Aqui está um e-mail de exemplo para rever o seu acesso a um grupo.

    ![Exemplo de e-mail da Microsoft para rever o seu acesso a um grupo](./media/review-your-access/access-review-email.png)

1. Clique no link **de acesso rever** para abrir a revisão de acesso.

Se não tiver o e-mail, pode encontrar as suas avaliações de acesso pendentes seguindo estes passos.

1. Inscreva-se no portal [https://myapps.microsoft.com](https://myapps.microsoft.com)My Apps em .

    ![My Apps portal listing apps você tem permissões para](./media/review-your-access/myapps-access-panel.png)

1. No canto superior direito da página, clique no símbolo de utilizador, que mostra o seu nome e a sua organização predefinida. Se aparecer mais de uma organização, selecione aquela que pediu a revisão de acesso.

1. No lado direito da página, clique no azulejo de comentários de **Acesso** para ver uma lista das avaliações de acesso pendentes.

    Se o mosaico não estiver visível, não há revisões de acesso a realizar nessa organização e não é necessária nenhuma ação nesse momento.

    ![Lista de avaliações de acesso pendentes para as suas apps e grupos](./media/review-your-access/access-reviews-list.png)

1. Clique no link **de revisão Iniciar** para a revisão de acesso que pretende realizar.

### <a name="perform-the-access-review"></a>Realizar a revisão de acesso

Uma vez aberta a revisão de acesso, pode ver o seu acesso.

1. Reveja o seu acesso e decida se ainda precisa de acesso.

    Se o pedido for para rever o acesso a outros, a página será diferente. Para mais informações, consulte [Rever o acesso a grupos ou aplicações](perform-access-review.md).

    ![Revisão de acesso aberto perguntando se você ainda precisa de acesso a um grupo](./media/review-your-access/perform-access-review.png)

1. Clique em **Sim** para manter o seu acesso ou clique **em Não** para remover o seu acesso.

1. Se clicar em **Sim,** poderá ter de especificar uma justificação na caixa **Reason.**

    ![Revisão de acesso concluída perguntando se ainda precisa de acesso a um grupo](./media/review-your-access/perform-access-review-submit.png)

1. Clique em **Submeter**.

    A sua seleção é submetida e regressou ao portal My Apps.

    Se quiser alterar a sua resposta, reabra a página de comentários de acesso e atualize a sua resposta. Pode alterar a sua resposta a qualquer momento até que a revisão de acesso termine.

    > [!NOTE]
    > Se indicou que já não precisa de acesso, não será removido imediatamente. É removido quando a revisão terminar ou quando um administrador para a revisão.

## <a name="review-your-own-access-using-my-access-new"></a>Reveja o seu próprio acesso usando o My Access (Novo)

Pode experimentar a nova experiência com a interface de utilizador atualizada no My Access de várias formas diferentes:

### <a name="my-apps-portal"></a>O portal My Apps

1. Inscreva-se no portal [https://myapps.microsoft.com](https://myapps.microsoft.com)My Apps em .

    ![My Apps portal listing apps você tem permissões para](./media/review-your-access/myapps-access-panel.png)

2. Clique no azulejo de comentários do **Access** para ver uma lista de avaliações de acesso pendentes.

    > [!NOTE]
    > Se o azulejo do **Access** não for visível, não existem avaliações de acesso para realizar para essa organização e não é necessária nenhuma ação neste momento.

3. Clique em **Experimente!** no banner no topo da página para ir para a nova experiência My Access.

    ![Lista de avaliações de acesso pendentes para apps e grupos com a nova experiência disponível banner exibido durante a pré-visualização](./media/review-your-access/banner-your-access.png)

4. Continuar na secção **Realizar a revisão de acesso**

### <a name="email"></a>Email

>[!IMPORTANT]
> Pode haver atrasos na receção de e-mails e em alguns casos pode demorar até 24 horas. Whitelist azure-noreply@microsoft.com para ter certeza de que está a receber todos os e-mails.

1. Procure um e-mail da Microsoft pedindo-lhe para rever o acesso. Pode ver uma mensagem de e-mail de exemplo abaixo:

 ![Email da Microsoft para rever o acesso a um grupo](./media/review-your-access/access-review-email-preview.png)

2. Clique no link **de acesso rever** para abrir a revisão de acesso.

3. Continuar na secção **Realizar a revisão de acesso**

>[!NOTE]
>Se clicar na revisão inicial leva-o a **My Apps** siga os passos listados na secção acima intitulada My **Apps Portal**.

### <a name="directly-at-my-access"></a>Diretamente no Meu Acesso

Também pode ver as suas avaliações de acesso pendentes utilizando o seu navegador para abrir o My Access.

1. Inscreva-se no My Access emhttps://myaccess.microsoft.com/

2. Selecione comentários de **Acesso** do menu na barra lateral esquerda para ver uma lista de avaliações de acesso pendentes atribuídas a si.

   ![avaliações de acesso no menu](./media/review-your-access/access-review-menu.png)

### <a name="perform-the-access-review"></a>Realizar a revisão de acesso

1. Em Grupos e Apps pode ver:
    
    - **Nome** O nome da revisão de acesso.
    - **Vencido** A data de vencimento para a revisão. Após esta data, os utilizadores podem ser removidos do grupo ou da aplicação que está a ser revista.
    - **Recurso** O nome do recurso em análise.
    - **Progresso** O número de utilizadores revistos sobre o número total de utilizadores que fazem parte desta revisão de acesso.
    
2. Clique no nome de uma revisão do Access para começar.

   ![Lista de avaliações de acesso pendentes para apps e grupos](./media/review-your-access/access-reviews-list-preview.png)

3. Reveja o seu acesso e decida se ainda precisa de acesso.

    Se o pedido for para rever o acesso a outros, a página será diferente. Para mais informações, consulte [Rever o acesso a grupos ou aplicações](perform-access-review.md).

    ![Revisão de acesso aberto perguntando se você ainda precisa de acesso a um grupo](./media/review-your-access/review-access-preview.png)

1. Selecione **Sim** para manter o seu acesso ou selecione **Não** para remover o seu acesso.

1. Se clicar em **Sim,** poderá ter de especificar uma justificação na caixa **Reason.**

    ![Revisão de acesso concluída perguntando se ainda precisa de acesso a um grupo](./media/review-your-access/review-access-yes-preview.png)

1. Clique em **Submeter**.

    A sua seleção é submetida e regressou à página My Access.

    Se quiser alterar a sua resposta, reabra a página de comentários de acesso e atualize a sua resposta. Pode alterar a sua resposta a qualquer momento até que a revisão de acesso termine.

    > [!NOTE]
    > Se indicou que já não precisa de acesso, não será removido imediatamente. É removido quando a revisão terminar ou quando um administrador para a revisão.

## <a name="next-steps"></a>Passos seguintes

- [Concluir uma revisão de acesso de grupos ou aplicações](complete-access-review.md)
