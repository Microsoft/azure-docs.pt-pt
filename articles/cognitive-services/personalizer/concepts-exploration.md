---
title: Exploração - Personalizer
titleSuffix: Azure Cognitive Services
description: Com a exploração, o Personalizer é capaz de continuar a apresentar bons resultados, mesmo à medida que o comportamento do utilizador muda. Escolher um cenário de exploração é uma decisão de negócio sobre a proporção de interações do utilizador para explorar, de forma a melhorar o modelo.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: 0b69c1fb070431ad61858322dce461f6496c35d7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "73490816"
---
# <a name="exploration-and-exploitation"></a>Exploração e exploração

Com a exploração, o Personalizer é capaz de continuar a apresentar bons resultados, mesmo à medida que o comportamento do utilizador muda.

Quando o Personalizer recebe uma chamada de Rank, devolve um RewardActionID que:
* Usa a exploração para corresponder ao comportamento mais provável do utilizador com base no atual modelo de aprendizagem automática.
* Usa a exploração, que não corresponde à ação que tem a maior probabilidade no ranking.

Personalizer usa atualmente um algoritmo chamado *epsilon ganancioso* para explorar. 

## <a name="choosing-an-exploration-setting"></a>Escolher um cenário de exploração

Configura a percentagem de tráfego a utilizar para exploração na página de **Configuração** do portal Azure para Personalizar. Esta definição determina a percentagem de chamadas rank que realizam a exploração. 

O personalizer determina se deve explorar ou explorar com esta probabilidade em cada chamada de classificação. Isto é diferente do comportamento em algumas estruturas A/B que bloqueiam um tratamento em iDs específicos do utilizador.

## <a name="best-practices-for-choosing-an-exploration-setting"></a>Boas práticas para escolher um cenário de exploração

Escolher um cenário de exploração é uma decisão de negócio sobre a proporção de interações do utilizador para explorar, de forma a melhorar o modelo. 

Um cenário de zero irá anular muitos dos benefícios do Personalizer. Com esta definição, o Personalizer não utiliza interações do utilizador para descobrir melhores interações do utilizador. Isto leva à estagnação do modelo, à deriva e, em última análise, a um desempenho mais baixo.

Uma definição demasiado alta irá anular os benefícios de aprender com o comportamento do utilizador. Defini-lo a 100% implica uma aleatoriedade constante, e qualquer comportamento aprendido dos utilizadores não influenciaria o resultado.

É importante não alterar o comportamento da aplicação com base no facto de ver se o Personalizer está a explorar ou a explorar. Isto levaria a preconceitos de aprendizagem que, em última análise, diminuiriam o desempenho potencial.

## <a name="next-steps"></a>Passos seguintes

[Aprendizagem por reforço](concepts-reinforcement-learning.md) 