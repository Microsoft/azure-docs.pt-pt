---
title: Executar recipiente de detetor de anomalias em instâncias de contentores azure
titleSuffix: Azure Cognitive Services
description: Desloque o recipiente do Detetor de Anomalias para uma instância de contentores Azure e teste-o num navegador web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: 2fba0a0d64502a30b6dfbc9f4f109bca65cca8b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "76716363"
---
# <a name="deploy-an-anomaly-detector-container-to-azure-container-instances"></a>Implante um recipiente de detetor de anomalias para instâncias de contentores de Azure

Aprenda a implantar o recipiente do Detetor de [Anomalias](../anomaly-detector-container-howto.md) dos Serviços Cognitivos para [as instâncias](https://docs.microsoft.com/azure/container-instances/)do contentor azure . Este procedimento demonstra a criação de um recurso do Detetor de Anomalias. Depois discutimos a imagem do contentor associado. Finalmente, destacamos a capacidade de exercer a orquestração dos dois a partir de um browser. A utilização de contentores pode desviar a atenção dos desenvolvedores da gestão da infraestrutura para, em vez disso, concentrar-se no desenvolvimento de aplicações.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>Solicitar acesso ao registo de contentores privados

Primeiro deve completar e submeter o formulário de pedido do recipiente de detetor de [anomalias](https://aka.ms/adcontainer) para solicitar o acesso ao recipiente.

[!INCLUDE [Request access](../../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

[!INCLUDE [Create an Anomaly Detector container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>Passos seguintes

* Reveja [Instalar e executar recipientes](../anomaly-detector-container-configuration.md) para puxar a imagem do recipiente e executar o recipiente
* Rever [Configure recipientes](../anomaly-detector-container-configuration.md) para configurações de configuração
* [Saiba mais sobre o serviço API do Detetor de Anomalias](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
