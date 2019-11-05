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
ms.openlocfilehash: 3c42bf2b2acc2472741bd603ea9d653a314ecc40
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73523641"
---
## <a name="create-a-speech-resource"></a>Criar um recurso de fala

1. Entre no [portal do Azure](https://portal.azure.com)
1. Clique em criar recurso de [ **fala** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)
1. Insira todas as configurações necessárias:

    |Definição|Valor|
    |--|--|
    |Nome|Nome desejado (2-64 caracteres)|
    |Subscrição|Selecione a assinatura apropriada|
    |Localização|Selecione qualquer local próximo e disponível|
    |Escalão de Preço|`F0`-o tipo de preço mínimo|
    |Grupo de Recursos|Selecionar um grupo de recursos disponível|

1. Clique em **criar** e aguarde até que o recurso seja criado. Depois de criado, navegue até a página de recursos
1. Coletar `endpoint` configuradas e uma chave de API:

    |Guia de recursos no portal|Definição|Valor|
    |--|--|--|
    |**Descrição geral**|Ponto Final|Copie o ponto de extremidade. Ele é semelhante a `https://speech.cognitiveservices.azure.com/sts/v1.0/issuetoken`|
    |**Novas**|Chave de API|Cópia 1 das duas chaves. É uma cadeia de caracteres de caractere alfanumérico 32 sem espaços ou traços, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
