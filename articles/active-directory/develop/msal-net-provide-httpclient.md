---
title: Fornecer um HttpClient e um proxy (MSAL.NET)
titleSuffix: Microsoft identity platform
description: Saiba como fornecer seu próprio HttpClient e proxy para se conectar ao Azure AD usando a biblioteca de autenticação da Microsoft para .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/23/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 259c796cb3653ebdd330f5e65db76cc29c181467
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802773"
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