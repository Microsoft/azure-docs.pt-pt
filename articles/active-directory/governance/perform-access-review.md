---
title: Examinar o acesso a grupos & aplicativos em revisões de acesso – Azure AD
description: Saiba como revisar o acesso de membros do grupo ou acesso ao aplicativo em Azure Active Directory revisões de acesso.
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
ms.openlocfilehash: 8393a1e4719137aa09233d0a87dabdf81d8ccb59
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75422462"
---
# <a name="review-access-to-groups-and-applications-in-azure-ad-access-reviews"></a>Examinar o acesso a grupos e aplicativos nas revisões de acesso do Azure AD

O Azure Active Directory (AD do Azure) simplifica como as empresas gerenciam o acesso a grupos e aplicativos no Azure AD e outros serviços online da Microsoft com um recurso chamado revisões de acesso do Azure AD.

Este artigo descreve como um revisor designado executa uma revisão de acesso para membros de um grupo ou usuários com acesso a um aplicativo.

## <a name="open-the-access-review"></a>Abrir a revisão de acesso

A primeira etapa para executar uma revisão de acesso é encontrar e abrir a revisão de acesso.

1. Procure um email da Microsoft que solicita que você examine o acesso. Aqui está um email de exemplo para examinar o acesso de um grupo.

    ![Email de exemplo da Microsoft para revisar o acesso a um grupo](./media/perform-access-review/access-review-email.png)

1. Clique no link **Iniciar revisão** para abrir a revisão de acesso.

Se você não tiver o email, poderá encontrar suas revisões de acesso pendentes seguindo estas etapas.

1. Entre no portal do myapps em [https://myapps.microsoft.com](https://myapps.microsoft.com).

    ![Portal do myapps Listando aplicativos aos quais você tem permissões](./media/perform-access-review/myapps-access-panel.png)

1. No canto superior direito da página, clique no símbolo de utilizador, que mostra o seu nome e a sua organização predefinida. Se aparecer mais de uma organização, selecione aquela que pediu a revisão de acesso.

1. Clique no bloco **revisões de acesso** para ver uma lista das revisões de acesso pendentes.

    Se o mosaico não estiver visível, não há revisões de acesso a realizar nessa organização e não é necessária nenhuma ação nesse momento.

    ![Lista de revisões de acesso pendente para aplicativos e grupos](./media/perform-access-review/access-reviews-list.png)

1. Clique no link **Iniciar revisão** para a revisão de acesso que você deseja executar.

## <a name="perform-the-access-review"></a>Executar a revisão de acesso

Depois de abrir a revisão de acesso, você verá os nomes dos usuários que precisam ser revisados.

Se a solicitação for revisar seu próprio acesso, a página terá uma aparência diferente. Para obter mais informações, consulte [examinar o acesso a grupos ou aplicativos](review-your-access.md).

![Abrir revisão de acesso listando os usuários que precisam ser revisados](./media/perform-access-review/perform-access-review.png)

Há duas maneiras de aprovar ou negar o acesso:

- Você pode aprovar ou negar o acesso para um ou mais usuários, ou
- Você pode aceitar as recomendações do sistema, que é a maneira mais fácil e rápida.

### <a name="approve-or-deny-access-for-one-or-more-users"></a>Aprovar ou negar acesso para um ou mais usuários

1. Examine a lista de usuários para decidir se deseja aprovar ou negar o acesso contínuo.

1. Para aprovar ou negar o acesso de um único usuário, clique na linha para abrir uma janela a fim de especificar a ação a ser tomada. Para aprovar ou negar acesso para vários usuários, adicione marcas de seleção ao lado dos usuários e, em seguida, clique no botão **revisar X usuário (s)** para abrir uma janela para especificar a ação a ser tomada.

1. Clique em **aprovar** ou **negar**. Se não tiver certeza, você pode clicar em **não saber**. Isso fará com que o usuário Mantenha seu acesso, mas a seleção será refletida nos logs de auditoria.

    ![Janela de ação que inclui opções aprovar, negar e não sei](./media/perform-access-review/approve-deny.png)

1. Se necessário, insira um motivo na caixa **motivo** .

    O administrador da revisão de acesso pode exigir que você forneça um motivo para aprovar o acesso contínuo ou a associação de grupo.

1. Depois de especificar a ação a ser tomada, clique em **salvar**.

    Se você quiser alterar sua resposta, selecione a linha e atualize a resposta. Por exemplo, você pode aprovar um usuário negado anteriormente ou negar um usuário aprovado anteriormente. Você pode alterar sua resposta a qualquer momento até que a revisão de acesso seja encerrada.

    Se houver vários revisores, a última resposta enviada será registrada. Considere um exemplo em que um administrador designa dois revisores – Alice e Bob. Alice abre a revisão de acesso primeiro e aprova o acesso. Antes de a revisão terminar, Bob abre a revisão de acesso e nega o acesso. A última resposta de negação é o que é registrado.

    > [!NOTE]
    > Se um usuário tiver acesso negado, ele não será removido imediatamente. Eles são removidos quando a revisão termina ou quando um administrador para a revisão.

### <a name="approve-or-deny-access-based-on-recommendations"></a>Aprovar ou negar o acesso com base nas recomendações

Para fazer análises de acesso mais fáceis e rápidas para você, também fornecemos recomendações que você pode aceitar com um único clique. As recomendações são geradas com base na atividade de entrada do usuário.

1. Na barra azul na parte inferior da página, clique em **aceitar recomendações**.

    ![Abrir a listagem de revisão de acesso mostrando o botão aceitar recomendações](./media/perform-access-review/accept-recommendations.png)

    Você verá um resumo das ações recomendadas.

    ![Janela que exibe um resumo das ações recomendadas](./media/perform-access-review/accept-recommendations-summary.png)

1. Clique em **OK** para aceitar as recomendações.

## <a name="next-steps"></a>Passos seguintes

- [Concluir uma revisão de acesso de grupos ou aplicativos](complete-access-review.md)
