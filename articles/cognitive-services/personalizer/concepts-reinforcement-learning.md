---
title: Reforço Learning – personalizador
titleSuffix: Azure Cognitive Services
description: O personalizador usa informações sobre ações e o contexto atual para fazer sugestões de classificação melhores. As informações sobre essas ações e o contexto são atributos ou propriedades que são chamados de recursos.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 36071cdee25cfa99fc54b0e5c0c0aa822cb5fe2f
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68662840"
---
# <a name="what-is-reinforcement-learning"></a>O que é o reforço Learning?

O aprendizado de reforço é uma abordagem para aprendizado de máquina que aprende comportamentos ao obter comentários de seu uso.
 
O reforço Learning funciona por:

* Fornecendo uma oportunidade ou um grau de liberdade para aplicar um comportamento, como tomar decisões ou escolhas.
* Fornecer informações contextuais sobre o ambiente e as opções.
* Fornecer comentários sobre o quão bem o comportamento atinge uma determinada meta.

Embora existam muitos subtipos e estilos de aprendizado de reforço, é assim que o conceito funciona no Personalizador:

* Seu aplicativo fornece a oportunidade de mostrar uma parte do conteúdo de uma lista de alternativas.
* Seu aplicativo fornece informações sobre cada alternativa e o contexto do usuário.
* Seu aplicativo computa uma _Pontuação de recompensa_.

Ao contrário de algumas abordagens para o aprendizado de reforço, o personalizador não requer uma simulação para trabalhar no. Seus algoritmos de aprendizado são projetados para reagir a um mundo externo (versus controlá-lo) e aprender de cada ponto de dados com uma compreensão de que se trata de uma oportunidade única que custa tempo e dinheiro para criar, e que haja um diferente de zero (perda de possível recompensa) se o desempenho inferior ocorre.

## <a name="what-type-of-reinforcement-learning-algorithms-does-personalizer-use"></a>Que tipo de algoritmos de aprendizado do reforço o personalizador usa?

A versão atual do personalizador usa **Bandits contextual**, uma abordagem para o aprendizado de reforço que é enquadrada para tomar decisões ou escolhas entre ações discretas, em um determinado contexto.

A _memória de decisão_, o modelo que foi treinado para capturar a melhor decisão possível, devido a um contexto, usa um conjunto de modelos lineares. Eles mostraram os resultados de negócios repetidamente e são uma abordagem comprovada, parcialmente porque eles podem aprender com o mundo real muito rapidamente sem precisar de treinamento de várias passagens, e parcialmente porque eles podem complementar modelos de aprendizado supervisionado e uma neural profunda modelos de rede.

A alocação de tráfego de explorar/explorar é feita aleatoriamente após o percentual definido para exploração, e o algoritmo padrão para exploração é de Épsilon-ávido.

### <a name="history-of-contextual-bandits"></a>Histórico de Bandits contextuais

João Langford Cunhau o nome Bandits (Langford e Zhang [2007]) para descrever um subconjunto indesejado de reforço Learning e trabalhou em uma meia dúzia de documentos, melhorando nossa compreensão de como aprender neste paradigma:

* Beygelzimer et al. [2011]
* Dudík et al. [2011a,b]
* Agarwal et al. [2014, 2012]
* Beygelzimer e Langford [2009]
* Li et al. [2010]

João também recebeu vários tutoriais anteriormente sobre tópicos como previsão conjunta (ICML 2015), teoria de Bandit contextual (NIPS 2013), aprendizado ativo (ICML 2009) e limites de complexidade de exemplo (ICML 2003)

## <a name="what-machine-learning-frameworks-does-personalizer-use"></a>Quais estruturas do Machine Learning o personalizador usa?

Atualmente, o personalizador usa [Vowpal Wabbit](https://github.com/VowpalWabbit/vowpal_wabbit/wiki) como base para o aprendizado de máquina. Essa estrutura permite a taxa de transferência máxima e a menor latência ao fazer a personalização classificar e treinar o modelo com todos os eventos.

## <a name="references"></a>Referências

* [Tomando decisões contextuais com débito técnico baixo](https://arxiv.org/abs/1606.03966)
* [Uma abordagem de reduções para classificação justa](https://arxiv.org/abs/1803.02453)
* [Bandits contextual eficientes em mundos não-estacionários](https://arxiv.org/abs/1708.01799)
* [Previsão de perda residual: Reforço: aprendendo sem comentários incrementais](https://openreview.net/pdf?id=HJNMYceCW)
* [Instruções de mapeamento e observações visuais para ações com o reforço Learning](https://arxiv.org/abs/1704.08795)
* [Aprendendo a Pesquisar melhor do que seu professor](https://arxiv.org/abs/1502.02206)

## <a name="next-steps"></a>Passos Seguintes

[Avaliação offline](concepts-offline-evaluation.md) 