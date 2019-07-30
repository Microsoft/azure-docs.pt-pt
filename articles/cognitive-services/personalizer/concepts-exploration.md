---
title: Exploração-personalizador
titleSuffix: Azure Cognitive Services
description: Com a exploração, o personalizador é capaz de continuar fornecendo bons resultados, mesmo que o comportamento do usuário mude. A escolha de uma configuração de exploração é uma decisão de negócios sobre a proporção de interações do usuário para explorar, a fim de melhorar o modelo.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: diberry
ms.openlocfilehash: cfecea6a64301d86aa657420dc300c26d4ed6f1e
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663390"
---
# <a name="exploration-and-exploitation"></a>Exploração e exploração

Com a exploração, o personalizador é capaz de continuar fornecendo bons resultados, mesmo que o comportamento do usuário mude.

Quando o personalizador recebe uma chamada de classificação, ele retorna um RewardActionID que:
* Usa a exploração para corresponder ao comportamento mais provável do usuário com base no modelo de aprendizado de máquina atual.
* Usa exploração, que não corresponde à ação que tem a maior probabilidade na classificação.

<!--
Returning the most probable action is called *exploit* behavior. Returning a different action is called *exploration*.
-->
No momento, o personalizador usa um algoritmo chamado *Épsilon ávido* para explorar. 

## <a name="choosing-an-exploration-setting"></a>Escolhendo uma configuração de exploração

Configure a porcentagem de tráfego a ser usada para exploração na página de **configurações** do portal do Azure para personalizador. Essa configuração determina a porcentagem de chamadas de classificação que executam a exploração. 

O personalizador determina se é para explorar ou explorar com essa probabilidade em cada chamada de classificação. Isso é diferente do comportamento em algumas estruturas A/B que bloqueiam um tratamento de IDs de usuário específicas.

## <a name="best-practices-for-choosing-an-exploration-setting"></a>Práticas recomendadas para escolher uma configuração de exploração

<!--
@edjez - you say what not to do, but make no recommendations of what **to** do. 
-->

A escolha de uma configuração de exploração é uma decisão de negócios sobre a proporção de interações do usuário para explorar, a fim de melhorar o modelo. 

Uma configuração de zero não irá negar muitos dos benefícios do personalizador. Com essa configuração, o personalizador não usa interações de usuário para descobrir melhores interações do usuário. Isso leva ao modelo estagnação, descompasso e, por fim, ao desempenho mais baixo.

Uma configuração muito alta irá negar os benefícios de aprender com o comportamento do usuário. Defini-lo como 100% implica em randomização constante, e qualquer comportamento aprendido dos usuários não influenciaria o resultado.

É importante não alterar o comportamento do aplicativo com base no fato de você ver se o personalizador está explorando ou explorando. Isso levaria a tendências de aprendizado que, por fim, diminuiria o desempenho potencial.

## <a name="next-steps"></a>Passos Seguintes

[Aprendizado de reforço](concepts-reinforcement-learning.md) 