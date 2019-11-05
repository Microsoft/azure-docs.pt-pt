---
title: Executar Pesquisa Visual Computacional contêiner em instâncias de contêiner do Azure
titleSuffix: Azure Cognitive Services
description: Implante o contêiner Pesquisa Visual Computacional em uma instância de contêiner do Azure e teste-o em um navegador da Web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 1c858432a3382e6dbc8e479aab11b18dc5eebfe4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499099"
---
# <a name="deploy-the-computer-vision-container-to-azure-container-instances"></a>Implantar o contêiner de Pesquisa Visual Computacional em instâncias de contêiner do Azure

Saiba como implantar os serviços cognitivas [Pesquisa Visual computacional](computer-vision-how-to-install-containers.md) contêiner nas [instâncias de contêiner](https://docs.microsoft.com/azure/container-instances/)do Azure. Este procedimento demonstra a criação do recurso de Pesquisa Visual Computacional. Em seguida, discutimos a extração da imagem de contêiner associada. Por fim, destacamos a capacidade de exercitar a orquestração dos dois em um navegador. O uso de contêineres pode mudar a atenção dos desenvolvedores para longe de gerenciar a infraestrutura para se concentrar no desenvolvimento de aplicativos.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>Solicitar acesso ao registro de contêiner privado

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Create a Cognitive Services Computer Vision resource](includes/create-computer-vision-resource.md)]

[!INCLUDE [Create the Computer Vision on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]