---
title: Como preparar conteúdo HTML para leitor imersivo
titleSuffix: Azure Cognitive Services
description: Saiba como lançar o leitor imersivo utilizando HTML, JavaScript, Python, Android ou iOS. O Leitor Imersivo usa técnicas comprovadas para melhorar a compreensão da leitura para os alunos de línguas, leitores emergentes e alunos com diferenças de aprendizagem.
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: erhopf
ms.openlocfilehash: 40aab9510eb90c405f92be49a3b4c0a3f756c872
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102620166"
---
# <a name="how-to-prepare-html-content-for-immersive-reader"></a>Como preparar conteúdo HTML para leitor imersivo

Este artigo mostra-lhe como estruturar o seu HTML e recuperar o conteúdo, para que possa ser utilizado pelo Leitor Imersivo.

## <a name="prepare-the-html-content"></a>Preparar o conteúdo HTML

Coloque o conteúdo que pretende renderizar no Leitor Imersivo dentro de um elemento de recipiente. Certifique-se de que o elemento do recipiente tem um elemento `id` único. O Leitor Imersivo fornece suporte para elementos HTML básicos, consulte a [referência](reference.md#html-support) para obter mais informações.

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

## <a name="next-steps"></a>Passos seguintes

* Explore a [referência SDK do leitor imersivo](reference.md)