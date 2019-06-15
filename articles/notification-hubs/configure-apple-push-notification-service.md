---
title: Configurar o serviço Apple Push Notification nos Hubs de notificação do Azure | Documentos da Microsoft
description: Saiba como configurar um hub de notificação do Azure com definições de Apple Push Notification Service (APNS).
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: 9a9db9f05895569b050e56cdeec1ee2ee25af0df
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60237819"
---
# <a name="configure-apple-push-notification-service-apns-settings-for-a-notification-hub-in-the-azure-portal"></a>Configurar definições de Apple Push Notification Service (APNS) para um hub de notificação no portal do Azure
Este artigo mostra-lhe como configurar definições de Apple Push Notification Service (APNS) para um hub de notificação do Azure com o portal do Azure. 

## <a name="prerequisites"></a>Pré-requisitos
Se ainda não tiver criado um hub de notificação, crie uma agora. Para obter mais informações, consulte [criar um hub de notificação do Azure no portal do Azure](create-notification-hub-portal.md). 

## <a name="configure-apple-push-notification-service"></a>Configurar o serviço Apple Push Notification

O procedimento seguinte dá-lhe os passos para configurar definições de Apple Push Notification Service (APNS) para um hub de notificação:

1. No portal do Azure, sobre o **Hub de notificação** página, selecione **Apple (APNS)** no menu da esquerda.

1. Para **modo de autenticação**, selecione **certificado** ou **Token**.

   a. Se selecionou **certificado**:
   * Selecione o ícone de ficheiro e, em seguida, selecione o *. p12* ficheiro que pretende carregar.
   * Introduza uma palavra-passe.
   * Selecione o modo **Sandbox**. Ou, para enviar notificações push para utilizadores que adquirido a aplicação da loja, selecione **produção** modo.

     ![Captura de ecrã de um APNS certificado configuração no portal do Azure](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)

   b. Se selecionou **Token**:

   * Introduza os valores para **ID de chave**, **ID do pacote**, **ID da equipa**, e **Token**.
   * Selecione o modo **Sandbox**. Ou, para enviar notificações push para utilizadores que adquirido a aplicação da loja, selecione **produção** modo.

     ![Captura de ecrã de um APNS token configuração no portal do Azure](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-token.png)

## <a name="next-steps"></a>Passos Seguintes
Para obter um tutorial com instruções passo a passo para enviar notificações para dispositivos iOS, veja o seguinte artigo: [Enviar notificações push para dispositivos iOS com Hubs de notificação e APNS](notification-hubs-ios-apple-push-notification-apns-get-started.md)
