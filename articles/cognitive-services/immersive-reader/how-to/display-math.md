---
title: Mostrar matemática no Leitor Imersivo
titleSuffix: Azure Cognitive Services
description: Este artigo irá mostrar-lhe como exibir matemática no Leitor Imersivo.
author: pasta
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: pasta
ms.openlocfilehash: e01cc9e8cedb6c38da0b56e04419c706d5d0566e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946124"
---
# <a name="how-to-display-math-in-the-immersive-reader"></a>Como mostrar matemática no Leitor Imersivo

O Leitor Imersivo pode apresentar matemática quando fornecida sob a forma de Linguagem De Marcação Matemática[(MathML).](https://developer.mozilla.org/docs/Web/MathML)
O tipo MIME pode ser definido através do [pedaço](../reference.md#chunk)de leitor imersivo . Consulte [os tipos mime suportados](../reference.md#supported-mime-types) para obter mais informações.

## <a name="send-math-to-the-immersive-reader"></a>Enviar Matemática ao Leitor Imersivo
Para enviar matemática ao Leitor Imersivo, forneça um pedaço que ```application/mathml+xml```contenha MathML e coloque o tipo MIME para;

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

* Explore o [SDK imersivo do leitor](https://github.com/microsoft/immersive-reader-sdk) e a [referência SDK do leitor imersivo](../reference.md)