---
title: 'Tutorial 2: Treinar modelos de risco de crédito'
titleSuffix: ML Studio (classic) - Azure
description: Um tutorial detalhado que mostra como criar uma solução de análise preditiva para avaliação de risco de crédito no Azure Machine Learning Studio (clássico). Este tutorial é a segunda parte de uma série tutorial em três partes. Mostra como treinar e avaliar modelos.
keywords: risco de crédito, solução de análise preditiva, avaliação de riscos
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: 8feca17f10bb891f0ca5577b2363f95901da4a46
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79217876"
---
# <a name="tutorial-2-train-credit-risk-models---azure-machine-learning-studio-classic"></a>Tutorial 2: Modelos de risco de crédito de treina - Azure Machine Learning Studio (clássico)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Neste tutorial, você olha para o processo de desenvolvimento de uma solução de análise preditiva. Desenvolve-se um modelo simples no Machine Learning Studio (clássico).  Em seguida, implementa o modelo como um serviço web Azure Machine Learning.  Este modelo implementado pode fazer previsões usando novos dados. Este tutorial é **a segunda parte de uma série tutorial em três partes.**

Suponhamos que precisa de prever o risco de crédito de um indivíduo com base nas informações fornecidas sobre uma aplicação de crédito.  

A avaliação do risco de crédito é um problema complexo, mas este tutorial vai simplificar um pouco. Vais usá-lo como um exemplo de como podes criar uma solução de análise preditiva utilizando o Microsoft Azure Machine Learning Studio (clássico). Você usará o Azure Machine Learning Studio (clássico) e um serviço web machine learning para esta solução.  

Neste tutorial em três partes, começa com dados de risco de crédito disponíveis ao público.  Depois desenvolves e treinas um modelo preditivo.  Finalmente implementa o modelo como um serviço web.

Na [primeira parte do tutorial,](tutorial-part1-credit-risk.md)criou um espaço de trabalho do Machine Learning Studio (clássico), carregou dados e criou uma experiência.

Nesta parte do tutorial:
 
> [!div class="checklist"]
> * Treinar vários modelos
> * Marque e avalie os modelos


Na [terceira parte do tutorial,](tutorial-part3-credit-risk-deploy.md)irá implementar o modelo como um serviço web.

## <a name="prerequisites"></a>Pré-requisitos

Complete a [parte um do tutorial.](tutorial-part1-credit-risk.md)

## <a name="train"></a>Treinar vários modelos

Um dos benefícios da utilização do Azure Machine Learning Studio (clássico) para criar modelos de machine learning é a capacidade de experimentar mais do que um tipo de modelo de cada vez numa única experiência e comparar os resultados. Este tipo de experimentação ajuda-o a encontrar a melhor solução para o seu problema.

Na experiência que estamos a desenvolver neste tutorial, vais criar dois tipos diferentes de modelos e depois comparar os resultados das pontuações para decidir que algoritmo queres usar na nossa experiência final.  

Há vários modelos que pode escolher. Para ver os modelos disponíveis, expanda o nó **machine learning** na paleta de módulos e, em seguida, expandir o **Modelo inicializar** e os nós por baixo. Para efeitos desta experiência, você irá selecionar a [Máquina vetorial de suporte de duas classes][two-class-support-vector-machine] (SVM) e os módulos de árvore de decisão de duas [classes.][two-class-boosted-decision-tree]

> [!TIP]
> Para obter ajuda para decidir qual o algoritmo de Machine Learning que melhor se adequa ao problema particular que está a tentar resolver, consulte como escolher algoritmos para o [Microsoft Azure Machine Learning Studio (clássico)](algorithm-choice.md).
> 
> 

Você irá adicionar tanto o módulo de [árvore de decisão de duas classes e][two-class-boosted-decision-tree] o módulo de máquina de [vetor de suporte de duas classes][two-class-support-vector-machine] nesta experiência.

### <a name="two-class-boosted-decision-tree"></a>Árvore de Decisões Elevada de Duas Classes

Primeiro, criar o modelo de árvore de decisão reforçado.

1. Encontre o módulo de árvore de decisão impulsionada de duas classes na paleta de módulos e [arraste-o][two-class-boosted-decision-tree] para a tela.

