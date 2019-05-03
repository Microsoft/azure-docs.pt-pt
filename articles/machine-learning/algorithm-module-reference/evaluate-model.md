---
title: 'Avalie o modelo: Referência do módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como utilizar o módulo de modelo de avaliação no serviço Azure Machine Learning para medir a precisão de um modelo preparado.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 40a8247c22da1f7a057e222565ffb2ec4c6b7fb3
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028744"
---
# <a name="evaluate-model-module"></a>Avalie o módulo do modelo

Este artigo descreve um módulo da interface visual (pré-visualização) para o serviço Azure Machine Learning.

Utilize este módulo para medir a precisão de um modelo preparado. Fornece um conjunto de dados que contém as pontuações geradas a partir de um modelo e o **Evaluate Model** módulo computa um conjunto de métricas de avaliação de norma da indústria.
  
 As métricas devolvidas pela **Evaluate Model** dependem do tipo de modelo que estiver a avaliar o:  
  
-   **Modelos de classificação**    
-   **Modelos de regressão**    



> [!TIP]
> Se estiver familiarizado com a avaliação do modelo, recomendamos que a série de vídeos ao Dr. Stephen Elston, como parte da [curso de aprendizagem de máquina](https://blogs.technet.microsoft.com/machinelearning/2015/09/08/new-edx-course-data-science-machine-learning-essentials/) da EdX. 


Existem três formas de utilizar o **Evaluate Model** módulo:

+ Gerar pontuações sobre os seus dados de formação e avaliar o modelo com base nestas classificações
+ Gerar pontuações no modelo, mas compare essas pontuações de pontuações num conjunto de teste reservado
+ Comparar as pontuações para dois modelos diferentes mas relacionadas, utilizando o mesmo conjunto de dados

## <a name="use-the-training-data"></a>Utilizar os dados de treinamento

Para avaliar um modelo, tem de ligar um conjunto de dados que contém um conjunto de colunas de entrada e as pontuações.  Se outros dados não estiverem disponíveis, pode usar o conjunto de dados original.

1. Ligar o **classificadas de conjunto de dados** o resultado do [modelo de pontuação](./score-model.md) à entrada de **Evaluate Model**. 
2. Clique em **Evaluate Model** módulo e execute a experimentação para gerar as pontuações de avaliação.

## <a name="use-testing-data"></a>Utilizar dados de testes

Um cenário comum no machine learning é separar o seu conjunto de dados original em preparação e teste de conjuntos de dados, com o [Split](./split-data.md) módulo, ou o [partição e amostras](./partition-and-sample.md) módulo. 

1. Ligar o **classificadas de conjunto de dados** o resultado do [modelo de pontuação](score-model.md) à entrada de **Evaluate Model**. 
2. Ligue a saída do módulo de dividir dados que contém dados de teste à entrada da direita de **Evaluate Model**.
2. Clique em **Evaluate Model** módulo e selecione **executar seleção** para gerar as pontuações de avaliação.

## <a name="compare-scores-from-two-models"></a>Comparar as pontuações de dois modelos

Também pode ligar-se um segundo conjunto de pontuações para **Evaluate Model**.  As pontuações podem ser um conjunto de avaliação partilhado que é conhecida resultados, ou um conjunto de resultados de um modelo diferente para os mesmos dados.

Esta funcionalidade é útil porque pode comparar facilmente resultados de dois modelos diferentes nos mesmos dados. Em alternativa, pode comparar as pontuações de duas execuções diferentes sobre os mesmos dados com diferentes parâmetros.

1. Ligar o **classificadas de conjunto de dados** o resultado do [modelo de pontuação](score-model.md) à entrada de **Evaluate Model**. 
2. Ligue a saída do módulo do modelo de pontuação para o segundo modelo à entrada da direita de **Evaluate Model**.
3. Com o botão direito **Evaluate Model**e selecione **executar seleção** para gerar as pontuações de avaliação.

## <a name="results"></a>Resultados

Depois de executar **Evaluate Model**, o módulo com o botão direito e selecione **resultados da avaliação** para ver os resultados. Pode:

+ Guardar os resultados como um conjunto de dados, para facilitar a análise com outras ferramentas
+ Gerar uma visualização da interface

Se ligar a conjuntos de dados para ambas as entradas da **Evaluate Model**, os resultados conterá as métricas para ambos os conjunto de dados, ou ambos os modelos.
O modelo ou os dados anexados para a porta à esquerda são apresentados pela primeira vez no relatório, seguido as métricas do conjunto de dados, ou modelo ligado na porta correta.  

Por exemplo, a imagem seguinte representa uma comparação dos resultados de dois modelos de clusters que foram criados nos mesmos dados, mas com diferentes parâmetros.  

![AML&#95;Comparing2Models](media/module/aml-comparing2models.png "AML_Comparing2Models")  

Como se trata de um modelo de clustering, os resultados da avaliação são diferentes de se em comparação com pontuações de dois modelos de regressão ou, em comparação com dois modelos de classificação. No entanto, a apresentação geral é o mesmo. 

## <a name="metrics"></a>Métricas

Esta secção descreve as métricas devolvidas para os tipos específicos de modelos suportados para utilização com **Evaluate Model**:

+ [modelos de classificação](#bkmk_classification)
+ [modelos de regressão](#bkmk_regression)

###  <a name="bkmk_classification"></a> Métricas de modelos de classificação

As métricas seguintes são comunicadas ao avaliar os modelos de classificação. Se comparar modelos, eles são classificados pela métrica selecionada para avaliação.  
  
-   **Precisão** mede o quão bom é um modelo de classificação como a proporção de resultados verdadeiros para casos total.  
  
-   **Precisão** é a proporção de verdadeira resulta em todos os resultados positivos.  
  
-   **Lembre-se de** é a fração de todos os resultados corretos devolvido pelo modelo.  
  
-   **Pontuação de F** é computado como a média ponderada de precisão e lembre-se entre 0 e 1, em que o valor de pontuação F ideal é 1.  
  
-   **AUC** medidas a área sob a curva desenhada com true positivos no y positivos eixo e false no eixo x. Esta métrica é útil porque fornece um único número, que permite que compare os modelos de diferentes tipos.  
  
- **Média de perda de log** é uma única pontuação usada para expressar a penalidade por resultados errados. Esta é calculada como a diferença entre dois de probabilidade de distribuições – a uma verdadeira e a outra no modelo.  
  
- **Perda de registo de treinamento** é uma única pontuação que representa a vantagem do classificador sobre uma predição aleatória. A perda de log mede a incerteza inerentes ao seu modelo ao comparar as probabilidades, que ele gera a saída para os valores conhecidos (verdade zero) em etiquetas. Deseja minimizar a perda de registo para o modelo como um todo.

##  <a name="bkmk_regression"></a> Métricas de modelos de regressão
 
As métricas devolvidas para modelos de regressão geralmente são projetadas para estimar a quantidade de erro.  Um modelo é considerado de acordo com os dados também se a diferença entre os valores observados e previstos é pequena. No entanto, observar o padrão dos residuals (a diferença entre um ponto de prevista e seu valor real correspondente) pode dizer muito sobre uma tendência potencial no modelo.  
  
 As métricas seguintes são apresentadas para avaliar os modelos de regressão. Ao comparar modelos, eles são classificados pela métrica selecionada para avaliação.  
  
- **Significa que o erro absoluto (MAE)** mede como fechar as previsões são para os resultados reais; portanto, é melhor uma pontuação inferior.  
  
- **Erro ao quadrado (RMSE) significa que de raiz** cria um valor único que resume o erro no modelo. Por squaring a diferença, a métrica disregards a diferença entre a predição insuficientemente e previsão de excesso.  
  
- **Erro relativo absoluto (RAE)** é relativa diferença absoluta entre os valores de expected e actual; relativo porque a diferença de média é dividida pela média aritmética.  
  
- **Relativo ao quadrado erro (RSE)** da mesma forma normaliza o total de erros ao quadrado dos valores previstos dividindo por total de erros ao quadrado dos valores reais.  
  
- **Significa Zero um erro (MZOE)** indica se a predição foi correta ou não.  Em outras palavras: `ZeroOneLoss(x,y) = 1` quando `x!=y`; caso contrário `0`.
  
- **Coeficiente de determinação**, frequentemente referidos como R<sup>2</sup>, representam a capacidade de previsão do modelo como um valor entre 0 e 1. Zero significa que o modelo é aleatório (explica nada); 1 significa que há um ajuste perfeito. No entanto, deve ter cuidado na interpretação R<sup>2</sup> valores, como valores de baixas podem ser totalmente normais e valores altos podem ser suspeita.
  

## <a name="next-steps"></a>Passos Seguintes

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço Azure Machine Learning. 