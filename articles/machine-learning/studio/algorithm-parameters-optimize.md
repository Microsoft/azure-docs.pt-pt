---
title: Otimizar algoritmos
titleSuffix: ML Studio (classic) - Azure
description: Explica como escolher o parâmetro ideal para um algoritmo no Azure Machine Learning Studio (clássico).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 11/29/2017
ms.openlocfilehash: 52853bb14b49d429100dabeffc46661289f55c84
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84711209"
---
# <a name="choose-parameters-to-optimize-your-algorithms-in-azure-machine-learning-studio-classic"></a>Escolha parâmetros para otimizar os seus algoritmos no Azure Machine Learning Studio (clássico)

Este tópico descreve como escolher o conjunto de hiperparímetros certo para um algoritmo no Azure Machine Learning Studio (clássico). A maioria dos algoritmos de aprendizagem automática têm parâmetros para definir. Quando treina um modelo, tem de fornecer valores para esses parâmetros. A eficácia do modelo treinado depende dos parâmetros do modelo que escolher. O processo de encontrar o conjunto ideal de parâmetros é conhecido como *seleção de modelos.*



Existem várias formas de fazer a seleção de modelos. No machine learning, a validação cruzada é um dos métodos mais utilizados para a seleção de modelos, e é o mecanismo de seleção de modelos padrão no Azure Machine Learning Studio (clássico). Como o Azure Machine Learning Studio (clássico) suporta tanto o R como o Python, pode sempre implementar os seus próprios mecanismos de seleção de modelos utilizando r ou Python.

Existem quatro passos no processo de encontrar o melhor parâmetro definido:

1. **Defina o espaço dos parâmetros**: Para o algoritmo, primeiro decida os valores exatos do parâmetro que pretende considerar.
2. **Definir as definições de validação cruzada**: Decida como escolher dobras de validação cruzada para o conjunto de dados.
3. **Definir a métrica**: Decida qual a métrica a utilizar para determinar o melhor conjunto de parâmetros, tais como precisão, erro quadrado de raiz, precisão, recolha ou f-score.
4. **Treinar, avaliar e comparar:** Para cada combinação única dos valores dos parâmetros, a validação cruzada é realizada por e com base na métrica de erro que define. Após avaliação e comparação, pode escolher o modelo com melhor desempenho.

A imagem a seguir ilustra como isto pode ser alcançado no Azure Machine Learning Studio (clássico).

![Encontre o melhor conjunto de parâmetros](./media/algorithm-parameters-optimize/fig1.png)

## <a name="define-the-parameter-space"></a>Definir o espaço dos parâmetros
Pode definir o parâmetro definido no passo de inicialização do modelo. O painel de parâmetros de todos os algoritmos de aprendizagem automática tem dois modos de treinador: *Single Parâmetro* e *Intervalo de Parâmetros.* Escolha o modo de alcance dos parâmetros. No modo de alcance dos parâmetros, pode introduzir vários valores para cada parâmetro. Pode introduzir valores separados por vírgula na caixa de texto.

![Árvore de decisão reforçada de duas classes, parâmetro único](./media/algorithm-parameters-optimize/fig2.png)

 Em alternativa, pode definir os pontos máximos e mínimos da grelha e o número total de pontos a gerar com **o Use Range Builder**. Por predefinição, os valores dos parâmetros são gerados numa escala linear. Mas se **a Escala de Registo** for verificada, os valores são gerados na escala de registo (isto é, a relação entre os pontos adjacentes é constante em vez da sua diferença). Para parâmetros inteiros, pode definir uma gama usando um hífen. Por exemplo, "1-10" significa que todos os inteiros entre 1 e 10 (ambos inclusive) formam o parâmetro definido. Um modo misto também é suportado. Por exemplo, o parâmetro definido "1-10, 20, 50" incluiria os inteiros 1-10, 20 e 50.

![Árvore de decisão reforçada de duas classes, intervalo de parâmetros](./media/algorithm-parameters-optimize/fig3.png)

## <a name="define-cross-validation-folds"></a>Definir dobras de validação cruzada
O módulo [de partição e amostra][partition-and-sample] pode ser usado para atribuir dobras aleatoriamente aos dados. Na seguinte configuração de amostra para o módulo, definimos cinco dobras e atribuímos aleatoriamente um número de dobra às instâncias da amostra.

![Partição e amostra](./media/algorithm-parameters-optimize/fig4.png)

## <a name="define-the-metric"></a>Definir a métrica
O módulo [Tune Model Hyperparameters][tune-model-hyperparameters] fornece suporte para escolher empiricamente o melhor conjunto de parâmetros para um determinado algoritmo e conjunto de dados. Além de outras informações relativas à formação do modelo, o painel **de propriedades** deste módulo inclui a métrica para determinar o melhor conjunto de parâmetros. Tem duas caixas de lista diferentes para algoritmos de classificação e regressão, respectivamente. Se o algoritmo em questão é um algoritmo de classificação, a métrica de regressão é ignorada e vice-versa. Neste exemplo específico, a métrica é **precisão.**   

![Varrer parâmetros](./media/algorithm-parameters-optimize/fig5.png)

## <a name="train-evaluate-and-compare"></a>Treinar, avaliar e comparar
O mesmo módulo [Tune Model Hyperparameters][tune-model-hyperparameters] treina todos os modelos que correspondem ao conjunto de parâmetros, avalia várias métricas e, em seguida, cria o modelo mais bem treinado com base na métrica que escolher. Este módulo tem duas entradas obrigatórias:

* O aprendiz destreinado
* O conjunto de dados

O módulo também tem uma entrada opcional do conjunto de dados. Ligue o conjunto de dados com a informação dobrável à entrada obrigatória do conjunto de dados. Se o conjunto de dados não for atribuído a nenhuma informação dobrável, então uma validação cruzada de 10 dobras é executada automaticamente por padrão. Se a atribuição dobrável não for feita e for fornecida uma dataset de validação na porta de dataset opcional, então é escolhido um modo de teste de comboio e o primeiro conjunto de dados é utilizado para treinar o modelo para cada combinação de parâmetros.

![Classificador de árvore de decisão reforçada](./media/algorithm-parameters-optimize/fig6a.png)

O modelo é então avaliado no conjunto de dados de validação. A porta de saída esquerda do módulo mostra métricas diferentes como funções dos valores dos parâmetros. A porta de saída certa dá ao modelo treinado que corresponde ao modelo de melhor desempenho de acordo com a métrica escolhida (**Precisão** neste caso).  

![Conjunto de dados de validação](./media/algorithm-parameters-optimize/fig6b.png)

Pode ver os parâmetros exatos escolhidos visualizando a porta de saída certa. Este modelo pode ser utilizado na pontuação de um conjunto de testes ou num serviço web operacionalizado depois de ter sido poupado como modelo treinado.

<!-- Module References -->
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[tune-model-hyperparameters]: https://msdn.microsoft.com/library/azure/038d91b6-c2f2-42a1-9215-1f2c20ed1b40/
