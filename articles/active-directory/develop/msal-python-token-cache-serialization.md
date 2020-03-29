---
title: Serialização de cache de símbolo personalizado (MSAL Python) / Azure
titleSuffix: Microsoft identity platform
description: Saiba como serializar a cache simbólica para mSAL para Python
services: active-directory
author: rayluo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/13/2019
ms.author: rayluo
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 2593cc856afb98cf5186c4e33032c5e9151614f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76704396"
---
# <a name="custom-token-cache-serialization-in-msal-for-python"></a>Serialização personalizada de cache em MSAL para Python

Na MSAL Python, uma cache de token na memória que persiste durante a duração da sessão da aplicação, é fornecida por padrão quando cria uma instância de [ClientApplication](https://msal-python.readthedocs.io/en/latest/#confidentialclientapplication).

A serialização da cache simbólica, para que diferentes sessões da sua aplicação possam aceder a ela, não é fornecida "fora da caixa". Isto porque o MSAL Python pode ser usado em tipos de aplicações que não têm acesso ao sistema de ficheiros - como aplicações Web. Para ter uma cache de token persistente numa aplicação MSAL Python, você deve fornecer serialização de cache token personalizada.

As estratégias para serializar a cache simbólica diferem consoante esteja a escrever uma aplicação de cliente público (Desktop), ou uma aplicação confidencial do cliente (Web App, Web API ou app Daemon).

## <a name="token-cache-for-a-public-client-application"></a>Cache token para uma aplicação de cliente público

As aplicações de clientes públicos funcionam no dispositivo de um utilizador e gerem fichas para um único utilizador. Neste caso, pode sondar toda a cache num ficheiro. Lembre-se de fornecer bloqueio de ficheiros se a sua aplicação, ou outra aplicação, puder aceder simultaneamente à cache. Para um simples exemplo de como serializar uma cache simbólica para um ficheiro sem bloqueio, consulte o exemplo na documentação de referência da classe [SerializableTokenCache.](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache)

## <a name="token-cache-for-a-web-app-confidential-client-application"></a>Cache token para uma aplicação Web (aplicação confidencial do cliente)

Para Aplicações Web ou APIs web, pode utilizar a sessão, ou uma cache Redis, ou uma base de dados para armazenar a cache token. Deve haver uma cache simbólica por utilizador (por conta) para garantir que serialize a cache simbólica por conta.

## <a name="next-steps"></a>Passos seguintes

Consulte [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py#L64-L72) para um exemplo de como usar o cache simbólico para uma aplicação Web Windows ou Linux ou Web API. O exemplo é para uma aplicação web que chama a API do Microsoft Graph.
