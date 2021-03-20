---
title: Configure o serviço de notificação push do Windows em Azure Notification Hubs | Microsoft Docs
description: Saiba como configurar as definições do Serviço de Notificação push do Windows para um hub de notificação Azure.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 08/04/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 62d73a23fff3bd15d873a579d451b2f91509b7fe
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87758735"
---
# <a name="configure-windows-push-notification-service-settings-in-the-azure-portal"></a>Configurar as definições do Serviço de Notificação push do Windows no portal Azure

Este artigo mostra como configurar as definições do Windows Notification Service (WNS) para um hub de notificação Azure utilizando o portal Azure.  

## <a name="prerequisites"></a>Pré-requisitos

Se ainda não criou um centro de notificação, crie um agora. Para mais informações, consulte [Criar um centro de notificação Azure no portal Azure](create-notification-hub-portal.md).

## <a name="configure-windows-push-notification-service-wns"></a>Configure o Serviço de Notificação push do Windows (WNS)

O procedimento a seguir descreve as etapas para configurar as definições do Windows Push Notification Service (WNS) para um centro de notificação:

1. No portal Azure, na página **'Centro de Notificação',** selecione **Windows (WNS)** no menu esquerdo.
2. Introduza os valores **para pacote SID** e chave de **segurança**.
3. Selecione **Guardar**.

   ![Screenshot que mostra as caixas de chaves de pacote SID e segurança](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

## <a name="next-steps"></a>Passos seguintes

Para um tutorial com instruções passo a passo para o envio de notificações push para aplicações da Plataforma Universal windows utilizando hubs de notificação Azure e Serviço de Notificação push do Windows (WNS), consulte [Enviar notificações para aplicações UWP utilizando centros de notificação Azure](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
