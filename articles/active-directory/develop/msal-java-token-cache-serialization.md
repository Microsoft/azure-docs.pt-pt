---
title: Serialização de cache de ficha personalizada (MSAL4j)
titleSuffix: Microsoft identity platform
description: Saiba como serializar a cache simbólica para MSAL para Java
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/07/2019
ms.author: sagonzal
ms.reviewer: nacanuma
ms.custom: aaddev, devx-track-java
ms.openlocfilehash: e85cfb8c03111b889a9f95509f337d415c517163
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87312372"
---
# <a name="custom-token-cache-serialization-in-msal-for-java"></a>Serialização de cache de ficha personalizada em MSAL para Java

Para persistir na cache simbólica entre instâncias da sua aplicação, terá de personalizar a serialização. As classes e interfaces de Java envolvidas na serialização da cache simbólica são as seguintes:

- [ITokenCache](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCache.html): Interface que representa cache de ficha de segurança.
- [ITokenCacheAccessAspect](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessAspect.html): Interface que representa a operação de execução do código antes e depois do acesso. Antes @Override *do CecacheAccess* e *depois do AcacheAccess* com a lógica responsável pela serialização e deserização da cache.
- [ITokenCacheContext](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessContext.html): Interface que representa o contexto em que a cache simbólica é acedida. 

Abaixo está uma implementação ingénua da serialização personalizada da serialização/deserialização da cache simbólica. Não copie e cole isto num ambiente de produção.

```Java
static class TokenPersistence implements ITokenCacheAccessAspect {
String data;

TokenPersistence(String data) {
        this.data = data;
}

@Override
public void beforeCacheAccess(ITokenCacheAccessContext iTokenCacheAccessContext) {
        iTokenCacheAccessContext.tokenCache().deserialize(data);
}

@Override
public void afterCacheAccess(ITokenCacheAccessContext iTokenCacheAccessContext) {
        data = iTokenCacheAccessContext.tokenCache().serialize();
}
```

```Java
// Loads cache from file
String dataToInitCache = readResource(this.getClass(), "/cache_data/serialized_cache.json");

ITokenCacheAccessAspect persistenceAspect = new TokenPersistence(dataToInitCache);

// By setting *TokenPersistence* on the PublicClientApplication, MSAL will call *beforeCacheAccess()* before accessing the cache and *afterCacheAccess()* after accessing the cache. 
PublicClientApplication app = 
PublicClientApplication.builder("my_client_id").setTokenCacheAccessAspect(persistenceAspect).build();
```

## <a name="learn-more"></a>Saiba mais

Saiba mais [e remova as contas da cache simbólica usando o MSAL para Java.](msal-java-get-remove-accounts-token-cache.md)
