---
title: Configure a autenticação do Facebook
description: Saiba como configurar a autenticação do Facebook como fornecedor de identidade para o seu Serviço de Aplicações ou aplicação Azure Functions.
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.topic: article
ms.date: 06/06/2019
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: b6aad323c0d6fa8f59c9fad203640c477b162503
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "80519965"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-use-facebook-login"></a>Configure o seu Serviço de Aplicações ou app Azure Functions para usar o login do Facebook

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este artigo mostra como configurar o Azure App Service ou Azure Functions para usar o Facebook como fornecedor de autenticação.

Para completar o procedimento neste artigo, precisa de uma conta no Facebook que tenha um endereço de e-mail verificado e um número de telemóvel. Para criar uma nova conta no Facebook, vá a [facebook.com].

## <a name="register-your-application-with-facebook"></a><a name="register"> </a>Registe a sua candidatura no Facebook

1. Vá ao site do [Facebook Developers] e inscreva-se com as credenciais da sua conta do Facebook.

   Se não tiver uma conta no Facebook para desenvolvedores, **selecione Get Started** e siga as etapas de registo.
1. Selecione **as minhas**  >  **aplicações Adicionar nova aplicação.**
1. No campo **Display Name:**
   1. Digite um nome único para a sua aplicação.
   1. Forneça o seu **Email de Contacto.**
   1. Selecione **Criar ID de aplicação.**
   1. Complete a verificação de segurança.

   Abre o painel de desenvolvedores da sua nova aplicação do Facebook.
1. Selecione **o Início de**  >  **Sessão do Facebook** do Dashboard  >  **Configurar** a  >  **Web**.
1. Na navegação à esquerda no **Facebook Login**, selecione **Definições**.
1. No campo **DE REorção de IU** válido, insira `https://<app-name>.azurewebsites.net/.auth/login/facebook/callback` . Lembre-se de substituir `<app-name>` pelo nome da sua aplicação Azure App Service.
1. Selecione **Guardar alterações**.
1. No painel esquerdo, selecione **Definições**  >  **Básicas**. 
1. No campo **App Secret,** selecione **Show**. Copie os valores do **ID** da App e **da App Secret.** Usa-os mais tarde para configurar a sua aplicação de Serviço de Aplicações em Azure.

   > [!IMPORTANT]
   > O segredo da aplicação é uma importante credencial de segurança. Não partilhe este segredo com ninguém nem o distribua dentro de uma aplicação ao cliente.
   >

1. A conta do Facebook que usou para registar a aplicação é administradora da aplicação. Neste momento, apenas os administradores podem inscrever-se nesta aplicação.

   Para autenticar outras contas do Facebook, selecione **App Review** e permita tornar **\<your-app-name> público** o acesso do público em geral à aplicação através da autenticação do Facebook.

## <a name="add-facebook-information-to-your-application"></a><a name="secrets"> </a>Adicione informações do Facebook à sua aplicação

1. Inscreva-se no [portal Azure] e navegue para a sua aplicação De Serviço de Aplicações.
1. Selecione **Definições**  >  **Autenticação / Autorização**, e certifique-se de que a **autenticação do serviço de aplicações** está **acesa**.
1. Selecione **o Facebook** e, em seguida, cole nos valores de ID da App e App Secret que obteve anteriormente. Ativar todos os âmbitos necessários à sua aplicação.
1. Selecione **OK**.

   ![Screenshot das definições do Facebook da aplicação móvel][0]

    Por predefinição, o Serviço de Aplicações fornece autenticação, mas não restringe o acesso autorizado ao conteúdo do seu site e APIs. Tem de autorizar os utilizadores no seu código de aplicação.
1. (Opcional) Para restringir apenas o acesso aos utilizadores autenticados pelo Facebook, detena **ação a tomar quando o pedido não for autenticado** no **Facebook**. Ao definir esta funcionalidade, a sua aplicação requer que todos os pedidos sejam autenticados. Também redireciona todos os pedidos não autenticados para o Facebook para autenticação.

   > [!CAUTION]
   > Restringir o acesso desta forma aplica-se a todas as chamadas para a sua app, o que pode não ser desejável para aplicações que tenham uma página inicial disponível ao público, como em muitas aplicações de página única. Para tais aplicações, **permitir pedidos anónimos (nenhuma ação)** pode ser preferido para que a aplicação comece manualmente a autenticação em si. Para mais informações, consulte [o fluxo de autenticação.](overview-authentication-authorization.md#authentication-flow)

1. Selecione **Guardar**.

Está agora pronto para usar o Facebook para autenticação na sua aplicação.

## <a name="next-steps"></a><a name="related-content"> </a>Passos seguintes

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Desenvolvedores do Facebook]: https://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: https://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Portal do Azure]: https://portal.azure.com/
