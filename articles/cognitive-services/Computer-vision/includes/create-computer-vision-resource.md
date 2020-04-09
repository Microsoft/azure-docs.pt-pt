---
title: Suporte de contentor
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 2647c604bebc2885f5c8516eb997792b65751e88
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876099"
---
## <a name="create-an-computer-vision-resource"></a>Criar um recurso de Visão Computacional

1. Assine no [portal Azure.](https://portal.azure.com)
1. Clique em criar recurso [ **de visão de computador.** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)
1. Introduza todas as definições necessárias:

    |Definição|Valor|
    |--|--|
    |Nome|Nome desejado (2-64 caracteres)|
    |Subscrição|Selecione subscrição apropriada|
    |Localização|Selecione qualquer local próximo e disponível|
    |Escalão de Preço|`F0`- o nível de preços mínimos|
    |Grupo de Recursos|Selecione um grupo de recursos disponíveis|

1. Clique em **Criar** e aguarde a criação do recurso. Depois de criado, navegue para a página de recursos.
1. Colete `{ENDPOINT_URI}` configurado `{API_KEY}`e, consulte a recolha de [parâmetros necessários](../computer-vision-how-to-install-containers.md#gathering-required-parameters) para obter detalhes.
