---
title: Executar instâncias de contentores Azure - Análise de Texto
titleSuffix: Azure Cognitive Services
description: Desloque os recipientes de análise de texto para a Instância de Contentores Azure e teste-os num navegador web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 7ae8fbd404c9c2b650e4eed30c219e8ffafe55f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74383539"
---
# <a name="deploy-a-text-analytics-container-to-azure-container-instances"></a>Implante um recipiente de Análise de Texto para instâncias de contentores de Azure

Saiba como implantar o recipiente de Análise de [Texto][install-and-run-containers] dos Serviços Cognitivos para [as Instâncias do Contentor][container-instances]De Azure . Este procedimento exemplifica a criação de um recurso Text Analytics, a criação de uma imagem associada de Análise de Sentimentos e a capacidade de exercer esta orquestração dos dois a partir de um browser. A utilização de contentores pode desviar a atenção dos desenvolvedores da gestão da infraestrutura para, em vez disso, concentrar-se no desenvolvimento de aplicações.

## <a name="prerequisites"></a>Pré-requisitos

* Utilize uma assinatura Azure. Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](../../containers/includes/create-container-instances-resource.md)]

#### <a name="key-phrase-extraction"></a>[Extração de Expressões-Chave](#tab/keyphrase)

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detection"></a>[Deteção de Idiomas](#tab/language)

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysis"></a>[Análise de Sentimentos](#tab/sentiment)

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

***

## <a name="next-steps"></a>Passos seguintes 

* Use mais [recipientes de serviços cognitivos](../../cognitive-services-container-support.md)
* Utilize o [Serviço Conectado](../vs-text-connected-service.md) de Análise de Texto

[install-and-run-containers]: ./text-analytics-how-to-install-containers.md
[container-instances]: https://docs.microsoft.com/azure/container-instances