---
title: Avaliar o desempenho dos modelos
titleSuffix: Azure Machine Learning Studio
description: Este artigo demonstra como avaliar o desempenho de um modelo no Azure Machine Learning Studio e fornece uma breve explicação sobre as métricas disponíveis para esta tarefa.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: seodec18, previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: f5e7a923ed5eb692f5fb05082c73b969081c739b
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56820999"
---
# <a name="how-to-evaluate-model-performance-in-azure-machine-learning-studio"></a>Como avaliar o desempenho de modelo no Azure Machine Learning Studio

Este artigo demonstra como avaliar o desempenho de um modelo no Azure Machine Learning Studio e fornece uma breve explicação sobre as métricas disponíveis para esta tarefa. São apresentados três cenários comuns de aprendizagem supervisionado: 

* regression
* Classificação binária 
* classificação de várias classes



Avaliar o desempenho de um modelo é uma das etapas no processo de ciência de dados principal. Ele indica o êxito das classificação (previsões) de um conjunto de dados tem sido por um modelo preparado. 

O Azure Machine Learning Studio suporta a avaliação de modelo por meio dos dois de módulos de aprendizagem principal: [Avaliar modelo] [ evaluate-model] e [modelo de validação cruzada][cross-validate-model]. Estes módulos permitem-lhe ver o seu modelo de desempenho em termos de um número de métricas que são frequentemente utilizadas no machine learning e estatísticas.

## <a name="evaluation-vs-cross-validation"></a>Avaliação vs. Validação cruzada
Validação cruzada e de avaliação são formas para medir o desempenho do seu modelo. Ambos geram métricas de avaliação que pode inspecionar ou comparar com os outros modelos.

[Avaliar modelo] [ evaluate-model] espera um conjunto de dados com a pontuação como entrada (ou 2 caso gostaria de comparar o desempenho dos dois modelos diferentes). Isso significa que precisa preparar o seu modelo com o [modelo de formação] [ train-model] módulo e faça previsões de indisponibilidade de conjunto de dados a utilizar o [Score Model] [ score-model]módulo, antes de pode avaliar os resultados. A avaliação é baseada em etiquetas/probabilidades classificadas, juntamente com as etiquetas verdadeiras, tudo o que são produzidos pela [modelo de pontuação] [ score-model] módulo.

Em alternativa, pode utilizar validação cruzada para efetuar diversas operações train pontuação avaliar (10 subconjuntos) automaticamente em diferentes subconjuntos dos dados de entrada. Os dados de entrada são divididos em 10 partes, onde um está reservado para fins de teste, e a outros 9 para treinamento. Este processo é repetido 10 vezes e as métricas de avaliação são transformadas em médias. Isto ajuda a determinar o quão bem um modelo seria generalizar para novos conjuntos de dados. O [modelo de validação cruzada] [ cross-validate-model] módulo aceita um modelo de destreinado e um conjunto de dados etiquetado e produz os resultados da avaliação de cada um os subconjuntos de 10, além dos resultados da média.

Nas secções seguintes, iremos criar modelos de regressão e classificação simples e avaliar seu desempenho, com ambos os [Evaluate Model] [ evaluate-model] e o [o modelo de validação cruzada ] [ cross-validate-model] módulos.

## <a name="evaluating-a-regression-model"></a>Avaliar um modelo de regressão
Suponha que queremos prever o preço de um carro com alguns recursos como dimensões, potência, especificações de motor e assim por diante. Esse é um problema de regressão típica, onde a variável de destino (*preço*) é um valor numérico contínuo. Conseguimos ajustar um modelo de regressão linear simples que, tendo em conta os valores da característica de um determinado carro, pode prever o preço desse carro. Este modelo de regressão pode ser utilizado para pontuar o mesmo conjunto de dados, com base em com. Assim que tivermos os preços previstos para todos os carros, pode avaliar o desempenho do modelo de observando quanto as previsões evitar em média os preços reais. Para ilustrar isso, usamos o *conjunto de dados do automóvel preço data (Raw)* disponível na **conjuntos de dados guardado** secção no Azure Machine Learning Studio.

