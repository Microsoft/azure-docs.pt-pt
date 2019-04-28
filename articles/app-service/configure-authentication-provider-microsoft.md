---
title: Configurar a autenticação de Account Microsoft - App Service do Azure
description: Saiba como configurar a autenticação de Account Microsoft para a sua aplicação de serviços de aplicações.
author: mattchenderson
services: app-service
documentationcenter: ''
manager: syntaxc4
editor: ''
ms.assetid: ffbc6064-edf6-474d-971c-695598fd08bf
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: e3da856efd7d44f15f9de27c9e38375d40dc211d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60850958"
---
# <a name="how-to-configure-your-app-service-application-to-use-microsoft-account-login"></a>Como configurar a sua aplicação de serviço de aplicações para utilizar o Microsoft Account login
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este tópico mostra-lhe como configurar o serviço de aplicações do Azure para utilizar o Microsoft Account como um fornecedor de autenticação. 

## <a name="register-microsoft-account"> </a>Registar a sua aplicação com a conta Microsoft
1. Inicie sessão para o [portal do Azure]e navegue para a sua aplicação. Copiar seus **URL**, que utilizará mais tarde para configurar a sua aplicação com a Microsoft Account.
2. Navegue para o [As Minhas Aplicações] página no Microsoft Account Developer Center e faça logon com sua conta Microsoft, se necessário.
3. Clique em **adicionar uma aplicação**, em seguida, escreva um nome de aplicação e clique em **criar**.
4. Anote o **ID da aplicação**, como irá precisar dele mais tarde. 
5. Em "Plataformas", clique em **adicionar plataforma** e selecione "Web".
6. Em "URIs de redirecionamento" fornecer o ponto final para a sua aplicação, em seguida, clique em **guardar**. 
   
   > [!NOTE]
   > Seu URI é o URL do seu aplicativo acrescentado com o caminho de redirecionamento */.auth/login/microsoftaccount/callback*. Por exemplo, `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`.   
   > Certifique-se de que está a utilizar o esquema HTTPS.
   
7. Em "Segredos da aplicação", clique em **gerar nova palavra-passe**. Tome nota do valor que é apresentada. Depois de sair da página, não será novamente apresentado.

    > [!IMPORTANT]
    > A palavra-passe é uma credencial de segurança importantes. Não partilhe a palavra-passe com qualquer pessoa ou distribuí-la dentro de um aplicativo de cliente.
    
8. Clicar em **Guardar**

## <a name="secrets"> </a>Adicionar informações Account Microsoft à sua aplicação de serviço de aplicações
1. De volta a [portal do Azure], navegue até à sua aplicação, clique em **definições** > **autenticação / autorização**.
2. Se a autenticação / autorização funcionalidade não está ativada, mudá-la **no**.
3. Clique em **conta Microsoft**. Cole os valores de ID da aplicação e a palavra-passe que obteve anteriormente e, opcionalmente, ativar qualquer âmbitos de que seu aplicativo requer. Em seguida, clique em **OK**.
   
    ![][1]
   
    Por predefinição, o serviço de aplicações fornece autenticação mas não restringe o acesso autorizado para o conteúdo do site e as APIs. Tem de autorizar os utilizadores no seu código de aplicação.
4. (Opcional) Para restringir o acesso ao seu site para apenas os utilizadores autenticados por conta da Microsoft, defina **ação a tomar quando o pedido não é autenticado** ao **Account Microsoft**. Isto requer que todos os pedidos de ser autenticados e todos os pedidos não autenticados são redirecionados para a conta Microsoft para a autenticação.
5. Clique em **Guardar**.

Agora está pronto para utilizar o Microsoft Account para autenticação na sua aplicação.

## <a name="related-content"> </a>Conteúdo relacionado
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-microsoftaccount-settings.png

<!-- URLs. -->

[As Minhas Aplicações]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Portal do Azure]: https://portal.azure.com/
