---
title: Como obter o ponto final de previsão V3
titleSuffix: Azure Cognitive Services
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 05/05/2020
ms.openlocfilehash: a607ad71915606d6046b4c71291a49b8641bdcc9
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91545252"
---
1. No portal LUIS, na secção **Gerir** (menu superior-direito), na página **Azure Resources** (menu esquerdo), no separador **Recursos de Previsão,** copiar a **Consulta exemplo** na parte inferior da página.

    Cole o URL num novo separador de navegador.

    O URL tem o seu ID de aplicação, chave e nome de slot. O URL do ponto final de previsão V3 parece:

    `https://YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-PREDICTION-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

