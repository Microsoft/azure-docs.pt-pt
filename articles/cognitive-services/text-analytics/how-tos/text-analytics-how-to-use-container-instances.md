---
title: Executar instâncias de contentores Azure - Análise de texto
titleSuffix: Azure Cognitive Services
description: Implemente os recipientes de análise de texto para a Instância do Recipiente Azure e teste-os num navegador web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: be43d04672dcefe368eb4052b4d1a929e25327ab
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94366864"
---
# <a name="deploy-a-text-analytics-container-to-azure-container-instances"></a>Implementar um recipiente de análise de texto para instâncias de contentores de Azure

Saiba como implantar o recipiente de [análise de texto][install-and-run-containers] dos serviços cognitivos para as [instâncias do contentor][container-instances]Azure . Este procedimento exemplifica a criação de um recurso Text Analytics, a criação de uma imagem de Análise de Sentimento associada e a capacidade de exercer esta orquestração dos dois a partir de um browser. A utilização de contentores pode desviar a atenção dos desenvolvedores da gestão das infraestruturas para, em vez disso, concentrarem-se no desenvolvimento de aplicações.

## <a name="prerequisites"></a>Pré-requisitos

* Utilize uma assinatura Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services) antes de começar.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](../../containers/includes/create-container-instances-resource.md)]

#### <a name="key-phrase-extraction"></a>[Extração de Expressões-Chave](#tab/keyphrase)

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detection"></a>[Deteção de Idioma](#tab/language)

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysis"></a>[Análise de Sentimentos](#tab/sentiment)

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

#### <a name="text-analytics-for-health"></a>[Análise de Texto para a saúde](#tab/health)

[!INCLUDE [Verify the health container instance](../includes/verify-health-container.md)]

***

## <a name="next-steps"></a>Passos seguintes 

* Use mais [recipientes de serviços cognitivos](../../cognitive-services-container-support.md)
* Utilize o [serviço ligado à análise de texto](../index.yml)

[install-and-run-containers]: ./text-analytics-how-to-install-containers.md
[container-instances]: https://docs.microsoft.com/azure/container-instances