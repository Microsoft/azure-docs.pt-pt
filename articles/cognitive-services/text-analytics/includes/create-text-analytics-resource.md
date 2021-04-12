---
title: Criar um recurso de Análise de Texto de Serviços Cognitivos
titleSuffix: Azure Cognitive Services
description: Saiba como criar um recurso de Análise de Texto de Serviços Cognitivos.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 02/09/2021
ms.author: aahi
ms.openlocfilehash: 5b6479d48a51ba962f2f6bfba16dac3b0886a9ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "101750170"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Criar um recurso de Análise de Texto de Serviços Cognitivos

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione **Criar um recurso** e, em seguida, aceda a **IA + Machine Learning** > **Análise de Texto**.
   Ou, vá para [criar a Análise de Texto.](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)
1. Introduza todas as definições necessárias:

    |Definição|Valor|
    |--|--|
    |Nome|Introduza um nome (2 a 64 caracteres).|
    |Subscrição|Selecione a subscrição adequada.|
    |Localização|Selecione uma localização próxima.|
    |Escalão de preço| Introduza **S**, o escalão de preço standard.|
    |Grupo de recursos|Selecione um grupo de recursos disponível.|

1. Selecione **Criar** e aguarde que o recurso seja criado. O seu browser redireciona automaticamente para a página de recursos recém-criada.
1. Recolha a chave API configurada `endpoint` e a API:

    |Separador de recursos no portal|Definição|Valor|
    |--|--|--|
    |**Descrição geral**|Ponto final|Copie o ponto final. Parece semelhante a `https://my-resource.cognitiveservices.azure.com/text/analytics/v3.0` .|
    |**Chaves**|Chave de API|Copie uma das duas chaves. É uma corda alfanumérica de 32 caracteres sem espaços ou traços: <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>.|
