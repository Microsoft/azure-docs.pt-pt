---
title: Integre a Azure Stream Analytics com Azure Machine Learning
description: Este artigo descreve como integrar um trabalho Azure Stream Analytics com modelos Azure Machine Learning.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/21/2020
ms.custom: devx-track-js
ms.openlocfilehash: 01c85311c9ea49be3543edee405cdd66a0659797
ms.sourcegitcommit: a89a517622a3886b3a44ed42839d41a301c786e0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/22/2020
ms.locfileid: "97733010"
---
# <a name="integrate-azure-stream-analytics-with-azure-machine-learning-preview"></a>Integre a Azure Stream Analytics com Azure Machine Learning (Preview)

Pode implementar modelos de machine learning como uma função definida pelo utilizador (UDF) nos seus trabalhos Azure Stream Analytics para fazer pontuações em tempo real e previsões nos seus dados de entrada de streaming. [O Azure Machine Learning](../machine-learning/overview-what-is-azure-ml.md) permite-lhe utilizar qualquer ferramenta de código aberto popular, como tensorflow, scikit-learn, ou PyTorch, para preparar, treinar e implementar modelos.

## <a name="prerequisites"></a>Pré-requisitos

Complete os seguintes passos antes de adicionar um modelo de aprendizagem automática como função ao seu trabalho stream Analytics:

1. Utilize a Azure Machine Learning para [implementar o seu modelo como serviço web](../machine-learning/how-to-deploy-and-where.md).

2. O seu script de pontuação deve ter [entradas e saídas de amostra](../machine-learning/how-to-deploy-and-where.md) que são usadas pela Azure Machine Learning para gerar uma especificação de esquema. Stream Analytics utiliza o esquema para entender a assinatura de função do seu serviço web. Pode utilizar esta [definição de swagger de amostra](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/AzureML/swagger-example.json) como referência para garantir que a definiu corretamente.

3. Certifique-se de que o seu serviço web aceita e devolve dados serializados da JSON.

