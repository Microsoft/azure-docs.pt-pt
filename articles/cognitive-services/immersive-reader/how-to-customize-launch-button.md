---
title: Personalize o botão Leitor Imersivo
titleSuffix: Azure Cognitive Services
description: Este artigo irá mostrar-lhe como personalizar o botão que lança o Leitor Imersivo.
services: cognitive-services
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: 6461cc60a237cf834226b07db5e1ea6fbbea7a92
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "75946212"
---
# <a name="how-to-customize-the-immersive-reader-button"></a>Como personalizar o botão Leitor Imersivo

Este artigo demonstra como personalizar o botão que lança o Leitor Imersivo para se adaptar às necessidades da sua aplicação.

## <a name="add-the-immersive-reader-button"></a>Adicione o botão Leitor Imersivo

O SDK do Leitor Imersivo fornece um estilo predefinido para o botão que lança o Leitor Imersivo. Utilize `immersive-reader-button` o atributo de classe para ativar este estilo.

```html
<div class='immersive-reader-button'></div>
```

## <a name="customize-the-button-style"></a>Personalize o estilo do botão

Utilize `data-button-style` o atributo para definir o estilo do botão. Os valores `icon` `text`permitidos `iconAndText`são, e . O valor predefinido é `icon`.

### <a name="icon-button"></a>Botão ícone

```html
<div class='immersive-reader-button' data-button-style='icon'></div>
```

Isto torna o seguinte:

![Botão ícone](./media/button-icon.png)

### <a name="text-button"></a>Botão de texto

```html
<div class='immersive-reader-button' data-button-style='text'></div>
```

Isto torna o seguinte:

![Botão ícone](./media/button-text.png)

### <a name="icon-and-text-button"></a>Ícone e botão de texto

```html
<div class='immersive-reader-button' data-button-style='iconAndText'></div>
```

Isto torna o seguinte:

![Botão ícone](./media/button-icon-and-text.png)

## <a name="customize-the-button-text"></a>Personalize o texto do botão

Configure o idioma e o texto `data-locale` alt para o botão utilizando o atributo. O idioma predefinido é o inglês.

```html
<div class='immersive-reader-button' data-locale='fr-FR'></div>
```

## <a name="customize-the-size-of-the-icon"></a>Personalize o tamanho do ícone

O tamanho do ícone do Leitor Imersivo pode ser configurado usando o `data-icon-px-size` atributo. Isto define o tamanho do ícone em pixels. O tamanho padrão é de 20px.

```html
<div class='immersive-reader-button' data-icon-px-size='50'></div>
```

## <a name="next-steps"></a>Passos seguintes

* Explore a [referência SDK do leitor imersivo](./reference.md)