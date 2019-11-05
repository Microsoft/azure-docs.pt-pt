---
title: Avaliar o desempenho dos modelos
titleSuffix: Azure Machine Learning Studio (classic)
description: Este artigo demonstra como avaliar o desempenho de um modelo no Azure Machine Learning Studio (clássico) e fornece uma breve explicação sobre as métricas disponíveis para essa tarefa.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18, previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 7947c1be552e72cea9fb0b9214e82a1ecc481fb1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493077"
---
# <a name="how-to-evaluate-model-performance-in-azure-machine-learning-studio-classic"></a>Como avaliar o desempenho do modelo no Azure Machine Learning Studio (clássico)

Este artigo demonstra como avaliar o desempenho de um modelo no Azure Machine Learning Studio (clássico) e fornece uma breve explicação sobre as métricas disponíveis para essa tarefa. Três cenários comuns de aprendizado supervisionado são apresentados: 

* Regressão
* classificação binária 
* classificação multiclasse



Avaliar o desempenho de um modelo é um dos principais estágios do processo de ciência de dados. Indica a êxito da Pontuação (previsões) de um conjunto de uma série de um modelo treinado. 

Azure Machine Learning Studio (clássico) dá suporte à avaliação de modelo por meio de dois dos seus principais módulos de aprendizado de máquina: [avaliar modelo][evaluate-model] e [modelo de validação cruzada][cross-validate-model]. Esses módulos permitem que você veja como seu modelo é executado em termos de várias métricas que são comumente usadas no aprendizado de máquina e estatísticas.

## <a name="evaluation-vs-cross-validation"></a>Avaliação versus validação cruzada
Avaliação e validação cruzada são maneiras padrão de medir o desempenho do seu modelo. Ambos geram métricas de avaliação que você pode inspecionar ou comparar com os de outros modelos.

O [modelo de avaliação][evaluate-model] espera um conjunto de dados pontuado como entrada (ou 2 caso você queira comparar o desempenho de dois modelos diferentes). Isso significa que você precisa treinar seu modelo usando o módulo [modelo de treinamento][train-model] e fazer previsões em algum conjunto de mesmos usando o módulo [modelo de Pontuação][score-model] , antes de poder avaliar os resultados. A avaliação é baseada em rótulos/probabilidades pontuados junto com os rótulos verdadeiros, todos os quais são gerados pelo módulo [modelo de Pontuação][score-model] .

Como alternativa, você pode usar a validação cruzada para executar várias operações Train-Score-Evaluate (10 dobras) automaticamente em diferentes subconjuntos dos dados de entrada. Os dados de entrada são divididos em 10 partes, em que um é reservado para teste e o outro 9 para treinamento. Esse processo é repetido 10 vezes e as métricas de avaliação são calculadas por média. Isso ajuda a determinar como um modelo seria generalizado para novos conjuntos de os. O módulo [modelo de validação cruzada][cross-validate-model] usa um modelo não treinado e algum conjunto de um DataSet rotulado e gera os resultados da avaliação de cada uma das 10 dobras, além dos resultados médios.

Nas seções a seguir, criaremos modelos simples de regressão e classificação e avaliaremos seu desempenho, usando os módulos [avaliar modelo][evaluate-model] e [modelo de validação cruzada][cross-validate-model] .

## <a name="evaluating-a-regression-model"></a>Avaliando um modelo de regressão
Suponha que desejamos prever o preço de um carro usando alguns recursos, como dimensões, potência, especificações de mecanismo e assim por diante. Esse é um problema de regressão típico, em que a variável de destino (*Price*) é um valor numérico contínuo. Podemos ajustar um modelo simples de regressão linear que, considerando os valores de recursos de um determinado carro, pode prever o preço desse carro. Esse modelo de regressão pode ser usado para pontuar o mesmo conjunto de um que treinamos. Assim que tivermos os preços previstos para todos os carros, podemos avaliar o desempenho do modelo examinando o quanto as previsões se desviam dos preços reais em média. Para ilustrar isso, usamos o *conjunto de dados de preço de automóvel (bruto)* disponível na seção DataSets **salvos** na versão clássica do Azure Machine Learning Studio.

