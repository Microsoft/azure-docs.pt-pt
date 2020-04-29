---
title: Ajustar hiperparâmetros do modelo
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo de hiperparâmetros do Modelo tune em Azure Machine Learning para efetuar uma varredura de parâmetronum modelo para determinar as definições ideais para os parâmetros.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: ff0ccbf201f2b83dd446859d8054d115a70f402e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80064166"
---
# <a name="tune-model-hyperparameters"></a>Ajustar hiperparâmetros do modelo

Este artigo descreve como utilizar o módulo de hiperparâmetros do Modelo tune em azure machine learning designer (pré-visualização). O objetivo é determinar os hiperparâmetros ideais para um modelo de aprendizagem automática. O módulo constrói e testa vários modelos utilizando diferentes combinações de configurações. Compara métricas em todos os modelos para obter as combinações de configurações. 

Os termos *parâmetro* e *hiperparâmetro* podem ser confusos. Os *parâmetros* do modelo são o que se define no painel direito do módulo. Basicamente, este módulo executa uma varredura de *parâmetro* sobre as definições especificadas do parâmetro. Aprende um conjunto ideal de _hiperparâmetros,_ que podem ser diferentes para cada árvore de decisão específica, conjunto de dados ou método de regressão. O processo de encontrar a configuração ideal é por vezes chamado *de afinação.* 

O módulo suporta o seguinte método para encontrar as configurações ideais para um modelo: *comboio integrado e melodia.* Neste método, configura um conjunto de parâmetros para utilizar. Em seguida, deixe o módulo iterar sobre várias combinações. O módulo mede a precisão até encontrar um modelo "melhor". Com a maioria dos módulos de aprendizagem, pode escolher quais os parâmetros que devem ser alterados durante o processo de treino, e quais devem permanecer fixos.

Dependendo do tempo que pretende que o processo de afinação decorra, pode decidir testar exaustivamente todas as combinações. Ou pode encurtar o processo estabelecendo uma grelha de combinações de parâmetros e testando um subconjunto aleatório da grelha do parâmetro.

Este método gera um modelo treinado que pode economizar para reutilização.  

> [!TIP] 
> Pode fazer uma tarefa relacionada. Antes de começar a sintonizar, aplique a seleção de funcionalidades para determinar as colunas ou variáveis que têm o maior valor de informação.

## <a name="how-to-configure-tune-model-hyperparameters"></a>Como configurar hiperparâmetros do modelo tune  

Aprender os hiperparâmetros ideais para um modelo de aprendizagem automática requer uma utilização considerável de gasodutos.

### <a name="train-a-model-by-using-a-parameter-sweep"></a>Treine um modelo usando uma varredura de parâmetros  

Esta secção descreve como realizar uma varredura de parâmetros básicos, que treina um modelo utilizando o módulo de hiperparâmetros do Modelo tune.

1.  Adicione o módulo de hiperparâmetros do Modelo tune ao seu pipeline no designer.

2.  Ligue um modelo destreinado à entrada mais à esquerda. 



4.  Adicione o conjunto de dados que pretende utilizar para treinar e conecte-o à entrada média dos Hiperparâmetros do Modelo tune.  

    Opcionalmente, se tiver um conjunto de dados marcado, pode ligá-lo à porta de entrada mais correta (conjunto de dados de**validação opcional).** Isto permite medir a precisão durante o treino e a sintonização.

5.  No painel certo dos hiperparâmetros do Modelo tune, escolha um valor para o **modo de varrimento**do parâmetro . Esta opção controla a forma como os parâmetros são selecionados.

    - **Toda**a grelha : Quando seleciona esta opção, o módulo passa por cima de uma grelha predefinida pelo sistema, para experimentar diferentes combinações e identificar o melhor aprendiz. Esta opção é útil quando não se sabe quais as melhores definições de parâmetros e quer experimentar todas as combinações possíveis de valores.

    - **Varredura aleatória**: Quando selecionar esta opção, o módulo selecionará aleatoriamente valores de parâmetros num intervalo definido pelo sistema. Deve especificar o número máximo de execuções que pretende executar o módulo. Esta opção é útil quando pretende aumentar o desempenho do modelo utilizando as métricas da sua escolha, mas conservando ainda os recursos de computação.    

6.  Para **a coluna Etiquetar,** abra o seletor de colunas para escolher uma única coluna de etiquetas.

7.  Escolha o número de execuções:

    1. **Número máximo de execuções em varredura aleatória**: Se escolher uma varredura aleatória, pode especificar quantas vezes o modelo deve ser treinado, utilizando uma combinação aleatória de valores de parâmetros.

8.  Para **ranking,** escolha uma única métrica para usar para classificar os modelos.

    Quando executa uma varredura de parâmetros, o módulo calcula todas as métricas aplicáveis para o tipo de modelo e devolve-as no relatório de resultados da **Varredura.** O módulo utiliza métricas separadas para modelos de regressão e classificação.

    No entanto, a métrica que escolher determina como os modelos são classificados. Apenas o modelo de topo, classificado pela métrica escolhida, é a saída como um modelo treinado para usar para pontuar.

9.  Para **sementes aleatórias,** introduza um número a utilizar para iniciar a varredura do parâmetro. 

