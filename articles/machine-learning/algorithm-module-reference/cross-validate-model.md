---
title: 'Modelo de validação cruzada: referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo Cross-Validate Model em Azure Machine Learning para validar as estimativas de parâmetros de classificação ou regressão através da partilha dos dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 6dd8246d5751609e2f20ee9d5e519529752940f7
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137532"
---
# <a name="cross-validate-model"></a>Modelo de validação cruzada

Este artigo descreve como usar o módulo Cross Validate Model em Azure Machine Learning designer (pré-visualização). *A validação cruzada* é uma técnica frequentemente utilizada na aprendizagem automática para avaliar tanto a variabilidade de um conjunto de dados como a fiabilidade de qualquer modelo treinado através desses dados.  

O módulo Cross Validate Model toma como entrada um conjunto de dados rotulado, juntamente com um modelo de classificação ou regressão não treinado. Divide o conjunto de dados em alguns subconjuntos *(dobras),* constrói um modelo em cada dobra e, em seguida, devolve um conjunto de estatísticas de precisão para cada dobra. Comparando as estatísticas de precisão para todas as dobras, pode interpretar a qualidade do conjunto de dados. Pode então compreender se o modelo é suscetível a variações nos dados.  

Cross Validate Model também devolve resultados e probabilidades previsíveis para o conjunto de dados, para que possa avaliar a fiabilidade das previsões.  

### <a name="how-cross-validation-works"></a>Como funciona a validação cruzada

1. A validação cruzada divide aleatoriamente os dados de treino em dobras. 

   O algoritmo falha em 10 dobras se não tiver previamente dividido o conjunto de dados. Para dividir o conjunto de dados num número diferente de dobras, pode utilizar o módulo [De Partilha e Amostra](partition-and-sample.md) e indicar quantas dobras usar.  

2.  O módulo reserva os dados no ponto 1 para ser utilizado para validação. (Isto às vezes é chamado de dobra de *retenção*.) O módulo utiliza as restantes dobras para treinar um modelo. 

    Por exemplo, se criar cinco dobras, o módulo gera cinco modelos durante a validação cruzada. O módulo treina cada modelo utilizando quatro quintos dos dados. Testa cada modelo no restante quinto.  

3.  Durante o teste do modelo para cada dobra, o módulo avalia estatísticas de precisão múltipla. Quais as estatísticas que o módulo utiliza depende do tipo de modelo que está a avaliar. Diferentes estatísticas são usadas para avaliar modelos de classificação versus modelos de regressão.  

4.  Quando o processo de construção e avaliação estiver concluído para todas as dobras, o Cross Validate Model gera um conjunto de métricas de desempenho e resultados pontuados para todos os dados. Reveja estas métricas para ver se qualquer dobra tem uma precisão elevada ou baixa. 

### <a name="advantages-of-cross-validation"></a>Vantagens da validação cruzada

Uma forma diferente e comum de avaliar um modelo é dividir os dados num conjunto de treino e teste utilizando [Dados Split](split-data.md)e, em seguida, validar o modelo nos dados de formação. Mas a validação cruzada oferece algumas vantagens:  

-   A validação cruzada utiliza mais dados de teste.

    A validação cruzada mede o desempenho do modelo com os parâmetros especificados num espaço de dados maior. Ou seja, a validação cruzada utiliza todo o conjunto de dados de treino tanto para treino como para avaliação, em vez de uma parte. Em contraste, se validar um modelo utilizando dados gerados a partir de uma divisão aleatória, normalmente avalia o modelo em apenas 30% ou menos dos dados disponíveis.  

    No entanto, como a validação cruzada treina e valida o modelo várias vezes sobre um conjunto de dados maior, é muito mais intensivo computacionalmente. Demora muito mais do que validar numa divisão aleatória.  

-   A validação cruzada avalia tanto o conjunto de dados como o modelo.

    A validação cruzada não mede simplesmente a precisão de um modelo. Também lhe dá uma ideia de quão representativo é o conjunto de dados e quão sensível o modelo pode ser para variações nos dados.  

## <a name="how-to-use-cross-validate-model"></a>Como usar o Modelo De Validação Cruzada

A validação cruzada pode demorar muito tempo a ser executada se o seu conjunto de dados for grande.  Assim, pode utilizar o Cross Validate Model na fase inicial de construção e teste do seu modelo. Nessa fase, pode avaliar a bondade dos parâmetros do modelo (assumindo que o tempo de computação é tolerável). Em seguida, pode treinar e avaliar o seu modelo utilizando os parâmetros estabelecidos com os módulos [Modelo de Comboio](train-model.md) e Avaliar os módulos do Modelo de [Comboio.](evaluate-model.md)