### <a name="creating-the-experiment"></a>Criando o experimento
Adicione os seguintes módulos ao seu espaço de trabalho na versão clássica do Azure Machine Learning Studio:

* Dados de preço de automóvel (bruto)
* [Regressão linear][linear-regression]
* [Modelo de treinamento][train-model]
* [Modelo de Pontuação][score-model]
* [Avaliar modelo][evaluate-model]

Conecte as portas conforme mostrado abaixo na Figura 1 e defina a coluna rótulo do módulo [modelo de treinamento][train-model] como *preço*.

![Avaliando um modelo de regressão](./media/evaluate-model-performance/1.png)

Figura 1. Avaliando um modelo de regressão.

### <a name="inspecting-the-evaluation-results"></a>Inspecionando os resultados da avaliação
Depois de executar o experimento, você pode clicar na porta de saída do módulo [avaliar modelo][evaluate-model] e selecionar *Visualizar* para ver os resultados da avaliação. As métricas de avaliação disponíveis para os modelos de regressão são: *erro absoluto médio*, *erro absoluto de média de raiz*, *erro absoluto relativo*, *erro de quadrado relativo*e o *coeficiente de determinação*.

O termo "erro" aqui representa a diferença entre o valor previsto e o valor verdadeiro. O valor absoluto ou o quadrado dessa diferença geralmente é calculado para capturar a magnitude total do erro em todas as instâncias, pois a diferença entre o valor previsto e o verdadeiro pode ser negativa em alguns casos. As métricas de erro medem o desempenho de previsão de um modelo de regressão em termos do desvio médio de suas previsões dos valores verdadeiros. Valores de erro inferiores significam que o modelo é mais preciso para fazer previsões. Uma métrica de erro geral de zero significa que o modelo se ajusta perfeitamente aos dados.

O coeficiente de determinação, que também é conhecido como R quadrado, também é uma maneira padrão de medir o quão bem o modelo se ajusta aos dados. Ele pode ser interpretado como a proporção de variação explicada pelo modelo. Uma proporção mais alta é melhor nesse caso, em que 1 indica um ajuste perfeito.

![Métricas de avaliação de regressão linear](./media/evaluate-model-performance/2.png)

Figura 2. Métricas de avaliação de regressão linear.

### <a name="using-cross-validation"></a>Usando a validação cruzada
Conforme mencionado anteriormente, você pode executar o treinamento, a pontuação e as avaliações repetidas automaticamente usando o módulo [modelo de validação cruzada][cross-validate-model] . Tudo o que você precisa nesse caso é um conjunto de um DataSet, um modelo não treinado e um módulo [modelo de validação cruzada][cross-validate-model] (veja a figura abaixo). Você precisa definir a coluna de rótulo como *Price* nas propriedades do módulo [modelo de validação cruzada][cross-validate-model] .

![Validação cruzada de um modelo de regressão](./media/evaluate-model-performance/3.png)

Figura 3. Validação cruzada de um modelo de regressão.

Depois de executar o experimento, você pode inspecionar os resultados da avaliação clicando na porta de saída à direita do módulo [modelo de validação cruzada][cross-validate-model] . Isso fornecerá uma exibição detalhada das métricas para cada iteração (dobra) e os resultados médios de cada uma das métricas (Figura 4).

![Resultados de validação cruzada de um modelo de regressão](./media/evaluate-model-performance/4.png)

Figura 4. Resultados de validação cruzada de um modelo de regressão.

