---
title: 'Tutorial 2: Utilizar modelos de risco de crédito'
titleSuffix: Azure Machine Learning Studio
description: Um tutorial detalhado que mostra como criar uma solução de Análise Preditiva para avaliação de riscos de crédito no Azure Machine Learning Studio. Este tutorial é a parte dois de uma série de tutoriais de três partes. Ele mostra como preparar e avaliar modelos.
keywords: risco de crédito, solução de análise preditiva, avaliação de riscos
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: 45407e183c70fe67e6bd59e3fd86a50a31844c47
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60735704"
---
# <a name="tutorial-2-train-credit-risk-models---azure-machine-learning-studio"></a>Tutorial 2: Utilizar modelos de risco de crédito - Azure Machine Learning Studio

Neste tutorial, demorar mais aprofundadamente o processo de desenvolvimento de uma solução de Análise Preditiva. Desenvolver um modelo simples no Machine Learning Studio.  Em seguida, implementar o modelo como um serviço web do Azure Machine Learning.  Este modelo implementado pode efetuar predições utilizando novos dados. Este tutorial é **parte dois de uma série de tutoriais de três partes**.

Suponhamos que precisa de prever o risco de crédito de um indivíduo com base nas informações fornecidas sobre uma aplicação de crédito.  

Avaliação de riscos de crédito é um problema complexo, mas este tutorial irá simplificá-lo um pouco. Irá utilizá-lo como um exemplo de como pode criar uma solução de Análise Preditiva com o Microsoft Azure Machine Learning Studio. Irá utilizar o Azure Machine Learning Studio e um serviço web do Machine Learning para esta solução.  

Neste tutorial de três partes, começar com dados de risco de crédito publicamente disponíveis.  Em seguida, desenvolver e formar um modelo preditivo.  Por fim implementa o modelo como um serviço web.

Na [primeira parte do tutorial](tutorial-part1-credit-risk.md), criou uma área de trabalho do Machine Learning Studio, carregou dados e criar uma experimentação.

Nesta parte do tutorial:
 
> [!div class="checklist"]
> * Utilizar vários modelos
> * Pontuar e avaliar os modelos


Na [parte três do tutorial](tutorial-part3-credit-risk-deploy.md), vai implementar o modelo como um serviço web.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]


## <a name="prerequisites"></a>Pré-requisitos

Concluída [primeira parte do tutorial](tutorial-part1-credit-risk.md).

## <a name="train"></a>Utilizar vários modelos

Uma das vantagens da utilização do Azure Machine Learning Studio para a criação de modelos de aprendizagem automática é a possibilidade de experimentar mais do que um tipo de modelo de cada vez numa experimentação individual e comparar os resultados. Este tipo de experimentação ajuda para encontrar a melhor solução para o seu problema.

A experimentação, que estamos a desenvolver neste tutorial, irá criar dois tipos diferentes de modelos e, em seguida, compare os resultados de classificação para decidir quais algoritmos que pretende utilizar na nossa experiência final.  

Existem vários modelos, que pode escolher entre. Para ver os modelos disponíveis, expanda o **Machine Learning** nó na paleta do módulo e, em seguida, expanda **inicializar modelo** e os nós abaixo dele. Para efeitos desta experiência, terá de selecionar o [máquina de Vetor com suporte a duas classes] [ two-class-support-vector-machine] (SVM) e o [árvore de decisões elevada de duas classes] [ two-class-boosted-decision-tree] módulos.

> [!TIP]
> Para obter ajuda para decidir quais algoritmos de Machine Learning que melhor se adequa aos está a tentar resolver o problema em particular, veja [como escolher algoritmos para o Microsoft Azure Machine Learning Studio](algorithm-choice.md).
> 
> 

Terá de adicionar ambos os [árvore de decisões elevada de duas classes] [ two-class-boosted-decision-tree] módulo e [máquina de Vetor com suporte a duas classes] [ two-class-support-vector-machine] módulo Nesse experimento .

### <a name="two-class-boosted-decision-tree"></a>Árvore de decisões elevada de duas classes

Primeiramente, configure o modelo de árvore de decisões elevada.

1. Encontrar o [árvore de decisões elevada de duas classes] [ two-class-boosted-decision-tree] módulo na paleta do módulo e arraste-o para a tela.

