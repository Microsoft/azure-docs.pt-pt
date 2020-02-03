---
title: Executar contêiner do reconhecedor de formulário em instâncias de contêiner do Azure
titleSuffix: Azure Cognitive Services
description: Implante o contêiner do reconhecedor de formulário em uma instância de contêiner do Azure e teste-o em um navegador da Web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: 5d9ab7d12bd6c5fe59bf521aff2c07446ac2f038
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717312"
---
# <a name="deploy-the-form-recognizer-container-to-azure-container-instances"></a>Implantar o contêiner do reconhecedor de formulário nas instâncias de contêiner do Azure

Saiba como implantar o recipiente de reconhecimento de [formulários](form-recognizer-container-howto.md) de serviços cognitivos para as [instâncias](https://docs.microsoft.com/azure/container-instances/)do contentor azure . Este procedimento demonstra a criação de um recurso do reconhecedor do Azure Form. Em seguida, discutimos a extração da imagem de contêiner associada. Por fim, destacamos a capacidade de exercitar a orquestração dos dois em um navegador. O uso de contêineres pode mudar a atenção dos desenvolvedores para longe de gerenciar a infraestrutura para se concentrar no desenvolvimento de aplicativos.

> [!IMPORTANT]
> No momento, os contêineres do reconhecedor de formulário usam a versão 1,0 da API do reconhecedor de formulário. Você pode acessar a versão mais recente da API usando o serviço gerenciado em vez disso.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>Solicitar acesso ao registro de contêiner

Primeiro deve completar e submeter o formulário de pedido de acesso aos recipientes de reconhecimento de formulários de [serviços cognitivos](https://aka.ms/FormRecognizerRequestAccess) para solicitar o acesso ao recipiente. Isso também se inscreve para Pesquisa Visual Computacional. Você não precisa se inscrever para o formulário de solicitação de Pesquisa Visual Computacional separadamente. 

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Form Recognizer resource](includes/create-resource.md)]

[!INCLUDE [Create an Form Recognizer container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
