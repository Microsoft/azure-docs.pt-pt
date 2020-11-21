---
title: Adulto, picante, conteúdo sangrento - Visão de Computador
titleSuffix: Azure Cognitive Services
description: Conceitos relacionados com a deteção de conteúdo adulto em imagens utilizando o APi de Visão De Computador.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 5cc8a4508ceeda245fbc10a81e16f3ecf05284c7
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95013610"
---
# <a name="detect-adult-content"></a>Detetar conteúdo adulto

A Visão Computacional pode detetar material adulto em imagens para que os desenvolvedores possam restringir a exibição destas imagens no seu software. As bandeiras de conteúdo são aplicadas com uma pontuação entre zero e uma para que os desenvolvedores possam interpretar os resultados de acordo com as suas próprias preferências.

> [!NOTE]
> Grande parte desta funcionalidade é oferecida pelo serviço [Azure Content Moderador.](../content-moderator/overview.md) Consulte esta alternativa para soluções para cenários de moderação de conteúdo mais rigorosos, como moderação de texto e fluxos de trabalho de revisão humana.

## <a name="content-flag-definitions"></a>Definições de bandeira de conteúdo

Dentro da classificação "adulto" estão várias categorias diferentes:

- As imagens **adultas** são definidas como aquelas que são explicitamente sexuais na natureza e muitas vezes retratam nudez e atos sexuais.
- As imagens **picantes** são definidas como imagens que são sexualmente sugestivas na natureza e muitas vezes contêm conteúdo menos sexualmente explícito do que as imagens marcadas como **Adulto.**
- As imagens **gory** são definidas como aquelas que retratam gore.

## <a name="use-the-api"></a>Utilizar a API

Pode detetar conteúdo adulto com a [API de Imagem de Análise.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) Quando adiciona o valor do `Adult` parâmetro de consulta **visualFeatures,** a API devolve três propriedades booleanas &mdash; `isAdultContent` , e na sua `isRacyContent` resposta `isGoryContent` &mdash; JSON. O método também devolve propriedades &mdash; `adultScore` correspondentes, e que `racyScore` `goreScore` &mdash; representam pontuações de confiança entre zero e uma para cada categoria respetiva.

- [Quickstart: Analisar uma imagem (.NET SDK)](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
- [Quickstart: Analisar uma imagem (REST API)](./quickstarts/csharp-analyze.md)