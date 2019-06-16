---
title: Configurar a autenticação do Google - serviço de aplicações do Azure
description: Saiba como configurar a autenticação do Google para a sua aplicação de serviços de aplicações.
services: app-service
documentationcenter: ''
author: mattchenderson
manager: syntaxc4
editor: ''
ms.assetid: 2b2f9abf-9120-4aac-ac5b-4a268d9b6e2b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/19/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 50905b86924e0f564eaf4867c2906ad8740ddbaf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60851193"
---
# <a name="how-to-configure-your-app-service-application-to-use-google-login"></a>Como configurar a sua aplicação de serviço de aplicações para utilizar o início de sessão do Google
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este tópico mostra-lhe como configurar o serviço de aplicações do Azure para utilizar o Google como um fornecedor de autenticação.

Para concluir o procedimento neste tópico, tem de ter uma conta do Google que tenha um endereço de correio eletrónico verificado. Para criar uma nova conta do Google, aceda a [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302).

## <a name="register"> </a>Registar a sua aplicação com o Google
1. Inicie sessão para o [portal do Azure]e navegue para a sua aplicação. Copiar seus **URL**, que irá utilizar mais tarde para configurar a sua aplicação do Google.
2. Navegue para o [apis do Google](https://go.microsoft.com/fwlink/p/?LinkId=268303) Web site, inicie sessão com as credenciais da conta Google, clique em **criar projeto**, fornecer um **nome do projeto**, em seguida, clique em  **Criar**.
3. Depois de criado o projeto, selecione-o. A partir do dashboard do projeto, clique em **aceda à descrição geral APIs**.
4. Selecione **ativar APIs e serviços**. Procure **API do Google +** e selecioná-lo. Em seguida, clique em **ativar**.
5. Na navegação à esquerda **credenciais** > **ecrã de consentimento do OAuth**, em seguida, selecione seu **endereço de E-Mail**, introduza um **nome do produto**e clique em **guardar**.
6. Na **credenciais** separador, clique em **criar credenciais** > **ID de cliente OAuth**.
7. No ecrã "Criar ID de cliente", selecione **aplicação Web**.
8. Cole o serviço de aplicações **URL** que copiou anteriormente para **autorizados origens de JavaScript**, em seguida, cole o redirecionamento URI para o **autorizado o URI de redirecionamento**. O redirecionamento URI é o URL do seu aplicativo acrescentado com o caminho */.auth/login/google/callback*. Por exemplo, `https://contoso.azurewebsites.net/.auth/login/google/callback`. Certifique-se de que está a utilizar o esquema HTTPS. Em seguida, clique em **Criar**.
9. No ecrã seguinte, tome nota dos valores do ID de cliente e segredo do cliente.

    > [!IMPORTANT]
    > O segredo do cliente é uma credencial de segurança importantes. Não partilhe este segredo com qualquer pessoa ou distribuí-la dentro de um aplicativo de cliente.


## <a name="secrets"> </a>Adicionar informações do Google à sua aplicação
1. De volta a [portal do Azure], navegue para a sua aplicação. Clique em **configurações**e, em seguida **autenticação / autorização**.
2. Se a autenticação / autorização funcionalidade não está ativada, ative a opção **no**.
3. Clique em **Google**. Cole os valores de ID de aplicação e o segredo de aplicação que obteve anteriormente e, opcionalmente, ativar qualquer âmbitos de que seu aplicativo requer. Em seguida, clique em **OK**.
   
   ![][1]
   
   Por predefinição, o serviço de aplicações fornece autenticação mas não restringe o acesso autorizado para o conteúdo do site e as APIs. Tem de autorizar os utilizadores no seu código de aplicação.
4. (Opcional) Para restringir o acesso ao seu site para apenas os utilizadores autenticados pela Google, defina **ação a tomar quando o pedido não é autenticado** ao **Google**. Isto requer que todos os pedidos de ser autenticados e todos os pedidos não autenticados são redirecionados para a Google para autenticação.
5. Clique em **Guardar**.

Agora está pronto para utilizar o Google para autenticação na sua aplicação.

## <a name="related-content"> </a>Conteúdo relacionado
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: https://go.microsoft.com/fwlink/p/?LinkId=268303

[Portal do Azure]: https://portal.azure.com/

