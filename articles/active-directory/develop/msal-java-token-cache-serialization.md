---
title: Serialização de cache de token personalizada em MSAL para Java
titleSuffix: Microsoft identity platform
description: Saiba como serializar o cache de token para MSAL para Java
services: active-directory
documentationcenter: dev-center-name
author: sangonzal
manager: henrikm
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/07/2019
ms.author: sagonzal
ms.reviewer: navyasri.canumalla
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7274a1b6b5ebf5b55c2cad4b52dfe4c997e26314
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74145897"
---
# <a name="custom-token-cache-serialization-in-msal-for-java-msal4j"></a>Serialização de cache de token personalizada em MSAL para Java (MSAL4J)

Para persistir o cache de token entre instâncias do seu aplicativo, você precisará personalizar a serialização. As classes e interfaces Java envolvidas na serialização de cache de token são as seguintes:

- [ITokenCache](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCache.html): interface que representa o cache de token de segurança.
- [ITokenCacheAccessAspect](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessAspect.html): interface que representa a operação de execução de código antes e depois do acesso. Você @Override *beforeCacheAccess* e *afterCacheAccess* com a lógica responsável pela serialização e desserialização do cache.
- [ITokenCacheContext](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessContext.html): interface que representa o contexto no qual o cache de token é acessado. 

Veja abaixo uma implementação ingênua da serialização personalizada da serialização/desserialização do cache de token. Não copie e cole isso em um ambiente de produção.

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

Saiba mais sobre como [obter e remover contas do cache de token usando o MSAL para Java](msal-java-get-remove-accounts-token-cache.md).
