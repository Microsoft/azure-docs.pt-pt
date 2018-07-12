---
title: Adicionar notificações push à aplicação xamarin. Android | Documentos da Microsoft
description: Saiba como utilizar o serviço de aplicações do Azure e os Hubs de notificação do Azure para enviar notificações push à sua aplicação xamarin. Android
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 6f7e8517-e532-4559-9b07-874115f4c65b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: crdun
ms.openlocfilehash: a6cdff68d63859c6a6612b606664d3e1fbaae375
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38306859"
---
# <a name="add-push-notifications-to-your-xamarinandroid-app"></a>Adicionar notificações push à aplicação Xamarin.Android
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Descrição geral
Neste tutorial, adicionar notificações push para o [guia de introdução do xamarin. Android](app-service-mobile-windows-store-dotnet-get-started.md) do projeto para que uma notificação push é enviada para o dispositivo sempre que um registro é inserido.

Se não utilizar o projeto de servidor de início rápido transferido, terá do pacote de extensão de notificação push. Para obter mais informações, consulte a [trabalhar com o SDK do servidor de back-end de .NET para aplicações móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) guia.

## <a name="prerequisites"></a>Pré-requisitos
Este tutorial requer a configuração:

* Uma conta Google ativa. Pode inscrever-se para obter uma conta Google no [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302).
* [Google Cloud Messaging componente cliente](http://components.xamarin.com/view/GCMClient/).

## <a name="configure-hub"></a>Configurar um Hub de notificação
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a id="register"></a>Ativar Firebase Cloud Messaging
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-azure-to-send-push-requests"></a>Configurar o Azure para enviar pedidos de push
[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a id="update-server"></a>Atualizar o projeto de servidor para enviar notificações push
[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a id="configure-app"></a>Configurar o projeto de cliente para as notificações push
[!INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

## <a id="add-push"></a>Adicione o código de notificações push à sua aplicação
[!INCLUDE [app-service-mobile-xamarin-android-push-add-to-app](../../includes/app-service-mobile-xamarin-android-push-add-to-app.md)]

## <a name="test"></a>Notificações de push de teste na sua aplicação
Pode testar a aplicação ao utilizar um dispositivo virtual no emulador. Existem passos de configuração adicionais necessários quando em execução no emulador.

1. O dispositivo virtual tem de ter as APIs da Google definido como o destino no Gestor de dispositivo Virtual Android (AVD).
   
    ![](./media/app-service-mobile-xamarin-android-get-started-push/google-apis-avd-settings.png)
2. Adicionar uma conta do Google para o dispositivo Android clicando **aplicações** > **definições** > **adicionar conta**, em seguida, siga as instruções.
   
    ![](./media/app-service-mobile-xamarin-android-get-started-push/add-google-account.png)
3. Execute a aplicação de todolist como antes e inserir um novo item de lista de tarefas. Desta vez, é apresentado um ícone de notificação na área de notificação. É possível abrir a gaveta de notificação para ver o texto completo da notificação.
   
    ![](./media/app-service-mobile-xamarin-android-get-started-push/android-notifications.png)

<!-- URLs. -->
[Xamarin.Android quick start]: app-service-mobile-xamarin-android-get-started.md
[Google Cloud Messaging Client Component]: http://components.xamarin.com/view/GCMClient/
[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/
