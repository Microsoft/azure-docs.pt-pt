---
title: Adicionar notificações push à sua aplicação Android com as aplicações móveis | Documentos da Microsoft
description: Saiba como utilizar aplicações móveis para enviar notificações push à aplicação Android.
services: app-service\mobile
documentationcenter: android
manager: crdun
editor: ''
author: elamalani
ms.assetid: 9058ed6d-e871-4179-86af-0092d0ca09d3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 529aa8327d31cdda044178b6d03035b602744db2
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443664"
---
# <a name="add-push-notifications-to-your-android-app"></a>Adicionar notificações push à sua aplicação Android

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Visual Studio App Center está a investir em serviços de novo e integrados essenciais para o desenvolvimento de aplicações móveis. Os desenvolvedores podem usar **crie**, **teste** e **distribuir** serviços para configurar os pipelines de integração e entrega contínuas. Assim que a aplicação é implementada, os programadores podem monitorizar o estado e a utilização da sua aplicação com o **Analytics** e **diagnóstico** serviços e interaja com os utilizadores que utilizam o **Push** serviço. Os desenvolvedores também podem aproveitar **Auth** autenticar seus usuários e **dados** serviço para manter e sincronizar dados de aplicações na cloud. Confira [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-android-get-started-push) hoje mesmo.
>

## <a name="overview"></a>Descrição geral

Neste tutorial, adicionar notificações push para o [início rápido do Android] do projeto para que uma notificação push é enviada para o dispositivo sempre que um registro é inserido.

Se não utilizar o projeto de servidor de início rápido transferido, terá do pacote de extensão de notificação push. Para obter mais informações, consulte [trabalhar com o SDK do servidor de back-end de .NET para aplicações móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Pré-requisitos

É necessário o seguinte:

* Um IDE, dependendo de back-end do seu projeto:

  * [Android Studio](https://developer.android.com/sdk/index.html) se esta aplicação tem um back-end de node. js.
  * [O Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) ou posterior se esta aplicação tem um back-end de .NET da Microsoft.
* Android 2.3 ou posterior, o repositório Google revisão 27 ou posterior e serviços do Google Play 9.0.2 ou posterior para Firebase Cloud Messaging.
* Concluir o [início rápido do Android].

## <a name="create-a-project-that-supports-firebase-cloud-messaging"></a>Criar um projeto que suporte o Firebase Cloud Messaging

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-notification-hub"></a>Configurar um hub de notificação

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Configurar o Azure para enviar notificações push

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a name="enable-push-notifications-for-the-server-project"></a>Ative as notificações push para o projeto de servidor

[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

## <a name="add-push-notifications-to-your-app"></a>Adicionar notificações push à aplicação

Nesta secção, atualizar a sua aplicação Android do cliente para processar as notificações push.

### <a name="verify-android-sdk-version"></a>Verificar a versão do Android SDK

[!INCLUDE [app-service-mobile-verify-android-sdk-version](../../includes/app-service-mobile-verify-android-sdk-version.md)]

O próximo passo é instalar serviços do Google Play. Firebase Cloud Messaging tem alguns requisitos de nível de API mínimos para desenvolvimento e teste, o que o **minSdkVersion** propriedade no manifesto têm de cumprir.

Se estiver a testar com um dispositivo mais antigo, consulte [Adicionar Firebase ao seu projeto Android] para determinar como baixa pode definir este valor e defini-la corretamente.

### <a name="add-firebase-cloud-messaging-to-the-project"></a>Adicionar Firebase Cloud Messaging para o projeto

[!INCLUDE [Add Firebase Cloud Messaging](../../includes/app-service-mobile-add-firebase-cloud-messaging.md)]

### <a name="add-code"></a>Adicione código

[!INCLUDE [app-service-mobile-android-getting-started-with-push](../../includes/app-service-mobile-android-getting-started-with-push.md)]

## <a name="test-the-app-against-the-published-mobile-service"></a>Testar a aplicação contra o serviço móvel publicado

Pode testar a aplicação ao anexar diretamente um telemóvel Android com um cabo USB ou com um dispositivo virtual no emulador.

## <a name="next-steps"></a>Passos Seguintes

Agora que concluiu este tutorial, considere continuar em uma das seguintes tutoriais:

* [Adicionar autenticação à sua aplicação Android](app-service-mobile-android-get-started-users.md).
  Saiba como adicionar autenticação para o projeto de início rápido todolist no Android através de um fornecedor de identidade com suporte.
* [Permitir sincronização offline para a aplicação Android](app-service-mobile-android-get-started-offline-data.md).
  Saiba como adicionar suporte offline à sua aplicação utilizando um back-end de aplicações móveis. Com a sincronização offline, os usuários podem interagir com uma aplicação móvel&mdash;visualizar, adicionar ou modificar dados&mdash;, mesmo quando não existe nenhuma ligação de rede.

<!-- URLs -->
[Início rápido do Android]: app-service-mobile-android-get-started.md
[Adicionar Firebase ao seu projeto Android]: https://firebase.google.com/docs/android/setup
