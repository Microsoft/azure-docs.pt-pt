---
title: Configurar notificações push nos Centros de Notificação do Azure [ Microsoft Docs
description: Saiba como configurar hubs de notificação Azure no portal Azure utilizando as definições do sistema de notificação da plataforma (PNS).
services: notification-hubs
author: sethmanheim
manager: femila
editor: dbradish-microsoft
ms.service: notification-hubs
ms.workload: mobile
ms.topic: quickstart
ms.date: 02/14/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 02/14/2019
ms.openlocfilehash: 78afb124ee1d1ab9b212197fb7a7140f88de9940
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80349518"
---
# <a name="quickstart-set-up-push-notifications-in-a-notification-hub"></a>Quickstart: Configurar notificações push num centro de notificação

O Azure Notification Hubs fornece um motor push que é fácil de usar e que se esescala. Utilize centros de notificação para enviar notificações para qualquer plataforma (iOS, Android, Windows, Baidu) e de qualquer extremidade traseira (cloud ou on-local). Para mais informações, consulte [o que é o Azure Notification Hubs?](notification-hubs-push-notification-overview.md)

Neste arranque rápido, utilizará as definições do sistema de notificação da plataforma (PNS) nos Centros de Notificação para configurar notificações push em várias plataformas. O quickstart mostra-lhe os passos a tomar no portal Azure.  O [Google Firebase Cloud Messaging](/azure/notification-hubs/configure-notification-hub-portal-pns-settings?tabs=azure-cli#google-firebase-cloud-messaging-fcm) inclui instruções para a utilização do Azure CLI.

Se ainda não criou um centro de notificação, crie um agora. Para mais informações, consulte Criar um hub de [notificação Azure no portal Azure](create-notification-hub-portal.md) ou Criar um hub de [notificação Azure utilizando o Azure CLI](create-notification-hub-azure-cli.md)

## <a name="apple-push-notification-service"></a>Serviço Apple Push Notification

Para configurar o Apple Push Notification Service (APNS):

1. No portal Azure, na página Do Hub de **Notificação,** selecione **Apple (APNS)** a partir do menu esquerdo.

1. Para **o modo de autenticação,** selecione **certificado** ou **token**.

   a. Se selecionar **Certificado:**
   * Selecione o ícone do ficheiro e, em seguida, selecione o ficheiro *.p12* que pretende carregar.
   * Introduza uma senha.
   * Selecione o modo **Sandbox**. Ou, para enviar notificações push aos utilizadores que compraram a sua aplicação na loja, selecione modo **Produção.**

     ![Screenshot de uma configuração de certificado APNS no portal Azure](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)

   b. Se selecionar **Token:**

   * Introduza os valores para **ID de chave,** **ID do pacote,** **ID da equipa**e **Token**.
   * Selecione o modo **Sandbox**. Ou, para enviar notificações push aos utilizadores que compraram a sua aplicação na loja, selecione modo **Produção.**

     ![Screenshot de uma configuração de token APNS no portal Azure](./media/configure-notification-hub-portal-pns-settings/notification-hubs-apple-config-token.png)

Para mais informações, consulte [notificações push para iOS utilizando hubs de notificação Azure](notification-hubs-ios-apple-push-notification-apns-get-started.md).

## <a name="google-firebase-cloud-messaging-fcm"></a>Google Firebase Cloud Messaging (FCM)

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para configurar notificações push para o Google FCM:

1. No portal Azure, na página Do Hub de **Notificação,** selecione **Google (GCM/FCM)** do menu esquerdo.
2. Colar a **Chave API** para o projeto Google FCM que guardou anteriormente.
3. Selecione **Guardar**.

   ![Screenshot que mostra como configurar Centros de Notificação para o Google FCM](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

Ao completar estes passos, um alerta indica que o centro de notificação foi atualizado com sucesso. O botão **Guardar** está desativado.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

### <a name="prerequisites"></a>Pré-requisitos

Antes de começar, deve ter o seguinte:

* A versão [Azure CLI](/cli/azure/install-azure-cli) 2.0.67 ou posterior.

* A extensão Azure CLI para Centros de [Notificação](/cli/azure/ext/notification-hub/notification-hub).
* A **Chave API** para um projeto de Mensagens Cloud (FCM) da Base de Fogo do Google.

### <a name="set-up-push-notifications-for-google-fcm"></a>Configurar notificações push para o Google FCM

1. Utilize o comando de [atualização de gcm credencial az notification-hub](/cli/azure/ext/notification-hub/notification-hub/credential/gcm#ext-notification-hub-az-notification-hub-credential-gcm-update) para adicionar a sua Chave API google ao seu centro de notificação.

   ```azurecli
   az notification-hub credential gcm update --resource-group spnhubrg --namespace-name spnhubns    --notification-hub-name spfcmtutorial1nhub --google-api-key myKey
   ```

2. A Aplicação Android precisa de uma cadeia de ligação para se conectar com o centro de notificação.  Utilize o comando de [lista de regras de autorização az-hub](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list) para listar as políticas de acesso disponíveis.  Utilize o comando de [regras de regras de regras de autorização az-hub](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) para obter as cordas de ligação da política de acesso.  Especifique o **PrimaryConnectionString** ou `--query` **o secondaryConnectionString** no parâmetro para obter a cadeia de ligação primária diretamente.

   ```azurecli
   #list access policies for a notification hub
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table

   #list keys and connection strings for a notification hub access policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name myAccessPolicyName --output json

   #get the primaryConnectionString for an access policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name myAccessPolicyName --query primaryConnectionString
   ```

3. Utilize o comando [de teste de teste az-hub](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-test-send) para testar o envio de mensagens para a Aplicação Android.

   ```azurecli
   #test with message body
   az notification-hub test-send --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --notification-format gcm --message "my message body"

   #test with JSON string
   az notification-hub test-send --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --notification-format gcm --payload "{\"data\":{\"message\":\"my JSON string\"}}"
   ```

Obtenha referências Azure CLI para outras plataformas com o comando [credencial az notification-hub.](/cli/azure/ext/notification-hub/notification-hub/credential)

---

Para obter mais informações sobre o push notificações para uma aplicação Android, consulte [Enviar notificações push para dispositivos Android usando firebase](notification-hubs-android-push-notification-google-fcm-get-started.md).

## <a name="windows-push-notification-service"></a>Serviço de Notificação push windows

Para configurar o Windows Push Notification Service (WNS):

1. No portal Azure, na página Centro de **Notificação,** selecione **Windows (WNS)** do menu esquerdo.
2. Introduza valores para **pacote SID** e Chave **de Segurança**.
3. Selecione **Guardar**.

   ![Screenshot que mostra as caixas de chaves de pacote SID e segurança](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

Para obter informações, consulte [Enviar notificações para aplicações UWP utilizando hubs de notificação Do Azure](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).

## <a name="microsoft-push-notification-service-for-windows-phone"></a>Microsoft Push Notification Service para Windows Phone

Para configurar o Microsoft Push Notification Service (MPNS) para windows Phone:

1. No portal Azure, na página Centro de **Notificação,** selecione **Windows Phone (MPNS)** do menu esquerdo.
1. Ativar notificações push não autenticadas ou autenticadas:

   a. Para ativar notificações push não autenticadas, selecione **Ativar** > o impulso não autenticado**Save**.

      ![Screenshot que mostra como ativar notificações push não autenticadas](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Para permitir notificações de push autenticadas:
      * Na barra de ferramentas, selecione **Certificado de Upload**.
      * Selecione o ícone do ficheiro e, em seguida, selecione o ficheiro de certificado.
      * Introduza a palavra-passe para o certificado.
      * Selecione **OK**.
      * Na página **Windows Phone (MPNS),** selecione **Guardar**.

Para mais informações, consulte [notificações push para aplicações do Windows Phone utilizando Centros](notification-hubs-windows-mobile-push-notifications-mpns.md)de Notificação .

## <a name="baidu-android-china"></a>Baidu (Android China)

Para configurar notificações push para Baidu:

1. No portal Azure, na página Do Hub de **Notificação,** selecione **Baidu (Android China)** a partir do menu esquerdo.
2. Introduza a **Chave Api** que obteve da consola Baidu no projeto baidu cloud push.
3. Introduza a **Chave Secreta** que obteve da consola Baidu no projeto baidu cloud push.
4. Selecione **Guardar**.

    ![Screenshot dos Centros de Notificação que mostram a configuração baidu (Android China) para notificações push](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

Ao completar estes passos, um alerta indica que o centro de notificação foi atualizado com sucesso. O botão **Guardar** está desativado.

Para mais informações, consulte [Começar com Centros de Notificação utilizando Baidu](notification-hubs-baidu-china-android-notifications-get-started.md).

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a configurar as definições do sistema de notificação da plataforma para um hub de notificação no portal Azure.

Para saber mais sobre como empurrar notificações para várias plataformas, consulte estes tutoriais:

-[Push notificações para dispositivos iOS utilizando Hubs](notification-hubs-ios-apple-push-notification-apns-get-started.md)de notificação e notificações de push
-[APNS para dispositivos Android usando Hubs](notification-hubs-android-push-notification-google-fcm-get-started.md)
-de notificação e notificações do Google FCM[Push para uma aplicação UWP que funciona num dispositivo](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
-Windows[Pressione as notificações para uma aplicação do Windows Phone 8 utilizando](notification-hubs-windows-mobile-push-notifications-mpns.md)
-notificações mpns Push usando Hubs de[notificação e push de nuvem baidu](notification-hubs-baidu-china-android-notifications-get-started.md)
