---
title: Configure a autenticação do Google
description: Saiba como configurar a autenticação do Google como fornecedor de identidade para a sua aplicação App Service.
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.topic: article
ms.date: 09/02/2019
ms.custom: seodec18
ms.openlocfilehash: 81ce3e393d308323c8d5a3d688c16c9b45e7be9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74670818"
---
# <a name="configure-your-app-service-app-to-use-google-login"></a>Configure a sua app app Service para usar o login do Google

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este tópico mostra-lhe como configurar o Azure App Service para utilizar o Google como fornecedor de autenticação.

Para completar o procedimento neste tópico, deve ter uma conta no Google que tenha um endereço de e-mail verificado. Para criar uma nova conta do Google, aceda a [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302).

## <a name="register-your-application-with-google"></a><a name="register"> </a>Registe a sua aplicação no Google

1. Siga a documentação do Google no [Google Sign-In para aplicações do lado do servidor](https://developers.google.com/identity/sign-in/web/server-side-flow) para criar um ID do cliente e segredo de cliente. Não há necessidade de fazer alterações de código. Basta utilizar as seguintes informações:
    - Para **origens JavaScript autorizadas,** utilize `https://<app-name>.azurewebsites.net` com o nome da sua aplicação no * \<nome da aplicação>*.
    - Para **redirecionar URI autorizado,** utilize `https://<app-name>.azurewebsites.net/.auth/login/google/callback`.
1. Copie o ID da app e os valores secretos da App.

    > [!IMPORTANT]
    > O segredo da App é uma importante credencial de segurança. Não partilhe este segredo com ninguém nem o distribua dentro de uma aplicação de cliente.

## <a name="add-google-information-to-your-application"></a><a name="secrets"> </a>Adicione informações do Google à sua aplicação

1. No [portal Azure,]vá à sua app App Service.
1. Selecione **Definições** > **Autenticação / Autorização,** e certifique-se de que a autenticação do serviço de aplicações está **a ser ressoada**. **App Service Authentication**
1. Selecione **Google**, em seguida, colá-se nos valores de ID da app e App Secret que obteve anteriormente. Ative todos os âmbitos necessários pela sua aplicação.
1. Selecione **OK**.

   O Serviço de Aplicações fornece a autenticação, mas não restringe o acesso autorizado aos conteúdos e APIs do seu site. Para mais informações, consulte [Autorizar ou negar aos utilizadores](app-service-authentication-how-to.md#authorize-or-deny-users).

1. (Opcional) Para restringir o acesso ao site apenas aos utilizadores autenticados pela Google, detete a **Action totake quando o pedido não for autenticado** no **Google**. Ao definir esta funcionalidade, a sua aplicação requer que todos os pedidos sejam autenticados. Também redireciona todos os pedidos não autenticados para a Google para autenticação.

    > [!CAUTION]
    > Restringir o acesso desta forma aplica-se a todas as chamadas para a sua app, o que pode não ser desejável para aplicações que tenham uma página inicial disponível ao público, como em muitas aplicações de página única. Para tais aplicações, **permitir pedidos anónimos (nenhuma ação)** pode ser preferido para que a aplicação comece manualmente a autenticação em si. Para mais informações, consulte o [fluxo de autenticação](overview-authentication-authorization.md#authentication-flow).

1. Selecione **Guardar**.

Está agora pronto para utilizar o Google para autenticação na sua aplicação.

## <a name="next-steps"></a><a name="related-content"> </a>Passos seguintes

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[Portal Azure]: https://portal.azure.com/

