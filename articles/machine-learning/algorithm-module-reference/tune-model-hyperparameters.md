---
title: Ajustar hiperparâmetros de modelo
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo ajustar hiperparâmetros de modelo no serviço Azure Machine Learning para executar uma limpeza de parâmetro em um modelo para determinar as configurações de parâmetro ideais.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: 06adfe66bfe894d7b3c95e3d416da866c7d103b3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515658"
---
# <a name="tune-model-hyperparameters"></a>Ajustar hiperparâmetros de modelo

Este artigo descreve como usar o módulo [ajustar hiperparâmetros de modelo](tune-model-hyperparameters.md) no designer de Azure Machine Learning (versão prévia) para determinar os hiperparâmetros ideais para um determinado modelo de aprendizado de máquina. O módulo cria e testa vários modelos, usando diferentes combinações de configurações e compara as métricas sobre todos os modelos para obter a combinação de configurações. 

O *parâmetro* de termos e *hiperparâmetro* podem ser confusos. Os *parâmetros* do modelo são o que você definiu no painel Propriedades. Basicamente, esse módulo executa uma *varredura de parâmetro* sobre as configurações de parâmetro especificadas e aprende um conjunto ideal de _hiperparâmetros_, que podem ser diferentes para cada árvore de decisão, conjunto de linhas ou método de regressão específico. O processo de encontrar a configuração ideal às vezes é chamado de *ajuste*. 

O módulo dá suporte ao seguinte método para localizar as configurações ideais para um modelo 

**Treinamento e ajuste integrados**: você configura um conjunto de parâmetros a serem usados e, em seguida, permite que o módulo Itere em várias combinações, medindo a precisão até encontrar um modelo "melhor". Com a maioria dos módulos do aprendiz, você pode escolher quais parâmetros devem ser alterados durante o processo de treinamento e quais devem permanecer fixos.

    Depending on how long you want the tuning process to run, you might decide to exhaustively test all combinations, or you could shorten the process by establishing a grid of parameter combinations and testing a randomized subset of the parameter grid.

 Esse método gera um modelo treinado que você pode salvar para reutilização.  

### <a name="related-tasks"></a>Tarefas relacionadas

+ Antes de ajustar, aplique a seleção de recursos para determinar as colunas ou variáveis que têm o maior valor de informações.

## <a name="how-to-configure-tune-model-hyperparameters"></a>Como configurar hiperparâmetros de modelo de ajuste  

Em geral, aprender os hiperparâmetros ideais para um determinado modelo de aprendizado de máquina requer um pipeline considerável.

### <a name="train-a-model-using-a-parameter-sweep"></a>Treinar um modelo usando uma varredura de parâmetro  

Esta seção descreve como executar uma varredura de parâmetro básica, que treina um modelo usando o módulo [ajustar hiperparâmetros de modelo](tune-model-hyperparameters.md) .

1.  Adicione o módulo [ajustar hiperparâmetros de modelo](tune-model-hyperparameters.md) ao seu pipeline no designer.

2.  Conecte um modelo não treinado à entrada mais à esquerda. 

3. Defina a opção **criar modo de instrutor** como **intervalo de parâmetros** e use o construtor de **intervalo** para especificar um intervalo de valores a serem usados na varredura de parâmetro.  

    Quase todos os módulos de classificação e regressão dão suporte a uma varredura de parâmetro integrada. Para os aprendizes que não dão suporte à configuração de um intervalo de parâmetros, somente os valores de parâmetro disponíveis podem ser testados.

    Você pode definir manualmente o valor para um ou mais parâmetros e, em seguida, varrer os parâmetros restantes. Isso pode economizar algum tempo.

4.  Adicione o conjunto de dados que você deseja usar para treinamento e conecte-o à entrada intermediária de [hiperparâmetros de modelo de ajuste](tune-model-hyperparameters.md).  

    Opcionalmente, se você tiver um conjunto de dados marcado, você poderá conectá-lo à porta de entrada mais à direita (conjunto de informações de**validação opcional**). Isso permite que você meça a precisão durante o treinamento e o ajuste.

