---
title: Como escolher algoritmos
titleSuffix: ML Studio (classic) Azure
description: Como escolher algoritmos de Azure Machine Learning Studio (clássico) para aprendizado supervisionado e não supervisionado em experimentos de clustering, classificação ou regressão.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-ms.author=pakalra, previous-author=pakalra
ms.date: 03/04/2019
ms.openlocfilehash: e8d296f8752e06e6e47c349be9c900b9d0489ec5
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73619770"
---
# <a name="how-to-choose-algorithms-for-azure-machine-learning-studio-classic"></a>Como escolher algoritmos para Azure Machine Learning Studio (clássico)

[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

A resposta à pergunta "qual algoritmo de aprendizado de máquina devo usar?" é sempre "depende". Depende do tamanho, da qualidade e da natureza dos dados. Depende do que você deseja fazer com a resposta. Depende de como a matemática do algoritmo foi traduzida em instruções para o computador que você está usando. E depende de quanto tempo você tem. Até mesmo os cientistas de dados mais experientes não conseguem saber qual algoritmo terá melhor desempenho antes de experimentá-los.

Machine Learning Studio (clássico) fornece algoritmos de ponta, como árvores de decisão aumentadas escalonáveis, sistemas de recomendação Bayesiana, redes neurais profundas e selvas de decisão desenvolvidos no Microsoft Research. Estão também incluídos pacotes de código aberto e dimensionáveis de machine learning, como o Vowpal Wabbit. A versão clássica do Machine Learning Studio dá suporte a algoritmos de aprendizado de máquina para classificação de multiclasse e binária, regressão e clustering. Consulte a lista completa de [módulos de Machine Learning](/azure/machine-learning/studio-module-reference/index).
A documentação fornece algumas informações sobre cada algoritmo e como ajustar parâmetros para otimizar o algoritmo para seu uso.  


## <a name="the-machine-learning-algorithm-cheat-sheet"></a>A folha de consulta do algoritmo de Machine Learning

A **[folha](../algorithm-cheat-sheet.md)** de consulta do algoritmo de Microsoft Azure Machine Learning ajuda a escolher o algoritmo de aprendizado de máquina correto para suas soluções de análise preditiva da biblioteca Azure Machine Learning de algoritmos.
Este artigo orienta você sobre como usar esta folha de consulta.

> [!NOTE]
> Para baixar a folha de consulta e acompanhar este artigo, vá para a [página](../algorithm-cheat-sheet.md)de dicas de algoritmo de aprendizado de máquina.
> 
> 

Essas recomendações são comentários e dicas compiladas de muitos cientistas de dados e especialistas em aprendizado de máquina. Não concordamos em tudo, mas tentamos harmonizar nossas opiniões em um consenso aproximado. A maioria das declarações de desacordo começa com "depende..."


> [!TIP]
> Para baixar uma visão geral de infográfico de fácil compreensão dos conceitos básicos de aprendizado de máquina para saber mais sobre algoritmos populares usados para responder a perguntas comuns de aprendizado de máquina, consulte [noções básicas de aprendizado de máquina com exemplos de algoritmo](basics-infographic-with-algorithm-examples.md).

## <a name="flavors-of-machine-learning"></a>Tipos de aprendizado de máquina

### <a name="supervised"></a>Supervisionado

Os algoritmos de aprendizado supervisionados fazem previsões com base em um conjunto de exemplos. Por exemplo, os preços de ações históricas podem ser usados para fazer palpites sobre preços futuros. Cada exemplo usado para treinamento é rotulado com o valor de interesse — nesse caso, o preço da ação. Um algoritmo de aprendizado supervisionado procura padrões nesses rótulos de valor. Ele pode usar qualquer informação que possa ser relevante — o dia da semana, a temporada, os dados financeiros da empresa, o tipo de setor, a presença de eventos de geopolítica de interrupção – e cada algoritmo procura tipos diferentes de padrões. Depois que o algoritmo encontrar o melhor padrão possível, ele usará esse padrão para fazer previsões para dados de teste sem rótulo — os preços do amanhã.

O aprendizado supervisionado é um tipo popular e útil de aprendizado de máquina. Com uma exceção, todos os módulos na versão clássica do Azure Machine Learning Studio são algoritmos de aprendizado supervisionados. Há vários tipos específicos de aprendizado supervisionado que são representados dentro de Azure Machine Learning Studio (clássico): classificação, regressão e detecção de anomalias.

* **Classificação**. Quando os dados estão sendo usados para prever uma categoria, o aprendizado supervisionado também é chamado de classificação. Esse é o caso ao atribuir uma imagem como uma imagem de um ' gato ' ou de um ' cachorro '. Quando há apenas duas opções, ela é chamada de **classificação binomial**ou de **duas classes** . Quando há mais categorias, como ao prever o vencedor do campeonato de loucura de março de NCAA, esse problema é conhecido como **classificação de várias classes**.
* **Regressão**. Quando um valor está sendo previsto, assim como os preços de estoque, o aprendizado supervisionado é chamado de regressão.
* **Detecção de anomalias**. Às vezes, o objetivo é identificar pontos de dados que são simplesmente incomuns. Na detecção de fraudes, por exemplo, qualquer padrão de gastos de cartão de crédito altamente incomum é suspeito. As variações possíveis são tão numerosas e os exemplos de treinamento, portanto, não é viável saber qual é a aparência da atividade fraudulenta. A abordagem que a detecção de anomalias leva é simplesmente aprender como é a atividade normal (usando um histórico de transações não fraudulentas) e identificar tudo o que é significativamente diferente.

### <a name="unsupervised"></a>Sem supervisão

No aprendizado não supervisionado, os pontos de dados não têm rótulos associados a eles. Em vez disso, o objetivo de um algoritmo de aprendizado não supervisionado é organizar os dados de alguma forma ou descrever sua estrutura. Isso pode significar agrupá-lo em clusters ou encontrar diferentes maneiras de examinar dados complexos para que ele pareça mais simples ou mais organizado.

### <a name="reinforcement-learning"></a>Aprendizagem por reforço

No reforço Learning, o algoritmo Obtém a escolha de uma ação em resposta a cada ponto de dados. O algoritmo de aprendizado também recebe um sinal de recompensa um pouco mais tarde, indicando quão boa é a decisão.
Com base nesse caso, o algoritmo modifica sua estratégia para alcançar o prêmio mais alto. No momento, não há nenhum módulo de algoritmo de aprendizado reforço em Azure Machine Learning Studio (clássico). O aprendizado de reforço é comum em robótica, em que o conjunto de leituras de sensor em um ponto no tempo é um ponto de dados e o algoritmo deve escolher a próxima ação do robô. Também é uma opção natural para Internet das Coisas aplicativos.

## <a name="considerations-when-choosing-an-algorithm"></a>Considerações ao escolher um algoritmo

### <a name="accuracy"></a>Correta

Obter a resposta mais precisa possível nem sempre é necessário.
Às vezes, uma aproximação é adequada, dependendo de como você deseja usá-la. Se esse for o caso, você poderá reduzir drasticamente o tempo de processamento acompanhando os métodos mais aproximados. Outra vantagem de métodos mais aproximados é que eles tendem naturalmente a evitar o superajuste.

### <a name="training-time"></a>Tempo de treinamento

O número de minutos ou horas necessárias para treinar um modelo varia muito entre algoritmos. O tempo de treinamento geralmente está muito ligado à precisão – um normalmente acompanha o outro. Além disso, alguns algoritmos são mais sensíveis ao número de pontos de dados do que outros.
Quando o tempo é limitado, ele pode orientar a escolha do algoritmo, especialmente quando o conjunto de dados é grande.

### <a name="linearity"></a>Linearidade

Muitos algoritmos de aprendizado de máquina fazem uso da linearidade. Os algoritmos de classificação linear pressupõem que as classes possam ser separadas por uma linha reta (ou sua analogia de alta dimensional). Isso inclui regressão logística e máquinas de vetor de suporte (conforme implementado em Azure Machine Learning Studio (clássico)).
Algoritmos de regressão linear pressupõem que as tendências de dados seguem uma linha reta. Essas suposições não são ruins para alguns problemas, mas em outras, elas reduzem a precisão.

![Limite de classe não linear](./media/algorithm-choice/image1.png)

***Limite de classe não linear*** *-depender de um algoritmo de classificação linear resultaria em baixa precisão*

![Dados com uma tendência não linear](./media/algorithm-choice/image2.png)

***Dados com uma tendência não linear*** *– usar um método de regressão linear geraria erros muito maiores do que o necessário*

Apesar de seus perigos, os algoritmos lineares são muito populares como uma primeira linha de ataque. Eles tendem a ser forma algorítmica simples e rápidos de treinar.

### <a name="number-of-parameters"></a>Número de parâmetros

Os parâmetros são os botões que um cientista de dados obtém para se transformar ao configurar um algoritmo. Eles são números que afetam o comportamento do algoritmo, como tolerância a erros ou número de iterações, ou opções entre variantes de como o algoritmo se comporta. Às vezes, o tempo de treinamento e a precisão do algoritmo podem ser bastante sensíveis para obter apenas as configurações corretas. Normalmente, os algoritmos com um grande número de parâmetros exigem a mais avaliação e o erro para encontrar uma boa combinação.

Como alternativa, há um bloco de módulo de [limpeza de parâmetro](algorithm-parameters-optimize.md) na versão clássica do Azure Machine Learning Studio que tenta automaticamente todas as combinações de parâmetros em qualquer granularidade escolhida. Embora essa seja uma ótima maneira de certificar-se de que você colocou o espaço de parâmetro, o tempo necessário para treinar um modelo aumenta exponencialmente com o número de parâmetros.

A vantagem é que ter muitos parâmetros normalmente indica que um algoritmo tem maior flexibilidade. Geralmente, isso pode atingir uma precisão muito boa, desde que você encontre a combinação certa de configurações de parâmetro.

### <a name="number-of-features"></a>Número de recursos

Para determinados tipos de dados, o número de recursos pode ser muito grande em comparação ao número de pontos de dados. Geralmente, esse é o caso com dados genéticos ou textuais. O grande número de recursos pode atrasár alguns algoritmos de aprendizado, tornando o tempo de treinamento unfeasibly longo. As máquinas de vetor de suporte são particularmente adequadas para esse caso (veja abaixo).

### <a name="special-cases"></a>Casos especiais

Alguns algoritmos de aprendizado fazem suposições específicas sobre a estrutura dos dados ou os resultados desejados. Se você puder encontrar um que atenda às suas necessidades, ele poderá fornecer resultados mais úteis, previsões mais precisas ou tempos de treinamento mais rápidos.

| **Algoritmo** | **Correta** | **Tempo de treinamento** | **Linearidade** | **Parâmetros** | **Notas** |
| --- |:---:|:---:|:---:|:---:| --- |
| **Classificação de duas classes** | | | | | |
| [regressão logística](/azure/machine-learning/studio-module-reference/two-class-logistic-regression) | |● |● |5 | |
| [floresta de decisão](/azure/machine-learning/studio-module-reference/two-class-decision-forest) |● |○ | |6 | |
| [selva de decisão](/azure/machine-learning/studio-module-reference/two-class-decision-jungle) |● |○ | |6 |Superfície de memória insuficiente |
| [árvore de decisão aumentada](/azure/machine-learning/studio-module-reference/two-class-boosted-decision-tree) |● |○ | |6 |Grande volume de memória |
| [rede neural](/azure/machine-learning/studio-module-reference/two-class-neural-network) |● | | |9 |[A personalização adicional é possível](azure-ml-netsharp-reference-guide.md) |
| [média de perceptron](/azure/machine-learning/studio-module-reference/two-class-averaged-perceptron) |○ |○ |● |4 | |
| [computador de vetor de suporte](/azure/machine-learning/studio-module-reference/two-class-support-vector-machine) | |○ |● |5 |Bom para grandes conjuntos de recursos |
| [computador de vetor de suporte em profundidade local](/azure/machine-learning/studio-module-reference/two-class-locally-deep-support-vector-machine) |○ | | |8 |Bom para grandes conjuntos de recursos |
| [Máquina do ponto de Bayes](/azure/machine-learning/studio-module-reference/two-class-bayes-point-machine) | |○ |● |3 | |
| **Classificação de várias classes** | | | | | |
| [regressão logística](/azure/machine-learning/studio-module-reference/multiclass-logistic-regression) | |● |● |5 | |
| [floresta de decisão](/azure/machine-learning/studio-module-reference/multiclass-decision-forest) |● |○ | |6 | |
| [selva de decisão](/azure/machine-learning/studio-module-reference/multiclass-decision-jungle) |● |○ | |6 |Superfície de memória insuficiente |
| [rede neural](/azure/machine-learning/studio-module-reference/multiclass-neural-network) |● | | |9 |[A personalização adicional é possível](azure-ml-netsharp-reference-guide.md) |
| [One-v-todos](/azure/machine-learning/studio-module-reference/one-vs-all-multiclass) |- |- |- |- |Consulte Propriedades do método de duas classes selecionado |
| **Regressão** | | | | | |
| [linear](/azure/machine-learning/studio-module-reference/linear-regression) | |● |● |4 | |
| [Bayesiana linear](/azure/machine-learning/studio-module-reference/bayesian-linear-regression) | |○ |● |2 | |
| [floresta de decisão](/azure/machine-learning/studio-module-reference/decision-forest-regression) |● |○ | |6 | |
| [árvore de decisão aumentada](/azure/machine-learning/studio-module-reference/boosted-decision-tree-regression) |● |○ | |5 |Grande volume de memória |
| [Quantil de floresta rápida](/azure/machine-learning/studio-module-reference/fast-forest-quantile-regression) |● |○ | |9 |Distribuições em vez de previsões de ponto |
| [rede neural](/azure/machine-learning/studio-module-reference/neural-network-regression) |● | | |9 |[A personalização adicional é possível](azure-ml-netsharp-reference-guide.md) |
| [Poisson](/azure/machine-learning/studio-module-reference/poisson-regression) | | |● |5 |Tecnicamente log-linear. Para prever contagens |
| [numera](/azure/machine-learning/studio-module-reference/ordinal-regression) | | | |0 |Para prever a ordenação de classificação |
| **Deteção de anomalias** | | | | | |
| [computador de vetor de suporte](/azure/machine-learning/studio-module-reference/one-class-support-vector-machine) |○ |○ | |2 |Especialmente bom para grandes conjuntos de recursos |
| [Detecção de anomalias baseada em PCA](/azure/machine-learning/studio-module-reference/pca-based-anomaly-detection) | |○ |● |3 | |
| [K-significa](/azure/machine-learning/studio-module-reference/k-means-clustering) | |○ |● |4 |Um algoritmo de clustering |

**Propriedades do algoritmo:**

**●** -mostra excelente precisão, tempos de treinamento rápidos e o uso de linearidade

**○** -mostra uma boa precisão e tempos de treinamento moderado

## <a name="algorithm-notes"></a>Notas de algoritmo

### <a name="linear-regression"></a>Regressão linear

Conforme mencionado anteriormente, a [regressão linear](/azure/machine-learning/studio-module-reference/linear-regression) ajusta-se a uma linha (ou plano ou hiperplano) para o conjunto de dados. É uma força de hoje, simples e rápida, mas pode ser excessivamente simplista para alguns problemas.

![Dados com uma tendência linear](./media/algorithm-choice/image3.png)

***Dados com uma tendência linear***

### <a name="logistic-regression"></a>Regressão logística

Embora inclua ' regressão ' no nome, a regressão logística é, na verdade, uma ferramenta poderosa para classificação de [duas classes](/azure/machine-learning/studio-module-reference/two-class-logistic-regression) e [multiclasse](/azure/machine-learning/studio-module-reference/multiclass-logistic-regression) . É rápido e simples. O fato de que ela usa uma curva em forma de ' s em vez de uma linha reta torna-a uma opção natural para dividir os dados em grupos. A regressão logística fornece limites de classe linear, portanto, ao usá-lo, verifique se uma aproximação linear é algo com o qual você pode viver.

![Regressão logística para dados de duas classes com apenas um recurso](./media/algorithm-choice/image4.png)

***Uma regressão logística para dados de duas classes com apenas um recurso*** *– o limite de classe é o ponto em que a curva logística é tão próxima de ambas as classes*

### <a name="trees-forests-and-jungles"></a>Árvores, florestas e selvas

As florestas de decisão ([regressão](/azure/machine-learning/studio-module-reference/decision-forest-regression), [duas classes](/azure/machine-learning/studio-module-reference/two-class-decision-forest)e [multiclasse),](/azure/machine-learning/studio-module-reference/multiclass-decision-forest)selvas de decisão ([duas classes](/azure/machine-learning/studio-module-reference/two-class-decision-jungle) e [multiclasse](/azure/machine-learning/studio-module-reference/multiclass-decision-jungle)) e árvores de decisão aumentadas ([regressão](/azure/machine-learning/studio-module-reference/boosted-decision-tree-regression) e [duas classes](/azure/machine-learning/studio-module-reference/two-class-boosted-decision-tree)) são todas baseadas na decisão árvores, um conceito básico de aprendizado de máquina. Há muitas variantes de árvores de decisão, mas todas fazem a mesma coisa — subdividir o espaço do recurso em regiões com principalmente o mesmo rótulo. Elas podem ser regiões de categoria consistente ou valor constante, dependendo se você estiver fazendo classificação ou regressão.

![A árvore de decisão subdivide um espaço de recurso](./media/algorithm-choice/image5.png)

***Uma árvore de decisão subdivide um espaço de recurso em regiões de valores aproximadamente uniformes***

Como um espaço de recurso pode ser subdividido em regiões arbitrariamente pequenas, é fácil imaginar a divisão do suficiente para ter um ponto de dados por região. Esse é um exemplo extremo de superajuste. Para evitar isso, um grande conjunto de árvores é construído com cuidado matemático especial para garantir que as árvores não sejam correlacionadas. A média dessa "floresta de decisão" é uma árvore que evita o superajuste. As florestas de decisão podem usar muita memória. As selvas de decisão são uma variante que consome menos memória às custas de um tempo de treinamento um pouco mais longo.

As árvores de decisão aumentadas evitam o superajuste, limitando quantas vezes eles podem subdividir e quantos pontos de dados são permitidos em cada região. O algoritmo constrói uma sequência de árvores, sendo que cada uma delas aprende a compensar o erro deixado pela árvore antes. O resultado é um aprendiz muito preciso que tende a usar muita memória. Para obter a descrição técnica completa, confira o [documento original do Friedman](https://www-stat.stanford.edu/~jhf/ftp/trebst.pdf).

A [regressão rápida de Quantil de floresta](/azure/machine-learning/studio-module-reference/fast-forest-quantile-regression) é uma variação de árvores de decisão para o caso especial em que você deseja saber não apenas o valor típico (mediano) dos dados em uma região, mas também sua distribuição na forma de quantis.

### <a name="neural-networks-and-perceptrons"></a>Redes neurais e perceptrons

As redes neurais são algoritmos de aprendizado inspirados em cérebro que abrangem problemas de [multiclasse](/azure/machine-learning/studio-module-reference/multiclass-neural-network), de [duas classes](/azure/machine-learning/studio-module-reference/two-class-neural-network)e de [regressão](/azure/machine-learning/studio-module-reference/neural-network-regression) . Eles são fornecidos em uma variedade infinita, mas as redes neurais dentro da versão clássica do Azure Machine Learning Studio são toda a forma de grafos acíclico direcionados. Isso significa que os recursos de entrada são passados para frente (nunca para trás) por meio de uma sequência de camadas antes de serem transformados em saídas. Em cada camada, as entradas são ponderadas em várias combinações, somadas e passadas para a próxima camada. Essa combinação de cálculos simples resulta na capacidade de aprender limites de classe e tendências de dados sofisticados, aparentemente por mágica. Redes de várias camadas dessa classificação realizam o "aprendizado profundo" que impulsiona tanto o relatório técnico quanto a ficção científica.

No entanto, esse alto desempenho não é fornecido gratuitamente. As redes neurais podem levar muito tempo para serem treinadas, especialmente para grandes conjuntos de dados com muitos recursos. Eles também têm mais parâmetros do que a maioria dos algoritmos, o que significa que a limpeza de parâmetros expande o tempo de treinamento um grande problema.
E para os superissores que desejam [especificar sua própria estrutura de rede](azure-ml-netsharp-reference-guide.md), as possibilidades são inesgotáveis.

![Limites aprendidos por redes neurais](./media/algorithm-choice/image6.png)

***Os limites aprendidos pelas redes neurais podem ser complexos e irregulares***

A [média de perceptron de duas classes](/azure/machine-learning/studio-module-reference/two-class-averaged-perceptron) é a resposta das redes neurais para tempos de treinamento mais progotados. Ele usa uma estrutura de rede que fornece limites de classe lineares. É quase primitivo pelos padrões atuais, mas tem um longo histórico de trabalhar de forma robusta e é pequeno o suficiente para aprender rapidamente.

### <a name="svms"></a>SVMs

As máquinas de vetor de suporte (SVMs) encontram o limite que separa as classes até a maior margem possível. Quando as duas classes não podem ser claramente separadas, os algoritmos encontram o melhor limite possível. Conforme escrito em Azure Machine Learning Studio (clássico), o [SVM de duas classes](/azure/machine-learning/studio-module-reference/two-class-support-vector-machine) faz isso somente com uma linha reta (em SVM-Speak, ele usa um kernel linear).
Como ele faz essa aproximação linear, ele pode ser executado com bastante rapidez. O local em que ele realmente se destaca é com dados intensos de recursos, como dados de texto ou de genoma. Nesses casos, SVMs são capazes de separar classes mais rapidamente e com menos superajuste do que a maioria dos outros algoritmos, além de exigir apenas uma quantidade modesto de memória.

![Limite de classe de máquina de vetor de suporte](./media/algorithm-choice/image7.png)

***Um limite de classe de máquina de vetor de suporte típico maximiza a margem separando duas classes***

Outro produto da Microsoft Research, a [SVM profunda local de duas classes](/azure/machine-learning/studio-module-reference/two-class-locally-deep-support-vector-machine) é uma variante não linear de SVM que mantém a maior parte da eficiência da velocidade e da memória da versão linear. É ideal para casos em que a abordagem linear não forneça respostas precisas o suficiente. Os desenvolvedores o mantiveram rapidamente, dividindo o problema em uma série de pequenos problemas de SVM lineares. Leia a [Descrição completa](http://proceedings.mlr.press/v28/jose13.html) para obter os detalhes sobre como eles retiraram esse truque.

Usando uma extensão inteligente de SVMs não lineares, o [SVM de uma classe](/azure/machine-learning/studio-module-reference/one-class-support-vector-machine) desenha um limite que descreve rigidamente o conjunto de dados inteiro. É útil para detecção de anomalias. Todos os novos pontos de dados que estão distantes fora desse limite são incomuns o suficiente para serem notáveis.

### <a name="bayesian-methods"></a>Métodos Bayesiana

Os métodos Bayesiana têm uma qualidade altamente desejável: eles evitam o superajuste. Eles fazem isso fazendo algumas suposições com antecedência sobre a distribuição provável da resposta. Outro subproduto dessa abordagem é que eles têm poucos parâmetros. A versão clássica do Azure Machine Learning Studio tem algoritmos Bayesiana para a classificação ([máquina de ponto de Bayes de duas classes](/azure/machine-learning/studio-module-reference/two-class-bayes-point-machine)) e regressão ([regressão linear Bayesiana](/azure/machine-learning/studio-module-reference/bayesian-linear-regression)).
Observe que eles pressupõem que os dados podem ser divididos ou ajustados com uma linha reta.

Em uma nota histórica, as máquinas de ponto do Bayes foram desenvolvidas no Microsoft Research. Eles têm algum trabalho teórico excepcionalmente bonito por trás deles. O aluno interessado é direcionado para o [artigo original em JMLR](http://jmlr.org/papers/volume1/herbrich01a/herbrich01a.pdf) e um [blog criterioso de Chris Bishop](https://blogs.technet.com/b/machinelearning/archive/2014/10/30/embracing-uncertainty-probabilistic-inference.aspx).

### <a name="specialized-algorithms"></a>Algoritmos especializados
Se você tiver uma meta muito específica, poderá estar em sorte. Dentro da coleção Azure Machine Learning Studio (clássica), há algoritmos especializados em:

- Previsão de classificação ([regressão ordinal](/azure/machine-learning/studio-module-reference/ordinal-regression)),
- Previsão de contagem ([regressão Poisson](/azure/machine-learning/studio-module-reference/poisson-regression)),
- detecção de anomalias (uma baseada na [análise de componentes principais](/azure/machine-learning/studio-module-reference/pca-based-anomaly-detection) e outra com base em [máquinas de vetor de suporte](/azure/machine-learning/studio-module-reference/one-class-support-vector-machine))
- clustering ([K-](/azure/machine-learning/studio-module-reference/k-means-clustering)means)

![Detecção de anomalias baseada em PCA](./media/algorithm-choice/image8.png)

***Detecção de anomalias baseada em PCA*** *– a grande maioria dos dados se enquadra em uma distribuição de estereótipo; os pontos deviating drasticamente dessa distribuição são suspeitos*

![Conjunto de dados agrupado usando K-means](./media/algorithm-choice/image9.png)

***Um conjunto de dados é agrupado em cinco clusters usando K-means***

Há também um [classificador multiclasse Ensemble One-v-All](/azure/machine-learning/studio-module-reference/one-vs-all-multiclass), que quebra o problema de classificação de n-classe em problemas de classificação de n-1 2-Class. A precisão, o tempo de treinamento e as propriedades de linearidade são determinados pelos classificadores de duas classes usados.

![Classificadores de duas classes combinados para formar um classificador de três classes](./media/algorithm-choice/image10.png)

***Um par de classificadores de duas classes é combinado para formar um classificador de três classes***

A versão clássica do Azure Machine Learning Studio também inclui acesso a uma poderosa estrutura de aprendizado de máquina sob o título de [Vowpal Wabbit](/azure/machine-learning/studio-module-reference/train-vowpal-wabbit-version-7-4-model).
VW desafia categorização aqui, já que ela pode aprender problemas de classificação e regressão e pode até mesmo aprender com dados parcialmente não rotulados. Você pode configurá-lo para usar qualquer um dos vários algoritmos de aprendizado, funções de perda e algoritmos de otimização. Ele foi projetado desde o início para ser eficiente, paralelo e extremamente rápido. Ele lida com conjuntos de recursos grandes do absurdamente com pouco esforço aparente.
Iniciado e liderado pelo próprio John Langford, da Microsoft Research, VW é a fórmula de uma entrada em um campo de algoritmos de carros de ações. Nem todo problema se encaixa VW, mas se o seu tiver, pode valer a pena subir a curva de aprendizado em sua interface. Ele também está disponível como [código-fonte aberto autônomo](https://github.com/JohnLangford/vowpal_wabbit) em várias linguagens.

## <a name="next-steps"></a>Passos Seguintes

* Para baixar uma visão geral de infográfico de fácil compreensão dos conceitos básicos de aprendizado de máquina para saber mais sobre algoritmos populares usados para responder a perguntas comuns de aprendizado de máquina, consulte [noções básicas de aprendizado de máquina com exemplos de algoritmo](basics-infographic-with-algorithm-examples.md).

* Para obter uma lista por categoria de todos os algoritmos de aprendizado de máquina disponíveis no Machine Learning Studio (clássico), consulte [inicializar o modelo](/azure/machine-learning/studio-module-reference/machine-learning-initialize-model) no algoritmo de Machine Learning Studio (clássico) e na ajuda do módulo.

* Para obter uma lista completa alfabética de algoritmos e módulos na versão clássica do Machine Learning Studio, consulte [a lista de a-Z de módulos Machine Learning Studio (clássico)](/azure/machine-learning/studio-module-reference/a-z-module-list) no algoritmo Machine Learning Studio (clássico) e na ajuda do módulo.
