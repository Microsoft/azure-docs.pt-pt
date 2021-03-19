---
title: Exploração - Personalização
titleSuffix: Azure Cognitive Services
description: Com a exploração, o Personalizer é capaz de continuar a apresentar bons resultados, mesmo à medida que o comportamento do utilizador muda. Escolher uma definição de exploração é uma decisão de negócio sobre a proporção de interações de utilizador para explorar, de forma a melhorar o modelo.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: 00b9cc881d9414e957d7503bb6156095bb821c06
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87128153"
---
# <a name="exploration-and-exploitation"></a>Exploração e exploração

Com a exploração, o Personalizer é capaz de continuar a apresentar bons resultados, mesmo à medida que o comportamento do utilizador muda.

Quando o Personaler recebe uma chamada de Rank, devolve um RewardActionID que:
* Utiliza a exploração para corresponder ao comportamento mais provável do utilizador com base no modelo atual de aprendizagem automática.
* Usa a exploração, que não corresponde à ação que tem a maior probabilidade no ranking.

O personalizar usa atualmente um algoritmo chamado *epsilon ganancioso* para explorar. 

## <a name="choosing-an-exploration-setting"></a>Escolher um cenário de exploração

Configura a percentagem de tráfego a utilizar para exploração na página de **Configuração** do portal Azure para Personalizar. Esta definição determina a percentagem de chamadas rank que realizam a exploração. 

O personalização determina se deve explorar ou explorar com esta probabilidade em cada chamada de classificação. Isto é diferente do comportamento em algumas estruturas A/B que bloqueiam um tratamento em IDs específicos do utilizador.

## <a name="best-practices-for-choosing-an-exploration-setting"></a>Melhores práticas para escolher um cenário de exploração

Escolher uma definição de exploração é uma decisão de negócio sobre a proporção de interações de utilizador para explorar, de forma a melhorar o modelo. 

Um ajuste de zero irá anular muitos dos benefícios do Personalizer. Com esta definição, o Personalizer não utiliza interações do utilizador para descobrir melhores interações do utilizador. Isto leva à estagnação do modelo, à deriva e, em última análise, a um desempenho mais baixo.

Uma definição demasiado alta irá anular os benefícios de aprender com o comportamento do utilizador. Defini-lo a 100% implica uma aleatoriedade constante, e qualquer comportamento aprendido dos utilizadores não influenciaria o resultado.

É importante não alterar o comportamento da aplicação com base no facto de ver se o Personalizer está a explorar ou a explorar. Isto levaria a preconceitos de aprendizagem que, em última análise, diminuiriam o desempenho potencial.

## <a name="next-steps"></a>Passos seguintes

[Aprendizagem por reforço](concepts-reinforcement-learning.md) 