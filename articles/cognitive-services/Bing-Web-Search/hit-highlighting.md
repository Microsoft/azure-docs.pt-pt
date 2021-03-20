---
title: Como usar marcadores de decoração para destacar texto - Bing Web Search API
titleSuffix: Azure Cognitive Services
description: Aprenda a usar decorações de texto e acerte em destaque nos resultados da sua pesquisa utilizando a API de Pesquisa web Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 5365B568-EA55-4D97-8FBE-0AF60158D4D5
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: scottwhi
ms.openlocfilehash: 32c3279cacddf10e77e8d245ba525ab766efd6fd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96351883"
---
# <a name="using-decoration-markers-to-highlight-text"></a>Usando marcadores de decoração para destacar texto

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)

Bing suporta o destaque do sucesso, que marca termos de consulta (ou outros termos que Bing considera relevantes) nas cadeias de exibição de algumas respostas. Por exemplo, um resultado de página web `name` , e campos podem conter termos de consulta `displayUrl` `snippet` marcados. 

Por predefinição, bing não inclui marcadores de destaque nas cordas de exibição. Para ativar os marcadores, inclua o `textDecorations` parâmetro de consulta no seu pedido e desa um remeta-o para `true` .

## <a name="hit-highlighting-example"></a>Exemplo de destaque de sucesso

O exemplo a seguir mostra um resultado web para `Sailing Dinghy` . Bing marcou o início e o fim do termo de consulta usando os caracteres E000 e E001 Unicode.
  
![Destaque de sucesso](./media/cognitive-services-bing-web-api/bing-hit-highlighting.png) 

Antes de apresentar o resultado na interface do utilizador, substitua os caracteres Unicode por outros apropriados para o seu formato de exibição.

## <a name="marker-formatting"></a>Formatação do marcador

Bing oferece a opção de usar caracteres Unicode ou tags HTML como marcadores. Para especificar quais os marcadores a utilizar, inclua o parâmetro de consulta [textFormat:](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#textformat) 

| Valor             | Marcador                       |
|-------------------|------------------------------|
| `textFormat=Raw`  | Caracteres unicódigo (padrão) |
| `textFormat=HTML` | Caracteres HTML              |

## <a name="additional-text-decorations"></a>Decorações de texto adicionais

Bing pode devolver várias decorações de texto diferentes. Por exemplo, uma `Computation` resposta pode conter marcadores de inscrição para o termo de consulta `log(2)` no `expression` campo.

![marcadores de computação](./media/cognitive-services-bing-web-api/bing-markers-computation.png) 

Se o pedido não especificar decorações, o `expression` campo conterá `log10(2)` . 

Se `textDecorations` for `true` , Bing pode incluir os seguintes marcadores nas cadeias de respostas do visor. Se não houver uma etiqueta HTML equivalente, a célula de mesa está vazia.

|Unicode|HTML|Description
|-|-|-
|U+E000|\<b>|Marca o início do termo de consulta (destaque de sucesso)
|U+E001|\</b>|Marca o fim do termo de consulta
|U+E002|\<i>|Marca o início do conteúdo itálico 
|U+E003|\</i>|Marca o fim do conteúdo itálico
|U+E004|\<br/>|Marca uma rutura de linha
|U+E005||Marca o início de um número de telefone
|U+E006||Marca o fim de um número de telefone
|U+E007||Marca o início de um endereço
|U+E008||Marca o fim de um endereço
|U+E009|\&nbsp;|Marca um espaço sem rutura
|U+E00C|\<strong>|Marca o início de conteúdo arrojado
|U+E00D|\</strong>|Marca o fim do conteúdo arrojado
|U+E00E||Marca o início do conteúdo cujo fundo deve ser mais leve do que o seu fundo circundante
|U+E00F||Marca o fim do conteúdo cujo fundo deve ser mais leve do que o seu fundo circundante
|U+E010||Marca o início do conteúdo cujo fundo deve ser mais escuro do que o seu fundo circundante
|U+E011||Marca o fim do conteúdo cujo fundo deve ser mais escuro do que o seu fundo circundante
|U+E012|\<del>|Marca o início do conteúdo que deve ser atingido através
|U+E013|\</del>|Marca o fim do conteúdo que deve ser atingido através
|U+E016|\<sub>|Marca o início do conteúdo subscrito
|U+E017|\</sub>|Marca o fim do conteúdo subscrito
|U+E018|\<sup>|Marca o início do conteúdo sobrescrito
|U+E019|\</sup>|Marca o fim do conteúdo sobrescrito

## <a name="next-steps"></a>Passos seguintes

* [O que é a API de Pesquisa Web Bing?](overview.md) 
* [Redimensionar e recortar miniaturas](resize-and-crop-thumbnails.md)