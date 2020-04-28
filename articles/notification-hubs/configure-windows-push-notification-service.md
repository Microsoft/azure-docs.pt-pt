---
title: Configure o Serviço de Notificação push do Windows em Hubs de Notificação Azure [ Microsoft Docs
description: Saiba como configurar as definições do Serviço de Notificação push do Windows para um hub de notificação Azure.
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
ms.openlocfilehash: 73304e191242725c80204efb132c26aede9ce7e9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80127315"
---
# <a name="configure-windows-push-notification-service-settings-in-the-azure-portal"></a>Configure as definições do Serviço de Notificação push do Windows no portal Azure

Este artigo mostra-lhe como configurar as definições do Serviço de Notificação do Windows (WNS) para um hub de notificação Azure utilizando o portal Azure.  

## <a name="prerequisites"></a>Pré-requisitos
Se ainda não criou um centro de notificação, crie um agora. Para mais informações, consulte Criar um hub de [notificação Azure no portal Azure.](create-notification-hub-portal.md) 

## <a name="configure-windows-push-notification-service-wns"></a>Configure O Serviço de Notificação push do Windows (WNS)

O procedimento seguinte dá-lhe passos para configurar as definições do Serviço de Notificação push do Windows (WNS) para um centro de notificação: 

1. No portal Azure, na página Do Hub de **Notificação,** selecione **Windows (WNS)** no menu esquerdo.
2. Introduza valores para **pacote SID** e Chave **de Segurança**.
3. Selecione **Guardar**.

   ![Screenshot que mostra as caixas de chaves de pacote SID e segurança](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

## <a name="next-steps"></a>Passos seguintes
Para um tutorial com instruções passo a passo para empurrar notificações para aplicações da Plataforma Universal Windows utilizando hubs de notificação Azure e Serviço de Notificação push do Windows (WNS), consulte [Enviar notificações para aplicações UWP utilizando hubs de notificação Do Azure.](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)


