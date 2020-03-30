---
title: Configure Baidu Cloud Push em Hubs de Notificação Azure [ Microsoft Docs
description: Saiba como configurar as definições de Baidu para um centro de notificação Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71212511"
---
# <a name="deprecated-configure-baidu-cloud-push-settings-for-a-notification-hub-in-the-azure-portal"></a>Preprecated: Configure as definições de Push de Nuvem de Baidu para um centro de notificação no portal Azure

Este artigo mostra-lhe como configurar as definições de Baidu Cloud Push para um hub de notificação Azure utilizando o portal Azure.

> [!IMPORTANT]
> Este tutorial está deprecida. 

## <a name="prerequisites"></a>Pré-requisitos
Se ainda não criou um centro de notificação, crie um agora. Para mais informações, consulte Criar um hub de [notificação Azure no portal Azure.](create-notification-hub-portal.md) 

## <a name="configure-baidu-cloud-push"></a>Configurar o Baidu Cloud Push
O seguinte procedimento dá-lhe passos para configurar as definições de Baidu Cloud Push para um centro de notificação:

1. No portal Azure, na página Do Hub de **Notificação,** selecione **Baidu (Android China)** no menu esquerdo. 
2. Introduza a **Chave Api** que obteve da consola Baidu no projeto baidu cloud push. 
3. Introduza a **Chave Secreta** que obteve da consola Baidu no projeto baidu cloud push. 
4. Selecione **Guardar**. 

    ![Screenshot dos Centros de Notificação que mostram a configuração baidu (Android China) para notificações push](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

## <a name="next-steps"></a>Passos seguintes
Para um tutorial com instruções passo a passo para empurrar notificações para Baidu usando Hubs de Notificação Azure e Baidu Cloud Push, consulte Começar com Centros de [Notificação utilizando Baidu](notification-hubs-baidu-china-android-notifications-get-started.md).
