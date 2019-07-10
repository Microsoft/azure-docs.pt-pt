---
title: Executar as instâncias de contentor do Azure
titleSuffix: Azure Cognitive Services
description: Implementar o contentor de imagem digitalizada para uma instância de contentor do Azure e testá-la num navegador da web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 859147d23ea78abac2da4a4c2f1fa26a8d976d02
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711617"
---
# <a name="deploy-the-computer-vision-container-to-azure-container-instances"></a>Implementar o contentor de imagem digitalizada no Azure Container Instances

Saiba como implementar os serviços cognitivos [de imagem digitalizada](computer-vision-how-to-install-containers.md) contentor para o Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/). Este procedimento demonstra a criação do recurso de imagem digitalizada. Em seguida, vamos abordar extrair a imagem de contentor associado. Por fim, podemos destacar a capacidade de exercer a orquestração dos dois num browser. Através de contentores podem deslocar a atenção dos desenvolvedores evitar o gerenciamento de infra-estrutura para em vez disso, com foco no desenvolvimento de aplicativos.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>Pedir acesso para o registo de contentor privado

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Create a Cognitive Services Computer Vision resource](includes/create-computer-vision-resource.md)]

[!INCLUDE [Create the Computer Vision on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]