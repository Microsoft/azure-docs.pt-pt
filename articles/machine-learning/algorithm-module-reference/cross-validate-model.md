---
title: 'Modelo de validação cruzada: referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo modelo de validação cruzada no serviço de Azure Machine Learning para validação cruzada de estimativas de parâmetro para modelos de classificação ou regressão ao particionar os dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: a5eea61ee8284010531e80e17bf1110ab470d04c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512850"
---
# <a name="cross-validate-model"></a>Modelo de validação cruzada

Este artigo descreve como usar o módulo **modelo de validação cruzada** no designer de Azure Machine Learning (versão prévia). A *validação cruzada* é uma técnica importante usada com frequência no aprendizado de máquina para avaliar a variabilidade de um conjunto de dados e a confiabilidade de qualquer modelo treinado usando os mesmos.  

O módulo **modelo de validação cruzada** usa como entrada um conjunto de dados rotulado, junto com um modelo de classificação ou regressão não treinado. Ele divide o conjunto de valores em alguns subconjuntos (*dobras*), cria um modelo em cada dobra e retorna um conjunto de estatísticas de precisão para cada dobra. Ao comparar as estatísticas de precisão de todas as dobras, você pode interpretar a qualidade do conjunto de dados e entender se o modelo é suscetível a variações nos dados.  

A validação cruzada também retorna resultados previstos e probabilidades para o conjunto de, para que você possa avaliar a confiabilidade das previsões.  

### <a name="how-cross-validation-works"></a>Como funciona a validação cruzada

1. A validação cruzada divide aleatoriamente os dados de treinamento em várias partições, também chamadas de *dobras*. 

    + O algoritmo assume o padrão de 10 dobras se você não tiver particionado anteriormente o conjunto de os. 
    + Para dividir o conjunto de espaço em um número diferente de dobras, você pode usar o módulo [partição e exemplo](partition-and-sample.md) e indicar quantas dobras deseja usar.  

2.  O módulo separa os dados na dobra 1 para usar para validação (isso às vezes é chamado de *dobra*de controle) e usa as dobras restantes para treinar um modelo. 

    Por exemplo, se você criar cinco dobras, o módulo geraria cinco modelos durante a validação cruzada, cada modelo treinado usando 4/5 dos dados e testado no 1/5 restante.  

3.  Durante o teste do modelo para cada dobra, várias estatísticas de precisão são avaliadas. Quais estatísticas são usadas depende do tipo de modelo que você está avaliando. Estatísticas diferentes são usadas para avaliar modelos de classificação versus modelos de regressão.  

4.  Quando o processo de criação e avaliação é concluído para todas as dobras, o **modelo de validação cruzada** gera um conjunto de métricas de desempenho e resultados pontuados para todos os dados. Você deve examinar essas métricas para ver se qualquer dobra tem uma precisão muito alta ou baixa 

### <a name="advantages-of-cross-validation"></a>Vantagens da validação cruzada

Uma maneira diferente e comum de avaliar um modelo é dividir os dados em um conjunto de treinamento e teste usando [dados divididos](split-data.md)e, em seguida, validar o modelo nos dados de treinamento. No entanto, a validação cruzada oferece algumas vantagens:  

-   A validação cruzada usa mais dados de teste.

     A validação cruzada mede o desempenho do modelo com os parâmetros especificados em um espaço de dados maior. Ou seja, a validação cruzada usa todo o conjunto de teste de treinamento para treinamento e avaliação, em vez de alguma parte. Por outro lado, se você validar um modelo usando dados gerados de uma divisão aleatória, normalmente você avaliará o modelo somente em 30% ou menos dos dados disponíveis.  

     No entanto, como a validação cruzada treina e valida o modelo várias vezes em um conjunto de informações maior, ele é muito mais intensivo computacionalmente e demora muito mais do que a validação em uma divisão aleatória.  

-   A validação cruzada avalia o conjunto de e o modelo.

     A validação cruzada não simplesmente mede a precisão de um modelo, mas também dá a você uma ideia de como o representativo do conjunto de dados e de quão confidencial o modelo pode ser para as variações deles.  

## <a name="how-to-use-cross-validate-model"></a>Como usar o modelo de validação cruzada

A validação cruzada pode levar muito tempo para ser executada se o conjunto de seus conjuntos de seus for grande.  Portanto, você pode usar o **modelo de validação cruzada** na fase inicial de criação e teste do modelo, para avaliar a incerteza dos parâmetros do modelo (supondo que o tempo de computação seja tolerável) e, em seguida, treinar e avaliar seu modelo usando o parâmetros estabelecidos com os módulos modelo de [treinamento](train-model.md) e [avaliar modelo](evaluate-model.md) .

Nesse cenário, você treina e testa o modelo usando o **modelo de validação cruzada**.

