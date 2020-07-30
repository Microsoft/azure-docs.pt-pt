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
ms.custom: devx-track-javascript
ms.openlocfilehash: 6bc55416c335f370151e54d2b09ccf00bd799fdb
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87406899"
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

## <a name="next-steps"></a>Próximos passos

* Veja o [Node.js arranque rápido](../quickstarts/client-libraries.md?pivots=programming-language-nodejs) para ver o que mais pode fazer com o SDK do leitor imersivo usando Node.js
* Veja o [tutorial do Android](../tutorial-android.md) para ver o que mais pode fazer com o SDK do leitor imersivo usando Java ou Kotlin para Android
* Veja o tutorial do [iOS](../tutorial-ios.md) para ver o que mais pode fazer com o SDK do leitor imersivo usando Swift para iOS
* Veja o [tutorial python](../tutorial-python.md) para ver o que mais pode fazer com o SDK do leitor imersivo usando Python
* Explore o [SDK do leitor imersivo](https://github.com/microsoft/immersive-reader-sdk) e a [referência SDK do leitor imersivo](../reference.md)