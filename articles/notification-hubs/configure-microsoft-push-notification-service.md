---
title: Configure o Serviço de Notificação push da Microsoft em Azure Notification Hubs | Microsoft Docs
description: Saiba como configurar as definições do Microsoft Push Notification Service para um hub de notificação Azure.
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
ms.openlocfilehash: 6784331dccd779390ace7dc632d54fe54aedc458
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87760996"
---
# <a name="configure-microsoft-push-notification-service-mpns-settings-in-the-azure-portal"></a>Configurar as definições do Microsoft Push Notification Service (MPNS) no portal Azure

Este artigo mostra-lhe como configurar as definições do Microsoft Push Notification Service (MPNS) para um hub de notificação Azure utilizando o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Se ainda não criou um centro de notificação, crie um agora. Para mais informações, consulte [Criar um centro de notificação Azure no portal Azure](create-notification-hub-portal.md).

## <a name="configure-microsoft-push-notification-service-mpns"></a>Configure o Serviço de Notificação push da Microsoft (MPNS)

O procedimento que se segue descreve como configurar as definições do Microsoft Push Notification Service (MPNS) para um centro de notificação:

1. No portal Azure, na página **'Centro de Notificação',** selecione **Windows Phone (MPNS)** no menu esquerdo.
2. Ativar notificações push não autenticadas ou autenticadas:

   a. Para ativar notificações push não autenticadas, selecione **Ative push**  >  **Save**.

      ![Screenshot que mostra como permitir notificações push não autenticadas](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Para permitir notificações push autenticadas:
      * Na barra de ferramentas, selecione **O Certificado de Upload**.
      * Selecione o ícone do ficheiro e, em seguida, selecione o ficheiro de certificado.
      * Introduza a palavra-passe para o certificado.
      * Selecione **OK**.
      * Na página **Do Windows Phone (MPNS),** selecione **Guardar**.

## <a name="next-steps"></a>Passos seguintes

Para obter um tutorial com instruções passo a passo para empurrar notificações para dispositivos Windows Phone utilizando os Hubs de Notificação Azure e o Microsoft Push Notification Service (MPNS), consulte [Enviar notificações push para apps do Windows Phone utilizando os Centros de Notificação](notification-hubs-windows-mobile-push-notifications-mpns.md).
