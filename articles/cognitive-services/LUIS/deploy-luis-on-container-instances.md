---
title: Executar instâncias de contêiner do Azure
titleSuffix: Azure Cognitive Services
description: Implante o contêiner LUIS em uma instância de contêiner do Azure e teste-o em um navegador da Web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 6a714178c9f8ec3f9239ba64bccbcf25b4aecf60
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73465919"
---
# <a name="deploy-the-language-understanding-luis-container-to-azure-container-instances"></a>Implantar o contêiner Reconhecimento vocal (LUIS) nas instâncias de contêiner do Azure

Saiba como implantar o contêiner [Luis](luis-container-howto.md) de serviços cognitivas nas [instâncias de contêiner](https://docs.microsoft.com/azure/container-instances/)do Azure. Este procedimento demonstra a criação de um recurso de detector de anomalias. Em seguida, discutimos a extração da imagem de contêiner associada. Por fim, destacamos a capacidade de exercitar a orquestração dos dois em um navegador. O uso de contêineres pode mudar a atenção dos desenvolvedores para longe de gerenciar a infraestrutura para se concentrar no desenvolvimento de aplicativos.

[!INCLUDE [Prerequisites](../containers/includes/container-prerequisites.md)]

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

[!INCLUDE [Create LUIS Container instance resource](../containers/includes/create-container-instances-resource.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Next steps](../containers/includes/containers-next-steps.md)]