4. Implemente o seu modelo no [Serviço Azure Kubernetes](../machine-learning/how-to-deploy-and-where.md#choose-a-compute-target) para implantações de produção de alta escala. Se o serviço web não for capaz de lidar com o número de pedidos provenientes do seu trabalho, o desempenho do seu trabalho stream Analytics será degradado, o que tem impacto na latência. Os modelos implantados em Instâncias de Contentores Azure só são suportados quando utilizar o portal Azure. Os modelos construídos com [o Azure Machine Learning Designer](../machine-learning/concept-designer.md) ainda não são suportados no Stream Analytics.

## <a name="add-a-machine-learning-model-to-your-job"></a>Adicione um modelo de aprendizagem automática ao seu trabalho

Pode adicionar funções de Aprendizagem automática Azure ao seu trabalho stream Analytics diretamente do portal Azure ou Código do Estúdio Visual.

### <a name="azure-portal"></a>Portal do Azure

1. Navegue para o seu trabalho stream Analytics no portal Azure e selecione **Funções** em **topologia de Job**. Em seguida, selecione O Serviço de Aprendizagem automática **Azure** a partir do menu **+ Adicionar** o menu de dropdown.

   ![Adicionar Azure Machine Learning UDF](./media/machine-learning-udf/add-azure-machine-learning-udf.png)

2. Preencha o formulário **de função Azure Machine Learning Service** com os seguintes valores de propriedade:

   ![Configurar Azure Machine Learning UDF](./media/machine-learning-udf/configure-azure-machine-learning-udf.png)

### <a name="visual-studio-code"></a>Visual Studio Code

1. Abra o seu projeto Stream Analytics no Código do Estúdio Visual e clique com o botão direito na pasta **Funções.** Em seguida, escolha **Adicionar Função**. Selecione **Machine Learning UDF** da lista de dropdown.

   :::image type="content" source="media/machine-learning-udf/visual-studio-code-machine-learning-udf-add-function.png" alt-text="Adicionar UDF no Código VS":::

   :::image type="content" source="media/machine-learning-udf/visual-studio-code-machine-learning-udf-add-function-2.png" alt-text="Adicione Azure Machine Learning UDF em código VS":::

2. Introduza o nome da função e preencha as definições no ficheiro de configuração utilizando **Selecione das suas subscrições** em CodeLens.

   :::image type="content" source="media/machine-learning-udf/visual-studio-code-machine-learning-udf-function-name.png" alt-text="Selecione Azure Machine Learning UDF em código VS":::

   :::image type="content" source="media/machine-learning-udf/visual-studio-code-machine-learning-udf-configure-settings.png" alt-text="Configurar Azure Machine Learning UDF em código VS":::

A tabela seguinte descreve cada propriedade das funções do Serviço de Aprendizagem automática Azure em Stream Analytics.

|Propriedade|Descrição|
|--------|-----------|
|Pseudónimo de função|Insira um nome para invocar a função na sua consulta.|
|Subscrição|A sua assinatura Azure.|
|Área de trabalho do Azure Machine Learning|O espaço de trabalho Azure Machine Learning que usou para implementar o seu modelo como serviço web.|
|Implementações|O serviço web que hospeda o seu modelo.|
|Assinatura de função|A assinatura do seu serviço web deduzida a partir da especificação de esquema da API. Se a sua assinatura não for carregada, verifique se forneceu a entrada e saída da amostra no seu script de pontuação para gerar automaticamente o esquema.|
|Número de pedidos paralelos por partição|Esta é uma configuração avançada para otimizar a produção de alta escala. Este número representa os pedidos simultâneos enviados de cada divisão do seu trabalho para o serviço web. Os trabalhos com seis unidades de streaming (SU) e inferiores têm uma divisória. Empregos com 12 SUs têm duas divisórias, 18 SUs têm três divisórias e assim por diante.<br><br> Por exemplo, se o seu trabalho tiver duas divisórias e definir este parâmetro para quatro, haverá oito pedidos simultâneos do seu trabalho para o seu serviço web. Neste momento de pré-visualização pública, este valor é de 20 e não pode ser atualizado.|
|Contagem de lotes max|Esta é uma configuração avançada para otimizar a produção de alta escala. Este número representa o número máximo de eventos a serem reunidos num único pedido enviado para o seu serviço web.|

## <a name="supported-input-parameters"></a>Parâmetros de entrada suportados

Quando a sua consulta Stream Analytics invoca um UDF de aprendizagem de máquinas Azure, o trabalho cria um pedido serializado JSON para o serviço web. O pedido baseia-se num esquema específico do modelo. Tem de fornecer uma entrada e saída de amostra no seu script de pontuação para [gerar automaticamente um esquema](../machine-learning/how-to-deploy-and-where.md). O esquema permite que o Stream Analytics construa o pedido serializado JSON para qualquer um dos tipos de dados suportados, tais como numpy, pandas e PySpark. Vários eventos de entrada podem ser reunidos num único pedido.

A seguinte consulta stream analytics é um exemplo de como invocar um UDF de aprendizagem de máquinas Azure:

```SQL
SELECT udf.score(<model-specific-data-structure>)
INTO output
FROM input
```

Stream Analytics suporta apenas a passagem de um parâmetro para as funções de Aprendizagem automática Azure. Poderá ter de preparar os seus dados antes de os passar como uma entrada para a aprendizagem automática da UDF. Deve certificar-se de que a entrada para o ML UDF não é nula, uma vez que as entradas nulas farão com que o trabalho falhe.

## <a name="pass-multiple-input-parameters-to-the-udf"></a>Passe vários parâmetros de entrada para o UDF

Exemplos mais comuns de entradas para modelos de aprendizagem automática são matrizes numpy e DataFrames. Pode criar uma matriz utilizando um UDF JavaScript e criar um DataFrame serializado por JSON usando a `WITH` cláusula.

### <a name="create-an-input-array"></a>Criar uma matriz de entrada

Pode criar um UDF JavaScript que aceita o número *N* de entradas e cria uma matriz que pode ser usada como entrada para o seu UDF de aprendizagem de máquinas Azure.

```javascript
function createArray(vendorid, weekday, pickuphour, passenger, distance) {
    'use strict';
    var array = [vendorid, weekday, pickuphour, passenger, distance]
    return array;
}
```

Uma vez adicionado o UDF JavaScript ao seu trabalho, pode invocar o seu UDF de aprendizagem de máquinas Azure utilizando a seguinte consulta:

```SQL
WITH 
ModelInput AS (
#use JavaScript UDF to construct array that will be used as input to ML UDF
SELECT udf.createArray(vendorid, weekday, pickuphour, passenger, distance) as inputArray
FROM input
)

SELECT udf.score(inputArray)
INTO output
FROM ModelInput
#validate inputArray is not null before passing it to ML UDF to prevent job from failing
WHERE inputArray is not null
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

Pode utilizar a `WITH` cláusula para criar um DataFrame serializado JSON que pode ser transmitido como entrada para o seu UDF de aprendizagem de máquinas Azure, como mostrado abaixo.

A seguinte consulta cria um DataFrame selecionando os campos necessários e utiliza o DataFrame como entrada para o UDF de aprendizagem de máquinas Azure.

```SQL
WITH 
Dataframe AS (
SELECT vendorid, weekday, pickuphour, passenger, distance
FROM input
)

SELECT udf.score(Dataframe)
INTO output
FROM Dataframe
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

## <a name="optimize-the-performance-for-azure-machine-learning-udfs"></a>Otimizar o desempenho para UDFs de aprendizagem automática Azure

Quando implementar o seu modelo no Serviço Azure Kubernetes, pode [perfilar o seu modelo para determinar a utilização do recurso.](../machine-learning/how-to-deploy-profile-model.md) Também pode [ativar o App Insights para as suas implementações](../machine-learning/how-to-enable-app-insights.md) para compreender as taxas de pedido, os tempos de resposta e as taxas de insucesso.

Se tiver um cenário com alta produção de eventos, poderá ter de alterar os seguintes parâmetros no Stream Analytics para obter o melhor desempenho com latências de ponta a ponta baixas:

1. Contagem de lotes max.
2. Número de pedidos paralelos por partição.

### <a name="determine-the-right-batch-size"></a>Determinar o tamanho certo do lote

Depois de ter implantado o seu serviço web, envia um pedido de amostra com diferentes tamanhos de lote a partir de 50 e aumenta-o por ordem de centenas. Por exemplo, 200, 500, 1000, 2000 e assim por diante. Vai notar que depois de um certo tamanho do lote, a latência da resposta aumenta. O ponto após o qual a latência da resposta aumenta deve ser a contagem máxima do lote para o seu trabalho.

### <a name="determine-the-number-of-parallel-requests-per-partition"></a>Determinar o número de pedidos paralelos por partição

No escalonamento ideal, o seu trabalho stream Analytics deve ser capaz de enviar vários pedidos paralelos para o seu serviço web e obter uma resposta dentro de alguns milissegundos. A latência da resposta do serviço web pode afetar diretamente a latência e desempenho do seu trabalho stream Analytics. Se a chamada do seu trabalho para o serviço web demorar muito tempo, é provável que veja um aumento no atraso da marca de água e poderá também ver um aumento no número de eventos de entrada suspensa.

Para evitar tal latência, certifique-se de que o seu cluster Azure Kubernetes Service (AKS) foi a provisionado com o [número certo de nós e réplicas](../machine-learning/how-to-deploy-azure-kubernetes-service.md#using-the-cli). É fundamental que o seu serviço web esteja altamente disponível e retorne respostas bem sucedidas. Se o seu trabalho receber uma resposta indisponíveis (503) do seu serviço web, ele irá continuamente voltar a tentar com o recuo exponencial. Qualquer resposta que não seja o sucesso (200) e o serviço indisponível (503) fará com que o seu trabalho vá para um estado falhado.

## <a name="next-steps"></a>Passos seguintes

* [Tutorial: funções definidas pelo utilizador do JavaScript do Azure Stream Analytics](stream-analytics-javascript-user-defined-functions.md)
* [Escalar o seu trabalho stream analytics com a função Azure Machine Learning Studio (clássico)](stream-analytics-scale-with-machine-learning-functions.md)
