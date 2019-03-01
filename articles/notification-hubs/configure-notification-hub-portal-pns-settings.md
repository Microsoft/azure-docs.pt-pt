---
title: Configure as notificações push nos Hubs de notificação do Azure | Documentos da Microsoft
description: Saiba como configurar o Notification Hubs do Azure no portal do Azure, utilizando as definições de sistema (PNS) de notificação de plataforma.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: quickstart
ms.date: 02/14/2019
ms.author: jowargo
ms.openlocfilehash: 6d0076f2ddaee0a1a0daa4c1bf6b0c8bec2ff3e8
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "57010179"
---
# <a name="set-up-push-notifications-in-a-notification-hub-in-the-azure-portal"></a>Configure as notificações push num hub de notificação no portal do Azure

Os Hubs de notificação do Azure oferece um mecanismo de push que é fácil de usar e que aumenta horizontalmente. Utilize os Hubs de notificação para enviar notificações para qualquer plataforma (iOS, Android, Windows, Kindle e Baidu) e a partir de qualquer back-end (na cloud ou no local). Para obter mais informações, consulte [o que é o Notification Hubs do Azure?](notification-hubs-push-notification-overview.md).

Neste início rápido, irá utilizar as definições de sistema (PNS) de notificação de plataforma nos Hubs de notificação para definir as notificações push em várias plataformas. O guia de introdução mostra-lhe os passos a efetuar no portal do Azure.

Se ainda não tiver criado um hub de notificação, crie uma agora. Para obter mais informações, consulte [criar um hub de notificação do Azure no portal do Azure](create-notification-hub-portal.md). 

## <a name="apple-push-notification-service"></a>Serviço Apple Push Notification

Para definir a Apple Push Notification Service (APNS):

1. No portal do Azure, no **Hub de notificação**, selecione **Apple (APNS)**.

1. Para **modo de autenticação**, selecione **certificado** ou **Token**.

   a. Se selecionou **certificado**:
      * Selecione o ícone de ficheiro e, em seguida, selecione o *. p12* ficheiro que pretende carregar.
      * Introduza uma palavra-passe.
      * Selecione o modo **Sandbox**. Ou, para enviar notificações push para utilizadores que adquirido a aplicação da loja, selecione **produção** modo.

      ![Captura de ecrã de um APNS certificado configuração no portal do Azure](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)

   b. Se selecionou **Token**:

      * Introduza os valores para **Id de chave**, **Id do pacote**, **Id da equipa**, e **Token**.
      * Selecione o modo **Sandbox**. Ou, para enviar notificações push para utilizadores que adquirido a aplicação da loja, selecione **produção** modo.

      ![Captura de ecrã de um APNS token configuração no portal do Azure](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-token.png)

Para obter mais informações, consulte [notificações Push para iOS ao utilizar os Hubs de notificação do Azure](notification-hubs-ios-apple-push-notification-apns-get-started.md).

## <a name="google-firebase-cloud-messaging"></a>Google Firebase Cloud Messaging

Para definir as notificações push para o Google Firebase Cloud Messaging (FCM):

