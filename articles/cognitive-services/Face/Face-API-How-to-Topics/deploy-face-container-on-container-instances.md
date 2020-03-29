---
title: Recipiente de rosto de corrida em instâncias de contentores azure
titleSuffix: Azure Cognitive Services
description: Desloque o recipiente Face para uma instância de contentores Azure e teste-o num navegador web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: 4f8a08697443ec6920b3fa73de19a950e54e7f40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "76716256"
---
# <a name="deploy-the-face-container-to-azure-container-instances"></a>Desloque o recipiente Face para as instâncias do contentor azure

Aprenda a implantar o recipiente [Face](../face-how-to-install-containers.md) dos Serviços Cognitivos para as [instâncias](https://docs.microsoft.com/azure/container-instances/)do contentor azure . Este procedimento demonstra a criação de um recurso Azure Face. Depois discutimos a imagem do contentor associado. Finalmente, destacamos a capacidade de exercer a orquestração dos dois a partir de um browser. A utilização de contentores pode desviar a atenção dos desenvolvedores da gestão da infraestrutura para, em vez disso, concentrar-se no desenvolvimento de aplicações.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>Solicitar acesso ao registo de contentores privados

[!INCLUDE [Request access to private container registry](../../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Create a Cognitive Services Face resource](../includes/create-face-resource.md)]

[!INCLUDE [Create an Face container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../../containers/includes/containers-next-steps.md)]