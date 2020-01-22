---
title: 'Avaliar modelo: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo modelo de avaliação no Azure Machine Learning para medir a precisão de um modelo treinado.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 11/19/2019
ms.openlocfilehash: b6a019fbea2ebfed67db4f7c2a9b0f8085438aa8
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76311653"
---
# <a name="evaluate-model-module"></a>Módulo avaliar modelo

Este artigo descreve um módulo no designer de Azure Machine Learning.

Use este módulo para medir a precisão de um modelo treinado. Você fornece um conjunto de resultados que contém pontuações geradas de um modelo e o módulo **avaliar modelo** computa um conjunto de métricas de avaliação padrão do setor.
  
 As métricas retornadas pelo **modelo de avaliação** dependem do tipo de modelo que você está avaliando:  
  
-   **Modelos de classificação**    
-   **Modelos de regressão**    


> [!TIP]
> Se você for novo na avaliação do modelo, recomendamos a série de vídeos de Dr. Stephen Elston, como parte do [curso do Machine Learning](https://blogs.technet.microsoft.com/machinelearning/2015/09/08/new-edx-course-data-science-machine-learning-essentials/) do EdX. 


Há três maneiras de usar o módulo **avaliar modelo** :

+ Gerar pontuações sobre seus dados de treinamento e avaliar o modelo com base nessas pontuações
+ Gerar pontuações no modelo, mas comparar essas pontuações com pontuações em um conjunto de teste reservado
+ Comparar pontuações para dois modelos diferentes, mas relacionados, usando o mesmo conjunto de dados

## <a name="use-the-training-data"></a>Usar os dados de treinamento

Para avaliar um modelo, você deve conectar um conjunto de dados que contém um conjunto de colunas de entrada e pontuações.  Se nenhum outro dado estiver disponível, você poderá usar seu conjunto de dados original.

1. Conecte a saída do conjunto de dados **pontuado** do [modelo de Pontuação](./score-model.md) à entrada do **modelo de avaliação**. 
2. Clique em **avaliar módulo modelo** e execute o pipeline para gerar as pontuações de avaliação.

## <a name="use-testing-data"></a>Usar dados de teste

Um cenário comum no aprendizado de máquina é separar o conjunto de dados original em conjuntos de dados de treinamento e teste, usando o módulo [dividir](./split-data.md) ou o módulo [partição e exemplo](./partition-and-sample.md) . 

1. Conecte a saída do conjunto de dados **pontuado** do [modelo de Pontuação](score-model.md) à entrada do **modelo de avaliação**. 
2. Conecte a saída do módulo dividir dados que contém os dados de teste à entrada do lado direito do **modelo de avaliação**.
2. Clique em **avaliar módulo modelo** e selecione **executar selecionado** para gerar as pontuações de avaliação.

## <a name="compare-scores-from-two-models"></a>Comparar pontuações de dois modelos

Você também pode conectar um segundo conjunto de pontuações para **avaliar o modelo**.  As pontuações podem ser um conjunto de avaliação compartilhado com resultados conhecidos ou um conjunto de resultados de um modelo diferente para os mesmos dados.

Esse recurso é útil porque você pode comparar facilmente os resultados de dois modelos diferentes nos mesmos dados. Ou, você pode comparar pontuações de duas execuções diferentes nos mesmos dados com parâmetros diferentes.

1. Conecte a saída do conjunto de dados **pontuado** do [modelo de Pontuação](score-model.md) à entrada do **modelo de avaliação**. 
2. Conecte a saída do módulo modelo de Pontuação para o segundo modelo à entrada do lado direito do **modelo de avaliação**.
3. Executar o pipeline.

## <a name="results"></a>Resultados

Depois de executar o **modelo de avaliação**, clique com o botão direito do mouse no módulo e selecione **Visualizar resultados da avaliação** para ver os resultados.

Se você conectar conjuntos de dados a ambas as entradas do **modelo de avaliação**, os resultados conterão métricas para ambos os conjuntos ou ambos os modelos.
O modelo ou os dados anexados à porta à esquerda são apresentados primeiro no relatório, seguidos pelas métricas para o conjunto de dados ou modelo anexado na porta correta.  

Por exemplo, a imagem a seguir representa uma comparação de resultados de dois modelos de clustering que foram criados nos mesmos dados, mas com parâmetros diferentes.  

![Comparing2Models&#95;AML](media/module/aml-comparing2models.png "AML_Comparing2Models")  

Como esse é um modelo de clustering, os resultados da avaliação são diferentes de se você comparasse pontuações de dois modelos de regressão ou compararam dois modelos de classificação. No entanto, a apresentação geral é a mesma. 

## <a name="metrics"></a>Métricas

Esta seção descreve as métricas retornadas para os tipos específicos de modelos com suporte para uso com o **modelo de avaliação**:

+ [modelos de classificação](#bkmk_classification)
+ [modelos de regressão](#bkmk_regression)

###  <a name="bkmk_classification"></a>Métricas para modelos de classificação

As métricas a seguir são relatadas ao avaliar modelos de classificação. Se você comparar modelos, eles serão classificados pela métrica selecionada para avaliação.  
  
-   A **precisão** mede a imsorte de um modelo de classificação como a proporção de resultados verdadeiros para casos totais.  
  
-   **Precisão** é a proporção de resultados verdadeiros em todos os resultados positivos.  
  
-   **Recall** é a fração de todos os resultados corretos retornados pelo modelo.  
  
-   A **Pontuação de f** é calculada como a média ponderada de precisão e RECALL entre 0 e 1, em que o valor ideal de Pontuação F é 1.  
  
-   **Auc** mede a área sob a curva plotada com verdadeiros positivos no eixo y e falsos positivos no eixo x. Essa métrica é útil porque fornece um único número que permite comparar modelos de tipos diferentes.  
  
- A **perda de log média** é uma única Pontuação usada para expressar a penalidade para resultados incorretos. Ele é calculado como a diferença entre duas distribuições de probabilidade – a verdadeira e a do modelo.  
  
- A **perda do log de treinamento** é uma única pontuação que representa a vantagem do classificador em uma previsão aleatória. A perda de log mede a incerteza de seu modelo comparando as probabilidades que gera para os valores conhecidos (verdade) nos rótulos. Você deseja minimizar a perda de log para o modelo como um todo.

##  <a name="bkmk_regression"></a>Métricas para modelos de regressão
 
As métricas retornadas para modelos de regressão são projetadas para estimar a quantidade de erros.  Um modelo é considerado adequado para os dados se a diferença entre os valores observados e previstos for pequena. No entanto, observar o padrão dos resíduos (a diferença entre qualquer ponto previsto e seu valor real correspondente) pode informá-lo muito sobre a possível diferença no modelo.  
  
 As métricas a seguir são relatadas para avaliar modelos de regressão. Quando você compara modelos, eles são classificados pela métrica selecionada para avaliação.  
  
- O **erro de média absoluta (Mae)** mede o quão próximo as previsões são para os resultados reais; Portanto, uma pontuação mais baixa é melhor.  
  
- **Erro de quadrado médio de raiz (RMSE)** cria um único valor que resume o erro no modelo. Ao elevar a diferença, a métrica não considera a diferença entre a previsão de excesso e a subprevisão.  
  
- **Erro absoluto relativo (RAE)** é a diferença absoluta relativa entre os valores esperado e real; relativo porque a diferença média é dividida pela média aritmética.  
  
- O **erro quadrado relativo (RSE)** normaliza, de forma semelhante, o erro quadrado total dos valores previstos dividindo pelo erro quadrado total dos valores reais.  
  
- **Significa zero um erro (MZOE)** indica se a previsão foi correta ou não.  Em outras palavras: `ZeroOneLoss(x,y) = 1` quando `x!=y`; caso contrário `0`.
  
- O **coeficiente de determinação**, geralmente referido como R<sup>2</sup>, representa a potência preditiva do modelo como um valor entre 0 e 1. Zero significa que o modelo é aleatório (explica nada); 1 significa que há um ajuste perfeito. No entanto, cuidado deve ser usado na interpretação de valores de R<sup>2</sup> , já que valores baixos podem ser totalmente normais e valores altos podem ser suspeitos.
  

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 