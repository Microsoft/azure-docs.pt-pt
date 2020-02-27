---
title: Eventos ativos e inativos - Personalizer
description: Este artigo discute a utilização de eventos ativos e inativos dentro do serviço Personalizer.
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: a8f27542208965e2b820b9fc45cfcc5353a7f193
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624255"
---
# <a name="active-and-inactive-events"></a>Eventos ativos e inativos

Um evento **ativo** é qualquer chamada para Rank onde sabe que vai mostrar o resultado ao cliente e determinar a pontuação da recompensa. Este é o comportamento padrão.

Um evento **inativo** é uma chamada para rank onde você não tem certeza se o utilizador alguma vez verá a ação recomendada, devido à lógica do negócio. Isto permite-lhe descartar o evento para que o Personalizer não seja treinado com a recompensa padrão. Eventos inativos não devem chamar a API recompensa.

É importante que o ciclo de aprendizagem conheça o tipo real de evento. Um evento inativo não terá uma chamada de Recompensa. Um evento ativo deve ter uma chamada De recompensa, mas se a chamada da API nunca for feita, a pontuação de recompensa padrão é aplicada. Mude o estado de um evento de inativo para ativo assim que souber que irá influenciar a experiência do utilizador.

## <a name="typical-active-events-scenario"></a>Cenário típico de eventos ativos

Quando a sua aplicação chama a Rank API, recebe a ação, que a aplicação deve mostrar no campo **rewardActionId.**  A partir desse momento, a Personalizer espera uma chamada de Recompensa com uma pontuação de recompensa que tenha o mesmo eventoId. A pontuação de recompensa é usada para treinar o modelo para futuras chamadas rank. Se nenhuma chamada Reward for recebida para o eventoId, uma recompensa padrão é aplicada. [As recompensas por defeito](how-to-settings.md#configure-rewards-for-the-feedback-loop) são definidas no seu recurso Personalizer no portal Azure.

## <a name="other-event-type-scenarios"></a>Outros cenários do tipo de evento

Em alguns cenários, a aplicação poderá ter de ligar para rank antes mesmo de saber se o resultado será usado ou exibido ao utilizador. Isto pode acontecer em situações em que, por exemplo, a renderização de páginas de conteúdos promovidos é substituída por uma campanha de marketing. Se o resultado da chamada Rank nunca foi utilizado e o utilizador nunca a viu, não envie uma chamada de Recompensa correspondente.

Normalmente, estes cenários acontecem quando:

* Está a pré-renderizar a UI que o utilizador pode ou não conseguir ver.
* A sua aplicação está a fazer personalização preditiva na qual as chamadas rank são feitas com pouco contexto em tempo real e a aplicação pode ou não usar a saída.

Nestes casos, use o Personalizer para ligar para rank, solicitando que o evento seja _inativo_. O personalizer não espera uma recompensa por este evento, e não aplicauma recompensa por defeito.

Mais tarde na sua lógica de negócio, se a aplicação usar a informação da chamada Rank, basta _ativar_ o evento. Assim que o evento estiver ativo, a Personalizer espera uma recompensa de evento. Se não for feita nenhuma chamada explícita para a API reward, o Personalizer aplica uma recompensa por defeito.

## <a name="inactive-events"></a>Eventos inativos

Para desativar o treino para um evento, ligue para rank usando `learningEnabled = False`.

Para um evento inativo, a aprendizagem é ativada implicitamente se enviar uma recompensa para o eventoId ou chamar a API `activate` para esse eventoId.

## <a name="next-steps"></a>Passos seguintes

* Saiba [como determinar a pontuação da recompensa e que dados considerar.](concept-rewards.md)
