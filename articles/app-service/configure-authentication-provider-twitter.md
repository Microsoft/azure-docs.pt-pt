---
title: Configurar a autenticação do Twitter - serviço de aplicações do Azure
description: Saiba como configurar a autenticação do Twitter na sua aplicação de serviços de aplicações.
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: c6dc91d7-30f6-448c-9f2d-8e91104cde73
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 51a2ac93fd2d863855c820ba147418c5397c2a89
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60851556"
---
# <a name="how-to-configure-your-app-service-application-to-use-twitter-login"></a>Como configurar a sua aplicação de serviço de aplicações para utilizar o início de sessão do Twitter
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este tópico mostra-lhe como configurar o serviço de aplicações do Azure para utilizar o Twitter como um fornecedor de autenticação.

Para concluir o procedimento neste tópico, tem de ter uma conta do Twitter que tem um número de endereço e telefone de correio eletrónico verificado. Para criar uma nova conta do Twitter, aceda a <a href="https://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>.

## <a name="register"> </a>Registar a sua aplicação com o Twitter
1. Inicie sessão para o [portal do Azure]e navegue para a sua aplicação. Copiar seus **URL**. Irá utilizá-lo a configurar a aplicação do Twitter.
2. Navegue para o [Os desenvolvedores do twitter] Web site, inicie sessão com as credenciais da sua conta do Twitter e clique em **criar nova aplicação**.
3. Escreva o **Name** e uma **Descrição** para a nova aplicação. Colar em seu aplicativo **URL** para o **site** valor. Em seguida, para o **URL de chamada de retorno**, cole a **URL de retorno de chamada** que copiou anteriormente. Este é o gateway de aplicação móvel acrescentado com o caminho */.auth/login/twitter/callback*. Por exemplo, `https://contoso.azurewebsites.net/.auth/login/twitter/callback`. Certifique-se de que está a utilizar o esquema HTTPS.
4. Na parte inferior da página, leia e aceite os termos. Em seguida, clique em **criar a sua aplicação do Twitter**. Isso registra a aplicação apresenta os detalhes da aplicação.
5. Clique no **definições** separador, verificação **permitir que esta aplicação a ser utilizado para iniciar sessão com o Twitter**, em seguida, clique em **definições de atualização de**.
6. Selecione o **chaves e Tokens de acesso** separador. Tome nota dos valores das **chave de consumidor (chave de API)** e **segredo de consumidor (segredo de API)** .
   
   > [!NOTE]
   > O segredo de consumidor é uma credencial de segurança importantes. Não partilhe este segredo com qualquer pessoa ou distribuí-la com a sua aplicação.
   > 
   > 

## <a name="secrets"> </a>Adicionar informações de Twitter à sua aplicação
1. De volta a [portal do Azure], navegue para a sua aplicação. Clique em **configurações**e, em seguida **autenticação / autorização**.
2. Se a autenticação / autorização funcionalidade não está ativada, ative a opção **no**.
3. Clique em **Twitter**. Cole o ID da aplicação e o segredo de aplicação valores que obteve anteriormente. Em seguida, clique em **OK**.
   
   ![][1]
   
   Por predefinição, o serviço de aplicações fornece autenticação mas não restringe o acesso autorizado para o conteúdo do site e as APIs. Tem de autorizar os utilizadores no seu código de aplicação.
4. (Opcional) Para restringir o acesso ao seu site para apenas os utilizadores autenticados pelo Twitter, defina **ação a tomar quando o pedido não é autenticado** ao **Twitter**. Isto requer que todos os pedidos de ser autenticados e todos os pedidos não autenticados são redirecionados para o Twitter para autenticação.
5. Clique em **Guardar**.

Agora está pronto para utilizar o Twitter para autenticação na sua aplicação.

## <a name="related-content"> </a>Conteúdo relacionado
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Os desenvolvedores do twitter]: https://go.microsoft.com/fwlink/p/?LinkId=268300
[Portal do Azure]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
