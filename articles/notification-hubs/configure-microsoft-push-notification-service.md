---
title: Configurar o serviço de notificação por push da Microsoft nos hubs de notificação do Azure | Microsoft Docs
description: Saiba como definir as configurações do serviço de notificação por push da Microsoft para um hub de notificação do Azure.
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
ms.openlocfilehash: 0d7bf5410e05bb74a215fb7a95c704673b764b93
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212487"
---
# <a name="configure-microsoft-push-notification-service-mpns-settings-for-a-notification-hub-in-the-azure-portal"></a>Definir as configurações do MPNS (serviço de notificação por push da Microsoft) para um hub de notificação no portal do Azure
Este artigo mostra como definir as configurações do MPNS (serviço de notificação por push da Microsoft) para um hub de notificação do Azure usando o portal do Azure. 

## <a name="prerequisites"></a>Pré-requisitos
Se você ainda não criou um hub de notificação, crie um agora. Para obter mais informações, consulte [criar um hub de notificação do Azure no portal do Azure](create-notification-hub-portal.md). 

## <a name="configure-microsoft-push-notification-service-mpns"></a>Configurar o serviço de notificação por push da Microsoft (MPNS)

O procedimento a seguir fornece as etapas para definir as configurações do serviço de notificação por push da Microsoft (MPNS) para um hub de notificação: 

1. Na portal do Azure, na página **Hub de notificação** , selecione **Windows Phone (MPNS)** no menu à esquerda.
1. Habilitar notificações por push autenticadas ou não autenticadas:

   a. Para habilitar as notificações por push não autenticadas, selecione Habilitar**gravação** **por push** > não autenticado.

      ![Captura de tela que mostra como habilitar notificações por push não autenticadas](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Para habilitar as notificações por push autenticadas:
      * Na barra de ferramentas, selecione **carregar certificado**.
      * Selecione o ícone arquivo e, em seguida, selecione o arquivo de certificado.
      * Introduza a palavra-passe para o certificado.
      * Selecione **OK**.
      * Na página **Windows Phone (MPNS)** , selecione **salvar**.

## <a name="next-steps"></a>Passos seguintes
Para obter um tutorial com as instruções passo a passo para enviar notificações por push para Windows Phone dispositivos usando os hubs de notificação do Azure e o MPNS (serviço de notificação por push da Microsoft), consulte [notificações por push para Windows Phone aplicativos usando os hubs de notificação](notification-hubs-windows-mobile-push-notifications-mpns.md).

