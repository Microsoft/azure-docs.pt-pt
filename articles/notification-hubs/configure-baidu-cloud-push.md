---
title: Configurar o Baidu Cloud Push nos Hubs de notificação do Azure | Documentos da Microsoft
description: Saiba como configurar as definições do Baidu para um hub de notificação do Azure.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: 9942b1d4d8b5d538f5150a36e596753282039be7
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488382"
---
# <a name="configure-baidu-cloud-push-settings-for-a-notification-hub-in-the-azure-portal"></a>Configurar as definições de Push do Baidu Cloud para um hub de notificação no portal do Azure
Este artigo mostra-lhe como configurar as definições de Push do Baidu Cloud para um hub de notificação do Azure com o portal do Azure. 

## <a name="prerequisites"></a>Pré-requisitos
Se ainda não tiver criado um hub de notificação, crie uma agora. Para obter mais informações, consulte [criar um hub de notificação do Azure no portal do Azure](create-notification-hub-portal.md). 

## <a name="configure-baidu-cloud-push"></a>Configure Baidu Cloud Push
O procedimento seguinte dá-lhe os passos para configurar as definições de Push do Baidu Cloud para um hub de notificação:

1. No portal do Azure, sobre o **Hub de notificação** página, selecione **Baidu (Android China)** no menu da esquerda. 
2. Introduza o **chave de Api** que obteve na consola do Baidu, no projeto Baidu cloud push. 
3. Introduza o **chave secreta** que obteve na consola do Baidu, no projeto Baidu cloud push. 
4. Selecione **Guardar**. 

    ![Captura de ecrã dos Hubs de notificação que mostra a configuração para as notificações push do Baidu (Android China)](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

## <a name="next-steps"></a>Passos Seguintes
Para obter um tutorial com instruções passo a passo para enviar notificações push aos Baidu com Notification Hubs do Azure e o Push do Baidu Cloud, veja [introdução aos Hubs de notificação utilizando o Baidu](notification-hubs-baidu-china-android-notifications-get-started.md).
