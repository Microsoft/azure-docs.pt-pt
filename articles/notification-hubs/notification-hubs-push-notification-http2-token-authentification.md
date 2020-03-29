---
title: Autenticação baseada em token (HTTP/2) para APNS em Hubs de Notificação Azure [ Microsoft Docs
description: Aprenda a usar a nova autenticação simbólica para APNS.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76263817"
---
# <a name="token-based-http2-authentication-for-apns"></a>Autenticação baseada em token (HTTP/2) para APNS

## <a name="overview"></a>Descrição geral

Este artigo explica como utilizar o novo protocolo APNS HTTP/2 com autenticação baseada em token.

Os principais benefícios da utilização do novo protocolo incluem:

* A geração token é relativamente simples (em comparação com os certificados)
* Não há mais datas de validade – controla os seus tokens de autenticação e a sua revogação
* Cargas já podem chegar a 4 KB
* Feedback sincronizado
* Você está no protocolo mais recente da Apple - os certificados ainda usam o protocolo binário, que está marcado para a depreciação

A utilização deste novo mecanismo pode ser realizada em duas etapas:

* Obtenha as informações necessárias no portal da conta Apple Developer.
* Configure o seu centro de notificação com as novas informações.

O Notification Hubs está agora definido para utilizar o novo sistema de autenticação com APNS.

Note que se tiver migrado da utilização de credenciais de certificado para APNS, as propriedades simbólicas sobreporem o seu certificado no nosso sistema, mas a sua aplicação continua a receber notificações sem problemas.

## <a name="obtaining-authentication-information-from-apple"></a>Obtenção de informações de autenticação da Apple

Para permitir a autenticação baseada em token, precisa das seguintes propriedades da sua conta Apple Developer:

### <a name="key-identifier"></a>Identificador chave

O identificador chave pode ser obtido a partir da página **Keys** em **Certificados, Identificadores & Perfis,** na sua conta Apple Developer:

![](./media/notification-hubs-push-notification-http2-token-authentification/keys.png)

![](./media/notification-hubs-push-notification-http2-token-authentification/obtaining-auth-information-from-apple.png)

### <a name="application-identifier-and-application-name"></a>Identificador de aplicação e nome de candidatura

O nome e identificador da aplicação também estão disponíveis na página **Certificados, Identificadores & Perfis** na conta do desenvolvedor:

![](./media/notification-hubs-push-notification-http2-token-authentification/app-name.png)

### <a name="configure-via-the-net-sdk-or-the-azure-portal"></a>Configure através do .NET SDK ou do portal Azure

Pode configurar o seu hub para utilizar a autenticação baseada em token suster utilizando o nosso [mais recente cliente SDK,](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs)ou no portal Azure. Para permitir a autenticação baseada em tokens no portal, inscreva-se no portal Azure e vá ao painel Definições do seu hub de notificação **> Apple (APNS).** Selecione **Token** da propriedade **do Modo de Autenticação** para atualizar o seu hub com todas as propriedades simbólicas relevantes.

![Ficha de configuração](./media/notification-hubs-push-notification-http2-token-authentification/azure-portal-apns-settings.png)

* Insira as propriedades que recuperou da sua conta Apple Developer.
* Escolha o modo de aplicação **(Produção** ou **Caixa de Areia).**
* Clique no botão **Guardar** para atualizar as suas credenciais APNS.

As credenciais baseadas em token são compostas pelos seguintes campos:

* **ID chave**: Identificador da chave privada gerada no portal Apple Developer; por exemplo, `2USFGKSKLT`.
* **ID da equipa**: Também chamado de "Prefixo" ou "Prefixo de Aplicação". Este é o identificador para a organização no portal Apple Developer; por exemplo, `S4V3D7CHJR`.
* **Id do pacote**: Também chamado de "ID da aplicação". Este é o identificador de pacote para a aplicação; por exemplo, `com.microsoft.nhubsample2019`. Note que pode utilizar uma chave para muitas aplicações. Este valor mapeia para o `apns-topic` cabeçalho HTTP ao enviar uma notificação, e é usado para direcionar a aplicação específica.
* **Token**: Também chamado de "Chave" ou "Chave Privada". Isto é obtido a partir do ficheiro .p8 gerado no portal Apple Developer. A chave deve ter APNS ativados (que é selecionado no portal Apple Developer ao gerar a chave). O valor deve ter o cabeçalho/rodapé PEM retirado dele quando o fornece ao Portal NH/API.
* **Ponto final**: Este é um alternância na lâmina do portal Dos Centros de Notificação e um campo de cordas na API. Valores `https://api.push.apple.com` válidos são ou `https://api.sandbox.push.apple.com`. Os Centros de Notificação utilizam este valor para o ambiente de produção ou caixa de areia, para o envio de notificações. Isto deve `aps-environment` corresponder ao direito na aplicação, caso contrário os tokens do dispositivo APNS gerados não correspondem ao ambiente, e as notificações não são enviadas.

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

* [Criar um hub de notificação Azure no portal Azure](create-notification-hub-portal.md)
* [Configure um centro de notificação no portal Azure](create-notification-hub-portal.md)
