---
title: Envie notificações push para aplicações Xamarin.Forms usando Hubs de Notificação Azure através de um serviço de backend ! Microsoft Docs
description: Saiba como empurrar notificações para aplicações Xamarin.Forms que utilizam os Hubs de Notificação Azure através de um serviço de backend.
author: mikeparker104
ms.service: notification-hubs
ms.topic: tutorial
ms.date: 06/02/2020
ms.author: miparker
ms.openlocfilehash: 5d8ad51c06411e46871fe16022538dfc61adbf5c
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85248943"
---
# <a name="tutorial-send-push-notifications-to-xamarinforms-apps-using-azure-notification-hubs-via-a-backend-service"></a>Tutorial: Enviar notificações push para aplicações Xamarin.Forms usando Hubs de Notificação Azure através de um serviço de backend  

[![Download Sample ](./media/notification-hubs-backend-service-xamarin-forms/download.png) Download the sample](https://github.com/xamcat/mobcat-samples/tree/master/notification_hub_backend_service)  

Neste tutorial, você usa [Azure Notification Hubs](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-overview) para empurrar notificações para uma aplicação [Xamarin.Forms](https://dotnet.microsoft.com/apps/xamarin/xamarin-forms) direcionada para **Android** e **iOS**.  

[!INCLUDE [Notification Hubs Backend Service Introduction](../../includes/notification-hubs-backend-service-introduction.md)]

Este tutorial leva-o através dos seguintes passos:

> [!div class="checklist"]
>
> * [Configurar serviços de notificação push e centros de notificação Azure.](#set-up-push-notification-services-and-azure-notification-hub)
> * [Crie uma aplicação de backend core web ASP.NET.](#create-an-aspnet-core-web-api-backend-application)
> * [Crie uma aplicação Xamarin.Forms de plataforma cruzada.](#create-a-cross-platform-xamarinforms-application)
> * [Configure o projeto Android nativo para notificações push.](#configure-the-native-android-project-for-push-notifications)
> * [Configure o projeto nativo do iOS para notificações push.](#configure-the-native-ios-project-for-push-notifications)
> * [Teste a solução.](#test-the-solution)

## <a name="prerequisites"></a>Pré-requisitos

Para acompanhar, é necessário:

* Uma [subscrição Azure](https://portal.azure.com) onde pode criar e gerir recursos.
* Um Mac com [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/) instalado (ou um PC a executar Visual Studio [2019](https://visualstudio.microsoft.com/vs) com o Desenvolvimento Móvel com carga de trabalho **.NET).**
* A capacidade de executar a aplicação em **dispositivos Android** (físicos ou emuladores) ou **iOS** (apenas dispositivos físicos).

Para Android, você deve ter:

* Um desenvolvedor desbloqueou um dispositivo físico ou um emulador *(executando a API 26 ou acima com os Serviços google Play instalados)*.

Para iOS, deve ter:

* Uma [conta](https://developer.apple.com)de desenvolvimento de Maçã ativa.
* Um dispositivo físico iOS [registado na sua conta de programador](https://help.apple.com/developer-account/#/dev40df0d9fa) *(executando o iOS 13.0 ou superior)*.
* Um [certificado de desenvolvimento](https://help.apple.com/developer-account/#/dev04fd06d56) **.p12** instalado no seu **chaveiro** permite-lhe [executar uma aplicação num dispositivo físico.](https://help.apple.com/xcode/mac/current/#/dev5a825a1ca)

> [!NOTE]
> O simulador iOS não suporta notificações remotas, pelo que é necessário um dispositivo físico ao explorar esta amostra no iOS. No entanto, não precisa de executar a aplicação tanto no **Android** como no **iOS** para completar este tutorial.

Pode seguir os passos neste exemplo de princípios iniciais sem experiência prévia. No entanto, beneficiará de ter familiaridade com os seguintes aspetos.

* [Portal do Desenvolvedor da Apple](https://developer.apple.com)
* [ASP.NET Core](https://docs.microsoft.com/aspnet/core/introduction-to-aspnet-core?view=aspnetcore-3.1)
* [Consola Google Firebase](https://console.firebase.google.com/u/0/)
* [Microsoft Azure](https://portal.azure.com) e [Enviar notificações push para aplicações iOS usando Azure Notification Hubs](ios-sdk-get-started.md).
* [Xamarin](https://dotnet.microsoft.com/apps/xamarin) e [Xamarin.Forms](https://dotnet.microsoft.com/apps/xamarin/xamarin-forms).

Os passos previstos são para [o Visual Studio para Mac,](https://visualstudio.microsoft.com/vs/mac/) mas é possível acompanhar usando o Visual Studio [2019.](https://visualstudio.microsoft.com/vs)

## <a name="set-up-push-notification-services-and-azure-notification-hub"></a>Configurar serviços de notificação push e hub de notificação Azure

Nesta secção, configura os **[Serviços de Mensagens Cloud Firebase (FCM)](https://firebase.google.com/docs/cloud-messaging)** e **[Apple Push Notification Services (APNS).](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html)** Em seguida, cria e configura um centro de notificação para trabalhar com esses serviços.

[!INCLUDE [Create a Firebase project and enable Firebase Cloud Messaging](../../includes/notification-hubs-common-enable-firebase-cloud-messaging.md)]

[!INCLUDE [Enable Apple Push Notifications](../../includes/notification-hubs-common-enable-apple-push-notifications.md)]

[!INCLUDE [Create a notification hub](../../includes/notification-hubs-common-create-notification-hub.md)]

[!INCLUDE [Configure your notification hub with APNs information](../../includes/notification-hubs-common-configure-with-apns-information.md)]

[!INCLUDE [Configure your notification hub with FCM information](../../includes/notification-hubs-common-configure-with-fcm-information.md)]

## <a name="create-an-aspnet-core-web-api-backend-application"></a>Crie uma aplicação de backend core web ASP.NET

Nesta secção, cria o [backend da API core web ASP.NET](https://dotnet.microsoft.com/apps/aspnet/apis) para lidar com o registo do [dispositivo](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-registration-management#what-is-device-registration) e o envio de notificações para a aplicação móvel Xamarin.Forms.

[!INCLUDE [Create an ASP.NET Core Web API backend application](../../includes/notification-hubs-backend-service-web-api.md)]

## <a name="create-a-cross-platform-xamarinforms-application"></a>Criar uma aplicação Xamarin.Forms cross-platform

Nesta secção, você constrói uma aplicação móvel [Xamarin.Forms](https://dotnet.microsoft.com/apps/xamarin/xamarin-forms) implementando notificações push de forma transversal.

[!INCLUDE [Sample application generic overview](../../includes/notification-hubs-backend-service-sample-app-overview.md)]

[!INCLUDE [Create Xamarin.Forms application](../../includes/notification-hubs-backend-service-sample-app-xamarin-forms.md)]

## <a name="configure-the-native-android-project-for-push-notifications"></a>Configure o projeto Android nativo para notificações push

[!INCLUDE [Configure the native Android project](../../includes/notification-hubs-backend-service-configure-xamarin-android.md)]

## <a name="configure-the-native-ios-project-for-push-notifications"></a>Configure o projeto nativo do iOS para notificações push

[!INCLUDE [Configure the native iOS project](../../includes/notification-hubs-backend-service-configure-xamarin-ios.md)]

## <a name="test-the-solution"></a>Testar a solução

Pode agora testar o envio de notificações através do serviço de backend.

[!INCLUDE [Testing the solution](../../includes/notification-hubs-backend-service-testing.md)]

## <a name="next-steps"></a>Passos seguintes

Deverá agora ter uma aplicação básica de Xamarin.Forms ligada a um centro de notificação através de um serviço de backend e pode enviar e receber notificações.

[!INCLUDE [Next steps](../../includes/notification-hubs-backend-service-next-steps.md)]

## <a name="troubleshooting"></a>Resolução de problemas

[!INCLUDE [Troubleshooting](../../includes/notification-hubs-backend-service-troubleshooting.md)]

## <a name="related-links"></a>Ligações relacionadas

* [Visão geral dos Hubs de Notificação do Azure](notification-hubs-push-notification-overview.md)
* [Instalação do Estúdio Visual para Mac](https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019)
* [Instalação de Xamarin no Windows](https://docs.microsoft.com/xamarin/get-started/installation/windows)
* [Hubs de Notificação SDK para operações de back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
* [Hubs de Notificação SDK no GitHub](https://github.com/Azure/azure-notificationhubs)
* [Registar com o back-end da aplicação](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
* [Gestão de registos](notification-hubs-push-notification-registration-management.md)
* [Trabalhar com etiquetas](notification-hubs-tags-segment-push-message.md)
* [Trabalhando com modelos personalizados](notification-hubs-templates-cross-platform-push-messages.md)
