---
title: Serviço Run Azure Kubernetes - Text Analytics
titleSuffix: Azure Cognitive Services
description: Desloque a imagem do contentor Text Analytics para o Serviço Azure Kubernetes e teste-a num navegador web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 155f32ee76f69fe0f16e7698123381fdc12efd0e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80877853"
---
# <a name="deploy-a-text-analytics-container-to-azure-kubernetes-service"></a>Implante um recipiente de Análise de Texto para o Serviço Azure Kubernetes

Saiba como implementar a imagem do recipiente De Análise de [Texto](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) dos Serviços Cognitivos Azure para o Serviço Azure Kubernetes (AKS). Este procedimento mostra como criar um recurso Text Analytics, como criar uma imagem de análise de sentimento associada, e como exercitar esta orquestração dos dois a partir de um browser. A utilização de contentores pode desviar a sua atenção da gestão da infraestrutura para, em vez disso, concentrar-se no desenvolvimento de aplicações.

## <a name="prerequisites"></a>Pré-requisitos

Este procedimento requer várias ferramentas que devem ser instaladas e executadas localmente. Não use a Casca de Nuvem Azure. Precisa do seguinte:

* Uma subscrição do Azure. Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Um editor de texto, por exemplo, [Código de Estúdio Visual.](https://code.visualstudio.com/download)
* O [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) instalado.
* O [Kubernetes CLI](https://kubernetes.io/docs/tasks/tools/install-kubectl/) instalado.
* Um recurso Azure com o nível de preços correto. Nem todos os níveis de preços funcionam com este recipiente:
    * Recurso **Azure Text Analytics** apenas com níveis de preços F0 ou standard.
    * Recurso dos **Serviços Cognitivos Azure** com o nível de preços S0.

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

***

## <a name="next-steps"></a>Passos seguintes

* Utilize mais [recipientes de Serviços Cognitivos](../../cognitive-services-container-support.md)
* Utilize o [Serviço Conectado](../vs-text-connected-service.md) de Análise de Texto
