---
title: Configurar o serviço de Azure App de autenticação do Google
description: Saiba como configurar a autenticação do Google para seu aplicativo do serviço de aplicativo.
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/02/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 917fa87a0cd0f7b0615a5139a7c15311f866739a
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176972"
---
# <a name="configure-your-app-service-app-to-use-google-login"></a>Configurar seu aplicativo do serviço de aplicativo para usar o logon do Google

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este tópico mostra como configurar o serviço de Azure App para usar o Google como um provedor de autenticação.

Para concluir o procedimento neste tópico, você deve ter uma conta do Google que tenha um endereço de email verificado. Para criar uma nova conta do Google, aceda a [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302).

## <a name="register"> </a>Registrar seu aplicativo com o Google

1. Siga a documentação do Google na [entrada do Google para aplicativos do lado do servidor](https://developers.google.com/identity/sign-in/web/server-side-flow) para criar uma ID do cliente e um segredo do cliente. Não há necessidade de fazer nenhuma alteração de código. Basta usar as seguintes informações:
    - Para **origens JavaScript autorizadas**, use `https://<app-name>.azurewebsites.net` com o nome do seu aplicativo no *> \<app*.
    - Para **URI de redirecionamento autorizado**, use `https://<app-name>.azurewebsites.net/.auth/login/google/callback`.
1. Copie a ID do aplicativo e os valores de segredo do aplicativo.

    > [!IMPORTANT]
    > O segredo do aplicativo é uma credencial de segurança importante. Não compartilhe esse segredo com ninguém ou distribua-o em um aplicativo cliente.

## <a name="secrets"> </a>Adicionar informações do Google ao seu aplicativo

1. Na [Azure portal], vá para o aplicativo do serviço de aplicativo.
1. Selecione **configurações** > **autenticação/autorização**e verifique se a **autenticação do serviço de aplicativo** está **ativada**.
1. Selecione **Google**e cole os valores de ID do aplicativo e segredo do aplicativo que você obteve anteriormente. Habilite todos os escopos necessários para seu aplicativo.
1. Selecione **OK**.

   O serviço de aplicativo fornece autenticação, mas não restringe o acesso autorizado ao conteúdo e às APIs do site. Para obter mais informações, consulte [autorizar ou negar usuários](app-service-authentication-how-to.md#authorize-or-deny-users).

1. Adicional Para restringir o acesso ao site somente para usuários autenticados pelo Google, defina **a ação a ser tomada quando a solicitação não for autenticada** para o **Google**. Quando você define essa funcionalidade, seu aplicativo requer que todas as solicitações sejam autenticadas. Ele também redireciona todas as solicitações não autenticadas para o Google para autenticação.

    > [!CAUTION]
    > Restringir o acesso dessa maneira se aplica a todas as chamadas para seu aplicativo, o que pode não ser desejável para aplicativos que têm um home page publicamente disponível, como em muitos aplicativos de página única. Para tais aplicativos, **Permitir solicitações anônimas (nenhuma ação)** pode ser preferível para que o aplicativo inicie manualmente a autenticação em si. Para obter mais informações, consulte [fluxo de autenticação](overview-authentication-authorization.md#authentication-flow).

1. Selecione **Guardar**.

Agora você está pronto para usar o Google para autenticação em seu aplicativo.

## <a name="related-content"> </a>Passos seguintes

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure portal]: https://portal.azure.com/

