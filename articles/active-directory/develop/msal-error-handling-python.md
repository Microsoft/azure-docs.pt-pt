---
title: Lidar com erros e exceções no MSAL para Python
titleSuffix: Microsoft identity platform
description: Saiba como lidar com erros e exceções, o Acesso Condicional reclama desafios e retrações no MSAL para aplicações Python.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2020
ms.author: marsma
ms.reviewer: saeeda, rayluo
ms.custom: aaddev
ms.openlocfilehash: 3046787393d38ed60b54236f33acc065db90321d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98761080"
---
# <a name="handle-errors-and-exceptions-in-msal-for-python"></a>Lidar com erros e exceções no MSAL para Python

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msal-for-python"></a>Manipulação de erros em MSAL para Python

No MSAL para Python, a maioria dos erros são transmitidos como um valor de retorno da chamada API. O erro é representado como um dicionário que contém a resposta JSON da plataforma de identidade da Microsoft.

* Uma resposta bem sucedida contém a `"access_token"` chave. O formato da resposta é definido pelo protocolo OAuth2. Para mais informações, consulte [5.1 Resposta com sucesso](https://tools.ietf.org/html/rfc6749#section-5.1)
* Uma resposta de erro contém `"error"` e normalmente `"error_description"` . O formato da resposta é definido pelo protocolo OAuth2. Para mais informações, consulte [5.2 Error Response](https://tools.ietf.org/html/rfc6749#section-5.2)

Quando um erro é devolvido, a `"error_description"` tecla contém uma mensagem legível pelo homem; que, por sua vez, normalmente contém um código de erro da plataforma de identidade da Microsoft. Para obter mais informações sobre os vários códigos de erro, consulte [códigos de erro de autenticação e autorização](./reference-aadsts-error-codes.md).

No MSAL para Python, as exceções são raras porque a maioria dos erros são tratados devolvendo um valor de erro. A `ValueError` exceção só é lançada quando há um problema com a forma como está a tentar usar a biblioteca, como quando os parâmetros(s) da API estão mal formados.

[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

## <a name="next-steps"></a>Passos seguintes

Considere permitir o [início de Sessão em MSAL para python](msal-logging-python.md) para ajudá-lo a diagnosticar e depurar problemas.
