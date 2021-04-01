---
title: Eventos ativos e inativos - Personalizer
description: Este artigo discute a utilização de eventos ativos e inativos no âmbito do serviço Personalizar.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 7d1044d02ceba1f3d0996b1fe1c8a9a44b31049b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "91253654"
---
# <a name="active-and-inactive-events"></a>Eventos ativos e inativos

Um evento **ativo** é qualquer chamada para Rank onde sabe que vai mostrar o resultado ao cliente e determinar a pontuação da recompensa. Este é o comportamento padrão.

Um evento **inativo** é uma chamada para Rank onde não tem certeza se o utilizador alguma vez verá a ação recomendada, devido à lógica do negócio. Isto permite-lhe descartar o evento para que o Personalizer não seja treinado com a recompensa padrão. Eventos inativos não devem chamar a API reward.

É importante que o ciclo de aprendizagem conheça o tipo real de evento. Um evento inativo não terá uma chamada de Recompensa. Um evento ativo deve ter uma chamada Reward mas se a chamada da API nunca for feita, a pontuação de recompensa padrão é aplicada. Alterar o estado de um evento de inativo para ativo assim que o souber influenciará a experiência do utilizador.

## <a name="typical-active-events-scenario"></a>Cenário típico de eventos ativos

Quando a sua aplicação chama a API de grau, recebe a ação, que a aplicação deve mostrar no campo **RewardActionId.**  A partir desse momento, o Personalizer espera uma chamada reward com uma pontuação de recompensa que tenha o mesmo eventId. A pontuação da recompensa é usada para treinar o modelo para futuras chamadas rank. Se não for recebida nenhuma chamada reward para o eventId, é aplicada uma recompensa por defeito. [As recompensas predefinidas](how-to-settings.md#configure-rewards-for-the-feedback-loop) são definidas no seu recurso Personalizer no portal Azure.

## <a name="other-event-type-scenarios"></a>Outros cenários de tipo de evento

Em alguns cenários, a aplicação poderá ter de ligar para o Rank antes mesmo de saber se o resultado será utilizado ou exibido ao utilizador. Isto pode acontecer em situações em que, por exemplo, a prestação de páginas de conteúdos promovidos é substituída por uma campanha de marketing. Se o resultado da chamada Rank nunca foi utilizado e o utilizador nunca a viu, não envie uma chamada de Recompensa correspondente.

Tipicamente, estes cenários acontecem quando:

* Está a pré-rerender uI que o utilizador pode ou não ver.
* A sua aplicação está a fazer personalização preditiva na qual as chamadas Rank são feitas com pouco contexto em tempo real e a aplicação pode ou não usar a saída.

Nestes casos, utilize o Personaler para ligar para o Rank, solicitando que o evento seja _inativo._ O personalização não espera uma recompensa por este evento, e não aplicará uma recompensa por defeito.

Mais tarde, na sua lógica de negócio, se a aplicação utilizar as informações da chamada Rank, basta _ativar_ o evento. Assim que o evento estiver ativo, o Personalizer espera uma recompensa do evento. Se não for feita nenhuma chamada explícita à API Reward, o Personalizer aplica uma recompensa por defeito.

## <a name="inactive-events"></a>Eventos inativos

Para desativar o treino para um evento, ligue para rank usando `learningEnabled = False` .

Para um evento inativo, a aprendizagem é ativada implicitamente se enviar uma recompensa pelo eventId ou ligar para a `activate` API para esse eventoId.

## <a name="next-steps"></a>Passos seguintes

* Saiba [como determinar a pontuação da recompensa e quais os dados a considerar.](concept-rewards.md)
