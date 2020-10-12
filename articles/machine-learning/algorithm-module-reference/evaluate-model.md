---
title: 'Modelo de avaliação: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo De Avaliar o Modelo em Azure Machine Learning para medir a precisão de um modelo treinado.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/27/2020
ms.openlocfilehash: 6dfee84c44643823a4ec76c32e750febc6646be5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90908056"
---
# <a name="evaluate-model-module"></a>Avaliar módulo de modelo

Este artigo descreve um módulo no designer de Aprendizagem automática Azure.

Utilize este módulo para medir a precisão de um modelo treinado. Fornece um conjunto de dados contendo pontuações geradas a partir de um modelo, e o módulo **De Avaliar o módulo** calcula um conjunto de métricas de avaliação padrão da indústria.
  
 As métricas devolvidas pelo **Modelo de Avaliação** dependem do tipo de modelo que está a avaliar:  
  
-   **Modelos de classificação**    
-   **Modelos de regressão**  
-   **Modelos de Agrupamento**  


> [!TIP]
> Se é novo na avaliação de modelos, recomendamos a série de vídeo do Dr. Stephen Elston, como parte do curso de [machine learning](https://blogs.technet.microsoft.com/machinelearning/2015/09/08/new-edx-course-data-science-machine-learning-essentials/) da EdX. 


## <a name="how-to-use-evaluate-model"></a>Como utilizar o Modelo de Avaliação
1. Ligue a saída de conjunto de **dados pontuado** do modelo de [pontuação](./score-model.md) ou saída de conjunto de dados de resultados dos [dados de atribuição aos clusters](./assign-data-to-clusters.md) à porta de entrada esquerda do Modelo de **Avaliação**. 
    > [!NOTE] 
    > Se utilizar módulos como "Selecione Colunas no Conjunto de Dados" para selecionar parte do conjunto de dados de entrada, certifique-se de que a coluna de etiquetas reais (utilizada em treino), a coluna 'Probabilidades Pontuadas' e a coluna 'Etiquetas Pontuadas' existem para calcular métricas como AUC, Precisão para classificação binária/deteção de anomalias.
    > Coluna de etiquetas reais, coluna 'Labels Scored' existe para calcular métricas para classificação/regressão multi-classes.
    > Coluna 'Atribuições', colunas 'DistancesToClusterCenter no. X' (X é índice centroid, que varia de 0, ..., Número de centrosids-1) existem para calcular métricas para o agrupamento.

2. [Opcional] Ligue a saída de conjunto de **dados pontuado** do modelo de [pontuação](./score-model.md) ou saída de conjunto de dados de resultados dos dados de atribuição a clusters para o segundo modelo à porta de entrada **direita** do Modelo de **Avaliação**. Pode facilmente comparar resultados de dois modelos diferentes nos mesmos dados. Os dois algoritmos de entrada devem ser do mesmo tipo de algoritmo. Ou, pode comparar pontuações de duas corridas diferentes sobre os mesmos dados com parâmetros diferentes.

    > [!NOTE]
    > O tipo de algoritmo refere-se a "Classificação de duas classes", "Classificação multi-classes", "Regressão", "Agrupamento" em "Algoritmos de Aprendizagem automática". 

3. Submeta o pipeline para gerar os resultados da avaliação.

## <a name="results"></a>Results

Depois de executar **o Modelo de Avaliação,** selecione o módulo para abrir o painel de navegação **do Modelo avaliar** à direita.  Em seguida, escolha o **separador Saídas + Registos** e nesse separador a secção **Saídas de Dados** tem vários ícones. O ícone **do Visualize** tem um ícone de gráfico de barras, e é uma primeira maneira de ver os resultados.

Para classificação binária, depois de clicar no ícone **Visualize,** pode visualizar a matriz de confusão binária.
Para multi-classificação, pode encontrar o ficheiro de enredo de matriz de confusão no separador **Saídas + Logs** como seguinte:
> [!div class="mx-imgBorder"]
> ![Pré-visualização da imagem carregada](media/module/multi-class-confusion-matrix.png)

Se ligar conjuntos de dados a ambas as entradas do Modelo de **Avaliação,** os resultados conterão métricas para ambos os conjuntos de dados, ou ambos os modelos.
O modelo ou dados anexados à porta esquerda é apresentado primeiro no relatório, seguido das métricas para o conjunto de dados, ou modelo anexado na porta direita.  

Por exemplo, a imagem a seguir representa uma comparação de resultados de dois modelos de clustering que foram construídos com base nos mesmos dados, mas com parâmetros diferentes.  

![Comparando2 Modelos](media/module/evaluate-2-models.png)  

Por se tratar de um modelo de agrupamento, os resultados da avaliação são diferentes dos que comparamos pontuações de dois modelos de regressão, ou comparados com dois modelos de classificação. No entanto, a apresentação geral é a mesma. 

## <a name="metrics"></a>Métricas

Esta secção descreve as métricas devolvidas para os tipos específicos de modelos suportados para utilização com **o Modelo de Avaliação:**

+ [modelos de classificação](#metrics-for-classification-models)
+ [modelos de regressão](#metrics-for-regression-models)
+ [modelos de agrupamento](#metrics-for-clustering-models)

### <a name="metrics-for-classification-models"></a>Métricas para modelos de classificação


As seguintes métricas são reportadas ao avaliar modelos de classificação binária.
  
-   **A precisão** mede a bondade de um modelo de classificação como a proporção de resultados verdadeiros para os casos totais.  
  
-   **Precisão** é a proporção de resultados verdadeiros sobre todos os resultados positivos. Precisão = TP/(TP+FP)  
  
-   **A recuperação** é a fração da quantidade total de instâncias relevantes que foram efetivamente recuperadas. Recordação = TP/(TP+FN)  
  
-   **A pontuação F1** é calculada como a média ponderada de precisão e recordação entre 0 e 1, onde o valor ideal de pontuação de F1 é 1.  
  
-   **A AUC** mede a área sob a curva traçada com verdadeiros positivos no eixo y e falsos positivos no eixo x. Esta métrica é útil porque fornece um único número que permite comparar modelos de diferentes tipos.  


### <a name="metrics-for-regression-models"></a>Métricas para modelos de regressão
 
As métricas devolvidas para modelos de regressão são projetadas para estimar a quantidade de erro.  Um modelo é considerado adequado para encaixar bem os dados se a diferença entre valores observados e previstos for pequena. No entanto, olhando para o padrão dos residuais (a diferença entre qualquer ponto previsto e o seu valor real correspondente) pode dizer-lhe muito sobre o potencial enviesamento no modelo.  
  
 As seguintes métricas são reportadas para avaliar modelos de regressão.
  
- **O erro absoluto médio (MAE)** mede a proximidade das previsões aos resultados reais; assim, uma pontuação mais baixa é melhor.  
  
- **Erro quadrado de raiz (RMSE)** cria um único valor que resume o erro no modelo. Ao esquartejar a diferença, a métrica ignora a diferença entre a sobre-previsão e a sub-previsão.  
  
- **Erro absoluto relativo (RAE)** é a diferença absoluta relativa entre valores esperados e reais; relativo porque a diferença média é dividida pela média aritmética.  
  
- **Erro ao quadrado relativo (RSE)** normaliza igualmente o erro total ao quadrado dos valores previstos, dividindo-se pelo erro total ao quadrado dos valores reais.  
  

  
- **Coeficiente de determinação**, muitas vezes referido como R<sup>2</sup>, representa o poder preditivo do modelo como um valor entre 0 e 1. Zero significa que o modelo é aleatório (não explica nada); 1 significa que há um ajuste perfeito. No entanto, deve ser usada cautela na interpretação dos valores R<sup>2,</sup> uma vez que valores baixos podem ser inteiramente normais e valores elevados podem ser suspeitos.

###  <a name="metrics-for-clustering-models"></a>Métricas para modelos de agrupamento

Como os modelos de clustering diferem significativamente dos modelos de classificação e regressão em muitos aspetos, [o Modelo de Avaliação](evaluate-model.md) também devolve um conjunto diferente de estatísticas para modelos de clustering.  
  
 As estatísticas devolvidas para um modelo de agrupamento descrevem quantos pontos de dados foram atribuídos a cada cluster, a quantidade de separação entre clusters, e quão apertados os pontos de dados são agrupados dentro de cada cluster.  
  
 As estatísticas do modelo de agrupamento são médias ao longo de todo o conjunto de dados, com linhas adicionais contendo as estatísticas por cluster.  
  
As seguintes métricas são reportadas para a avaliação de modelos de agrupamento.
    
-   As pontuações na coluna, **Distância Média para Outro Centro,** representam o quão perto, em média, cada ponto do cluster é para os centrosids de todos os outros clusters.   

-   As pontuações na coluna, **Distância Média ao Centro de Agrupamentos,** representam a proximidade de todos os pontos num aglomerado ao centroid daquele cluster.  
  
-   A coluna **Número de Pontos** mostra quantos pontos de dados foram atribuídos a cada cluster, juntamente com o número total total de pontos de dados em qualquer cluster.  
  
     Se o número de pontos de dados atribuídos aos clusters for inferior ao número total de pontos de dados disponíveis, significa que os pontos de dados não poderiam ser atribuídos a um cluster.  
  
-   As pontuações na coluna, **Maximal Distance to Cluster Center,** representam o máximo das distâncias entre cada ponto e o centroírico do cluster desse ponto.  
  
     Se este número for elevado, pode significar que o aglomerado está amplamente disperso. Deve rever esta estatística juntamente com a **Distância Média ao Cluster Center** para determinar a propagação do cluster.   

-   A **pontuação de Avaliação Combinada** na parte inferior de cada secção de resultados lista as pontuações médias para os clusters criados nesse modelo específico.  
  

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 
