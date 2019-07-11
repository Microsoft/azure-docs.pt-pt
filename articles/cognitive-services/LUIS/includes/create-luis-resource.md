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
ms.openlocfilehash: 52d8e1355558b197b193a50c7cde571799541268
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717154"
---
## <a name="create-a-luis-resource"></a>Criar um recurso de LUIS

1. Inicie sessão no [portal do Azure](https://portal.azure.com)
1. Clique em [criar **compreensão de idiomas**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUIS)
1. Introduza todas as definições necessárias:

    |Definição|Valor|
    |--|--|
    |Nome|Nome pretendido (2 e 64 carateres)|
    |Subscription|Selecione a subscrição adequada|
    |Location|Selecione qualquer localização disponível e próximas|
    |Escalão de Preço|`F0` -o escalão de preço mínimo|
    |Grupo de Recursos|Selecione um grupo de recursos disponíveis|

1. Clique em **criar** e espere até que o recurso a ser criada. Depois de criado, navegue para a página de recursos
1. Recolher configurado `endpoint` e uma chave de API:

    |Guia de recursos no Portal|Definição|Valor|
    |--|--|--|
    |**Descrição Geral**|Ponto Final|Copie o ponto final. Que é semelhante a `https://luis.cognitiveservices.azure.com/luis/v2.0`|
    |**chaves**|Chave de API|Copie a 1 de duas chaves. É uma cadeia de caracteres alfanuméricos 32 sem espaços ou traços, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
