---
title: Como usar marcadores de decoração para realçar API de Pesquisa na Web do Bing de texto
titleSuffix: Azure Cognitive Services
description: Saiba como usar decorações de texto e realce de clique nos resultados da pesquisa usando o API de Pesquisa na Web do Bing.
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
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854026"
---
# <a name="using-decoration-markers-to-highlight-text"></a>Usando marcadores de decoração para realçar o texto

O Bing dá suporte ao realce de visita, que marca os termos de consulta (ou outros termos que o Bing encontra relevante) nas cadeias de caracteres de exibição de algumas respostas. Por exemplo, os campos, `name` `displayUrl`e `snippet` dos resultados de uma página da Web podem conter os termos de consulta marcados. 

Por padrão, o Bing não inclui marcadores de realce em cadeias de caracteres de exibição. Para habilitar os marcadores, inclua o `textDecorations` parâmetro de consulta em sua solicitação e defina-o como. `true`

## <a name="hit-highlighting-example"></a>Exemplo de realce de clique

O exemplo a seguir mostra um resultado da `Sailing Dinghy`Web para. Bing marcou o início e o final do termo de consulta usando os caracteres Unicode E000 e E001.
  
![Realce de clique](./media/cognitive-services-bing-web-api/bing-hit-highlighting.png) 

Antes de exibir o resultado na interface do usuário, substitua os caracteres Unicode pelos que são apropriados para seu formato de exibição.

## <a name="marker-formatting"></a>Formatação do marcador

O Bing fornece a opção de usar caracteres Unicode ou marcas HTML como marcadores. Para especificar quais marcadores usar, inclua o parâmetro de [](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#textformat) consulta TextFormat: 

| Value             | Marker                       |
|-------------------|------------------------------|
| `textFormat=Raw`  | Caracteres Unicode (padrão) |
| `textFormat=HTML` | Caracteres HTML              |

## <a name="additional-text-decorations"></a>Decorações de texto adicionais

O Bing pode retornar várias decorações de texto diferentes. Por exemplo, uma `Computation` resposta pode conter marcadores de subscrito para o termo `log(2)` de consulta no `expression` campo.

![marcadores de computação](./media/cognitive-services-bing-web-api/bing-markers-computation.png) 

Se a solicitação não especificou decorações, o `expression` campo conterá `log10(2)`. 

Se `textDecorations` for`true`, o Bing pode incluir os marcadores a seguir nas cadeias de caracteres de exibição de respostas. Se não houver nenhuma marca HTML equivalente, a célula da tabela estará vazia.

|Unicode|HTML|Descrição
|-|-|-
|U+E000|\<b>|Marca o início do termo de consulta (realce de clique)
|U+E001|\</b>|Marca o final do termo de consulta
|U+E002|\<i>|Marca o início do conteúdo em itálico 
|U+E003|\</i>|Marca o final do conteúdo em itálico
|U+E004|\<br/>|Marca uma quebra de linha
|U+E005||Marca o início de um número de telefone
|U+E006||Marca o fim de um número de telefone
|U+E007||Marca o início de um endereço
|U+E008||Marca o final de um endereço
|U+E009|\&nbsp;|Marca um espaço não separável
|U+E00C|\<strong>|Marca o início do conteúdo em negrito
|U+E00D|\</strong>|Marca o final do conteúdo em negrito
|U+E00E||Marca o início do conteúdo cujo plano de fundo deve ser mais leve do que seu plano de fundo ao redor
|U+E00F||Marca o fim do conteúdo cujo plano de fundo deve ser mais leve do que seu plano de fundo ao redor
|U+E010||Marca o início do conteúdo cujo plano de fundo deve ser mais escuro do que seu plano de fundo ao redor
|U+E011||Marca o fim do conteúdo cujo plano de fundo deve ser mais escuro do que seu plano de fundo ao redor
|U+E012|\<del>|Marca o início do conteúdo que deve ser riscado
|U+E013|\</del>|Marca o final do conteúdo que deve ser riscado
|U+E016|\<sub>|Marca o início do conteúdo de subscrito
|U+E017|\</sub>|Marca o final do conteúdo de subscrito
|U+E018|\<sup>|Marca o início do conteúdo sobrescrito
|U+E019|\</sup>|Marca o final do conteúdo sobrescrito

## <a name="next-steps"></a>Passos Seguintes

* [O que é o API de Pesquisa na Web do Bing?](overview.md) 
* [Redimensionar e cortar miniaturas](resize-and-crop-thumbnails.md)