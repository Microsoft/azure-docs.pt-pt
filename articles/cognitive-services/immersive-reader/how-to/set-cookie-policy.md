---
title: Definir Política de Cookies de Leitor Imersivo
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
ms.openlocfilehash: ae4812a69a1e6833224dc4dedf36566167ae4796
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/27/2020
ms.locfileid: "85483419"
---
# <a name="how-to-set-the-cookie-policy-for-the-immersive-reader"></a>Como definir a política de cookies para o Leitor Imersivo

O Leitor Imersivo irá desativar o uso de cookies por predefinição. Se ativar a utilização de cookies, o Leitor Imersivo poderá utilizar cookies para manter as preferências do utilizador e rastrear o uso da funcionalidade. Se ativar a utilização de cookies no Leitor Imersivo, considere os requisitos da Política de Conformidade com os Cookies da UE. É da responsabilidade da aplicação do anfitrião obter o consentimento necessário do utilizador de acordo com a Política de Conformidade com os Cookies da UE.

A política de cookies pode ser definida através das [opções imersivas](../reference.md#options)do Leitor.

## <a name="enable-cookie-usage"></a>Ativar o uso de cookies

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

* Veja o [Node.js arranque rápido](../quickstarts/client-libraries.md?pivots=programming-language-nodejs) para ver o que mais pode fazer com o SDK do leitor imersivo usando Node.js
* Veja o [tutorial python](../tutorial-python.md) para ver o que mais pode fazer com o SDK do leitor imersivo usando Python
* Veja o [tutorial swift](../tutorial-ios-picture-immersive-reader.md) para ver o que mais pode fazer com o SDK do leitor imersivo usando Swift
* Explore o [SDK do leitor imersivo](https://github.com/microsoft/immersive-reader-sdk) e a [referência SDK do leitor imersivo](../reference.md)