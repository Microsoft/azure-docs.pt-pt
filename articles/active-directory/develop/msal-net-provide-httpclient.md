---
title: Fornecer um HttpClient e o proxy (MSAL.NET) | Azure
description: Saiba mais sobre como fornecer seus próprios HttpClient e o proxy para ligar ao Azure AD com a biblioteca de autenticação da Microsoft para .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/23/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 234c9d0724021017ec8c411d637420b05284ea52
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544162"
---
# <a name="providing-your-own-httpclient-and-proxy-using-msalnet"></a>Fornecer seu próprio proxy com MSAL.NET e HttpClient
Ao inicializar uma aplicação cliente público, pode usar o `.WithHttpClientFactory method` para fornecer sua própria HttpClient.  Fornecer seu próprio HttpClient permite cenários avançados esse controlo detalhado de um proxy HTTP, personalizando os cabeçalhos de agentes de utilizador, ou forçar a MSAL para utilizar um HttpClient específico (por exemplo, em aplicações de web de ASP.NET Core/APIs).

## <a name="initialize-with-httpclientfactory"></a>Inicializar com HttpClientFactory
O exemplo seguinte mostra para criar um `HttpClientFactory` e, em seguida, iniciar uma aplicação de cliente público com o mesmo:

```csharp
IMsalHttpClientFactory httpClientFactory = new MyHttpClientFactory();

var pca = PublicClientApplicationBuilder.Create(MsalTestConstants.ClientId) 
                                        .WithHttpClientFactory(httpClientFactory)
                                        .Build();
```

## <a name="httpclient-and-xamarin-ios"></a>HttpClient e Xamarin iOS
Ao utilizar o Xamarin iOS, é recomendado que crie uma `HttpClient` que utiliza explicitamente a `NSURLSession`-com base manipulador para o iOS 7 e versões mais recentes. MSAL.NET cria automaticamente uma `HttpClient` que utiliza `NSURLSessionHandler` para iOS 7 e versões mais recentes. Para obter mais informações, leia os [documentação do Xamarin iOS para o HttpClient](/xamarin/cross-platform/macios/http-stack).