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
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878375"
---
## <a name="create-an-face-resource"></a>Criar um recurso Face

1. Assine no [portal Azure](https://portal.azure.com)
1. Clique em criar recurso [ **Criar Face** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)
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
    |**Descrição Geral**|Ponto Final|Copie o ponto final. Parece semelhante a`https://face.cognitiveservices.azure.com/face/v1.0`|
    |**Chaves**|Chave da API|Cópia 1 das duas chaves. É uma corda de 32 caracteres alfanuméricos sem espaços ou traços, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