5.  No painel **Propriedades** de [ajustar hiperparâmetros de modelo](tune-model-hyperparameters.md), escolha um valor para o **modo de limpeza de parâmetro**. Esta opção controla como os parâmetros são selecionados.

    - **Grade inteira**: quando você seleciona essa opção, o módulo executa um loop em uma grade predefinida pelo sistema, para tentar combinações diferentes e identificar o melhor aprendiz. Essa opção é útil para casos em que você não sabe quais são as melhores configurações de parâmetro e deseja tentar todas as combinações possíveis de valores.

    - **Varredura aleatória**: quando você seleciona essa opção, o módulo seleciona os valores de parâmetro aleatoriamente em um intervalo definido pelo sistema. Você deve especificar o número máximo de execuções que deseja que o módulo execute. Essa opção é útil para casos em que você deseja aumentar o desempenho do modelo usando as métricas de sua escolha, mas ainda conservar recursos de computação.

    Se você escolher uma varredura aleatória, poderá especificar o **número máximo de execuções em varredura aleatória**, o que significa quantas vezes o modelo deve ser treinado, usando uma combinação aleatória de valores de parâmetro.

6.  Para a **coluna de rótulo**, inicie o seletor de coluna para escolher uma única coluna de rótulo.

7.  **Número máximo de execuções em varredura aleatória**: se você escolher uma varredura aleatória, poderá especificar quantas vezes o modelo deve ser treinado, usando uma combinação aleatória de valores de parâmetro.

    **Número máximo de execuções em grade aleatória**: essa opção também controla o número de iterações em uma amostragem aleatória de valores de parâmetro, mas os valores não são gerados aleatoriamente do intervalo especificado; em vez disso, uma matriz é criada de todas as combinações possíveis de valores de parâmetro e uma amostragem aleatória é feita pela matriz. Esse método é mais eficiente e menos propenso a sobreamostragens regionais ou a subamostragem.

8.  Escolha uma única métrica a ser usada ao **classificar** os modelos.

    Quando você executa uma varredura de parâmetro, todas as métricas aplicáveis para o tipo de modelo são calculadas e retornadas no relatório de **resultados de varredura** . Métricas separadas são usadas para modelos de regressão e classificação.

    No entanto, a métrica escolhida determina como os modelos são classificados. Somente o modelo superior, conforme classificado pela métrica escolhida, é a saída como um modelo treinado a ser usado para pontuação.

9.  Para **semente aleatória**, digite um número a ser usado ao inicializar a limpeza de parâmetro. 

10. Executar o pipeline.

## <a name="results-of-hyperparameter-tuning"></a>Resultados do ajuste de hiperparâmetro

Quando o treinamento for concluído:

+ Para exibir um conjunto de métricas de precisão para o melhor modelo, clique com o botão direito do mouse no módulo, selecione **resultados da varredura**e, em seguida, selecione **Visualizar**.

    Todas as métricas de precisão aplicáveis ao tipo de modelo são saída, mas a métrica que você selecionou para classificação determina qual modelo é considerado "melhor".

+ Para usar o modelo para pontuação em outros pipelines, sem precisar repetir o processo de ajuste, clique com o botão direito do mouse na saída do modelo e selecione **salvar como modelo treinado**. 


## <a name="technical-notes"></a>Notas técnicas

Esta seção contém detalhes de implementação, dicas e respostas para perguntas frequentes.

### <a name="how-a-parameter-sweep-works"></a>Como funciona uma varredura de parâmetro

Esta seção descreve como a varredura de parâmetro funciona em geral e como as opções neste módulo interagem.

Quando você configura uma varredura de parâmetro, define o escopo da sua pesquisa, para usar um número finito de parâmetros selecionados aleatoriamente ou uma pesquisa exaustiva sobre um espaço de parâmetro que você define.

