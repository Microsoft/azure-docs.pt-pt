---
title: Como obter o ponto final de previsão V3
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 05/05/2020
ms.author: diberry
ms.openlocfilehash: d73508e8734883f5ffef205b6c1f03905f349f91
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91316494"
---
1. No portal LUIS, na secção **Gerir** (menu superior-direito), na página **Azure Resources** (menu esquerdo), no separador **Recursos de Previsão,** copiar a **Consulta exemplo** na parte inferior da página.

    Cole o URL num novo separador de navegador.

    O URL tem o seu ID de aplicação, chave e nome de slot. O URL do ponto final de previsão V3 parece:

    `https://YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-PREDICTION-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

