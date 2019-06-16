---
author: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/09/2018
ms.author: nitinme
ms.openlocfilehash: fccc036a5e0422508f7ebc3370a4b5faa5176dc2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66124761"
---
Bing suporta detetor de ocorrências que marca a termos de consulta (ou outros termos que Bing encontra relevantes) nas cadeias de apresentação de algumas de respostas. Por exemplo, uma página Web `name`, `displayUrl`, e `snippet` campos podem marcar os termos de consulta.

Por predefinição, o Bing não inclui realce de marcadores em cadeias de apresentação. Para incluir os marcadores, incluem os `textDecorations` parâmetro no seu pedido de consulta e defina-o como **true**. Bing marca os termos de consulta com os carateres E000 e E001 Unicode para marcar o início e fim do período da. Por exemplo, se o termo de consulta é Sailing Dinghy e o termo existe no campo, o termo está entre caracteres de realce acessos conforme mostrado no exemplo a seguir:  
  
![Detetor](./media/cognitive-services-bing-hit-highlighting/bing-hit-highlighting.PNG) 

Antes de exibir a cadeia de caracteres na interface do usuário, poderia substituir os carateres Unicode com caracteres que são adequadas para seu formato de apresentação. Por exemplo, se estiver a apresentar o texto como HTML, pode realçar o termo de consulta, substituindo E000 com o < b\> e E001 com </b\>. Se não pretender aplicar a formatação, remova os marcadores da cadeia de caracteres. 

O Bing oferece a opção de utilizar os carateres Unicode ou tags de HTML como marcadores. Para especificar quais marcadores para utilizar, inclua o `textFormat` parâmetro de consulta. Para marcar o conteúdo com caracteres Unicode, defina `textFormat` Raw (predefinição) e marcar o conteúdo com marcas HTML, defina `textFormat` ao HTML. 
  
Se `textDecorations` é **true**, Bing pode incluir os seguintes marcadores em cadeias de caracteres de exibição de respostas. Se não existe nenhum equivalente HTML, a célula de tabela HTML está vazia.

|Unicode|HTML|Descrição
|-|-|-
|U+E000|\<b>|Marca o início do período da consulta (detetor de ocorrências)
|U+E001|\</b>|Marca o fim do período da consulta
|U+E002|\<i>|Marca o início do conteúdo em itálico 
|U+E003|\</i>|Marca o fim do conteúdo em itálico
|U+E004|\<br/>|Marca uma quebra de linha
|U+E005||Marca o início de um número de telefone
|U+E006||Marca o fim de um número de telefone
|U+E007||Marca o início de um endereço
|U+E008||Marca o fim de um endereço
|U+E009|\&nbsp;|Marca um espaço sem quebra
|U+E00C|\<strong>|Marca o início do conteúdo em negrito
|U+E00D|\</strong>|Marca o fim do conteúdo em negrito
|U+E00E||Marca o início do cujo em segundo plano deve ser mais leve do seu plano de fundo ao redor de conteúdo
|U+E00F||Marca o fim de cujo em segundo plano deve ser mais leve do seu plano de fundo ao redor de conteúdo
|U+E010||Marca o início do cujo em segundo plano deve ser mais do que o plano de fundo ao redor de conteúdo
|U+E011||Marca o fim de cujo em segundo plano deve ser mais do que o plano de fundo ao redor de conteúdo
|U+E012|\<del>|Marca o início do conteúdo que deve ser chamaram a atenção por meio de
|U+E013|\</del>|Marca o fim de conteúdo que deve ser chamaram a atenção por meio de
|U+E016|\<sub>|Marca o início do conteúdo subscrito
|U+E017|\</sub>|Marca o fim do conteúdo subscrito
|U+E018|\<sup>|Marca o início do conteúdo sobrescrito
|U+E019|\</sup>|Marca o fim do conteúdo sobrescrito

A exemplo a seguir mostra um `Computation` resposta que contém os marcadores subscrito para um termo de consulta log(2). O `expression` campo contém o se apenas de marcadores `textDecoration` é **verdadeiro**.

![marcadores de computação](./media/cognitive-services-bing-hit-highlighting/bing-markers-computation.PNG) 

Se o pedido não pediu decorações, a expressão seria log10(2). 
  
