---
title: Suporte de contentor
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: aahi
ms.openlocfilehash: 0a330c2401412e1e3d5e2e49bf8121a681342808
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80878375"
---
## <a name="create-an-face-resource"></a>Criar um recurso Face

1. Inscreva-se no [portal Azure](https://portal.azure.com)
1. Clique [em Criar Recurso **Face** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)
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
    |**Descrição geral**|Ponto final|Copie o ponto final. Parece-se com `https://face.cognitiveservices.azure.com/face/v1.0`|
    |**Chaves**|Chave de API|Copiar 1 das duas chaves. É uma corda de 32 caracteres alfanuméricos sem espaços ou traços, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` .|
