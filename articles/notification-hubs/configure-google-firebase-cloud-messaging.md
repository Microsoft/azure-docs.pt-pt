---
title: Configure Google Firebase Cloud Messaging em Hubs de Notificação Azure / Microsoft Docs
description: Saiba como configurar um hub de notificação Azure com as definições de Mensagens Cloud da Base de Fogo do Google.
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
ms.openlocfilehash: 1adbce654bc5c057270df9a874911731a0135034
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80127475"
---
# <a name="configure-google-firebase-settings-for-a-notification-hub-in-the-azure-portal"></a>Configure as definições da Base de Fogo do Google para um centro de notificação no portal Azure

Este artigo mostra-lhe como configurar as definições de Google Firebase Cloud Messaging (FCM) para um hub de notificação Azure utilizando o portal Azure.  

## <a name="prerequisites"></a>Pré-requisitos
Se ainda não criou um centro de notificação, crie um agora. Para mais informações, consulte Criar um hub de [notificação Azure no portal Azure.](create-notification-hub-portal.md) 

## <a name="configure-google-firebase-cloud-messaging-fcm"></a>Configure Google Firebase Cloud Messaging (FCM)

O seguinte procedimento dá-lhe passos para configurar as definições de Google Firebase Cloud Messaging (FCM) para um centro de notificação: 

1. No portal Azure, na página Do Hub de **Notificação,** selecione **google (GCM/FCM)** no menu esquerdo. 
2. Colar a **Chave API** para o projeto FCM que guardou anteriormente. 
3. Selecione **Guardar**. 

   ![Screenshot que mostra como configurar Centros de Notificação para o Google FCM](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

## <a name="next-steps"></a>Passos seguintes
Para um tutorial com instruções passo a passo para empurrar notificações para dispositivos Android utilizando hubs de notificação Azure e Google Firebase Cloud Messaging, consulte [notificações push para dispositivos Android utilizando Centros de Notificação e Google FCM.](notification-hubs-android-push-notification-google-fcm-get-started.md)

