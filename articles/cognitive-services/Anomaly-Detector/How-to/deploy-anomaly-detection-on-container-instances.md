---
title: Executar as instâncias de contentor do Azure
titleSuffix: Azure Cognitive Services
description: Implementar o contentor de detetor de anomalias para uma instância de contentor do Azure e testá-la num navegador da web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: b5adc3ed9234050d3977e812202717a0ce83e842
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711694"
---
# <a name="deploy-an-anomaly-detector-container-to-azure-container-instances"></a>Implementar um contentor de detetor de anomalias no Azure Container Instances

Saiba como implementar os serviços cognitivos [detetor de anomalias](../anomaly-detector-container-howto.md) contentor para o Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/). Este procedimento demonstra a criação de um recurso de detetor de anomalias. Em seguida, vamos abordar extrair a imagem de contentor associado. Por fim, podemos destacar a capacidade de exercer a orquestração dos dois num browser. Através de contentores podem deslocar a atenção dos desenvolvedores evitar o gerenciamento de infra-estrutura para em vez disso, com foco no desenvolvimento de aplicativos.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>Pedir acesso para o registo de contentor privado

Primeiro tem de concluir e submeter o [formulário de pedido do contentor de detetor de anomalias](https://aka.ms/adcontainer) para pedir acesso ao contentor.

[!INCLUDE [Request access](../../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

[!INCLUDE [Create an Anomaly Detector container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../../containers/includes/containers-next-steps.md)]