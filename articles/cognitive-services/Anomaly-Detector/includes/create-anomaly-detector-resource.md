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
ms.openlocfilehash: d1add17c1c84d2a22d76aaa1f96aeca4db645ba7
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/15/2020
ms.locfileid: "97506303"
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
    |Escalão de Preço|`F0` - 100 chamadas por segundo, 20K Transações por mês. <br> Ou:<br> `S0` - 80 chamadas por segundo|
    |Grupo de Recursos|Selecione um grupo de recursos disponível|

1. Clique **em Criar** e aguarde a criação do recurso. Depois de criado, navegue para a página de recursos
1. Recolha configurado `endpoint` e uma chave API:

    |Teclas e separador Endpoint no portal|Definição|Valor|
    |--|--|--|
    |**Descrição geral**|Ponto final|Copie o ponto final. Parece-se com ` https://<your-resource-name>.cognitiveservices.azure.com/`|
    |**Chaves**|Chave de API|Copiar 1 das duas chaves. É uma corda de 32 caracteres alfanuméricos sem espaços ou traços, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` .|



