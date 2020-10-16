---
title: 'Modelo validado cruzado: referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo Modelo Cross-Valide em Azure Machine Learning para validar estimativas de parâmetros para modelos de classificação ou regressão dividindo os dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 63c61b12ad68a3add2e7b40ab0bec38d3c2835e5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90898574"
---
# <a name="cross-validate-model"></a>Modelo de validação cruzada

Este artigo descreve como usar o módulo Modelo Cross Valide no designer de Aprendizagem automática Azure. *A validação cruzada* é uma técnica frequentemente utilizada na aprendizagem automática para avaliar tanto a variabilidade de um conjunto de dados como a fiabilidade de qualquer modelo treinado através desses dados.  

O módulo Modelo Validado Cruz toma como entrada um conjunto de dados rotulado, juntamente com um modelo de classificação ou regressão não treinado. Divide o conjunto de dados em alguns subconjuntos *(dobras),* constrói um modelo em cada dobra e, em seguida, devolve um conjunto de estatísticas de precisão para cada dobra. Ao comparar as estatísticas de precisão de todas as dobras, pode interpretar a qualidade do conjunto de dados. Pode então compreender se o modelo é suscetível a variações nos dados.  

O Modelo Validado Cruzado também devolve os resultados e probabilidades previstos para o conjunto de dados, para que possa avaliar a fiabilidade das previsões.  

### <a name="how-cross-validation-works"></a>Como funciona a validação cruzada

1. A validação cruzada divide aleatoriamente os dados de treino em dobras. 

   O algoritmo é padrão de 10 dobras se não tiver dividido previamente o conjunto de dados. Para dividir o conjunto de dados num número diferente de dobras, pode utilizar o módulo [de partição e amostra](partition-and-sample.md) e indicar quantas dobras utilizar.  

2.  O módulo reserva os dados na dobra 1 para utilizar para validação. (Isto às vezes é chamado de *dobra de retenção*.) O módulo utiliza as restantes dobras para treinar um modelo. 

    Por exemplo, se criar cinco dobras, o módulo gera cinco modelos durante a validação cruzada. O módulo treina cada modelo utilizando quatro quintos dos dados. Testa cada modelo no quinto restante.  

3.  Durante o teste do modelo para cada dobra, o módulo avalia estatísticas de precisão múltiplas. Quais as estatísticas que o módulo usa depende do tipo de modelo que está a avaliar. Diferentes estatísticas são usadas para avaliar modelos de classificação versus modelos de regressão.  

4.  Quando o processo de construção e avaliação está completo para todas as dobras, o Modelo Cross Validate gera um conjunto de métricas de desempenho e obteve resultados para todos os dados. Reveja estas métricas para ver se uma única dobra tem alta ou baixa precisão. 

### <a name="advantages-of-cross-validation"></a>Vantagens da validação cruzada

Uma forma diferente e comum de avaliar um modelo é dividir os dados num conjunto de treino e teste utilizando [Dados Divididos](split-data.md)e, em seguida, validar o modelo nos dados de formação. Mas a validação cruzada oferece algumas vantagens:  

-   A validação cruzada utiliza mais dados de teste.

    A validação cruzada mede o desempenho do modelo com os parâmetros especificados num espaço de dados maior. Ou seja, a validação cruzada utiliza todo o conjunto de dados de treino para treino e avaliação, em vez de uma parte. Em contraste, se validar um modelo utilizando dados gerados a partir de uma divisão aleatória, normalmente avalia o modelo em apenas 30% ou menos dos dados disponíveis.  

    No entanto, como os comboios de validação cruzada e validam o modelo várias vezes ao longo de um conjunto de dados maior, é muito mais computacionalmente intensivo. Demora muito mais do que validar numa divisão aleatória.  

-   A validação cruzada avalia tanto o conjunto de dados como o modelo.

    A validação cruzada não mede simplesmente a precisão de um modelo. Também lhe dá uma ideia de quão representativo é o conjunto de dados e quão sensível o modelo pode ser às variações dos dados.  

## <a name="how-to-use-cross-validate-model"></a>Como utilizar o modelo de validação cruzada

A validação cruzada pode demorar muito tempo a ser executada se o seu conjunto de dados for grande.  Por isso, poderá utilizar o Modelo Validado Cross na fase inicial de construção e teste do seu modelo. Nessa fase, pode avaliar a bondade dos parâmetros do modelo (assumindo que o tempo de computação é tolerável). Em seguida, pode treinar e avaliar o seu modelo utilizando os parâmetros estabelecidos com os módulos [Train Model](train-model.md) e [Assess Model.](evaluate-model.md)

