---
title: Definir política imersiva de cookies de leitor
titleSuffix: Azure Cognitive Services
description: Este artigo irá mostrar-lhe como definir a política de cookies para o Leitor Imersivo.
services: cognitive-services
author: pasta
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: pasta
ms.openlocfilehash: 6de651f1eb51ea1bc941c2af675c8a8d5b0f9cd7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "75946113"
---
# <a name="how-to-set-the-cookie-policy-for-the-immersive-reader"></a>Como definir a política de cookies para o Leitor Imersivo

O Leitor Imersivo irá desativar o uso de cookies por padrão. Se ativar a utilização de cookies, o Leitor Imersivo poderá utilizar cookies para manter as preferências do utilizador e rastrear a utilização da funcionalidade. Se ativar o uso de cookies no Leitor Imersivo, considere os requisitos da Política de Conformidade de Cookies da UE. É da responsabilidade da aplicação anfitriã obter o consentimento necessário do utilizador de acordo com a Política de Conformidade de Cookies da UE.

A política de cookies pode ser definida através das [opções](../reference.md#options)do Leitor Imersivo. Consulte [cookiePolicy enum](../reference.md#cookiepolicy-enum) para mais informações.

## <a name="enable-cookie-usage"></a>Ativar a utilização de cookies

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Enable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="disable-cookie-usage"></a>Desativar o uso de cookies

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Disable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="next-steps"></a>Passos seguintes

* Veja o [Node.js quickstart](../quickstart-nodejs.md) para ver o que mais pode fazer com o SDK de Leitor Imersivo usando Node.js
* Veja o [tutorial](../tutorial-python.md) python para ver o que mais pode fazer com o SDK de Leitor Imersivo usando Python
* Veja o [tutorial Swift](../tutorial-ios-picture-immersive-reader.md) para ver o que mais pode fazer com o SDK de Leitor Imersivo usando Swift
* Explore o [SDK imersivo do leitor](https://github.com/microsoft/immersive-reader-sdk) e a [referência SDK do leitor imersivo](../reference.md)