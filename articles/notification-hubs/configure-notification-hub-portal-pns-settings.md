---
title: Set up push notifications in Azure Notification Hubs | Microsoft Docs
description: Learn how to set up Azure Notification Hubs in the Azure portal by using platform notification system (PNS) settings.
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.topic: quickstart
ms.date: 02/14/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 02/14/2019
ms.openlocfilehash: 951f03f581906e45946ef75742421ba27d405267
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406986"
---
# <a name="set-up-push-notifications-in-a-notification-hub-in-the-azure-portal"></a>Set up push notifications in a notification hub in the Azure portal

Azure Notification Hubs provides a push engine that's easy to use and that scales out. Use Notification Hubs to send notifications to any platform (iOS, Android, Windows, Baidu) and from any back end (cloud or on-premises). For more information, see [What is Azure Notification Hubs?](notification-hubs-push-notification-overview.md).

In this quickstart, you'll use the platform notification system (PNS) settings in Notification Hubs to set up push notifications on multiple platforms. The quickstart shows you the steps to take in the Azure portal.

If you haven't already created a notification hub, create one now. For more information, see [Create an Azure notification hub in the Azure portal](create-notification-hub-portal.md). 

## <a name="apple-push-notification-service"></a>Serviço Apple Push Notification

To set up Apple Push Notification Service (APNS):

1. In the Azure portal, on the **Notification Hub** page, select **Apple (APNS)** from the left menu.

1. For **Authentication Mode**, select either **Certificate** or **Token**.

   a. If you select **Certificate**:
   * Select the file icon, and then select the *.p12* file you want to upload.
   * Enter a password.
   * Selecione o modo **Sandbox**. Or, to send push notifications to users who purchased your app from the store, select **Production** mode.

     ![Screenshot of an APNS certificate configuration in the Azure portal](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)

   b. If you select **Token**:

   * Enter the values for **Key ID**, **Bundle ID**, **Team ID**, and **Token**.
   * Selecione o modo **Sandbox**. Or, to send push notifications to users who purchased your app from the store, select **Production** mode.

     ![Screenshot of an APNS token configuration in the Azure portal](./media/configure-notification-hub-portal-pns-settings/notification-hubs-apple-config-token.png)

For more information, see [Push notifications to iOS by using Azure Notification Hubs](notification-hubs-ios-apple-push-notification-apns-get-started.md).

## <a name="google-firebase-cloud-messaging"></a>Google Firebase Cloud Messaging

To set up push notifications for Google Firebase Cloud Messaging (FCM):

1. In the Azure portal, on the **Notification Hub** page, select **Google (GCM/FCM)** from the left menu. 
2. Paste the **API Key** for the FCM project that you saved earlier. 
3. Selecione **Guardar**. 

   ![Screenshot that shows how to configure Notification Hubs for Google FCM](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

When you complete these steps, an alert indicates that the notification hub has been successfully updated. The **Save** button is disabled. 

For more information, see [Push notifications to Android devices by using Notification Hubs and Google FCM](notification-hubs-android-push-notification-google-fcm-get-started.md).

## <a name="windows-push-notification-service"></a>Windows Push Notification Service

To set up Windows Push Notification Service (WNS):

1. In the Azure portal, on the **Notification Hub** page, select **Windows (WNS)** from the left menu.
2. Enter values for **Package SID** and **Security Key**.
3. Selecione **Guardar**.

   ![Screenshot that shows the Package SID and Security Key boxes](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

For information, see [Send notifications to UWP apps by using Azure Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).

## <a name="microsoft-push-notification-service-for-windows-phone"></a>Microsoft Push Notification Service for Windows Phone

To set up Microsoft Push Notification Service (MPNS) for Windows Phone: 

1. In the Azure portal, on the **Notification Hub** page, select **Windows Phone (MPNS)** from the left menu.
1. Enable either unauthenticated or authenticated push notifications:

   a. To enable unauthenticated push notifications, select **Enable unauthenticated push** > **Save**.

      ![Screenshot that shows how to enable unauthenticated push notifications](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. To enable authenticated push notifications:
      * On the toolbar, select **Upload Certificate**.
      * Select the file icon, and then select the certificate file.
      * Introduza a palavra-passe para o certificado.
      * Selecione **OK**.
      * On the **Windows Phone (MPNS)** page, select **Save**.

For more information, see [Push notifications to Windows Phone apps by using Notification Hubs](notification-hubs-windows-mobile-push-notifications-mpns.md).
      

## <a name="baidu-android-china"></a>Baidu (Android China)

To set up push notifications for Baidu:

1. In the Azure portal, on the **Notification Hub** page, select **Baidu (Android China)** from the left menu. 
2. Enter the **Api Key** that you obtained from the Baidu console in the Baidu cloud push project. 
3. Enter the **Secret Key** that you obtained from the Baidu console in the Baidu cloud push project. 
4. Selecione **Guardar**. 

    ![Screenshot of Notification Hubs that shows the Baidu (Android China) configuration for push notifications](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

When you complete these steps, an alert indicates that the notification hub has been successfully updated. The **Save** button is disabled. 

For more information, see [Get started with Notification Hubs by using Baidu](notification-hubs-baidu-china-android-notifications-get-started.md).

## <a name="next-steps"></a>Passos seguintes
In this quickstart, you learned how to configure platform notification system settings for a notification hub in the Azure portal. 

To learn more about how to push notifications to various platforms, see these tutorials:

- [Push notifications to iOS devices by using Notification Hubs and APNS](notification-hubs-ios-apple-push-notification-apns-get-started.md)
- [Push notifications to Android devices by using Notification Hubs and Google FCM](notification-hubs-android-push-notification-google-fcm-get-started.md)
- [Push notifications to a UWP app running on a Windows device](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
- [Push notifications to a Windows Phone 8 app by using MPNS](notification-hubs-windows-mobile-push-notifications-mpns.md)
- [Push notifications by using Notification Hubs and Baidu cloud push](notification-hubs-baidu-china-android-notifications-get-started.md)
