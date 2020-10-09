---
title: Executar o recipiente de visão computacional em instâncias de contentores Azure
titleSuffix: Azure Cognitive Services
description: Implemente o recipiente de Visão de Computador para uma Instância de Contentores Azure e teste-o num navegador web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: ecb0b6a6577161a5349d9d701dd6c116f69411f0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80876100"
---
# <a name="deploy-the-computer-vision-container-to-azure-container-instances"></a>Implementar o recipiente de visão computacional para instâncias de contentores Azure

Saiba como implantar o recipiente de [visão computacional](computer-vision-how-to-install-containers.md) dos serviços cognitivos para [instâncias de contentores Azure](https://docs.microsoft.com/azure/container-instances/). Este procedimento demonstra a criação do recurso Visão Computacional. Depois discutimos a retirada da imagem do contentor associado. Finalmente, destacamos a capacidade de exercer a orquestração dos dois a partir de um browser. A utilização de contentores pode desviar a atenção dos desenvolvedores da gestão das infraestruturas para, em vez disso, concentrarem-se no desenvolvimento de aplicações.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>Solicitar acesso ao registo privado de contentores

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Create a Cognitive Services Computer Vision resource](includes/create-computer-vision-resource.md)]

[!INCLUDE [Create the Computer Vision on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]