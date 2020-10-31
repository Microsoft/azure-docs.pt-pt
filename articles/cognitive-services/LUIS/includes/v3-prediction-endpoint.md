---
title: Como obter o ponto final de previsão V3
titleSuffix: Azure Cognitive Services
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 05/05/2020
ms.openlocfilehash: 85fcd0308083350a02afd4eadac2b361337f3b33
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93128223"
---
1. No portal LUIS, na secção **Gerir** (menu superior-direito), na página **Azure Resources** (menu esquerdo), no separador **Recursos de Previsão,** copiar a **Consulta exemplo** na parte inferior da página. O URL tem o seu ID de aplicação, chave e nome de slot. O URL do ponto final de previsão V3 tem a forma de: `https://YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-PREDICTION-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

    :::image type="content" source="../media/prediction-resources-example-query.png" alt-text="consulta de exemplo na secção de recursos de previsão" lightbox="../media/prediction-resources-example-query.png":::
    
    Cole o URL num novo separador de navegador. Se não vir o URL, não tem um recurso de previsão e terá de criar um. 

    

    

