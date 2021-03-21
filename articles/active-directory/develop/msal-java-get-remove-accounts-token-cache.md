---
title: Obtenha & remova as contas da cache simbólica (MSAL4j) | Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como visualizar e remover contas da cache simbólica utilizando a Biblioteca de Autenticação da Microsoft para Java.
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/07/2019
ms.author: sagonzal
ms.reviewer: navyasri.canumalla
ms.custom: aaddev, devx-track-java
ms.openlocfilehash: fc039e06c8c9d75608b60c2f48e86bc5503e5aec
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96344866"
---
# <a name="get-and-remove-accounts-from-the-token-cache-using-msal-for-java"></a>Obtenha e remova as contas da cache simbólica usando MSAL para Java

MSAL para Java fornece uma cache simbólica na memória por padrão. A cache simbólica na memória dura a duração da aplicação.

## <a name="see-which-accounts-are-in-the-cache"></a>Ver quais as contas que estão na cache

Pode verificar quais as contas que estão na cache, ligando `PublicClientApplication.getAccounts()` como mostrado no seguinte exemplo:

```java
PublicClientApplication pca = new PublicClientApplication.Builder(
                labResponse.getAppId()).
                authority(TestConstants.ORGANIZATIONS_AUTHORITY).
                build();

Set<IAccount> accounts = pca.getAccounts().join();
```

## <a name="remove-accounts-from-the-cache"></a>Remover contas da cache

Para remover uma conta da cache, encontre a conta que precisa de ser removida e, em seguida, ligue `PublicClientApplication.removeAccount()` como mostrado no exemplo seguinte:

```java
Set<IAccount> accounts = pca.getAccounts().join();

IAccount accountToBeRemoved = accounts.stream().filter(
                x -> x.username().equalsIgnoreCase(
                        UPN_OF_USER_TO_BE_REMOVED)).findFirst().orElse(null);

pca.removeAccount(accountToBeRemoved).join();
```

## <a name="learn-more"></a>Saiba mais

Se estiver a utilizar o MSAL para Java, saiba mais sobre [a serialização de cache de ficha personalizada em MSAL para Java.](msal-java-token-cache-serialization.md)
