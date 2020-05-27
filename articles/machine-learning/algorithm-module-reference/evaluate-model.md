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
ms.date: 04/24/2020
ms.openlocfilehash: e522291bdf1982ff65a62f028107b15b3249898c
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83847417"
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


## <a name="how-to-use-evaluate-model"></a>Como utilizar o Modelo de Avaliação
1. Ligue a saída do conjunto de **dados pontuado** do [Modelo de Pontuação](./score-model.md) ou resultado da saída do Conjunto de Dados de [Atribuição aos Clusters](./assign-data-to-clusters.md) à porta de entrada esquerda do Modelo de **Avaliação**. 
  > [!NOTE] 
  > Se utilizar módulos como "Select Columns in Dataset" para selecionar parte do conjunto de dados de entrada, certifique-se de que a coluna de etiquetas reais (utilizada no treino), a coluna 'Pontuadas Probabilidades' e a coluna "Etiquetas Pontuadas" existem para calcular métricas como AUC, Precisão para deteção de classificação/anomalia binária.
  > A coluna de etiquetas real, a coluna "Etiquetas Pontuadas" existe para calcular métricas para classificação/regressão de várias classes.
  > Coluna 'Atribuições', colunas 'DistancesToClusterCenter no. X' (X é índice centroídico, que varia de 0, ..., número de centrosides-1) existem para calcular métricas para agrupamento.

2. [Opcional] Ligue a saída do conjunto de **dados pontuado** do [Modelo de Pontuação](./score-model.md) ou resultado da saída do Conjunto de Dados de Atribuição para clusters para o segundo modelo à porta de entrada **direita** do Modelo de **Avaliação**. Pode facilmente comparar resultados de dois modelos diferentes nos mesmos dados. Os dois algoritmos de entrada devem ser do mesmo tipo de algoritmo. Ou, pode comparar pontuações de duas diferentes corridas sobre os mesmos dados com diferentes parâmetros.

    > [!NOTE]
    > O tipo de algoritmo refere-se a 'Classificação de duas classes', 'Classificação multi-classe', 'Regressão', 'Clustering' em 'Algoritmos de Aprendizagem automática'. 

3. Submeta o oleoduto para gerar as pontuações de avaliação.

## <a name="results"></a>Resultados

Depois de executar O **Modelo de Avaliação,** selecione o módulo para abrir o painel de navegação **do Modelo de Avaliação** à direita.  Em seguida, escolha o separador **Saídas + Logs** e, nesse separador, a secção **saídas** de dados tem vários ícones.   O ícone **Visualizar** tem um ícone de gráfico de barras, e é uma primeira maneira de ver os resultados.

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
