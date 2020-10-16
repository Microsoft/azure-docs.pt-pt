---
title: Executar recipiente de rosto em instâncias de contentores Azure
titleSuffix: Azure Cognitive Services
description: Desloque o recipiente Face para uma Instância de Recipiente Azure e teste-o num navegador web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: aahi
ms.openlocfilehash: 5fbe316580cfa2ca280a2587536df037146e8d02
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86538127"
---
# <a name="deploy-the-face-container-to-azure-container-instances"></a>Colocar o contentor Face para instâncias de contentores Azure

> [!IMPORTANT]
> Foi atingido o limite de utilizadores do contentor de Face. De momento, não estamos a aceitar novas aplicações para o contentor de Face.

Saiba como implantar o recipiente [de rosto](../face-how-to-install-containers.md) dos serviços cognitivos para as [instâncias do contentor Azure](https://docs.microsoft.com/azure/container-instances/). Este procedimento demonstra a criação de um recurso Azure Face. Depois discutimos a retirada da imagem do contentor associado. Finalmente, destacamos a capacidade de exercer a orquestração dos dois a partir de um browser. A utilização de contentores pode desviar a atenção dos desenvolvedores da gestão das infraestruturas para, em vez disso, concentrarem-se no desenvolvimento de aplicações.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

[!INCLUDE [Create a Cognitive Services Face resource](../includes/create-face-resource.md)]

[!INCLUDE [Create an Face container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../../containers/includes/containers-next-steps.md)]