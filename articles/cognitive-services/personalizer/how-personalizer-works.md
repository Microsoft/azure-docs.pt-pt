---
title: Como funciona o Personalizer - Personalizer
description: O _loop_ Personalizer utiliza machine learning para construir o modelo que prevê a ação de topo para o seu conteúdo. O modelo é treinado exclusivamente nos seus dados que lhe enviou com as chamadas Rank and Reward.
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 836c207213ac52a60e27da6fc957418187059023
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77623749"
---
# <a name="how-personalizer-works"></a>Como funciona o Personalizador

O recurso Personalizer, o seu ciclo de _aprendizagem,_ utiliza machine learning para construir o modelo que prevê a ação de topo para o seu conteúdo. O modelo é treinado exclusivamente nos seus dados que lhe enviou com as chamadas **Rank** and **Reward.** Cada loop é completamente independente um do outro.

## <a name="rank-and-reward-apis-impact-the-model"></a>As APIs de classificação e recompensa têm impacto no modelo

Envia _ações com funcionalidades_ e _características_ de contexto para a Rank API. A **API de grau** decide usar qualquer um:

* _Explore_: O modelo atual para decidir a melhor ação com base em dados anteriores.
* _Explore_: Selecione uma ação diferente em vez da ação de topo. [Configura esta percentagem](how-to-settings.md#configure-exploration-to-allow-the-learning-loop-to-adapt) para o seu recurso Personalizer no portal Azure.

Determina a pontuação da recompensa e envia essa pontuação para a API recompensa. A **API recompensa:**

* Recolhe dados para treinar o modelo gravando as funcionalidades e recompensando as pontuações de cada chamada de classificação.
* Utiliza esses dados para atualizar o modelo com base na configuração especificada na _Política de Aprendizagem_.

## <a name="your-system-calling-personalizer"></a>O seu sistema chama personalizador

A imagem seguinte mostra o fluxo arquitetónico de chamar as chamadas Rank and Reward:

![texto alt](./media/how-personalizer-works/personalization-how-it-works.png "Como funciona a Personalização")

1. Envia _ações com funcionalidades_ e _características_ de contexto para a Rank API.

    * O Personalizer decide se explora o modelo atual ou explora novas escolhas para o modelo.
    * O resultado do ranking é enviado para o EventHub.
1. O topo de patente é devolvido ao seu sistema como _id de ação de recompensa_.
    O seu sistema apresenta esse conteúdo e determina uma pontuação de recompensa com base nas suas próprias regras de negócio.
1. O seu sistema devolve a pontuação de recompensa ao ciclo de aprendizagem.
    * Quando o Personalizer recebe a recompensa, a recompensa é enviada para o EventHub.
    * O posto e a recompensa estão correlacionados.
    * O modelo de IA é atualizado com base nos resultados da correlação.
    * O motor de inferência é atualizado com o novo modelo.

## <a name="personalizer-retrains-your-model"></a>Personalizer retreina o seu modelo

O personalizer retreina o seu modelo com base na definição de **atualização** de frequência do Modelo no seu recurso Personalizer no portal Azure.

O Personalizer utiliza todos os dados atualmente retidos, com base na definição de **retenção** de Dados em número de dias no seu recurso Personalizer no portal Azure.

## <a name="research-behind-personalizer"></a>Pesquisa por trás do Personalizer

O Personalizer baseia-se em ciência e investigação de ponta na área da Aprendizagem de [Reforço,](concepts-reinforcement-learning.md) incluindo trabalhos, atividades de investigação e áreas de exploração em curso na Microsoft Research.

## <a name="next-steps"></a>Passos seguintes

Conheça os [melhores cenários](where-can-you-use-personalizer.md) para Personalizar