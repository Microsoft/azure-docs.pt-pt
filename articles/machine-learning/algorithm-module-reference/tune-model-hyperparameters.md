---
title: Ajustar hiperparâmetros do modelo
titleSuffix: Azure Machine Learning
description: Utilize o módulo Tune Model Hyperparameters no designer para executar uma varredura de parâmetros para sintonizar hiperparpartimentos.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2020
ms.openlocfilehash: 2bbf75ba5de4ad20e11261bdcfd1204b1a0b0766
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93420636"
---
# <a name="tune-model-hyperparameters"></a>Ajustar hiperparâmetros do modelo

Este artigo descreve como usar o módulo Tune Model Hyperparameters no designer Azure Machine Learning. O objetivo é determinar os hiperparmetros ideais para um modelo de aprendizagem automática. O módulo constrói e testa vários modelos utilizando diferentes combinações de configurações. Compara métricas em todos os modelos para obter as combinações de configurações. 

O *parâmetro* de termos e *o hiperparímetro* podem ser confusos. Os *parâmetros* do modelo são o que se define no painel direito do módulo. Basicamente, este módulo executa uma *varredura de parâmetros* sobre as definições de parâmetros especificados. Aprende um conjunto ideal de _hiperparímetros,_ que pode ser diferente para cada decisão específica, conjunto de dados ou método de regressão. O processo de encontrar a configuração ideal é por vezes chamado *de sintonização*. 

O módulo suporta o seguinte método para encontrar as configurações ideais para um modelo: *comboio e sintonização integrados.* Neste método, configura-se um conjunto de parâmetros a utilizar. Em seguida, deixe o módulo iterar sobre várias combinações. O módulo mede a precisão até encontrar um modelo "melhor". Com a maioria dos módulos de aprendizagem, pode escolher quais os parâmetros que devem ser alterados durante o processo de treino, e que devem permanecer fixos.

Dependendo do tempo que pretende que o processo de afinação seja executado, poderá decidir testar exaustivamente todas as combinações. Ou pode encurtar o processo estabelecendo uma grelha de combinações de parâmetros e testando um subconjunto aleatório da grelha de parâmetros.

Este método gera um modelo treinado que pode guardar para reutilização.  

> [!TIP] 
> Pode fazer uma tarefa relacionada. Antes de começar a sintonizar, aplique a seleção de funcionalidades para determinar as colunas ou variáveis que têm o maior valor de informação.

## <a name="how-to-configure-tune-model-hyperparameters"></a>Como configurar hiperparmetros de modelo de sintonização  

Aprender os hiperparmetros ideais para um modelo de aprendizagem automática requer uma utilização considerável de gasodutos.

### <a name="train-a-model-by-using-a-parameter-sweep"></a>Treine um modelo usando uma varredura de parâmetros  

Esta secção descreve como executar uma varredura de parâmetros básicos, que treina um modelo utilizando o módulo Tune Model Hyperparameters.

1.  Adicione o módulo Tune Model Hyperparameters ao seu pipeline no designer.

2.  Ligue um modelo destreinado à entrada mais à esquerda. 

    > [!NOTE] 
    > **Os Hiperparmetros Tune Model** só podem ser ligados a módulos de algoritmos de aprendizagem automática incorporados, e não podem suportar um modelo personalizado construído no **Modelo Create Python.**


3.  Adicione o conjunto de dados que pretende utilizar para o treino e conecte-o à entrada média dos Hiperparímetros do Tune Model.  

    Opcionalmente, se tiver um conjunto de dados marcado, pode conectá-lo à porta de entrada mais à direita **(Conjunto de dados de validação opcional).** Isto permite medir a precisão durante o treino e a sintonização.

4.  No painel direito de Tune Model Hyperparameters, escolha um valor para **o modo de varredura de parâmetros**. Esta opção controla a forma como os parâmetros são selecionados.

    - **Grelha inteira**: Ao selecionar esta opção, o módulo roda sobre uma grelha predefinida pelo sistema, para experimentar diferentes combinações e identificar o melhor aprendiz. Esta opção é útil quando não sabe quais as melhores definições de parâmetros e quer experimentar todas as combinações possíveis de valores.

    - **Varredura aleatória**: Quando selecionar esta opção, o módulo selecionará aleatoriamente os valores dos parâmetros sobre um intervalo definido pelo sistema. Tem de especificar o número máximo de execuções que pretende que o módulo execute. Esta opção é útil quando pretende aumentar o desempenho do modelo utilizando as métricas da sua escolha, mas ainda conservar recursos informáticos.    

5.  Para **a coluna Label,** abra o seletor de colunas para escolher uma única coluna de etiqueta.

6.  Escolha o número de runs:

    - **Número máximo de corridas em varredura aleatória**: Se escolher uma varredura aleatória, pode especificar quantas vezes o modelo deve ser treinado, utilizando uma combinação aleatória de valores de parâmetros.

7.  Para **ranking,** escolha uma única métrica para utilizar para classificar os modelos.

    Quando faz uma varredura de parâmetros, o módulo calcula todas as métricas aplicáveis para o tipo de modelo e devolve-as no relatório **de resultados do Sweep.** O módulo utiliza métricas separadas para modelos de regressão e classificação.

    No entanto, a métrica que escolhe determina como os modelos estão classificados. Apenas o modelo de topo, classificado pela métrica escolhida, é a saída como um modelo treinado para usar para pontuar.

