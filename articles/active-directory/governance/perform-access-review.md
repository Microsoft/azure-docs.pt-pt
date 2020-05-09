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
ms.date: 04/30/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a939eece83b94043f750d6f7f63db290f827da4
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611254"
---
# <a name="review-access-to-groups-and-applications-in-azure-ad-access-reviews"></a>Rever o acesso a grupos e aplicações nas avaliações de acesso à AD Azure

O Azure Ative Directory (Azure AD) simplifica a forma como as empresas gerem o acesso a grupos e aplicações em Azure AD e outros Serviços Online da Microsoft com uma funcionalidade chamada avaliações de acesso a AD Azure. Este artigo irá analisar a forma como um revisor designado realiza uma revisão de acesso para membros de um grupo ou utilizadores com acesso a uma aplicação. Se quiser rever, aceda a um pacote ler [Rever o acesso a um pacote de acesso na gestão de direitos da AD Azure](entitlement-management-access-reviews-review-access.md)

## <a name="perform-access-review-using-my-apps"></a>Execute a revisão de acesso usando as Minhas Apps

Pode iniciar o processo de Access Review a partir do e-mail de notificação ou indo diretamente para o site.

- **E-mail:**

>[!IMPORTANT]
> Pode haver atrasos na receção de e-mails e em alguns casos pode demorar até 24 horas. Whitelist azure-noreply@microsoft.com para ter certeza de que está a receber todos os e-mails.

1. Procure um e-mail da Microsoft pedindo-lhe para rever o acesso. Aqui está um e-mail de exemplo para rever o acesso de um grupo.

    ![Email da Microsoft para rever o acesso a um grupo](./media/perform-access-review/access-review-email.png)

1. Clique no link **de revisão Iniciar** para abrir a revisão de acesso.

