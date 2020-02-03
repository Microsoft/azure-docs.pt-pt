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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76716363"
---
# <a name="deploy-an-anomaly-detector-container-to-azure-container-instances"></a>Implante um recipiente de detetor de anomalias para instâncias de contentores de Azure

Aprenda a implantar o recipiente do Detetor de [Anomalias](../anomaly-detector-container-howto.md) dos Serviços Cognitivos para [as instâncias](https://docs.microsoft.com/azure/container-instances/)do contentor azure . Este procedimento demonstra a criação de um recurso de detector de anomalias. Em seguida, discutimos a extração da imagem de contêiner associada. Por fim, destacamos a capacidade de exercitar a orquestração dos dois em um navegador. O uso de contêineres pode mudar a atenção dos desenvolvedores para longe de gerenciar a infraestrutura para se concentrar no desenvolvimento de aplicativos.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>Pedir acesso para o registo de contentor privado

Primeiro deve completar e submeter o formulário de pedido do recipiente de detetor de [anomalias](https://aka.ms/adcontainer) para solicitar o acesso ao recipiente.

[!INCLUDE [Request access](../../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

[!INCLUDE [Create an Anomaly Detector container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>Passos Seguintes

* Reveja [Instalar e executar recipientes](../anomaly-detector-container-configuration.md) para puxar a imagem do recipiente e executar o recipiente
* Rever [Configure recipientes](../anomaly-detector-container-configuration.md) para configurações de configuração
* [Saiba mais sobre o serviço API do Detetor de Anomalias](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
