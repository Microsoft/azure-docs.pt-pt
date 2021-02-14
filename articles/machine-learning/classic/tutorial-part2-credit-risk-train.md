---
title: 'ML Studio (clássico) tutorial 2: Modelos de risco de crédito de comboio - Azure'
description: Um tutorial detalhado que mostra como criar uma solução de análise preditiva para a avaliação do risco de crédito no Azure Machine Learning Studio (clássico). Este tutorial é a segunda parte de uma série tutorial em três partes. Mostra como treinar e avaliar modelos.
keywords: risco de crédito, solução de análise preditiva, avaliação de riscos
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: 677c5b791f475468fbcf15c81fbabdcdbb1972de
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100517455"
---
# <a name="tutorial-2-train-credit-risk-models---azure-machine-learning-studio-classic"></a>Tutorial 2: Modelos de risco de crédito de comboio - Azure Machine Learning Studio (clássico)

**APLICA-SE A:** ![ Esta é uma marca de verificação, o que significa que este artigo se aplica ao Machine Learning Studio (clássico).  ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (clássico)   ![ Este é um X, o que significa que este artigo se aplica ao Azure Machine Learning.](../../../includes/media/aml-applies-to-skus/no.png)[ Aprendizagem de Máquinas Azure](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

Neste tutorial, você dá uma olhada alargada no processo de desenvolvimento de uma solução de análise preditiva. Desenvolve-se um modelo simples no Machine Learning Studio (clássico).  Em seguida, implementa o modelo como um serviço web Azure Machine Learning.  Este modelo implementado pode fazer previsões usando novos dados. Este tutorial é **a segunda parte de uma série tutorial em três partes.**

Suponhamos que precisa de prever o risco de crédito de um indivíduo com base nas informações fornecidas sobre uma aplicação de crédito.  

A avaliação do risco de crédito é um problema complexo, mas este tutorial irá simplificar um pouco. Você vai usá-lo como um exemplo de como você pode criar uma solução de análise preditiva usando Microsoft Azure Machine Learning Studio (clássico). Você usará o Azure Machine Learning Studio (clássico) e um serviço web machine learning para esta solução.  

Neste tutorial em três partes, começa-se com dados de risco de crédito disponíveis ao público.  Depois desenvolve-se e treina-se um modelo preditivo.  Finalmente implementa o modelo como um serviço web.

Na [primeira parte do tutorial,](tutorial-part1-credit-risk.md)criou um espaço de trabalho do Machine Learning Studio (clássico), carregou dados e criou uma experiência.

Nesta parte do tutorial:
 
> [!div class="checklist"]
> * Treine vários modelos
> * Marque e avalie os modelos


Na [terceira parte do tutorial,](tutorial-part3-credit-risk-deploy.md)irá implementar o modelo como um serviço web.

## <a name="prerequisites"></a>Pré-requisitos

Complete [a primeira parte do tutorial.](tutorial-part1-credit-risk.md)

## <a name="train-multiple-models"></a><a name="train"></a>Treine vários modelos

Um dos benefícios de usar o Azure Machine Learning Studio (clássico) para criar modelos de machine learning é a capacidade de experimentar mais do que um tipo de modelo de cada vez numa única experiência e comparar os resultados. Este tipo de experimentação ajuda-o a encontrar a melhor solução para o seu problema.

Na experiência que estamos a desenvolver neste tutorial, irás criar dois tipos diferentes de modelos e depois comparar os resultados de pontuação deles para decidir que algoritmo queres usar na nossa experiência final.  

Há vários modelos que pode escolher. Para ver os modelos disponíveis, expanda o nó **machine learning** na paleta do módulo e, em seguida, expanda o **Modelo Inicialize** e os nós por baixo. Para efeitos desta experiência, você irá selecionar a [máquina de vetor de suporte de duas classes][two-class-support-vector-machine] (SVM) e os módulos de árvore de [decisão reforçada de duas classes.][two-class-boosted-decision-tree]

> [!TIP]
> Para obter ajuda para decidir qual o algoritmo machine learning que melhor se adequa ao problema específico que está a tentar resolver, consulte [Como escolher algoritmos para o Microsoft Azure Machine Learning Studio (clássico)](../how-to-select-algorithms.md).
> 
> 

Você adicionará tanto o módulo [de árvore de decisão reforçada de duas classes][two-class-boosted-decision-tree] e o módulo de máquina de [vetor de suporte de duas classes][two-class-support-vector-machine] nesta experiência.

### <a name="two-class-boosted-decision-tree"></a>Árvore de Decisões Elevada de Duas Classes

Primeiro, crie o modelo de árvore de decisão reforçada.

1. Encontre o módulo [de árvore de decisão reforçada de duas classes][two-class-boosted-decision-tree] na paleta do módulo e arraste-o para a tela.

1. Encontre o módulo [Modelo de Comboio,][train-model] arraste-o para a tela e, em seguida, ligue a saída do módulo [Árvore de Decisão Reforçada de duas classes][two-class-boosted-decision-tree] à porta de entrada esquerda do módulo Modelo de [Comboio.][train-model]
   
   O módulo [de árvore de decisão reforçada de duas classes][two-class-boosted-decision-tree] inicializa o modelo genérico, e o Modelo de [Comboio][train-model] utiliza dados de formação para treinar o modelo. 

1. Ligue a saída esquerda do módulo [executo r][execute-r-script] esquerda ao porta de entrada direita do módulo [Modelo de Comboio][train-model] (neste tutorial utilizou os [dados provenientes do lado esquerdo](#train) do módulo De dados divididos para treino).
   
   > [!TIP]
   > não precisa de duas das entradas e uma das saídas do módulo [Execut R Script][execute-r-script] para esta experiência, para que possa deixá-las desapeadas. 
   > 
   > 

Esta parte da experiência agora se parece com isto:  

![Treinar um modelo](./media/tutorial-part2-credit-risk-train/experiment-with-train-model.png)

Agora precisa de dizer ao módulo [Train Model][train-model] que pretende que o modelo preveja o valor de Risco de Crédito.

1. Selecione o módulo [Modelo de Comboio.][train-model] No painel **propriedades,** clique no **seletor de colunas de lançamento**.

1. No **Diálogo de uma única coluna, digite** "risco de crédito" no campo de pesquisa sob **as Colunas Disponíveis,** selecione "Risco de Crédito" abaixo e clique no botão de seta direita ( **>** ) para mover "Risco de crédito" para **colunas selecionadas**. 

    ![Selecione a coluna de risco de crédito para o módulo Modelo de Comboio](./media/tutorial-part2-credit-risk-train/train-model-select-column.png)

1. Clique na marca de verificação **OK.**

### <a name="two-class-support-vector-machine"></a>Máquina de Vetores de Suporte de Duas Classes

Em seguida, configura o modelo SVM.  

Primeiro, uma pequena explicação sobre a SVM. As árvores de decisão reforçadas funcionam bem com características de qualquer tipo. No entanto, uma vez que o módulo SVM gera um classificador linear, o modelo que gera tem o melhor erro de teste quando todas as funcionalidades numéricas têm a mesma escala. Para converter todas as características numéricas na mesma escala, utilize uma transformação "Tanh" (com o módulo [de Dados Normalizar).][normalize-data] Isto transforma os nossos números na gama [0,1]. O módulo SVM converte as características das cordas em funcionalidades categóricas e, em seguida, para funcionalidades binárias 0/1, para que não seja necessário transformar manualmente as características das cordas. Além disso, não queres transformar a coluna de Risco de Crédito (coluna 21) - é numérica, mas é o valor que estamos a treinar o modelo para prever, por isso tens de a deixar em paz.  

Para configurar o modelo SVM, faça o seguinte:

1. Encontre o módulo [de máquina de vetor de suporte de duas classes][two-class-support-vector-machine] na paleta do módulo e arraste-o para a tela.

1. Clique com o botão direito no módulo [Modelo de Comboio,][train-model] selecione **Copy** e, em seguida, clique com o direito na tela e selecione **Pasta**. A cópia do módulo [Train Model][train-model] tem a mesma seleção de colunas que a original.

1. Ligue a saída do módulo [de vetor de dois classes][two-class-support-vector-machine] à porta de entrada esquerda do segundo módulo do Modelo de [Comboio.][train-model]

1. Encontre o módulo [de dados normalizar][normalize-data] e arraste-o para a tela.

1. Ligue a saída esquerda do módulo [executo r][execute-r-script] esquerda à entrada deste módulo (note que a porta de saída de um módulo pode ser ligada a mais do que um outro módulo).

1. Ligue a porta de saída esquerda do módulo [de dados normalizar][normalize-data] à porta de entrada direita do segundo módulo [Train Model.][train-model]

Esta parte da nossa experiência deve agora ser mais ou menos assim:  

![Treinando o segundo modelo](./media/tutorial-part2-credit-risk-train/svm-model-added.png)

Agora configurar o módulo [de dados normalizar:][normalize-data]

1. Clique para selecionar o módulo [de dados normalizar.][normalize-data] No painel **propriedades,** selecione **Tanh** para o parâmetro **do método de transformação.**

1. Clique no **seletor de colunas de lançamento**, selecione "No columns" para Começar **Com**, selecione **Inclua** no primeiro dropdown, selecione o **tipo de coluna** no segundo dropdown e selecione **Nummérico** no terceiro dropdown. Isto especifica que todas as colunas numéricas (e apenas numéricas) são transformadas.

1. Clique no sinal de mais (+) à direita desta linha - isto cria uma linha de dropdowns. **Selecione Excluir** no primeiro dropdown, selecione nomes de **colunas** na segunda redução e introduza "Risco de Crédito" no campo de texto. Isto especifica que a coluna de Risco de Crédito deve ser ignorada (é preciso fazê-lo porque esta coluna é numérica e por isso seria transformada se não a excluísse).

1. Clique na marca de verificação **OK.**  

    ![Selecione colunas para o módulo de dados normalizar](./media/tutorial-part2-credit-risk-train/normalize-data-select-column.png)


O módulo [de dados normalizar][normalize-data] está agora definido para realizar uma transformação de Tanh em todas as colunas numéricas, exceto para a coluna de Risco de Crédito.  

## <a name="score-and-evaluate-the-models"></a>Marque e avalie os modelos

utiliza os dados de teste que foram separados pelo módulo [De Dados Divididos][split] para marcar os nossos modelos treinados. pode então comparar os resultados dos dois modelos para ver quais geraram melhores resultados.  

### <a name="add-the-score-model-modules"></a>Adicione os módulos do Modelo de Pontuação

1. Encontre o módulo ['Modelo de Pontuação'][score-model] e arraste-o para a tela.

1. Ligue o módulo [Modelo][train-model] de Comboio que está ligado ao módulo [Árvore de Decisão Reforçada de duas classes][two-class-boosted-decision-tree] à porta de entrada esquerda do módulo ['Modelo de Pontuação'.][score-model]

1. Ligue o módulo [de script executo r][execute-r-script] direito (os nossos dados de teste) à porta de entrada direita do módulo ['Modelo de Pontuação'.][score-model]

    ![Módulo de modelo de pontuação conectado](./media/tutorial-part2-credit-risk-train/score-model-connected.png)

   
   O módulo ['Modelo de Pontuação'][score-model] pode agora retirar a informação de crédito dos dados de teste, executá-la através do modelo e comparar as previsões que o modelo gera com a coluna de risco de crédito real nos dados de teste.

1. Copie e cole o módulo ['Modelo de Pontuação'][score-model] para criar uma segunda cópia.

1. Ligue a saída do modelo SVM (isto é, a porta de saída do módulo [Modelo de Comboio][train-model] que está ligada ao módulo de máquina de [vetor de suporte de duas classes)][two-class-support-vector-machine] à porta de entrada do segundo módulo [do Modelo de Pontuação.][score-model]

1. Para o modelo SVM, tem de fazer a mesma transformação para os dados de teste como fez com os dados de treino. Por isso, copie e cole o módulo [de Dados Normalizar][normalize-data] para criar uma segunda cópia e conectá-la ao módulo [de Script Execut R][execute-r-script] certo.

1. Ligue a saída esquerda do segundo módulo [de Dados normalizar][normalize-data] à porta de entrada direita do segundo módulo [Do Modelo de Pontuação.][score-model]

    ![Ambos os módulos do Modelo de Pontuação conectados](./media/tutorial-part2-credit-risk-train/both-score-models-added.png)


### <a name="add-the-evaluate-model-module"></a>Adicione o módulo de modelo de avaliação

Para avaliar os dois resultados de pontuação e compará-los, utilize um módulo [De Modelo avaliar.][evaluate-model]  

1. Encontre o módulo [Modelo de Avaliação][evaluate-model] e arraste-o para a tela.

1. Ligue a porta de saída do módulo ['Modelo de Pontuação'][score-model] associado ao modelo de decisão reforçado à porta de entrada esquerda do módulo [Modelo de Avaliação.][evaluate-model]

1. Ligue o outro módulo [do Modelo de Pontuação][score-model] à porta de entrada direita.  

    ![Avaliar módulo de modelo conectado](./media/tutorial-part2-credit-risk-train/evaluate-model-added.png)


### <a name="run-the-experiment-and-check-the-results"></a>Faça a experiência e verifique os resultados

Para executar a experiência, clique no botão **RUN** abaixo da tela. Esta operação poderá demorar alguns minutos. Um indicador de rotação em cada módulo mostra que está em funcionamento e, em seguida, uma marca de verificação verde mostra quando o módulo está terminado. Quando todos os módulos têm uma marca de verificação, a experiência terminou de funcionar.

A experiência deve agora ser mais ou menos assim:  

![Avaliação de ambos os modelos](./media/tutorial-part2-credit-risk-train/final-experiment.png)


Para verificar os resultados, clique na porta de saída do módulo [Modelo avaliar][evaluate-model] e selecione **Visualize**.  

O módulo ['Avaliar Modelo'][evaluate-model] produz um par de curvas e métricas que permitem comparar os resultados dos dois modelos pontuados. Pode ver os resultados como curvas características do operador recetor (ROC), curvas de precisão/recuperação ou curvas de elevação. Os dados adicionais apresentados incluem uma matriz de confusão, valores cumulativos para a área sob a curva (AUC), e outras métricas. Pode alterar o valor do limiar movendo o slider para a esquerda ou para a direita e ver como afeta o conjunto de métricas.  

À direita do gráfico, clique em Conjunto de **dados Marcado** ou **Conjunto de dados Marcado para comparar** para realçar a curva associada e para exibir as métricas associadas abaixo. Na legenda das curvas, "Conjunto de dados pontuado" corresponde à porta de entrada esquerda do módulo [Modelo de Avaliação][evaluate-model] - no nosso caso, este é o modelo de árvore de decisão reforçada. "Conjunto de dados pontuado para comparar" corresponde à porta de entrada certa - o modelo SVM no nosso caso. Quando clica numa destas etiquetas, a curva para este modelo é realçada e as métricas correspondentes são apresentadas, como mostra o gráfico seguinte.  

![Curvas ROC para modelos](./media/tutorial-part2-credit-risk-train/roc-curves.png)

Ao examinar estes valores, pode decidir qual o modelo que está mais próximo de lhe dar os resultados que procura. Pode voltar e iterar a sua experiência alterando os valores dos parâmetros nos diferentes modelos. 

A ciência e a arte de interpretar estes resultados e afinar o desempenho do modelo está fora do âmbito deste tutorial. Para obter ajuda adicional, pode ler os seguintes artigos:
- [Como avaliar o desempenho do modelo no Azure Machine Learning Studio (clássico)](evaluate-model-performance.md)
- [Escolha parâmetros para otimizar os seus algoritmos no Azure Machine Learning Studio (clássico)](algorithm-parameters-optimize.md)
- [Interpretar os resultados do modelo no Azure Machine Learning Studio (clássico)](interpret-model-results.md)

> [!TIP]
> Cada vez que faz a experiência, um registo dessa iteração é mantido na História da Execução. Pode ver estas iterações e regressar a qualquer uma delas, clicando em **VER RUN HISTORY** abaixo da tela. Também pode clicar em **Prior Run** no painel **Propriedades** para voltar à iteração imediatamente anterior à que tem aberta.
> 
> Pode fazer uma cópia de qualquer iteração da sua experiência clicando **em SAVE AS** abaixo da tela. 
> Use as propriedades **resumo** e **descrição** da experiência para manter um registo do que experimentou nas suas iterações de experiência.
> 
> Para obter mais informações, consulte [Gerir iterações experimentais no Azure Machine Learning Studio (clássico)](manage-experiment-iterations.md).  
> 
> 

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, completou estes passos: 
 
> [!div class="checklist"]
> * Criar uma experimentação
> * Treine vários modelos
> * Marque e avalie os modelos

Está agora pronto para implementar modelos para estes dados.

> [!div class="nextstepaction"]
> [Tutorial, parte 3 – Implementar modelos](tutorial-part3-credit-risk-deploy.md)


<!-- Module References -->
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
[edit-metadata]: /azure/machine-learning/studio-module-reference/edit-metadata
[split]: /azure/machine-learning/studio-module-reference/split-data
[evaluate-model]: /azure/machine-learning/studio-module-reference/evaluate-model
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
[normalize-data]: /azure/machine-learning/studio-module-reference/normalize-data
[score-model]: /azure/machine-learning/studio-module-reference/score-model
[train-model]: /azure/machine-learning/studio-module-reference/train-model
[two-class-boosted-decision-tree]: /azure/machine-learning/studio-module-reference/two-class-boosted-decision-tree
[two-class-support-vector-machine]: /azure/machine-learning/studio-module-reference/two-class-support-vector-machine
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/