Neste cenário, treina-se e testa o modelo utilizando o Modelo Cross Validate.

1. Adicione o módulo Modelo De Validação Cruzada ao seu oleoduto. Pode encontrá-lo no designer de Machine Learning Azure, na categoria **model Scoring & Assessment.** 

2. Ligue a saída de qualquer modelo de classificação ou regressão. 

    Por exemplo, se estiver a utilizar **a Árvore de Decisão Reforçada de Duas Classes** para classificação, configuure o modelo com os parâmetros que deseja. Em seguida, arraste um conector da porta **modelo não treinada** do classificador para a porta correspondente do Modelo Validado Cruzado. 

    > [!TIP] 
    > Não é preciso treinar o modelo, porque o Modelo Cross-Validate treina automaticamente o modelo como parte da avaliação.  
3.  Na porta **dataset** do Modelo Validado Cruzado, ligue qualquer conjunto de dados de formação rotulado.  

4.  No painel direito do Modelo De Validação Cruzada, clique na **coluna Editar**. Selecione a única coluna que contém a etiqueta de classe ou o valor previsível. 

5. Desave um valor para o parâmetro **de sementes Aleatórias** se quiser repetir os resultados da validação cruzada em sucessivas execuções nos mesmos dados.  

6. Envie o oleoduto.

7. Consulte a secção [resultados](#results) para obter uma descrição dos relatórios.

## <a name="results"></a>Results

Depois de todas as iterações estarem completas, o Modelo Validado Cross cria pontuações para todo o conjunto de dados. Também cria métricas de desempenho que pode usar para avaliar a qualidade do modelo.

### <a name="scored-results"></a>Resultados pontuados

A primeira saída do módulo fornece os dados de origem para cada linha, juntamente com alguns valores previstos e probabilidades relacionadas. 

Para ver os resultados, no pipeline, clique à direita no módulo Modelo Validado Cruz. **Selecione Visualizar Resultados Pontuados**.

| Nome da coluna nova      | Descrição                              |
| -------------------- | ---------------------------------------- |
| Etiquetas Classificadas        | Esta coluna é adicionada no final do conjunto de dados. Contém o valor previsto para cada linha. |
| Probabilidades Pontuadas | Esta coluna é adicionada no final do conjunto de dados. Indica a probabilidade estimada do valor em **Etiquetas Pontuadas**. |
| Número dobrável          | Indica o índice baseado em zero da dobra a que cada linha de dados foi atribuída durante a validação cruzada. |

 ### <a name="evaluation-results"></a>Resultados da avaliação

O segundo relatório é agrupado por dobras. Lembre-se que durante a execução, o Modelo Cross Validate divide aleatoriamente os dados de treino em *n* folds (por defeito, 10). Em cada iteração sobre o conjunto de dados, o Modelo Validado Cross usa uma dobra como conjunto de dados de validação. Utiliza as restantes dobras *n-1* para treinar um modelo. Cada um dos modelos *n* é testado com os dados em todas as outras dobras.

Neste relatório, as dobras são listadas pelo valor do índice, por ordem ascendente.  Para encomendar em qualquer outra coluna, pode guardar os resultados como um conjunto de dados.

Para ver os resultados, no pipeline, clique à direita no módulo Modelo Validado Cruz. Selecione **visualizar os resultados da avaliação por dobra.**


|Nome da coluna| Descrição|
|----|----|
|Número dobrável| Um identificador para cada dobra. Se criasse cinco dobras, haveria cinco subconjuntos de dados, numerados de 0 a 4.
|Número de exemplos em dobra|O número de linhas atribuídas a cada dobra. Devem ser aproximadamente iguais. |


O módulo também inclui as seguintes métricas para cada dobra, dependendo do tipo de modelo que está a avaliar: 

+ **Modelos de classificação**: Precisão, recordação, pontuação F, AUC, precisão  

+ **Modelos de regressão**: Erro absoluto, erro quadrado de raiz, erro absoluto relativo, erro quadrado relativo e coeficiente de determinação


## <a name="technical-notes"></a>Notas técnicas  

+ É uma boa prática normalizar conjuntos de dados antes de usá-los para validação cruzada. 

+ O Cross Validate Model é muito mais intensivo computacionalmente e demora mais tempo a ser concluído do que se validou o modelo utilizando um conjunto de dados dividido aleatoriamente. A razão é que cross validate Model treina e valida o modelo várias vezes.

+ Não há necessidade de dividir o conjunto de dados em conjuntos de treino e testes quando se utiliza a validação cruzada para medir a precisão do modelo. 


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 

