---
title: Criar um recurso de Análise de Texto de Serviços Cognitivos
titleSuffix: Azure Cognitive Services
description: Aprenda a criar um recurso de Análise de Texto de Serviços Cognitivos.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: d3b65174f3d161e0b8780a890f297d9d1c812b44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74383465"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Criar um recurso de Análise de Texto de Serviços Cognitivos

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
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