1. No portal do Azure, no **Hub de notificação**, selecione **Google (GCM/FCM)**. 
2. Colar o **chave de API** para o projeto do FCM que guardou anteriormente. 
3. Selecione **Guardar**. 

   ![Captura de ecrã que mostra como configurar os Hubs de notificação para o Google FCM](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

Quando concluir estes passos, um alerta indica que o hub de notificação foi atualizado com êxito. O **guardar** botão está desativado. 

Para obter mais informações, consulte [notificações Push para dispositivos Android com Hubs de notificação e o Google FCM](notification-hubs-android-push-notification-google-fcm-get-started.md).

## <a name="windows-push-notification-service"></a>Serviço de notificação Push do Windows

Para definir a cópia de segurança Windows Push Notification Service (WNS):

1. No portal do Azure, no **Hub de notificação**, selecione **Windows (WNS)**.
2. Introduza os valores para **SID do pacote** e **chave de segurança**.
3. Selecione **Guardar**.

   ![Captura de ecrã que mostra as caixas do SID do pacote e a chave de segurança](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

Para obter informações, consulte [enviar notificações para aplicações UWP com Notification Hubs do Azure](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).

## <a name="microsoft-push-notification-service-for-windows-phone"></a>Serviço de notificações Push da Microsoft para Windows Phone

Para definir a cópia de segurança Microsoft Push Notification Service (MPNS) para Windows Phone: 

1. No portal do Azure, no **Hub de notificação**, selecione **Windows Phone (MPNS)**.
1. Ative qualquer uma das notificações push não autenticado ou autenticado:

   a. Para ativar as notificações push não autenticado, selecione **ativar push não autenticado** > **guardar**.

      ![Captura de ecrã que mostra como ativar notificações push não autenticado](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Para ativar as notificações push autenticado:
      * Na barra de ferramentas, selecione **carregar certificado**.
      * Selecione o ícone de ficheiro e, em seguida, selecione o ficheiro de certificado.
      * Introduza a palavra-passe para o certificado.
      * Selecione **OK**.
      * Sobre o **Windows Phone (MPNS)** página, selecione **guardar**.

Para obter mais informações, consulte [notificações Push para aplicações Windows Phone com os Notification Hubs](notification-hubs-windows-mobile-push-notifications-mpns.md).
      
## <a name="amazon-device-messaging"></a>Amazon Device Messaging

Para definir as notificações push para o Amazon Device Messaging (ADM):

1. No portal do Azure, no **Hub de notificação**, selecione **Amazon (ADM)**.
2. Introduza os valores para **ID de cliente** e **segredo do cliente**.
3. Selecione **Guardar**.
    
   ![Definições de captura de ecrã do ADM no portal do Azure](./media/notification-hubs-kindle-get-started/notification-hub-adm-settings.png)

Para obter mais informações, consulte [introdução aos Hubs de notificação para aplicações Kindle](notification-hubs-kindle-amazon-adm-push-notification.md).

## <a name="baidu-android-china"></a>Baidu (Android China)

Para definir as notificações push para o Baidu:

1. No portal do Azure, no **Hub de notificação**, selecione **Baidu (Android China)**. 
2. Introduza o **chave de Api** que obteve na consola do Baidu, no projeto Baidu cloud push. 
3. Introduza o **chave secreta** que obteve na consola do Baidu, no projeto Baidu cloud push. 
4. Selecione **Guardar**. 

    ![Captura de ecrã dos Hubs de notificação que mostra a configuração para as notificações push do Baidu (Android China)](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

Quando concluir estes passos, um alerta indica que o hub de notificação foi atualizado com êxito. O **guardar** botão está desativado. 

Para obter mais informações, consulte [introdução aos Hubs de notificação utilizando o Baidu](notification-hubs-baidu-china-android-notifications-get-started.md).

## <a name="next-steps"></a>Passos Seguintes
Neste início rápido, aprendeu a configurar as definições de sistema de notificação de plataforma para um hub de notificação no portal do Azure. 

Para saber mais sobre como enviar notificações push para várias plataformas, veja estes tutoriais:

- [Enviar notificações push para dispositivos iOS com Hubs de notificação e APNS](notification-hubs-ios-apple-push-notification-apns-get-started.md)
- [Notificações push para dispositivos Android com Hubs de notificação e FCM do Google](notification-hubs-android-push-notification-google-fcm-get-started.md)
- [Notificações push para uma aplicação UWP em execução num dispositivo Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
- [Enviar notificações push para um aplicativo Windows Phone 8 com o MPNS](notification-hubs-windows-mobile-push-notifications-mpns.md)
- [Notificações push para uma aplicação Kindle](notification-hubs-kindle-amazon-adm-push-notification.md)
- [Notificações push utilizando a emissão de cloud os Hubs de notificação e Baidu](notification-hubs-baidu-china-android-notifications-get-started.md)
