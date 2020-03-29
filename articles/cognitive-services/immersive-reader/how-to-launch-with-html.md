---
title: Iniciar a Leitura Avançada com conteúdo HTML
titleSuffix: Azure Cognitive Services
description: Este artigo irá mostrar-lhe como lançar o Leitor Imersivo com conteúdo HTML.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: bc7ab46113e1b819fc71a9f6e8a18400f8acfbef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946245"
---
# <a name="how-to-launch-the-immersive-reader-with-html-content"></a>Como lançar o Leitor Imersivo com conteúdo HTML

Este artigo demonstra como lançar o Leitor Imersivo com conteúdo HTML.

## <a name="prepare-the-html-content"></a>Preparar o conteúdo HTML

Coloque o conteúdo que pretende renderizar no Leitor Imersivo dentro de um elemento de recipiente. Certifique-se de que o `id`elemento recipiente tem uma . única . O Leitor Imersivo fornece suporte para elementos HTML básicos, consulte a [referência](./reference.md#html-support) para mais informações.

```html
<div id='immersive-reader-content'>
    <b>Bold</b>
    <i>Italic</i>
    <u>Underline</u>
    <strike>Strikethrough</strike>
    <code>Code</code>
    <sup>Superscript</sup>
    <sub>Subscript</sub>
    <ul><li>Unordered lists</li></ul>
    <ol><li>Ordered lists</li></ol>
</div>
```

## <a name="get-the-html-content-in-javascript"></a>Obtenha o conteúdo HTML no JavaScript

Utilize `id` o elemento do recipiente para obter o conteúdo HTML no seu código JavaScript.

```javascript
const htmlContent = document.getElementById('immersive-reader-content').innerHTML;
```

## <a name="launch-the-immersive-reader-with-your-html-content"></a>Lance o Leitor Imersivo com o seu conteúdo HTML

Ao `ImmersiveReader.launchAsync`ligar, coloque a `mimeType` propriedade `text/html` do pedaço para permitir a renderização HTML.

```javascript
const data = {
    chunks: [{
        content: htmlContent,
        mimeType: 'text/html'
    }]
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, data, YOUR_OPTIONS);
```

## <a name="next-steps"></a>Passos seguintes

* Explore a [referência SDK do leitor imersivo](./reference.md)