10. Submeta o oleoduto.

## <a name="results-of-hyperparameter-tuning"></a>Resultados da afinação do hiperparâmetro

Quando o treino estiver completo:

+ Para ver um conjunto de métricas de precisão para o melhor modelo, clique no módulo à direita e, em seguida, **selecione Visualize**.

    A saída inclui todas as métricas de precisão que se aplicam ao tipo de modelo, mas a métrica que selecionou para o ranking determina qual o modelo considerado "melhor".

+ Para guardar uma imagem do modelo treinado, selecione o separador **Saídas** no painel direito do módulo **modelo Train.** Selecione o ícone do conjunto de **dados Register** para salvar o modelo como um módulo reutilizável.


## <a name="technical-notes"></a>Notas técnicas

Esta secção contém detalhes e dicas de implementação.

### <a name="how-a-parameter-sweep-works"></a>Como funciona uma varredura de parâmetros

Quando configura uma varredura de parâmetros, define o âmbito da sua pesquisa. A pesquisa pode usar um número finito de parâmetros selecionados aleatoriamente. Ou pode ser uma busca exaustiva sobre um espaço de parâmetroque se define.

+ **Varredura aleatória**: Esta opção treina um modelo utilizando um número conjunto de iterações. 

  Especifica-se uma gama de valores ao iterato sobre, e o módulo utiliza um subconjunto escolhido aleatoriamente desses valores. Os valores são escolhidos com substituição, o que significa que os números anteriormente escolhidos aleatoriamente não são removidos do conjunto de números disponíveis. Assim, a possibilidade de qualquer valor ser selecionado permanece a mesma em todos os passes.  

+ **Grelha inteira**: A opção de utilizar toda a grelha significa que todas as combinações são testadas. Esta opção é a mais minuciosa, mas requer mais tempo. 

### <a name="controlling-the-length-and-complexity-of-training"></a>Controlando o comprimento e a complexidade da formação

Iterar sobre muitas combinações de configurações pode ser demorado, por isso o módulo fornece várias maneiras de conter o processo:

+ Limite o número de iterações usadas para testar um modelo.
+ Limite o espaço do parâmetro.
+ Limite tanto o número de iterações como o espaço do parâmetro.

Recomendamos que faça o pipeline com as definições para determinar o método de treino mais eficiente num determinado conjunto de dados e modelo.

### <a name="choosing-an-evaluation-metric"></a>Escolher uma métrica de avaliação

No final dos testes, o modelo apresenta um relatório que contém a precisão de cada modelo para que possa rever os resultados métricos:

- Um conjunto uniforme de métricas é usado para todos os modelos de classificação binária.
- A precisão é usada para todos os modelos de classificação multi-classe.
- Um conjunto diferente de métricas é usado para modelos de regressão. 

No entanto, durante o treino, deve escolher uma *única* métrica para utilizar no ranking dos modelos que são gerados durante o processo de afinação. Pode descobrir que a melhor métrica varia, dependendo do seu problema de negócio e do custo de falsos positivos e falsos negativos.

#### <a name="metrics-used-for-binary-classification"></a>Métricas utilizadas para a classificação binária

-   **A precisão** é a proporção de resultados reais para o total de casos.  

-   **Precisão** é a proporção de resultados verdadeiros para resultados positivos.  

-   **Recorde-se** que a fração de todos os resultados corretos sobre todos os resultados.  

-   **F-score** é uma medida que equilibra precisão e recordação.  

-   **A UC** é um valor que representa a área sob a curva quando falsos positivos são traçados no eixo x e verdadeiros positivos são traçados no eixo y.  

-   **Perda média de registo** é a diferença entre duas distribuições de probabilidade: a verdadeira, e a do modelo.  

#### <a name="metrics-used-for-regression"></a>Métricas usadas para regressão

-   **Erro absoluto médio** media todos os erros do modelo, *onde* erro significa a distância do valor previsto do valor real. É muitas vezes abreviado como *MAE.*  

-   **A raiz do erro quadrado médio** mede a média dos quadrados dos erros, e depois toma a raiz desse valor. É frequentemente abreviado como *RMSE.*  

-   **Erro absoluto relativo** representa o erro em percentagem do valor real.  

-   **Erro quadrado relativo** normaliza o erro quadrado total dividindo-se pelo erro quadrado total dos valores previstos.  

-   **O coeficiente de determinação** é um único número que indica o quão bem os dados se encaixam num modelo. Um valor de um significa que o modelo corresponde exatamente aos dados. Um valor de zero significa que os dados são aleatórios ou não podem ser adequados ao modelo. É frequentemente chamado *r<sup>2</sup>*, *R<sup>2</sup>*, ou *r-quadrado*.  

### <a name="modules-that-dont-support-a-parameter-sweep"></a>Módulos que não suportam uma varredura de parâmetros

Quase todos os alunos do Azure Machine Learning suportam validação cruzada com uma varredura de parâmetros integrada, que permite escolher os parâmetros para o pipeline com. Se o aluno não apoiar a definição de uma gama de valores, ainda pode usá-lo em validação cruzada. Neste caso, é selecionada uma gama de valores permitidos para a varredura. 


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 

