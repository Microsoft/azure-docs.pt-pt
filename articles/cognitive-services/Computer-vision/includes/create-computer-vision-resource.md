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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80876099"
---
## <a name="create-an-computer-vision-resource"></a>Criar um recurso de Visão De Computador

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Clique em Criar recurso [ **de visão** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision) de computador.
1. Introduza todas as definições necessárias:

    |Definição|Valor|
    |--|--|
    |Nome|Nome desejado (2-64 caracteres)|
    |Subscrição|Selecione subscrição apropriada|
    |Localização|Selecione qualquer localização próxima e disponível|
    |Escalão de Preço|`F0` - o nível mínimo de preços|
    |Grupo de Recursos|Selecione um grupo de recursos disponível|

1. Clique **em Criar** e aguarde a criação do recurso. Depois de criado, navegue para a página de recursos.
1. Recolha configurado `{ENDPOINT_URI}` `{API_KEY}` e, consulte [a recolha dos parâmetros necessários](../computer-vision-how-to-install-containers.md#gathering-required-parameters) para obter detalhes.
