---
title: Suporte de contentor
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 8dbe36693e551bd03022f4a00044b75b912c834a
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875137"
---
## <a name="create-an-anomaly-detector-resource"></a>Criar um recurso do Detetor de Anomalias

1. Assine no [portal Azure](https://portal.azure.com)
1. Clique em criar recurso [de detetor de **anomalias** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector)
1. Introduza todas as definições necessárias:

    |Definição|Valor|
    |--|--|
    |Nome|Nome desejado (2-64 caracteres)|
    |Subscrição|Selecione subscrição apropriada|
    |Localização|Selecione qualquer local próximo e disponível|
    |Escalão de Preço|`F0`- o nível de preços mínimos|
    |Grupo de Recursos|Selecione um grupo de recursos disponíveis|
    |Caixa de verificação de confirmação de pré-visualização (necessária)|Quer tenha lido ou não o aviso de **pré-visualização**|

1. Clique em **Criar** e aguarde a criação do recurso. Depois de criado, navegue para a página de recursos
1. Colete configurado `endpoint` e uma chave API:

    |Separador de recursos no Portal|Definição|Valor|
    |--|--|--|
    |**Descrição Geral**|Ponto Final|Copie o ponto final. Parece semelhante a`https://westus2.api.cognitive.microsoft.com/`|
    |**Chaves**|Chave da API|Cópia 1 das duas chaves. É uma corda de 32 caracteres alfanuméricos sem espaços ou traços, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|



