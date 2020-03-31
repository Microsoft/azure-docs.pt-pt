---
title: Fornecer um proxy httpClient & (MSAL.NET) [ Azure
titleSuffix: Microsoft identity platform
description: Saiba fornecer o seu próprio HttpClient e proxy para ligar ao Azure AD utilizando a Microsoft Authentication Library para .NET (MSAL.NET).
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
ms.openlocfilehash: dbf08e23b2bc1f657363f69df55763437e6c8a90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76695058"
---
# <a name="providing-your-own-httpclient-and-proxy-using-msalnet"></a>Fornecendo o seu próprio HttpClient e procuração usando MSAL.NET
Ao [inicializar uma aplicação de cliente público,](msal-net-initializing-client-applications.md)pode utilizar o para fornecer o `.WithHttpClientFactory method` seu próprio HttpClient.  Fornecer o seu próprio HttpClient permite cenários avançados como o controlo de grãos finos de um proxy HTTP, personalizando os cabeçalhos do agente de utilizador, ou forçando a MSAL a usar um HttpClient específico (por exemplo, em ASP.NET aplicações web/APIs do Core).

## <a name="initialize-with-httpclientfactory"></a>Inicializar com httpClientFactory
O exemplo seguinte mostra `HttpClientFactory` criar uma e, em seguida, inicializar uma aplicação de cliente público com ele:

```csharp
IMsalHttpClientFactory httpClientFactory = new MyHttpClientFactory();

var pca = PublicClientApplicationBuilder.Create(MsalTestConstants.ClientId) 
                                        .WithHttpClientFactory(httpClientFactory)
                                        .Build();
```

## <a name="httpclient-and-xamarin-ios"></a>HttpClient e Xamarin iOS
Ao utilizar o xamarin iOS, `HttpClient` recomenda-se criar `NSURLSession`um que utilize explicitamente o manipulador baseado no iOS 7 e mais recente. MSAL.NET cria automaticamente `HttpClient` um `NSURLSessionHandler` que utiliza para iOS 7 e mais recente. Para mais informações, leia a [documentação do Xamarin iOS para httpClient](/xamarin/cross-platform/macios/http-stack).