### <a name="creating-the-experiment"></a>Criar a experimentação
Adicione os seguintes módulos à sua área de trabalho no Azure Machine Learning Studio:

* Dados do preço automóvel (bruto)
* [Regressão linear][linear-regression]
* [Preparar modelo][train-model]
* [Modelo de pontuação][score-model]
* [Avaliar modelo][evaluate-model]

Ligar as portas, conforme apresentado abaixo na figura 1 e definir a coluna de etiqueta do [Train Model] [ train-model] módulo *preço*.

![Avaliar um modelo de regressão](./media/evaluate-model-performance/1.png)

Figura 1. Avaliar um modelo de regressão.

### <a name="inspecting-the-evaluation-results"></a>Inspecionar os resultados da avaliação
Depois de executar a experimentação, pode clicar na porta de saída a [Evaluate Model] [ evaluate-model] módulo e selecione *Visualize* para ver os resultados da avaliação. As métricas de avaliação disponíveis para modelos de regressão são: *Significa que o erro absoluto*, *raiz Mean erro absoluto*, *erro relativo absoluto*, *relativo ao quadrado erro*e o *coeficiente de Determinação*.

O termo "error" aqui representa a diferença entre o valor previsto e o valor true. O valor absoluto ou o quadrado essa diferença, normalmente, é calculado para capturar a magnitude total de erros em todas as instâncias, como a diferença entre o valor previsto e verdadeiro pode ser negativa em alguns casos. As métricas de erro medem o desempenho de previsão de um modelo de regressão em termos do desvio de média de suas previsões de entre os valores verdadeiros. Valores mais baixos de erro significa que o modelo é mais preciso em fazer previsões. Uma métrica de erro geral igual a zero significa que o modelo é adequado para os dados perfeitamente.

O coeficiente de determinação, o que é também conhecido como R ao quadrado, também é uma forma padrão de medir a eficiência com que o modelo é adequado para os dados. Ele pode ser interpretado como a proporção de variação bem explicada por meio do modelo. Uma maior proporção é melhor nesse caso, onde 1 indica um ajuste perfeito.

![Métricas de avaliação de regressão linear](./media/evaluate-model-performance/2.png)

Figura 2. Métricas de avaliação de regressão linear.

### <a name="using-cross-validation"></a>Usando cruzada validação
Como mencionado anteriormente, pode realizar treinamento repetido, classificação e avaliações automaticamente ao utilizar o [modelo de validação cruzada] [ cross-validate-model] módulo. Tudo o que precisa neste caso é um conjunto de dados, um modelo destreinado e um [modelo de validação cruzada] [ cross-validate-model] módulo (veja a figura abaixo). Tem de definir a coluna de etiqueta *preço* no [modelo de validação cruzada] [ cross-validate-model] propriedades do módulo.

![Um modelo de regressão de validação cruzada](./media/evaluate-model-performance/3.png)

Figura 3. Validação cruzada um modelo de regressão.

Depois de executar a experimentação, pode inspecionar os resultados da avaliação ao clicar na porta saída direito do [modelo de validação cruzada] [ cross-validate-model] módulo. Isso fornecerá uma visão detalhada das métricas para cada iteração (subconjuntos de validação) e os resultados de média de cada uma das métricas (figura 4).

![Resultados da validação cruzada de um modelo de regressão](./media/evaluate-model-performance/4.png)

Figura 4. Resultados da validação cruzada de um modelo de regressão.

