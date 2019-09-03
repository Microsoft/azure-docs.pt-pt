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
ms.openlocfilehash: fcbb284a0807ef88c5f40a7c8b65398d45bf73d7
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232142"
---
# <a name="how-to-configure-your-app-service-application-to-use-google-login"></a>Como configurar seu aplicativo do serviço de aplicativo para usar o logon do Google
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este tópico mostra como configurar o serviço de Azure App para usar o Google como um provedor de autenticação.

Para concluir o procedimento neste tópico, você deve ter uma conta do Google que tenha um endereço de email verificado. Para criar uma nova conta do Google, aceda a [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302).

## <a name="register"> </a>Registrar seu aplicativo com o Google
1. Siga a documentação do Google na [entrada do Google para aplicativos do lado do servidor](https://developers.google.com/identity/sign-in/web/server-side-flow) para criar uma ID do cliente e um segredo do cliente, com as seguintes informações (sem necessidade de fazer nenhuma alteração de código):
    - Para **origens JavaScript autorizadas**, `https://<app-name>.azurewebsites.net` use com o nome do seu aplicativo em  *\<> do nome do aplicativo*.
    - Para **URI**de redirecionamento `https://<app-name>.azurewebsites.net/.auth/login/google/callback`autorizado, use.
1. Depois que a ID do cliente e os segredos do cliente forem criados, copie seus valores.

    > [!IMPORTANT]
    > O segredo do cliente é uma credencial de segurança importante. Não compartilhe esse segredo com ninguém ou distribua-o em um aplicativo cliente.


## <a name="secrets"> </a>Adicionar informações do Google ao seu aplicativo
1. Na [Azure portal], navegue até o aplicativo do serviço de aplicativo. No menu à esquerda, selecione **autenticação/autorização**.
2. Se o recurso de autenticação/autorização não estiver habilitado, ative a opção para **ativado**.
3. Clique em **Google**. Cole os valores de ID do aplicativo e segredo do aplicativo que você obteve anteriormente e, opcionalmente, habilite todos os escopos que seu aplicativo requer. Em seguida, clique em **OK**.

   O serviço de aplicativo fornece autenticação, mas não restringe o acesso autorizado ao conteúdo e às APIs do site. Para obter mais informações, consulte [autorizar ou negar usuários](app-service-authentication-how-to.md#authorize-or-deny-users).
4. Adicional Para restringir o acesso ao seu site somente para usuários autenticados pelo Google, defina **a ação a ser tomada quando a solicitação não for autenticada** no **Google**. Isso requer que todas as solicitações sejam autenticadas e todas as solicitações não autenticadas sejam redirecionadas para o Google para autenticação.

    > [!NOTE]
    > Restringir o acesso dessa maneira se aplica a todas as chamadas para seu aplicativo, o que pode não ser desejável para aplicativos que querem um home page publicamente disponível, como em muitos aplicativos de página única. Para tais aplicativos, **Permitir solicitações anônimas (nenhuma ação)** pode ser preferível, com o aplicativo iniciando o logon manualmente, conforme descrito [aqui](overview-authentication-authorization.md#authentication-flow).
    
5. Clique em **Guardar**.

Agora você está pronto para usar o Google para autenticação em seu aplicativo.

## <a name="related-content"> </a>Conteúdo relacionado
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure portal]: https://portal.azure.com/

