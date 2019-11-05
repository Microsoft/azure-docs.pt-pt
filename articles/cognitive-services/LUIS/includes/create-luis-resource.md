---
title: Criar recurso LUIS
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/23/2019
ms.author: dapine
ms.openlocfilehash: a765ac27936da9da5a2f41464c17491e3561f44b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73465920"
---
## <a name="create-a-luis-resource"></a>Criar um recurso LUIS

1. Entre no [portal do Azure](https://portal.azure.com)
1. Clique em [criar **reconhecimento vocal** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
1. Insira todas as configurações necessárias:

    |Definição|Valor|
    |--|--|
    |Nome|Nome desejado (2-64 caracteres)|
    |Subscrição|Selecione a assinatura apropriada|
    |Localização|Selecione qualquer local próximo e disponível|
    |Escalão de Preço|`F0`-o tipo de preço mínimo|
    |Grupo de Recursos|Selecionar um grupo de recursos disponível|

1. Clique em **criar** e aguarde até que o recurso seja criado. Após a criação, navegue até a página de recursos.
1. Coletar `endpoint` configuradas e uma chave de API, consulte [coletando parâmetros necessários](#gathering-required-parameters).

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]
