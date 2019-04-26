---
title: Descrever o conteúdo para adultos, imagem digitalizada
titleSuffix: Azure Cognitive Services
description: Conceitos relacionados a detecção de conteúdos para adultos em imagens usando a APi de imagem digitalizada.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 69a4c136e9c210dd40e004b8d5e1c1a2a8fceaa7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60368355"
---
# <a name="detect-adult-and-racy-content"></a>Detetar conteúdos para adultos

Imagem digitalizada pode detetar material para adultos em imagens, para que os desenvolvedores podem restringir a exibição de tais imagens no seu software. Sinalizadores de conteúdo são aplicadas com uma pontuação entre zero e outro para que os desenvolvedores podem interpretar os resultados, de acordo com suas próprias preferências. 

> [!NOTE]
> Esta funcionalidade também é oferecida pela [Azure Content Moderator](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview) serviço. Consulte esta alternativa para soluções para cenários de moderação de conteúdos mais rigorosos, como a moderação de texto e fluxos de trabalho de revisão humana.

## <a name="content-flag-definitions"></a>Definições do sinalizador de conteúdo

**Para adultos** imagens são definidas como aqueles que são pornográfico por natureza e, muitas vezes, retratar nudez e atos sexual. 

**Ousado** imagens são definidas conforme as imagens que estão sexualmente suggestive por natureza e, muitas vezes, contêm menos conteúdo sexualmente explícito que imagens etiquetagem como **adulto**. 

## <a name="identify-adult-and-racy-content"></a>Identificar conteúdo para adultos

O [analisar](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API.

O método de imagem de analisar retorna duas propriedades booleanas, `isAdultContent` e `isRacyContent`, na resposta JSON do método para indicar o conteúdo para adultos, respetivamente. O método também retorna duas propriedades, `adultScore` e `racyScore`, que representam as pontuações de confiança para identificar os conteúdos para adultos, respetivamente.

## <a name="next-steps"></a>Passos Seguintes

Conheça os conceitos [detetar conteúdo específicas do domínio](concept-detecting-domain-content.md) e [detetar rostos](concept-detecting-faces.md).
