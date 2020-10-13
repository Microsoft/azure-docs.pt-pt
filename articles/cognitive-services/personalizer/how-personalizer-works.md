---
title: Como funciona o Personaler - Personalizer
description: O _loop_ Personalizer utiliza machine learning para construir o modelo que prevê a ação de topo para o seu conteúdo. O modelo é treinado exclusivamente nos seus dados que lhe enviou com as chamadas Rank and Reward.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: cfbe5cf8c19bfafb38f6149391e09350785ebf9c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91303612"
---
# <a name="how-personalizer-works"></a>Como funciona o Personalizador

O recurso Personalizer, o seu _ciclo de aprendizagem,_ utiliza machine learning para construir o modelo que prevê a ação de topo para o seu conteúdo. O modelo é treinado exclusivamente nos seus dados que lhe enviou com as chamadas **Rank** and **Reward.** Cada ciclo é completamente independente um do outro.

## <a name="rank-and-reward-apis-impact-the-model"></a>ApIs de classificação e recompensa impactam o modelo

Envia _ações com funcionalidades_ e _funcionalidades_ de contexto para a Rank API. A **API de classificação** decide usar:

* _Exploração_: O modelo atual para decidir a melhor ação com base em dados passados.
* _Explore:_ Selecione uma ação diferente em vez da ação de topo. Configura [esta percentagem](how-to-settings.md#configure-exploration-to-allow-the-learning-loop-to-adapt) para o seu recurso Personalizer no portal Azure.

Determina a pontuação da recompensa e envia essa pontuação para a API reward. A **API de Recompensa:**

* Recolhe dados para treinar o modelo gravando as características e recompensando pontuações de cada classificação.
* Utiliza esses dados para atualizar o modelo com base na configuração especificada na _Política de Aprendizagem_.

## <a name="your-system-calling-personalizer"></a>O seu sistema chama de Personaler

A imagem a seguir mostra o fluxo arquitetónico de chamar as chamadas Rank and Reward:

![alt texto](./media/how-personalizer-works/personalization-how-it-works.png "Como funciona a personalização")

1. Envia _ações com funcionalidades_ e _funcionalidades_ de contexto para a Rank API.

    * Personalizer decide se explora o modelo atual ou explora novas escolhas para o modelo.
    * O resultado do ranking é enviado para o EventHub.
1. O topo de hierarquia é devolvido ao seu sistema como _ID de ação de recompensa._
    O seu sistema apresenta esse conteúdo e determina uma pontuação de recompensa com base nas suas próprias regras de negócio.
1. O seu sistema devolve a pontuação da recompensa ao ciclo de aprendizagem.
    * Quando o Personaler recebe a recompensa, a recompensa é enviada para o EventHub.
    * O posto e a recompensa estão correlacionados.
    * O modelo de IA é atualizado com base nos resultados da correlação.
    * O motor de inferência é atualizado com o novo modelo.

## <a name="personalizer-retrains-your-model"></a>Personalizer retreina o seu modelo

O personalização retrinta o seu modelo com base na definição **de atualização de frequência do Modelo** no seu recurso Personalizer no portal Azure.

O personalizador utiliza todos os dados atualmente retidos, com base na definição de retenção de **Dados** em número de dias no seu recurso Personalizer no portal Azure.

## <a name="research-behind-personalizer"></a>Pesquisa por trás do Personalizar

O personalização baseia-se em ciência de ponta e investigação na área da Aprendizagem do [Reforço,](concepts-reinforcement-learning.md) incluindo trabalhos, atividades de investigação e áreas de exploração em curso na Microsoft Research.

## <a name="next-steps"></a>Passos seguintes

Conheça os [principais cenários](where-can-you-use-personalizer.md) para Personalizar