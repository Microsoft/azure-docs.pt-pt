---
title: Suporte de contentor
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 8f93e0ffe74ade79059fc845788faf36ab5b4f13
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91025017"
---
## <a name="create-an-anomaly-detector-resource"></a>Criar um recurso de detetor de anomalias

1. Inscreva-se no [portal Azure](https://portal.azure.com)
1. Clique em Criar Recurso [ **de Detetor de Anomalias** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector)
1. Introduza todas as definições necessárias:

    |Definição|Valor|
    |--|--|
    |Nome|Nome desejado (2-64 caracteres)|
    |Subscrição|Selecione subscrição apropriada|
    |Localização|Selecione qualquer localização próxima e disponível|
    |Escalão de Preço|`F0` - o nível mínimo de preços|
    |Grupo de Recursos|Selecione um grupo de recursos disponível|

1. Clique **em Criar** e aguarde a criação do recurso. Depois de criado, navegue para a página de recursos
1. Recolha configurado `endpoint` e uma chave API:

    |Separador de recursos no Portal|Definição|Valor|
    |--|--|--|
    |**Descrição geral**|Ponto final|Copie o ponto final. Parece-se com `https://westus2.api.cognitive.microsoft.com/`|
    |**Chaves**|Chave de API|Copiar 1 das duas chaves. É uma corda de 32 caracteres alfanuméricos sem espaços ou traços, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` .|



