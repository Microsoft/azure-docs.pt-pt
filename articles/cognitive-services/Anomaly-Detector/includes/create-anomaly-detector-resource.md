---
title: Suporte de contentor
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: 037b9746571678e24e80bd76e7e2ed173ab8eb90
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/18/2020
ms.locfileid: "97677565"
---
## <a name="create-an-anomaly-detector-resource"></a>Criar um recurso de detetor de anomalias

1. Inscreva-se no <a href="https://portal.azure.com" target="_blank">portal <span class="docon docon-navigate-external x-hidden-focus"></span> Azure</a>.
1. Selecione Criar recurso <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector" target="_blank">de detetor <span class="docon docon-navigate-external x-hidden-focus"></span> de anomalias.</a>
1. Introduza todas as definições necessárias:

    |Definição|Valor|
    |--|--|
    |Nome|Nome desejado (2-64 caracteres)|
    |Subscrição|Selecione subscrição apropriada|
    |Localização|Selecione qualquer localização próxima e disponível|
    |Escalão de Preço|`F0` - 10 chamadas por segundo, 20K Transações por mês. <br> Ou:<br> `S0` - 80 chamadas por segundo|
    |Grupo de Recursos|Selecione um grupo de recursos disponível|

1. Clique **em Criar** e aguarde a criação do recurso. Depois de criado, navegue para a página de recursos
1. Recolha configurado `endpoint` e uma chave API:

    |Teclas e separador Endpoint no portal|Definição|Valor|
    |--|--|--|
    |**Descrição geral**|Ponto final|Copie o ponto final. Parece-se com ` https://<your-resource-name>.cognitiveservices.azure.com/`|
    |**Chaves**|Chave de API|Copiar 1 das duas chaves. É uma corda de 32 caracteres alfanuméricos sem espaços ou traços, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` .|



