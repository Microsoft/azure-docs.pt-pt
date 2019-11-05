---
title: Como obter o ponto de extremidade de previsão v3
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: bc6e43faca47e360daa8214e6b9f6e9df4a2f130
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73495241"
---
1. No portal do LUIS, na seção Gerenciar (menu superior direito), na página chaves e pontos de extremidade (menu à esquerda), selecione a URL do ponto de extremidade na parte inferior da página.

    Essa ação abre a guia navegador com a URL do ponto de extremidade na barra de endereços.

    A URL tem a ID do aplicativo, a chave e o nome do slot. A URL do ponto de extremidade de previsão v3 é semelhante a:

    `https://REGION.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-KEY&<optional-name-value-pairs>&query=UTTERANCE-TEXT.`

