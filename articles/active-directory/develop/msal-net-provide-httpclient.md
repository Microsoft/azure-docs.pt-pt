---
title: Fornecer um HttpClient e um proxy (MSAL.NET) | Azure
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
ms.openlocfilehash: daae88cd8e76d0ae1af04c45a7191027e9adece9
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68834951"
---
# <a name="providing-your-own-httpclient-and-proxy-using-msalnet"></a>Fornecendo seu próprio HttpClient e proxy usando o MSAL.NET
Ao [inicializar um aplicativo cliente público](msal-net-initializing-client-applications.md), você pode usar o `.WithHttpClientFactory method` para fornecer seu próprio HttpClient.  Fornecer seu próprio HttpClient permite cenários avançados, como o controle refinado de um proxy HTTP, a personalização de cabeçalhos de agente do usuário ou a força do MSAL a usar um HttpClient específico (por exemplo, em ASP.NET Core aplicativos Web/APIs).

## <a name="initialize-with-httpclientfactory"></a>Inicializar com HttpClientFactory
O exemplo a seguir mostra como criar `HttpClientFactory` um e, em seguida, inicializar um aplicativo cliente público com ele:

```csharp
IMsalHttpClientFactory httpClientFactory = new MyHttpClientFactory();

var pca = PublicClientApplicationBuilder.Create(MsalTestConstants.ClientId) 
                                        .WithHttpClientFactory(httpClientFactory)
                                        .Build();
```

## <a name="httpclient-and-xamarin-ios"></a>HttpClient e Xamarin iOS
Ao usar o Xamarin Ios, é recomendável criar um `HttpClient` que usa explicitamente o `NSURLSession`manipulador baseado em para o Ios 7 e mais recente. O MSAL.NET cria automaticamente `HttpClient` um que `NSURLSessionHandler` usa o para IOS 7 e mais recente. Para obter mais informações, leia a [documentação do Xamarin Ios para HttpClient](/xamarin/cross-platform/macios/http-stack).