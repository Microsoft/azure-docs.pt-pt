---
title: Configure a autenticação do Twitter
description: Saiba como configurar a autenticação do Twitter como fornecedor de identidade para a sua aplicação App Service.
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.topic: article
ms.date: 02/28/2020
ms.custom: seodec18
ms.openlocfilehash: 794f671b36b5aeb9f19cf5d80e488500cedb1098
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2020
ms.locfileid: "78207149"
---
# <a name="configure-your-app-service-app-to-use-twitter-login"></a>Configure a sua app app Service para usar o login do Twitter

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este artigo mostra como configurar o Azure App Service para usar o Twitter como fornecedor de autenticação.

Para completar o procedimento neste artigo, precisa de uma conta no Twitter que tenha um endereço de e-mail verificado e número de telefone. Para criar uma nova conta no Twitter, vá a [twitter.com].

## <a name="register"> </a>Registe a sua aplicação no Twitter

1. Inscreva-se no [Portal do Azure] e vá à sua candidatura. Copie o seu **URL**. Vai usá-lo para configurar a sua aplicação do Twitter.
1. Vá ao site do [Desenvolvedores do Twitter] inscreva-se com as credenciais da sua conta do Twitter e selecione **Criar uma aplicação**.
1. Insira o nome da **App** e a descrição da **Aplicação** para a sua nova aplicação. Colhe o **URL** da sua aplicação no campo URL do **Site.** Na secção **DeCallback URLs,** introduza o URL HTTPS da sua app App Service e aperte o caminho `/.auth/login/twitter/callback`. Por exemplo, `https://contoso.azurewebsites.net/.auth/login/twitter/callback`.
1. Na parte inferior da página, digite pelo menos 100 caracteres em **Dizer-nos como esta aplicação será usada**e, em seguida, selecione **Criar**. Clique em **Criar** novamente no pop-up. Os detalhes da aplicação são apresentados.
1. Selecione o separador **Keys e Access Tokens.**

   Tome nota destes valores:
   - Chave de API
   - Chave secreta da API

   > [!NOTE]
   > A chave secreta da API é uma importante credencial de segurança. Não partilhe este segredo com ninguém nem o distribua com a sua aplicação.

## <a name="secrets"> </a>Adicione informações do Twitter à sua aplicação

1. Vá à sua aplicação no [Portal do Azure]
1. Selecione **Definições** > **Autenticação /Autorização,** e certifique-se de que a autenticação do serviço de **aplicações** está **a ser ressoada**.
1. Selecione **Twitter**.
1. Pasta nos valores `API key` e `API secret key` que obteve anteriormente.
1. Selecione **OK**.

   ![Screenshot das definições do Twitter da aplicação móvel][1]

   Por padrão, o Serviço de Aplicações fornece a autenticação, mas não restringe o acesso autorizado aos conteúdos e APIs do seu site. Tem de autorizar os utilizadores no seu código de aplicações.

1. (Opcional) Para restringir o acesso ao seu site a apenas utilizadores autenticados pelo Twitter, detete toda a Ação quando o **pedido não for autenticado** no **Twitter**. Ao definir esta funcionalidade, a sua aplicação requer que todos os pedidos sejam autenticados. Também redireciona todos os pedidos não autenticados para o Twitter para autenticação.

   > [!CAUTION]
   > Restringir o acesso desta forma aplica-se a todas as chamadas para a sua app, o que pode não ser desejável para aplicações que tenham uma página inicial disponível ao público, como em muitas aplicações de página única. Para tais aplicações, **permitir pedidos anónimos (nenhuma ação)** pode ser preferido para que a aplicação comece manualmente a autenticação em si. Para mais informações, consulte o [fluxo de autenticação](overview-authentication-authorization.md#authentication-flow).

1. Selecione **Guardar**.

Está agora pronto para usar o Twitter para autenticação na sua aplicação.

## <a name="related-content"> </a>Próximos passos

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Desenvolvedores do Twitter]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[twitter.com]: https://go.microsoft.com/fwlink/p/?LinkID=268287
[Portal do Azure]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
