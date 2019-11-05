---
title: 'Tutorial 2: treinar modelos de risco de crédito'
titleSuffix: Azure Machine Learning Studio (classic)
description: Um tutorial detalhado que mostra como criar uma solução de análise preditiva para avaliação de risco de crédito na versão clássica do Azure Machine Learning Studio. Este tutorial é a parte dois de uma série de tutoriais de três partes. Ele mostra como treinar e avaliar modelos.
keywords: risco de crédito, solução de análise preditiva, avaliação de riscos
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: ce3661a75d35ab39b7e8b551cc0b84b57a76e032
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492553"
---
# <a name="tutorial-2-train-credit-risk-models---azure-machine-learning-studio-classic"></a>Tutorial 2: treinar modelos de risco de crédito-Azure Machine Learning Studio (clássico)

Neste tutorial, você tem uma visão detalhada do processo de desenvolvimento de uma solução de análise preditiva. Você desenvolve um modelo simples na versão clássica do Machine Learning Studio.  Em seguida, você implanta o modelo como um serviço Web Azure Machine Learning.  Esse modelo implantado pode fazer previsões usando novos dados. Este tutorial é **a parte dois de uma série de tutoriais de três partes**.

Suponhamos que precisa de prever o risco de crédito de um indivíduo com base nas informações fornecidas sobre uma aplicação de crédito.  

A avaliação de risco de crédito é um problema complexo, mas este tutorial vai simplificar um pouco. Você o usará como um exemplo de como é possível criar uma solução de análise preditiva usando Microsoft Azure Machine Learning Studio (clássico). Você usará a versão clássica do Azure Machine Learning Studio e um serviço Web Machine Learning para essa solução.  

Neste tutorial de três partes, você começa com dados de risco de crédito disponíveis publicamente.  Em seguida, você desenvolve e treina um modelo de previsão.  Por fim, você implanta o modelo como um serviço Web.

Na [parte um do tutorial](tutorial-part1-credit-risk.md), você criou um espaço de trabalho Machine Learning Studio (clássico), carregou dados e criou um experimento.

Nesta parte do tutorial, você:
 
> [!div class="checklist"]
> * Treinar vários modelos
> * Pontuar e avaliar os modelos


Na [parte três do tutorial](tutorial-part3-credit-risk-deploy.md), você implantará o modelo como um serviço Web.

## <a name="prerequisites"></a>Pré-requisitos

Conclua [a parte um do tutorial](tutorial-part1-credit-risk.md).

## <a name="train"></a>Treinar vários modelos

Um dos benefícios de usar a versão clássica do Azure Machine Learning Studio para criar modelos de aprendizado de máquina é a capacidade de experimentar mais de um tipo de modelo de cada vez em um único experimento e comparar os resultados. Esse tipo de experimentação ajuda a encontrar a melhor solução para o seu problema.

No experimento que estamos desenvolvendo neste tutorial, você criará dois tipos diferentes de modelos e, em seguida, comparará seus resultados de Pontuação para decidir qual algoritmo você deseja usar em nosso experimento final.  

Há vários modelos dos quais você pode escolher. Para ver os modelos disponíveis, expanda o nó **Machine Learning** na paleta de módulo e, em seguida, expanda o **modelo de inicialização** e os nós abaixo dele. Para os fins deste experimento, você selecionará o [computador de vetor de suporte de duas classes][two-class-support-vector-machine] (SVM) e os módulos de árvore de [decisão aumentada de duas classes][two-class-boosted-decision-tree] .

> [!TIP]
> Para obter ajuda para decidir qual algoritmo de Machine Learning melhor se adapta ao problema específico que você está tentando resolver, consulte [como escolher algoritmos para Microsoft Azure Machine Learning Studio (clássico)](algorithm-choice.md).
> 
> 

Você adicionará o módulo [árvore de decisão aumentada de duas classes][two-class-boosted-decision-tree] e o módulo [máquina de vetor de suporte de duas classes][two-class-support-vector-machine] neste experimento.

### <a name="two-class-boosted-decision-tree"></a>Árvore de decisão aumentada de duas classes

Primeiro, configure o modelo de árvore de decisão aumentada.

