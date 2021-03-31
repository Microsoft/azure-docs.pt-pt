---
title: Aprendizagem de Reforço - Personalizante
titleSuffix: Azure Cognitive Services
description: O personalizador utiliza informação sobre ações e contexto atual para fazer melhores sugestões de classificação. A informação sobre estas ações e contexto são atributos ou propriedades que são referidas como funcionalidades.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/07/2019
ms.openlocfilehash: 8b97221de4921e06ddfab610618f37683b990181
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "87132743"
---
# <a name="what-is-reinforcement-learning"></a>O que é Aprendizagem de Reforço?

O Reforço Learning é uma abordagem à aprendizagem automática que aprende comportamentos obtendo feedback da sua utilização.
 
O Reforço aprendizagem funciona por:

* Proporcionar uma oportunidade ou grau de liberdade para decretar um comportamento - como tomar decisões ou escolhas.
* Fornecendo informação contextual sobre o ambiente e escolhas.
* Fornecendo feedback sobre como o comportamento alcança um determinado objetivo.

Embora existam muitos subtipos e estilos de aprendizagem de reforço, é assim que o conceito funciona no Personalizer:

* A sua aplicação oferece a oportunidade de mostrar um conteúdo de uma lista de alternativas.
* A sua aplicação fornece informações sobre cada alternativa e o contexto do utilizador.
* A sua aplicação calcula uma _pontuação de recompensa._

Ao contrário de algumas abordagens para a aprendizagem de reforço, personalizer não requer uma simulação para trabalhar. Os seus algoritmos de aprendizagem são projetados para reagir a um mundo exterior (versus controlá-lo) e aprender com cada ponto de dados com a compreensão de que é uma oportunidade única que custa tempo e dinheiro para criar, e que há um arrependimento não-zero (perda de possível recompensa) se o desempenho subóptocal acontecer.

## <a name="what-type-of-reinforcement-learning-algorithms-does-personalizer-use"></a>Que tipo de algoritmos de aprendizagem de reforço usam o Personalizer?

A versão atual do Personalizer usa **bandidos contextuais,** uma abordagem à aprendizagem de reforço que se enquadra em tomar decisões ou escolhas entre ações discretas, num determinado contexto.

A _memória de decisão_, o modelo que foi treinado para capturar a melhor decisão possível, dado um contexto, utiliza um conjunto de modelos lineares. Estes têm mostrado repetidamente resultados de negócio e são uma abordagem comprovada, em parte porque podem aprender com o mundo real muito rapidamente sem precisar de formação multi-pass, e em parte porque podem complementar modelos de aprendizagem supervisionados e modelos de rede neural profunda.

A alocação de tráfego de exploração/exploração é feita aleatoriamente seguindo a percentagem definida para exploração, e o algoritmo padrão para exploração é ganancioso epsilon..

### <a name="history-of-contextual-bandits"></a>História dos Bandidos Contextuais

John Langford cunhou o nome De Bandidos Contextuais (Langford e Zhang [2007]) para descrever um subconjunto tratável de aprendizagem de reforço e trabalhou em meia dúzia de trabalhos melhorando a nossa compreensão de como aprender neste paradigma:

* Beygelzimer et al. [2011]
* Dudík et al. [2011a,b]
* Agarwal et al. [2014, 2012]
* Beygelzimer e Langford [2009]
* Li et al. [2010]

João também já deu vários tutoriais anteriormente sobre temas como A Previsão Conjunta (ICML 2015), Teoria do Bandido Contextual (NIPS 2013), Aprendizagem Ativa (ICML 2009) e Limites de Complexidade da Amostra (ICML 2003)

## <a name="what-machine-learning-frameworks-does-personalizer-use"></a>Que quadros de aprendizagem automática utiliza o Personalizer?

Atualmente, o personalização usa [o Vowpal Wabbit](https://github.com/VowpalWabbit/vowpal_wabbit/wiki) como base para a aprendizagem automática. Este quadro permite a máxima produção e menor latência ao fazer as fileiras de personalização e treinar o modelo com todos os eventos.

## <a name="references"></a>Referências

* [Tomar decisões contextuais com baixo endividamento técnico](https://arxiv.org/abs/1606.03966)
* [Uma abordagem de redução da classificação justa](https://arxiv.org/abs/1803.02453)
* [Bandidos Contextuais Eficientes em Mundos Não Estacionários](https://arxiv.org/abs/1708.01799)
* [Previsão de Perda Residual: Reforço: aprendizagem sem feedback incremental](https://openreview.net/pdf?id=HJNMYceCW)
* [Instruções de mapeamento e observações visuais para ações com aprendizagem de reforço](https://arxiv.org/abs/1704.08795)
* [Aprender a procurar melhor do que o seu professor](https://arxiv.org/abs/1502.02206)

## <a name="next-steps"></a>Passos seguintes

[Avaliação offline](concepts-offline-evaluation.md) 