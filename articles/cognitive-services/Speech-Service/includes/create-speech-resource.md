---
title: Suporte de contentor
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: trbye
ms.openlocfilehash: 8abd520a7bb80f1f9a2d8ebcbe46b90fe59a96ec
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421862"
---
## <a name="create-a-speech-resource"></a>Criar um recurso da Fala

1. Assine no [portal Azure](https://portal.azure.com)
1. Clique em criar recurso [ **de fala** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)
1. Introduza todas as definições necessárias:

    |Definição|Valor|
    |--|--|
    |Nome|Nome desejado (2-64 caracteres)|
    |Subscrição|Selecione subscrição apropriada|
    |Localização|Selecione qualquer local próximo e disponível|
    |Escalão de Preço|`F0`- o nível de preços mínimos|
    |Grupo de Recursos|Selecione um grupo de recursos disponíveis|

1. Clique em **Criar** e aguarde a criação do recurso. Depois de criado, navegue para a página de recursos
1. Colete configurado `endpoint` e uma chave API:

    |Separador de recursos no Portal|Definição|Valor|
    |--|--|--|
    |**Descrição Geral**|Ponto Final|Copie o ponto final. Parece semelhante a`https://speech.cognitiveservices.azure.com/sts/v1.0/issuetoken`|
    |**Chaves**|Chave da API|Cópia 1 das duas chaves. É uma corda de 32 caracteres alfanuméricos sem espaços ou traços, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
