---
title: Autenticação baseada em token (HTTP/2) para APNS em Azure Notification Hubs / Microsoft Docs
description: Saiba como utilizar a nova autenticação simbólica para a APNS.
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
ms.custom: devx-track-csharp
ms.openlocfilehash: 4f8de6389a04448579672b84e91f0bb4dd0f4ce2
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460461"
---
# <a name="token-based-http2-authentication-for-apns"></a>Autenticação baseada em token (HTTP/2) para APNS

## <a name="overview"></a>Descrição geral

Este artigo explica como utilizar o novo protocolo APNS HTTP/2 com autenticação baseada em fichas.

Os principais benefícios da utilização do novo protocolo incluem:

* A geração token é relativamente simples (em comparação com os certificados)
* Sem mais datas de validade – você está no controlo das suas fichas de autenticação e da sua revogação
* As cargas podem agora chegar a 4 KB
* Feedback sincronizado
* Está no mais recente protocolo da Apple – os certificados ainda usam o protocolo binário, que está marcado para depreciação

A utilização deste novo mecanismo pode ser executada em duas etapas:

* Obtenha as informações necessárias a partir do portal da conta Apple Developer.
* Configure o seu centro de notificação com as novas informações.

Os Centros de Notificação estão agora definidos para utilizar o novo sistema de autenticação com a APNS.

Note que se emigrou de usar credenciais de certificado para APNS, as propriedades simbólicas substituem o seu certificado no nosso sistema, mas a sua aplicação continua a receber notificações sem problemas.

## <a name="obtaining-authentication-information-from-apple"></a>Obtenção de informações de autenticação da Apple

Para ativar a autenticação baseada em fichas, necessita das seguintes propriedades da sua conta Apple Developer:

### <a name="key-identifier"></a>Identificador de chave

O identificador chave pode ser obtido a partir da página **Chaves** em **Certificados, Identificadores & Perfis,** na sua conta Apple Developer:

![Certificados](./media/notification-hubs-push-notification-http2-token-authentification/keys.png)

![Identificadores](./media/notification-hubs-push-notification-http2-token-authentification/obtaining-auth-information-from-apple.png)

### <a name="application-identifier-and-application-name"></a>Identificador de aplicação e nome de aplicação

O nome da aplicação e o identificador também estão disponíveis na página **certificados, identificadores & Perfis** na conta do programador:

![Certificados e IDs](./media/notification-hubs-push-notification-http2-token-authentification/app-name.png)

### <a name="configure-via-the-net-sdk-or-the-azure-portal"></a>Configurar através do .NET SDK ou do portal Azure

Pode configurar o seu hub para utilizar a autenticação baseada em fichas utilizando o nosso [mais recente cliente SDK,](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs)ou no portal Azure. Para ativar a autenticação baseada em fichas no portal, inscreva-se no portal Azure e aceda ao painel De definições do seu centro de **notificações > painel Apple (APNS).** Selecione **Token** a partir da propriedade **Modo de Autenticação** para atualizar o seu hub com todas as propriedades token relevantes.

![Ficha de configuração](./media/notification-hubs-push-notification-http2-token-authentification/azure-portal-apns-settings.png)

* Introduza as propriedades que recuperou da sua conta Apple Developer.
* Escolha o modo de aplicação **(Produção** ou **Caixa de Areia).**
* Clique no botão **Guardar** para atualizar as suas credenciais APNS.

As credenciais baseadas em token são compostas pelos seguintes campos:

* **ID chave**: Identificador da chave privada gerada no portal Apple Developer; por exemplo, `2USFGKSKLT` . .
* **ID da equipa**: Também chamado de "Prefixo" ou "Prefixo de aplicações". Este é o identificador da organização no portal Apple Developer; por exemplo, `S4V3D7CHJR` . .
* **Bundle ID**: Também chamado de "App ID". Este é o identificador do pacote para a aplicação; por exemplo, `com.example.myapp` . . Note que só pode usar uma chave para uma aplicação. Este valor mapeia para o `apns-topic` cabeçalho HTTP ao enviar uma notificação, e é usado para direcionar a aplicação específica. Não se pode definir explicitamente o `apns-topic` valor.
* **Token**: Também chamado de "Chave" ou "Chave Privada". Isto é obtido a partir do ficheiro .p8 gerado no portal Apple Developer. A tecla deve ter APNS ativada (que é selecionada no portal Apple Developer ao gerar a chave). O valor deve ter o cabeçalho/rodapé PEM despojado dele quando o fornece ao Nh Portal/API.
* Ponto final : **Trata-se** de um toggle na lâmina do portal 'Hubs de Notificação' e de um campo de cordas na API. Valores válidos são `https://api.development.push.apple.com:443/3/device` ou `https://api.sandbox.push.apple.com:443/3/device` . Os Centros de Notificação utilizam este valor para o ambiente de produção ou caixa de areia, para o envio de notificações. Isto deve corresponder ao `aps-environment` direito na aplicação, caso contrário os tokens do dispositivo APNS gerados não correspondem ao ambiente, e as notificações não são enviadas.

Aqui está uma amostra de código que ilustra o uso correto:

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
* [Configurar um centro de notificação no portal Azure](create-notification-hub-portal.md)