1. Encontre o módulo [Modelo de Comboio,][train-model] arraste-o para a tela e ligue a saída do módulo de [decisão de duas classes][two-class-boosted-decision-tree] à porta de entrada esquerda do módulo Modelo de [Comboio.][train-model]
   
   O módulo [de duas classes boosted Decision Tree][two-class-boosted-decision-tree] inicializa o modelo genérico, e o Modelo de [Comboio][train-model] utiliza dados de formação para treinar o modelo. 

1. Ligue a saída esquerda do módulo do [Script ExecutR R][execute-r-script] esquerdo à porta de entrada direita do módulo [Modelo de Comboio][train-model] (neste tutorial utilizou os [dados provenientes do lado esquerdo](#train) do módulo Split Data para treino).
   
   > [!TIP]
   > não precisa de duas das inputs e uma das saídas do módulo [Execute R Script][execute-r-script] para esta experiência, para que possa deixá-las desapegadas. 
   > 
   > 

Esta parte da experiência agora se parece com isto:  

![Formação de um modelo](./media/tutorial-part2-credit-risk-train/experiment-with-train-model.png)

Agora precisa dizer ao módulo [Modelo de Comboio][train-model] que quer que o modelo preveja o valor do Risco de Crédito.

1. Selecione o módulo [Modelo de Comboio.][train-model] No painel **Propriedades,** clique no seletor de **colunas de lançamento**.

1. No select um único diálogo de **coluna,** escreva "risco de crédito" no campo de pesquisa sob **colunas disponíveis,** selecione "Risco de crédito" abaixo e clique no botão de seta direita **(>** ) para mover "Risco de Crédito" para **Colunas Selecionadas**. 

    ![Selecione a coluna de Risco de Crédito para o módulo Modelo de Comboio](./media/tutorial-part2-credit-risk-train/train-model-select-column.png)

1. Clique na marca de verificação **OK.**

### <a name="two-class-support-vector-machine"></a>Máquina de Vetores de Suporte de Duas Classes

Em seguida, configuraste o modelo SVM.  

Primeiro, uma pequena explicação sobre a SVM. As árvores de decisão impulsionadas funcionam bem com características de qualquer tipo. No entanto, uma vez que o módulo SVM gera um classificador linear, o modelo que gera tem o melhor erro de teste quando todas as características numéricas têm a mesma escala. Para converter todas as características numéricas na mesma escala, utiliza-se uma transformação "Tanh" (com o módulo [Normalize Data).][normalize-data] Isto transforma os nossos números na gama [0,1]. O módulo SVM converte funcionalidades de cadeia em características categóricas e, em seguida, para funcionalidades binárias de 0/1, para que não seja necessário transformar manualmente as funcionalidades das cordas. Além disso, não quer transformar a coluna de Risco de Crédito (coluna 21) - é numérico, mas é o valor que estamos a treinar o modelo para prever, por isso tens de a deixar em paz.  

Para configurar o modelo SVM, faça o seguinte:

1. Encontre o módulo [de máquina de vetores de suporte de duas classes][two-class-support-vector-machine] na paleta de módulos e arraste-o para a tela.

1. Clique no módulo [Modelo de Comboio,][train-model] selecione **Copy**, e, em seguida, clique à direita na tela e selecione **Paste**. A cópia do módulo [Modelo de Comboio][train-model] tem a mesma seleção de colunas que a original.

1. Ligue a saída do módulo vetor de [suporte de duas classes][two-class-support-vector-machine] à porta de entrada esquerda do segundo módulo Modelo de [Comboio.][train-model]

1. Encontre o módulo [Normalize Data][normalize-data] e arraste-o para a tela.

1. Ligue a saída esquerda do módulo do [Script ExecutR R][execute-r-script] esquerdo à entrada deste módulo (note que a porta de saída de um módulo pode estar ligada a mais do que um outro módulo).

1. Ligue a porta de saída esquerda do módulo [Normalize Data][normalize-data] à porta de entrada direita do segundo módulo [Modelo de Comboio.][train-model]

Esta parte da nossa experiência deve agora ser algo assim:  

![Formação do segundo modelo](./media/tutorial-part2-credit-risk-train/svm-model-added.png)

Agora configure o módulo De normalizar os [dados:][normalize-data]

1. Clique para selecionar o módulo [Normalize Data.][normalize-data] No painel **Propriedades,** selecione **Tanh** para o parâmetro do **método transformação.**

1. Clique no seletor de **colunas de lançamento**, selecione "Sem colunas" para Iniciar **Com**, selecione **Incluir** na primeira gota, selecione o tipo de **coluna** no segundo dropdown e selecione **Numérico** no terceiro dropdown. Isto especifica que todas as colunas numéricas (e apenas numéricas) são transformadas.

1. Clique no sinal de mais (+) à direita desta linha - isto cria uma linha de quedas. **Selecione Excluir** no primeiro dropdown, selecione nomes de **colunas** no segundo dropdown e introduza "Risco de Crédito" no campo de texto. Isto especifica que a coluna de Risco de Crédito deve ser ignorada (é preciso fazê-lo porque esta coluna é numérica e por isso seria transformada se não a excluísse).

1. Clique na marca de verificação **OK.**  

    ![Selecione colunas para o módulo De normalizar dados](./media/tutorial-part2-credit-risk-train/normalize-data-select-column.png)


O módulo [Normalize Data][normalize-data] está agora definido para realizar uma transformação de Tanh em todas as colunas numéricas, exceto para a coluna risco de crédito.  

## <a name="score-and-evaluate-the-models"></a>Marque e avalie os modelos

utiliza os dados de teste que foram separados pelo módulo [Split Data][split] para marcar os nossos modelos treinados. pode então comparar os resultados dos dois modelos para ver quais os resultados que geraram melhores resultados.  

### <a name="add-the-score-model-modules"></a>Adicione os módulos 'Modelo de Pontuação'

1. Encontre o módulo [Modelo de Pontuação][score-model] e arraste-o para a tela.

1. Ligue o módulo [Modelo de Comboio][train-model] que está ligado ao módulo de decisão reforçado de duas [classes][two-class-boosted-decision-tree] à porta de entrada esquerda do módulo Modelo [de Pontuação.][score-model]

1. Ligue o módulo de [script execute r][execute-r-script] direito (os nossos dados de teste) à porta de entrada certa do módulo ['Modelo de Pontuação'.][score-model]

    ![Módulo Modelo de Pontuação ligado](./media/tutorial-part2-credit-risk-train/score-model-connected.png)

   
   O módulo [Score Model][score-model] pode agora retirar a informação de crédito dos dados de teste, executá-la através do modelo e comparar as previsões que o modelo gera com a coluna de risco de crédito real nos dados de teste.

1. Copie e cole o módulo ['Modelo de Pontuação'][score-model] para criar uma segunda cópia.

1. Ligue a saída do modelo SVM (isto é, a porta de saída do módulo [Modelo de Comboio][train-model] que está ligado ao módulo vetor de suporte de duas [classes)][two-class-support-vector-machine] à porta de entrada do segundo módulo ['Modelo de Pontuação'.][score-model]

1. Para o modelo SVM, tem de fazer a mesma transformação nos dados de teste que fez aos dados de treino. Por isso, copie e cole o módulo [Normalize Data][normalize-data] para criar uma segunda cópia e conectá-lo ao módulo de [Script Execute R][execute-r-script] certo.

1. Ligue a saída esquerda do segundo módulo [Normalize Data][normalize-data] à porta de entrada direita do segundo módulo ['Modelo de Pontuação'.][score-model]

    ![Ambos os módulos de modelo de pontuação ligados](./media/tutorial-part2-credit-risk-train/both-score-models-added.png)


### <a name="add-the-evaluate-model-module"></a>Adicione o módulo Modelo de Avaliação

Para avaliar os dois resultados de pontuação e compará-los, utiliza-se um módulo [De Avaliar Modelo.][evaluate-model]  

1. Encontre o módulo [Modelo de Avaliação][evaluate-model] e arraste-o para a tela.

1. Ligue a porta de saída do módulo [Score Model][score-model] associado ao modelo de árvore de decisão reforçado à porta de entrada esquerda do módulo Modelo [de Avaliação.][evaluate-model]

1. Ligue o outro módulo ['Modelo de Pontuação'][score-model] à porta de entrada certa.  

    ![Avaliar módulo modelo ligado](./media/tutorial-part2-credit-risk-train/evaluate-model-added.png)


### <a name="run-the-experiment-and-check-the-results"></a>Faça a experiência e verifique os resultados

Para executar a experiência, clique no botão **RUN** abaixo da tela. Esta operação poderá demorar alguns minutos. Um indicador de rotação em cada módulo mostra que está em execução, e então uma marca de verificação verde mostra quando o módulo está terminado. Quando todos os módulos têm uma marca de verificação, a experiência terminou de funcionar.

A experiência deve agora ser algo assim:  

![Avaliação de ambos os modelos](./media/tutorial-part2-credit-risk-train/final-experiment.png)


Para verificar os resultados, clique na porta de saída do módulo ['Avaliar Modelo'][evaluate-model] e selecione **Visualize**.  

O módulo ['Avaliar Modelo'][evaluate-model] produz um par de curvas e métricas que lhe permitem comparar os resultados dos dois modelos pontuados. Pode ver os resultados como curvas Características do Operador recetor (ROC), curvas de precisão/recall ou curvas de elevação. Os dados adicionais apresentados incluem uma matriz de confusão, valores cumulativos para a área sob a curva (AUC), e outras métricas. Pode alterar o valor limiar movendo o slider para a esquerda ou para a direita e ver como afeta o conjunto de métricas.  

À direita do gráfico, clique em Conjunto de **dados pontuado** ou **marcado para comparar** para destacar a curva associada e para exibir as métricas associadas abaixo. Na lenda das curvas, "Conjunto de dados pontuado" corresponde à porta de entrada esquerda do módulo [Modelo de Avaliação][evaluate-model] - no nosso caso, este é o modelo de árvore de decisão reforçado. "Conjunto de dados pontuado para comparar" corresponde à porta de entrada certa - o modelo SVM no nosso caso. Quando clica numa destas etiquetas, destaca-se a curva para esse modelo e as métricas correspondentes são apresentadas, como mostra o gráfico seguinte.  

![Curvas ROC para modelos](./media/tutorial-part2-credit-risk-train/roc-curves.png)

Ao examinar estes valores, pode decidir qual o modelo mais próximo de lhe dar os resultados que procura. Pode voltar e iterar na sua experiência alterando os valores dos parâmetros nos diferentes modelos. 

A ciência e arte de interpretar estes resultados e afinar a performance do modelo está fora do âmbito deste tutorial. Para obter ajuda adicional, pode ler os seguintes artigos:
- [Como avaliar o desempenho do modelo no Azure Machine Learning Studio (clássico)](evaluate-model-performance.md)
- [Escolha parâmetros para otimizar os seus algoritmos no Azure Machine Learning Studio (clássico)](algorithm-parameters-optimize.md)
- [Interpretar resultados do modelo no Azure Machine Learning Studio (clássico)](interpret-model-results.md)

> [!TIP]
> Cada vez que executas a experiência, um registo dessa iteração é mantido na História da Corrida. Pode ver estas iterações e voltar a qualquer uma delas, clicando em **VER RUN HISTORY** abaixo da tela. Também pode clicar em **Prior Run** no painel **Propriedades** para voltar à iteração imediatamente anterior à que tem aberto.
> 
> Pode fazer uma cópia de qualquer iteração da sua experiência clicando em **SAVE AS** abaixo da tela. 
> Use as propriedades **sumárias** e **descritivas** da experiência para manter um registo do que tentou nas suas iterações de experiência.
> 
> Para mais informações, consulte [Gerir iterações de experimentação no Azure Machine Learning Studio (clássico)](manage-experiment-iterations.md).  
> 
> 

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, completou estes passos: 
 
> [!div class="checklist"]
> * Criar uma experimentação
> * Treinar vários modelos
> * Marque e avalie os modelos

Está agora pronto para implementar modelos para estes dados.

> [!div class="nextstepaction"]
> [Tutorial, parte 3 – Implementar modelos](tutorial-part3-credit-risk-deploy.md)


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/