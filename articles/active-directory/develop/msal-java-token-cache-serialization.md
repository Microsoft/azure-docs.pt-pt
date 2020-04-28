---
title: Serialização de cache de token personalizada (MSAL4j)
titleSuffix: Microsoft identity platform
description: Saiba como serializar a cache simbólica para a MSAL para Java
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/07/2019
ms.author: sagonzal
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: bcb34d83365112b97769186ad74dfd762b05c2e8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76696168"
---
# <a name="custom-token-cache-serialization-in-msal-for-java"></a>Serialização personalizada de cache em MSAL para Java

Para persistir a cache simbólica entre as instâncias da sua aplicação, terá de personalizar a serialização. As classes java e interfaces envolvidas na cacheização simbólica são as seguintes:

- [ITokenCache](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCache.html): Interface que representa cache de token de segurança.
- [ITokenCacheAccessAspect](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessAspect.html): Interface que representa o funcionamento do código de execução antes e depois do acesso. Seria *antes do CacheAccess* e depois do *CacheAccess* com a lógica responsável pela serialização e desserialização da cache. @Override
- [ITokenCacheContext](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessContext.html): Interface que representa o contexto em que a cache simbólica é acedida. 

Abaixo está uma implementação ingénua de serialização personalizada de serialização/desserialização de cache token. Não copie e cole isto num ambiente de produção.

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

Saiba mais sobre [Obter e remover contas da cache simbólica utilizando MSAL para Java](msal-java-get-remove-accounts-token-cache.md).
