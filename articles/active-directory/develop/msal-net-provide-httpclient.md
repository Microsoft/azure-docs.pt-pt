---
title: Fornecer um proxy httpClient & (MSAL.NET) Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba se fornece o seu próprio HttpClient e procuração para ligar ao AD Azure utilizando a Microsoft Authentication Library for .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/23/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 6099bfe339ae398e4683f17a4397656a4507b6ef
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2020
ms.locfileid: "88166013"
---
# <a name="providing-your-own-httpclient-and-proxy-using-msalnet"></a>Fornecendo o seu próprio HttpClient e proxy usando MSAL.NET
Ao [rubricar uma aplicação de cliente público,](msal-net-initializing-client-applications.md)pode utilizar `.WithHttpClientFactory method` o para fornecer o seu próprio HttpClient.  Fornecer o seu próprio HttpClient permite cenários avançados como o controlo de um proxy HTTP, personalizando cabeçalhos de agente de utilizador, ou forçando a MSAL a usar um HttpClient específico (por exemplo, em ASP.NET core web apps/APIs).

## <a name="initialize-with-httpclientfactory"></a>Inicializar com HttpClientFactory
O exemplo a seguir mostra para criar uma `HttpClientFactory` e, em seguida, inicializar uma aplicação de cliente público com ele:

```csharp
IMsalHttpClientFactory httpClientFactory = new MyHttpClientFactory();

var pca = PublicClientApplicationBuilder.Create(MsalTestConstants.ClientId) 
                                        .WithHttpClientFactory(httpClientFactory)
                                        .Build();
```

## <a name="httpclient-and-xamarin-ios"></a>HttpClient e Xamarin iOS
Ao utilizar o Xamarin iOS, recomenda-se criar um `HttpClient` que utilize explicitamente o `NSURLSession` manipulador baseado para iOS 7 e mais recente. MSAL.NET cria automaticamente um `HttpClient` que utiliza `NSURLSessionHandler` para iOS 7 e mais recente. Para mais informações, leia a documentação do [Xamarin iOS para HttpClient](/xamarin/cross-platform/macios/http-stack).