+ **Varredura aleatória**: essa opção treina um modelo usando um número definido de iterações. 

     Você especifica um intervalo de valores para iteração e o módulo usa um subconjunto escolhido aleatoriamente desses valores.  Os valores são escolhidos com substituição, o que significa que números anteriormente escolhidos em Random não são removidos do pool de números disponíveis. Assim, a possibilidade de qualquer valor selecionado permanecerá a mesma em todos os passos.  

+ **Grade inteira**: a opção de usar a grade inteira significa apenas que: cada combinação é testada. Essa opção pode ser considerada a mais completa, mas requer mais tempo. 

### <a name="controlling-the-length-and-complexity-of-training"></a>Controlando o tamanho e a complexidade do treinamento

A iteração em várias combinações de configurações pode ser demorada, portanto, o módulo fornece várias maneiras de restringir o processo:

+ Limitar o número de iterações usadas para testar um modelo
+ Limitar o espaço de parâmetro
+ Limitar o número de iterações e o espaço de parâmetro

É recomendável que você pipeline com as configurações para determinar o método mais eficiente de treinamento em um determinado conjunto de dado e modelo.

### <a name="choosing-an-evaluation-metric"></a>Escolhendo uma métrica de avaliação

Um relatório que contém a precisão de cada modelo é apresentado no final para que você possa examinar os resultados da métrica. Um conjunto uniforme de métricas é usado para todos os modelos de classificação binária, a precisão é usada para todos os modelos de classificação de várias classes e um conjunto diferente de métricas é usado para modelos de regressão. No entanto, durante o treinamento, você deve escolher uma **única** métrica a ser usada na classificação dos modelos gerados durante o processo de ajuste. Você pode achar que a melhor métrica varia, dependendo do problema de sua empresa, e o custo de falsos positivos e falsos negativos.

#### <a name="metrics-used-for-binary-classification"></a>Métricas usadas para classificação binária

-   **Precisão** A proporção de resultados verdadeiros para casos totais.  

-   **Precisão** A proporção de resultados verdadeiros para resultados positivos.  

-   **Cancelar** A fração de todos os resultados corretos em todos os resultados.  

-   **Pontuação F** Uma medida que equilibra precisão e RECALL.  

-   **Auc** Um valor que representa a área sob a curva quando falsos positivos são plotados no eixo x e os positivos verdadeiros são plotados no eixo y.  

-   **Perda de log média** A diferença entre duas distribuições de probabilidade: a verdadeira, e a do modelo.  

#### <a name="metrics-used-for-regression"></a>Métricas usadas para regressão

-   Média de **erros absolutos** calcula todos os erros no modelo, em que o erro significa a distância do valor previsto do valor verdadeiro. Geralmente abreviado como **Mae**.  

-   A **raiz do erro de quadrado médio** mede a média dos quadrados dos erros e, em seguida, usa a raiz desse valor. Abreviado frequentemente como **RMSE**  

-   **Erro absoluto relativo** representa o erro como uma porcentagem do valor verdadeiro.  

-   **Erro quadrado relativo** normaliza o erro quadrado total ao dividir pelo erro quadrado total dos valores previstos.  

-   **Coeficiente de determinação** Um único número que indica quão bem os dados se ajustam a um modelo. Um valor de um significa que o modelo corresponde exatamente aos dados; um valor de zero significa que os dados são aleatórios ou não podem se ajustar ao modelo. Geralmente chamado de **r<sup>2</sup>** , **r<sup>2</sup>** ou **r-quadrado**.  

### <a name="modules-that-do-not-support-a-parameter-sweep"></a>Módulos que não dão suporte a uma varredura de parâmetro

Quase todos os aprendizes no Azure Machine Learning dão suporte à validação cruzada com uma varredura de parâmetro integrada, que permite escolher os parâmetros com os quais o pipeline será escolhido. Se o aprendiz não der suporte à definição de um intervalo de valores, você ainda poderá usá-lo em validação cruzada. Nesse caso, um intervalo de valores permitidos é selecionado para a varredura. 


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning serviço. 

