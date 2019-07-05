---
title: Adicionar a autenticação no Android com as aplicações móveis | Documentos da Microsoft
description: Saiba como utilizar a funcionalidade de aplicações móveis do serviço de aplicações do Azure para autenticar os utilizadores da sua aplicação Android através de uma variedade de fornecedores de identidade, incluindo Google, Facebook, Twitter e Microsoft.
services: app-service\mobile
documentationcenter: android
author: elamalani
manager: crdun
editor: ''
ms.assetid: 1fc8e7c1-6c3c-40f4-9967-9cf5e21fc4e1
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: f138911510db4e6839ff96317fa6004e449e58be
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443590"
---
# <a name="add-authentication-to-your-android-app"></a>Adicionar autenticação à sua aplicação Android
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center está a investir em serviços de novo e integrados essenciais para o desenvolvimento de aplicações móveis. Os desenvolvedores podem usar **crie**, **teste** e **distribuir** serviços para configurar os pipelines de integração e entrega contínuas. Assim que a aplicação é implementada, os programadores podem monitorizar o estado e a utilização da sua aplicação com o **Analytics** e **diagnóstico** serviços e interaja com os utilizadores que utilizam o **Push** serviço. Os desenvolvedores também podem aproveitar **Auth** autenticar seus usuários e **dados** serviço para manter e sincronizar dados de aplicações na cloud. Confira [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-android-get-started-users) hoje mesmo.
>

## <a name="summary"></a>Resumo
Neste tutorial, adicionar autenticação para o projeto de início rápido todolist no Android utilizando um fornecedor de identidade com suporte. Este tutorial baseia-se sobre o [introdução às Mobile Apps] tutorial, o que tem de concluir primeiro.

## <a name="register"></a>Registar a sua aplicação para autenticação e configurar o serviço de aplicações do Azure
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Adicionar a sua aplicação para os URLs de redirecionamento externo permitidos

Autenticação segura requer que defina um novo esquema de URL para a sua aplicação. Isso permite que o sistema de autenticação redirecionar para a sua aplicação, uma vez concluído o processo de autenticação. Neste tutorial, utilizamos o esquema de URL _appname_ em todo. No entanto, pode utilizar qualquer esquema de URL que escolher. Deve ser exclusivo para a sua aplicação móvel. Para ativar o redirecionamento no lado do servidor:

1. Na [Azure portal], selecione o serviço de aplicações.

2. Clique nas **autenticação / autorização** opção de menu.

3. Na **permitido URLs de redirecionamento externo**, introduza `appname://easyauth.callback`.  O _appname_ nessa cadeia é o esquema de URL para a sua aplicação móvel.  Deve seguir normal especificação de URL para um protocolo (utilize letras e números apenas e começar com uma letra).  Deve tome nota da cadeia de caracteres que escolha, como precisará ajustar o código da aplicação móvel com o esquema de URL em vários locais.

4. Clique em **OK**.

5. Clique em **Guardar**.

## <a name="permissions"></a>Restringir as permissões para utilizadores autenticados
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* No Android Studio, abra o projeto que concluiu o tutorial [introdução às Mobile Apps]. Do **execute** menu, clique em **executar aplicação**e certifique-se de que uma exceção não processada com um código de estado de 401 (não autorizado) é gerada depois da aplicação for iniciada.

     Essa exceção ocorre porque a aplicação tenta aceder ao back-end como um utilizador não autenticado, mas a *TodoItem* tabela agora requer autenticação.

Em seguida, atualizar a aplicação para autenticar os utilizadores antes de solicitar recursos de back-end das aplicações móveis.

## <a name="add-authentication-to-the-app"></a>Adicionar autenticação à aplicação
[!INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]



## <a name="cache-tokens"></a>Tokens de autenticação de cache no cliente
[!INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Passos Seguintes
Agora que concluiu este tutorial de autenticação básica, considere continuar em uma das seguintes tutoriais:

* [Adicionar notificações push à sua aplicação Android](app-service-mobile-android-get-started-push.md).
  Saiba como configurar as suas aplicações móveis de back-end utilizar hubs de notificação do Azure para enviar notificações push.
* [Permitir sincronização offline para a aplicação Android](app-service-mobile-android-get-started-offline-data.md).
  Saiba como adicionar suporte offline à sua aplicação utilizando um back-end de aplicações móveis. Com a sincronização offline, os usuários podem interagir com uma aplicação móvel&mdash;visualizar, adicionar ou modificar dados&mdash;, mesmo quando não existe nenhuma ligação de rede.

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]:#next-steps


<!-- URLs. -->
[Introdução às Mobile Apps]: app-service-mobile-android-get-started.md
[Azure portal]: https://portal.azure.com/
