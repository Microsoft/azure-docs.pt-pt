---
title: Autenticação baseada em token (HTTP/2) para APNS nos hubs de notificação do Azure | Microsoft Docs
description: Este tópico explica como aproveitar a nova autenticação de token para o APNS
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 02/13/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 02/13/2019
ms.openlocfilehash: a7fdaae33e28bd543b44c54868324339d1269bc2
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213118"
---
# <a name="token-based-http2-authentication-for-apns"></a>Autenticação baseada em token (HTTP/2) para APNS

## <a name="overview"></a>Descrição geral

Este artigo fornece detalhes sobre como usar o novo protocolo de HTTP/2 APNS com autenticação baseada em token.

Os principais benefícios do uso do novo protocolo incluem:

* A geração de tokens é relativamente sem complicações (em comparação com os certificados)
* Não há mais datas de expiração – você está no controle de seus tokens de autenticação e sua revogação
* Agora, as cargas podem ser de até 4 KB
* Comentários síncronos
* Você está no protocolo mais recente da Apple – os certificados ainda usam o protocolo binário, que é marcado para substituição

O uso desse novo mecanismo pode ser feito em duas etapas em alguns minutos:

1. Obter as informações necessárias do portal de conta de desenvolvedor da Apple
2. Configurar o Hub de notificação com as novas informações

Os hubs de notificação agora estão todos configurados para usar o novo sistema de autenticação com o APNS.

Observe que, se você migrou do usando credenciais de certificado para APNS:

* as propriedades do token substituem seu certificado em nosso sistema,
* Mas seu aplicativo continua a receber notificações diretamente.

## <a name="obtaining-authentication-information-from-apple"></a>Obtendo informações de autenticação da Apple

Para habilitar a autenticação baseada em token, você precisa das seguintes propriedades de sua conta de desenvolvedor da Apple:

### <a name="key-identifier"></a>Identificador de Chave

O identificador de chave pode ser obtido na página "chaves" em sua conta de desenvolvedor da Apple

![](./media/notification-hubs-push-notification-http2-token-authentification/obtaining-auth-information-from-apple.png)

### <a name="application-identifier--application-name"></a>Identificador do aplicativo & nome do aplicativo

O nome do aplicativo está disponível por meio da página IDs do aplicativo na conta de desenvolvedor.

![](./media/notification-hubs-push-notification-http2-token-authentification/app-name.png)

O identificador de aplicativo está disponível por meio da página de detalhes de associação na conta de desenvolvedor.

![](./media/notification-hubs-push-notification-http2-token-authentification/app-id.png)

### <a name="authentication-token"></a>Token de autenticação

O token de autenticação pode ser baixado depois que você gera um token para seu aplicativo. Para obter detalhes sobre como gerar esse token, consulte a [documentação do desenvolvedor da Apple](https://help.apple.com/xcode/mac/current/#/devdfd3d04a1).

## <a name="configuring-your-notification-hub-to-use-token-based-authentication"></a>Configurando o Hub de notificação para usar a autenticação baseada em token

### <a name="configure-via-the-azure-portal"></a>Configurar por meio do portal do Azure

Para habilitar a autenticação baseada em token no portal, faça logon no portal do Azure e vá para o Hub de notificação > Notification Services > painel APNS.

Há uma nova propriedade – *modo de autenticação*. Selecionar token permite que você atualize seu hub com todas as propriedades de token relevantes.

![](./media/notification-hubs-push-notification-http2-token-authentification/azure-portal-apns-settings.png)

* Insira as propriedades que você recuperou de sua conta de desenvolvedor da Apple
* Escolha o modo do aplicativo (produção ou área restrita)
* Clique no botão **salvar** para atualizar suas credenciais de APNS

### <a name="configure-via-management-api-rest"></a>Configurar via API de gerenciamento (REST)

Você pode usar nossas [APIs de gerenciamento](https://msdn.microsoft.com/library/azure/dn495827.aspx) para atualizar seu hub de notificação para usar a autenticação baseada em token.
Dependendo se o aplicativo que você está configurando for um aplicativo de área restrita ou de produção (especificado em sua conta de desenvolvedor da Apple), use um dos pontos de extremidade correspondentes:

* Ponto de extremidade de área restrita:[https://api.development.push.apple.com:443/3/device](https://api.development.push.apple.com:443/3/device)
* Ponto de extremidade de produção:[https://api.push.apple.com:443/3/device](https://api.push.apple.com:443/3/device)

> [!IMPORTANT]
> A autenticação baseada em token requer uma versão de API de: **2017-04 ou posterior**.

Aqui está um exemplo de uma solicitação PUT para atualizar um hub com autenticação baseada em token:

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

### <a name="configure-via-the-net-sdk"></a>Configurar por meio do SDK do .NET

Você pode configurar seu hub para usar a autenticação baseada em token usando nosso [SDK do cliente mais recente](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/1.0.8).

Aqui está um exemplo de código que ilustra o uso correto:

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

## <a name="reverting-to-using-certificate-based-authentication"></a>Revertendo para usando a autenticação baseada em certificado

Você pode reverter a qualquer momento para usar a autenticação baseada em certificado usando qualquer método anterior e passando o certificado em vez das propriedades do token. Essa ação substitui as credenciais armazenadas anteriormente.
