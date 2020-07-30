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
ms.custom: devx-track-javascript
ms.openlocfilehash: fbe54b3bd6e0fc8c42b4dfc7401be74ae65ce6cd
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87406933"
---
# <a name="how-to-launch-the-immersive-reader-with-html-content"></a>Como lançar o Leitor Imersivo com conteúdo HTML

Este artigo demonstra como lançar o Leitor Imersivo com conteúdo HTML.

## <a name="prepare-the-html-content"></a>Preparar o conteúdo HTML

Coloque o conteúdo que pretende renderizar no Leitor Imersivo dentro de um elemento de recipiente. Certifique-se de que o elemento do recipiente tem um elemento `id` único. O Leitor Imersivo fornece suporte para elementos HTML básicos, consulte a [referência](./reference.md#html-support) para obter mais informações.

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

## <a name="get-the-html-content-in-javascript"></a>Obtenha o conteúdo HTML em JavaScript

Utilize o `id` elemento do recipiente para obter o conteúdo HTML no seu código JavaScript.

```javascript
const htmlContent = document.getElementById('immersive-reader-content').innerHTML;
```

## <a name="launch-the-immersive-reader-with-your-html-content"></a>Lance o Leitor Imersivo com o seu conteúdo HTML

Ao `ImmersiveReader.launchAsync` ligar, desa estale a propriedade do pedaço `mimeType` `text/html` para permitir a renderização html.

```javascript
const data = {
    chunks: [{
        content: htmlContent,
        mimeType: 'text/html'
    }]
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, data, YOUR_OPTIONS);
```

## <a name="next-steps"></a>Próximos passos

* Explore a [referência SDK do leitor imersivo](./reference.md)