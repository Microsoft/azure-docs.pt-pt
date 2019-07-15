---
title: Criar um recurso de análise de texto dos serviços cognitivos
titleSuffix: Azure Cognitive Services
description: Saiba como criar um recurso de análise de texto dos serviços cognitivos.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 7e8b4480911f00afa8524ef4b81d697bb8ee5bcc
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67877472"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Criar um recurso de análise de texto dos serviços cognitivos

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione **criar um recurso**e, em seguida, aceda à **IA + Machine Learning** > **análise de texto**.
   Em alternativa, aceda a [criar de análise de texto](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics).
1. Introduza as definições necessárias:

    |Definição|Valor|
    |--|--|
    |Nome|Introduza um nome (2 e 64 carateres)|
    |Subscription|Selecione a subscrição adequada|
    |Location|Selecione uma localização próximas|
    |Escalão de preço| Introduza **S**, o escalão de preço standard|
    |Resource group|Selecione um grupo de recursos disponíveis|

1. Selecione **criar** e espere até que o recurso a ser criada. O browser redireciona automaticamente para a página de recursos criado recentemente.
1. Recolher configurada `endpoint` e uma chave de API:

    |Guia de recursos no portal|Definição|Valor|
    |--|--|--|
    |**Descrição Geral**|Ponto Final|Copie o ponto final. Ele é semelhante a `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`|
    |**chaves**|Chave de API|Copie uma das duas chaves. É uma cadeia de alfanumérica de 32 carateres sem espaços ou traços: <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>.|
