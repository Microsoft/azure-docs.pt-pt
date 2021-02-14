---
title: Gerencie o acesso da sua organização a grupos de & de apps - Azure AD
description: Saiba como realizar uma revisão de acesso para gerir o acesso à segurança das aplicações e grupos da sua organização a partir do portal My Apps.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 01/19/2021
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 34885e2a364778a2f81f4920aa26aa3bb5f40320
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/10/2021
ms.locfileid: "100095023"
---
# <a name="perform-an-access-review-from-the-my-apps-portal"></a>Realizar uma análise de acesso a partir do portal My Apps

Pode utilizar a sua conta de trabalho ou escola com o portal **My Apps** baseado na Web para realizar avaliações de acesso para as suas apps e grupos. As avaliações de acesso ajudam-no a gerir o acesso desatualizado ou a alterar os requisitos de acesso e a garantir que são revistos e atualizados.

Se não tiver acesso ao portal **My Apps,** contacte o seu Helpdesk para obter permissão.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Este conteúdo destina-se aos **utilizadores das Minhas Apps.** Se for administrador, pode encontrar mais informações sobre como configurar e gerir as suas aplicações baseadas na nuvem na [Documentação de Gestão de Aplicações.](../manage-apps/index.yml)
>
> Se vir um erro de sessão com uma conta pessoal da Microsoft, ainda pode iniciar sessão utilizando o nome de domínio da sua organização (como contoso.com) ou o **ID** do Inquilino da sua organização a partir de um dos seguintes URLs:
>
>   - https://myapplications.microsoft.com?tenantId=*your_domain_name*
>   - https://myapplications.microsoft.com?tenant=*your_tenant_ID*

## <a name="manage-access-reviews"></a>Gerir comentários de acesso

Se o seu administrador lhe tiver dado permissão para realizar as suas próprias avaliações de acesso, pode gerir os seus grupos ou aplicações de acesso a partir do azulejo de **comentários access** na página do portal **My Apps.**

>[!Note]
>Se não vir o azulejo de **comentários do Access,** ou significa que não tem permissão para realizar comentários de acesso, ou que não tem quaisquer comentários pendentes à espera da sua aprovação. Se acha que deve ter acesso ao azulejo, contacte o seu Helpdesk para obter ajuda.

## <a name="to-perform-your-access-reviews"></a>Para realizar as suas avaliações de acesso

1. Inscreva-se na sua conta de trabalho ou escola.

1. Abra o seu navegador web e vá https://myapps.microsoft.com para, ou use o link fornecido pela sua organização. Por exemplo, pode ser direcionado para uma página personalizada para a sua organização, como https://myapps.microsoft.com/contoso.com .

    A página **apps** aparece, mostrando todas as aplicações baseadas na nuvem pertencentes à sua organização e disponíveis para você usar.

    ![Página de aplicativos no portal My Apps](media/my-apps-portal/my-apps-home.png)

1. Selecione o **azulejo de comentários de acesso** para ver uma lista de avaliações de acesso à espera da sua aprovação.

    ![Aceder à página de comentários com comentários de acesso pendentes para a organização](media/my-apps-portal/my-apps-portal-access-reviews-page.png)

1. Selecione Iniciar a **revisão** para iniciar a sua avaliação de acesso.

5. Reveja o seu acesso e determine se ainda é necessário.

    ![Aceder à página de revisão, mostrar os detalhes da análise](media/my-apps-portal/my-apps-portal-perform-access-reviews-page.png)

    >[!Note]
    >Se for um administrador e tiver permissão para rever o acesso da sua organização a grupos e aplicações, verá uma página diferente. Para obter mais informações sobre grupos de revisão ou apps para a sua organização, consulte [o acesso de Revisão a grupos ou aplicações em Azure AD Access Reviews](../governance/perform-access-review.md).

6. Selecione **Sim** para manter o seu acesso ou **Não** para remover o seu acesso.

    Se selecionar **Sim,** poderá ter de especificar uma justificação na caixa **Reason.**

    ![Aceder à página de revisão, mostrando a caixa Reason com texto de amostra](media/my-apps-portal/my-apps-portal-perform-access-reviews-reason-box.png)

7. Selecione **Submeter**.

    A sua análise de acesso está completa e regressa ao portal **My Apps.**

    >[!Note]
    >Pode alterar o seu acesso a qualquer momento até que o período de revisão de acesso termine. Se remover o seu acesso a uma aplicação ou grupo, não é removido imediatamente. A remoção ocorre quando o período de revisão de acesso termina ou quando um administrador fecha a revisão.

## <a name="next-steps"></a>Passos seguintes

- [Aceder e utilizar aplicativos no portal My Apps](my-apps-portal-end-user-access.md)
- [Alterar as informações de perfil](./my-account-portal-settings.md)
- [Ver e atualizar as informações relacionadas com os seus grupos](my-apps-portal-end-user-groups.md)