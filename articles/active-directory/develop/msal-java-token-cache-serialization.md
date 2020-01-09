---
title: Serialização de cache de token personalizado (MSAL4j)
titleSuffix: Microsoft identity platform
description: Saiba como serializar o cache de token para MSAL para Java
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: d80011d3fbf8973ce913ac885a7841fe19760c4b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424309"
---
# <a name="custom-token-cache-serialization-in-msal-for-java"></a>Serialização de cache de token personalizada em MSAL para Java

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

## <a name="learn-more"></a>Saber mais

Saiba mais sobre como [obter e remover contas do cache de token usando o MSAL para Java](msal-java-get-remove-accounts-token-cache.md).
