---
title: Adicione notificações push à sua aplicação Android
description: Saiba como usar aplicações móveis para enviar notificações push para a sua aplicação Android.
ms.assetid: 9058ed6d-e871-4179-86af-0092d0ca09d3
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 6fec85c028e992c15fb9503ffb599023e668c58f
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77459944"
---
# <a name="add-push-notifications-to-your-android-app"></a>Adicione notificações push à sua aplicação Android

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Descrição geral

Neste tutorial, adiciona notificações push ao projeto [Início rápido do Android] do Android para que seja enviada uma notificação push para o dispositivo sempre que um registo é inserido.

Se não utilizar o projeto de servidor de arranque rápido descarregado, necessita do pacote de extensão de notificação push. Para mais informações, consulte [Trabalhar com o servidor de backend .NET SDK para aplicações móveis Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Pré-requisitos

Precisa do seguinte:

* Um IDE, dependendo do final do seu projeto:

  * [Android Studio](https://developer.android.com/sdk/index.html) se esta aplicação tiver um node.js back end.
  * [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) ou mais tarde se esta aplicação tiver uma extremidade traseira microsoft .NET.
* Android 2.3 ou mais tarde, revisão do Repositório do Google 27 ou posterior, e Google Play Services 9.0.2 ou mais tarde para Firebase Cloud Messaging.
* Complete o arranque rápido do [Início rápido do Android]

## <a name="create-a-project-that-supports-firebase-cloud-messaging"></a>Criar um projeto que suporte o Firebase Cloud Messaging

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-notification-hub"></a>Configurar um hub de notificação

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="configure-azure-to-send-push-notifications"></a>Configure Azure para enviar notificações push

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a name="enable-push-notifications-for-the-server-project"></a>Ativar notificações push para o projeto do servidor

[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

## <a name="add-push-notifications-to-your-app"></a>Adicionar notificações push à aplicação

Nesta secção, atualiza a sua aplicação Android para lidar com notificações push.

### <a name="verify-android-sdk-version"></a>Verifique a versão Android SDK

[!INCLUDE [app-service-mobile-verify-android-sdk-version](../../includes/app-service-mobile-verify-android-sdk-version.md)]

O próximo passo é instalar os serviços do Google Play. A Firebase Cloud Messaging tem alguns requisitos mínimos de nível de API para desenvolvimento e teste, aos quais a propriedade **minSdkVersion** no manifesto deve estar em conformidade.

Se estiver a testar com um dispositivo mais antigo, consulte [Adicione firebase ao seu projeto Android] para determinar até que ponto pode definir este valor e defina-o adequadamente.

### <a name="add-firebase-cloud-messaging-to-the-project"></a>Adicione mensagens cloud firebase ao projeto

[!INCLUDE [Add Firebase Cloud Messaging](../../includes/app-service-mobile-add-firebase-cloud-messaging.md)]

### <a name="add-code"></a>Adicionar código

[!INCLUDE [app-service-mobile-android-getting-started-with-push](../../includes/app-service-mobile-android-getting-started-with-push.md)]

## <a name="test-the-app-against-the-published-mobile-service"></a>Teste a aplicação contra o serviço móvel publicado

Pode testar a aplicação ligando diretamente um telefone Android com um cabo USB, ou utilizando um dispositivo virtual no emulador.

## <a name="next-steps"></a>Passos seguintes

Agora que completou este tutorial, considere continuar a um dos seguintes tutoriais:

* [Adicione a autenticação à sua aplicação Android.](app-service-mobile-android-get-started-users.md)
  Saiba como adicionar autenticação ao projeto de arranque rápido todo-lista no Android utilizando um fornecedor de identidade suportado.
* [Ative a sincronização offline para a sua aplicação Android.](app-service-mobile-android-get-started-offline-data.md)
  Aprenda a adicionar suporte offline à sua aplicação utilizando uma extremidade de apps móveis. Com sincronização offline, os utilizadores podem interagir com uma aplicação móvel&mdash;visualização, adição ou modificação de dados&mdash;mesmo quando não há ligação de rede.

<!-- URLs -->
[Início rápido do Android]: app-service-mobile-android-get-started.md
[Adicione firebase ao seu projeto Android]: https://firebase.google.com/docs/android/setup
