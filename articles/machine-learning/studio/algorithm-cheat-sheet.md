---
title: Cábula do algoritmo de aprendizagem automática
titleSuffix: Azure Machine Learning Studio
description: Uma máquina imprimível cábula do algoritmo de aprendizagem ajuda-o a escolher o algoritmo certo para o modelo de previsão no Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: previous-ms.author=pakalra, previous-author=pakalra
ms.date: 12/18/2017
ms.openlocfilehash: 8d0b123045ace223dbea6c8fa98980091cfe73e1
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56819995"
---
# <a name="machine-learning-algorithm-cheat-sheet-for-azure-machine-learning-studio"></a>Machine learning cábula do algoritmo para o Azure Machine Learning Studio

O **do Azure Machine Learning Studio Trapacear algoritmos** ajuda a escolher o algoritmo certo para um modelo de Análise Preditiva.

[O Azure Machine Learning Studio](https://studio.azureml.net/) tem uma ampla biblioteca de algoritmos dos ***regressão***, ***classificação***, ***clustering***, e  ***Deteção de anomalias*** famílias. Cada uma foi concebido para resolver outro tipo de problema do machine learning.

## <a name="download-machine-learning-algorithm-cheat-sheet"></a>Transferir: Cábula do algoritmo de aprendizagem automática

**Baixe a folha de referência rápida aqui: [Machine Learning Algorithm Cheat Sheet (11 x 17 pol.)](https://download.microsoft.com/download/A/6/1/A613E11E-8F9C-424A-B99D-65344785C288/microsoft-machine-learning-algorithm-cheat-sheet-v7.pdf)**

![Máquina de referência rápida para algoritmos de aprendizagem: Saiba como escolher um algoritmo de Machine Learning.](./media/algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet-small_v_0_6-01.png)

Transferir e imprimir o Machine Learning Studio Trapacear algoritmos no tamanho de tabloide à mão e obtenha ajuda para escolher um algoritmo.

> [!NOTE]
> Para obter ajuda nesta referência rápida para escolher o algoritmo certo, além de uma discussão mais detalhada sobre os diferentes tipos de algoritmos de machine learning e como são utilizadas, consulte [como escolher algoritmos para o Microsoft Azure Machine Learning Studio](algorithm-choice.md).

## <a name="notes-and-terminology-definitions-for-the-machine-learning-studio-algorithm-cheat-sheet"></a>Notas e de definições terminológicas para o algoritmo de Machine Learning Studio referência rápida

* As sugestões disponibilizadas neste cábula do algoritmo são aproximadas regras de bolso. Alguns podem ser vinculada e algumas podem ser flagrantly violadas. Isso se destina a sugerir um ponto de partida. Não tenha medo de executar uma competição de cabeça a cabeça entre vários algoritmos nos seus dados. Há simplesmente nenhum substituto para entender os princípios de cada algoritmo e entender o sistema que gerou os seus dados.

* Cada algoritmo de machine learning tem seu próprio estilo ou *tendência inductive*. Para um problema específico, vários algoritmos podem ser adequados e um algoritmo pode ser uma opção melhor do que outras pessoas. Mas nem sempre é possível saber de antemão qual é a melhor opção. Em casos como esses, vários algoritmos estão listados em conjunto na folha de referência rápida. Uma estratégia apropriada seria tentar um algoritmo e se os resultados ainda não forem satisfatórios, experimente os outros. Eis um exemplo do [Galeria de IA do Azure](http://gallery.azure.ai/) de uma experimentação que tenta vários algoritmos contra os mesmos dados e compara os resultados: [Compare classificadores de Roc: Reconhecimento de letra](http://gallery.azure.ai/Details/a635502fc98b402a890efe21cec65b92).

* Existem três categorias principais do machine learning: **aprendizagem supervisionada**, **learning não supervisionado**, e **aprendizagem por reforço**.

  * Na **aprendizagem supervisionada**, cada ponto de dados é o nome ou associado a uma categoria ou o valor de interesse.  Um exemplo de uma etiqueta categórico é atribuir uma imagem como um "gato" ou "cachorro".  Um exemplo de uma etiqueta de valor é o preço de venda associado a um carros usados. O objetivo de aprendizagem supervisionado é estudar muitos exemplos etiquetados como essas e, em seguida, para poder fazer previsões sobre os pontos de dados futuros. Por exemplo, identificar novas fotos com o animal correto ou o atribuir os preços de venda precisos para outros carros usados. Este é um tipo de úteis e popular do machine learning. Todos os módulos no Azure Machine Learning Studio são supervisionados, exceto para os algoritmos de aprendizagem [Clustering de K-Means][k-means-clustering].

  * Na **learning não supervisionado**, pontos de dados têm sem etiquetas associadas a eles. Em vez disso, o objetivo de um algoritmo de aprendizado não supervisionado é para organizar os dados de alguma forma ou para descrever a sua estrutura. Isso pode significar agrupando-lo em clusters, como faz de K-means ou de diferentes formas de ver dados complexos para que seja apresentado mais simples de localizar.

  * Na **aprendizagem por reforço**, o algoritmo obtém para escolher uma ação em resposta a cada ponto de dados. É uma abordagem comum a robótica, onde o conjunto das leituras dos sensores num ponto no tempo é um ponto de dados e o algoritmo tem de escolher a ação de seguinte o robô. Também é um natural para aplicativos de Internet das coisas. O algoritmo de aprendizagem também recebe um sinal de recompensa um curto período de tempo mais tarde, que indica o quão bom a decisão era. Com base nisso, o algoritmo modifica sua estratégia de forma a alcançar a recompensa mais alta. Atualmente, não há nenhum por reforço módulos de algoritmo no Azure Machine Learning studio de aprendizagem.

* **Métodos de Bayesianos** partem do princípio de pontos de dados estatisticamente independentes. Isso significa que a variabilidade unmodeled num ponto de dados é uncorrelated com outras pessoas, ou seja, não é possível prever. Por exemplo, se os dados a ser registados o número de minutos até o próximo comboio de metrô chega, duas medidas tomadas por dia de diferença são estatisticamente independentes. No entanto, duas medidas tomadas um minuto diferença não são estatisticamente independentes – o valor de um é altamente preditivo do valor das outras.

* **Aumentou a regressão da árvore de decisão** tira partido da sobreposição de funcionalidades ou interação entre recursos. Isso significa que, em qualquer ponto de dados fornecido, o valor de um recurso é um pouco preditivo do valor de outro. Por exemplo, nos dados de temperatura alta/baixa diária, saber a temperatura baixa para o dia permite-lhe fazer uma estimativa razoável no alto. As informações contidas nos dois recursos são algo redundantes.

* Classificar dados em mais de duas categorias pode ser feito utilizando um classificador inerentemente multi classe ou ao combinar um conjunto de classificadores de duas classes num **ensemble**. Na abordagem ensemble, há um classificador de duas classes separado para cada classe – cada um deles separa os dados em duas categorias: "Esta classe" e "não esta classe." Em seguida, estes classificadores votar na atribuição correta do ponto de dados. Este é o princípio operacional por trás [várias classes de One-vs-All][one-vs-all-multiclass].

* Vários métodos, incluindo regressão logística e a máquina do ponto Bayes, partem do princípio **limites de classe linear**. Ou seja, partem do princípio de que as fronteiras entre classes são aproximadamente retas linhas (ou hyperplanes no caso mais geral). Muitas vezes, esta é uma característica dos dados que não sabe até depois de tentar para separá-lo, mas é algo que normalmente podem ser adquiridos por visualização de antemão. Se os limites de classe olhar bastante irregulares, permaneça com as árvores de decisões, selvas de decisão, suportar máquinas de vetor ou redes neurais.

* As redes neurais podem ser usadas com variáveis categóricas através da criação de um **variável fictício** para cada categoria, defini-la como 1 em casos em que se aplica a categoria, 0, onde não.

## <a name="next-steps"></a>Passos Seguintes

* Para um infográfico transferível que descreve os algoritmos e fornece exemplos, consulte [Infográfico transferível: Noções básicas com exemplos de algoritmos de aprendizagem máquina](basics-infographic-with-algorithm-examples.md).

* Para obter uma lista por categoria de todos os algoritmos de aprendizagem automática disponíveis no Machine Learning Studio, consulte [inicializar modelo] [ initialize-model] no algoritmo do Machine Learning Studio e, em seguida, ajuda do módulo.

* Para obter uma lista alfabética completa de algoritmos e módulos no Machine Learning Studio, consulte [lista A-Z dos módulos do Machine Learning Studio] [ a-z-list] no algoritmo de Machine Learning Studio e a ajuda do módulo.



<!-- Module References -->
[a-z-list]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/a-z-module-list
[initialize-model]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/machine-learning-initialize-model
[k-means-clustering]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/k-means-clustering
[one-vs-all-multiclass]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/one-vs-all-multiclass
