---
title: Gerencie o acesso da sua organização a apps e grupos - Azure AD
description: Saiba como realizar uma revisão de acesso para gerir o acesso à segurança das aplicações e grupos da sua organização a partir do portal My Apps.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 3/21/2019
ms.author: lizross
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: e739024686bdac497b9b7dd450c5ed46e3cf9a63
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705025"
---
# <a name="perform-an-access-review-from-the-my-apps-portal"></a>Execute uma análise de acesso a partir do portal My Apps

Pode utilizar o seu trabalho ou conta escolar com o portal **My Apps** baseado na Web, para visualizar e iniciar muitas das aplicações baseadas na nuvem da sua organização, atualizar algumas das informações do seu perfil e conta, ver as informações dos seus **Grupos** e realizar **avaliações** de acesso para as suas apps e grupos. Se não tiver acesso ao portal **My Apps,** deve contactar o seu Helpdesk para obter permissão.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Este conteúdo destina-se aos utilizadores. Se você for um administrador, poderá encontrar mais informações sobre como configurar e gerenciar seus aplicativos baseados em nuvem na documentação do gerenciamento de [aplicativos](https://docs.microsoft.com/azure/active-directory/manage-apps).

## <a name="manage-access-reviews"></a>Gerir avaliações de acesso

Se o seu administrador lhe tiver dado permissão para realizar as suas próprias avaliações de acesso, pode gerir o acesso dos seus grupos ou aplicações a partir do azulejo de comentários de **Acesso** na página do portal **My Apps.**

>[!Note]
>Se não vir o azulejo de avaliações do **Access,** significa que não tem permissão para realizar avaliações de acesso, ou que não tem quaisquer avaliações pendentes à espera da sua aprovação. Se acha que deve ter acesso ao azulejo, contacte o seu balcão de ajuda para obter assistência.

### <a name="to-perform-your-access-reviews"></a>Para realizar as suas avaliações de acesso

1. Entre em sua conta corporativa ou de estudante.

2. Abra o seu navegador web e vá para https://myapps.microsoft.com, ou use o link fornecido pela sua organização. Por exemplo, pode ser direcionado para uma página personalizada para a sua organização, como https://myapps.microsoft.com/contoso.com.

    A página apps aparece, mostrando todas as aplicações baseadas na nuvem detidas pela sua organização e disponíveis para que possa utilizar.

    ![Página de aplicativos no portal My Apps](media/my-apps-portal/my-apps-portal-apps-page-access-review-tile.png)

3. Selecione o azulejo de **comentários de Acesso** para ver uma lista de avaliações de acesso à espera da sua aprovação.

    ![Página de comentários de acesso com avaliações de acesso pendentes para a organização](media/my-apps-portal/my-apps-portal-access-reviews-page.png)

4. Selecione **Iniciar a revisão** para iniciar a sua revisão de acesso.

5. Reveja o seu acesso e determine se ainda é necessário.

    ![Página de análise de acesso, mostrando os detalhes da revisão](media/my-apps-portal/my-apps-portal-perform-access-reviews-page.png)

    >[!Note]
    >Se for administrador e permitir rever o acesso da sua organização a grupos e aplicações, verá uma página diferente. Para obter mais informações sobre a revisão de grupos ou aplicações para a sua organização, consulte [o acesso de Revisão a grupos ou aplicações em Comentários de Acesso AD Azure](https://docs.microsoft.com/azure/active-directory/governance/perform-access-review).

6. Selecione **Sim** para manter o seu acesso ou **Não** para remover o seu acesso.

    Se selecionar **Sim,** poderá ter de especificar uma justificação na caixa **Reason.**

    ![Página de revisão de acesso, mostrando a caixa Razão com texto de amostra](media/my-apps-portal/my-apps-portal-perform-access-reviews-reason-box.png)

7. Selecione **submeter**.

    A sua análise de acesso está completa e regressa ao portal **My Apps.**

    >[!Note]
    >Pode alterar o seu acesso a qualquer momento até que o período de revisão de acesso termine. Se remover o seu acesso a uma aplicação ou grupo, não será removido imediatamente. A remoção ocorre quando o período de revisão de acesso termina ou quando um administrador encerra a revisão.

## <a name="next-steps"></a>Passos seguintes

- [Aceda e utilize aplicações no portal My Apps](my-apps-portal-end-user-access.md).

- [Altere a informação do seu perfil.](my-apps-portal-end-user-update-profile.md)

- [Ver e atualizar as informações relacionadas com os seus grupos.](my-apps-portal-end-user-groups.md)
