---
title: 'Modelo de avaliação: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo Modelo de Avaliação em Aprendizagem automática Azure para medir a precisão de um modelo treinado.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/24/2020
ms.openlocfilehash: c1bcbb6a368c9c80f968c48c1a6e0bc6c95133d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456409"
---
# <a name="evaluate-model-module"></a>Avaliar módulo modelo

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Utilize este módulo para medir a precisão de um modelo treinado. Fornece um conjunto de dados contendo pontuações geradas a partir de um modelo, e o módulo **De Avaliação** do Modelo calcula um conjunto de métricas de avaliação padrão da indústria.
  
 As métricas devolvidas pelo **Modelo de Avaliação** dependem do tipo de modelo que está a avaliar:  
  
-   **Modelos de Classificação**    
-   **Modelos de regressão**  
-   **Modelos de agrupamento**  


> [!TIP]
> Se você é novo na avaliação de modelos, recomendamos a série de vídeo do Dr. Stephen Elston, como parte do curso de [machine learning](https://blogs.technet.microsoft.com/machinelearning/2015/09/08/new-edx-course-data-science-machine-learning-essentials/) da EdX. 


Existem três formas de utilizar o módulo **Modelo de Avaliação:**

+ Gere pontuações sobre os seus dados de treino e avalie o modelo com base nestas pontuações
+ Gere pontuações no modelo, mas compare essas pontuações com pontuações num conjunto de testes reservados
+ Compare pontuações para dois modelos diferentes mas relacionados, usando o mesmo conjunto de dados

## <a name="use-the-training-data"></a>Use os dados de formação

Para avaliar um modelo, deve ligar um conjunto de dados que contenha um conjunto de colunas e pontuações de entrada.  Se não houver outros dados disponíveis, pode utilizar o seu conjunto de dados original.

1. Ligue a saída do conjunto de **dados pontuado** do [Modelo de Pontuação](./score-model.md) à entrada do Modelo de **Avaliação**. 
2. Clique em Avaliar o módulo **Model** e execute o pipeline para gerar as pontuações de avaliação.

## <a name="use-testing-data"></a>Utilizar dados de teste

Um cenário comum na aprendizagem automática é separar o seu conjunto de dados original em conjuntos de dados de treino e teste, utilizando o módulo [Split,](./split-data.md) ou o módulo [de partição e amostra.](./partition-and-sample.md) 

1. Ligue a saída do conjunto de **dados pontuado** do [Modelo de Pontuação](score-model.md) à entrada do Modelo de **Avaliação**. 
2. Ligue a saída do módulo De dados divididos que contém os dados de teste à entrada direita do Modelo de **Avaliação**.
2. Clique em avaliar o módulo **Model** e selecione **Executar selecionado** para gerar as pontuações de avaliação.

## <a name="compare-scores-from-two-models"></a>Comparar pontuações de dois modelos

Também pode ligar um segundo conjunto de pontuações ao **Modelo de Avaliação**.  As pontuações podem ser um conjunto de avaliação partilhada que já conheceu resultados, ou um conjunto de resultados de um modelo diferente para os mesmos dados.

Esta funcionalidade é útil porque pode facilmente comparar resultados de dois modelos diferentes nos mesmos dados. Ou, pode comparar pontuações de duas diferentes corridas sobre os mesmos dados com diferentes parâmetros.

1. Ligue a saída do conjunto de **dados pontuado** do [Modelo de Pontuação](score-model.md) à entrada do Modelo de **Avaliação**. 
2. Ligue a saída do módulo 'Modelo de Pontuação' para o segundo modelo à entrada direita do Modelo de **Avaliação**.
3. Submeta o oleoduto.

## <a name="results"></a>Resultados

Depois de executar O **Modelo de Avaliação,** clique no módulo e selecione resultados de **Avaliação visualize** para ver os resultados.

Se ligar conjuntos de dados a ambas as inputs do Modelo de **Avaliação,** os resultados conterão métricas para ambos os conjuntos de dados, ou ambos os modelos.
O modelo ou dados anexados à porta esquerda é apresentado primeiro no relatório, seguido das métricas para o conjunto de dados, ou modelo anexado na porta direita.  

Por exemplo, a imagem que se segue representa uma comparação dos resultados de dois modelos de agrupamento que foram construídos com os mesmos dados, mas com diferentes parâmetros.  

![Comparar2Modelos](media/module/evaluate-2-models.png)  

Como este é um modelo de agrupamento, os resultados da avaliação são diferentes dos resultados de dois modelos de regressão ou comparados com dois modelos de classificação. No entanto, a apresentação global é a mesma. 

## <a name="metrics"></a>Métricas

Esta secção descreve as métricas devolvidas para os tipos específicos de modelos suportados para utilização com modelo de **avaliação:**

+ [modelos de classificação](#metrics-for-classification-models)
+ [modelos de regressão](#metrics-for-regression-models)
+ [modelos de agrupamento](#metrics-for-clustering-models)

### <a name="metrics-for-classification-models"></a>Métricas para modelos de classificação

As seguintes métricas são relatadas na avaliação dos modelos de classificação.
  
-   **A precisão** mede a bondade de um modelo de classificação como a proporção de resultados reais para casos totais.  
  
-   **Precisão** é a proporção de resultados verdadeiros sobre todos os resultados positivos.  
  
-   **Recorde-se** a fração de todos os resultados corretos devolvidos pelo modelo.  
  
-   **A pontuação F** é calculada como a média ponderada de precisão e recuperação entre 0 e 1, onde o valor ideal de pontuação F é 1.  
  
-   **A Uca** mede a área sob a curva traçada com verdadeiros positivos no eixo y e falsos positivos no eixo x. Esta métrica é útil porque fornece um único número que permite comparar modelos de diferentes tipos.  
  
- **A perda média** de registo é uma pontuação única usada para expressar a penalidade pelos resultados errados. É calculada como a diferença entre duas distribuições de probabilidades – a verdadeira, e a do modelo.  
  
- **A perda** de registo de treino é uma pontuação única que representa a vantagem do classificador sobre uma previsão aleatória. A perda de registo mede a incerteza do seu modelo comparando as probabilidades que produz com os valores conhecidos (verdade no solo) nos rótulos. Quer minimizar a perda de registo para o modelo como um todo.

### <a name="metrics-for-regression-models"></a>Métricas para modelos de regressão
 
As métricas devolvidas para os modelos de regressão são projetadas para estimar a quantidade de erro.  Considera-se que um modelo se adequa bem aos dados se a diferença entre valores observados e previsíveis for pequena. No entanto, olhar para o padrão dos residuais (a diferença entre qualquer ponto previsto e o seu valor real correspondente) pode dizer-lhe muito sobre o potencial enviesamento no modelo.  
  
 As seguintes métricas são reportadas para avaliar modelos de regressão.
  
- **Erro absoluto médio (MAE)** mede a proximidade das previsões aos resultados reais; assim, uma pontuação mais baixa é melhor.  
  
- **Erro quadrado da média raiz (RMSE)** cria um único valor que resume o erro no modelo. Ao esquartejar a diferença, a métrica ignora a diferença entre a previsão excessiva e a subprevisão.  
  
- **Erro absoluto relativo (RAE)** é a diferença absoluta relativa entre valores esperados e reais; relativo porque a diferença média é dividida pela média aritmética.  
  
- **Erro quadrado relativo (RSE)** também normaliza o erro quadrado total dos valores previstos dividindo-se pelo erro quadrado total dos valores reais.  
  

  
- **O coeficiente de determinação**, muitas vezes referido como R<sup>2</sup>, representa o poder preditivo do modelo como um valor entre 0 e 1. Zero significa que o modelo é aleatório (não explica nada); 1 significa que há um ajuste perfeito. No entanto, deve ser utilizada cautela na interpretação dos valores R<sup>2,</sup> uma vez que os valores baixos podem ser totalmente normais e os valores elevados podem ser suspeitos.

###  <a name="metrics-for-clustering-models"></a>Métricas para modelos de agrupamento

Como os modelos de agrupamento diferem significativamente dos modelos de classificação e regressão em muitos aspetos, o [Modelo de Avaliação](evaluate-model.md) também devolve um conjunto diferente de estatísticas para modelos de agrupamento.  
  
 As estatísticas devolvidas para um modelo de agrupamento descrevem quantos pontos de dados foram atribuídos a cada cluster, a quantidade de separação entre clusters e a forma como os pontos de dados estão agrupados dentro de cada cluster.  
  
 As estatísticas do modelo de agrupamento são médias ao longo de todo o conjunto de dados, com linhas adicionais contendo as estatísticas por cluster.  
  
As seguintes métricas são reportadas para avaliar modelos de agrupamento.
    
-   As pontuações na coluna, **Distância Média para Outro Centro,** representam o quão perto, em média, cada ponto do cluster é dos centrosides de todos os outros clusters.   

-   As pontuações na coluna, **Distância Média ao Cluster Center,** representam a proximidade de todos os pontos num cluster ao centroide daquele cluster.  
  
-   A coluna **Número de Pontos** mostra quantos pontos de dados foram atribuídos a cada cluster, juntamente com o número total total de pontos de dados em qualquer cluster.  
  
     Se o número de pontos de dados atribuídos aos clusters for inferior ao número total de pontos de dados disponíveis, significa que os pontos de dados não podem ser atribuídos a um cluster.  
  
-   As pontuações na coluna, **Distância Máxima ao Cluster Center,** representam a soma das distâncias entre cada ponto e o centroid do cluster desse ponto.  
  
     Se este número for elevado, pode significar que o cluster está amplamente disperso. Deve rever esta estatística juntamente com o Centro de **Distância Média para O Cluster para** determinar a propagação do cluster.   

-   A pontuação de **Avaliação Combinada** na parte inferior de cada secção de resultados enumera as pontuações médias dos clusters criados nesse modelo em particular.  
  

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 