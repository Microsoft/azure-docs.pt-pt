---
title: Executar recipiente de detetor de anomalias em instâncias de contentores azure
titleSuffix: Azure Cognitive Services
description: Desloque o recipiente do Detetor de Anomalias para uma instância de contentores Azure e teste-o num navegador web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 975efa9fdaff9175317794d8b6df7afbfc73cba8
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83585692"
---
# <a name="deploy-an-anomaly-detector-container-to-azure-container-instances"></a>Implante um recipiente de detetor de anomalias para instâncias de contentores de Azure

Aprenda a implantar o recipiente do Detetor de [Anomalias](../anomaly-detector-container-howto.md) dos Serviços Cognitivos para [as instâncias](https://docs.microsoft.com/azure/container-instances/)do contentor azure . Este procedimento demonstra a criação de um recurso do Detetor de Anomalias. Depois discutimos a imagem do contentor associado. Finalmente, destacamos a capacidade de exercer a orquestração dos dois a partir de um browser. A utilização de contentores pode desviar a atenção dos desenvolvedores da gestão da infraestrutura para, em vez disso, concentrar-se no desenvolvimento de aplicações.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

[!INCLUDE [Create an Anomaly Detector container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>Passos seguintes

* Reveja [Instalar e executar recipientes](../anomaly-detector-container-configuration.md) para puxar a imagem do recipiente e executar o recipiente
* Rever [Configure recipientes](../anomaly-detector-container-configuration.md) para configurações de configuração
* [Saiba mais sobre o serviço API do Detetor de Anomalias](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