## <a name="evaluating-a-binary-classification-model"></a>Avaliar um modelo de classificação binária
Num cenário de classificação binária, a variável de destino tem apenas dois resultados possíveis, por exemplo: {0, 1} ou {FALSO, VERDADEIRO}, {negativo, positivo}. Suponha que tem um conjunto de dados de funcionários para adultos com algumas demográficos e variáveis de emprego, e que é-lhe perguntado para prever o nível de receitas, uma variável binário com os valores {"< = 50 mil", "> 50k"}. Em outras palavras, a classe negativa representa os funcionários que efetuam inferior ou igual a 50 mil por ano e a classe positiva representa todos os outros funcionários. Como no cenário de regressão, vamos preparar um modelo, pontuação alguns dados e, avaliar os resultados. A principal diferença aqui é a escolha de métricas que do Azure Machine Learning Studio computa e saídas. Para ilustrar o cenário de predição de nível de rendimento, iremos utilizar o [adulto](http://archive.ics.uci.edu/ml/datasets/Adult) conjunto de dados para criar uma experimentação do Studio e avaliar o desempenho de um modelo de regressão logística de classe dois, um classificador binário comumente utilizado.

### <a name="creating-the-experiment"></a>Criar a experimentação
Adicione os seguintes módulos à sua área de trabalho no Azure Machine Learning Studio:

* Conjunto de dados de classificação binária de receitas de censo para adultos
* [Regressão logística de duas classes][two-class-logistic-regression]
* [Preparar modelo][train-model]
* [Modelo de pontuação][score-model]
* [Avaliar modelo][evaluate-model]

Ligar as portas, conforme apresentado abaixo na figura 5 e definir a coluna de etiqueta do [Train Model] [ train-model] módulo *rendimento*.

![Avaliar um modelo de classificação binária](./media/evaluate-model-performance/5.png)

Figura 5. Avaliar um modelo de classificação binária.

### <a name="inspecting-the-evaluation-results"></a>Inspecionar os resultados da avaliação
Depois de executar a experimentação, pode clicar na porta de saída a [Evaluate Model] [ evaluate-model] módulo e selecione *Visualize* para ver os resultados da avaliação (figura 7). As métricas de avaliação disponíveis para os modelos de classificação binária estão: *Precisão*, *precisão*, *Lembre-se*, *F1 pontuação*, e *AUC*. Além disso, o módulo produz uma matriz de confusão, que mostra o número de positivos verdadeiros, falsos negativos, falsos positivos e negativos verdadeiros, bem como *ROC*, *precisão/Recolhimento*, e  *Lift* curvas.

Precisão é simplesmente a proporção de instâncias corretamente classificadas. Normalmente, é a métrica primeiro que examinar ao avaliar um classificador. No entanto, quando os dados de teste são desequilibrado (em que a maioria das instâncias de pertence a uma das classes) ou estiver mais interessado no desempenho numa das classes, precisão realmente não captura a eficácia de um classificador. No cenário de nível de classificação de receitas, partem do princípio de que está testando em alguns dados em que 99% das instâncias representam as pessoas que recebem inferior ou igual a 50 mil por ano. É possível obter uma precisão 0.99 ao prever a classe "< = 50 mil" para todas as instâncias. Neste caso aparece o classificador estar fazendo um bom trabalho em geral, mas na realidade, ele não conseguir classificar qualquer um dos indivíduos renda ("% 1) corretamente.

Por esse motivo, é útil computar métricas adicionais que capturam aspectos mais específicos da avaliação. Antes de entrar nos detalhes de tais métricas, é importante compreender a matriz de confusão de uma avaliação de classificação binária. As etiquetas de classe no conjunto de treinamento podem ter apenas 2 valores possíveis, quais nos Referimos normalmente como positivo ou negativo. As instâncias de positivas e negativas que um classificador prevê corretamente são denominadas positivos verdadeiros (TP) e verdadeiros negativos (TN), respectivamente. Da mesma forma, as instâncias classificadas incorretamente são chamadas de falsos positivos (FP) e falsos negativos (FN). A matriz de confusão é simplesmente uma tabela que mostra o número de instâncias que se enquadram em cada uma destas 4 categorias. O Azure Machine Learning Studio automaticamente decide que uma das duas classes no conjunto de dados é a classe positiva. Se as etiquetas de classe são inteiros ou booleanos, as instâncias com nome 'true' ou '1' são atribuídas a classe positiva. Se as etiquetas são cadeias de caracteres, como no caso do conjunto de dados do rendimento, as etiquetas são ordenadas por ordem alfabética e o primeiro nível é escolhido para ser a classe negativa, enquanto o segundo nível é a classe positiva.

![Matriz de confusão de classificação binária](./media/evaluate-model-performance/6a.png)

Figura 6. Matriz de confusão de classificação binária.

Voltando ao problema de classificação de receitas, podemos desejar fazer várias perguntas de avaliação que nos ajudar a compreender o desempenho do classificador utilizado. Uma pergunta muito comum é: "Fora os indivíduos quem o modelo de previsão para ser ganhar > 50k (TP + FP), quantas foram corretamente classificadas (TP)?" Este pode ser às suas perguntas ao observar a **precisão** do modelo, que é a proporção de positivos que estão corretamente classificados: TP/(TP+FP). Outra pergunta comum é "fora de todos os o alto ganhar os funcionários com rendimento > 50k (TP + FN), quantas o classificador de classificar corretamente (TP)". Isto é, na verdade, o **Lembre-se de**, ou a taxa de positiva verdadeira: TP/(TP+fn) do classificador. Pode observar que há uma desvantagem óbvia entre a precisão e lembre-se. Por exemplo, devido um conjunto de dados relativamente equilibrado, um classificador que prevê principalmente positivas instâncias, teria um recall alta, mas uma precisão de baixa em vez disso, como muitas das instâncias negativas seria classificado erroneamente resultando num grande número de falsos positivos. Para ver um desenho de como essas duas métricas variam, pode clicar no **precisão/RECOLHIMENTO** curva na página de saída do resultado da avaliação (parte do canto superior esquerdo da figura 7).

![Resultados da avaliação de classificação binária](./media/evaluate-model-performance/7.png)

Figura 7. Resultados da avaliação de classificação binária.

Outro relacionados com a métrica que é frequentemente utilizada é o **F1 pontuação**, que demora a precisão e lembre-se em consideração. Ele é a média harmônica destas métricas de 2 e é computado como tal: F1 = 2 (Lembre-se na precisão x) / (precisão + recolhimento). A pontuação de F1 é uma boa maneira para resumir a avaliação num único número, mas é sempre uma boa prática para verificar a precisão e lembre-se em conjunto para compreender melhor como se comporta um classificador.

Além disso, um pode inspecionar a taxa de positiva verdadeira vs. a taxa de falsos positivos no **Recetor operacional característica () multiclasse mediante** curva e correspondentes **área sob a curva (AUC)** valor. Quanto mais próximo nessa curva é o canto superior esquerdo, melhor desempenho do classificador é (que é maximizar a taxa de positiva verdadeira enquanto minimiza a taxa de falsos positivos). Curvas que estão próximos diagonal de plotagem, resultado de classificadores que tendem a fazer predições que estão próximos adivinhação aleatório.

### <a name="using-cross-validation"></a>Usando cruzada validação
Como no exemplo de regressão, podemos executar a validação cruzada para repetidamente dar formação e classificação e avaliar diferentes subconjuntos de dados automaticamente. Da mesma forma, podemos usar o [modelo de validação cruzada] [ cross-validate-model] módulo, um modelo de regressão logística destreinado e um conjunto de dados. A coluna de etiqueta tem de ser definida como *rendimento* no [modelo de validação cruzada] [ cross-validate-model] propriedades do módulo. Depois de executar a experimentação e clicar no lado direito de porta de saída a [modelo de validação cruzada] [ cross-validate-model] módulo, podemos ver os valores de métrica de classificação binária para cada subconjuntos de validação, além para a média e desvio-padrão de cada. 

![Validação cruzada de um modelo de classificação binária](./media/evaluate-model-performance/8.png)

Figura 8. Validação cruzada um modelo de classificação binária.

![Resultados da validação cruzada de um classificador binário](./media/evaluate-model-performance/9.png)

Figura 9. Resultados da validação cruzada de um classificador binário.

## <a name="evaluating-a-multiclass-classification-model"></a>Avaliar um modelo de classificação Multiclasses
Nesse experimento, irá utilizar o popular [Iris](http://archive.ics.uci.edu/ml/datasets/Iris "íris") conjunto de dados que contém instâncias de 3 tipos diferentes (classes) da planta íris. Existem 4 funcionalidade valores (comprimento/largura de sépala e Pétala. o comprimento/largura) para cada instância. As experimentações anteriores foi treinado e testado os modelos usando os mesmos conjuntos de dados. Aqui, vamos utilizar o [Split Data] [ split] módulo para criar 2 subconjuntos de dados, a dar formação no primeiro e a pontuação e a avaliar no segundo. O conjunto de dados de Iris está disponível publicamente no [repositório de Aprendizado de máquina de UCI](http://archive.ics.uci.edu/ml/index.html)e podem ser baixados com um [importar dados] [ import-data] módulo.

### <a name="creating-the-experiment"></a>Criar a experimentação
Adicione os seguintes módulos à sua área de trabalho no Azure Machine Learning Studio:

* [Importar dados][import-data]
* [Floresta de decisão de várias classes][multiclass-decision-forest]
* [Dividir os dados][split]
* [Preparar modelo][train-model]
* [Modelo de pontuação][score-model]
* [Avaliar modelo][evaluate-model]

Ligar as portas, conforme apresentado abaixo na figura 10.

Definir o índice de coluna de etiqueta do [Train Model] [ train-model] módulo para 5. O conjunto de dados não tem nenhuma linha de cabeçalho, mas nós sabemos que as etiquetas de classe estão a quinta coluna.

Clique no [importar dados] [ import-data] módulo e o conjunto a *origem de dados* propriedade *URL de Web por meio de HTTP*e o *URL* para http://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data.

Defina a fração de instâncias para ser usada para treinamento no [Split Data] [ split] módulo (0,7 por exemplo).

![Avaliar um classificador de várias classes](./media/evaluate-model-performance/10.png)

Figura 10. Avaliar um classificador de várias classes

### <a name="inspecting-the-evaluation-results"></a>Inspecionar os resultados da avaliação
Execute a experimentação e clique na porta de saída [Evaluate Model][evaluate-model]. Os resultados da avaliação são apresentados sob a forma de uma matriz de confusão, neste caso. A matriz mostra real vs. instâncias previstas para todas as classes de 3.

![Resultados da avaliação de classificação multiclasses](./media/evaluate-model-performance/11.png)

Figura 11. Resultados da avaliação de classificação multiclasses.

### <a name="using-cross-validation"></a>Usando cruzada validação
Como mencionado anteriormente, pode executar repetida de formação, classificação e avaliações automaticamente ao utilizar o [modelo de validação cruzada] [ cross-validate-model] módulo. Precisaria de um conjunto de dados, um modelo destreinado e um [modelo de validação cruzada] [ cross-validate-model] módulo (veja a figura abaixo). Novamente, precisa de definir a coluna de etiqueta do [modelo de validação cruzada] [ cross-validate-model] módulo (índice de coluna de 5 neste caso). Depois de executar a experimentação e clicar à direita de porta de saída a [modelo de validação cruzada][cross-validate-model], pode inspecionar os valores de métrica para cada subconjuntos de validação, bem como o desvio médio e standard. As métricas apresentadas aqui são semelhantes das discutidas no caso de classificação binária. No entanto, observe que na classificação de várias classes, o verdadeiros positivos/negativos e positivos falsos/negativos de computação é feita por contagem numa base por classe, porque não existe nenhuma classe geral positivo ou negativo. Por exemplo, ao computar a precisão ou remoção da classe "Iris-setosa", presume-se que essa é a classe positiva e todos os outros como negativa.

![Um modelo de classificação de várias classes de validação cruzada](./media/evaluate-model-performance/12.png)

Figura 12. Validação cruzada um modelo de classificação Multiclasses.

![Resultados da validação cruzada de um modelo de classificação Multiclasses](./media/evaluate-model-performance/13.png)

Figura 13. Resultados da validação cruzada de um modelo de classificação Multiclasses.

<!-- Module References -->
[cross-validate-model]: https://msdn.microsoft.com/library/azure/75fb875d-6b86-4d46-8bcc-74261ade5826/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[multiclass-decision-forest]: https://msdn.microsoft.com/library/azure/5e70108d-2e44-45d9-86e8-94f37c68fe86/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-logistic-regression]: https://msdn.microsoft.com/library/azure/b0fd7660-eeed-43c5-9487-20d9cc79ed5d/

