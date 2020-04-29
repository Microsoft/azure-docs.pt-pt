---
title: Como obter ponto final de previsão V3
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 23e7f88b23ac7af7470183877eeb1be89e79779c
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80287752"
---
1. No portal LUIS, na secção **Gerir** (menu superior direito), na página **Dos Recursos Azure** (menu à esquerda), no separador Recursos de **Previsão,** copie o **exemplo Consulta** na parte inferior da página.

    Colá-lo num novo separador de navegador.

    O URL tem o seu id de aplicação, chave e nome de slot. O URL final de ponto final de previsão V3 parece:

    `https://REGION.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

