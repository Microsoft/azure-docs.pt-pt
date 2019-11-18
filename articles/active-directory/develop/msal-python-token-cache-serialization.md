---
title: Serialização de cache de token personalizada em MSAL para Python | Azure
titleSuffix: Microsoft identity platform
description: Saiba como serializar o cache de token para MSAL para Python
services: active-directory
documentationcenter: dev-center-name
author: rayluo
manager: henrikm
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/13/2019
ms.author: rayluo
ms.reviewer: navyasri.canumalla
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae6628e1fd4929723f11a2195136f94532a4ed76
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152739"
---
# <a name="custom-token-cache-serialization-in-msal-for-python"></a>Serialização de cache de token personalizada em MSAL para Python

No MSAL Python, um cache de token na memória que persiste pela duração da sessão de aplicativo, é fornecido por padrão quando você cria uma instância de [ClientApplication](https://msal-python.readthedocs.io/en/latest/#confidentialclientapplication).

Serialização do cache de token, para que diferentes sessões do seu aplicativo possam acessá-lo, não é fornecido "pronto para uso". Isso ocorre porque o MSAL Python pode ser usado em tipos de aplicativos que não têm acesso ao sistema de arquivos, como aplicativos Web. Para ter um cache de token persistente em um aplicativo Python MSAL, você deve fornecer a serialização de cache de token personalizada.

As estratégias para serialização do cache de tokens diferem dependendo se você está escrevendo um aplicativo cliente público (Desktop) ou um aplicativo cliente confidencial (aplicativo Web, API Web ou aplicativo daemon).

## <a name="token-cache-for-a-public-client-application"></a>Cache de token para um aplicativo cliente público

Os aplicativos cliente públicos são executados no dispositivo de um usuário e gerenciam tokens para um único usuário. Nesse caso, você poderia serializar todo o cache em um arquivo. Lembre-se de fornecer bloqueio de arquivo se seu aplicativo ou outro aplicativo puder acessar o cache simultaneamente. Para obter um exemplo simples de como serializar um cache de token para um arquivo sem bloqueio, consulte o exemplo na documentação de referência da classe [SerializableTokenCache](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) .

## <a name="token-cache-for-a-web-app-confidential-client-application"></a>Cache de token para um aplicativo Web (aplicativo cliente confidencial)

Para aplicativos Web ou APIs Web, você pode usar a sessão ou um cache Redis ou um banco de dados para armazenar o cache de token. Deve haver um cache de token por usuário (por conta), portanto, certifique-se de serializar o cache de token por conta.

## <a name="next-steps"></a>Passos seguintes

Consulte [MS-Identity-Python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py#L64-L72) para obter um exemplo de como usar o cache de token para um aplicativo Web ou uma API Web do Windows ou Linux. O exemplo é para um aplicativo Web que chama a API de Microsoft Graph.