1. Localize o módulo [árvore de decisão aumentada de duas classes][two-class-boosted-decision-tree] na paleta de módulo e arraste-a para a tela.

1. Localize o módulo [modelo de treinamento][train-model] , arraste-o para a tela e conecte a saída do módulo [árvore de decisão aumentada de duas classes][two-class-boosted-decision-tree] à porta de entrada à esquerda do módulo modelo de [treinamento][train-model] .
   
   O módulo [árvore de decisão aumentada de duas classes][two-class-boosted-decision-tree] Inicializa o modelo genérico e o [modelo Train][train-model] usa dados de treinamento para treinar o modelo. 

1. Conecte a saída à esquerda do módulo [Executar script R][execute-r-script] esquerdo à porta de entrada à direita do módulo [modelo de treinamento][train-model] (neste tutorial, você [usou os dados provenientes do lado esquerdo](#train) do módulo dividir dados para treinamento).
   
   > [!TIP]
   > Você não precisa de duas das entradas e uma das saídas do módulo [Executar script R][execute-r-script] para este experimento, para que você possa deixá-las desanexadas. 
   > 
   > 

Esta parte do experimento agora se parece com esta:  

![Treinando um modelo](./media/tutorial-part2-credit-risk-train/experiment-with-train-model.png)

Agora você precisa informar ao módulo [modelo de treinamento][train-model] que deseja que o modelo preveja o valor de risco de crédito.

1. Selecione o módulo [modelo de treinamento][train-model] . No painel **Propriedades** , clique em **Iniciar seletor de coluna**.

1. Na caixa de diálogo **selecionar uma única coluna** , digite "risco de crédito" no campo de pesquisa em **colunas disponíveis**, selecione "risco de crédito" abaixo e clique no botão de seta para a direita ( **>** ) para mover o "risco de crédito" para as **colunas selecionadas**. 

    ![Selecione a coluna risco de crédito para o módulo modelo de treinamento](./media/tutorial-part2-credit-risk-train/train-model-select-column.png)

1. Clique na marca de seleção **OK** .

### <a name="two-class-support-vector-machine"></a>Máquina de Vetores de Suporte de Duas Classes

Em seguida, configure o modelo SVM.  

Primeiro, uma pequena explicação sobre SVM. Árvores de decisão aumentadas funcionam bem com recursos de qualquer tipo. No entanto, como o módulo SVM gera um classificador linear, o modelo que ele gera tem o melhor erro de teste quando todos os recursos numéricos têm a mesma escala. Para converter todos os recursos numéricos na mesma escala, use uma transformação "tanh" (com o módulo [normalizar dados][normalize-data] ). Isso transforma os números no intervalo [0, 1]. O módulo SVM converte recursos de cadeia de caracteres em recursos categóricos e, em seguida, em recursos binários 0/1, para que você não precise transformar manualmente os recursos de cadeia de caracteres. Além disso, você não deseja transformar a coluna de risco de crédito (coluna 21) – ela é numérica, mas é o valor que estamos treinando para prever o modelo, portanto, você precisa deixá-lo sozinho.  

Para configurar o modelo SVM, faça o seguinte:

1. Localize o módulo [máquina de vetor de suporte de duas classes][two-class-support-vector-machine] na paleta de módulo e arraste-o para a tela.

1. Clique com o botão direito do mouse no módulo [modelo de treinamento][train-model] , selecione **copiar**e clique com o botão direito do mouse na tela e selecione **colar**. A cópia do módulo [modelo de treinamento][train-model] tem a mesma seleção de coluna que o original.

1. Conecte a saída do módulo [máquina de vetor de suporte de duas classes][two-class-support-vector-machine] à porta de entrada à esquerda do segundo módulo [modelo de treinamento][train-model] .

1. Localize o módulo [normalizar dados][normalize-data] e arraste-o para a tela.

1. Conecte a saída à esquerda do módulo [Executar script R][execute-r-script] esquerdo à entrada deste módulo (Observe que a porta de saída de um módulo pode estar conectada a mais de um outro módulo).

1. Conecte a porta de saída à esquerda do módulo [normalizar dados][normalize-data] à porta de entrada à direita do segundo módulo [modelo de treinamento][train-model] .

Essa parte do experimento agora deve ser semelhante a esta:  

![Treinando o segundo modelo](./media/tutorial-part2-credit-risk-train/svm-model-added.png)

Agora, configure o módulo [normalizar dados][normalize-data] :

1. Clique para selecionar o módulo [normalizar dados][normalize-data] . No painel **Propriedades** , selecione **tanh** para o parâmetro **método de transformação** .

1. Clique em **Iniciar seletor de coluna**, selecione "sem colunas" para **começar com**, selecione **incluir** na primeira lista suspensa, selecione **tipo de coluna** no segundo menu suspenso e selecione **numérico** na terceira lista suspensa. Isso especifica que todas as colunas numéricas (e somente as numéricas) são transformadas.

1. Clique no sinal de adição (+) à direita desta linha – isso cria uma linha de listas suspensas. Selecione **excluir** na primeira lista suspensa, selecione **nomes de coluna** no segundo menu suspenso e insira "risco de crédito" no campo de texto. Isso especifica que a coluna de risco de crédito deve ser ignorada (você precisa fazer isso porque essa coluna é numérica e, portanto, seria transformada se você não a excluiu).

1. Clique na marca de seleção **OK** .  

    ![Selecionar colunas para o módulo normalizar dados](./media/tutorial-part2-credit-risk-train/normalize-data-select-column.png)


O módulo [normalizar dados][normalize-data] agora está definido para executar uma transformação tanh em todas as colunas numéricas, exceto para a coluna risco de crédito.  

## <a name="score-and-evaluate-the-models"></a>Pontuar e avaliar os modelos

Você usa os dados de teste que foram separados pelo módulo [dividir dados][split] para pontuar nossos modelos treinados. em seguida, você pode comparar os resultados dos dois modelos para ver que geraram resultados melhores.  

### <a name="add-the-score-model-modules"></a>Adicionar os módulos do modelo de Pontuação

1. Localize o módulo [modelo de Pontuação][score-model] e arraste-o para a tela.

1. Conecte o módulo [modelo de treinamento][train-model] que está conectado ao módulo árvore de [decisão aumentada de duas classes][two-class-boosted-decision-tree] à porta de entrada à esquerda do módulo [modelo de Pontuação][score-model] .

1. Conecte o módulo [Executar script R][execute-r-script] à direita (nossos dados de teste) à porta de entrada à direita do módulo [modelo de Pontuação][score-model] .

    ![Módulo de modelo de Pontuação conectado](./media/tutorial-part2-credit-risk-train/score-model-connected.png)

   
   O módulo [modelo de Pontuação][score-model] agora pode pegar as informações de crédito dos dados de teste, executá-lo por meio do modelo e comparar as previsões que o modelo gera com a coluna risco de crédito real nos dados de teste.

1. Copie e cole o módulo [modelo de Pontuação][score-model] para criar uma segunda cópia.

1. Conecte a saída do modelo SVM (ou seja, a porta de saída do módulo [modelo de treinamento][train-model] que está conectado ao módulo [máquina de vetor de suporte de duas classes][two-class-support-vector-machine] ) à porta de entrada do segundo módulo [modelo de Pontuação][score-model] .

1. Para o modelo SVM, você precisa fazer a mesma transformação para os dados de teste como fez nos dados de treinamento. Portanto, copie e cole o módulo [normalizar dados][normalize-data] para criar uma segunda cópia e conectá-la ao módulo [Executar script R][execute-r-script] correto.

1. Conecte a saída à esquerda do segundo módulo [normalizar dados][normalize-data] à porta de entrada à direita do segundo módulo [modelo de Pontuação][score-model] .

    ![Ambos os módulos de modelo de Pontuação conectados](./media/tutorial-part2-credit-risk-train/both-score-models-added.png)


### <a name="add-the-evaluate-model-module"></a>Adicionar o módulo modelo de avaliação

Para avaliar os dois resultados de Pontuação e compará-los, use um módulo [avaliar modelo][evaluate-model] .  

1. Localize o módulo [modelo de avaliação][evaluate-model] e arraste-o para a tela.

1. Conecte a porta de saída do módulo [modelo de Pontuação][score-model] associado ao modelo de árvore de decisão aumentada à porta de entrada à esquerda do módulo modelo de [avaliação][evaluate-model] .

1. Conecte o outro módulo de [modelo de Pontuação][score-model] à porta de entrada à direita.  

    ![Módulo modelo de avaliação conectado](./media/tutorial-part2-credit-risk-train/evaluate-model-added.png)


### <a name="run-the-experiment-and-check-the-results"></a>Executar o experimento e verificar os resultados

Para executar o experimento, clique no botão **executar** abaixo da tela. Esta operação poderá demorar alguns minutos. Um indicador girando em cada módulo mostra que ele está em execução e, em seguida, uma marca de seleção verde mostra quando o módulo é concluído. Quando todos os módulos têm uma marca de seleção, o experimento concluiu a execução.

O experimento agora deve ser semelhante a este:  

![Avaliando ambos os modelos](./media/tutorial-part2-credit-risk-train/final-experiment.png)


Para verificar os resultados, clique na porta de saída do módulo [avaliar modelo][evaluate-model] e selecione **Visualizar**.  

O módulo [modelo de avaliação][evaluate-model] produz um par de curvas e métricas que permitem comparar os resultados dos dois modelos pontuados. Você pode exibir os resultados como curvas de características de operador de receptor (ROC), curvas de precisão/recall ou curvas de comparação. Os dados adicionais exibidos incluem uma matriz de confusão, valores cumulativos para a área sob a curva (AUC) e outras métricas. Você pode alterar o valor do limite movendo o controle deslizante para a esquerda ou direita e ver como ele afeta o conjunto de métricas.  

À direita do grafo, clique em conjunto de texto **pontuado** ou conjunto de texto classificado **para comparar** a realçar a curva associada e para exibir as métricas associadas abaixo. Na legenda para as curvas, "conjunto de dados pontuado" corresponde à porta de entrada à esquerda do módulo [avaliar modelo][evaluate-model] – em nosso caso, esse é o modelo de árvore de decisão aumentada. "Conjunto de dados pontuado para comparação" corresponde à porta de entrada à direita – o modelo SVM em nosso caso. Quando você clica em um desses rótulos, a curva desse modelo é realçada e as métricas correspondentes são exibidas, conforme mostrado no gráfico a seguir.  

![Curvas ROC para modelos](./media/tutorial-part2-credit-risk-train/roc-curves.png)

Ao examinar esses valores, você pode decidir qual modelo está mais próximo de fornecer os resultados que está procurando. Você pode voltar e iterar em seu experimento alterando os valores de parâmetro nos diferentes modelos. 

A ciência e a arte de interpretar esses resultados e ajustar o desempenho do modelo estão fora do escopo deste tutorial. Para obter ajuda adicional, você pode ler os seguintes artigos:
- [Como avaliar o desempenho do modelo no Azure Machine Learning Studio (clássico)](evaluate-model-performance.md)
- [Escolha os parâmetros para otimizar seus algoritmos no Azure Machine Learning Studio (clássico)](algorithm-parameters-optimize.md)
- [Interpretar os resultados do modelo em Azure Machine Learning Studio (clássico)](interpret-model-results.md)

> [!TIP]
> Cada vez que você executa o experimento, um registro dessa iteração é mantido no histórico de execução. Você pode exibir essas iterações e retornar a qualquer uma delas clicando em **Exibir histórico de execução** abaixo da tela. Você também pode clicar em **execução anterior** no painel **Propriedades** para retornar à iteração imediatamente anterior à que você abriu.
> 
> Você pode fazer uma cópia de qualquer iteração de seu experimento clicando em **salvar como** abaixo da tela. 
> Use as propriedades **Resumo** e **Descrição** do experimento para manter um registro do que você tentou em suas iterações de teste.
> 
> Para obter mais informações, consulte [gerenciar iterações de experimento no Azure Machine Learning Studio (clássico)](manage-experiment-iterations.md).  
> 
> 

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você concluiu estas etapas: 
 
> [!div class="checklist"]
> * Criar uma experimentação
> * Treinar vários modelos
> * Pontuar e avaliar os modelos

Agora você está pronto para implantar modelos para esses dados.

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