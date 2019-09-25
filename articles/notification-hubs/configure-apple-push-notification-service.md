---
title: Configurar Apple Push Notification Service nos hubs de notificação do Azure | Microsoft Docs
description: Saiba como configurar um hub de notificação do Azure com configurações de Apple Push Notification Service (APNS).
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
ms.openlocfilehash: 74e7e3c74934e292b668b8bff594a5efbca19716
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212557"
---
# <a name="configure-apple-push-notification-service-apns-settings-for-a-notification-hub-in-the-azure-portal"></a>Definir configurações de Apple Push Notification Service (APNS) para um hub de notificação no portal do Azure
Este artigo mostra como definir as configurações de Apple Push Notification Service (APNS) para um hub de notificação do Azure usando o portal do Azure. 

## <a name="prerequisites"></a>Pré-requisitos
Se você ainda não criou um hub de notificação, crie um agora. Para obter mais informações, consulte [criar um hub de notificação do Azure no portal do Azure](create-notification-hub-portal.md). 

## <a name="configure-apple-push-notification-service"></a>Configurar Apple Push Notification Service

O procedimento a seguir fornece as etapas para definir as configurações de Apple Push Notification Service (APNS) para um hub de notificação:

1. Na portal do Azure, na página **Hub de notificação** , selecione **Apple (APNS)** no menu à esquerda.

1. Para o **modo de autenticação**, selecione **certificado** ou **token**.

   a. Se você selecionar **certificado**:
   * Selecione o ícone arquivo e, em seguida, selecione o arquivo *. p12* que você deseja carregar.
   * Insira uma senha.
   * Selecione o modo **Sandbox**. Ou, para enviar notificações por push aos usuários que compraram seu aplicativo da loja, selecione modo de **produção** .

     ![Captura de tela de uma configuração de certificado APNS no portal do Azure](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)

   b. Se você selecionar **token**:

   * Insira os valores para **ID da chave**, **ID do pacote**, **ID da equipe**e **token**.
   * Selecione o modo **Sandbox**. Ou, para enviar notificações por push aos usuários que compraram seu aplicativo da loja, selecione modo de **produção** .

     ![Captura de tela de uma configuração de token APNS no portal do Azure](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-token.png)

## <a name="next-steps"></a>Passos seguintes
Para obter um tutorial com as instruções passo a passo para enviar notificações para dispositivos iOS, consulte o seguinte artigo: [Notificações por push para dispositivos iOS usando hubs de notificação e APNS](notification-hubs-ios-apple-push-notification-apns-get-started.md)
