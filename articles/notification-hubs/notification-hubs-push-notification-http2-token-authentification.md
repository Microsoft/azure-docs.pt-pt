---
title: Autenticação baseada em tokens (HTTP/2) para o APNS nos Hubs de notificação do Azure | Documentos da Microsoft
description: Este tópico explica como tirar partido da autenticação de token novo para o APNS
services: notification-hubs
documentationcenter: .net
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 02/13/2019
ms.author: jowargo
ms.openlocfilehash: 890577c013a96fc06acf3b05881649ad8202a083
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56671646"
---
# <a name="token-based-http2-authentication-for-apns"></a>Autenticação baseada em tokens (HTTP/2) para o APNS

## <a name="overview"></a>Descrição geral

Este artigo fornece detalhes sobre como utilizar o novo protocolo HTTP/2 APNS com autenticação baseada em token.

As principais vantagens de utilizar o novo protocolo incluem:

* Geração de tokens é relativamente livre de complicações (em comparação comparada certificados)
* Não existem mais datas de expiração – são no controlo dos seus tokens de autenticação e a sua revogação
* Payloads podem agora ser até 4 KB
* Comentários síncrono
* Está no protocolo de mais recente da Apple – certificados continuar a utilizar o protocolo binário, que é marcado para descontinuação

Usando esse novo mecanismo pode ser feito em duas etapas dentro de alguns minutos:

1. Obter as informações necessárias a partir do portal da conta de programador da Apple
2. Configurar o notification hub com novas informações

Os Hubs de notificação está agora pronto para utilizar o novo sistema de autenticação com APNS.

Tenha em atenção que se tiver migrado com as credenciais de certificado de APNS:

* as propriedades do token substituir o certificado no nosso sistema,
* mas a sua aplicação continua a receber notificações de forma totalmente integrada.

## <a name="obtaining-authentication-information-from-apple"></a>Obter informações de autenticação da Apple

Para ativar a autenticação baseada em tokens, tem as seguintes propriedades da sua conta de programador da Apple:

### <a name="key-identifier"></a>Identificador de Chave

O identificador de chave pode ser obtido a partir da página de "Chaves" na sua conta de programador da Apple

![](./media/notification-hubs-push-notification-http2-token-authentification/obtaining-auth-information-from-apple.png)

### <a name="application-identifier--application-name"></a>Identificador da aplicação & nome da aplicação

O nome da aplicação está disponível através da página de IDs de aplicações na conta de desenvolvedor.

![](./media/notification-hubs-push-notification-http2-token-authentification/app-name.png)

O identificador da aplicação está disponível através da página de detalhes de associação na conta de desenvolvedor.

![](./media/notification-hubs-push-notification-http2-token-authentification/app-id.png)

### <a name="authentication-token"></a>Token de autenticação

O token de autenticação pode ser baixado depois de gerar um token para a sua aplicação. Para obter detalhes sobre como gerar este token, consulte [documentação de programador da Apple](https://help.apple.com/xcode/mac/current/#/devdfd3d04a1).

## <a name="configuring-your-notification-hub-to-use-token-based-authentication"></a>Configurar o notification hub para utilizar a autenticação baseada em tokens

### <a name="configure-via-the-azure-portal"></a>Configurar através do portal do Azure

Para ativar a autenticação baseada em token no portal, inicie sessão no portal do Azure e aceda ao seu Hub de notificação > Serviços de notificação > Painel de APNS.

Há uma nova propriedade – *modo de autenticação*. Selecionar Token permite-lhe atualizar o seu hub com todas as propriedades de token relevantes.

![](./media/notification-hubs-push-notification-http2-token-authentification/azure-portal-apns-settings.png)

* Introduza as propriedades que obteve da sua conta de programador da Apple
* Escolha o modo de aplicativo (de produção ou de Sandbox)
* Clique nas **guardar** botão para atualizar as suas credenciais do APNS

### <a name="configure-via-management-api-rest"></a>Configurar através da gestão de API (REST)

Pode utilizar o nosso [APIs de gestão](https://msdn.microsoft.com/library/azure/dn495827.aspx) para atualizar o seu hub de notificação para utilizar a autenticação baseada em tokens.
Dependendo da aplicação que estiver a configurar a é uma aplicação de Sandbox ou produção (especificada na sua conta de programador Apple), utilize um dos pontos de extremidade correspondentes:

* Ponto final de sandbox: [https://api.development.push.apple.com:443/3/device](https://api.development.push.apple.com:443/3/device)
* Ponto final de produção: [https://api.push.apple.com:443/3/device](https://api.push.apple.com:443/3/device)

> [!IMPORTANT]
> Autenticação baseada em tokens requer uma versão de API do: **04 de 2017 ou posterior**.

Eis um exemplo de um pedido PUT para atualizar um hub com a autenticação baseada em tokens:

    ```text
    PUT https://{namespace}.servicebus.windows.net/{Notification Hub}?api-version=2017-04
      "Properties": {
        "ApnsCredential": {
          "Properties": {
            "KeyId": "<Your Key Id>",
            "Token": "<Your Authentication Token>",
            "AppName": "<Your Application Name>",
            "AppId": "<Your Application Id>",
            "Endpoint":"<Sandbox/Production Endpoint>"
          }
        }
      }
    ```

### <a name="configure-via-the-net-sdk"></a>Configurar através do SDK de .NET

Pode configurar o hub para usar o token de autenticação com base em nossa [SDK de cliente mais recente](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/1.0.8).

Eis um exemplo de código que ilustram o uso correto:

```csharp
NamespaceManager nm = NamespaceManager.CreateFromConnectionString(_endpoint);
string token = "YOUR TOKEN HERE";
string keyId = "YOUR KEY ID HERE";
string appName = "YOUR APP NAME HERE";
string appId = "YOUR APP ID HERE";
NotificationHubDescription desc = new NotificationHubDescription("PATH TO YOUR HUB");
desc.ApnsCredential = new ApnsCredential(token, keyId, appId, appName);
desc.ApnsCredential.Endpoint = @"https://api.development.push.apple.com:443/3/device";
nm.UpdateNotificationHubAsync(desc);
```

## <a name="reverting-to-using-certificate-based-authentication"></a>Reverter para utilizar a autenticação baseada em certificado

Pode reverter a qualquer momento para utilizar a autenticação baseada em certificado usando qualquer método anterior e passando o certificado em vez das propriedades do token. Essa ação substitui as credenciais armazenadas anteriormente.
