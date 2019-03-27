---
title: Configurar o serviço de notificações Push da Microsoft nos Hubs de notificação do Azure | Documentos da Microsoft
description: Saiba como configurar as definições de serviço de notificação Push da Microsoft para um hub de notificação do Azure.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: 1c76b44438e6527439d0a370c92f4120424b8da5
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488354"
---
# <a name="configure-microsoft-push-notification-service-mpns-settings-for-a-notification-hub-in-the-azure-portal"></a>Configurar definições de Microsoft Push Notification Service (MPNS) para um hub de notificação no portal do Azure
Este artigo mostra-lhe como configurar as definições do Microsoft Push Notification Service (MPNS) para um hub de notificação do Azure com o portal do Azure. 

## <a name="prerequisites"></a>Pré-requisitos
Se ainda não tiver criado um hub de notificação, crie uma agora. Para obter mais informações, consulte [criar um hub de notificação do Azure no portal do Azure](create-notification-hub-portal.md). 

## <a name="configure-microsoft-push-notification-service-mpns"></a>Configurar o serviço de notificações Push da Microsoft (MPNS)

O procedimento seguinte dá-lhe os passos para configurar as definições do Microsoft Push Notification Service (MPNS) para um hub de notificação: 

1. No portal do Azure, sobre o **Hub de notificação** página, selecione **Windows Phone (MPNS)** no menu da esquerda.
1. Ative qualquer uma das notificações push não autenticado ou autenticado:

   a. Para ativar as notificações push não autenticado, selecione **ativar push não autenticado** > **guardar**.

      ![Captura de ecrã que mostra como ativar notificações push não autenticado](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Para ativar as notificações push autenticado:
      * Na barra de ferramentas, selecione **carregar certificado**.
      * Selecione o ícone de ficheiro e, em seguida, selecione o ficheiro de certificado.
      * Introduza a palavra-passe para o certificado.
      * Selecione **OK**.
      * Sobre o **Windows Phone (MPNS)** página, selecione **guardar**.

## <a name="next-steps"></a>Passos Seguintes
Para obter um tutorial com instruções passo a passo para enviar notificações push para dispositivos Windows Phone com os Hubs de notificação do Azure e o Microsoft Push Notification Service (MPNS), consulte [notificações Push para aplicações Windows Phone ao utilizar a notificação Os hubs](notification-hubs-windows-mobile-push-notifications-mpns.md).

