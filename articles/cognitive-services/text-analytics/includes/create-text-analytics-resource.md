---
title: Criar um recurso de Análise de Texto de Serviços Cognitivos
titleSuffix: Azure Cognitive Services
description: Aprenda a criar um recurso de Análise de Texto de Serviços Cognitivos.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 6cd653909e26dc5e0484ca289a1d2ab47e20457f
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876452"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Criar um recurso de Análise de Texto de Serviços Cognitivos

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione **Criar um recurso**e, em seguida, ir para **AI + Machine Learning** > **Text Analytics**.
   Ou, vá criar análise de [texto.](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)
1. Introduza todas as definições necessárias:

    |Definição|Valor|
    |--|--|
    |Nome|Introduza um nome (2-64 caracteres).|
    |Subscrição|Selecione a Subscrição adequada.|
    |Localização|Selecione um local próximo.|
    |Escalão de preço| Insira **S,** o nível de preços padrão.|
    |Grupo de recursos|Selecione um grupo de recursos disponíveis.|

1. Selecione **Criar**e aguarde a criação do recurso. O seu navegador redireciona automaticamente para a página de recursos recém-criada.
1. Recolher a `endpoint` chave Configurada e API:

    |Separador de recursos no portal|Definição|Valor|
    |--|--|--|
    |**Descrição Geral**|Ponto Final|Copie o ponto final. Parece semelhante a `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`.|
    |**Chaves**|Chave da API|Copie uma das duas chaves. É uma corda alfanumérica de 32 caracteres sem `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` espaços ou traços: <>.|
