---
title: Conteúdo adulto e erótico-Pesquisa Visual Computacional
titleSuffix: Azure Cognitive Services
description: Conceitos relacionados à detecção de conteúdo adulto e erótico em imagens usando a APi Pesquisa Visual Computacional.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: ca5f35ab47822d74de556671c38886942d23d9ff
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946289"
---
# <a name="detect-adult-and-racy-content"></a>Detectar conteúdo adulto e erótico

Pesquisa Visual Computacional pode detectar material adulto em imagens para que os desenvolvedores possam restringir a exibição dessas imagens em seu software. Os sinalizadores de conteúdo são aplicados com uma pontuação entre zero e um para que os desenvolvedores possam interpretar os resultados de acordo com suas próprias preferências. 

> [!NOTE]
> Esse recurso também é oferecido pelo serviço de [Content moderator do Azure](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview) . Veja essa alternativa para soluções para cenários mais rigorosos de moderação de conteúdo, como a moderação de texto e fluxos de trabalho de revisão humana.

## <a name="content-flag-definitions"></a>Definições de sinalizador de conteúdo

As imagens adultas são definidas como aquelas que são pornográficos por natureza e muitas vezes descrevem nudez e atos sexuais. 

As imagens **erótico** são definidas como imagens que são obscenas com natureza e geralmente contêm conteúdo menos sexualmente explícito que as imagens marcadas como **adultas**. 

## <a name="identify-adult-and-racy-content"></a>Identificar conteúdo adulto e erótico

A API de [análise](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) .

O método Analyze Image retorna duas propriedades `isAdultContent` booleanas e `isRacyContent`, na resposta JSON do método, para indicar conteúdo adulto e erótico, respectivamente. O método também retorna duas propriedades, `adultScore` e `racyScore`, que representam as pontuações de confiança para identificar conteúdo adulto e erótico, respectivamente.

## <a name="next-steps"></a>Passos Seguintes

Aprenda conceitos sobre [a detecção de conteúdo específico de domínio](concept-detecting-domain-content.md) e a [detecção de rostos](concept-detecting-faces.md).
