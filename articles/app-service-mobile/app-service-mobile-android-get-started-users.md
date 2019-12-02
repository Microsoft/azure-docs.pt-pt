---
title: Adicionar autenticação no Android
description: Saiba como usar o serviço de Azure App para autenticar usuários de seu aplicativo Android com provedores de identidade, como Google, Facebook, Twitter e Microsoft.
ms.assetid: 1fc8e7c1-6c3c-40f4-9967-9cf5e21fc4e1
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: f68b4f8477d5b21a7107270370af387a7e88756e
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668939"
---
# <a name="add-authentication-to-your-android-app"></a>Adicionar autenticação ao seu aplicativo Android
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> O Visual Studio App Center suporta serviços de ponto a ponto e integrados, fundamentais para o desenvolvimento de aplicações móveis. Os programadores podem utilizar os serviços de **Compilação**, **Teste** e **Distribuição** para configurar o pipeline de Integração e Entrega Contínuas. Após a implementação da aplicação, os programadores podem monitorizar o estado e a utilização da aplicação através dos serviços de **Análise** e de **Diagnóstico** e interagir com os utilizadores através do serviço **Push**. Os programadores também podem tirar partido da **Autenticação** para autenticar os utilizadores e do serviço de **Dados** para manter e sincronizar os dados da aplicação na cloud.
>
> Se quiser integrar serviços cloud na sua aplicação móvel, inscreva-se no [App Center](https://appcenter.ms/signup?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) hoje mesmo.

## <a name="summary"></a>Resumo
Neste tutorial, você adiciona autenticação ao projeto de início rápido do ToDoList no Android usando um provedor de identidade com suporte. Este tutorial se baseia no tutorial [introdução aos aplicativos móveis] , que você deve concluir primeiro.

## <a name="register"></a>Registrar seu aplicativo para autenticação e configurar Azure App serviço
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Adicionar seu aplicativo às URLs de redirecionamento externo permitidas

A autenticação segura exige que você defina um novo esquema de URL para seu aplicativo. Isso permite que o sistema de autenticação Redirecione para seu aplicativo após a conclusão do processo de autenticação. Neste tutorial, usamos o esquema de URL _AppName_ em todo o. No entanto, você pode usar qualquer esquema de URL que escolher. Ele deve ser exclusivo para seu aplicativo móvel. Para habilitar o redirecionamento no lado do servidor:

1. No [portal do Azure], selecione o serviço de aplicativo.

2. Clique na opção de menu **autenticação/autorização** .

3. Nas **URLs de redirecionamento externo permitidas**, insira `appname://easyauth.callback`.  O _AppName_ nesta cadeia de caracteres é o esquema de URL para seu aplicativo móvel.  Ele deve seguir a especificação de URL normal para um protocolo (Use somente letras e números e comece com uma letra).  Você deve anotar a cadeia de caracteres que escolher, pois será necessário ajustar o código do aplicativo móvel com o esquema de URL em vários locais.

4. Clique em **OK**.

5. Clique em **Guardar**.

## <a name="permissions"></a>Restringir permissões a usuários autenticados
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* No Android Studio, abra o projeto que você concluiu com o tutorial introdução [Introdução aos aplicativos móveis]. No menu **executar** , clique em **Executar aplicativo**e verifique se uma exceção sem tratamento com um código de status 401 (não autorizado) é gerada depois que o aplicativo é iniciado.

     Essa exceção ocorre porque o aplicativo tenta acessar o back-end como um usuário não autenticado, mas a tabela *TodoItem* agora requer autenticação.

Em seguida, você atualiza o aplicativo para autenticar usuários antes de solicitar recursos do back-end dos aplicativos móveis.

## <a name="add-authentication-to-the-app"></a>Adicionar autenticação ao aplicativo
[!INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]



## <a name="cache-tokens"></a>Tokens de autenticação de cache no cliente
[!INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Passos seguintes
Agora que você concluiu este tutorial de autenticação básica, considere continuar com um dos seguintes tutoriais:

* [Adicione notificações por push ao seu aplicativo Android](app-service-mobile-android-get-started-push.md).
  Saiba como configurar seu back-end de aplicativos móveis para usar os hubs de notificação do Azure para enviar notificações por push.
* [Habilite a sincronização offline para seu aplicativo Android](app-service-mobile-android-get-started-offline-data.md).
  Saiba como adicionar suporte offline ao seu aplicativo usando um back-end de aplicativos móveis. Com a sincronização offline, os usuários podem interagir com um aplicativo móvel&mdash;exibir, adicionar ou modificar dados&mdash;mesmo quando não há conexão de rede.

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]:#next-steps


<!-- URLs. -->
[Introdução aos aplicativos móveis]: app-service-mobile-android-get-started.md
[Portal do Azure]: https://portal.azure.com/
