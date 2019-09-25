---
title: Configurar o Google firebase Cloud Messaging nos hubs de notificação do Azure | Microsoft Docs
description: Saiba como configurar um hub de notificação do Azure com as configurações de mensagens de nuvem do Google firebase.
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
ms.openlocfilehash: 803289476d4d8869f7828b2843a983ee0c86bfbd
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212492"
---
# <a name="configure-google-firebase-cloud-messaging-fcm-settings-for-a-notification-hub-in-the-azure-portal"></a>Configurar as configurações do FCM (Google firebase Cloud Messaging) para um hub de notificação no portal do Azure
Este artigo mostra como configurar as configurações do FCM (Google firebase Cloud Messaging) para um hub de notificação do Azure usando o portal do Azure.  

## <a name="prerequisites"></a>Pré-requisitos
Se você ainda não criou um hub de notificação, crie um agora. Para obter mais informações, consulte [criar um hub de notificação do Azure no portal do Azure](create-notification-hub-portal.md). 

## <a name="configure-google-firebase-cloud-messaging-fcm"></a>Configurar o Google firebase Cloud Messaging (FCM)

O procedimento a seguir fornece as etapas para configurar as configurações do FCM (Google firebase Cloud Messaging) para um hub de notificação: 

1. Na portal do Azure, na página **Hub de notificação** , selecione **Google (GCM/FCM)** no menu à esquerda. 
2. Cole a **chave de API** para o projeto FCM que você salvou anteriormente. 
3. Selecione **Guardar**. 

   ![Captura de tela que mostra como configurar os hubs de notificação para o Google FCM](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

## <a name="next-steps"></a>Passos seguintes
Para obter um tutorial com as instruções passo a passo para enviar notificações para dispositivos Android usando os hubs de notificação do Azure e o Google firebase Cloud Messaging, consulte [notificações por push para dispositivos Android usando hubs de notificação e o Google FCM](notification-hubs-android-push-notification-google-fcm-get-started.md).