1. Adicione o módulo **modelo de validação cruzada** ao seu pipeline. Você pode encontrá-lo no Azure Machine Learning designer, na categoria de **avaliação de Pontuação do modelo &** . 

2. Conecte a saída de qualquer modelo de **classificação** ou **regressão** . 

    Por exemplo, se você estiver usando uma **máquina de ponto de Bayes de duas classes** para classificação, configure o modelo com os parâmetros desejados e arraste um conector da porta de **modelo não treinada** do classificador para a porta correspondente de **validação cruzada Modelo**. 

    > [!TIP] 
    > O modelo não precisa ser treinado porque o **modelo de validação cruzada** treina automaticamente o modelo como parte da avaliação.  
3.  Na porta do **conjunto** de um **modelo de validação cruzada**, conecte qualquer conjunto de DataSet de treinamento rotulado.  

4.  No painel **Propriedades** do **modelo de validação cruzada**, clique em **Iniciar seletor de coluna** e escolha a única coluna que contém o rótulo de classe ou o valor previsível. 

5. Defina um valor para o parâmetro **semente aleatória** se você quiser poder repetir os resultados da validação cruzada entre execuções sucessivas nos mesmos dados.  

6.  Executar o pipeline.

7. Consulte a seção [resultados](#results) para obter uma descrição dos relatórios.

    Para obter uma cópia do modelo para reutilização posterior, clique com o botão direito do mouse na saída do módulo que contém o algoritmo (por exemplo, a **máquina de ponto de Bayes de duas classes**) e clique em **salvar como modelo treinado**.

## <a name="results"></a>Resultados

Após a conclusão de todas as iterações, o **modelo de validação cruzada** cria pontuações para todo o conjunto de resultados, bem como as métricas de desempenho que você pode usar para avaliar a qualidade do modelo.

### <a name="scored-results"></a>Resultados pontuados

A primeira saída do módulo fornece os dados de origem para cada linha, junto com alguns valores previstos e probabilidades relacionadas. 

Para exibir esses resultados, no pipeline, clique com o botão direito do mouse no módulo **modelo de validação cruzada** , selecione **resultados pontuados**e clique em **Visualizar**.

| Nome da nova coluna      | Descrição                              |
| -------------------- | ---------------------------------------- |
| Rótulos pontuados        | Essa coluna é adicionada ao final do conjunto de e contém o valor previsto para cada linha |
| Probabilidades pontuadas | Essa coluna é adicionada ao final do conjunto de e indica a probabilidade estimada do valor em **Rótulos pontuados**. |
| Número de dobra          | Indica o índice baseado em 0 da dobra de cada linha de dados foi atribuída durante a validação cruzada. |

 ### <a name="evaluation-results"></a>Resultados da avaliação

O segundo relatório é agrupado por dobras. Lembre-se de que, durante a execução, o **modelo de validação cruzada** divide aleatoriamente os dados de treinamento em *n* dobras (por padrão, 10). Em cada iteração sobre o conjunto de cada, o **modelo de validação cruzada** usa uma dobra como um conjunto de uma validação e usa as *n-1* dobras restantes para treinar um modelo. Cada um dos modelos *n* é testado em relação aos dados em todas as outras dobras.

Neste relatório, as dobras são listadas por valor de índice, em ordem crescente.  Para fazer a ordem em qualquer outra coluna, você pode salvar os resultados como um conjunto de um.

Para exibir esses resultados, no pipeline, clique com o botão direito do mouse no módulo **modelo de validação cruzada** , selecione **resultados da avaliação por dobra**e clique em **Visualizar**.


|Nome da coluna| Descrição|
|----|----|
|Número de dobra| Um identificador para cada dobra. Se você criou 5 dobras, haveria 5 subconjuntos de dados, numerados de 0 a 4.
|Número de exemplos em dobra|O número de linhas atribuídas a cada dobra. Eles devem ser aproximadamente iguais. |


Além disso, as métricas a seguir são incluídas para cada dobra, dependendo do tipo de modelo que você está avaliando. 

+ **Modelos de classificação**: precisão, recall, F-score, AUC, precisão  

+ **Modelos de regressão**: erro absoluto médio, erro de raiz quadrada média, erro absoluto relativo, erro de quadrado relativo e coeficiente de determinação


## <a name="technical-notes"></a>Notas técnicas  

+ É uma prática recomendada normalizar conjuntos de valores antes de usá-los para validação cruzada. 

+ Como o **modelo de validação cruzada** treina e valida o modelo várias vezes, é muito mais intensivo computacionalmente e leva mais tempo para ser concluído do que se você validou o modelo usando um conjunto de informações dividido aleatoriamente. 

+ Não é necessário dividir o conjunto de linhas em conjuntos de treinamento e teste quando você usa a validação cruzada para medir a precisão do modelo. 


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning serviço. 

