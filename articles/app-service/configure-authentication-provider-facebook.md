---
title: Configurar autenticação do Facebook – serviço de Azure App
description: Saiba como configurar a autenticação do Facebook para seu aplicativo de serviços de aplicativos.
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
ms.openlocfilehash: c9767ff1e6f0b31270f37842cf99d71cab561505
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69033856"
---
# <a name="how-to-configure-your-app-service-application-to-use-facebook-login"></a>Como configurar a sua aplicação do Serviço de Aplicações para utilizar o início de sessão do Facebook
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este tópico mostra como configurar o serviço de Azure App para usar o Facebook como um provedor de autenticação.

Para concluir o procedimento neste tópico, você deve ter uma conta do Facebook que tenha um endereço de email verificado e um número de telefone celular. Para criar uma nova conta do Facebook, acesse [Facebook.com].

## <a name="register"> </a>Registrar seu aplicativo com o Facebook
1. Navegue até o site de [desenvolvedores do Facebook] e entre com suas credenciais de conta do Facebook.
3. Adicional Se você não tiver uma conta do Facebook para desenvolvedores, clique em introdução e siga as etapas de registro.
4. Clique em **meus aplicativos** > **Adicionar novo aplicativo**.
5. Em **nome de exibição**, digite um nome exclusivo para seu aplicativo. Forneça também seu **email de contato**e, em seguida, clique em **criar ID do aplicativo** e conclua a verificação de segurança. Isso o levará para o painel do desenvolvedor do seu novo aplicativo do Facebook.
6. Clique **em painel** > **de** > logon do Facebook configurar Web. > 
1. No painel de navegação à esquerda, em **logon do Facebook**, clique em **configurações**.
1. Em **URIs**de redirecionamento OAuth `https://<app-name>.azurewebsites.net/.auth/login/facebook/callback` válidos, digite e substitua  *\<o nome de aplicativo >* pelo nome do seu aplicativo de serviço de Azure app. Clique em **salvar alterações**.
8. No painel de navegação à esquerda, clique em **configurações** > **básico**. No campo **segredo do aplicativo** , clique em **Mostrar**. Copie os valores da **ID do aplicativo** e do **segredo do aplicativo**. Você os usará posteriormente para configurar seu aplicativo do serviço de aplicativo no Azure.
   
   > [!IMPORTANT]
   > O segredo do aplicativo é uma credencial de segurança importante. Não compartilhe esse segredo com ninguém ou distribua-o em um aplicativo cliente.
   > 
   > 
9. A conta do Facebook que foi usada para registrar o aplicativo é um administrador do aplicativo. Neste ponto, somente os administradores podem entrar nesse aplicativo. Para autenticar outras contas do Facebook, clique em **revisão do aplicativo** e habilite **tornar \<seu nome de aplicativo > público** para habilitar o acesso público geral usando a autenticação do Facebook.

## <a name="secrets"> </a>Adicionar informações do Facebook ao seu aplicativo
1. Entre no [Azure portal] e navegue até o aplicativo do serviço de aplicativo. Clique em **configurações** > **autenticação/autorização**e verifique se a **autenticação do serviço de aplicativo** está **ativada**.
2. Clique em **Facebook**, Cole os valores de ID do aplicativo e segredo do aplicativo que você obteve anteriormente, opcionalmente, habilite todos os escopos necessários para seu aplicativo e clique em **OK**.
   
    ![][0]
   
    Por padrão, o serviço de aplicativo fornece autenticação, mas não restringe o acesso autorizado ao conteúdo e às APIs do site. Você deve autorizar os usuários no código do aplicativo.
3. Adicional Para restringir o acesso ao seu site somente para os usuários autenticados pelo Facebook, defina **a ação a ser tomada quando a solicitação não for autenticada** no **Facebook**. Isso requer que todas as solicitações sejam autenticadas e todas as solicitações não autenticadas sejam redirecionadas para o Facebook para autenticação.
 
> [!CAUTION]
> Restringir o acesso dessa maneira se aplica a todas as chamadas para seu aplicativo, o que pode não ser desejável para aplicativos que querem um home page publicamente disponível, como em muitos aplicativos de página única. Para tais aplicativos, **Permitir solicitações anônimas (nenhuma ação)** pode ser preferível, com o aplicativo iniciando o logon manualmente, conforme descrito [aqui](overview-authentication-authorization.md#authentication-flow).

4. Quando terminar de configurar a autenticação, clique em **salvar**.

Agora você está pronto para usar o Facebook para autenticação em seu aplicativo.

## <a name="related-content"> </a>Conteúdo relacionado
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Desenvolvedores do Facebook]: https://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: https://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure portal]: https://portal.azure.com/
