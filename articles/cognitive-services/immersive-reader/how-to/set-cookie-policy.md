---
title: Definir Política de Cookies de Leitor Imersivo
titleSuffix: Azure Cognitive Services
description: Este artigo irá mostrar-lhe como definir a política de cookies para o Leitor Imersivo.
services: cognitive-services
author: nitinme
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: nitinme
ms.custom: devx-track-js
ms.openlocfilehash: 02901e6b4a75257b2cda8dee84dbe3438dc15c18
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91332377"
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
* Veja o [tutorial do Android](../tutorial-android.md) para ver o que mais pode fazer com o SDK do leitor imersivo usando Java ou Kotlin para Android
* Veja o tutorial do [iOS](../tutorial-ios.md) para ver o que mais pode fazer com o SDK do leitor imersivo usando Swift para iOS
* Veja o [tutorial python](../tutorial-python.md) para ver o que mais pode fazer com o SDK do leitor imersivo usando Python
* Explore o [SDK do leitor imersivo](https://github.com/microsoft/immersive-reader-sdk) e a [referência SDK do leitor imersivo](../reference.md)