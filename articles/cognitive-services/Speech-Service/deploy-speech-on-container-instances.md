---
title: Executar Instâncias de Contentores Azure - Serviço de Fala
titleSuffix: Azure Cognitive Services
description: Desloque o recipiente de serviço da Fala para uma instância de contentores Azure e teste-o num navegador web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: e5d9143b0c84b579945b283ccae1cce36a7a4291
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717404"
---
# <a name="deploy-the-speech-service-container-to-azure-container-instances"></a>Desloque o contentor de serviço da Fala para as instâncias de contentores de Azure

Saiba como implantar o recipiente de serviço de [fala](speech-container-howto.md) de serviços cognitivos para as instâncias de [contentores](https://docs.microsoft.com/azure/container-instances/)de Azure . Este procedimento demonstra a criação de um recurso de serviço Azure Speech. Em seguida, discutimos a extração da imagem de contêiner associada. Por fim, destacamos a capacidade de exercitar a orquestração dos dois em um navegador. O uso de contêineres pode mudar a atenção dos desenvolvedores para longe de gerenciar a infraestrutura para se concentrar no desenvolvimento de aplicativos.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>Solicitar acesso ao registro de contêiner

Primeiro deve completar e submeter o formulário de pedido de pedidos de pedidos de portadores de discurso de [serviços cognitivos](https://aka.ms/speechcontainerspreview/) para solicitar o acesso ao recipiente. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Speech service resource](includes/create-speech-resource.md)]

[!INCLUDE [Create a Speech service container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