## <a name="evaluating-a-binary-classification-model"></a>Avaliando um modelo de classificação binária
Em um cenário de classificação binária, a variável de destino tem apenas dois resultados possíveis, por exemplo: {0, 1} ou {false, true}, {negativo, positivo}. Suponha que você receba um conjunto de dados de funcionários adultos com algumas variáveis demográficas e de emprego, e que seja solicitado a prever o nível de renda, uma variável binária com os valores {"< = 50 K", "> 50 K"}. Em outras palavras, a classe negativa representa os funcionários que fazem menor ou igual a 50 K por ano, e a classe positiva representa todos os outros funcionários. Como no cenário de regressão, treinamos um modelo, pontuamos alguns dados e avaliamos os resultados. A principal diferença aqui é a escolha da versão clássica do metricsthe do Azure Machine Learning Studio computações e saídas. Para ilustrar o cenário de previsão de nível de renda, usaremos o conjunto de [conteúdo adulto](https://archive.ics.uci.edu/ml/datasets/Adult) para criar um experimento de estúdio (clássico) e avaliar o desempenho de um modelo de regressão logística de duas classes, um classificador binário comumente usado.

### <a name="creating-the-experiment"></a>Criando o experimento
Adicione os seguintes módulos ao seu espaço de trabalho na versão clássica do Azure Machine Learning Studio:

* Conjunto de censo de classificação binária de renda de adulto
* [Regressão logística de duas classes][two-class-logistic-regression]
* [Modelo de treinamento][train-model]
* [Modelo de Pontuação][score-model]
* [Avaliar modelo][evaluate-model]

Conecte as portas conforme mostrado abaixo na Figura 5 e defina a coluna rótulo do módulo [modelo de treinamento][train-model] como *renda*.

![Avaliando um modelo de classificação binária](./media/evaluate-model-performance/5.png)

Figura 5. Avaliando um modelo de classificação binária.

### <a name="inspecting-the-evaluation-results"></a>Inspecionando os resultados da avaliação
Depois de executar o experimento, você pode clicar na porta de saída do módulo [avaliar modelo][evaluate-model] e selecionar *Visualizar* para ver os resultados da avaliação (Figura 7). As métricas de avaliação disponíveis para modelos de classificação binária são: *precisão*, *precisão*, *recall*, *Pontuação F1*e *auc*. Além disso, o módulo gera uma matriz de confusão que mostra o número de verdadeiros positivos, falsos negativos, falsos positivos e verdadeiros negativos, bem como as curvas *Roc*, *Precision/recall*e de comparação *de* precisão.

A precisão é simplesmente a proporção de instâncias classificadas corretamente. Normalmente, é a primeira métrica que você vê ao avaliar um classificador. No entanto, quando os dados de teste estão desequilibrados (onde a maioria das instâncias pertencem a uma das classes), ou você está mais interessado no desempenho em qualquer uma das classes, a precisão não captura realmente a eficácia de um classificador. No cenário de classificação de nível de renda, suponha que você esteja testando em alguns dados em que 99% das instâncias representam pessoas que ganham menos ou igual a 50 mil por ano. É possível obter uma precisão de 0,99 prevendo a classe "< = 50 mil" para todas as instâncias. O classificador, nesse caso, parece estar fazendo um bom trabalho geral, mas, na realidade, ele falha ao classificar qualquer um dos indivíduos de alto rendimento (o 1%) devidamente.

Por esse motivo, é útil computar métricas adicionais que capturam aspectos mais específicos da avaliação. Antes de entrar nos detalhes dessas métricas, é importante entender a matriz de confusão de uma avaliação de classificação binária. Os rótulos de classe no conjunto de treinamento podem assumir apenas dois valores possíveis, que geralmente nos referimos como positivos ou negativos. As instâncias positivas e negativas que um classificador prevê corretamente são chamadas true positivos (TP) e verdadeiros negativos (TN), respectivamente. Da mesma forma, as instâncias classificadas incorretamente são chamadas de FP (falsos positivos) e falsos negativos (FN). A matriz de confusão é simplesmente uma tabela que mostra o número de instâncias que se enquadram em cada uma dessas quatro categorias. A versão clássica do Azure Machine Learning Studio decide automaticamente qual das duas classes no DataSet é a classe positiva. Se os rótulos de classe forem boolianos ou inteiros, as instâncias ' true ' ou ' 1 ' rotuladas serão atribuídas à classe positiva. Se os rótulos forem cadeias de caracteres, como no caso do conjunto de resultado de renda, os rótulos serão classificados alfabeticamente e o primeiro nível será escolhido como a classe negativa, enquanto o segundo nível é a classe positiva.

![Matriz de confusão de classificação binária](./media/evaluate-model-performance/6a.png)

Figura 6. Matriz de confusão de classificação binária.

Voltando ao problema de classificação de renda, queremos fazer várias perguntas de avaliação que nos ajudam a entender o desempenho do classificador usado. Uma questão muito natural é: "fora dos indivíduos que o modelo preencheu para ganhar > 50 mil (TP + FP), quantos foram classificados corretamente (TP)?" Essa pergunta pode ser respondida examinando a **precisão** do modelo, que é a proporção de positivos que são classificados corretamente: TP/(TP + FP). Outra pergunta comum é "fora de todos os funcionários de alta disponibilidade com renda > 50 mil (TP + FN), quantas o classificador classificou corretamente (TP)". Isso é, na verdade, a **recall**ou a verdadeira taxa positiva: TP/(TP + FN) do classificador. Você pode observar que há uma compensação óbvia entre precisão e RECALL. Por exemplo, dado um conjunto de dados relativamente equilibrado, um classificador que prevê principalmente instâncias positivas, teria uma recall alta, mas uma precisão muito baixa, pois muitas das instâncias negativas seriam erroneamente classificadas, resultando em um grande número de falsos positivos. Para ver uma plotagem de como essas duas métricas variam, você pode clicar na curva de **precisão/recall** na página saída do resultado da avaliação (parte superior esquerda da Figura 7).

![Resultados da avaliação de classificação binária](./media/evaluate-model-performance/7.png)

Figura 7. Resultados da avaliação de classificação binária.

Outra métrica relacionada que geralmente é usada é a **Pontuação F1**, que usa precisão e recall em consideração. É a média harmônica dessas duas métricas e é computada da seguinte forma: F1 = 2 (precisão x recuperação)/(precisão + recall). A pontuação F1 é uma boa maneira de resumir a avaliação em um único número, mas é sempre uma boa prática examinar a precisão e a recuperação em conjunto para entender melhor como um classificador se comporta.

Além disso, é possível inspecionar a taxa de verdadeiro positivo versus a taxa de falsos positivos na curva de **características operacionais do receptor (ROC)** e a **área correspondente sob o valor da curva (AUC)** . Quanto mais próxima essa curva for para o canto superior esquerdo, melhor será o desempenho do classificador (que está maximizando a taxa de verdadeiro positivo, minimizando a taxa de falsos positivos). As curvas que estão próximas à diagonal do gráfico, resultam de classificadores que tendem a fazer previsões próximas à adivinhação aleatória.

### <a name="using-cross-validation"></a>Usando a validação cruzada
Como no exemplo de regressão, podemos executar a validação cruzada para treinar repetidamente, pontuar e avaliar diferentes subconjuntos de dados automaticamente. Da mesma forma, podemos usar o módulo [modelo de validação cruzada][cross-validate-model] , um modelo de regressão logística não treinado e um conjunto de um. A coluna de rótulo deve ser definida como *renda* nas propriedades do módulo [modelo de validação cruzada][cross-validate-model] . Depois de executar o experimento e clicar na porta de saída à direita do módulo [modelo de validação cruzada][cross-validate-model] , podemos ver os valores de métrica de classificação binária para cada dobra, além da média e do desvio padrão de cada um. 

![Validação cruzada de um modelo de classificação binária](./media/evaluate-model-performance/8.png)

Figura 8. Validação cruzada de um modelo de classificação binária.

![Resultados de validação cruzada de um classificador binário](./media/evaluate-model-performance/9.png)

Figura 9. Resultados de validação cruzada de um classificador binário.

## <a name="evaluating-a-multiclass-classification-model"></a>Avaliando um modelo de classificação multiclasse
Neste experimento, usaremos o conjunto de uma popular [íris](https://archive.ics.uci.edu/ml/datasets/Iris "Íris") , que contém instâncias de três tipos diferentes (classes) da planta íris. Há quatro valores de recursos (comprimento/largura e comprimento/largura de pétala) para cada instância. Nos experimentos anteriores, treinamos e testamos os modelos usando os mesmos conjuntos de valores. Aqui, usaremos o módulo [dividir dados][split] para criar dois subconjuntos dos dados, treinar no primeiro e pontuar e avaliar no segundo. O conjunto de dados íris está publicamente disponível no [repositório do Machine Learning UCI](https://archive.ics.uci.edu/ml/index.html)e pode ser baixado usando um módulo [importar dados][import-data] .

### <a name="creating-the-experiment"></a>Criando o experimento
Adicione os seguintes módulos ao seu espaço de trabalho na versão clássica do Azure Machine Learning Studio:

* [Importar dados][import-data]
* [Floresta de decisão multiclasse][multiclass-decision-forest]
* [Dividir dados][split]
* [Modelo de treinamento][train-model]
* [Modelo de Pontuação][score-model]
* [Avaliar modelo][evaluate-model]

Conecte as portas, conforme mostrado abaixo na Figura 10.

Defina o índice de coluna de rótulo do módulo [modelo de treinamento][train-model] como 5. O conjunto de linhas não tem nenhuma linha de cabeçalho, mas sabemos que os rótulos de classe estão na quinta coluna.

Clique no módulo [importar dados][import-data] e defina a propriedade *fonte de dados* como *URL da Web via HTTP*e a *URL* para http://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data.

Defina a fração de instâncias a serem usadas para treinamento no módulo [dividir dados][split] (0,7, por exemplo).

![Avaliando um classificador multiclasse](./media/evaluate-model-performance/10.png)

Figura 10. Avaliando um classificador multiclasse

### <a name="inspecting-the-evaluation-results"></a>Inspecionando os resultados da avaliação
Execute o experimento e clique na porta de saída do [modelo de avaliação][evaluate-model]. Os resultados da avaliação são apresentados na forma de uma matriz de confusão, nesse caso. A matriz mostra as instâncias real versus previstas para todas as 3 classes.

![Resultados da avaliação de classificação multiclasse](./media/evaluate-model-performance/11.png)

Figura 11. Resultados da avaliação de classificação multiclasse.

### <a name="using-cross-validation"></a>Usando a validação cruzada
Conforme mencionado anteriormente, você pode executar o treinamento, a pontuação e avaliações repetidas automaticamente usando o módulo [modelo de validação cruzada][cross-validate-model] . Você precisaria de um conjunto de um, um modelo não treinado e um módulo de [modelo de validação cruzada][cross-validate-model] (veja a figura abaixo). Novamente, você precisa definir a coluna de rótulo do módulo [modelo de validação cruzada][cross-validate-model] (índice de coluna 5, neste caso). Depois de executar o experimento e clicar na porta de saída à direita do [modelo de validação cruzada][cross-validate-model], você pode inspecionar os valores de métrica para cada dobra, bem como a média e o desvio padrão. As métricas exibidas aqui são semelhantes àquelas discutidas no caso de classificação binária. No entanto, observe que, na classificação multiclasse, a computação dos verdadeiros positivos/negativos e falsos positivos/negativos é feita contando-se com base em cada classe, pois não há nenhuma classe positiva ou negativa geral. Por exemplo, ao computar a precisão ou recall da classe ' íris-setosa ', supõe-se que essa é a classe positiva e todas as outras como negativas.

![Validação cruzada de um modelo de classificação multiclasse](./media/evaluate-model-performance/12.png)

Figura 12. Validação cruzada de um modelo de classificação multiclasse.

![Resultados de validação cruzada de um modelo de classificação multiclasse](./media/evaluate-model-performance/13.png)

Figura 13. Resultados de validação cruzada de um modelo de classificação multiclasse.

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