8.  Para **sementes aleatórias,** introduza um número a utilizar para iniciar a varredura do parâmetro. 

9. Envie o oleoduto.

## <a name="results-of-hyperparameter-tuning"></a>Resultados da afinação do hiperparímetro

Quando o treino estiver completo:

+ Para visualizar os resultados da varredura, pode clicar no módulo à direita e, em seguida, **selecionar Visualizar**, ou clicar na porta de saída esquerda do módulo para visualizar.

    Os **resultados do Sweep** incluem todas as métricas de varredura de parâmetros e precisão que se aplicam ao tipo de modelo, e a métrica que selecionou para o ranking determina qual o modelo considerado "melhor".

+ Para guardar uma imagem do modelo treinado, selecione o **separador Saídas+logs** no painel direito do módulo **modelo Train.** Selecione o ícone **do conjunto de dados registar** para guardar o modelo como um módulo reutilizável.


## <a name="technical-notes"></a>Notas técnicas

Esta secção contém detalhes e dicas de implementação.

### <a name="how-a-parameter-sweep-works"></a>Como funciona uma varredura de parâmetros

Quando configurar uma varredura de parâmetros, define o âmbito da sua pesquisa. A procura pode usar um número finito de parâmetros selecionados aleatoriamente. Ou pode ser uma pesquisa exaustiva sobre um espaço de parâmetro que você define.

+ **Varredura aleatória**: Esta opção treina um modelo utilizando um conjunto de iterações. 

  Especifica uma gama de valores para iterar e o módulo utiliza um subconjunto escolhido aleatoriamente desses valores. Os valores são escolhidos com substituição, o que significa que os números previamente escolhidos aleatoriamente não são removidos do conjunto de números disponíveis. Assim, a chance de qualquer valor ser selecionado permanece o mesmo em todos os passes.  

+ **Grelha inteira**: A opção de utilizar toda a grelha significa que todas as combinações são testadas. Esta opção é a mais completa, mas requer mais tempo. 

### <a name="controlling-the-length-and-complexity-of-training"></a>Controlando o comprimento e a complexidade do treino

A iteração sobre muitas combinações de configurações pode ser demorado, pelo que o módulo fornece várias formas de conter o processo:

+ Limitar o número de iterações utilizadas para testar um modelo.
+ Limite o espaço dos parâmetros.
+ Limite tanto o número de iterações como o espaço dos parâmetros.

Recomendamos que se mete com as definições para determinar o método de treino mais eficiente num determinado conjunto de dados e modelo.

### <a name="choosing-an-evaluation-metric"></a>Escolher uma métrica de avaliação

No final dos testes, o modelo apresenta um relatório que contém a precisão de cada modelo para que possa rever os resultados da métrica:

- Um conjunto uniforme de métricas é usado para todos os modelos de classificação binária.
- A precisão é utilizada para todos os modelos de classificação de várias classes.
- Um conjunto diferente de métricas é usado para modelos de regressão. 

No entanto, durante o treino, deve escolher uma *única* métrica para utilizar no ranking dos modelos gerados durante o processo de afinação. Pode descobrir que a melhor métrica varia, dependendo do seu problema de negócio e do custo de falsos positivos e falsos negativos.

#### <a name="metrics-used-for-binary-classification"></a>Métricas utilizadas para a classificação binária

-   **Precisão** é a proporção de resultados verdadeiros para os casos totais.  

-   **Precisão** é a proporção de resultados verdadeiros para resultados positivos.  

-   **A recuperação** é a fração de todos os resultados corretos sobre todos os resultados.  

-   **F-score** é uma medida que equilibra a precisão e a recuperação.  

-   **AUC** é um valor que representa a área sob a curva quando falsos positivos são traçados no eixo x e os verdadeiros positivos são traçados no eixo y.  

-   **Perda média de log** é a diferença entre duas distribuições de probabilidade: a verdadeira, e a do modelo.  

#### <a name="metrics-used-for-regression"></a>Métricas utilizadas para regressão

-   **Erro absoluto médio** media todos os erros no modelo, onde *erro* significa a distância do valor previsto do valor real. É frequentemente abreviado como *MAE.*  

-   **A raiz do erro quadrado médio** mede a média dos quadrados dos erros, e depois toma a raiz desse valor. É frequentemente abreviado como *RMSE.*  

-   **Erro absoluto relativo** representa o erro em percentagem do valor real.  

-   **Erro quadrado relativo** normaliza o erro total ao quadrado dividindo-se pelo erro total ao quadrado dos valores previstos.  

-   **Coeficiente de determinação** é um único número que indica como os dados se encaixam bem num modelo. Um valor de um significa que o modelo corresponde exatamente aos dados. Um valor de zero significa que os dados são aleatórios ou não podem ser adequados ao modelo. É frequentemente chamado *r <sup>2</sup>*, *R <sup>2,</sup>* ou *r-squared*.  

### <a name="modules-that-dont-support-a-parameter-sweep"></a>Módulos que não suportam uma varredura de parâmetros

Quase todos os alunos do Azure Machine Learning apoiam a validação cruzada com uma varredura de parâmetros integrada, que permite escolher os parâmetros para pipeline com. Se o aprendiz não suportar a definição de uma gama de valores, ainda pode usá-lo em validação cruzada. Neste caso, é selecionada uma gama de valores permitidos para a varredura. 


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 

