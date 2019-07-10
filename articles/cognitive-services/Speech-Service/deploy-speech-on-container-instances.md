---
title: Executar as instâncias de contentor do Azure
titleSuffix: Azure Cognitive Services
description: Implementar o contentor de serviço de voz para uma instância de contentor do Azure e testá-la num navegador da web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 062765be22135b12abb29ff6f7ce8a772c67adae
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711518"
---
# <a name="deploy-the-speech-service-container-to-azure-container-instances"></a>Implementar o contentor de serviço de voz no Azure Container Instances

Saiba como implementar os serviços cognitivos [serviço de voz](speech-container-howto.md) contentor para o Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/). Este procedimento demonstra a criação de um recurso de serviço de voz do Azure. Em seguida, vamos abordar extrair a imagem de contentor associado. Por fim, podemos destacar a capacidade de exercer a orquestração dos dois num browser. Através de contentores podem deslocar a atenção dos desenvolvedores evitar o gerenciamento de infra-estrutura para em vez disso, com foco no desenvolvimento de aplicativos.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>Pedir acesso ao registo de contentor

Primeiro tem de concluir e submeter o [formulário de pedido de contentores de voz dos serviços cognitivos](https://aka.ms/speechcontainerspreview/) para pedir acesso ao contentor. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Speech Service resource](includes/create-speech-resource.md)]

[!INCLUDE [Create an Speech Service container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
