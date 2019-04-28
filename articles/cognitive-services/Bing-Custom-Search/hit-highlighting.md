---
title: Utilizar marcadores decoration para realçar texto - pesquisa personalizada do Bing
titlesuffix: Azure Cognitive Services
description: Mostra como ativar decorações de texto nas respostas da pesquisa.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: maheshb
ms.openlocfilehash: bf87db232484a8ba4fa1e3b8e4de921f152e2345
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62110123"
---
# <a name="using-decoration-markers-to-highlight-text"></a>Utilizar marcadores decoration para realçar o texto

Bing suporta detetor de ocorrências que marca a termos de consulta (ou outros termos que Bing encontra relevantes) nas cadeias de apresentação de algumas de respostas. Por exemplo, uma página Web `name`, `displayUrl`, e `snippet` campos podem marcar os termos de consulta.

Por predefinição, o Bing não inclui realce de marcadores em cadeias de apresentação. Para incluir os marcadores, incluem os `textDecorations` parâmetro no seu pedido de consulta e defina-o como **true**. Bing marca os termos de consulta com os carateres E000 e E001 Unicode para marcar o início e fim do período da. Por exemplo, se o termo de consulta é Sailing Dinghy e o termo existe no campo, o termo está entre caracteres de realce acessos conforme mostrado no exemplo a seguir:  
  
![Detetor](./media/bing-hit-highlighting.PNG) 

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

A exemplo a seguir mostra um `Computation` resposta que contém os marcadores subscrito para um termo de consulta log(2). O `expression` campo contém o marcadores apenas se for ' é textDecoration **true**.

![marcadores de computação](./media/bing-markers-computation.PNG) 

Se o pedido não pediu decorações, a expressão seria log10(2). 
  
