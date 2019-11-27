---
title: Obter e remover contas do cache de token usando MSAL para Java (MSAL4j)
titleSuffix: Microsoft identity platform
description: Saiba como exibir e remover contas do cache de token usando a biblioteca de autenticação da Microsoft para Java.
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
ms.openlocfilehash: fa6c4c53d04f227db7a9a83946182c109dc06d39
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74452471"
---
# <a name="get-and-remove-accounts-from-the-token-cache-using-msal-for-java"></a>Obter e remover contas do cache de token usando MSAL para Java

O MSAL para Java fornece um cache de token na memória por padrão. O cache de token na memória dura a duração da instância do aplicativo.

## <a name="see-which-accounts-are-in-the-cache"></a>Ver quais contas estão no cache

Você pode verificar quais contas estão no cache chamando `PublicClientApplication.getAccounts()`, conforme mostrado no exemplo a seguir:

```java
PublicClientApplication pca = new PublicClientApplication.Builder(
                labResponse.getAppId()).
                authority(TestConstants.ORGANIZATIONS_AUTHORITY).
                build();

Set<IAccount> accounts = pca.getAccounts().join();
```

## <a name="remove-accounts-from-the-cache"></a>Remover contas do cache

Para remover uma conta do cache, localize a conta que precisa ser removida e, em seguida, chame `PublicClientApplicatoin.removeAccount()`, conforme mostrado no exemplo a seguir:

```java
Set<IAccount> accounts = pca.getAccounts().join();

IAccount accountToBeRemoved = accounts.stream().filter(
                x -> x.username().equalsIgnoreCase(
                        UPN_OF_USER_TO_BE_REMOVED)).findFirst().orElse(null);

pca.removeAccount(accountToBeRemoved).join();
```

## <a name="learn-more"></a>Obter mais informações

Se você estiver usando o MSAL para Java, saiba mais sobre a [serialização de cache de token personalizada em MSAL para Java](msal-java-token-cache-serialization.md).
