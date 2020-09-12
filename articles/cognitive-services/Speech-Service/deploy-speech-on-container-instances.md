---
title: Executar Instâncias de Contentores Azure - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Desloque o recipiente de serviço de voz para uma instância de recipiente Azure e teste-o num navegador web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/03/2020
ms.author: aahi
ms.openlocfilehash: 5a85086ea8d969dc70f4cd24b25a00765da285eb
ms.sourcegitcommit: ac5cbef0706d9910a76e4c0841fdac3ef8ed2e82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/03/2020
ms.locfileid: "89425811"
---
# <a name="deploy-the-speech-service-container-to-azure-container-instances"></a>Desdobre o recipiente de serviço de fala para instâncias de contentores Azure

Saiba como implantar o recipiente de [serviço de fala](speech-container-howto.md) de serviços cognitivos para [instâncias de contentores Azure](https://docs.microsoft.com/azure/container-instances/). Este procedimento demonstra a criação de um recurso de serviço de discurso Azure. Depois discutimos a retirada da imagem do contentor associado. Finalmente, destacamos a capacidade de exercer a orquestração dos dois a partir de um browser. A utilização de contentores pode desviar a atenção dos desenvolvedores da gestão das infraestruturas para, em vez disso, concentrarem-se no desenvolvimento de aplicações.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>Solicitar acesso ao registo de contentores

Primeiro deve completar e submeter o formulário de Pedido de Pedido de Recipientes de [Discurso dos Serviços Cognitivos](https://aka.ms/csgate/) para solicitar o acesso ao contentor. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Speech service resource](includes/create-speech-resource.md)]

[!INCLUDE [Create a Speech service container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
