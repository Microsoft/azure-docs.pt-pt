---
title: Como escolher algoritmos
titleSuffix: Azure Machine Learning Studio
description: Como escolher algoritmos do Azure Machine Learning Studio para o learning supervisionado e nas experimentações de clustering, classificação ou regressão.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: previous-ms.author=pakalra, previous-author=pakalra
ms.date: 12/18/2017
ms.openlocfilehash: 8ae8ecab5363c9eff7fcadd65c76dd31ca6818e1
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56877933"
---
# <a name="how-to-choose-algorithms-for-azure-machine-learning-studio"></a>Como escolher algoritmos para o Azure Machine Learning Studio

A resposta à pergunta "O algoritmo de machine learning devo utilizar?" é sempre "Depende". Depende do tamanho, a qualidade e a natureza dos dados. Depende do que deseja fazer com a resposta. Depende de como a matemática do algoritmo foi traduzida nas instruções para o computador que estiver a utilizar. Além de depender da sobre quanto tempo tem. Até mesmo os mais experientes cientistas de dados não podem informar quais algoritmos serão o desempenho antes de tentá-los.

O Machine Learning Studio dispõe de algoritmos avançados, como Árvores de Decisão Escaláveis, Sistemas de Recomendação Bayesianos, Redes Neurais Profundas e Selvas de Decisão desenvolvidas junto da Microsoft Research. Estão também incluídos pacotes de código aberto e dimensionáveis de machine learning, como o Vowpal Wabbit. O Machine Learning Studio suporta algoritmos de machine learning para classificação de várias classes e binária, regressão e clustering. Ver a lista completa dos [módulos do Machine Learning](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/index).
A documentação fornece algumas informações sobre cada algoritmo e de como otimizar os parâmetros para otimizar o algoritmo para a sua utilização.  


## <a name="the-machine-learning-algorithm-cheat-sheet"></a>Roteiro para o algoritmo de Machine Learning

O **Microsoft Azure Machine Learning Studio Trapacear algoritmos** algoritmo de aprendizagem para as suas soluções de Análise Preditiva da biblioteca do Azure Machine Learning Studio dos algoritmos de machine ajuda a escolher as corretas.
Este artigo explica como usá-lo.

> [!NOTE]
> Para transferir a folha de referência rápida e seguir este artigo, aceda a [Machine cábula do algoritmo de aprendizagem do Microsoft Azure Machine Learning Studio](algorithm-cheat-sheet.md).
> 
> 

Esta referência rápida tem um público muito específico em mente: início cientista de dados com nível de licenciatura machine learning, tentando escolher um algoritmo para começar no Azure Machine Learning Studio. Isso significa que o faz algumas generalizations e oversimplifications, mas aponta em direção segura. Isso também significa que existem muitos algoritmos não listados aqui. À medida que cresce Azure Machine Learning Studio para abranger um conjunto mais completo de métodos disponíveis, vamos adicioná-los.

Estas recomendações são compilados comentários e sugestões de muitos cientistas de dados e especialistas do machine learning. Não concordar sobre tudo, mas eu tentou harmonize nossas opiniões num consenso aproximado. A maioria das declarações de desentendimentos começam com "Depende..."

### <a name="how-to-use-the-cheat-sheet"></a>Como utilizar a folha de referência rápida

Leia as etiquetas de caminho e o algoritmo no gráfico, como "para  *&lt;etiqueta do caminho&gt;*, utilize  *&lt;algoritmo&gt;*." Por exemplo, "para *velocidade*, utilize *dois classe regressão logística*." Por vezes, mais de um ramo aplica-se.
Por vezes, nenhuma delas é um ajuste perfeito. Se se destinam a ser recomendações de regra de geral, para que não se preocupe a ser exata.
Cientistas de dados de várias que falei com disse que a forma-se de que apenas para encontrar o melhor algoritmo é tentar todas elas.