- **Se não tiver o e-mail,** pode encontrar as suas avaliações de acesso pendentes seguindo estes passos.

    1. Inscreva-se no portal [https://myapps.microsoft.com](https://myapps.microsoft.com)My Apps em .

        ![My Apps portal listing apps você tem permissões para](./media/perform-access-review/myapps-access-panel.png)

    1. No canto superior direito da página, clique no utilizador ao lado do seu nome e organização predefinida. Se aparecer mais de uma organização, selecione aquela que pediu a revisão de acesso.

    1. Clique no azulejo de comentários do **Access** para ver uma lista de avaliações de acesso pendentes.

        > [!NOTE]
        > Se o azulejo do **Access** não for visível, não existem avaliações de acesso para realizar para essa organização e não é necessária nenhuma ação neste momento.

        ![Lista de avaliações de acesso pendentes para apps e grupos](./media/perform-access-review/access-reviews-list.png)

    1. Clique no link **de revisão Iniciar** para a revisão de acesso que pretende realizar.

Uma vez aberta a revisão de acesso, vê os nomes dos utilizadores que precisam de ter o seu acesso revisto.

Se o pedido for para rever o seu próprio acesso, a página será diferente. Para mais informações, consulte [o acesso do Reexame a grupos ou aplicações.](review-your-access.md)

![Revisão de acesso aberto listando os utilizadores para rever](./media/perform-access-review/perform-access-review.png)

Há duas formas de aprovar ou negar o acesso:

- Pode aprovar ou negar o acesso a um ou mais utilizadores 'manualmente', escolhendo as medidas adequadas para cada pedido do utilizador.
- Pode aceitar as recomendações do sistema.

### <a name="approve-or-deny-access-for-one-or-more-users"></a>Aprovar ou negar o acesso a um ou mais utilizadores

1. Reveja a lista de utilizadores e decida se aprova ou nega o seu acesso continuado.

    - Para aprovar ou negar o acesso a um único utilizador, clique na linha para abrir uma janela para especificar a ação a tomar. 
    - Para aprovar ou negar o acesso a vários utilizadores, adicione marcas de verificação ao lado dos utilizadores e, em seguida, clique no botão **do utilizador(s) do Review X** para abrir uma janela para especificar a ação a tomar.

1. Clique em **Aprovar** ou **Negar**. 

    ![Janela de ação que inclui Aprovar, Negar e Não Sei opções](./media/perform-access-review/approve-deny.png)
    >[!NOTE]
    > Se não tiver a certeza, pode clicar **em Não saber**. e o utilizador pode manter o seu acesso e a sua escolha é registada nos registos de auditoria.

1. O administrador da revisão de acesso pode exigir que forneça uma razão na caixa **Razão** para a sua decisão. Mesmo quando uma razão não é necessária. Ainda pode fornecer uma razão para a sua decisão e as informações que incluir estarão disponíveis para outros revisores.

1. Depois de especificar a ação a tomar, clique em **Guardar**.

    >[!NOTE]
    > Pode alterar a sua resposta a qualquer momento antes do fim da revisão de acesso. Se quiser alterar a sua resposta, selecione a linha e atualize a resposta. Por exemplo, pode aprovar um utilizador previamente negado ou negar um utilizador previamente aprovado.

    >[!IMPORTANT]
    > - Se um utilizador não tiver acesso, não é removido imediatamente. São removidos quando o período de revisão termina ou quando um administrador para a revisão se a [aplicação auto](complete-access-review.md#apply-the-changes) estiver ativada.
    > - Se houver vários revisores, a última resposta submetida é gravada. Considere um exemplo onde um administrador designa dois revisores - Alice e Bob. Alice abre primeiro a revisão de acesso e aprova o pedido de acesso de um utilizador. Antes do fim do período de revisão, Bob abre a revisão de acesso e nega o acesso ao mesmo pedido previamente aprovado por Alice. A última decisão que nega o acesso é a resposta que é registada.

### <a name="approve-or-deny-access-based-on-recommendations"></a>Aprovar ou negar o acesso com base em recomendações

Para tornar as avaliações de acesso mais fáceis e rápidas para si, também fornecemos recomendações que pode aceitar com um único clique. As recomendações são geradas com base na atividade de inscrição do utilizador.

1. Na barra azul na parte inferior da página, clique em **Aceitar recomendações.**

    ![Lista de revisão de acesso aberto mostrando o botão de recomendações Aceitar](./media/perform-access-review/accept-recommendations.png)

    Vê um resumo das ações recomendadas.

    ![Janela que exibe um resumo das ações recomendadas](./media/perform-access-review/accept-recommendations-summary.png)

1. Clique **em Ok** para aceitar as recomendações.

## <a name="perform-access-review-using-my-access-new"></a>Execute a revisão de acesso usando o My Access (Novo)

Pode aceder à nova experiência do revisor com a interface de utilizador atualizada no My Access de várias maneiras diferentes:

### <a name="my-apps-portal"></a>O portal My Apps

1. Inscreva-se nas [https://myapps.microsoft.com](https://myapps.microsoft.com)Minhas Apps em .

    ![My Apps portal listing apps você tem permissões para](./media/perform-access-review/myapps-access-panel.png)

2. Clique no azulejo de comentários do **Access** para ver uma lista de avaliações de acesso pendentes.

    > [!NOTE]
    > Se o azulejo do **Access** não for visível, não existem avaliações de acesso para realizar para essa organização e não é necessária nenhuma ação neste momento.

![Lista de avaliações de acesso pendentes para apps e grupos com a nova experiência disponível banner exibido durante a pré-visualização](./media/perform-access-review/banner.png)

3. Clique em **Experimente!** no estandarte no topo da página. Isto vai levá-lo à nova experiência My Access.
  
### <a name="email"></a>Email

  >[!IMPORTANT]
> Pode haver atrasos na receção de e-mails e em alguns casos pode demorar até 24 horas. Whitelist azure-noreply@microsoft.com para ter certeza de que está a receber todos os e-mails.

   1. Procure um e-mail da Microsoft pedindo-lhe para rever o acesso. Pode ver uma mensagem de e-mail de exemplo abaixo:

   ![Email da Microsoft para rever o acesso a um grupo](./media/perform-access-review/access-review-email-preview.png)

   2. Clique no link **de revisão Iniciar** para abrir a revisão de acesso.

>[!NOTE]
>Se clicar na revisão inicial leva-o a **My Apps** siga os passos listados na secção acima intitulada My **Apps Portal**.

### <a name="navigate-to-my-access-directly"></a>Navegue diretamente para o meu acesso

Também pode ver as suas avaliações de acesso pendentes utilizando o seu navegador para abrir o My Access.

1. Inscreva-se no My Access emhttps://myaccess.microsoft.com/

2. Selecione comentários de **Acesso** do menu na barra lateral esquerda para ver uma lista de avaliações de acesso pendentes atribuídas a si.

   ![avaliações de acesso no menu](./media/perform-access-review/access-review-menu.png)

### <a name="approve-or-deny-access-for-one-or-more-users"></a>Aprovar ou negar o acesso a um ou mais utilizadores

Depois de abrir o Meu Acesso em Grupos e Apps, pode ver:

- **Nome** O nome da revisão de acesso.
- **Vencido** A data de vencimento para a revisão. Após esta data, os utilizadores podem ser removidos do grupo ou da aplicação que está a ser revista.
- **Recurso** O nome do recurso em análise.
- **Progresso** O número de utilizadores revistos sobre o número total de utilizadores que fazem parte desta revisão de acesso.

Clique no nome de uma revisão de acesso para começar.

![Lista de avaliações de acesso pendentes para apps e grupos](./media/perform-access-review/access-reviews-list-preview.png)

Assim que abrir, verá a lista de utilizadores no âmbito da revisão de acesso. Se o pedido for para rever o seu próprio acesso, a página será diferente. Para mais informações, consulte [o acesso do Reexame a grupos ou aplicações.](review-your-access.md)

Há duas formas de aprovar ou negar o acesso:

- Pode aprovar manualmente ou negar o acesso a um ou mais utilizadores.
- Pode aceitar as recomendações do sistema.

#### <a name="manually-approve-or-deny-access-for-one-or-more-users"></a>Aprovar manualmente ou negar o acesso a um ou mais utilizadores

1. Reveja a lista de utilizadores e decida se aprova ou nega o seu acesso continuado.
2. Selecione um ou mais utilizadores clicando no círculo ao lado dos seus nomes.
3. Selecione **Aprovar** ou **Negar** na barra acima.
    - Se não tiver a certeza, pode clicar **em Não saber**. O utilizador pode manter o seu acesso e a sua escolha é registada nos registos de auditoria. É importante que tenha em mente que qualquer informação que forneça estará disponível para outros revisores. Podem ler os seus comentários e levá-los em conta quando reverem o pedido.

    ![Revisão de acesso aberto listando os utilizadores que precisam de revisão](./media/perform-access-review/user-list-preview.png)

4. O administrador da revisão de acesso pode exigir que forneça uma razão na caixa **Razão** para a sua decisão. Mesmo quando uma razão não é necessária. Ainda pode fornecer uma razão para a sua decisão e as informações que incluir estarão disponíveis para outros aprovadores para revisão.

5. Clique em **Submeter**.
    - Pode alterar a sua resposta a qualquer momento até que a revisão de acesso termine. Se quiser alterar a sua resposta, selecione a linha e atualize a resposta. Por exemplo, pode aprovar um utilizador previamente negado ou negar um utilizador previamente aprovado.

 >[!IMPORTANT]
 > - Se um utilizador não tiver acesso, não é removido imediatamente. São removidos quando o período de revisão termina ou quando um administrador para a revisão. 
 > - Se houver vários revisores, a última resposta submetida é gravada. Considere um exemplo onde um administrador designa dois revisores - Alice e Bob. Alice abre primeiro a revisão de acesso e aprova o pedido de acesso de um utilizador. Antes do fim do período de revisão, Bob abre a revisão de acesso e nega o acesso ao mesmo pedido previamente aprovado por Alice. A última decisão que nega o acesso é a resposta que é registada.

#### <a name="approve-or-deny-access-based-on-recommendations"></a>Aprovar ou negar o acesso com base em recomendações

Para tornar as avaliações de acesso mais fáceis e rápidas para si, também fornecemos recomendações que pode aceitar com um único clique. As recomendações são geradas com base na atividade de inscrição do utilizador.

1. Selecione um ou mais utilizadores e, em seguida, clique em **aceitar recomendações**.

    ![Lista de revisão de acesso aberto mostrando o botão de recomendações Aceitar](./media/perform-access-review/accept-recommendations-preview.png)

1. Clique em **Submeter** para aceitar as recomendações.

Para aceitar recomendações para todos os utilizadores certifique-se de que ninguém é selecionado e clique no botão **de recomendações Aceitar** na barra superior.

>[!NOTE]
>Quando aceitar recomendações, as decisões anteriores não serão alteradas.

## <a name="next-steps"></a>Passos seguintes

- [Concluir uma revisão de acesso de grupos ou aplicações](complete-access-review.md)

