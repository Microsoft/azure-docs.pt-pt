---
title: Aprendizagem por reforço - Personalizer
titleSuffix: Azure Cognitive Services
description: Personalizer utiliza informações sobre ações e contexto atual para fazer uma melhor classificação sugestões. As informações sobre estas ações e contexto são atributos ou propriedades que são chamadas de recursos.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: b65073c0646db0cd0c27a71005bb4f74b091ae09
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65506890"
---
# <a name="what-is-reinforcement-learning"></a>O que é a aprendizagem por reforço?

Aprendizagem por reforço é uma abordagem para machine learning que aprende a comportamentos obtendo comentários da sua utilização.
 
Aprendizagem por reforço funciona por:

* Fornecendo uma oportunidade ou o grau de liberdade adotar um comportamento - como a realização de decisões ou opções.
* Fornecer informações contextuais sobre o ambiente e opções.
* Fornecer comentários sobre a eficiência com que o comportamento atinge um determinado objetivo.

Embora haja muitos subtipos e estilos de por reforço de aprendizagem, isso é como o conceito funciona no Personalizer:

* Seu aplicativo fornece a oportunidade de mostrar uma parte do conteúdo de uma lista de alternativas.
* A aplicação fornece informações sobre cada alternativa e o contexto do utilizador.
* Seu aplicativo de computações uma _recompensar pontuação_.

Ao contrário de algumas abordagens para por reforço de aprendizagem, Personalizer não necessita de uma simulação para funcionar no. Os algoritmos de aprendizagem foram concebidos para reagir a um mundo exterior (versus controlá-lo) e aprender com cada ponto de dados com uma compreensão, que é uma oportunidade única que custam tempo e dinheiro para criar, e que existe um diferente de zero se arrependa (perda de recompensa possíveis) se acontece de desempenho inferior ao ideal.

## <a name="what-type-of-reinforcement-learning-algorithms-does-personalizer-use"></a>Que tipo de por reforço algoritmos de aprendizagem o Personalizer usa?

Utiliza a versão atual do Personalizer **bandits contextuais**, uma abordagem para por reforço aprender ou seja estruturado em torno de tomar decisões ou opções entre ações separadas, num determinado contexto.

O _memória de decisão_, o modelo que foi treinado para capturar a melhor decisão possíveis, dada um contexto, utiliza um conjunto de modelos lineares. Estas repetidamente demonstraram resultados de negócios e são uma abordagem testada, parcialmente porque eles podem aprender do mundo real muito rapidamente sem precisar de treinamento de pass multi e parcialmente, porque eles podem complementá neurais profundas e modelos de aprendizagem supervisionado modelos de rede.

A alocação de tráfego de explorar/exploração é feita aleatoriamente seguindo a percentagem definido para exploração e o algoritmo predefinido para uma exploração é épsilon greedy.

### <a name="history-of-contextual-bandits"></a>Histórico de Bandits contextuais

John Langford cunhou o Bandits de Contextual da nome (Langford e Zhang [2007]) de para descrever um subconjunto tractable de aprendizagem por reforço e já trabalhou num papers meia-dúzia de melhorar nossa compreensão de como pode saber nesse paradigma:

* Beygelzimer et al. [2011]
* Dudík et al. [2011a,b]
* Agarwal et al. [2014, 2012]
* Beygelzimer e Langford [2009]
* Li et al. [2010]

John também tenha dado vários tutoriais anteriormente sobre tópicos, como previsão conjunta (ICML 2015), contextuais Bandit teoria (NIPS 2013), aprendizagem ativa (ICML 2009) e limites de complexidade de exemplo (ICML 2003)

## <a name="what-machine-learning-frameworks-does-personalizer-use"></a>As arquiteturas de machine learning utilizar Personalizer?

Atualmente, utiliza personalizer [Vowpal Wabbit](https://github.com/VowpalWabbit/vowpal_wabbit/wiki) como a base para o machine learning. Essa estrutura permite débito máximo e mais baixa latência ao fazer a personalização classifica e o modelo com todos os eventos de formação.

## <a name="references"></a>Referências

* [Tomada de decisões contextuais com baixa dívida técnica](https://arxiv.org/abs/1606.03966)
* [Uma abordagem de reduções a classificação justa](https://arxiv.org/abs/1803.02453)
* [Eficientes Bandits contextuais em mundos não estacionários](https://arxiv.org/abs/1708.01799)
* [Predição de perda residuais: Por reforço: com não Incremental comentários de aprendizagem](https://openreview.net/pdf?id=HJNMYceCW)
* [Mapeamento de instruções e observações sobre o Visual para ações com aprendizagem por reforço](https://arxiv.org/abs/1704.08795)
* [Melhor do que sua professora de aprendizagem para pesquisa](https://arxiv.org/abs/1502.02206)

## <a name="next-steps"></a>Passos Seguintes

[Avaliação offline](concepts-offline-evaluation.md) 