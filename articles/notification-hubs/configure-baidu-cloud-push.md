---
title: Configurar o push do Baidu Cloud nos hubs de notificação do Azure | Microsoft Docs
description: Saiba como definir as configurações de Baidu para um hub de notificação do Azure.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 759e35ba353f470ea3abc5f5d4182fa2b2ea0e73
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212511"
---
# <a name="deprecated-configure-baidu-cloud-push-settings-for-a-notification-hub-in-the-azure-portal"></a>Preterido Definir as configurações de push do Baidu Cloud para um hub de notificação no portal do Azure

Este artigo mostra como definir as configurações de push do Baidu Cloud para um hub de notificação do Azure usando o portal do Azure.

> [!IMPORTANT]
> Este tutorial foi preterido. 

## <a name="prerequisites"></a>Pré-requisitos
Se você ainda não criou um hub de notificação, crie um agora. Para obter mais informações, consulte [criar um hub de notificação do Azure no portal do Azure](create-notification-hub-portal.md). 

## <a name="configure-baidu-cloud-push"></a>Configurar o Baidu Cloud Push
O procedimento a seguir fornece as etapas para definir as configurações de push do Baidu Cloud para um hub de notificação:

1. Na portal do Azure, na página **Hub de notificação** , selecione **Baidu (Android China)** no menu à esquerda. 
2. Insira a **chave de API** que você obteve no console do Baidu no projeto Push da nuvem Baidu. 
3. Insira a **chave secreta** que você obteve no console do Baidu no projeto Push da nuvem Baidu. 
4. Selecione **Guardar**. 

    ![Captura de tela de hubs de notificação que mostra a configuração Baidu (Android China) para notificações por push](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

## <a name="next-steps"></a>Passos seguintes
Para obter um tutorial com as instruções passo a passo para enviar notificações por push ao Baidu usando os hubs de notificação do Azure e o Baidu Cloud Push, consulte Introdução [aos hubs de notificação usando o Baidu](notification-hubs-baidu-china-android-notifications-get-started.md).
