---
title: Suporte de contentor
titleSuffix: Azure Cognitive Services
description: Saiba como criar um recurso de análise de texto dos serviços cognitivos.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 022ffcd806d4d4f89f8a8cf256a541518ea12602
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455111"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Criar um recurso de análise de texto dos serviços cognitivos

1. Inicie sessão no [portal do Azure](https://portal.azure.com)
1. Selecione **+ criar um recurso**, navegue até à **IA + Machine Learning > análise de texto**, ou clique em [criar **de análise de texto**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)
1. Introduza todas as definições necessárias:

    |Definição|Valor|
    |--|--|
    |Name|Nome pretendido (2 e 64 carateres)|
    |Subscrição|Selecione a subscrição adequada|
    |Location|Selecione uma localização próximas|
    |Escalão de Preço|`S` -o escalão de preço standard|
    |Grupo de Recursos|Selecione um grupo de recursos disponíveis|

1. Clique em **criar** e espere até que o recurso a ser criada. Depois de criado, o seu browser redireciona automaticamente para a página de recursos recém-criado
1. Recolher configurado `endpoint` e uma chave de API:

    |Guia de recursos no Portal|Definição|Value|
    |--|--|--|
    |**Descrição Geral**|Ponto Final|Copie o ponto final. Que é semelhante a `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`|
    |**chaves**|Chave de API|Copie a 1 de duas chaves. É uma cadeia de caracteres alfanuméricos 32 sem espaços ou traços, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|