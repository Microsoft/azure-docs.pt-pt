---
title: Reveja o seu acesso a grupos & aplicações em comentários de acesso - Azure AD
description: Saiba como rever o seu próprio acesso a grupos ou aplicações em avaliações de acesso ao Azure Ative Directory.
services: active-directory
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/17/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 959837ff540fd95d186497858d7b9de1f8b1124d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91274015"
---
# <a name="review-access-for-yourself-to-groups-or-applications-in-azure-ad-access-reviews"></a>Reveja o acesso a grupos ou aplicações em avaliações de acesso a Azure AD

O Azure Ative Directory (Azure AD) simplifica a forma como as empresas gerem o acesso a grupos ou aplicações em AZure AD e outros Serviços Online da Microsoft com uma funcionalidade chamada Azure AD.

Este artigo descreve como rever o seu próprio acesso a um grupo ou a uma aplicação.

## <a name="review-your-access-using-my-apps"></a>Reveja o seu acesso usando as Minhas Apps

O primeiro passo para realizar uma revisão de acesso é encontrar e abrir a revisão de acesso.

>[!IMPORTANT]
> Pode haver atrasos na receção de e-mail e alguns casos podem demorar até 24 horas. Adicione azure-noreply@microsoft.com à sua lista de destinatários seguros para se certificar de que está a receber todos os e-mails.

1. Procure um e-mail da Microsoft que lhe peça para rever o acesso. Aqui está um e-mail de exemplo para rever o seu acesso a um grupo.

    ![Exemplo de e-mail da Microsoft para rever o seu acesso a um grupo](./media/review-your-access/access-review-email.png)

1. Clique no link **de acesso do Review** para abrir a revisão de acesso.

Se não tiver o e-mail, pode encontrar as suas avaliações de acesso pendentes seguindo estes passos.

