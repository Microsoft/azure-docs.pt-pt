---
title: Configure o Serviço de Notificação push da Microsoft em Hubs de Notificação Azure [ Microsoft Docs
description: Saiba como configurar as definições do Microsoft Push Notification Service para um hub de notificação Azure.
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
ms.openlocfilehash: 99f29e7910fe6070c6202f6a936173455f979732
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80127333"
---
# <a name="configure-microsoft-push-notification-service-mpns-settings-in-the-azure-portal"></a>Configure as definições do Microsoft Push Notification Service (MPNS) no portal Azure

Este artigo mostra-lhe como configurar as definições do Microsoft Push Notification Service (MPNS) para um hub de notificação Azure utilizando o portal Azure. 

## <a name="prerequisites"></a>Pré-requisitos
Se ainda não criou um centro de notificação, crie um agora. Para mais informações, consulte Criar um hub de [notificação Azure no portal Azure.](create-notification-hub-portal.md) 

## <a name="configure-microsoft-push-notification-service-mpns"></a>Configure o Microsoft Push Notification Service (MPNS)

O seguinte procedimento dá-lhe passos para configurar as definições do Microsoft Push Notification Service (MPNS) para um centro de notificação: 

1. No portal Azure, na página Do Centro de **Notificação,** selecione **Windows Phone (MPNS)** no menu esquerdo.
1. Ativar notificações push não autenticadas ou autenticadas:

   a. Para ativar notificações push não autenticadas, selecione **Ativar** > o impulso não autenticado**Save**.

      ![Screenshot que mostra como ativar notificações push não autenticadas](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Para permitir notificações de push autenticadas:
      * Na barra de ferramentas, selecione **Certificado de Upload**.
      * Selecione o ícone do ficheiro e, em seguida, selecione o ficheiro de certificado.
      * Introduza a palavra-passe para o certificado.
      * Selecione **OK**.
      * Na página **Windows Phone (MPNS),** selecione **Guardar**.

## <a name="next-steps"></a>Passos seguintes
Para um tutorial com instruções passo a passo para empurrar notificações para dispositivos Windows Phone utilizando hubs de notificação Do Azure e Microsoft Push Notification Service (MPNS), consulte [notificações push para aplicações do Windows Phone utilizando Centros](notification-hubs-windows-mobile-push-notifications-mpns.md)de Notificação .

