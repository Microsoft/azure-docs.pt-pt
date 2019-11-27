---
title: Executar instâncias de contêiner do Azure-Análise de Texto
titleSuffix: Azure Cognitive Services
description: Implante os contêineres de análise de texto na instância de contêiner do Azure e teste-os em um navegador da Web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 7ae8fbd404c9c2b650e4eed30c219e8ffafe55f7
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383539"
---
# <a name="deploy-a-text-analytics-container-to-azure-container-instances"></a>Implantar um contêiner de Análise de Texto nas instâncias de contêiner do Azure

Saiba como implantar os serviços cognitivas [análise de texto][install-and-run-containers] contêiner nas [instâncias de contêiner][container-instances]do Azure. Esse procedimento exemplifica a criação de um recurso de Análise de Texto, a criação de uma imagem de Análise de Sentimento associada e a capacidade de exercitar essa orquestração dos dois em um navegador. O uso de contêineres pode mudar a atenção dos desenvolvedores para longe de gerenciar a infraestrutura para se concentrar no desenvolvimento de aplicativos.

## <a name="prerequisites"></a>Pré-requisitos

* Use uma assinatura do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](../../containers/includes/create-container-instances-resource.md)]

#### <a name="key-phrase-extractiontabkeyphrase"></a>[Extração de Frases-chave](#tab/keyphrase)

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detectiontablanguage"></a>[Detecção de Idioma](#tab/language)

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysistabsentiment"></a>[Análise de Sentimento](#tab/sentiment)

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

***

## <a name="next-steps"></a>Passos Seguintes 

* Usar mais [contêineres de serviços cognitivas](../../cognitive-services-container-support.md)
* Usar o [serviço conectado do análise de texto](../vs-text-connected-service.md)

[install-and-run-containers]: ./text-analytics-how-to-install-containers.md
[container-instances]: https://docs.microsoft.com/azure/container-instances