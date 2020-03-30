---
title: Configure O Serviço de Notificação push da Apple em Hubs de Notificação Azure [ Microsoft Docs
description: Saiba como configurar um hub de notificação Azure com as definições do Apple Push Notification Service (APNS).
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
ms.openlocfilehash: eb1122ba3de0002507589d3e607d1e39d905c308
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127520"
---
# <a name="configure-apple-push-notification-service-settings-for-a-notification-hub-in-the-azure-portal"></a>Configure as definições do Serviço de Notificação push da Apple para um hub de notificação no portal Azure

Este artigo mostra-lhe como configurar as definições do Apple Push Notification Service (APNS) para um hub de notificação Azure utilizando o portal Azure. 

## <a name="prerequisites"></a>Pré-requisitos
Se ainda não criou um centro de notificação, crie um agora. Para mais informações, consulte Criar um hub de [notificação Azure no portal Azure.](create-notification-hub-portal.md) 

## <a name="configure-apple-push-notification-service"></a>Configure o serviço de notificação push da Apple

O seguinte procedimento dá-lhe passos para configurar as definições do Apple Push Notification Service (APNS) para um centro de notificação:

1. No portal Azure, na página Do Hub de **Notificação,** selecione **Apple (APNS)** no menu esquerdo.

1. Para **o modo de autenticação,** selecione **certificado** ou **token**.

   a. Se selecionar **Certificado:**
   * Selecione o ícone do ficheiro e, em seguida, selecione o ficheiro *.p12* que pretende carregar.
   * Introduza uma senha.
   * Selecione o modo **Sandbox**. Ou, para enviar notificações push aos utilizadores que compraram a sua aplicação na loja, selecione modo **Produção.**

     ![Screenshot de uma configuração de certificado APNS no portal Azure](./media/configure-apple-push-notification-service/notification-hubs-apple-config-cert.png)

   b. Se selecionar **Token:**

   * Introduza os valores para **ID de chave,** **ID do pacote,** **ID da equipa**e **Token**.
   * Selecione o modo **Sandbox**. Ou, para enviar notificações push aos utilizadores que compraram a sua aplicação na loja, selecione modo **Produção.**

     ![Screenshot de uma configuração de token APNS no portal Azure](./media/configure-apple-push-notification-service/notification-hubs-apple-config-token.png)

## <a name="next-steps"></a>Passos seguintes
Para um tutorial com instruções passo a passo para empurrar notificações para dispositivos iOS, consulte o seguinte artigo: [Enviar notificações aos dispositivos iOS utilizando Centros de Notificação e APNS](notification-hubs-ios-apple-push-notification-apns-get-started.md)
