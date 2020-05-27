---
title: Integrar o Azure Stream Analytics com o Azure Machine Learning
description: Este artigo descreve como integrar um trabalho de Azure Stream Analytics com modelos de Machine Learning Azure.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: c232ab06d2b3a28dad7ae98a8f22f457778fd3e6
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83874073"
---
# <a name="integrate-azure-stream-analytics-with-azure-machine-learning-preview"></a>Integrar o Azure Stream Analytics com o Azure Machine Learning (Pré-visualização)

Pode implementar modelos de machine learning como uma função definida pelo utilizador (UDF) nos seus trabalhos de Azure Stream Analytics para fazer pontuações em tempo real e previsões nos seus dados de entrada de streaming. [O Azure Machine Learning](../machine-learning/overview-what-is-azure-ml.md) permite-lhe utilizar qualquer ferramenta popular de código aberto, como tensorflow, scikit-learn, ou PyTorch, para preparar, treinar e implementar modelos.

## <a name="prerequisites"></a>Pré-requisitos

Complete os seguintes passos antes de adicionar um modelo de aprendizagem automática em função do seu trabalho de Stream Analytics:

1. Utilize o Azure Machine Learning para [implementar o seu modelo como um serviço web.](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where)

2. O seu script de pontuação deve ter [inputs e saídas](../machine-learning/how-to-deploy-and-where.md#example-entry-script) de amostra que são usados pela Azure Machine Learning para gerar uma especificação de esquema. O Stream Analytics utiliza o esquema para compreender a assinatura de função do seu serviço web.

3. Certifique-se de que o seu serviço web aceita e devolve dados serializados da JSON.

4. Implemente o seu modelo no [Serviço Azure Kubernetes](../machine-learning/how-to-deploy-and-where.md#choose-a-compute-target) para implementações de produção em larga escala. Se o serviço web não conseguir lidar com o número de pedidos provenientes do seu trabalho, o desempenho do seu trabalho em Stream Analytics será degradado, o que afeta a latência. Os modelos implantados em Instâncias de Contentores Azure só são suportados quando utiliza o portal Azure.

## <a name="add-a-machine-learning-model-to-your-job"></a>Adicione um modelo de aprendizagem automática ao seu trabalho

Pode adicionar funções de Machine Learning Azure ao seu trabalho de Stream Analytics diretamente do portal Azure.

1. Navegue para o seu trabalho de Stream Analytics no portal Azure e selecione **Funções** sob **topologia de trabalho.** Em seguida, selecione **Serviço Azure ML** a partir do menu **+ Adicionar** dropdown.

   ![Adicionar UDF Azure ML](./media/machine-learning-udf/add-azureml-udf.png)

2. Preencha o formulário de **função azure machine learning service** com os seguintes valores de propriedade:

   ![Configure Azure ML UDF](./media/machine-learning-udf/configure-azureml-udf.png)

A tabela seguinte descreve cada propriedade das funções de Serviço Azure ML no Stream Analytics.

|Propriedade|Descrição|
|--------|-----------|
|Pseudónimo de função|Insira um nome para invocar a função na sua consulta.|
|Subscrição|A sua assinatura Azure.|
|Espaço de trabalho Azure ML|O espaço de trabalho Azure Machine Learning que usou para implementar o seu modelo como serviço web.|
|Implementações|O serviço web que acolhe o seu modelo.|
|Assinatura de função|A assinatura do seu serviço web inferida a partir da especificação de esquema da API. Se a sua assinatura não carregar, verifique se forneceu a entrada e saída da amostra no seu script de pontuação para gerar automaticamente o esquema.|
|Número de pedidos paralelos por partição|Esta é uma configuração avançada para otimizar a entrada em alta escala. Este número representa os pedidos simultâneos enviados de cada partição do seu trabalho para o serviço web. Os empregos com seis unidades de streaming (SU) e inferiores têm uma divisória. Os empregos com 12 US têm duas divisórias, 18 US têm três divisórias e assim por diante.<br><br> Por exemplo, se o seu trabalho tiver duas divisórias e definir este parâmetro para quatro, haverá oito pedidos simultâneos do seu trabalho para o seu serviço web. Neste momento de pré-visualização pública, este valor não chega a 20 e não pode ser atualizado.|
|Contagem máxima de lote|Esta é uma configuração avançada para otimizar a entrada em alta escala. Este número representa o número máximo de eventos que são loteados num único pedido enviado ao seu serviço web.|

## <a name="supported-input-parameters"></a>Parâmetros de entrada suportados

Quando a sua consulta stream analytics invoca uma UDF de Aprendizagem automática Azure, o trabalho cria um pedido serializado JSON para o serviço web. O pedido baseia-se num esquema específico do modelo. Tem de fornecer uma entrada e saída de amostra no seu script de pontuação para [gerar automaticamente um esquema](../machine-learning/how-to-deploy-and-where.md). O esquema permite que o Stream Analytics construa o pedido serializado da JSON para qualquer um dos tipos de dados suportados, tais como dorpy, pandas e PySpark. Vários eventos de entrada podem ser loteados num único pedido.

A seguinte consulta stream analytics é um exemplo de como invocar uma UDF de Aprendizagem Automática Azure:

```SQL
SELECT udf.score(<model-specific-data-structure>)
INTO output
FROM input
```

Stream Analytics apenas suporta passar um parâmetro para funções de Aprendizagem automática Azure. Pode ser necessário preparar os seus dados antes de os passar como uma entrada para a UDF de aprendizagem automática.

## <a name="pass-multiple-input-parameters-to-the-udf"></a>Passe vários parâmetros de entrada para a UDF

Exemplos mais comuns de inputs para modelos de aprendizagem automática são matrizes dormentes e DataFrames. Pode criar uma matriz utilizando uma UDF JavaScript e criar um DataFrame baseado em JSON utilizando a `WITH` cláusula.

### <a name="create-an-input-array"></a>Criar um conjunto de entrada

Pode criar uma UDF JavaScript que aceita o número *N* de entradas e cria uma matriz que pode ser usada como entrada para a sua UDF de Aprendizagem automática Azure.

```javascript
function createArray(vendorid, weekday, pickuphour, passenger, distance) {
    'use strict';
    var array = [vendorid, weekday, pickuphour, passenger, distance]
    return array;
}
```

Uma vez adicionado o JavaScript UDF ao seu trabalho, pode invocar a sua UDF de Aprendizagem automática Azure utilizando a seguinte consulta:

```SQL
SELECT udf.score(
udf.createArray(vendorid, weekday, pickuphour, passenger, distance)
)
INTO output
FROM input
```

O seguinte JSON é um pedido de exemplo:

```JSON
{
    "data": [
        ["1","Mon","12","1","5.8"],
        ["2","Wed","10","2","10"]
    ]
}
```

### <a name="create-a-pandas-or-pyspark-dataframe"></a>Criar um Pandas ou PySpark DataFrame

Pode utilizar a `WITH` cláusula para criar um DataFrame serializado JSON que pode ser passado como entrada para a uDF de aprendizagem automática Azure, como mostrado abaixo.

A seguinte consulta cria um DataFrame selecionando os campos necessários e utiliza o DataFrame como entrada para a UDF de Aprendizagem automática Azure.

```SQL
WITH 
Dataframe AS (
SELECT vendorid, weekday, pickuphour, passenger, distance
FROM input
)

SELECT udf.score(Dataframe)
INTO output
FROM input
```

O seguinte JSON é um pedido de exemplo da consulta anterior:

```JSON
{
    "data": [{
            "vendorid": "1",
            "weekday": "Mon",
            "pickuphour": "12",
            "passenger": "1",
            "distance": "5.8"
        }, {
            "vendorid": "2",
            "weekday": "Tue",
            "pickuphour": "10",
            "passenger": "2",
            "distance": "10"
        }
    ]
}
```

## <a name="optimize-the-performance-for-azure-machine-learning-udfs"></a>Otimize o desempenho dos UDFs de Aprendizagem automática Azure

Quando implementar o seu modelo para o Serviço Azure Kubernetes, pode [perfilar o seu modelo para determinar a utilização de recursos](../machine-learning/how-to-deploy-and-where.md#profilemodel). Também pode permitir que as [Informações da App para as suas implementações](../machine-learning/how-to-enable-app-insights.md) compreendam as taxas de pedido, os tempos de resposta e as taxas de falha.

Se tiver um cenário com alta produção de eventos, poderá ter de alterar os seguintes parâmetros no Stream Analytics para obter um desempenho ideal com tardios de ponta a ponta:

1. Contagem máxima de lote.
2. Número de pedidos paralelos por partição.

### <a name="determine-the-right-batch-size"></a>Determine o tamanho certo do lote

Depois de ter implantado o seu serviço web, envia um pedido de amostra com tamanhos de lote variados a partir de 50 e aumentando-o por ordem de centenas. Por exemplo, 200, 500, 1000, 2000 e assim por diante. Notará que depois de um certo tamanho de lote, a latência da resposta aumenta. O ponto após o qual a latência de resposta aumenta deve ser a contagem máxima de lote para o seu trabalho.

### <a name="determine-the-number-of-parallel-requests-per-partition"></a>Determinar o número de pedidos paralelos por partição

Na escala ideal, o seu trabalho de Stream Analytics deverá ser capaz de enviar múltiplos pedidos paralelos para o seu serviço web e obter uma resposta dentro de poucos milissegundos. A latência da resposta do serviço web pode afetar diretamente a latência e desempenho do seu trabalho de Stream Analytics. Se a chamada do seu trabalho para o serviço web demorar muito tempo, provavelmente verá um aumento no atraso da marca de água e poderá também ver um aumento no número de eventos de entrada em atraso.

Para evitar tal latência, certifique-se de que o seu cluster azure Kubernetes Service (AKS) foi aprovisionado com o [número certo de nós e réplicas](../machine-learning/how-to-deploy-azure-kubernetes-service.md#using-the-cli). É fundamental que o seu serviço web esteja altamente disponível e retorne respostas bem sucedidas. Se o seu trabalho receber uma resposta indisponível de serviço (503) do seu serviço web, irá continuamente retentar com recuo exponencial. Qualquer resposta que não seja o sucesso (200) e o serviço indisponível (503) fará com que o seu trabalho vá para um estado falhado.

## <a name="next-steps"></a>Passos seguintes

* [Tutorial: funções definidas pelo utilizador do JavaScript do Azure Stream Analytics](stream-analytics-javascript-user-defined-functions.md)
* [Escala o seu trabalho de Stream Analytics com a função Azure Machine Learning Studio (clássico)](stream-analytics-scale-with-machine-learning-functions.md)

