---
title: Serialização de cache de ficha personalizada (MSAL Python) Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como serializar a cache simbólica para MSAL para Python
services: active-directory
author: rayluo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/13/2019
ms.author: rayluo
ms.reviewer: nacanuma
ms.custom: aaddev, tracking-python
ms.openlocfilehash: 57efe11f0c44d7dfe6d35f2e56d512d6e2e2fa98
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85479288"
---
# <a name="custom-token-cache-serialization-in-msal-for-python"></a>Serialização de cache de ficha personalizada em MSAL para Python

No MSAL Python, uma cache simbólica na memória que persiste durante a sessão da aplicação, é fornecida por padrão quando cria um caso de [Applicação](https://msal-python.readthedocs.io/en/latest/#confidentialclientapplication)do Cliente .

A serialização da cache simbólica, para que diferentes sessões da sua aplicação possam aceder à sua aplicação, não é fornecida "fora da caixa". Isto porque o MSAL Python pode ser usado em tipos de aplicações que não têm acesso ao sistema de ficheiros - como aplicações Web. Para ter uma cache token persistente numa aplicação MSAL Python, você deve fornecer serialização de cache de ficha personalizada.

As estratégias para serializar a cache simbólica diferem consoante esteja a escrever uma aplicação de cliente público (Desktop), ou uma aplicação confidencial para clientes (web app, web API ou daemon app).

## <a name="token-cache-for-a-public-client-application"></a>Cache token para uma aplicação de cliente público

As aplicações de clientes públicos funcionam no dispositivo de um utilizador e gerem fichas para um único utilizador. Neste caso, pode serializar toda a cache num ficheiro. Lembre-se de fornecer bloqueio de ficheiros se a sua aplicação, ou outra app, puder aceder ao cache simultaneamente. Para um simples exemplo de como serializar uma cache simbólica para um ficheiro sem bloquear, consulte o exemplo na documentação de referência da classe [SerializableTokenCache.](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache)

## <a name="token-cache-for-a-web-app-confidential-client-application"></a>Cache token para uma aplicação Web (aplicação confidencial do cliente)

Para aplicações web ou APIs web, você pode usar a sessão, ou uma cache Redis, ou uma base de dados para armazenar a cache simbólica. Deve haver uma cache simbólica por utilizador (por conta) para garantir que serialize a cache simbólica por conta.

## <a name="next-steps"></a>Próximos passos

Consulte [o ms-identidade-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py#L64-L72) para um exemplo de como usar a cache simbólica para uma aplicação Web Windows ou Linux ou web API. O exemplo é para uma aplicação web que chama a API do Microsoft Graph.
