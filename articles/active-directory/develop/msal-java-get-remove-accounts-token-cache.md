---
title: Get and remove accounts from the token cache using MSAL for Java (MSAL4j)
titleSuffix: Microsoft identity platform
description: Learn how to view and remove accounts from the token cache using the Microsoft Authentication Library for Java.
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
# <a name="get-and-remove-accounts-from-the-token-cache-using-msal-for-java"></a>Get and remove accounts from the token cache using MSAL for Java

MSAL for Java provides an in-memory token cache by default. The in-memory token cache lasts the duration of the application instance.

## <a name="see-which-accounts-are-in-the-cache"></a>See which accounts are in the cache

You can check what accounts are in the cache by calling `PublicClientApplication.getAccounts()` as shown in the following example:

```java
PublicClientApplication pca = new PublicClientApplication.Builder(
                labResponse.getAppId()).
                authority(TestConstants.ORGANIZATIONS_AUTHORITY).
                build();

Set<IAccount> accounts = pca.getAccounts().join();
```

## <a name="remove-accounts-from-the-cache"></a>Remove accounts from the cache

To remove an account from the cache, find the account that needs to be removed and then call `PublicClientApplicatoin.removeAccount()` as shown in the following example:

```java
Set<IAccount> accounts = pca.getAccounts().join();

IAccount accountToBeRemoved = accounts.stream().filter(
                x -> x.username().equalsIgnoreCase(
                        UPN_OF_USER_TO_BE_REMOVED)).findFirst().orElse(null);

pca.removeAccount(accountToBeRemoved).join();
```

## <a name="learn-more"></a>Saber mais

If you are using MSAL for Java, learn about [Custom token cache serialization in MSAL for Java](msal-java-token-cache-serialization.md).
