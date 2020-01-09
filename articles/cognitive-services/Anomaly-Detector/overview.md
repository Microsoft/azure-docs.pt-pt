---
title: O que é a API do Detetor de Anomalias?
titleSuffix: Azure Cognitive Services
description: Use os algoritmos avançados da API do detector de anomalias para identificar anomalias nos dados de série temporal.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: overview
ms.date: 12/18/2019
ms.author: aahi
ms.openlocfilehash: 05ed7903cafbb265f23ba74a24ee0498be0effe9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448976"
---
# <a name="what-is-the-anomaly-detector-api"></a>O que é a API do Detetor de Anomalias?

A API do detector de anomalias permite que você monitore e detecte anormalidades em seus dados de série temporal com o aprendizado de máquina. A API do detector de anomalias se adapta identificando e aplicando automaticamente os modelos de melhor ajuste aos seus dados, independentemente do setor, cenário ou volume de dados. Usando seus dados de série temporal, a API determina os limites para detecção de anomalias, valores esperados e quais pontos de dados são anomalias.

![Detectar alterações de padrão em solicitações de serviço](./media/anomaly_detection2.png)

O uso do detector de anomalias não exige nenhuma experiência anterior no aprendizado de máquina, e a API RESTful permite que você integre facilmente o serviço em seus aplicativos e processos.

## <a name="features"></a>Funcionalidades

Com o detector de anomalias, você pode detectar automaticamente anomalias em seus dados de série temporal ou conforme elas ocorrem em tempo real. 

|Funcionalidade  |Descrição  |
|---------|---------|
|Detectar anomalias conforme elas ocorrem em tempo real. | Detectar anomalias em seus dados de streaming usando pontos de dados vistos anteriormente para determinar se o mais recente é uma anomalia. Essa operação gera um modelo usando os pontos de dados que você envia e determina se o ponto de destino é uma anomalia. Ao chamar a API com cada novo ponto de dados gerado, você pode monitorar seus dados conforme eles são criados. |
|Detectar anomalias em todo o conjunto de dados como um lote. | Use sua série temporal para detectar quaisquer anomalias que possam existir em seus dados. Esta operação gera um modelo usando seus dados de série temporal inteiros, com cada ponto analisado com o mesmo modelo.         |
| Obtenha informações adicionais sobre seus dados. | Obtenha detalhes úteis sobre seus dados e quaisquer anomalias observadas, incluindo valores esperados, limites e posições de anomalias. |
| Ajuste os limites de detecção de anomalias. | A API do detector de anomalias cria automaticamente limites para detecção de anomalias. Ajuste esses limites para aumentar ou diminuir a sensibilidade da API para anomalias de dados e ajustar melhor seus dados. |

## <a name="demo"></a>Demonstração

Confira esta [demonstração interativa](https://aka.ms/adDemo) para entender como funciona o detector de anomalias.
Para executar a demonstração, você precisa criar um recurso de detector de anomalias e obter a chave de API e o ponto de extremidade.

## <a name="notebook"></a>Bloco de Notas

Para saber como chamar a API do detector de anomalias, experimente este [bloco de anotações do Azure](https://aka.ms/adNotebook). Este Jupyter Notebook hospedado na Web mostra como enviar uma solicitação de API e visualizar o resultado.

Para executar o bloco de anotações, conclua as seguintes etapas:

1. Obtenha uma chave de assinatura da API do detector de anomalias válida e um ponto de extremidade de API. A seção a seguir apresenta instruções para se inscrever.
1. Entre e clique em clonar, no canto superior direito.
1. Desmarque a opção "público" na caixa de diálogo antes de concluir a operação de clonagem, caso contrário, o bloco de anotações, incluindo as chaves de assinatura, será público.
1. Clique em **executar em computação gratuita**
1. Selecione um dos notebooks.
1. Adicione a chave de assinatura API do detector de anomalias válida à variável `subscription_key`. 
1. Altere a variável `endpoint` para seu ponto de extremidade. Por exemplo: `https://westus2.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/last/detect`
1. Na barra de menus superior, clique em **célula**e em **executar tudo**.

## <a name="workflow"></a>Fluxo de Trabalho

A API do detector de anomalias é um serviço Web RESTful, facilitando a chamada de qualquer linguagem de programação que possa fazer solicitações HTTP e analisar o JSON.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

Após a inscrição:

1. Pegue seus dados de série temporal e converta-os em um formato JSON válido. Use [as práticas recomendadas](concepts/anomaly-detection-best-practices.md) ao preparar seus dados para obter os melhores resultados.
1. Envie uma solicitação para a API do detector de anomalias com seus dados.
1. Processe a resposta da API ao analisar a mensagem JSON devolvida.

## <a name="algorithms"></a>Algoritmos

* Consulte os seguintes Blogs técnicos para obter informações sobre os algoritmos usados:
    * [Apresentando a API do detector de anomalias do Azure](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Introducing-Azure-Anomaly-Detector-API/ba-p/490162)
    * [Visão geral do algoritmo SR-CNN no detector de anomalias do Azure](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Overview-of-SR-CNN-algorithm-in-Azure-Anomaly-Detector/ba-p/982798)

Você pode ler o [serviço de detecção de anomalias da série temporal da Microsoft](https://arxiv.org/abs/1906.03821) (aceito pelo KDD 2019) para saber mais sobre os algoritmos do Sr-CNN desenvolvidos pela Microsoft.


> [!VIDEO https://www.youtube.com/embed/ERTaAnwCarM]

## <a name="join-the-anomaly-detector-community"></a>Juntar-se à comunidade do Detetor de Anomalias

* Junte-se ao [grupo de consultores do detector de anomalias no Microsoft Teams](https://aka.ms/AdAdvisorsJoin)
* Consulte o [conteúdo gerado pelo usuário](user-generated-content.md) selecionado

## <a name="next-steps"></a>Passos seguintes

* [Início rápido: detectar anomalias nos dados de série temporal usando a API REST do detector de anomalias](quickstarts/detect-data-anomalies-csharp.md)
* A [demonstração online](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector) da API do detector de anomalias
* A [referência da API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect) do detector de anomalias
