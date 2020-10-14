---
title: Executar detetor de anomalias em instâncias de contentores de Azure
titleSuffix: Azure Cognitive Services
description: Desloque o recipiente do Detetor de Anomalias para uma Instância de Contentores Azure e teste-o num navegador web.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: mbullwin
ms.openlocfilehash: c7ce1985f4c7dcd14befce14abe5b913e2c9a67e
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92014750"
---
# <a name="deploy-an-anomaly-detector-container-to-azure-container-instances"></a>Implantar um detetor de anomalias em instâncias de contentores de Azure

Saiba como implantar o detetor [de anomalias de](../anomaly-detector-container-howto.md) serviços cognitivos para [instâncias de contentores Azure](https://docs.microsoft.com/azure/container-instances/). Este procedimento demonstra a criação de um recurso de detetor de anomalias. Depois discutimos a retirada da imagem do contentor associado. Finalmente, destacamos a capacidade de exercer a orquestração dos dois a partir de um browser. A utilização de contentores pode desviar a atenção dos desenvolvedores da gestão das infraestruturas para, em vez disso, concentrarem-se no desenvolvimento de aplicações.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

[!INCLUDE [Create an Anomaly Detector container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>Passos seguintes

* Rever [Instalar e executar recipientes](../anomaly-detector-container-configuration.md) para puxar a imagem do contentor e executar o recipiente
* [Rever recipientes de configuração](../anomaly-detector-container-configuration.md) para configurações de configuração
* [Saiba mais sobre o serviço API do Detetor de Anomalias](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
