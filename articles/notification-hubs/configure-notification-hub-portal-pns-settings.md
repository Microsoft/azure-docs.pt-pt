---
title: Configurar notificações push nos hubs de notificação do Azure / Microsoft Docs
description: Saiba como configurar os Hubs de Notificação Azure no portal Azure utilizando as definições do sistema de notificação da plataforma (PNS).
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: quickstart
ms.date: 06/22/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 02/14/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5dd1044895ba55d1fbc6be7f4f4a2d7f615daa16
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2020
ms.locfileid: "94887268"
---
# <a name="quickstart-set-up-push-notifications-in-a-notification-hub"></a>Quickstart: Configurar notificações push num centro de notificação

O Azure Notification Hubs fornece um motor de pressão fácil de usar e que escala. Utilize os Centros de Notificações para enviar notificações para qualquer plataforma (iOS, Android, Windows, Baidu) e a partir de qualquer extremidade traseira (nuvem ou no local). Para mais informações, veja [o que é a Azure Notification Hubs?](notification-hubs-push-notification-overview.md)

Neste arranque rápido, irá utilizar as definições do sistema de notificações da plataforma (PNS) nos Centros de Notificações para configurar notificações push em várias plataformas. O quickstart mostra-lhe os passos a dar no portal Azure. [O Google Firebase Cloud Messaging](?tabs=azure-cli#google-firebase-cloud-messaging-fcm) inclui instruções para a utilização do CLI Azure.

Se ainda não criou um centro de notificação, crie um agora. Para obter mais informações, consulte [Criar um centro de notificação Azure no portal Azure](create-notification-hub-portal.md) ou criar um centro de [notificação Azure utilizando o Azure CLI](create-notification-hub-azure-cli.md).

## <a name="apple-push-notification-service"></a>Serviço Apple Push Notification

Para configurar o Serviço de Notificação de Push apple (APNS):

1. No portal Azure, na página **'Centro de Notificação',** selecione **Apple (APNS)** do menu esquerdo.

1. Para **modo de autenticação**, selecione **Certificado** ou **Token**.

   a. Se selecionar **o Certificado:**
   * Selecione o ícone do ficheiro e, em seguida, selecione o ficheiro *.p12* que pretende carregar.
   * Introduza uma senha.
   * Selecione o modo **Sandbox**. Ou, para enviar notificações push aos utilizadores que compraram a sua app na loja, selecione o modo **de Produção.**

     ![Screenshot de uma configuração de certificado APNS no portal Azure](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)

   b. Se selecionar **Token:**

   * Introduza os valores para **Key ID,** **Bundle ID,** **Team ID** e **Token**.
   * Selecione o modo **Sandbox**. Ou, para enviar notificações push aos utilizadores que compraram a sua app na loja, selecione o modo **de Produção.**

     ![Screenshot de uma configuração de token APNS no portal Azure](./media/configure-notification-hub-portal-pns-settings/notification-hubs-apple-config-token.png)

Para obter mais informações, consulte [Enviar notificações push para aplicações iOS utilizando hubs de notificação Azure](ios-sdk-get-started.md).

## <a name="google-firebase-cloud-messaging-fcm"></a>Mensagens cloud do Google Firebase (FCM)

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para configurar notificações push para o Google FCM:

1. No portal Azure, na página **'Centro de Notificação',** selecione **Google (GCM/FCM)** a partir do menu esquerdo.
2. Cole a **Chave API** para o projeto Google FCM que guardou anteriormente.
3. Selecione **Guardar**.

   ![Screenshot que mostra como configurar os Centros de Notificação para o Google FCM](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

Quando completar estes passos, um alerta indica que o centro de notificação foi atualizado com sucesso. O botão **Guardar** está desativado.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Você precisará da **Chave API** para o seu projeto de Mensagens Cloud (FCM) do Google Firebase.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

- Este artigo requer a versão 2.0.67 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

### <a name="set-up-push-notifications-for-google-fcm"></a>Configurar notificações push para o Google FCM

1. Utilize o comando [de atualização gcm credencial de az notification-hub](/cli/azure/ext/notification-hub/notification-hub/credential/gcm#ext-notification-hub-az-notification-hub-credential-gcm-update) para adicionar a sua chave Google API ao seu centro de notificação.

   ```azurecli
   az notification-hub credential gcm update --resource-group spnhubrg --namespace-name spnhubns    --notification-hub-name spfcmtutorial1nhub --google-api-key myKey
   ```

2. A Aplicação Android necessita de uma cadeia de ligação para se conectar com o centro de notificação.  Utilize o comando [da lista de regras de autorização do centro de notificação az](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list) para listar as políticas de acesso disponíveis.  Utilize o comando [az notification-hub-regra-regra-chaves de lista](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) para obter as cordas de ligação da política de acesso.  Especifique o **Sistema de Ligação primário** ou o Sistema de **Ligação secundário** No parâmetro para obter `--query` diretamente a cadeia de ligação primária.

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

Para obter mais informações sobre o envio de notificações para uma aplicação Android, consulte [Enviar notificações push para dispositivos Android utilizando o Firebase](notification-hubs-android-push-notification-google-fcm-get-started.md).

---

## <a name="windows-push-notification-service"></a>Serviço de Notificação push do Windows

Para configurar o Serviço de Notificação push do Windows (WNS):

1. No portal Azure, na página **'Centro de Notificação',** selecione **Windows (WNS)** do menu esquerdo.
2. Introduza os valores **para pacote SID** e chave de **segurança**.
3. Selecione **Guardar**.

   ![Screenshot que mostra as caixas de chaves de pacote SID e segurança](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

Para obter informações, consulte [Enviar notificações para aplicações UWP utilizando os Hubs de Notificação Azure](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).

## <a name="microsoft-push-notification-service-for-windows-phone"></a>Serviço de notificação push microsoft para windows phone

Para configurar o Microsoft Push Notification Service (MPNS) para Windows Phone:

1. No portal Azure, na página **'Centro de Notificação',** selecione **Windows Phone (MPNS)** a partir do menu esquerdo.
1. Ativar notificações push não autenticadas ou autenticadas:

   a. Para ativar notificações push não autenticadas, selecione **Ative push**  >  **Save**.

      ![Screenshot que mostra como permitir notificações push não autenticadas](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Para permitir notificações push autenticadas:
      * Na barra de ferramentas, selecione **O Certificado de Upload**.
      * Selecione o ícone do ficheiro e, em seguida, selecione o ficheiro de certificado.
      * Introduza a palavra-passe para o certificado.
      * Selecione **OK**.
      * Na página **Do Windows Phone (MPNS),** selecione **Guardar**.

Para obter mais informações, consulte [notificações push para aplicações do Windows Phone utilizando os Centros de Notificações](notification-hubs-windows-mobile-push-notifications-mpns.md).

## <a name="baidu-android-china"></a>Baidu (Android China)

Para configurar notificações push para Baidu:

1. No portal Azure, na página **Do Centro de Notificações,** selecione **Baidu (Android China)** a partir do menu esquerdo.
2. Introduza a **Chave API** que obteve a partir da consola Baidu no projeto baidu cloud push.
3. Introduza a **Chave Secreta** que obteve a partir da consola Baidu no projeto baidu cloud push.
4. Selecione **Guardar**.

    ![Screenshot de Centros de Notificação que mostra a configuração baidu (Android China) para notificações push](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

Quando completar estes passos, um alerta indica que o centro de notificação foi atualizado com sucesso. O botão **Guardar** está desativado.

Para obter mais informações, consulte [Começar com os Centros de Notificação utilizando o Baidu.](notification-hubs-baidu-china-android-notifications-get-started.md)

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a configurar as definições do sistema de notificações da plataforma para um hub de notificação no portal Azure.

Para saber mais sobre como empurrar notificações para várias plataformas, consulte estes tutoriais:

* [Enviar notificações push para aplicações iOS usando hubs de notificação Azure](ios-sdk-get-started.md)
* [Envie notificações para dispositivos Android utilizando Os Centros de Notificação e o Google FCM](notification-hubs-android-push-notification-google-fcm-get-started.md)
* [Enviar notificações para uma aplicação UWP em execução num dispositivo Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
* [Envie notificações para uma aplicação do Windows Phone 8 utilizando o MPNS](notification-hubs-windows-mobile-push-notifications-mpns.md)
* [Envie notificações utilizando os Centros de Notificação e o push nuvem baidu](notification-hubs-baidu-china-android-notifications-get-started.md)
