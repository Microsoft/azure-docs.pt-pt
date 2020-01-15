---
title: Definir política de cookie de leitor de imersão
titleSuffix: Azure Cognitive Services
description: Este artigo mostrará como definir a política de cookie para o leitor de imersão.
services: cognitive-services
author: pasta
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: pasta
ms.openlocfilehash: 6de651f1eb51ea1bc941c2af675c8a8d5b0f9cd7
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75946113"
---
# <a name="how-to-set-the-cookie-policy-for-the-immersive-reader"></a>Como definir a política de cookie para o leitor de imersão

O leitor de imersão desabilitará o uso do cookie por padrão. Se você habilitar o uso do cookie, o leitor de imersão poderá usar cookies para manter as preferências do usuário e controlar o uso do recurso. Se você habilitar o uso de cookies no leitor de imersão, considere os requisitos da política de conformidade do cookie da UE. É responsabilidade do aplicativo host obter qualquer consentimento do usuário necessário de acordo com a política de conformidade do cookie da UE.

A política de cookie pode ser definida por meio das [Opções](../reference.md#options)de leitura imersiva. Consulte [CookiePolicy enum](../reference.md#cookiepolicy-enum) para obter mais informações.

## <a name="enable-cookie-usage"></a>Habilitar uso de cookie

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Enable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="disable-cookie-usage"></a>Desabilitar o uso do cookie

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Disable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="next-steps"></a>Passos seguintes

* Veja o guia de [início rápido do node. js](../quickstart-nodejs.md) para ver o que mais você pode fazer com o SDK do leitor de imersão usando node. js
* Veja o [tutorial do Python](../tutorial-python.md) para ver o que mais você pode fazer com o SDK do leitor de imersão usando o Python
* Veja o [tutorial do Swift](../tutorial-ios-picture-immersive-reader.md) para ver o que mais você pode fazer com o SDK do leitor de imersão usando o Swift
* Explore o [SDK do leitor de imersão](https://github.com/microsoft/immersive-reader-sdk) e a [referência do SDK do leitor de imersão](../reference.md)