Neste cenário, ambos treinam e testam o modelo utilizando o Cross Validate Model.

1. Adicione o módulo Cross Validate Model ao seu pipeline. Pode encontrá-lo no designer de Machine Learning Azure, na categoria **Model Scoring & Evaluation.** 

2. Ligue a saída de qualquer modelo de classificação ou regressão. 

    Por exemplo, se estiver a usar a Árvore de Decisão Impulsionada de **Duas Classes** para classificação, configure o modelo com os parâmetros que deseja. Em seguida, arraste um conector da porta **modelo não treinada** do classificador para a porta correspondente do Modelo Cross Validate. 

    > [!TIP] 
    > Não é preciso treinar o modelo, porque o Cross-Validate Model treina automaticamente o modelo como parte da avaliação.  
3.  Na porta **dataset** do Cross Validate Model, ligue qualquer conjunto de dados de treino rotulado.  

4.  No painel direito do Cross Validate Model, clique na **coluna Editar**. Selecione a coluna única que contém a etiqueta de classe ou o valor previsível. 

5. Defina um valor para o parâmetro de **sementes Aleatórias** se pretender repetir os resultados da validação cruzada através de sucessivas correções nos mesmos dados.  

6. Executar o pipeline.

7. Consulte a secção [resultados](#results) para obter uma descrição dos relatórios.

## <a name="results"></a>Resultados

Depois de todas as iterações estarem completas, cross Validate Model cria pontuações para todo o conjunto de dados. Também cria métricas de desempenho que pode usar para avaliar a qualidade do modelo.

### <a name="scored-results"></a>Resultados pontuados

A primeira saída do módulo fornece os dados de origem para cada linha, juntamente com alguns valores previstos e probabilidades relacionadas. 

Para exibir os resultados, no pipeline, clique com o botão direito do mouse no módulo modelo de validação cruzada. **Selecione Visualizar resultados pontuados**.

| Novo nome da coluna      | Descrição                              |
| -------------------- | ---------------------------------------- |
| Etiquetas pontuadas        | Esta coluna é adicionada no final do conjunto de dados. Contém o valor previsto para cada linha. |
| Probabilidades pontuadas | Esta coluna é adicionada no final do conjunto de dados. Indica a probabilidade estimada do valor em **etiquetas pontuadas**. |
| Número de dobra          | Indica o índice de base zero da dobra a que cada linha de dados foi atribuída durante a validação cruzada. |

 ### <a name="evaluation-results"></a>Resultados da avaliação

O segundo relatório é agrupado por dobras. Lembre-se que durante a execução, cross Validate Model divide aleatoriamente os dados de treino em *n* dobras (por padrão, 10). Em cada iteração sobre o conjunto de dados, o Cross Validate Model utiliza uma dobra como conjunto de dados de validação. Usa as restantes dobras *n-1* para treinar um modelo. Cada um dos modelos *n* é testado com os dados em todas as outras dobras.

Neste relatório, as dobras são listadas pelo valor do índice, por ordem ascendente.  Para encomendar em qualquer outra coluna, pode guardar os resultados como conjunto de dados.

Para exibir os resultados, no pipeline, clique com o botão direito do mouse no módulo modelo de validação cruzada. **Selecione Visualizar resultados de avaliação por dobrar**.


|nome da coluna| Descrição|
|----|----|
|Número de dobragem| Um identificador para cada dobra. Se criasse cinco dobras, haveria cinco subconjuntos de dados, numerados de 0 a 4.
|Número de exemplos em dobra|O número de linhas atribuídas a cada dobra. Devem ser aproximadamente iguais. |


O módulo também inclui as seguintes métricas para cada dobra, dependendo do tipo de modelo que está a avaliar: 

+ **Modelos de classificação:** Precisão, recordação, pontuação F, AUC, precisão  

+ **Modelos de regressão**: Erro absoluto médio, erro quadrado da raiz, erro absoluto relativo, erro quadrado relativo e coeficiente de determinação


## <a name="technical-notes"></a>Notas técnicas  

+ É uma boa prática normalizar conjuntos de dados antes de usá-los para validação cruzada. 

+ Cross Validate Model é muito mais intensivo computacionalmente e demora mais tempo a ser concluído do que se tiver validado o modelo utilizando um conjunto de dados dividido aleatoriamente. A razão é que cross Validate Model treina e valida o modelo várias vezes.

+ Não há necessidade de dividir o conjunto de dados em conjuntos de treino e teste quando se utiliza validação cruzada para medir a precisão do modelo. 


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 

