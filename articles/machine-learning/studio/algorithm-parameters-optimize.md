---
title: Otimizar algoritmos
titleSuffix: Azure Machine Learning Studio (classic)
description: Explica como escolher o conjunto de parâmetros ideal para um algoritmo no Azure Machine Learning Studio (clássico).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 11/29/2017
ms.openlocfilehash: 74775550054fc3b4fdbed5cc006d14a913c369c5
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493514"
---
# <a name="choose-parameters-to-optimize-your-algorithms-in-azure-machine-learning-studio-classic"></a>Escolha os parâmetros para otimizar seus algoritmos no Azure Machine Learning Studio (clássico)

Este tópico descreve como escolher o conjunto de hiperparâmetros corretos para um algoritmo no Azure Machine Learning Studio (clássico). A maioria dos algoritmos de aprendizado de máquina tem parâmetros a serem definidos. Ao treinar um modelo, você precisa fornecer valores para esses parâmetros. A eficácia do modelo treinado depende dos parâmetros de modelo que você escolher. O processo de localizar o conjunto ideal de parâmetros é conhecido como *seleção de modelo*.



Há várias maneiras de fazer a seleção de modelo. No Machine Learning, a validação cruzada é um dos métodos mais amplamente usados para seleção de modelo e é o mecanismo de seleção de modelo padrão na versão clássica do Azure Machine Learning Studio. Como a versão clássica do Azure Machine Learning Studio dá suporte a R e Python, você sempre pode implementar seus próprios mecanismos de seleção de modelo usando o R ou o Python.

Há quatro etapas no processo de encontrar o melhor conjunto de parâmetros:

1. **Definir o espaço de parâmetro**: para o algoritmo, primeiro decida os valores de parâmetro exatos que você deseja considerar.
2. **Definir as configurações de validação cruzada**: decida como escolher as dobras de validação cruzada para o conjunto de um.
3. **Definir a métrica**: decida qual métrica deve ser usada para determinar o melhor conjunto de parâmetros, como precisão, erro de raiz quadrada média, precisão, Recall ou f-score.
4. **Treinar, avaliar e comparar**: para cada combinação exclusiva dos valores de parâmetro, a validação cruzada é executada pelo e com base na métrica de erro que você definir. Após a avaliação e a comparação, você pode escolher o modelo de melhor desempenho.

A imagem a seguir mostra como isso pode ser obtido na versão clássica do Azure Machine Learning Studio.

![Localizar o melhor conjunto de parâmetros](./media/algorithm-parameters-optimize/fig1.png)

## <a name="define-the-parameter-space"></a>Definir o espaço do parâmetro
Você pode definir o conjunto de parâmetros na etapa de inicialização do modelo. O painel de parâmetros de todos os algoritmos de aprendizado de máquina tem dois modos de instrutor: *parâmetro único* e *intervalo de parâmetro*. Escolha o modo de intervalo de parâmetro. No modo de intervalo de parâmetro, você pode inserir vários valores para cada parâmetro. Você pode inserir valores separados por vírgula na caixa de texto.

![Árvore de decisão aumentada de duas classes, parâmetro único](./media/algorithm-parameters-optimize/fig2.png)

 Como alternativa, você pode definir os pontos máximo e mínimo da grade e o número total de pontos a serem gerados com o **uso do construtor de intervalo**. Por padrão, os valores de parâmetro são gerados em uma escala linear. Mas se a opção de **escala de log** estiver marcada, os valores serão gerados na escala de log (ou seja, a taxa dos pontos adjacentes será constante em vez de sua diferença). Para parâmetros de número inteiro, você pode definir um intervalo usando um hífen. Por exemplo, "1-10" significa que todos os inteiros entre 1 e 10 (ambos inclusivos) formam o conjunto de parâmetros. Também há suporte para um modo misto. Por exemplo, o conjunto de parâmetros "1-10, 20, 50" incluiria inteiros 1-10, 20 e 50.

![Árvore de decisão aumentada de duas classes, intervalo de parâmetros](./media/algorithm-parameters-optimize/fig3.png)

## <a name="define-cross-validation-folds"></a>Definir dobras de validação cruzada
A [partição e][partition-and-sample] o módulo de exemplo podem ser usados para atribuir aleatoriamente dobras aos dados. No exemplo de configuração a seguir para o módulo, definimos cinco dobras e atribuímos aleatoriamente um número de dobra para as instâncias de exemplo.

![Partição e exemplo](./media/algorithm-parameters-optimize/fig4.png)

## <a name="define-the-metric"></a>Definir a métrica
O módulo [ajustar hiperparâmetros de modelo][tune-model-hyperparameters] fornece suporte para empiricamente escolhendo o melhor conjunto de parâmetros para um determinado algoritmo e conjunto de dados. Além de outras informações sobre o treinamento do modelo, o painel **Propriedades** desse módulo inclui a métrica para determinar o melhor conjunto de parâmetros. Ele tem duas caixas de listagem suspensas diferentes para algoritmos de classificação e regressão, respectivamente. Se o algoritmo em questão for um algoritmo de classificação, a métrica de regressão será ignorada e vice-versa. Neste exemplo específico, a métrica é **precisão**.   

![Parâmetros de varredura](./media/algorithm-parameters-optimize/fig5.png)

## <a name="train-evaluate-and-compare"></a>Treinar, avaliar e comparar
O mesmo módulo [ajustar hiperparâmetros de modelo][tune-model-hyperparameters] treina todos os modelos que correspondem ao conjunto de parâmetros, avalia várias métricas e, em seguida, cria o modelo de melhor treinamento com base na métrica escolhida. Este módulo tem duas entradas obrigatórias:

* O aprendiz não treinado
* O conjunto de

O módulo também tem uma entrada de conjunto de dados opcional. Conecte o conjunto de dados com informações de dobra à entrada obrigatória do conjunto de dados. Se o conjunto de dados não for atribuído a nenhuma informação de dobra, uma validação cruzada de 10 dobras será executada automaticamente por padrão. Se a atribuição de dobra não for feita e um conjunto de testes for fornecido na porta opcional do conjunto de um, um modo de teste de treinamento será escolhido e o primeiro conjunto de um será usado para treinar o modelo para cada combinação de parâmetro.

![Classificador da árvore de decisão aumentada](./media/algorithm-parameters-optimize/fig6a.png)

O modelo é então avaliado no conjunto de conjuntos de validação. A porta de saída à esquerda do módulo mostra diferentes métricas como funções de valores de parâmetro. A porta de saída à direita fornece o modelo treinado que corresponde ao modelo de melhor desempenho de acordo com a métrica escolhida (**precisão** , nesse caso).  

![Conjunto de validação](./media/algorithm-parameters-optimize/fig6b.png)

Você pode ver os parâmetros exatos escolhidos visualizando a porta de saída à direita. Esse modelo pode ser usado na pontuação de um conjunto de testes ou em um serviço Web operacional depois de salvar como um modelo treinado.

<!-- Module References -->
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[tune-model-hyperparameters]: https://msdn.microsoft.com/library/azure/038d91b6-c2f2-42a1-9215-1f2c20ed1b40/
