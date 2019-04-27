---
title: Configurar a autenticação do Facebook - serviço de aplicações do Azure
description: Saiba como configurar a autenticação do Facebook para a sua aplicação de serviços de aplicações.
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: b6b4f062-fcb4-47b3-b75a-ec4cb51a62fd
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: f37a0c9e4c664ac9631a0a07fa6f114e62939845
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60852620"
---
# <a name="how-to-configure-your-app-service-application-to-use-facebook-login"></a>Como configurar a sua aplicação do Serviço de Aplicações para utilizar o início de sessão do Facebook
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este tópico mostra-lhe como configurar o serviço de aplicações do Azure para utilizar o Facebook como um fornecedor de autenticação.

Para concluir o procedimento neste tópico, tem de ter uma conta do Facebook que tenha um endereço de correio eletrónico verificado e um número de telefone celular. Para criar uma nova conta do Facebook, aceda a [facebook.com].

## <a name="register"> </a>Registar a sua aplicação com o Facebook
1. Inicie sessão para o [portal do Azure]e navegue para a sua aplicação. Copiar seus **URL**. Irá utilizá-lo a configurar a aplicação do Facebook.
2. Na outra janela do browser, navegue para o [desenvolvedores de Facebook] credenciais da conta de Web site e inicie sessão com o Facebook.
3. (Opcional) Se ainda não registou, clique em **aplicações** > **registrar como um desenvolvedor**, em seguida, aceite a política e siga os passos de registo.
4. Clique em **meus aplicativos** > **adicionar uma nova aplicação**.
5. Na **nome a apresentar**, escreva um nome exclusivo para a sua aplicação. Também fornecer seus **E-Mail de contacto**e, em seguida, clique em **criar ID da aplicação** e concluir a verificação de segurança. Isto leva-o para o dashboard de desenvolvedor para a nova aplicação de Facebook.
6. Sob **início de sessão do Facebook**, clique em **configurar**e, em seguida, escolha **definições** no painel de navegação esquerda sob **início de sessão do Facebook**.
7. Adicionar a sua aplicação **URI de redirecionamento** ao **URIs de redirecionamento OAuth válido**, em seguida, clique em **guardar alterações**.
   
   > [!NOTE]
   > Seu URI é o URL do seu aplicativo acrescentado com o caminho de redirecionamento */.auth/login/facebook/callback*. Por exemplo, `https://contoso.azurewebsites.net/.auth/login/facebook/callback`. Certifique-se de que está a utilizar o esquema HTTPS.
   > 
   > 
8. No painel de navegação esquerdo, clique em **configurações** > **básica**. Sobre o **segredo da aplicação** campo, clique em **mostrar**, forneça a palavra-passe, se solicitado, em seguida, tome nota dos valores das **ID da aplicação** e **segredo da aplicação** . Utilize estes mais tarde para configurar a sua aplicação no Azure.
   
   > [!IMPORTANT]
   > O segredo de aplicação é uma credencial de segurança importantes. Não partilhe este segredo com qualquer pessoa ou distribuí-la dentro de um aplicativo de cliente.
   > 
   > 
9. A conta do Facebook que foi utilizada para registar a aplicação é um administrador da aplicação. Neste momento, apenas os administradores podem iniciar sessão nesta aplicação. Para autenticar a outras contas do Facebook, clique em **revisão da aplicação** e ative **tornar \<nome da aplicação > pública** para ativar o acesso público geral, usando autenticação do Facebook.

## <a name="secrets"> </a>Adicionar informações de Facebook à sua aplicação
1. De volta a [portal do Azure], navegue para a sua aplicação. Clique em **configurações** > **autenticação / autorização**e certifique-se de que **autenticação do serviço de aplicações** é **em**.
2. Clique em **Facebook**, cole os valores de ID de aplicação e o segredo de aplicação que obteve anteriormente, opcionalmente, ativar qualquer âmbitos necessários para seu aplicativo, em seguida, clique em **OK**.
   
    ![][0]
   
    Por predefinição, o serviço de aplicações fornece autenticação mas não restringe o acesso autorizado para o conteúdo do site e as APIs. Tem de autorizar os utilizadores no seu código de aplicação.
3. (Opcional) Para restringir o acesso ao seu site para apenas os utilizadores autenticada pelo Facebook, defina **ação a tomar quando o pedido não é autenticado** ao **Facebook**. Isto requer que todos os pedidos de ser autenticados e todos os pedidos não autenticados são redirecionados para Facebook para autenticação.
4. Quando terminar a configuração da autenticação, clique em **guardar**.

Agora está pronto para utilizar o Facebook para autenticação na sua aplicação.

## <a name="related-content"> </a>Conteúdo relacionado
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Desenvolvedores de Facebook]: https://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: https://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Portal do Azure]: https://portal.azure.com/
