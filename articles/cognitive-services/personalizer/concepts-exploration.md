---
title: Exploração - Personalizer
titleSuffix: Azure Cognitive Services
description: Com a exploração, Personalizer é podem continuar a entrega de bons resultados, mesmo quando altera o comportamento do utilizador. A escolha de uma definição de exploração é uma decisão comercial sobre a proporção de interações do usuário para explorar com, para melhorar o modelo.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: edjez
ms.openlocfilehash: ebb59b6bb7c36f4558b2bd63d2d55fa95823c4c3
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722475"
---
# <a name="exploration-and-exploitation"></a>Exploração e a exploração

Com a exploração, Personalizer é podem continuar a entrega de bons resultados, mesmo quando altera o comportamento do utilizador.

Quando Personalizer recebe uma chamada de classificação, ele retorna um RewardActionID que optar por:
* Utiliza a exploração de acordo com o mais provável comportamento de utilizador com base no modelo de aprendizagem de máquina atual.
* Utiliza a exploração, que não corresponde a ação que tem a maior probabilidade da classificação.

<!--
Returning the most probable action is called *exploit* behavior. Returning a different action is called *exploration*.
-->
Personalizer atualmente usa um algoritmo chamado *épsilon greedy* para explorar. 

## <a name="choosing-an-exploration-setting"></a>Escolher uma definição de exploração

Configura a percentagem de tráfego a utilizar para exploração do portal do Azure **definições** página para Personalizer. Esta definição determina a percentagem de chamadas de classificação que realizam a exploração. 

Personalizer determina se deve explorar ou explorar com este probabilidade em cada chamada de classificação. Isto é diferente de comportamento na alguns A / estruturas de B, que bloquear um tratamento nos IDs de utilizador específico.

## <a name="best-practices-for-choosing-an-exploration-setting"></a>Melhores práticas para a escolha de uma definição de exploração

<!--
@edjez - you say what not to do, but make no recommendations of what **to** do. 
-->

A escolha de uma definição de exploração é uma decisão comercial sobre a proporção de interações do usuário para explorar com, para melhorar o modelo. 

Uma definição de zero irá negar muitos dos benefícios de Personalizer. Com esta definição, o Personalizer utiliza não interações de utilizador para detetar melhor interações de utilizador. Isso leva a estagnação de modelo, descompassos e desempenho, por fim, inferior.

Uma definição que é demasiado elevada irá negar os benefícios do aprendizado de comportamento do utilizador. Defini-la como 100% implica aleatoriedade, de forma constante, e nenhum aprendido comportamento dos utilizadores não seria influenciam o resultado.

É importante não alterar o comportamento da aplicação com base se lhe forem apresentados se Personalizer é explorar ou a explorar. Isso poderia levar a tendências que, por fim, devem diminuir o desempenho potencial de aprendizado.

## <a name="next-steps"></a>Passos Seguintes

[Aprendizagem por reforço](concepts-reinforcement-learning.md) 