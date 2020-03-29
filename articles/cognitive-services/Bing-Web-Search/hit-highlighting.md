---
title: Como usar marcadores de decoração para destacar texto - Bing Web Search API
titleSuffix: Azure Cognitive Services
description: Saiba como usar decorações de texto e acerte o destaque nos resultados da sua pesquisa utilizando a API bing Web Search.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 5365B568-EA55-4D97-8FBE-0AF60158D4D5
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: scottwhi
ms.openlocfilehash: a6d394fec6e7cf0a230f61ad05c236a1f84dad9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "68854026"
---
# <a name="using-decoration-markers-to-highlight-text"></a>Usando marcadores de decoração para destacar texto

Bing suporta o destaque do hit, que marca termos de consulta (ou outros termos que Bing considera relevantes) nas cadeias de exibição de algumas respostas. Por exemplo, um resultado `name`de `displayUrl`página `snippet` web, e os campos podem conter termos de consulta marcados. 

Por padrão, bing não inclui marcadores de destaque nas cordas de exibição. Para ativar os marcadores, inclua o parâmetro `textDecorations` de `true`consulta no seu pedido e defina-o para .

## <a name="hit-highlighting-example"></a>Exemplo de destaque de sucesso

O exemplo que se `Sailing Dinghy`segue mostra um resultado web para . Bing marcou o início e o fim do período de consulta usando os caracteres E000 e E001 Unicode.
  
![Destaque de sucesso](./media/cognitive-services-bing-web-api/bing-hit-highlighting.png) 

Antes de apresentar o resultado na interface do utilizador, substitua os caracteres Unicode por aqueles adequados para o formato de exibição.

## <a name="marker-formatting"></a>Formatação de marcador

Bing fornece a opção de usar caracteres Unicode ou tags HTML como marcadores. Para especificar quais os marcadores a utilizar, inclua o parâmetro de consulta [textFormat:](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#textformat) 

| Valor             | Marcador                       |
|-------------------|------------------------------|
| `textFormat=Raw`  | Caracteres unicódigo (padrão) |
| `textFormat=HTML` | Caracteres HTML              |

## <a name="additional-text-decorations"></a>Decorações de texto adicionais

Bing pode devolver várias decorações de texto diferentes. Por exemplo, `Computation` uma resposta pode conter marcadores `log(2)` subscriptpara o termo de consulta no `expression` campo.

![marcadores de cálculo](./media/cognitive-services-bing-web-api/bing-markers-computation.png) 

Se o pedido não especificasse as decorações, o `expression` campo conteria `log10(2)`. 

Se `textDecorations` `true`estiver, bing pode incluir os seguintes marcadores nas cordas de exibição de respostas. Se não houver uma etiqueta HTML equivalente, a célula de mesa está vazia.

|Unicode|HTML|Descrição
|-|-|-
|U+E000|\<b>|Marca o início do termo de consulta (destaque do sucesso)
|U+E001|\</b>|Marca o fim do período de consulta
|U+E002|\<i>|Marca o início do conteúdo itálico 
|U+E003|\</i>|Marca o fim do conteúdo itálico
|U+E004|\<br/>|Marca uma rutura de linha
|U+E005||Marca o início de um número de telefone
|U+E006||Marca o fim de um número de telefone
|U+E007||Marca o início de um endereço
|U+E008||Marca o fim de um endereço
|U+E009|\&nbsp;|Marca um espaço sem quebras
|U+E00C|\<> forte|Marca o início de conteúdo arrojado
|U+E00D|\</> forte|Marca o fim do conteúdo arrojado
|U+E00E||Marca o início do conteúdo cujo fundo deve ser mais leve do que o seu fundo circundante
|U+E00F||Marca o fim do conteúdo cujo fundo deve ser mais leve do que o seu fundo circundante
|U+E010||Marca o início do conteúdo cujo fundo deve ser mais escuro do que o seu fundo circundante
|U+E011||Marca o fim do conteúdo cujo fundo deve ser mais escuro do que o seu fundo circundante
|U+E012|\<del>|Marca o início do conteúdo que deve ser atingido através
|U+E013|\</del>|Marca o fim do conteúdo que deve ser atingido através
|U+E016|\<sub>|Marca o início do conteúdo subscrito
|U+E017|\</sub>|Marca o fim do conteúdo subscript
|U+E018|\<sup>|Marca o início do conteúdo sobrescript
|U+E019|\</sup>|Marca o fim do conteúdo sobrescript

## <a name="next-steps"></a>Passos seguintes

* [O que é a API de pesquisa web bing?](overview.md) 
* [Redimensionar e recortar miniaturas](resize-and-crop-thumbnails.md)