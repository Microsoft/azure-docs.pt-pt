---
title: O que é a API do Detetor de Anomalias?
titleSuffix: Azure Cognitive Services
description: Utilize os algoritmos avançados da API do Detetor de Anomalias para identificar anomalias nos dados da série de tempo.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: overview
ms.date: 12/18/2019
ms.author: aahi
ms.openlocfilehash: 9237e670dd8d43c4036f996c477948944718e3aa
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80053700"
---
# <a name="what-is-the-anomaly-detector-api"></a>O que é a API do Detetor de Anomalias?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

A API do Detetor de Anomalias permite-lhe monitorizar e detetar anomalias nos dados da série temporal com machine learning. A API do Detetor de Anomalias adapta-se identificando e aplicando automaticamente os modelos mais adequados aos seus dados, independentemente da indústria, cenário ou volume de dados. Utilizando os dados da série de tempo, a API determina limites para a deteção de anomalias, valores esperados e quais os pontos de dados que são anomalias.

![Detetar alterações de padrão nos pedidos de serviço](./media/anomaly_detection2.png)

A utilização do Detetor de Anomalias não requer qualquer experiência prévia em machine learning, e a API RESTful permite-lhe integrar facilmente o serviço nas suas aplicações e processos.

## <a name="features"></a>Funcionalidades

Com o Detetor de Anomalias, pode detetar automaticamente anomalias ao longo dos dados da série de tempo, ou como ocorrem em tempo real.

|Funcionalidade  |Descrição  |
|---------|---------|
|Detete anomalias como ocorrem em tempo real. | Detete anomalias nos seus dados de streaming utilizando pontos de dados previamente vistos para determinar se o mais recente é uma anomalia. Esta operação gera um modelo utilizando os pontos de dados que envia e determina se o ponto alvo é uma anomalia. Ao ligar para a API com cada novo ponto de dados que gera, pode monitorizar os seus dados à medida que são criados. |
|Detete anomalias em todo o seu conjunto de dados como um lote. | Utilize a sua série de horários para detetar quaisquer anomalias que possam existir ao longo dos seus dados. Esta operação gera um modelo utilizando todos os dados da série time, com cada ponto analisado com o mesmo modelo.         |
| Obtenha informações adicionais sobre os seus dados. | Obtenha detalhes úteis sobre os seus dados e quaisquer anomalias observadas, incluindo valores esperados, limites de anomalias e posições. |
| Ajuste os limites de deteção de anomalias. | O Detetor de Anomalias API cria automaticamente limites para a deteção de anomalias. Ajuste estes limites para aumentar ou diminuir a sensibilidade da API às anomalias de dados e ajuste melhor aos seus dados. |

## <a name="demo"></a>Demonstração

Confira esta [demonstração interativa](https://aka.ms/adDemo) para entender como funciona o Detetor de Anomalias.
Para executar a demonstração, você precisa criar um recurso do Detetor de Anomalias e obter a chave API e ponto final.

## <a name="notebook"></a>Bloco de Notas

Para aprender a chamar a API do Detetor de Anomalias, experimente este [Caderno Azure](https://aka.ms/adNotebook). Este Caderno Jupyter hospedado na web mostra-lhe como enviar um pedido de API e visualizar o resultado.

Para executar o Caderno, complete os seguintes passos:

1. Obtenha uma chave de assinatura API do Detetor de Anomalia seletiva válida e um ponto final da API. A secção abaixo tem instruções para se inscrever.
1. Inscreva-se e clique em Clone, no canto superior direito.
1. Desverifique a opção "pública" na caixa de diálogo antes de concluir a operação do clone, caso contrário o seu caderno, incluindo quaisquer chaves de subscrição, será público.
1. Clique **em Executar em computação gratuita**
1. Selecione um dos cadernos.
1. Adicione a chave de subscrição `subscription_key` api do Detetor de Anomalia válida à variável.
1. Mude `endpoint` a variável para o seu ponto final. Por exemplo: `https://westus2.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/last/detect`
1. Na barra de menu superior, clique em **Cell,** em **seguida, Executar Tudo**.

## <a name="workflow"></a>Fluxo de trabalho

O API do Detetor de Anomalias é um serviço web RESTful, facilitando a chamada de qualquer linguagem de programação que possa fazer pedidos HTTP e analisar jSON.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

Depois de se inscrever:

1. Pegue nos dados da série de tempo e converta-os num formato JSON válido. Utilize [as melhores práticas](concepts/anomaly-detection-best-practices.md) ao preparar os seus dados para obter os melhores resultados.
1. Envie um pedido para a API do Detetor de Anomalias com os seus dados.
1. Processe a resposta da API ao analisar a mensagem JSON devolvida.

## <a name="algorithms"></a>Algoritmos

* Consulte os seguintes blogs técnicos para obter informações sobre os algoritmos utilizados:
    * [Introdução à API do Detetor de Anomalias do Azure](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Introducing-Azure-Anomaly-Detector-API/ba-p/490162)
    * [Visão geral do algoritmo SR-CNN no Detetor de Anomalias Azure](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Overview-of-SR-CNN-algorithm-in-Azure-Anomaly-Detector/ba-p/982798)

Pode ler o serviço de [deteção de anomalias](https://arxiv.org/abs/1906.03821) da Série Time na Microsoft (aceite pelo KDD 2019) para saber mais sobre os algoritmos SR-CNN desenvolvidos pela Microsoft.


> [!VIDEO https://www.youtube.com/embed/ERTaAnwCarM]

## <a name="join-the-anomaly-detector-community"></a>Juntar-se à comunidade do Detetor de Anomalias

* Junte-se ao grupo de consultores de detetores de [anomalias em Equipas microsoft](https://aka.ms/AdAdvisorsJoin)
* Ver [conteúdo gerado pelo utilizador](user-generated-content.md) selecionado

## <a name="next-steps"></a>Passos seguintes

* [Quickstart: Detete anomalias nos dados da série de tempo utilizando a API DO DETETOR de Anomalias](quickstarts/detect-data-anomalies-csharp.md)
* A [demonstração online](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector) do Detetor de Anomalias API
* A [referência da API](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect) do Detetor de Anomalias
