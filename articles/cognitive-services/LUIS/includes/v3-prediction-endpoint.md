---
title: Como obter ponto final de previsão V3
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/05/2020
ms.author: diberry
ms.openlocfilehash: 18f5422202cf972d49349cc04b845c623cabffa3
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83589161"
---
1. No portal LUIS, na secção **Gerir** (menu superior-direito), na página **Dos Recursos Azure** (menu à esquerda), no separador Recursos de **Previsão,** copie a **Consulta de Exemplo** na parte inferior da página.

    Colá-lo num novo separador de navegador.

    O URL tem o seu id de aplicação, chave e nome de slot. O URL final de ponto final de previsão V3 parece:

    `https://YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-PREDICTION-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

