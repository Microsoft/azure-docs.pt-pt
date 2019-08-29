---
title: Configurar o serviço de Azure App de autenticação do Google
description: Saiba como configurar a autenticação do Google para seu aplicativo de serviços de aplicativos.
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 4e28f4e330fa24476b717334dfc6d3265640c62a
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70088216"
---
# <a name="how-to-configure-your-app-service-application-to-use-google-login"></a>Como configurar seu aplicativo do serviço de aplicativo para usar o logon do Google
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este tópico mostra como configurar o serviço de Azure App para usar o Google como um provedor de autenticação.

Para concluir o procedimento neste tópico, você deve ter uma conta do Google que tenha um endereço de email verificado. Para criar uma nova conta do Google, aceda a [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302).

## <a name="register"> </a>Registrar seu aplicativo com o Google
1. Faça logon no [Azure portal]e navegue até seu aplicativo. Copie a **URL**, que você usará posteriormente para configurar seu aplicativo do Google.
2. Navegue até o site de [APIs do Google](https://go.microsoft.com/fwlink/p/?LinkId=268303) , entre com suas credenciais de conta do Google, clique em **criar projeto**, forneça um **nome de projeto**e, em seguida, clique em **criar**.
3. Depois que o projeto for criado, selecione-o. No painel do projeto, clique em **ir para APIs visão geral**.
4. Selecione **habilitar APIs e serviços**. Pesquise **API do Google +** e selecione-a. Em seguida, clique em **habilitar**.
5. Na tela de navegação esquerda, **credenciais** > de**consentimento do OAuth**, selecione seu endereço de **email**, insira um **nome de produto**e clique em **salvar**.
6. Na guia **credenciais** , clique em **criar credenciais** > **ID do cliente OAuth**.
7. Na tela "criar ID do cliente", selecione **aplicativo Web**.
8. Cole a **URL** do serviço de aplicativo que você copiou anteriormente em **origens do JavaScript autorizadas**e cole o URI de redirecionamento no URI de redirecionamento **autorizado**. O URI de redirecionamento é a URL do seu aplicativo acrescentada com o caminho */.auth/login/Google/callback*. Por exemplo, `https://contoso.azurewebsites.net/.auth/login/google/callback`. Verifique se você está usando o esquema HTTPS. Em seguida, clique em **Criar**.
9. Na próxima tela, anote os valores da ID do cliente e do segredo do cliente.

    > [!IMPORTANT]
    > O segredo do cliente é uma credencial de segurança importante. Não compartilhe esse segredo com ninguém ou distribua-o em um aplicativo cliente.


## <a name="secrets"> </a>Adicionar informações do Google ao seu aplicativo
1. De volta ao [Azure portal], navegue até seu aplicativo. Clique em **configurações**e em **autenticação/autorização**.
2. Se o recurso de autenticação/autorização não estiver habilitado, ative a opção para **ativado**.
3. Clique em **Google**. Cole os valores de ID do aplicativo e segredo do aplicativo que você obteve anteriormente e, opcionalmente, habilite todos os escopos que seu aplicativo requer. Em seguida, clique em **OK**.
   
   ![][1]
   
   Por padrão, o serviço de aplicativo fornece autenticação, mas não restringe o acesso autorizado ao conteúdo e às APIs do site. Você deve autorizar os usuários no código do aplicativo.
4. Adicional Para restringir o acesso ao seu site somente para usuários autenticados pelo Google, defina **a ação a ser tomada quando a solicitação não for autenticada** no **Google**. Isso requer que todas as solicitações sejam autenticadas e todas as solicitações não autenticadas sejam redirecionadas para o Google para autenticação.

> [!CAUTION]
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

