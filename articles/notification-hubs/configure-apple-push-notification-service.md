---
title: Configure Apple Push Notification Service in Azure Notification Hubs | Microsoft Docs
description: Learn how to configure an Azure notification hub with Apple Push Notification Service (APNS) settings.
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 116af5192236045ec50409f65a9687ffec5223b5
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406117"
---
# <a name="configure-apple-push-notification-service-apns-settings-for-a-notification-hub-in-the-azure-portal"></a>Configure Apple Push Notification Service (APNS) settings for a notification hub in the Azure portal
This article shows you how to configure Apple Push Notification Service (APNS) settings for an Azure notification hub by using the Azure portal. 

## <a name="prerequisites"></a>Pré-requisitos
If you haven't already created a notification hub, create one now. For more information, see [Create an Azure notification hub in the Azure portal](create-notification-hub-portal.md). 

## <a name="configure-apple-push-notification-service"></a>Configure Apple Push Notification Service

The following procedure gives you steps to configure Apple Push Notification Service (APNS) settings for a notification hub:

1. In the Azure portal, on the **Notification Hub** page, select **Apple (APNS)** on the left menu.

1. For **Authentication Mode**, select either **Certificate** or **Token**.

   a. If you select **Certificate**:
   * Select the file icon, and then select the *.p12* file you want to upload.
   * Enter a password.
   * Selecione o modo **Sandbox**. Or, to send push notifications to users who purchased your app from the store, select **Production** mode.

     ![Screenshot of an APNS certificate configuration in the Azure portal](./media/configure-apple-push-notification-service/notification-hubs-apple-config-cert.png)

   b. If you select **Token**:

   * Enter the values for **Key ID**, **Bundle ID**, **Team ID**, and **Token**.
   * Selecione o modo **Sandbox**. Or, to send push notifications to users who purchased your app from the store, select **Production** mode.

     ![Screenshot of an APNS token configuration in the Azure portal](./media/configure-apple-push-notification-service/notification-hubs-apple-config-token.png)

## <a name="next-steps"></a>Passos seguintes
For a tutorial with step-by-step instructions for pushing notifications to iOS devices, see the following article: [Push notifications to iOS devices by using Notification Hubs and APNS](notification-hubs-ios-apple-push-notification-apns-get-started.md)
