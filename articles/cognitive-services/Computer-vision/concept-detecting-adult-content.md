---
title: Adulto, picante, conteúdo sangrento - Visão de Computador
titleSuffix: Azure Cognitive Services
description: Conceitos relacionados com a deteção de conteúdo adulto em imagens utilizando a API de Visão Computacional.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: ceef604fe07a11be89376e26c6fecc49298ebacf
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778864"
---
# <a name="detect-adult-content"></a>Detetar conteúdo adulto

A Visão Computacional pode detetar material adulto em imagens para que os desenvolvedores possam restringir a exibição destas imagens no seu software. As bandeiras de conteúdo são aplicadas com uma pontuação entre zero e uma para que os desenvolvedores possam interpretar os resultados de acordo com as suas próprias preferências.

> [!NOTE]
> Grande parte desta funcionalidade é oferecida pelo serviço [Azure Content Moderador.](../content-moderator/overview.md) Consulte esta alternativa para soluções para cenários de moderação de conteúdo mais rigorosos, como moderação de texto e fluxos de trabalho de revisão humana.

## <a name="content-flag-definitions"></a>Definições de bandeira de conteúdo

A classificação "adulto" contém várias categorias diferentes:

- As imagens **adultas** são explicitamente sexuais na natureza e muitas vezes mostram nudez e atos sexuais.
- As imagens **picantes** são sexualmente sugestivas na natureza e muitas vezes contêm conteúdo menos sexualmente explícito do que as imagens marcadas como **Adulto.**
- **Imagens sangrentas** mostram sangue/sangue.

## <a name="use-the-api"></a>Utilizar a API

Pode detetar conteúdo adulto com a [API de Imagem de Análise.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) Quando adiciona o valor do `Adult` parâmetro de consulta **visualFeatures,** a API devolve três propriedades booleanas &mdash; `isAdultContent` , e na sua `isRacyContent` resposta `isGoryContent` &mdash; JSON. O método também devolve propriedades &mdash; `adultScore` correspondentes, e que `racyScore` `goreScore` &mdash; representam pontuações de confiança entre zero e uma para cada categoria respetiva.

- [Quickstart: Computer Vision REST API ou bibliotecas de clientes](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
