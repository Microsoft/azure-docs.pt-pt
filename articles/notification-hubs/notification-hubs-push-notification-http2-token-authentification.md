---
title: Autenticação baseada em token (HTTP/2) para APNS nos hubs de notificação do Azure | Microsoft Docs
description: Saiba como usar a nova autenticação de token para APNS.
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
ms.date: 11/14/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 02/13/2019
ms.openlocfilehash: 448b5c38371024c2eae900f4f87b343ee0a3b36a
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76263817"
---
# <a name="token-based-http2-authentication-for-apns"></a>Autenticação baseada em token (HTTP/2) para APNS

## <a name="overview"></a>Visão geral

Este artigo explica como usar o novo protocolo HTTP/2 do APNS com autenticação baseada em token.

Os principais benefícios do uso do novo protocolo incluem:

* A geração de tokens é relativamente simples (em comparação com os certificados)
* Não há mais datas de expiração – você está no controle de seus tokens de autenticação e sua revogação
* Agora, as cargas podem ser de até 4 KB
* Comentários síncronos
* Você está no protocolo mais recente da Apple – os certificados ainda usam o protocolo binário, que é marcado para substituição

O uso desse novo mecanismo pode ser executado em duas etapas:

* Obtenha as informações necessárias no portal da conta de desenvolvedor da Apple.
* Configure seu hub de notificação com as novas informações.

Os hubs de notificação agora estão definidos para usar o novo sistema de autenticação com o APNS.

Observe que, se você migrar do usando credenciais de certificado para o APNS, as propriedades do token substituirão seu certificado em nosso sistema, mas o aplicativo continuará a receber notificações diretamente.

## <a name="obtaining-authentication-information-from-apple"></a>Obtendo informações de autenticação da Apple

Para habilitar a autenticação baseada em token, você precisa das seguintes propriedades de sua conta de desenvolvedor da Apple:

### <a name="key-identifier"></a>Identificador de chave

O identificador de chave pode ser obtido na página **chaves** em **certificados, identificadores & perfis**, em sua conta de desenvolvedor da Apple:

![](./media/notification-hubs-push-notification-http2-token-authentification/keys.png)

![](./media/notification-hubs-push-notification-http2-token-authentification/obtaining-auth-information-from-apple.png)

### <a name="application-identifier-and-application-name"></a>Identificador do aplicativo e nome do aplicativo

O nome do aplicativo e o identificador também estão disponíveis na página **certificados, identificadores & perfis** na conta de desenvolvedor:

![](./media/notification-hubs-push-notification-http2-token-authentification/app-name.png)

### <a name="configure-via-the-net-sdk-or-the-azure-portal"></a>Configurar por meio do SDK do .NET ou do portal do Azure

Você pode configurar seu hub para usar a autenticação baseada em token usando nosso [SDK do cliente mais recente](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs)ou no portal do Azure. Para habilitar a autenticação baseada em token no portal, entre no portal do Azure e vá para as configurações do hub de notificação **> painel da Apple (APNS)** . Selecione o **token** da propriedade **modo de autenticação** para atualizar o Hub com todas as propriedades de token relevantes.

![Configurar token](./media/notification-hubs-push-notification-http2-token-authentification/azure-portal-apns-settings.png)

* Insira as propriedades que você recuperou de sua conta de desenvolvedor da Apple.
* Escolha o modo de aplicativo (**produção** ou **área restrita**).
* Clique no botão **salvar** para atualizar suas credenciais de APNS.

As credenciais baseadas em token são compostas dos seguintes campos:

* **ID da chave**: identificador da chave privada gerada no portal do desenvolvedor da Apple; por exemplo, `2USFGKSKLT`.
* **ID da equipe**: também chamado de "prefixo" ou "prefixo do aplicativo". Este é o identificador para a organização no portal do desenvolvedor da Apple; por exemplo, `S4V3D7CHJR`.
* **ID do pacote**: também chamado de "ID do aplicativo". Este é o identificador do pacote para o aplicativo; por exemplo, `com.microsoft.nhubsample2019`. Observe que você pode usar uma chave para muitos aplicativos. Esse valor é mapeado para o cabeçalho HTTP `apns-topic` ao enviar uma notificação e é usado para direcionar o aplicativo específico.
* **Token**: também chamado de "chave" ou "chave privada". Isso é obtido do arquivo. P8 gerado no portal do desenvolvedor da Apple. A chave deve ter o APNS habilitado (que está selecionado no portal do desenvolvedor da Apple ao gerar a chave). O valor deve ter o cabeçalho/rodapé PEM extraído dele ao fornecê-lo ao portal/API do NH.
* **Ponto de extremidade**: é uma alternância na folha do portal de hubs de notificação e um campo de cadeia de caracteres na API. Os valores válidos são `https://api.push.apple.com` ou `https://api.sandbox.push.apple.com`: Os hubs de notificação usam esse valor para o ambiente de produção ou área restrita, para enviar notificações. Isso deve corresponder ao direito de `aps-environment` no aplicativo, caso contrário, os tokens de dispositivo APNS gerados não corresponderão ao ambiente e as notificações falharão ao enviar.

Aqui está um exemplo de código que ilustra o uso correto:

```csharp
NamespaceManager nm = NamespaceManager.CreateFromConnectionString(_endpoint);
string token = "YOUR PRIVATE KEY HERE";
string keyId = "YOUR KEY ID HERE";
string appName = "YOUR APP NAME HERE";
string appId = "YOUR APP ID HERE";
NotificationHubDescription desc = new NotificationHubDescription("PATH TO YOUR HUB");
desc.ApnsCredential = new ApnsCredential(token, keyId, appId, appName);
desc.ApnsCredential.Endpoint = @"https://api.development.push.apple.com:443/3/device";
nm.UpdateNotificationHubAsync(desc);
```

## <a name="next-steps"></a>Passos seguintes

* [Criar um hub de notificação do Azure no portal do Azure](create-notification-hub-portal.md)
* [Configurar um hub de notificação no portal do Azure](create-notification-hub-portal.md)
