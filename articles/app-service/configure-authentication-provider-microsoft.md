---
title: Configurar a autenticação da conta da Microsoft-serviço de Azure App
description: Saiba como configurar a autenticação da conta da Microsoft para seu aplicativo de serviços de aplicativos.
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
ms.openlocfilehash: 10b661f0c4b7dc45284b907e83df3c0372f97cab
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561545"
---
# <a name="how-to-configure-your-app-service-application-to-use-microsoft-account-login"></a>Como configurar seu aplicativo do serviço de aplicativo para usar o logon da conta da Microsoft
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este tópico mostra como configurar o serviço de Azure App para usar a conta da Microsoft como um provedor de autenticação. 

## <a name="register-microsoft-account"> </a>Registrar seu aplicativo com a conta da Microsoft
1. Faça logon no [Azure portal]e navegue até seu aplicativo. Copie a **URL**, que posteriormente você usa para configurar seu aplicativo com a conta da Microsoft.
2. Navegue até [**registros de aplicativo**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)e entre com sua conta Microsoft, se solicitado.
3. Clique em **Adicionar um aplicativo**, digite um nome de aplicativo e clique em **criar**.
4. Anote a **ID do aplicativo**, pois você precisará dela mais tarde. 
5. Em "plataformas", clique em **Adicionar plataforma** e selecione "Web".
6. Em "URIs de redirecionamento", forneça o ponto de extremidade para seu aplicativo e, em seguida, clique em **salvar**. 
   
   > [!NOTE]
   > O URI de redirecionamento é a URL do seu aplicativo acrescentada com o caminho */.auth/login/MicrosoftAccount/callback*. Por exemplo, `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`.   
   > Verifique se você está usando o esquema HTTPS.
   
7. Em "segredos do aplicativo", clique em **gerar nova senha**. Anote o valor que aparece. Depois de sair da página, ela não será exibida novamente.

    > [!IMPORTANT]
    > A senha é uma credencial de segurança importante. Não compartilhe a senha com ninguém nem a distribua em um aplicativo cliente.
    
8. Clique em **Guardar**

## <a name="secrets"> </a>Adicionar informações da conta da Microsoft ao seu aplicativo do serviço de aplicativo
1. De volta ao [Azure portal], navegue até o aplicativo, clique em **configurações** > **autenticação/autorização**.
2. Se o recurso de autenticação/autorização não estiver habilitado, alterne **-o**.
3. Clique em **conta da Microsoft**. Cole os valores de ID do aplicativo e senha que você obteve anteriormente e, opcionalmente, habilite todos os escopos que seu aplicativo requer. Em seguida, clique em **OK**.
   
    ![][1]
   
    Por padrão, o serviço de aplicativo fornece autenticação, mas não restringe o acesso autorizado ao conteúdo e às APIs do site. Você deve autorizar os usuários no código do aplicativo.
4. Adicional Para restringir o acesso ao seu site somente para os usuários autenticados pelo conta Microsoft, defina **a ação a ser tomada quando a solicitação não for autenticada** para a **conta da Microsoft**. Isso requer que todas as solicitações sejam autenticadas e todas as solicitações não autenticadas sejam redirecionadas para conta Microsoft para autenticação.
5. Clique em **Guardar**.

Agora você está pronto para usar a conta da Microsoft para autenticação em seu aplicativo.

## <a name="related-content"> </a>Conteúdo relacionado
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-microsoftaccount-settings.png

<!-- URLs. -->

[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure portal]: https://portal.azure.com/
