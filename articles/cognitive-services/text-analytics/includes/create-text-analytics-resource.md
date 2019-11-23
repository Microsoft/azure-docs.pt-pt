---
title: Create a Cognitive Services Text Analytics resource
titleSuffix: Azure Cognitive Services
description: Learn how to create a Cognitive Services Text Analytics resource.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: d3b65174f3d161e0b8780a890f297d9d1c812b44
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383465"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Create a Cognitive Services Text Analytics resource

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Select **Create a resource**, and then go to **AI + Machine Learning** > **Text Analytics**.
   Or, go to [Create Text Analytics](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics).
1. Enter all the required settings:

    |Definição|Valor|
    |--|--|
    |Nome|Enter a name (2-64 characters).|
    |Subscrição|Select the appropriate subscription.|
    |Localização|Select a nearby location.|
    |Escalão de preço| Enter **S**, the standard pricing tier.|
    |Grupo de recursos|Select an available resource group.|

1. Select **Create**, and wait for the resource to be created. Your browser automatically redirects to the newly created resource page.
1. Collect the configured `endpoint` and an API key:

    |Resource tab in portal|Definição|Valor|
    |--|--|--|
    |**Descrição geral**|Ponto Final|Copy the endpoint. It appears similar to `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`.|
    |**Keys**|API Key|Copy one of the two keys. It's a 32-character alphanumeric string with no spaces or dashes: <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>.|