1. Inscreva-se no portal My Apps em [https://myapps.microsoft.com](https://myapps.microsoft.com) .

    ![O portal my Apps listagem de aplicativos para que você tem permissões](./media/review-your-access/myapps-access-panel.png)

1. No canto superior direito da página, clique no símbolo de utilizador, que mostra o seu nome e a sua organização predefinida. Se aparecer mais de uma organização, selecione aquela que pediu a revisão de acesso.

1. No lado direito da página, clique no azulejo de **comentários de Acesso** para ver uma lista das avaliações de acesso pendentes.

    Se o mosaico não estiver visível, não há revisões de acesso a realizar nessa organização e não é necessária nenhuma ação nesse momento.

    ![Lista de avaliações de acesso pendentes para as suas apps e grupos](./media/review-your-access/access-reviews-list.png)

1. Clique no link **de revisão Iniciar** para a revisão de acesso que pretende realizar.

### <a name="perform-the-access-review"></a>Realizar a revisão de acesso

Uma vez aberta a revisão de acesso, pode ver o seu acesso.

1. Reveja o seu acesso e decida se ainda precisa de acesso.

    Se o pedido for para rever o acesso a outros, a página será diferente. Para obter mais informações, consulte [o acesso ao Rever a grupos ou aplicações.](perform-access-review.md)

    ![Screenshot que mostra uma revisão de acesso aberto perguntando se você ainda precisa de acesso a um grupo.](./media/review-your-access/perform-access-review.png)

1. Clique **em Sim** para manter o seu acesso ou clique em **Não** para remover o seu acesso.

1. Se clicar em **Sim,** poderá ter de especificar uma justificação na caixa **Reason.**

    ![Screenshot que mostra uma revisão de acesso completa que pergunta se ainda precisa de acesso a um grupo, com "Sim" selecionado.](./media/review-your-access/perform-access-review-submit.png)

1. Clique **em Submeter.**

    A sua seleção é submetida e regressou ao portal My Apps.

    Se quiser alterar a sua resposta, reabre a página de comentários de acesso e atualize a sua resposta. Pode alterar a sua resposta a qualquer momento até que a revisão de acesso termine.

    > [!NOTE]
    > Se indicou que já não precisa de acesso, não será removido imediatamente. É removido quando a revisão tiver terminado ou quando um administrador para a revisão.

## <a name="review-your-own-access-using-my-access-new"></a>Reveja o seu próprio acesso usando o My Access (Novo)

Pode experimentar a nova experiência com a interface de utilizador atualizada no My Access de várias maneiras:

### <a name="my-apps-portal"></a>O meu portal apps

1. Inscreva-se no portal My Apps em [https://myapps.microsoft.com](https://myapps.microsoft.com) .

    ![O portal my Apps listagem de aplicativos para que você tem permissões](./media/review-your-access/myapps-access-panel.png)

2. Clique no azulejo de **comentários de Acesso** para ver uma lista de avaliações de acesso pendentes.

    > [!NOTE]
    > Se o **azulejo de comentários do Access** não for visível, não existem comentários de acesso para essa organização e não é necessária nenhuma ação neste momento.

3. Clique em **Experimentá-lo!** no banner no topo da página para ir para a nova experiência My Access.

    ![Lista de comentários de acesso pendente para apps e grupos com a nova experiência banner disponível exibida durante a pré-visualização](./media/review-your-access/banner-your-access.png)

4. Continuar na secção **Realizar a revisão de acesso**

### <a name="email"></a>E-mail

>[!IMPORTANT]
> Pode haver atrasos na receção de e-mail e alguns casos podem demorar até 24 horas. Adicione azure-noreply@microsoft.com à sua lista de destinatários seguros para se certificar de que está a receber todos os e-mails.

1. Procure um e-mail da Microsoft pedindo-lhe para rever o acesso. Pode ver uma mensagem de e-mail de exemplo abaixo:

 ![Exemplo de e-mail da Microsoft para rever o acesso a um grupo](./media/review-your-access/access-review-email-preview.png)

2. Clique no link **de acesso do Review** para abrir a revisão de acesso.

3. Continuar na secção **Realizar a revisão de acesso**

>[!NOTE]
>Se clicar na revisão inicial leva-o às **Minhas Apps** siga os passos listados na secção acima intitulada **My Apps Portal**.

### <a name="directly-at-my-access"></a>Diretamente no Meu Acesso

Também pode ver os comentários de acesso pendentes utilizando o seu navegador para abrir o My Access.

1. Inscreva-se no My Access em https://myaccess.microsoft.com/

2. Selecione **avaliações** de Acesso do menu na barra lateral esquerda para ver uma lista de avaliações de acesso pendentes atribuídas a si.

   ![comentários de acesso no menu](./media/review-your-access/access-review-menu.png)

### <a name="perform-the-access-review"></a>Realizar a revisão de acesso

1. Em Grupos e Aplicativos pode ver:
    
    - **Nome** O nome da revisão de acesso.
    - **Devido** A data de vencimento para a revisão. Após esta data, os utilizadores foram impedidos de serem removidos do grupo ou da app que está a ser analisado.
    - **Recurso** O nome do recurso em análise.
    - **Progresso** O número de utilizadores revistos sobre o número total de utilizadores parte desta revisão de acesso.
    
2. Clique no nome de uma revisão de Acesso para começar.

   ![Lista de comentários de acesso pendente para apps e grupos](./media/review-your-access/access-reviews-list-preview.png)

3. Reveja o seu acesso e decida se ainda precisa de acesso.

    Se o pedido for para rever o acesso a outros, a página será diferente. Para obter mais informações, consulte [o acesso ao Rever a grupos ou aplicações.](perform-access-review.md)

    ![Revisão de acesso aberto perguntando se ainda precisa de acesso a um grupo](./media/review-your-access/review-access-preview.png)

1. Selecione **Sim** para manter o seu acesso ou selecione **Não** para remover o seu acesso.

1. Se clicar em **Sim,** poderá ter de especificar uma justificação na caixa **Reason.**

    ![Análise de acesso concluída perguntando se ainda precisa de acesso a um grupo](./media/review-your-access/review-access-yes-preview.png)

1. Clique **em Submeter.**

    A sua seleção é submetida e regressou à página 'O Meu Acesso'.

    Se quiser alterar a sua resposta, reabre a página de comentários de acesso e atualize a sua resposta. Pode alterar a sua resposta a qualquer momento até que a revisão de acesso termine.

    > [!NOTE]
    > Se indicou que já não precisa de acesso, não será removido imediatamente. É removido quando a revisão tiver terminado ou quando um administrador para a revisão.

## <a name="next-steps"></a>Passos seguintes

- [Complete uma revisão de acesso de grupos ou aplicações](complete-access-review.md)
