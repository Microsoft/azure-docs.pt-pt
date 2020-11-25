---
title: Executar serviço Azure Kubernetes - Análise de Texto
titleSuffix: Azure Cognitive Services
description: Implemente a imagem do contentor Text Analytics para o Serviço Azure Kubernetes e teste-a num navegador web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 744be742d9f638df14f9d6720c9dad9678685af0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017892"
---
# <a name="deploy-a-text-analytics-container-to-azure-kubernetes-service"></a>Implementar um recipiente de análise de texto para o serviço Azure Kubernetes

Saiba como implantar a imagem do recipiente Azure Cognitive Services [Text Analytics](./text-analytics-how-to-install-containers.md) para o Serviço Azure Kubernetes (AKS). Este procedimento mostra como criar um recurso Text Analytics, como criar uma imagem de análise de sentimento associada, e como exercer esta orquestração dos dois a partir de um navegador. A utilização de contentores pode desviar a sua atenção da gestão das infraestruturas para, em vez disso, concentrar-se no desenvolvimento de aplicações.

## <a name="prerequisites"></a>Pré-requisitos

Este procedimento requer várias ferramentas que devem ser instaladas e executadas localmente. Não use a Azure Cloud Shell. Precisa do seguinte:

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services) antes de começar.
* Um editor de texto, por exemplo, [Código de Estúdio Visual.](https://code.visualstudio.com/download)
* O [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) instalado.
* O [CLI de Kubernetes](https://kubernetes.io/docs/tasks/tools/install-kubectl/) instalado.
* Um recurso Azure com o nível de preços correto. Nem todos os níveis de preços funcionam com este contentor:
    * **Recurso Azure Text Analytics** apenas com níveis de preços F0 ou standard.
    * **Recurso Azure Cognitive Services** com o nível de preços S0.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics container on Azure Kubernetes Service (AKS)](../../containers/includes/create-aks-resource.md)]

#### <a name="key-phrase-extraction"></a>[Extração de Expressões-Chave](#tab/keyphrase)

[!INCLUDE [Key Phrase Extraction Kubernetes config and deploy steps](../includes/key-phrase-extraction-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detection"></a>[Deteção de Idioma](#tab/language)

[!INCLUDE [Language Detection Kubernetes config and deploy steps](../includes/language-detection-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysis"></a>[Análise de Sentimentos](#tab/sentiment)

[!INCLUDE [Sentiment Analysis Kubernetes config and deploy steps](../includes/sentiment-analysis-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

**_

## <a name="next-steps"></a>Passos seguintes

_ Utilize mais [recipientes de Serviços Cognitivos](../../cognitive-services-container-support.md)
* Utilize o [serviço ligado à análise de texto](../index.yml)