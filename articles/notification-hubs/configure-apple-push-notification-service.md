---
title: Configure o Serviço de Notificação push da Apple em Azure Notification Hubs / Microsoft Docs
description: Saiba como configurar um hub de notificação Azure com as definições do Apple Push Notification Service (APNS).
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 06/22/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 63c7e0c9569428b55420911f253deee52ce440cb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85255403"
---
# <a name="configure-apple-push-notification-service-settings-for-a-notification-hub-in-the-azure-portal"></a>Configurar as definições do Serviço de Notificação apple push para um centro de notificação no portal Azure

Este artigo mostra-lhe como configurar as definições do Apple Push Notification Service (APNS) para um hub de notificação Azure utilizando o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Se ainda não criou um centro de notificação, crie um agora. Para mais informações, consulte [Criar um centro de notificação Azure no portal Azure](create-notification-hub-portal.md).

## <a name="configure-apple-push-notification-service"></a>Configure o serviço de notificação push da Apple

O procedimento a seguir dá-lhe passos para configurar as definições do Apple Push Notification Service (APNS) para um centro de notificação:

1. No portal Azure, na página **'Centro de Notificação',** selecione **Apple (APNS)** no menu esquerdo.

1. Para **modo de autenticação**, selecione **Certificado** ou **Token**.

   - Se selecionar **o Certificado:**
      - Selecione o ícone do ficheiro e, em seguida, selecione o ficheiro *.p12* que pretende carregar.
      - Introduza uma senha.
      - Selecione o modo **Sandbox**. Ou, para enviar notificações push aos utilizadores que compraram a sua app na loja, selecione o modo **de Produção.**

     ![Screenshot de uma configuração de certificado APNS no portal Azure](./media/configure-apple-push-notification-service/notification-hubs-apple-config-cert.png)

   - Se selecionar **Token:**
      - Introduza os valores para **Key ID,** **Bundle ID,** **Team ID**e **Token**.
      - Selecione o modo **Sandbox**. Ou, para enviar notificações push aos utilizadores que compraram a sua app na loja, selecione o modo **de Produção.**

     ![Screenshot de uma configuração de token APNS no portal Azure](./media/configure-apple-push-notification-service/notification-hubs-apple-config-token.png)

## <a name="next-steps"></a>Passos seguintes

Para um tutorial com instruções passo a passo para o envio de notificações para dispositivos iOS, consulte o seguinte artigo: [Enviar notificações push para aplicações iOS usando Hubs de Notificação Azure](ios-sdk-get-started.md).
