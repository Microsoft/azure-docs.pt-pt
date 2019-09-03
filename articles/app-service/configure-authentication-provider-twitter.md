---
title: Configurar a autenticação do Twitter-serviço de Azure App
description: Saiba como configurar a autenticação do Twitter para seu aplicativo do serviço de aplicativo.
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: d49b8bf9c62813023c1a1e06e0f8fc0d7809f48d
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232040"
---
# <a name="how-to-configure-your-app-service-application-to-use-twitter-login"></a>Como configurar seu aplicativo do serviço de aplicativo para usar o logon do Twitter
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este tópico mostra como configurar o serviço de Azure App para usar o Twitter como um provedor de autenticação.

Para concluir o procedimento neste tópico, você deve ter uma conta do Twitter que tenha um endereço de email e número de telefone verificados. Para criar uma nova conta do Twitter, acesse <a href="https://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">Twitter.com</a>.

## <a name="register"> </a>Registrar seu aplicativo com o Twitter
1. Faça logon no [Azure portal]e navegue até seu aplicativo. Copie a **URL**. Você vai usá-lo para configurar seu aplicativo do Twitter.
2. Navegue até o site de [desenvolvedores do Twitter] , entre com suas credenciais de conta do Twitter e clique em **criar novo aplicativo**.
3. Digite o **nome** e uma **Descrição** para o novo aplicativo. Cole a **URL** do seu aplicativo para o valor do **site** . Em seguida, para a **URL de retorno de chamada**, digite a URL do seu aplicativo do serviço `/.auth/login/aad/callback`de aplicativo e acrescente o caminho. Por exemplo, `https://contoso.azurewebsites.net/.auth/login/twitter/callback`. Verifique se você está usando o esquema HTTPS.
4. Na parte inferior da página, leia e aceite os termos. Em seguida, clique em **criar seu aplicativo do Twitter**. Os detalhes do aplicativo são exibidos.
5. Clique na guia **configurações** , marque **permitir que este aplicativo seja usado para entrar com o Twitter**e, em seguida, clique em **Atualizar configurações**.
6. Selecione a guia **chaves e tokens de acesso** . Anote os valores da **chave do consumidor (chave de API)** e **do segredo do consumidor (segredo da API)** .
   
   > [!NOTE]
   > O segredo do consumidor é uma credencial de segurança importante. Não compartilhe esse segredo com ninguém ou distribua-o com seu aplicativo.
   > 
   > 

## <a name="secrets"> </a>Adicionar informações do Twitter ao seu aplicativo
1. De volta ao [Azure portal], navegue até seu aplicativo. Clique em **configurações**e em **autenticação/autorização**.
2. Se o recurso de autenticação/autorização não estiver habilitado, ative a opção para **ativado**.
3. Clique em **Twitter**. Cole os valores de ID do aplicativo e segredo do aplicativo que você obteve anteriormente. Em seguida, clique em **OK**.
   
   ![][1]
   
   Por padrão, o serviço de aplicativo fornece autenticação, mas não restringe o acesso autorizado ao conteúdo e às APIs do site. Você deve autorizar os usuários no código do aplicativo.
4. Adicional Para restringir o acesso ao seu site somente para usuários autenticados pelo Twitter, defina **a ação a ser tomada quando a solicitação não for autenticada** no **Twitter**. Isso requer que todas as solicitações sejam autenticadas e todas as solicitações não autenticadas sejam redirecionadas para o Twitter para autenticação.

> [!NOTE]
> Restringir o acesso dessa maneira se aplica a todas as chamadas para seu aplicativo, o que pode não ser desejável para aplicativos que querem um home page publicamente disponível, como em muitos aplicativos de página única. Para tais aplicativos, **Permitir solicitações anônimas (nenhuma ação)** pode ser preferível, com o aplicativo iniciando o logon manualmente, conforme descrito [aqui](overview-authentication-authorization.md#authentication-flow).

5. Clique em **Guardar**.

Agora você está pronto para usar o Twitter para autenticação em seu aplicativo.

## <a name="related-content"> </a>Conteúdo relacionado
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Desenvolvedores do Twitter]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[Azure portal]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
