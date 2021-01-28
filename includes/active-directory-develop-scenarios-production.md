---
title: incluir ficheiro
description: incluir ficheiro
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 75650d7ff0ac647aeb6dace76c270680b1b89347
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98954969"
---
## <a name="enable-logging"></a>Ativar registo

Para ajudar a depurar e autenticar cenários de resolução de problemas de falha de autenticação, a Microsoft Authentication Library fornece suporte de registo incorporado. O registo madeireira é que cada biblioteca é abrangida pelos seguintes artigos:

:::row:::
    :::column:::
        - [Registos no MSAL.NET](../articles/active-directory/develop/msal-logging-dotnet.md)
        - [Registos no MSAL para Android](../articles/active-directory/develop/msal-logging-android.md)
        - [Registos no MSAL.js](../articles/active-directory/develop/msal-logging-js.md)
    :::column-end:::
    :::column:::
        - [Registos no MSAL para iOS/macOS](../articles/active-directory/develop/msal-logging-ios.md)
        - [Registos no MSAL para Java](../articles/active-directory/develop/msal-logging-java.md)
        - [Registos no MSAL para Python](../articles/active-directory/develop/msal-logging-python.md)
    :::column-end:::
:::row-end:::

Aqui ficam algumas sugestões para recolha de dados:

- Os utilizadores podem pedir ajuda quando têm problemas. Uma boa prática é capturar e armazenar temporariamente registos. Forneça um local onde os utilizadores possam fazer o upload dos registos. A MSAL fornece extensões de registo para capturar informações detalhadas sobre a autenticação.

- Se a telemetria estiver disponível, permita-o através do MSAL recolher dados sobre como os utilizadores se inscrevem na sua aplicação.


## <a name="validate-your-integration"></a>Validar a sua integração

Teste a sua integração seguindo a lista de verificação de [integração da plataforma de identidade da Microsoft.](../articles/active-directory/develop/identity-platform-integration-checklist.md)
