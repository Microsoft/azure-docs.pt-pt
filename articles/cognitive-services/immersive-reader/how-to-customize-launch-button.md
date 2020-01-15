---
title: Personalizar o botão leitor de imersão
titleSuffix: Azure Cognitive Services
description: Este artigo mostrará como personalizar o botão que inicia o leitor de imersão.
services: cognitive-services
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: 6461cc60a237cf834226b07db5e1ea6fbbea7a92
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75946212"
---
# <a name="how-to-customize-the-immersive-reader-button"></a>Como personalizar o botão do leitor de imersão

Este artigo demonstra como personalizar o botão que inicia o leitor de imersão para atender às necessidades do seu aplicativo.

## <a name="add-the-immersive-reader-button"></a>Adicionar o botão leitor de imersão

O SDK do leitor de imersão fornece o estilo padrão para o botão que inicia o leitor de imersão. Use o atributo de classe `immersive-reader-button` para habilitar esse estilo.

```html
<div class='immersive-reader-button'></div>
```

## <a name="customize-the-button-style"></a>Personalizar o estilo do botão

Use o atributo `data-button-style` para definir o estilo do botão. Os valores permitidos são `icon`, `text`e `iconAndText`. O valor predefinido é `icon`.

### <a name="icon-button"></a>Botão de ícone

```html
<div class='immersive-reader-button' data-button-style='icon'></div>
```

Isso renderiza o seguinte:

![Botão de ícone](./media/button-icon.png)

### <a name="text-button"></a>Botão de texto

```html
<div class='immersive-reader-button' data-button-style='text'></div>
```

Isso renderiza o seguinte:

![Botão de ícone](./media/button-text.png)

### <a name="icon-and-text-button"></a>Ícone e botão de texto

```html
<div class='immersive-reader-button' data-button-style='iconAndText'></div>
```

Isso renderiza o seguinte:

![Botão de ícone](./media/button-icon-and-text.png)

## <a name="customize-the-button-text"></a>Personalizar o texto do botão

Configure o idioma e o texto ALT para o botão usando o atributo `data-locale`. O idioma predefinido é o inglês.

```html
<div class='immersive-reader-button' data-locale='fr-FR'></div>
```

## <a name="customize-the-size-of-the-icon"></a>Personalizar o tamanho do ícone

O tamanho do ícone do leitor de imersão pode ser configurado usando o atributo `data-icon-px-size`. Isso define o tamanho do ícone em pixels. O tamanho padrão é 20px.

```html
<div class='immersive-reader-button' data-icon-px-size='50'></div>
```

## <a name="next-steps"></a>Passos seguintes

* Explore a [referência do SDK do leitor de imersão](./reference.md)