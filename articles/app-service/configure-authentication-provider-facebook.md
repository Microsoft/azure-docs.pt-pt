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
ms.date: 06/06/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: e91d55c29d325301b8ac70ddc63fb408961fbb2c
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66742966"
---
# <a name="how-to-configure-your-app-service-application-to-use-facebook-login"></a>Como configurar a sua aplicação do Serviço de Aplicações para utilizar o início de sessão do Facebook
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este tópico mostra-lhe como configurar o serviço de aplicações do Azure para utilizar o Facebook como um fornecedor de autenticação.

Para concluir o procedimento neste tópico, tem de ter uma conta do Facebook que tenha um endereço de correio eletrónico verificado e um número de telefone celular. Para criar uma nova conta do Facebook, aceda a [facebook.com].

## <a name="register"> </a>Registar a sua aplicação com o Facebook
1. Navegue para o [desenvolvedores de Facebook] credenciais da conta de Web site e inicie sessão com o Facebook.
3. (Opcional) Se não tiver um Facebook para a conta de desenvolvedores, clique em **começar** e siga os passos de registo.
4. Clique em **meus aplicativos** > **Adicionar nova aplicação**.
5. Na **nome a apresentar**, escreva um nome exclusivo para a sua aplicação. Também fornecer seus **E-Mail de contacto**e, em seguida, clique em **criar ID da aplicação** e concluir a verificação de segurança. Isto leva-o para o dashboard de desenvolvedor para a nova aplicação de Facebook.
6. Clique em **Dashboard** > **início de sessão do Facebook** > **configurar** > **Web**.
1. No painel de navegação esquerda sob **início de sessão do Facebook**, clique em **definições**.
1. Na **URIs de redirecionamento OAuth válido**, tipo `https://<app-name>.azurewebsites.net/.auth/login/facebook/callback` e substitua  *\<nome da aplicação >* com o nome da sua aplicação de serviço de aplicações do Azure. Clique em **guardar alterações**.
8. No painel de navegação esquerdo, clique em **configurações** > **básica**. Sobre o **segredo da aplicação** campo, clique em **mostrar**. Copie os valores da **ID da aplicação** e **segredo da aplicação**. Utilize estes mais tarde para configurar a sua aplicação de serviço de aplicações no Azure.
   
   > [!IMPORTANT]
   > O segredo de aplicação é uma credencial de segurança importantes. Não partilhe este segredo com qualquer pessoa ou distribuí-la dentro de um aplicativo de cliente.
   > 
   > 
9. A conta do Facebook que foi utilizada para registar a aplicação é um administrador da aplicação. Neste momento, apenas os administradores podem iniciar sessão nesta aplicação. Para autenticar a outras contas do Facebook, clique em **revisão da aplicação** e ative **tornar \<nome da aplicação > pública** para ativar o acesso público geral, usando autenticação do Facebook.

## <a name="secrets"> </a>Adicionar informações de Facebook à sua aplicação
1. Inicie sessão para o [portal do Azure] e navegue para a aplicação de serviço de aplicações. Clique em **configurações** > **autenticação / autorização**e certifique-se de que **autenticação do serviço de aplicações** é **em**.
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
