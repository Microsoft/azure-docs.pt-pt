---
title: Envie notificações push para apps flutter usando hubs de notificação Azure através de um serviço de backend ! Microsoft Docs
description: Saiba como empurrar notificações para aplicações Flutter que usam hubs de notificação Azure através de um serviço de backend.
author: mikeparker104
ms.service: notification-hubs
ms.topic: tutorial
ms.date: 07/07/2020
ms.author: miparker
ms.openlocfilehash: 5fa753a6b8b1284c4f8fcd046f74fabcbae3f8fb
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86171011"
---
# <a name="tutorial-send-push-notifications-to-flutter-apps-using-azure-notification-hubs-via-a-backend-service"></a>Tutorial: Enviar notificações push para apps flutter usando hubs de notificação Azure através de um serviço de backend  

[![Download Sample ](./media/notification-hubs-backend-service-flutter/download.png) Download the sample](https://github.com/xamcat/mobcat-samples/tree/master/notification_hub_backend_service)  

> [!div class="op_single_selector"]
>
> * [Xamarin.Forms](notification-hubs-backend-service-xamarin-forms.md)
> * [Agitação](notification-hubs-backend-service-flutter.md)
> * [React Native](notification-hubs-backend-service-react-native.md)

Neste tutorial, você usa [Azure Notification Hubs](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-overview) para empurrar notificações para uma aplicação [Flutter](https://flutter.dev) direcionada para **Android** e **iOS.**  

[!INCLUDE [Notification Hubs Backend Service Introduction](../../includes/notification-hubs-backend-service-introduction.md)]

Este tutorial leva-o através dos seguintes passos:

> [!div class="checklist"]
>
> * [Configurar serviços de notificação push e centros de notificação Azure.](#set-up-push-notification-services-and-azure-notification-hub)
> * [Crie uma aplicação de backend core web ASP.NET.](#create-an-aspnet-core-web-api-backend-application)
> * [Crie uma aplicação flutter de plataforma cruzada.](#create-a-cross-platform-flutter-application)
> * [Configure o projeto Android nativo para notificações push.](#configure-the-native-android-project-for-push-notifications)
> * [Configure o projeto nativo do iOS para notificações push.](#configure-the-native-ios-project-for-push-notifications)
> * [Teste a solução.](#test-the-solution)

## <a name="prerequisites"></a>Pré-requisitos

Para acompanhar, é necessário:

* Uma [subscrição Azure](https://portal.azure.com) onde pode criar e gerir recursos.
* O kit de ferramentas [Flutter](https://flutter.dev/docs/get-started/install) (juntamente com os seus pré-requisitos).
* [Código de estúdio visual](https://code.visualstudio.com) com os [plugins Flutter e Dart instalados.](https://flutter.dev/docs/get-started/editor?tab=vscode)
* A capacidade de executar a aplicação em **dispositivos Android** (físicos ou emuladores) ou **iOS** (apenas dispositivos físicos).

Para Android, você deve ter:

* Um desenvolvedor desbloqueou um dispositivo físico ou um emulador *(executando a API 26 ou acima com os Serviços google Play instalados)*.

Para iOS, deve ter:

* Uma [conta](https://developer.apple.com)de desenvolvimento de Maçã ativa.
* Um dispositivo físico iOS [registado na sua conta de programador](https://help.apple.com/developer-account/#/dev40df0d9fa) *(executando o iOS 13.0 ou superior)*.
* Um [certificado de desenvolvimento](https://help.apple.com/developer-account/#/dev04fd06d56) **.p12** instalado no seu **chaveiro** permite-lhe [executar uma aplicação num dispositivo físico.](https://help.apple.com/xcode/mac/current/#/dev5a825a1ca)
* [Cacau instalados](https://guides.cocoapods.org/using/getting-started.html#installation) para gerir dependências da biblioteca.

> [!NOTE]
> O simulador iOS não suporta notificações remotas, pelo que é necessário um dispositivo físico ao explorar esta amostra no iOS. No entanto, não precisa de executar a aplicação tanto no **Android** como no **iOS** para completar este tutorial.

Pode seguir os passos neste exemplo de princípios iniciais sem experiência prévia. No entanto, beneficiará de ter familiaridade com os seguintes aspetos.

* [Portal do Desenvolvedor da Apple](https://developer.apple.com)
* [ASP.NET Core](https://docs.microsoft.com/aspnet/core/introduction-to-aspnet-core?view=aspnetcore-3.1)
* [Azure Notification Hubs](notification-hubs-push-notification-overview.md)
* [Consola Google Firebase](https://console.firebase.google.com/u/0/)
* [Flutter](https://flutter.dev) e [Dardo](https://dart.dev) para desenvolvimento de plataformas cruzadas
* [Kotlin](https://kotlinlang.org) e [Swift](https://developer.apple.com/swift) para desenvolvimento nativo android e iOS

Os passos fornecidos são específicos do [macOS.](https://developer.apple.com/macos) É possível seguir o [Windows](https://www.microsoft.com/windows) ignorando os aspetos do **iOS.**

## <a name="set-up-push-notification-services-and-azure-notification-hub"></a>Configurar serviços de notificação push e hub de notificação Azure

Nesta secção, configura os **[Serviços de Mensagens Cloud Firebase (FCM)](https://firebase.google.com/docs/cloud-messaging)** e **[Apple Push Notification Services (APNS).](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html)** Em seguida, cria e configura um centro de notificação para trabalhar com esses serviços.

[!INCLUDE [Create a Firebase project and enable Firebase Cloud Messaging](../../includes/notification-hubs-common-enable-firebase-cloud-messaging.md)]

[!INCLUDE [Enable Apple Push Notifications](../../includes/notification-hubs-common-enable-apple-push-notifications.md)]

[!INCLUDE [Create a notification hub](../../includes/notification-hubs-common-create-notification-hub.md)]

[!INCLUDE [Configure your notification hub with APNs information](../../includes/notification-hubs-common-configure-with-apns-information.md)]

[!INCLUDE [Configure your notification hub with FCM information](../../includes/notification-hubs-common-configure-with-fcm-information.md)]

## <a name="create-an-aspnet-core-web-api-backend-application"></a>Crie uma aplicação de backend core web ASP.NET

Nesta secção, cria-se o [backend da API core web ASP.NET](https://dotnet.microsoft.com/apps/aspnet/apis) para lidar com o registo do [dispositivo](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-registration-management#what-is-device-registration) e o envio de notificações para a aplicação móvel Flutter.

[!INCLUDE [Create an ASP.NET Core Web API backend application](../../includes/notification-hubs-backend-service-web-api.md)]

## <a name="create-a-cross-platform-flutter-application"></a>Criar uma aplicação cross-platform Flutter

Nesta secção, você constrói uma aplicação móvel [Flutter](https://flutter.dev) implementando notificações push de forma transversal.

[!INCLUDE [Sample application generic overview](../../includes/notification-hubs-backend-service-sample-app-overview.md)]

[!INCLUDE [Create Flutter application](../../includes/notification-hubs-backend-service-sample-app-flutter.md)]

## <a name="configure-the-native-android-project-for-push-notifications"></a>Configure o projeto Android nativo para notificações push

[!INCLUDE [Configure the native Android project](../../includes/notification-hubs-backend-service-configure-flutter-android.md)]

## <a name="configure-the-native-ios-project-for-push-notifications"></a>Configure o projeto nativo do iOS para notificações push

[!INCLUDE [Configure the native iOS project](../../includes/notification-hubs-backend-service-configure-flutter-ios.md)]

## <a name="test-the-solution"></a>Testar a solução

Pode agora testar o envio de notificações através do serviço de backend.

[!INCLUDE [Testing the solution](../../includes/notification-hubs-backend-service-testing.md)]

## <a name="next-steps"></a>Passos seguintes

Deverá agora ter uma aplicação básica do Flutter ligada a um centro de notificação através de um serviço de backend e pode enviar e receber notificações.

[!INCLUDE [Next steps](../../includes/notification-hubs-backend-service-next-steps.md)]

## <a name="troubleshooting"></a>Resolução de problemas

[!INCLUDE [Troubleshooting](../../includes/notification-hubs-backend-service-troubleshooting.md)]

## <a name="related-links"></a>Ligações relacionadas

* [Visão geral dos Hubs de Notificação do Azure](notification-hubs-push-notification-overview.md)
* [Instalar flutter no macOS](https://flutter.dev/docs/get-started/install/macos)
* [Instalação flutter no Windows](https://flutter.dev/docs/get-started/install/windows)
* [Hubs de Notificação SDK para operações de back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
* [Hubs de Notificação SDK no GitHub](https://github.com/Azure/azure-notificationhubs)
* [Registar com o back-end da aplicação](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
* [Gestão de registos](notification-hubs-push-notification-registration-management.md)
* [Trabalhar com etiquetas](notification-hubs-tags-segment-push-message.md)
* [Trabalhando com modelos personalizados](notification-hubs-templates-cross-platform-push-messages.md)
