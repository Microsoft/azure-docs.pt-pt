---
title: Editar o botão de lançamento do Leitor Imersivo
titleSuffix: Azure Cognitive Services
description: Este artigo irá mostrar-lhe como personalizar o botão que lança o Leitor Imersivo.
services: cognitive-services
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 03/08/2021
ms.author: metang
ms.openlocfilehash: d60e37a437cacda8afbe88a901089f9478a53c16
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102608619"
---
# <a name="how-to-customize-the-immersive-reader-button"></a>Como personalizar o botão Leitor Imersivo

Este artigo demonstra como personalizar o botão que lança o Leitor Imersivo para se adaptar às necessidades da sua aplicação.

## <a name="add-the-immersive-reader-button"></a>Adicione o botão leitor imersivo

O Leitor Imersivo SDK fornece um estilo predefinido para o botão que lança o Leitor Imersivo. Utilize o `immersive-reader-button` atributo de classe para ativar este estilo.

```html
<div class='immersive-reader-button'></div>
```

## <a name="customize-the-button-style"></a>Personalize o estilo do botão

Utilize o `data-button-style` atributo para definir o estilo do botão. Os valores permitidos `icon` `text` são, e `iconAndText` . O valor predefinido é `icon`.

### <a name="icon-button"></a>Botão de ícone

```html
<div class='immersive-reader-button' data-button-style='icon'></div>
```

Isto torna o seguinte:

![Este é o botão de texto renderizado](./media/button-icon.png)

### <a name="text-button"></a>Botão de texto

```html
<div class='immersive-reader-button' data-button-style='text'></div>
```

Isto torna o seguinte:

![Este é o botão de leitor imersivo renderizado.](./media/button-text.png)

### <a name="icon-and-text-button"></a>Ícone e botão de texto

```html
<div class='immersive-reader-button' data-button-style='iconAndText'></div>
```

Isto torna o seguinte:

![Botão de ícone](./media/button-icon-and-text.png)

## <a name="customize-the-button-text"></a>Personalize o texto do botão

Configure o idioma e o texto alt para o botão utilizando o `data-locale` atributo. O idioma predefinido é o inglês.

```html
<div class='immersive-reader-button' data-locale='fr-FR'></div>
```

## <a name="customize-the-size-of-the-icon"></a>Personalize o tamanho do ícone

O tamanho do ícone leitor imersivo pode ser configurado usando o `data-icon-px-size` atributo. Isto define o tamanho do ícone em pixels. O tamanho padrão é de 20px.

```html
<div class='immersive-reader-button' data-icon-px-size='50'></div>
```

## <a name="next-steps"></a>Passos seguintes

* Explore a [referência SDK do leitor imersivo](./reference.md)