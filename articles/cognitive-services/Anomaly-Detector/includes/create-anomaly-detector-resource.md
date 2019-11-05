---
title: Suporte de contentor
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: b40f1833f08074cb0a8d45fe3afc6bac7cbac7f0
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73523599"
---
## <a name="create-an-anomaly-detector-resource"></a>Criar um recurso de detector de anomalias

1. Entre no [portal do Azure](https://portal.azure.com)
1. Clique em criar recurso de [ **detector de anomalias** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector)
1. Insira todas as configurações necessárias:

    |Definição|Valor|
    |--|--|
    |Nome|Nome desejado (2-64 caracteres)|
    |Subscrição|Selecione a assinatura apropriada|
    |Localização|Selecione qualquer local próximo e disponível|
    |Escalão de Preço|`F0`-o tipo de preço mínimo|
    |Grupo de Recursos|Selecionar um grupo de recursos disponível|
    |Caixa de seleção de confirmação de visualização (obrigatório)|Se você leu ou não o aviso de **Visualização**|

1. Clique em **criar** e aguarde até que o recurso seja criado. Depois de criado, navegue até a página de recursos
1. Coletar `endpoint` configuradas e uma chave de API:

    |Guia de recursos no portal|Definição|Valor|
    |--|--|--|
    |**Descrição geral**|Ponto Final|Copie o ponto de extremidade. Ele é semelhante a `https://westus2.api.cognitive.microsoft.com/`|
    |**Novas**|Chave de API|Cópia 1 das duas chaves. É uma cadeia de caracteres de caractere alfanumérico 32 sem espaços ou traços, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|



