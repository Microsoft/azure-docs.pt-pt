---
title: Configurar o serviço de notificação por push do Windows nos hubs de notificação do Azure | Microsoft Docs
description: Saiba como definir as configurações do serviço de notificação por push do Windows para um hub de notificação do Azure.
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
ms.openlocfilehash: a7f7734d97cd67c133ff0cedc3ef2376967bcdf4
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212407"
---
# <a name="configure-windows-push-notification-service-wns-settings-for-a-notification-hub-in-the-azure-portal"></a>Configurar as configurações do WNS (serviço de notificação por push do Windows) para um hub de notificação no portal do Azure
Este artigo mostra como definir as configurações do WNS (serviço de notificação do Windows) para um hub de notificação do Azure usando o portal do Azure.  

## <a name="prerequisites"></a>Pré-requisitos
Se você ainda não criou um hub de notificação, crie um agora. Para obter mais informações, consulte [criar um hub de notificação do Azure no portal do Azure](create-notification-hub-portal.md). 

## <a name="configure-windows-push-notification-service-wns"></a>Configurar o serviço de notificação por push do Windows (WNS)

O procedimento a seguir fornece as etapas para definir as configurações de WNS (serviço de notificação por push do Windows) para um hub de notificação: 

1. Na portal do Azure, na página **Hub de notificação** , selecione **Windows (WNS)** no menu à esquerda.
2. Insira valores para o **SID do pacote** e a chave de **segurança**.
3. Selecione **Guardar**.

   ![Captura de tela que mostra as caixas SID do pacote e chave de segurança](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

## <a name="next-steps"></a>Passos seguintes
Para obter um tutorial com as instruções passo a passo para enviar notificações por push para Plataforma Universal do Windows aplicativos usando os hubs de notificação do Azure e o WNS (serviço de notificação por push do Windows), consulte [enviar notificações para aplicativos UWP usando a notificação do Azure Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).


