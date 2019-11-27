---
title: Configurar notificações por push nos hubs de notificação do Azure | Microsoft Docs
description: Saiba como configurar os hubs de notificação do Azure no portal do Azure usando as configurações do PNS (sistema de notificação de plataforma).
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.topic: quickstart
ms.date: 02/14/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 02/14/2019
ms.openlocfilehash: 951f03f581906e45946ef75742421ba27d405267
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406986"
---
# <a name="set-up-push-notifications-in-a-notification-hub-in-the-azure-portal"></a>Configurar notificações por push em um hub de notificação no portal do Azure

Os hubs de notificação do Azure fornecem um mecanismo de envio por push que é fácil de usar e que pode ser dimensionado. Use os hubs de notificação para enviar notificações para qualquer plataforma (iOS, Android, Windows, Baidu) e de qualquer back-end (nuvem ou local). Para obter mais informações, consulte [o que são hubs de notificação do Azure?](notification-hubs-push-notification-overview.md).

Neste guia de início rápido, você usará as configurações do PNS (sistema de notificação de plataforma) nos hubs de notificação para configurar notificações por push em várias plataformas. O guia de início rápido mostra as etapas a serem executadas na portal do Azure.

Se você ainda não criou um hub de notificação, crie um agora. Para obter mais informações, consulte [criar um hub de notificação do Azure no portal do Azure](create-notification-hub-portal.md). 

## <a name="apple-push-notification-service"></a>Serviço Apple Push Notification

Para configurar Apple Push Notification Service (APNS):

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

     ![Captura de tela de uma configuração de token APNS no portal do Azure](./media/configure-notification-hub-portal-pns-settings/notification-hubs-apple-config-token.png)

Para obter mais informações, consulte [notificações por push para o Ios usando os hubs de notificação do Azure](notification-hubs-ios-apple-push-notification-apns-get-started.md).

## <a name="google-firebase-cloud-messaging"></a>Mensagens de nuvem do Google firebase

Para configurar notificações por push para o Google firebase Cloud Messaging (FCM):

1. Na portal do Azure, na página **Hub de notificação** , selecione **Google (GCM/FCM)** no menu à esquerda. 
2. Cole a **chave de API** para o projeto FCM que você salvou anteriormente. 
3. Selecione **Guardar**. 

   ![Captura de tela que mostra como configurar os hubs de notificação para o Google FCM](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

Quando você concluir essas etapas, um alerta indicará que o Hub de notificação foi atualizado com êxito. O botão **salvar** está desabilitado. 

Para obter mais informações, consulte [notificações por push para dispositivos Android usando hubs de notificação e o Google FCM](notification-hubs-android-push-notification-google-fcm-get-started.md).

## <a name="windows-push-notification-service"></a>Serviço de notificação por push do Windows

Para configurar o serviço de notificação por push do Windows (WNS):

1. Na portal do Azure, na página **Hub de notificação** , selecione **Windows (WNS)** no menu à esquerda.
2. Insira valores para o **SID do pacote** e a chave de **segurança**.
3. Selecione **Guardar**.

   ![Captura de tela que mostra as caixas SID do pacote e chave de segurança](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

Para obter informações, consulte [enviar notificações para aplicativos UWP usando os hubs de notificação do Azure](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).

## <a name="microsoft-push-notification-service-for-windows-phone"></a>Serviço de notificação por push da Microsoft para Windows Phone

Para configurar o MPNS (serviço de notificação por push da Microsoft) para Windows Phone: 

1. Na portal do Azure, na página **Hub de notificação** , selecione **Windows Phone (MPNS)** no menu à esquerda.
1. Habilitar notificações por push autenticadas ou não autenticadas:

   a. Para habilitar as notificações por push não autenticadas, selecione **habilitar envio por push não autenticado** > **salvar**.

      ![Captura de tela que mostra como habilitar notificações por push não autenticadas](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Para habilitar as notificações por push autenticadas:
      * Na barra de ferramentas, selecione **carregar certificado**.
      * Selecione o ícone arquivo e, em seguida, selecione o arquivo de certificado.
      * Introduza a palavra-passe para o certificado.
      * Selecione **OK**.
      * Na página **Windows Phone (MPNS)** , selecione **salvar**.

Para obter mais informações, consulte [notificações por push para Windows Phone aplicativos usando os hubs de notificação](notification-hubs-windows-mobile-push-notifications-mpns.md).
      

## <a name="baidu-android-china"></a>Baidu (Android China)

Para configurar notificações por push para Baidu:

1. Na portal do Azure, na página **Hub de notificação** , selecione **Baidu (Android China)** no menu à esquerda. 
2. Insira a **chave de API** que você obteve no console do Baidu no projeto Push da nuvem Baidu. 
3. Insira a **chave secreta** que você obteve no console do Baidu no projeto Push da nuvem Baidu. 
4. Selecione **Guardar**. 

    ![Captura de tela de hubs de notificação que mostra a configuração Baidu (Android China) para notificações por push](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

Quando você concluir essas etapas, um alerta indicará que o Hub de notificação foi atualizado com êxito. O botão **salvar** está desabilitado. 

Para obter mais informações, consulte Introdução [aos hubs de notificação usando Baidu](notification-hubs-baidu-china-android-notifications-get-started.md).

## <a name="next-steps"></a>Passos seguintes
Neste guia de início rápido, você aprendeu a definir as configurações do sistema de notificação de plataforma para um hub de notificação no portal do Azure. 

Para saber mais sobre como enviar notificações por push para várias plataformas, consulte estes tutoriais:

- [Notificações por push para dispositivos iOS usando hubs de notificação e APNS](notification-hubs-ios-apple-push-notification-apns-get-started.md)
- [Notificações por push para dispositivos Android usando os hubs de notificação e o Google FCM](notification-hubs-android-push-notification-google-fcm-get-started.md)
- [Notificações por push para um aplicativo UWP em execução em um dispositivo Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
- [Notificações por push para um aplicativo Windows Phone 8 usando o MPNS](notification-hubs-windows-mobile-push-notifications-mpns.md)
- [Notificações por push usando hubs de notificação e push da nuvem Baidu](notification-hubs-baidu-china-android-notifications-get-started.md)
