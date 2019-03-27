---
title: Configurar o serviço de notificação Push do Windows nos Hubs de notificação do Azure | Documentos da Microsoft
description: Saiba como configurar as definições de serviço do Windows Push Notification para um hub de notificação do Azure.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: c3e3f1e7df5c90c690756375ff1e1b0350c72714
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488333"
---
# <a name="configure-windows-push-notification-service-wns-settings-for-a-notification-hub-in-the-azure-portal"></a>Configurar definições de Windows Push Notification Service (WNS) para um hub de notificação no portal do Azure
Este artigo mostra-lhe como configurar definições de serviço de notificação Windows (WNS) para um hub de notificação do Azure com o portal do Azure.  

## <a name="prerequisites"></a>Pré-requisitos
Se ainda não tiver criado um hub de notificação, crie uma agora. Para obter mais informações, consulte [criar um hub de notificação do Azure no portal do Azure](create-notification-hub-portal.md). 

## <a name="configure-windows-push-notification-service-wns"></a>Configurar o serviço de notificações de Push do Windows (WNS)

O procedimento seguinte dá-lhe os passos para configurar as definições do Windows Push Notification Service (WNS) para um hub de notificação: 

1. No portal do Azure, sobre o **Hub de notificação** página, selecione **Windows (WNS)** no menu da esquerda.
2. Introduza os valores para **SID do pacote** e **chave de segurança**.
3. Selecione **Guardar**.

   ![Captura de ecrã que mostra as caixas do SID do pacote e a chave de segurança](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

## <a name="next-steps"></a>Passos Seguintes
Para obter um tutorial com instruções passo a passo para enviar notificações push para aplicações de plataforma Universal do Windows com os Hubs de notificação do Azure e o Windows Push Notification Service (WNS), consulte [enviar notificações para aplicações UWP ao utilizar o Azure Os Hubs de notificação](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).


