---
title: Configure a autenticação do Google
description: Saiba como configurar a autenticação do Google como fornecedor de identidade para o seu Serviço de Aplicações ou aplicação Azure Functions.
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.topic: article
ms.date: 09/02/2019
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: e8a9fbe6072f3628d755ad3ad5aa5a623fc3ab23
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "80519934"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-google-login"></a>Configure o seu Serviço de Aplicações ou app Azure Functions para usar o login do Google

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este tópico mostra-lhe como configurar o Azure App Service ou Azure Functions para usar o Google como um fornecedor de autenticação.

Para completar o procedimento neste tópico, tem de ter uma conta google que tenha um endereço de e-mail verificado. Para criar uma nova conta do Google, aceda a [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302).

## <a name="register-your-application-with-google"></a><a name="register"> </a>Registe a sua aplicação com o Google

1. Siga a documentação do Google no [Google Sign-In para aplicações do lado do servidor](https://developers.google.com/identity/sign-in/web/server-side-flow) para criar um ID de cliente e segredo de cliente. Não há necessidade de fazer alterações de código. Basta utilizar as seguintes informações:
    - Para **Origens JavaScript Autorizadas,** utilize `https://<app-name>.azurewebsites.net` com o nome da sua aplicação em *\<app-name>* .
    - Para **Redirecionar autorizado URI,** utilize `https://<app-name>.azurewebsites.net/.auth/login/google/callback` .
1. Copie o ID da aplicação e os valores secretos da App.

    > [!IMPORTANT]
    > O segredo da App é uma importante credencial de segurança. Não partilhe este segredo com ninguém nem o distribua dentro de uma aplicação ao cliente.

## <a name="add-google-information-to-your-application"></a><a name="secrets"> </a>Adicione informações do Google à sua aplicação

1. No [portal Azure, aceda]à sua aplicação De Serviço de Aplicações.
1. Selecione **Definições**  >  **Autenticação / Autorização**, e certifique-se de que a **autenticação do serviço de aplicações** está **acesa**.
1. Selecione **o Google,** em seguida, cole nos valores de ID da App e App Secret que obteve anteriormente. Ativar todos os âmbitos necessários à sua aplicação.
1. Selecione **OK**.

   O Serviço de Aplicações fornece autenticação, mas não restringe o acesso autorizado ao conteúdo do seu site e APIs. Para mais informações, consulte [Autorizar ou negar utilizadores.](app-service-authentication-how-to.md#authorize-or-deny-users)

1. (Opcional) Para restringir o acesso do site apenas aos utilizadores autenticados pela Google, desemote **a ação a tomar quando o pedido não for autenticado** no **Google.** Ao definir esta funcionalidade, a sua aplicação requer que todos os pedidos sejam autenticados. Também redireciona todos os pedidos não autenticados para a Google para autenticação.

    > [!CAUTION]
    > Restringir o acesso desta forma aplica-se a todas as chamadas para a sua app, o que pode não ser desejável para aplicações que tenham uma página inicial disponível ao público, como em muitas aplicações de página única. Para tais aplicações, **permitir pedidos anónimos (nenhuma ação)** pode ser preferido para que a aplicação comece manualmente a autenticação em si. Para mais informações, consulte [o fluxo de autenticação.](overview-authentication-authorization.md#authentication-flow)

1. Selecione **Guardar**.

Está agora pronto para usar o Google para autenticação na sua aplicação.

## <a name="next-steps"></a><a name="related-content"> </a>Passos seguintes

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[Portal do Azure]: https://portal.azure.com/