Eis um exemplo do [Galeria de IA do Azure](http://gallery.azure.ai/) de uma experimentação que tenta vários algoritmos contra os mesmos dados e compara os resultados: [Compare classificadores de Roc: Reconhecimento de letra](http://gallery.azure.ai/Details/a635502fc98b402a890efe21cec65b92).

> [!TIP]
> Para transferir uma visão geral de fácil de compreender o infográfico de noções básicas do machine learning para saber mais sobre os algoritmos populares utilizados para responder a perguntas comuns de machine learning, consulte [de Machine learning Noções básicas com exemplos de algoritmos](basics-infographic-with-algorithm-examples.md).

## <a name="flavors-of-machine-learning"></a>Tipos do machine learning

### <a name="supervised"></a>Supervisionado

Algoritmos de aprendizagem supervisionado fazer predições com base num conjunto de exemplos. Por exemplo, os preços históricos de ações podem ser utilizado para palpites de perigo aos preços de futuras. Cada exemplo usado para treinamento é o nome com o valor de interesse — neste caso, o preço das ações. Um algoritmo de aprendizagem supervisionada procura de padrões nessas etiquetas de valor. Ele pode usar qualquer informação que pode ser relevante — o dia da semana, a temporada, os dados da empresa financeira, o tipo da indústria, a presença de eventos problemáticos de geopolíticas — e a cada algoritmo para diferentes tipos de padrões. Depois do algoritmo encontrou o padrão melhor possível, ele usa esse padrão para fazer predições para dados de teste sem etiqueta, os preços de amanhã.

Aprendizagem supervisionada é um tipo de úteis e popular do machine learning. Com uma exceção, todos os módulos no Azure Machine Learning Studio são supervisionados Algoritmos de aprendizagem. Existem vários tipos específicos de aprendizagem supervisionada que são representados no Azure Machine Learning Studio: classificação, regressão e deteção de anomalias.

* **Classificação**. Quando os dados estão a ser utilizados para prever uma categoria, aprendizagem supervisionada também é denominada classificação. Esse é o caso quando atribui uma imagem como uma imagem de um "gato" ou "cachorro". Quando há apenas duas opções, ela é chamada **duas classes** ou **classificação binomial**. Quando existem mais categorias, como ao prever o vencedor de torneio NCAA March Madness, esse problema é conhecido como **Roc classificação**.
* **Regressão**. Quando está a ser previsto um valor, tal como acontece com os preços de ações, aprendizagem supervisionado é chamada regressão.
* **Deteção de anomalias**. Às vezes, o objetivo é identificar os pontos de dados que são simplesmente invulgares. Na deteção de fraudes, por exemplo, qualquer padrões de gastos de cartão de crédito bastante invulgar são suspeito. As variações possíveis são então inúmeras e os exemplos de treinamento por isso, poucos ou que não é viável para saber que fraudulentas atividade é semelhante. A abordagem que usa a deteção de anomalias é simplesmente saiba que atividade normal é semelhante (usando um transações não fraudulentas de histórico) e identificar tudo o que é significativamente diferente.

### <a name="unsupervised"></a>Não supervisionado

No learning não supervisionado, pontos de dados têm sem etiquetas associadas a eles. Em vez disso, o objetivo de um algoritmo de aprendizado não supervisionado é para organizar os dados de alguma forma ou para descrever a sua estrutura. Isso pode significar agrupando-lo em clusters ou ao localizar as diferentes formas de ver dados complexos para que seja apresentado mais simples ou mais organizada.

### <a name="reinforcement-learning"></a>Aprendizagem por reforço

Por reforço de aprendizado, o algoritmo obtém para escolher uma ação em resposta a cada ponto de dados. O algoritmo de aprendizagem também recebe um sinal de recompensa um curto período de tempo mais tarde, que indica o quão bom a decisão era.
Com base nisso, o algoritmo modifica sua estratégia de forma a alcançar a recompensa mais alta. Atualmente, não há nenhum por reforço módulos de algoritmo no Azure Machine Learning Studio de aprendizagem. Aprendizagem por reforço é comum a robótica, onde o conjunto das leituras dos sensores num ponto no tempo é um ponto de dados e o algoritmo tem de escolher a ação de seguinte o robô. Também é que um natural se encaixam para Internet das coisas aplicativos.

## <a name="considerations-when-choosing-an-algorithm"></a>Considerações ao escolher um algoritmo

### <a name="accuracy"></a>Precisão

Obter a resposta mais exata possível nem sempre é necessário.
Às vezes, uma aproximação é adequada, consoante o que pretende utilizá-lo. Se for esse o caso, poderá reduzir drasticamente o seu tempo de processamento aproximando-se com o mais aproximados métodos. Outra vantagem de mais aproximados métodos é que eles naturalmente tendem a evitar [overfitting](https://youtu.be/DQWI1kvmwRg).

### <a name="training-time"></a>Tempo de treinamento

O número de minutos ou horas necessárias para preparar um modelo varia muito entre algoritmos. O tempo de treinamento é geralmente intimamente ligado a precisão — normalmente um acompanha o outro. Além disso, alguns algoritmos são mais sensíveis ao número de pontos de dados do que outras.
Quando o tempo seja limitado pode promover a escolha do algoritmo, especialmente quando o conjunto de dados é grande.

### <a name="linearity"></a>Linearity

Muitos algoritmos de machine learning fazer uso de linearity. Algoritmos de classificação linear partem do princípio de que as classes podem ser separadas por uma linha reta (ou seu analógico superior dimensional). Estes incluem a regressão logística e suportam máquinas de vetor (conforme implementado no Azure Machine Learning Studio).
Regressão linear algoritmos partem do princípio de que as tendências de dados, siga uma linha reta. Essas suposições não são ruins para alguns problemas, mas em outros utilizadores que utilizam a precisão para baixo.

![Limite de classe não-linear](./media/algorithm-choice/image1.png)

***Limite de classe não-linear*** *-depender de um algoritmo de classificação linear iria resultar na precisão baixa*

![Dados com uma tendência de não-linear](./media/algorithm-choice/image2.png)

***Dados com uma tendência de não-linear*** *-usando um método de regressão linear geraria muito maiores erros que o necessário*

Apesar dos perigos lineares algoritmos são muito populares como uma primeira linha do ataque. Eles tendem a ser a forma algorítmica simples e rápida para preparar.

### <a name="number-of-parameters"></a>Número de parâmetros

Os parâmetros são os recursos que um cientista de dados obtém para ativar na configuração de um algoritmo. Eles são números que afetam o comportamento do algoritmo, como tolerância de erro ou o número de iterações, ou opções entre variantes de como o algoritmo se comporta. O tempo de treinamento e a precisão do algoritmo podem ser muito sensíveis para obter apenas as configurações corretas. Normalmente, algoritmos com parâmetros de números grandes exigem que a maioria dos tentativa e erro para encontrar uma boa combinação.

Em alternativa, há uma [varrimento de parâmetro](algorithm-parameters-optimize.md) bloco de módulo no Azure Machine Learning Studio, que tenta automaticamente todas as combinações de parâmetros em qualquer granularidade que escolher. Embora essa seja uma excelente forma de certificar-se de que já abrangido o espaço de parâmetro, o tempo necessário para preparar um modelo aumenta exponencialmente com o número de parâmetros.

A vantagem é que ter muitos parâmetros normalmente indica que um algoritmo tem maior flexibilidade. Muitas vezes, ele pode obter precisão muito bom. Fornecido que pode encontrar a combinação certa de definições de parâmetro.

### <a name="number-of-features"></a>Número de recursos

Para determinados tipos de dados, o número de recursos pode ser muito grande em comparação com o número de pontos de dados. Isto é, muitas vezes, o caso com genética ou dados textuais. O grande número de recursos pode sobrecarrega alguns algoritmos de aprendizagem, treinamento unfeasibly longo do tempo de fazer. As máquinas de Vetores de suporte são particularmente adequadas para o caso (ver abaixo).

### <a name="special-cases"></a>Casos especiais

Alguns algoritmos de aprendizagem assumem pressupostos específicos sobre a estrutura dos dados ou os resultados desejados. Se pode encontrar um que atende às suas necessidades, podemos dar resultados mais úteis, previsões mais precisas ou tempos mais rápidos de treinamento.

| **Algoritmo** | **precisão** | **Tempo de treinamento** | **Linearity** | **Parâmetros** | **Notas** |
| --- |:---:|:---:|:---:|:---:| --- |
| **Classificação de duas classes** | | | | | |
| [Regressão logística](https://msdn.microsoft.com/library/azure/dn905994.aspx) | |● |● |5 | |
| [floresta de decisão](https://msdn.microsoft.com/library/azure/dn906008.aspx) |● |○ | |6 | |
| [Uganda de decisão](https://msdn.microsoft.com/library/azure/dn905976.aspx) |● |○ | |6 |Requisitos de espaço de memória baixa |
| [árvore de decisões elevada](https://msdn.microsoft.com/library/azure/dn906025.aspx) |● |○ | |6 |Requisitos de espaço de memória de grandes dimensões |
| [rede neural](https://msdn.microsoft.com/library/azure/dn905947.aspx) |● | | |9 |[Personalização adicional é possível](https://go.microsoft.com/fwlink/?LinkId=402867) |
| [perceptron média](https://msdn.microsoft.com/library/azure/dn906036.aspx) |○ |○ |● |4 | |
| [máquina de vetor com suporte](https://msdn.microsoft.com/library/azure/dn905835.aspx) | |○ |● |5 |Vale a conjuntos de recursos grandes |
| [máquina de vetor com suporte aprofundado localmente](https://msdn.microsoft.com/library/azure/dn913070.aspx) |○ | | |8 |Vale a conjuntos de recursos grandes |
| [Máquina do ponto dos bayesiana Ingênua](https://msdn.microsoft.com/library/azure/dn905930.aspx) | |○ |● |3 | |
| **Classificação de Roc** | | | | | |
| [Regressão logística](https://msdn.microsoft.com/library/azure/dn905853.aspx) | |● |● |5 | |
| [floresta de decisão](https://msdn.microsoft.com/library/azure/dn906015.aspx) |● |○ | |6 | |
| [Uganda de decisão](https://msdn.microsoft.com/library/azure/dn905963.aspx) |● |○ | |6 |Requisitos de espaço de memória baixa |
| [rede neural](https://msdn.microsoft.com/library/azure/dn906030.aspx) |● | | |9 |[Personalização adicional é possível](https://go.microsoft.com/fwlink/?LinkId=402867) |
| [one-v-all](https://msdn.microsoft.com/library/azure/dn905887.aspx) |- |- |- |- |Ver as propriedades do método de classe dois selecionado |
| **Regressão** | | | | | |
| [linear](https://msdn.microsoft.com/library/azure/dn905978.aspx) | |● |● |4 | |
| [Bayesianos linear](https://msdn.microsoft.com/library/azure/dn906022.aspx) | |○ |● |2 | |
| [floresta de decisão](https://msdn.microsoft.com/library/azure/dn905862.aspx) |● |○ | |6 | |
| [árvore de decisões elevada](https://msdn.microsoft.com/library/azure/dn905801.aspx) |● |○ | |5 |Requisitos de espaço de memória de grandes dimensões |
| [floresta rápida quantile](https://msdn.microsoft.com/library/azure/dn913093.aspx) |● |○ | |9 |Distribuições em vez de ponto de previsões |
| [rede neural](https://msdn.microsoft.com/library/azure/dn905924.aspx) |● | | |9 |[Personalização adicional é possível](https://go.microsoft.com/fwlink/?LinkId=402867) |
| [Poisson](https://msdn.microsoft.com/library/azure/dn905988.aspx) | | |● |5 |Tecnicamente registos linear. Para prever as contagens |
| [ordinal](https://msdn.microsoft.com/library/azure/dn906029.aspx) | | | |0 |Para prever a ordem de classificação |
| **Deteção de anomalias** | | | | | |
| [máquina de vetor com suporte](https://msdn.microsoft.com/library/azure/dn913103.aspx) |○ |○ | |2 |Especialmente bons para conjuntos de recursos grandes |
| [Deteção de anomalias baseado em PCA](https://msdn.microsoft.com/library/azure/dn913102.aspx) | |○ |● |3 | |
| [K-means](https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/) | |○ |● |4 |Um algoritmo de clustering |

**Propriedades de algoritmo:**

**●** -mostra a precisão excelente, horas de treinamento rápido e a utilização de linearity

**○** -mostra a precisão bom e as horas de treinamento moderado

## <a name="algorithm-notes"></a>Notas de algoritmo

### <a name="linear-regression"></a>Regressão linear

Como mencionado anteriormente, [regressão linear](https://msdn.microsoft.com/library/azure/dn905978.aspx) se encaixa uma linha (ou plano ou hyperplane) para o conjunto de dados. É um trabalho pesado, simples e rápido, mas pode ser muito simplista para alguns problemas.
Verifique aqui para um [tutorial de regressão linear](linear-regression-in-azure.md).

![Dados com uma tendência linear](./media/algorithm-choice/image3.png)

***Dados com uma tendência linear***

### <a name="logistic-regression"></a>Regressão logística

Embora ele erroneamente inclui "regressão" no nome, regressão logística é, na verdade, uma ferramenta poderosa para [duas classes](https://msdn.microsoft.com/library/azure/dn905994.aspx) e [várias classes](https://msdn.microsoft.com/library/azure/dn905853.aspx) classificação. É rápido e simples. O fato de que ele usa de um "-moldada curva em vez de uma linha reta faz com que seja uma opção natural para dividir os dados em grupos. Limites de classe linear dá de regressão logística, por isso, quando usá-lo, certifique-se de que uma aproximação linear é algo que puder conviver com.

![Regressão logística aos dados de duas classes com apenas um recurso](./media/algorithm-choice/image4.png)

***Um regressão logística aos dados de duas classes com apenas um recurso*** *-o limite de classe é o momento em que a curva de logística apenas mais próximo as duas classes*

### <a name="trees-forests-and-jungles"></a>Árvores e florestas e selvas

Florestas de decisão ([regressão](https://msdn.microsoft.com/library/azure/dn905862.aspx), [duas classes](https://msdn.microsoft.com/library/azure/dn906008.aspx), e [várias classes](https://msdn.microsoft.com/library/azure/dn906015.aspx)), selvas de decisão ([duas classes](https://msdn.microsoft.com/library/azure/dn905976.aspx) e [ várias classes](https://msdn.microsoft.com/library/azure/dn905963.aspx)) e aumentou a árvores de decisão ([regressão](https://msdn.microsoft.com/library/azure/dn905801.aspx) e [duas classes](https://msdn.microsoft.com/library/azure/dn906025.aspx)) são todas com base em árvores de decisões, um conceito de aprendizagem automática que se alicerça. Há muitas variantes de árvores de decisões, mas todos eles fazem a mesma coisa — subdividir o espaço de recursos em regiões com principalmente a mesma etiqueta. Estes podem ser regiões de categoria consistente ou de valor constante, dependendo se estiver fazendo classificação ou regressão.

![Árvore de decisão subdivide-um espaço de funcionalidade](./media/algorithm-choice/image5.png)

***Uma árvore de decisão subdivide-um espaço de recursos em regiões de valores de aproximadamente uniforme***

Uma vez que um espaço de recurso pode ser subdividido em arbitrariamente em pequenas regiões, é fácil imaginar dividindo-escalados suficiente para ter um ponto de dados por região. Este é um exemplo extremo de overfitting. Para evitar isso, um grande conjunto de árvores são construídos com especial matemático cuidado que as árvores não são correlacionadas. A média desta "floresta de decisão" é uma árvore que evita overfitting. Florestas de decisão podem utilizar muita memória. Grupo de decisões é uma variante que consome menos memória às custas de um pouco mais tempo de treinamento.

Árvores de decisões elevada evitar overfitting ao limitar o número de vezes que podem subdividir e como pontos de dados são permitidos em cada região. O algoritmo constrói uma seqüência de árvores, cada um dos quais aprende a compensar o erro à esquerda por uma árvore de antes. O resultado é um aprendiz muito preciso que tende a utilizar muita memória. Para a descrição do técnica completo, confira [documento original de Friedman](http://www-stat.stanford.edu/~jhf/ftp/trebst.pdf).

[Rápida de regressão de quantile floresta](https://msdn.microsoft.com/library/azure/dn913093.aspx) é uma variação de árvores de decisão para o caso especial em que deseja saber não apenas o valor típico (mediano) dos dados dentro de uma região, mas também sua distribuição sob a forma de quantiles.

### <a name="neural-networks-and-perceptrons"></a>As redes neurais e perceptrons

As redes neurais são cérebro inspirada que abordam os algoritmos de aprendizagem [várias classes](https://msdn.microsoft.com/library/azure/dn906030.aspx), [duas classes](https://msdn.microsoft.com/library/azure/dn905947.aspx), e [regressão](https://msdn.microsoft.com/library/azure/dn905924.aspx) problemas. Eles são fornecidos numa variedade infinita, mas as redes neurais no Azure Machine Learning Studio são todas de forma de gráficos acíclicos direcionados. Isso significa que os recursos de entrada são transmitidos para a frente (nunca para trás) numa seqüência de camadas antes de a ser transformado em saídas. Em cada camada, entradas são ponderadas em várias combinações, somadas e passadas para a próxima camada. Esta combinação de cálculos simples resulta na capacidade de saber tendências de limites e os dados de classe sofisticada, aparentemente por mágica. Redes em camadas muitos desse tipo efetuar a "deep learning" que alimenta o tech reporting tanto e de ficção.

Este desempenho elevado não é gratuitos, no entanto. As redes neurais podem demorar muito tempo para preparar, especialmente para grandes conjuntos de dados com muitos recursos. Também têm mais parâmetros do que a maioria dos algoritmos, o que significa que o varrimento de parâmetro expande o tempo de treinamento muito.
E para essas overachievers que pretendam [especificar sua própria estrutura de rede](https://go.microsoft.com/fwlink/?LinkId=402867), as possibilidades são inexhaustible.

![Limites aprenderam através de redes neurais](./media/algorithm-choice/image6.png)
***os limites aprendidos através de redes neurais podem ser complexa e irregular***

O [apresentou uma média de duas classes perceptron](https://msdn.microsoft.com/library/azure/dn906036.aspx) resposta de redes neurais para horas de treinamento está disparando. Ele usa uma estrutura de rede que fornece limites de classe linear. É quase primitivo pelos padrões de hoje, mas tem uma longa história de trabalhar de modo eficiente e é pequena o bastante para saber rapidamente.

### <a name="svms"></a>SVMs

Máquinas de vetores de suporte (SVMs) encontre o limite que separa as classes, como ao nível de uma margem possível. Quando as duas classes não podem ser claramente separadas, os algoritmos de encontrar o melhor limite podem. Como está escrito no Azure Machine Learning Studio, o [SVM de duas classes](https://msdn.microsoft.com/library/azure/dn905835.aspx) faz isso com apenas uma linha reta. (No SVM fala, ele usa um kernel linear.) Como faz essa aproximação linear, é capaz de executar rapidamente. Em que ele realmente se destaca é com dados com uso intenso de funcionalidade, como texto ou de genomas. Nestes casos SVMs são capazes de separar classes mais rapidamente e com menos overfitting que a maioria dos outros algoritmos, além de exigir um volume modesto de memória.

![Limite de classe de máquina de vetor de suporte](./media/algorithm-choice/image7.png)

***Um limite de classe de máquina de vetor de suporte típico maximiza a margem separar as duas classes***

Outro produto da Microsoft Research, a [SVM localmente profunda de duas classes](https://msdn.microsoft.com/library/azure/dn913070.aspx) é uma variant não lineares líderes de SVM que mantém a maior parte da eficiência velocidade e a memória da versão linear. É ideal para casos em que a abordagem linear não dá respostas suficientemente precisas. Os desenvolvedores o mantive rápida, dividindo o problema num monte de pequenos problemas SVM lineares. Leitura a [total Descrição](http://proceedings.mlr.press/v28/jose13.html) para obter os detalhes sobre como eles extraídos esse truque.

Utilizar uma extensão inteligente do SVMs não lineares, o [SVM de uma classe](https://msdn.microsoft.com/library/azure/dn913103.aspx) desenha um limite que descreve totalmente a todo o conjunto de dados. É útil para deteção de anomalias. Quaisquer novos pontos de dados que ficam muito fora desse limite são bastante invulgares seja digno de nota.

### <a name="bayesian-methods"></a>Métodos de Bayesianos

Métodos de Bayesianos possuem uma qualidade altamente desejável: eles evitar overfitting. Eles fazem isso fazendo algumas suposições com antecedência sobre a distribuição de probabilidade da resposta. Outro subproduto dessa abordagem é que eles têm muito poucos parâmetros. O Azure Machine Learning Studio tem ambos os algoritmos de Bayesianos para ambos os classificação ([máquina do ponto dos Bayes de duas classes](https://msdn.microsoft.com/library/azure/dn905930.aspx)) e regressão ([regressão linear de Bayesianos](https://msdn.microsoft.com/library/azure/dn906022.aspx)).
Tenha em atenção que estes partem do princípio de que os dados podem ser divididos ou ajustar com uma linha reta.

Numa nota histórica, máquinas de ponto dos Ingênua foram desenvolvidas junto da Microsoft Research. Eles têm algum trabalho teórico excepcionalmente belo por trás delas. Os estudantes interessados é direcionado para o [artigo original no JMLR](http://jmlr.org/papers/volume1/herbrich01a/herbrich01a.pdf) e uma [blog esclarecedores, de Chris Bishop](http://blogs.technet.com/b/machinelearning/archive/2014/10/30/embracing-uncertainty-probabilistic-inference.aspx).

### <a name="specialized-algorithms"></a>Algoritmos especializados
Se tiver um objetivo muito específico pode ser com sorte. Dentro da coleção do Azure Machine Learning Studio, há algoritmos especializam em:

- predição de classificar ([regressão ordinal](https://msdn.microsoft.com/library/azure/dn906029.aspx)),
- Contagem de previsão ([regressão de Poisson](https://msdn.microsoft.com/library/azure/dn905988.aspx)),
- Deteção de anomalias (um com base na [analysis principais componentes](https://msdn.microsoft.com/library/azure/dn913102.aspx) e outro com base nos [suportar máquinas de vetor](https://msdn.microsoft.com/library/azure/dn913103.aspx))
- clustering ([K-means](https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/))

![Deteção de anomalias baseado em PCA](./media/algorithm-choice/image8.png)

***Deteção de anomalias baseado em PCA*** *-a grande maioria dos dados recai sobre uma distribuição stereotypical; pontos tenha se desviado drasticamente partir dessa distribuição são suspeita*

![Conjunto de dados agrupadas usando K-means](./media/algorithm-choice/image9.png)

***Um conjunto de dados é agrupado em cinco clusters usando K-means***

Também há um ensemble [classificador de várias classes de one-v-all](https://msdn.microsoft.com/library/azure/dn905887.aspx), que divide o problema de classificação de classe de N em problemas de classificação de duas classes de n-1. A precisão, tempo de treinamento e propriedades de linearity são determinadas pelos classificadores de duas classes utilizados.

![Combinados para formar um classificador de três classes de classificadores de duas classes](./media/algorithm-choice/image10.png)

***Um par de classificadores de duas classes combinados para formar um classificador de classe de três***

O Azure Machine Learning Studio inclui também o acesso a uma estrutura de aprendizagem automática avançada sob o título da [Vowpal Wabbit](https://msdn.microsoft.com/library/azure/8383eb49-c0a3-45db-95c8-eb56a1fef5bf).
VW defies categorização aqui, uma vez que pode aprender a problemas de classificação e regressão e até mesmo aprender com dados parcialmente sem etiqueta. Pode configurar para utilizar qualquer um dos vários algoritmos, funções de perda e algoritmos de otimização de aprendizado. Foi projetado desde o backup para ser eficiente, parallel e extremamente rápida. Ele lida com conjuntos de recursos ridiculamente grande com pouco esforço aparente.
Iniciado e liderado pelo John Langford do Microsoft Research próprio, VW é uma fórmula uma entrada num campo de algoritmos de carro de estoque. Nem todo problema se encaixa VW, mas se a sua, ele talvez valha a pena a surgir a curva de aprendizado em sua interface. Também está disponível como [código-fonte aberto autónomo](https://github.com/JohnLangford/vowpal_wabbit) em várias linguagens.

## <a name="next-steps"></a>Próximos Passos

* Para transferir uma visão geral de fácil de compreender o infográfico de noções básicas do machine learning para saber mais sobre os algoritmos populares utilizados para responder a perguntas comuns de machine learning, consulte [de Machine learning Noções básicas com exemplos de algoritmos](basics-infographic-with-algorithm-examples.md).

* Para obter uma lista por categoria de todos os algoritmos de aprendizagem automática disponíveis no Machine Learning Studio, consulte [inicializar modelo](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/machine-learning-initialize-model) no algoritmo do Machine Learning Studio e, em seguida, ajuda do módulo.

* Para obter uma lista alfabética completa de algoritmos e módulos no Machine Learning Studio, consulte [lista A-Z dos módulos do Machine Learning Studio](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/a-z-module-list) no algoritmo de Machine Learning Studio e a ajuda do módulo.
