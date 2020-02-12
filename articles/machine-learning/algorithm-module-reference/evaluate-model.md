---
title: 'Avaliar modelo: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo modelo de avaliação no Azure Machine Learning para medir a precisão de um modelo treinado.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 5951c6ec63478b4b266f22eaf8bf3162e0a45df0
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137551"
---
# <a name="evaluate-model-module"></a>Módulo avaliar modelo

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Use este módulo para medir a precisão de um modelo treinado. Fornece um conjunto de dados contendo pontuações geradas a partir de um modelo, e o módulo **De Avaliação** do Modelo calcula um conjunto de métricas de avaliação padrão da indústria.
  
 As métricas devolvidas pelo **Modelo de Avaliação** dependem do tipo de modelo que está a avaliar:  
  
-   **Modelos de Classificação**    
-   **Modelos de regressão**    


> [!TIP]
> Se você é novo na avaliação de modelos, recomendamos a série de vídeo do Dr. Stephen Elston, como parte do curso de [machine learning](https://blogs.technet.microsoft.com/machinelearning/2015/09/08/new-edx-course-data-science-machine-learning-essentials/) da EdX. 


Existem três formas de utilizar o módulo **Modelo de Avaliação:**

+ Gerar pontuações sobre seus dados de treinamento e avaliar o modelo com base nessas pontuações
+ Gerar pontuações no modelo, mas comparar essas pontuações com pontuações em um conjunto de teste reservado
+ Comparar pontuações para dois modelos diferentes, mas relacionados, usando o mesmo conjunto de dados

## <a name="use-the-training-data"></a>Usar os dados de treinamento

Para avaliar um modelo, você deve conectar um conjunto de dados que contém um conjunto de colunas de entrada e pontuações.  Se nenhum outro dado estiver disponível, você poderá usar seu conjunto de dados original.

1. Ligue a saída do conjunto de **dados pontuado** do [Modelo de Pontuação](./score-model.md) à entrada do Modelo de **Avaliação**. 
2. Clique em Avaliar o módulo **Model** e execute o pipeline para gerar as pontuações de avaliação.

## <a name="use-testing-data"></a>Usar dados de teste

Um cenário comum na aprendizagem automática é separar o seu conjunto de dados original em conjuntos de dados de treino e teste, utilizando o módulo [Split,](./split-data.md) ou o módulo [de partição e amostra.](./partition-and-sample.md) 

1. Ligue a saída do conjunto de **dados pontuado** do [Modelo de Pontuação](score-model.md) à entrada do Modelo de **Avaliação**. 
2. Ligue a saída do módulo De dados divididos que contém os dados de teste à entrada direita do Modelo de **Avaliação**.
2. Clique em avaliar o módulo **Model** e selecione **Executar selecionado** para gerar as pontuações de avaliação.

## <a name="compare-scores-from-two-models"></a>Comparar pontuações de dois modelos

Também pode ligar um segundo conjunto de pontuações ao **Modelo de Avaliação**.  As pontuações podem ser um conjunto de avaliação compartilhado com resultados conhecidos ou um conjunto de resultados de um modelo diferente para os mesmos dados.

Esse recurso é útil porque você pode comparar facilmente os resultados de dois modelos diferentes nos mesmos dados. Ou, você pode comparar pontuações de duas execuções diferentes nos mesmos dados com parâmetros diferentes.

1. Ligue a saída do conjunto de **dados pontuado** do [Modelo de Pontuação](score-model.md) à entrada do Modelo de **Avaliação**. 
2. Ligue a saída do módulo 'Modelo de Pontuação' para o segundo modelo à entrada direita do Modelo de **Avaliação**.
3. Executar o pipeline.

## <a name="results"></a>Resultados

Depois de executar O **Modelo de Avaliação,** clique no módulo e selecione resultados de **Avaliação visualize** para ver os resultados.

Se ligar conjuntos de dados a ambas as inputs do Modelo de **Avaliação,** os resultados conterão métricas para ambos os conjuntos de dados, ou ambos os modelos.
O modelo ou os dados anexados à porta à esquerda são apresentados primeiro no relatório, seguidos pelas métricas para o conjunto de dados ou modelo anexado na porta correta.  

Por exemplo, a imagem a seguir representa uma comparação de resultados de dois modelos de clustering que foram criados nos mesmos dados, mas com parâmetros diferentes.  

![AML&#95;Comparando2Modelos](media/module/aml-comparing2models.png "AML_Comparing2Models")  

Como esse é um modelo de clustering, os resultados da avaliação são diferentes de se você comparasse pontuações de dois modelos de regressão ou compararam dois modelos de classificação. No entanto, a apresentação geral é a mesma. 

## <a name="metrics"></a>Métricas

Esta secção descreve as métricas devolvidas para os tipos específicos de modelos suportados para utilização com modelo de **avaliação:**

+ [modelos de classificação](#metrics-for-classification-models)
+ [modelos de regressão](#metrics-for-regression-models)

### <a name="metrics-for-classification-models"></a>Métricas para modelos de classificação

As métricas a seguir são relatadas ao avaliar modelos de classificação. Se você comparar modelos, eles serão classificados pela métrica selecionada para avaliação.  
  
-   **A precisão** mede a bondade de um modelo de classificação como a proporção de resultados reais para casos totais.  
  
-   **Precisão** é a proporção de resultados verdadeiros sobre todos os resultados positivos.  
  
-   **Recorde-se** a fração de todos os resultados corretos devolvidos pelo modelo.  
  
-   **A pontuação F** é calculada como a média ponderada de precisão e recuperação entre 0 e 1, onde o valor ideal de pontuação F é 1.  
  
-   **A Uca** mede a área sob a curva traçada com verdadeiros positivos no eixo y e falsos positivos no eixo x. Essa métrica é útil porque fornece um único número que permite comparar modelos de tipos diferentes.  
  
- **A perda média** de registo é uma pontuação única usada para expressar a penalidade pelos resultados errados. Ele é calculado como a diferença entre duas distribuições de probabilidade – a verdadeira e a do modelo.  
  
- **A perda** de registo de treino é uma pontuação única que representa a vantagem do classificador sobre uma previsão aleatória. A perda de log mede a incerteza de seu modelo comparando as probabilidades que gera para os valores conhecidos (verdade) nos rótulos. Você deseja minimizar a perda de log para o modelo como um todo.

### <a name="metrics-for-regression-models"></a>Métricas para modelos de regressão
 
As métricas retornadas para modelos de regressão são projetadas para estimar a quantidade de erros.  Um modelo é considerado adequado para os dados se a diferença entre os valores observados e previstos for pequena. No entanto, observar o padrão dos resíduos (a diferença entre qualquer ponto previsto e seu valor real correspondente) pode informá-lo muito sobre a possível diferença no modelo.  
  
 As métricas a seguir são relatadas para avaliar modelos de regressão. Quando você compara modelos, eles são classificados pela métrica selecionada para avaliação.  
  
- **Erro absoluto médio (MAE)** mede a proximidade das previsões aos resultados reais; assim, uma pontuação mais baixa é melhor.  
  
- **Erro quadrado da média raiz (RMSE)** cria um único valor que resume o erro no modelo. Ao elevar a diferença, a métrica não considera a diferença entre a previsão de excesso e a subprevisão.  
  
- **Erro absoluto relativo (RAE)** é a diferença absoluta relativa entre valores esperados e reais; relativo porque a diferença média é dividida pela média aritmética.  
  
- **Erro quadrado relativo (RSE)** também normaliza o erro quadrado total dos valores previstos dividindo-se pelo erro quadrado total dos valores reais.  
  

  
- **O coeficiente de determinação**, muitas vezes referido como R<sup>2</sup>, representa o poder preditivo do modelo como um valor entre 0 e 1. Zero significa que o modelo é aleatório (explica nada); 1 significa que há um ajuste perfeito. No entanto, deve ser utilizada cautela na interpretação dos valores R<sup>2,</sup> uma vez que os valores baixos podem ser totalmente normais e os valores elevados podem ser suspeitos.
  

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 