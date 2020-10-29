---
title: Executar o recipiente do reconhecimento do formulário em instâncias de contentores Azure
titleSuffix: Azure Cognitive Services
description: Implemente o recipiente Do Reconhecimento de Formulários para uma Instância de Recipiente Azure e teste-o num navegador web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: aahi
ms.openlocfilehash: fb837dfc72a148d20d382a5dc356c99306aa400f
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913181"
---
# <a name="deploy-the-form-recognizer-container-to-azure-container-instances"></a>Implementar o recipiente do Reconhecimento de Formulários para instâncias de contentores Azure

[!INCLUDE [Form Recognizer containers limit](includes/container-limit.md)]

Saiba como implantar o recipiente de reconhecimento de [formulários](form-recognizer-container-howto.md) de serviços cognitivos para [instâncias de contentores Azure](../../container-instances/index.yml). Este procedimento demonstra a criação de um recurso Azure Form Recogniser. Depois discutimos a retirada da imagem do contentor associado. Finalmente, destacamos a capacidade de exercer a orquestração dos dois a partir de um browser. A utilização de contentores pode desviar a atenção dos desenvolvedores da gestão das infraestruturas para, em vez disso, concentrarem-se no desenvolvimento de aplicações.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

[!INCLUDE [Create a Cognitive Services Form Recognizer resource](includes/create-resource.md)]

[!INCLUDE [Create an Form Recognizer container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]