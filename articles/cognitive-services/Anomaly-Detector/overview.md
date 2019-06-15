---
title: O que é a API do Detetor de Anomalias? | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: Utilize algoritmos avançados da API de detetor de anomalias para identificar anomalias nos seus dados de séries de tempo.
services: cognitive-services
author: aahill
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 48dffb8853d3def590e434c1683ecf038e2a194e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64415803"
---
# <a name="what-is-the-anomaly-detector-api"></a>O que é a API do Detetor de Anomalias?

A API de detetor de anomalias permite-lhe monitorizar e detetar anomalias nos seus dados de séries de tempo com o machine learning. A API de detetor de anomalias adapta-se ao automaticamente identificar e aplicar os modelos de oferta fitting aos seus dados, independentemente da indústria, o cenário ou o volume de dados. Utilizar os seus dados de séries de tempo, a API determina os limites para deteção de anomalias, valores esperados, e quais os pontos de dados são anomalias.

![Detetar alterações padrão em pedidos de serviço](./media/anomaly_detection2.png)

Usar o detetor de anomalias não requer qualquer experiência anterior na aprendizagem automática e a API RESTful permite-lhe integrar facilmente o serviço em seus aplicativos e processos.

## <a name="features"></a>Funcionalidades

Com o detetor de anomalias, pode detetar automaticamente anomalias ao longo de seus dados de séries de tempo, ou à medida que ocorrem em tempo real. 

|Funcionalidade  |Descrição  |
|---------|---------|
|Detete anomalias, à medida que ocorrem em tempo real. | Detete anomalias nos seus dados de transmissão em fluxo com pontos de dados de vista anteriormente para determinar se sua mais recente é uma anomalia. Esta operação gera um modelo com os pontos de dados, enviar e determina se o ponto de destino é uma anomalia. Ao chamar a API com cada novo ponto de dados, gerar, pode monitorar seus dados, já que é criado. |
|Detete anomalias em todo o seu conjunto de dados como um lote. | Utilize a sua série de tempo para detetar quaisquer anomalias que possam existir ao longo de seus dados. Esta operação gera um modelo com os seus dados de séries de tempo todo, com cada ponto de analisado com o mesmo modelo.         |
| Obter informações adicionais sobre os seus dados. | Obtenha detalhes úteis sobre os seus dados e quaisquer anomalias observadas, incluindo os valores esperados, limites de anomalias e posições. |
| Ajuste os limites de deteção de anomalias. | A API de detetor de anomalias automática cria limites para deteção de anomalias. Ajustar estes limites para aumentar ou diminuir a sensibilidade a API para anomalias de dados e, se ajustar melhor aos seus dados. |

## <a name="demo"></a>Demonstração

Para começar rapidamente a utilizar a API de detetor de anomalias, experimente um [demonstração online](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector) que pode ser executado no seu browser. Esta demonstração é executado num bloco de notas do Jupyter hospedado na web e mostra-lhe como enviar um pedido de API e visualizar o resultado.

Para executar a demonstração, conclua os seguintes passos:

1. Obtenha uma chave de assinatura de API de detetor de anomalias válida e um ponto final da API. A secção abaixo tem instruções para inscrever-se. 
2. Inicie sessão e clique em Clone, no canto superior direito.
3. Clique em **executar no cálculo gratuita**
4. Selecione um dos blocos de notas para este exemplo.
5. Adicionar a chave de subscrição de API de detetor de anomalias válida para o `subscription_key` variável. Alterar o `endpoint` variável para o ponto final. Por exemplo: `https://westus2.api.cognitive.microsoft.com`
1. Na barra de menus superior, clique em **célula**, em seguida, **executar todos**.

## <a name="workflow"></a>Fluxo de trabalho

A API de detetor de anomalias é um serviço RESTful web, facilitando a chamar a partir de qualquer linguagem de programação que pode fazer solicitações HTTP e analisar JSON.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

Depois de se inscrever:

1. Leve os seus dados de séries de tempo e convertê-lo num formato JSON válido. Uso [melhores práticas](concepts/anomaly-detection-best-practices.md) ao se preparar seus dados para obter os melhores resultados.
1. Envie um pedido para a API de detetor de anomalias com os seus dados.
1. Processe a resposta da API ao analisar a mensagem JSON devolvida.

## <a name="next-steps"></a>Passos Seguintes

* [Quickstart: Detetar anomalias nos seus dados de séries de tempo com a API de REST de detetor de anomalias](quickstarts/detect-data-anomalies-csharp.md)
* A API de detetor de anomalias [demonstração online](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector)
* O detetor de anomalias [referência da REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)