---
title: Executar as instâncias de contentor do Azure
titleSuffix: Text Analytics - Azure Cognitive Services
description: Implementar os contentores de análise de texto com a imagem de análise de sentimentos, a instância de contentor do Azure e testá-la num navegador da web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: dapine
ms.openlocfilehash: a82b5a1cbed662289d3a406a61fdd19a3ca8861b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67454985"
---
# <a name="deploy-a-sentiment-analysis-container-to-azure-container-instances-aci"></a>Implementar um contentor de análise de sentimentos para o Azure Container Instances (ACI)

Saiba como implementar os serviços cognitivos [análise de texto](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) contentor com a imagem de análise de sentimentos para o Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/) (ACI). Este procedimento exemplifica a criação de um recurso de análise de texto, a criação de uma imagem de análise de sentimentos associada e a capacidade de testar esta orquestração dos dois num browser. Através de contentores podem deslocar a atenção dos desenvolvedores evitar o gerenciamento de infra-estrutura para em vez disso, com foco no desenvolvimento de aplicativos.

## <a name="prerequisites"></a>Pré-requisitos

* Utilize uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances (ACI)](../../containers/includes/create-aci-resource.md)]

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

## <a name="next-steps"></a>Passos Seguintes 

* Utilizar mais [contentores de serviços cognitivos](../../cognitive-services-container-support.md)
* Utilize o [serviço ligado de análise de texto](../vs-text-connected-service.md)
