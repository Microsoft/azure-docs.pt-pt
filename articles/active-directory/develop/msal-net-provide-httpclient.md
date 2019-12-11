---
title: Fornecer um proxy de & HttpClient (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Saiba como fornecer seu próprio HttpClient e proxy para se conectar ao Azure AD usando a biblioteca de autenticação da Microsoft para .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/23/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 80822c2a745d7c9c5b3f56b7921fcc83c5c807d1
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74963244"
---
# <a name="providing-your-own-httpclient-and-proxy-using-msalnet"></a>Fornecendo seu próprio HttpClient e proxy usando o MSAL.NET
Ao [inicializar um aplicativo cliente público](msal-net-initializing-client-applications.md), você pode usar o `.WithHttpClientFactory method` para fornecer seu próprio HttpClient.  Fornecer seu próprio HttpClient permite cenários avançados, como o controle refinado de um proxy HTTP, a personalização de cabeçalhos de agente do usuário ou a força do MSAL a usar um HttpClient específico (por exemplo, em ASP.NET Core aplicativos Web/APIs).

## <a name="initialize-with-httpclientfactory"></a>Inicializar com HttpClientFactory
O exemplo a seguir mostra como criar um `HttpClientFactory` e, em seguida, inicializar um aplicativo cliente público com ele:

```csharp
IMsalHttpClientFactory httpClientFactory = new MyHttpClientFactory();

var pca = PublicClientApplicationBuilder.Create(MsalTestConstants.ClientId) 
                                        .WithHttpClientFactory(httpClientFactory)
                                        .Build();
```

## <a name="httpclient-and-xamarin-ios"></a>HttpClient e Xamarin iOS
Ao usar o Xamarin iOS, é recomendável criar um `HttpClient` que usa explicitamente o manipulador baseado em `NSURLSession`para iOS 7 e mais recente. O MSAL.NET cria automaticamente um `HttpClient` que usa `NSURLSessionHandler` para iOS 7 e mais recente. Para obter mais informações, leia a [documentação do Xamarin Ios para HttpClient](/xamarin/cross-platform/macios/http-stack).