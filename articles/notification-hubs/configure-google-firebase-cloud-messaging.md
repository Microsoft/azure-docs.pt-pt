---
title: Configurar Cloud Google Firebase mensagens nos Hubs de notificação do Azure | Documentos da Microsoft
description: Saiba como configurar um hub de notificação do Azure com as definições do Google Firebase Cloud Messaging.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: d200f5746d5f7c62ff3a52873d5d5d6c3e1322dc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60239192"
---
# <a name="configure-google-firebase-cloud-messaging-fcm-settings-for-a-notification-hub-in-the-azure-portal"></a>Configurar definições de Google Firebase Cloud Messaging (FCM) para um hub de notificação no portal do Azure
Este artigo mostra-lhe como configurar as definições do Google Firebase Cloud Messaging (FCM) para um hub de notificação do Azure com o portal do Azure.  

## <a name="prerequisites"></a>Pré-requisitos
Se ainda não tiver criado um hub de notificação, crie uma agora. Para obter mais informações, consulte [criar um hub de notificação do Azure no portal do Azure](create-notification-hub-portal.md). 

## <a name="configure-google-firebase-cloud-messaging-fcm"></a>Configure Google Firebase Cloud Messaging (FCM)

O procedimento seguinte dá-lhe os passos para configurar as definições do Google Firebase Cloud Messaging (FCM) para um hub de notificação: 

1. No portal do Azure, sobre o **Hub de notificação** página, selecione **Google (GCM/FCM)** no menu da esquerda. 
2. Colar o **chave de API** para o projeto do FCM que guardou anteriormente. 
3. Selecione **Guardar**. 

   ![Captura de ecrã que mostra como configurar os Hubs de notificação para o Google FCM](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

## <a name="next-steps"></a>Passos Seguintes
Para obter um tutorial com instruções passo a passo para enviar notificações push para dispositivos Android com Notification Hubs do Azure e o Google Firebase Cloud Messaging, consulte [notificações Push para dispositivos Android com Hubs de notificação e FCM do Google ](notification-hubs-android-push-notification-google-fcm-get-started.md).

