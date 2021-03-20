---
title: Mostrar matemática no Leitor Imersivo
titleSuffix: Azure Cognitive Services
description: Este artigo irá mostrar-lhe como exibir matemática no Leitor Imersivo.
author: nitinme
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: nitinme
ms.custom: devx-track-js
ms.openlocfilehash: 75b748604e8faa502970ac71e3fec4fae2774c19
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91334519"
---
# <a name="how-to-display-math-in-the-immersive-reader"></a>Como exibir matemática no Leitor Imersivo

O Leitor Imersivo pode apresentar matemática quando fornecido sob a forma de Linguagem de Marcação Matemática[(MathML).](https://developer.mozilla.org/docs/Web/MathML)
O tipo MIME pode ser definido através do [pedaço](../reference.md#chunk)de Leitor Imersivo. Consulte [os tipos de MIME suportados](../reference.md#supported-mime-types) para obter mais informações.

## <a name="send-math-to-the-immersive-reader"></a>Enviar Matemática para o Leitor Imersivo
Para enviar matemática ao Leitor Imersivo, forneça um pedaço contendo MathML e desemalte o tipo MIME ```application/mathml+xml``` para;

Por exemplo, se o seu conteúdo fosse o seguinte:

```html
<div id='ir-content'>
    <math xmlns='http://www.w3.org/1998/Math/MathML'>
        <mfrac>
            <mrow>
                <msup>
                    <mi>x</mi>
                    <mn>2</mn>
                </msup>
                <mo>+</mo>
                <mn>3</mn>
                <mi>x</mi>
                <mo>+</mo>
                <mn>2</mn>
            </mrow>
            <mrow>
                <mi>x</mi>
                <mo>−</mo>
                <mn>3</mn>
            </mrow>
        </mfrac>
        <mo>=</mo>
        <mn>4</mn>
    </math>
</div>
```

Em seguida, pode exibir o seu conteúdo utilizando o seguinte JavaScript.

```javascript
const data = {
    title: 'My Math',
    chunks: [{
        content: document.getElementById('ir-content').innerHTML.trim(),
        mimeType: 'application/mathml+xml'
    }]
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, data, YOUR_OPTIONS);
```

Quando lançar o Leitor Imersivo, deve ver:

![Matemática em Leitor Imersivo](../media/how-tos/1-math.png)

## <a name="next-steps"></a>Passos seguintes

* Explore o [SDK do leitor imersivo](https://github.com/microsoft/immersive-reader-sdk) e a [referência SDK do leitor imersivo](../reference.md)