1. Encontrar o [Train Model] [ train-model] módulo, arraste-o para a tela e, em seguida, ligue a saída do [árvore de decisões elevada de duas classes] [ two-class-boosted-decision-tree] porta de entrada do módulo para a esquerda a [Train Model] [ train-model] módulo.
   
   O [árvore de decisões elevada de duas classes] [ two-class-boosted-decision-tree] módulo inicializa o modelo genérico, e [Train Model] [ train-model] utiliza dados de treinamento para preparar o modelo. 

1. Ligue a saída à esquerda do esquerda [executar Script R] [ execute-r-script] porta de entrada do módulo para a direita a [Train Model] [ train-model] módulo (neste tutorial [utilizados os dados originários do lado esquerdo](#train) do módulo para a formação de dividir dados).
   
   > [!TIP]
   > não precisa de duas das entradas e uma das saídas do [executar Script R] [ execute-r-script] módulo nesta experimentação, portanto, pode deixá-los desanexados. 
   > 
   > 

Essa parte da experimentação agora terá um aspeto semelhante ao seguinte:  

![Preparar um modelo](./media/tutorial-part2-credit-risk-train/experiment-with-train-model.png)

Agora precisa dizer a [Train Model] [ train-model] módulo que pretende que o modelo para prever o valor de risco de crédito.

1. Selecione o [Train Model] [ train-model] módulo. Na **propriedades** painel, clique em **iniciar Seletor de colunas**.

1. Na **Selecione uma única coluna** caixa de diálogo, escreva "risco de crédito" no campo de pesquisa em **colunas disponíveis**, selecione "Risco de crédito" abaixo e clique no botão de seta para a direita (**>**) para mover o "Risco de crédito" para o **Selected Columns**. 

    ![Selecione a coluna de risco de crédito para o módulo preparar modelo](./media/tutorial-part2-credit-risk-train/train-model-select-column.png)

1. Clique nas **OK** marca de verificação.

### <a name="two-class-support-vector-machine"></a>Máquina de Vetores de Suporte de Duas Classes

Em seguida, configure o modelo SVM.  

Em primeiro lugar, um pouco de explicação sobre SVM. Árvores de decisões elevada funcionam bem com funcionalidades de qualquer tipo. No entanto, uma vez que o módulo SVM gera um classificador linear, o modelo que ele gera tem o erro de teste melhor quando todas as funcionalidades numérico têm a mesma escala. Para converter todas as funcionalidades numérico para a mesma escala, utilizar uma transformação de "Tanh" (com o [normalizar dados] [ normalize-data] módulo). Isso transforma nossos números no intervalo [0,1]. O módulo SVM converte os recursos de cadeia de caracteres para funcionalidades categóricas e, em seguida, às funcionalidades de 0/1 binárias, não precisa de transformar manualmente os recursos de cadeia de caracteres. Além disso, não quer transformar a risco de crédito coluna (21) – é numérico, mas é o valor que está o modelo de formação para prever, portanto, precisa deixar de lado.  

Para configurar o modelo SVM, faça o seguinte:

1. Encontrar o [máquina de Vetor com suporte a duas classes] [ two-class-support-vector-machine] módulo na paleta do módulo e arraste-o para a tela.

1. Com o botão direito a [Train Model] [ train-model] módulo, selecione **cópia**e, em seguida, clique com botão direito do ecrã e selecione **colar**. A cópia do [Train Model] [ train-model] do módulo é a seleção de coluna mesmo que o original.

1. Ligue a saída dos [máquina de Vetor com suporte a duas classes] [ two-class-support-vector-machine] porta do segundo de entrada do módulo para a esquerda [Train Model] [ train-model] módulo.

1. Encontrar o [normalizar dados] [ normalize-data] módulo e arraste-o para a tela.

1. Ligue a saída à esquerda do esquerda [executar Script R] [ execute-r-script] módulo para a entrada deste módulo (Observe que a porta de saída de um módulo pode ser conectada a mais do que um outro módulo).

1. Ligue a porta de saída à esquerda do [normalizar dados] [ normalize-data] porta do segundo de entrada do módulo para a direita [Train Model] [ train-model] módulo.

Essa parte do nosso experimentação deve agora ter um aspeto semelhante ao seguinte:  

![O segundo modelo de formação](./media/tutorial-part2-credit-risk-train/svm-model-added.png)

Agora configurar o [normalizar dados] [ normalize-data] módulo:

1. Clique para selecionar o [normalizar dados] [ normalize-data] módulo. Na **propriedades** painel, selecione **Tanh** para o **método transformação** parâmetro.

1. Clique em **iniciar Seletor de colunas**, selecione "Não existem colunas" para **começam com**, selecione **incluir** na primeira lista pendente, selecione **tipo de coluna** na segunda lista pendente e selecione **numérico** na terceira área suspensa. Esta ação Especifica que todas as colunas numéricas (e apenas numérico) são transformadas.

1. Clique no sinal de menos (+) para a direita desta linha - esta ação cria uma linha de listas suspensas. Selecione **excluir** na primeira lista pendente, selecione **os nomes das colunas** na segunda lista pendente e introduza "Risco de crédito" no campo de texto. Esta ação Especifica que a coluna de risco de crédito deve ser ignorada (precisa fazer isso, uma vez que esta coluna é numérica e para que poderia ser transformadas se não exclui-lo).

1. Clique nas **OK** marca de verificação.  

    ![Selecionar colunas para o módulo de normalizar dados](./media/tutorial-part2-credit-risk-train/normalize-data-select-column.png)


O [normalizar dados] [ normalize-data] módulo está agora definido como executar uma transformação de Tanh em todas as colunas numéricas, exceto para a coluna de risco de crédito.  

## <a name="score-and-evaluate-the-models"></a>Pontuar e avaliar os modelos

utilizar dados de teste foi separados pela [Split Data] [ split] módulo para classificar os nossos modelos de formação. em seguida, pode comparar os resultados dos dois modelos para ver qual gerado melhores resultados.  

### <a name="add-the-score-model-modules"></a>Adicionar os módulos do modelo de pontuação

1. Encontrar o [modelo de pontuação] [ score-model] módulo e arraste-o para a tela.

1. Ligar o [Train Model] [ train-model] módulo que está ligado ao [árvore de decisões elevada de duas classes] [ two-class-boosted-decision-tree] porta de entrada do módulo para a esquerda a [Modelo de pontuação] [ score-model] módulo.

1. Ligar o direito [executar Script R] [ execute-r-script] módulo (nossos dados de testes) à direita de porta de entrada a [Score Model] [ score-model] módulo.

    ![Módulo de modelo de pontuação ligado](./media/tutorial-part2-credit-risk-train/score-model-connected.png)

   
   O [modelo de pontuação] [ score-model] módulo agora pode levar as informações de crédito dos dados de teste, executá-lo por meio do modelo e comparar as previsões do modelo gera com a coluna de risco de crédito reais na dados de teste.

1. Copie e cole o [modelo de pontuação] [ score-model] módulo para criar uma segunda cópia.

1. Ligue a saída do modelo SVM (ou seja, a porta de saída a [Train Model] [ train-model] módulo que está ligado ao [máquina de Vetor com suporte a duas classes] [ two-class-support-vector-machine] módulo) para a porta de entrada da segunda [Score Model] [ score-model] módulo.

1. Para o modelo SVM, que precisa fazer a mesma transformação para os dados de teste, tal como fez para os dados de treinamento. Por isso, copie e cole o [normalizar dados] [ normalize-data] módulo criar uma segunda cópia e ligá-la para a direita [executar Script do R] [ execute-r-script] módulo.

1. Ligue a saída à esquerda da segunda [normalizar dados] [ normalize-data] porta do segundo de entrada do módulo para a direita [Score Model] [ score-model] módulo.

    ![Ambos os módulos de modelo de pontuação ligados](./media/tutorial-part2-credit-risk-train/both-score-models-added.png)


### <a name="add-the-evaluate-model-module"></a>Adicione o módulo de modelo de avaliação

Para avaliar os resultados de classificação dois e compará-los, utiliza um [Evaluate Model] [ evaluate-model] módulo.  

1. Encontrar o [Evaluate Model] [ evaluate-model] módulo e arraste-o para a tela.

1. Ligue a porta de saída a [modelo de pontuação] [ score-model] módulo associado ao modelo de árvore de decisões elevada para a porta de entrada à esquerda do [Evaluate Model] [ evaluate-model] módulo.

1. Ligar a outra [modelo de pontuação] [ score-model] porta de entrada do módulo para a direita.  

    ![Avaliar modelo módulo ligado](./media/tutorial-part2-credit-risk-train/evaluate-model-added.png)


### <a name="run-the-experiment-and-check-the-results"></a>Execute a experimentação e verificar os resultados

Para executar a experimentação, clique a **executar** botão abaixo da tela. Esta operação poderá demorar alguns minutos. Um indicador de rotação em cada módulo mostra que está em execução e, em seguida, uma marca de verificação verde mostra terminou o módulo. Quando todos os módulos têm uma marca de verificação, a experimentação foi concluída em execução.

A experimentação deverá agora ser semelhante ao seguinte:  

![Ambos os modelos de avaliação](./media/tutorial-part2-credit-risk-train/final-experiment.png)


Para verificar os resultados, clique na porta de saída do [Evaluate Model] [ evaluate-model] módulo e selecione **Visualize**.  

O [Evaluate Model] [ evaluate-model] módulo gera um par de curvas e métricas que permitem-lhe comparar os resultados dos dois modelos com a pontuação. Pode ver os resultados como curvas de característica de operador de destinatário (ROC), curvas de precisão/remoção ou curvas de comparação de precisão. Dados adicionais incluem uma matriz de confusão, valores cumulativos para a área abaixo da curva (AUC) e outras métricas. Pode alterar o valor de limiar ao mover o controlo de deslize, à esquerda ou direita e ver como ele afeta o conjunto de métricas.  

À direita do gráfico, clique em **classificadas de conjunto de dados** ou **classificadas de conjunto de dados para comparar** para destacar a curva associada e para apresentar as métricas associadas abaixo. Na legenda para as curvas, "Classificadas de conjunto de dados" corresponde a porta de entrada à esquerda dos [Evaluate Model] [ evaluate-model] módulo - em nosso caso, este é o modelo de árvore de decisões elevada. "Classificadas de conjunto de dados para comparar" corresponde a para a porta de entrada à direita - o modelo SVM no nosso caso. Quando clica destas etiquetas, a curva para esse modelo é realçada e as métricas correspondentes são apresentadas, conforme mostrado no seguinte gráfico.  

![Curvas ROC para modelos](./media/tutorial-part2-credit-risk-train/roc-curves.png)

Examinando estes valores, pode decidir qual modelo é mais próximo em oferecer-lhe os resultados que procura. Pode voltar atrás e iterar a experiência, alterando os valores de parâmetro nos modelos diferentes. 

A ciência e a arte de interpretar esses resultados e ajuste do desempenho do modelo está fora do âmbito deste tutorial. Para obter ajuda adicional, poderá ler os artigos seguintes:
- [Como avaliar o desempenho de modelo no Azure Machine Learning Studio](evaluate-model-performance.md)
- [Escolher parâmetros para otimizar os seus algoritmos no Azure Machine Learning Studio](algorithm-parameters-optimize.md)
- [Interpretar os resultados de modelo no Azure Machine Learning Studio](interpret-model-results.md)

> [!TIP]
> Sempre que executar a experimentação, um registo de iteração é mantido no histórico de execuções. Pode ver estes iterações e retornar a qualquer uma delas, ao clicar em **ver histórico de EXECUÇÕES** abaixo da tela. Também pode clicar **executar anteriores** no **propriedades** painel para regressar à iteração imediatamente precedente aquele tiver aberto.
> 
> Pode fazer uma cópia de qualquer iteração da sua experimentação ao clicar em **guardar como** abaixo da tela. 
> Utilizar a experimentação **resumo** e **Descrição** propriedades para manter um registro do que já tentou em suas iterações de experimentação.
> 
> Para obter mais informações, consulte [gerir iterações de experimentação no Azure Machine Learning Studio](manage-experiment-iterations.md).  
> 
> 

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, concluiu estes passos: 
 
> [!div class="checklist"]
> * Criar uma experimentação
> * Utilizar vários modelos
> * Pontuar e avaliar os modelos

Agora, está pronto para implementar os modelos para estes dados.

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