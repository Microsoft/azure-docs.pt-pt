---
title: Aprendizagem de Reforço - Personalizer
titleSuffix: Azure Cognitive Services
description: O Personalizer utiliza informação sobre ações e contexto atual para fazer melhores sugestões de classificação. As informações sobre estas ações e contexto são atributos ou propriedades que são referidas como funcionalidades.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 36071cdee25cfa99fc54b0e5c0c0aa822cb5fe2f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "68662840"
---
# <a name="what-is-reinforcement-learning"></a>O que é a Aprendizagem de Reforço?

O Reforço Learning é uma abordagem à aprendizagem automática que aprende comportamentos obtendo feedback da sua utilização.
 
A Aprendizagem de Reforço funciona:

* Proporcionando uma oportunidade ou grau de liberdade para decretar um comportamento - como tomar decisões ou escolhas.
* Fornecendo informações contextuais sobre o ambiente e as escolhas.
* Fornecendo feedback sobre como o comportamento alcança um determinado objetivo.

Embora existam muitos subtipos e estilos de aprendizagem de reforço, é assim que o conceito funciona no Personalizer:

* A sua aplicação oferece a oportunidade de mostrar um pedaço de conteúdo de uma lista de alternativas.
* A sua aplicação fornece informações sobre cada alternativa e o contexto do utilizador.
* A sua aplicação calcula uma _pontuação de recompensa._

Ao contrário de algumas abordagens para a aprendizagem de reforço, personalizer não requer uma simulação para trabalhar. Os seus algoritmos de aprendizagem são projetados para reagir a um mundo exterior (versus controlá-lo) e aprender a partir de cada ponto de dados com a compreensão de que é uma oportunidade única que custa tempo e dinheiro para criar, e que há um arrependimento não-zero (perda de possível recompensa) se o desempenho sub-ideal acontecer.

## <a name="what-type-of-reinforcement-learning-algorithms-does-personalizer-use"></a>Que tipo de algoritmos de aprendizagem de reforço utiliza o Personalizer?

A versão atual do Personalizer usa **bandidos contextuais,** uma abordagem à aprendizagem de reforço que é enquadrada em torno de tomar decisões ou escolhas entre ações discretas, num dado contexto.

A _memória de decisão_, o modelo que foi treinado para capturar a melhor decisão possível, dado um contexto, utiliza um conjunto de modelos lineares. Estes têm mostrado repetidamente resultados de negócio e são uma abordagem comprovada, em parte porque podem aprender com o mundo real muito rapidamente sem precisardede sondar multi-passes, e em parte porque podem complementar modelos de aprendizagem supervisionados e modelos de rede neural profunda.

A alocação de tráfego de exploração/exploração é feita aleatoriamente seguindo a percentagem definida para a exploração, e o algoritmo padrão para exploração é epsilon-ganancioso.

### <a name="history-of-contextual-bandits"></a>História dos Bandidos Contextuais

John Langford cunhou o nome Bandidos Contextuais (Langford e Zhang [2007]) para descrever um subconjunto tratável de aprendizagem de reforço e trabalhou em meia dúzia de artigos que melhoram a nossa compreensão de como aprender neste paradigma:

* Beygelzimer et al. [2011]
* Dudík et al. [2011a,b]
* Agarwal et al. [2014, 2012]
* Beygelzimer e Langford [2009]
* Li et al. [2010]

John também já deu vários tutoriais sobre temas como Previsão Conjunta (ICML 2015), Teoria do Bandido Contextual (NIPS 2013), Aprendizagem Ativa (ICML 2009) e Limites de Complexidade da Amostra (ICML 2003)

## <a name="what-machine-learning-frameworks-does-personalizer-use"></a>Que quadros de aprendizagem automática usa o Personalizer?

Personalizer usa atualmente [a Vowpal Wabbit](https://github.com/VowpalWabbit/vowpal_wabbit/wiki) como base para a aprendizagem automática. Este quadro permite a máxima produção e menor latência ao fazer rankings de personalização e treinar o modelo com todos os eventos.

## <a name="references"></a>Referências

* [Tomar decisões contextuais com baixa dívida técnica](https://arxiv.org/abs/1606.03966)
* [Uma abordagem de reduções à classificação justa](https://arxiv.org/abs/1803.02453)
* [Bandidos contextuais eficientes em mundos não estacionários](https://arxiv.org/abs/1708.01799)
* [Previsão de Perda Residual: Reforço: aprendizagem sem feedback incremental](https://openreview.net/pdf?id=HJNMYceCW)
* [Instruções de mapeamento e observações visuais a ações com aprendizagem de reforço](https://arxiv.org/abs/1704.08795)
* [Aprender a procurar melhor do que o seu professor](https://arxiv.org/abs/1502.02206)

## <a name="next-steps"></a>Passos seguintes

[Avaliação offline](concepts-offline-evaluation.md) 