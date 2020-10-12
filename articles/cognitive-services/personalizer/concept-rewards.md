---
title: Pontuação da recompensa - Personalizer
description: A pontuação da recompensa indica o quão bem a escolha de personalização, RewardActionID, resultou para o utilizador. O valor da pontuação de recompensa é determinado pela sua lógica de negócio, com base em observações do comportamento do utilizador. O personalizar treina os seus modelos de machine learning avaliando as recompensas.
ms.service: cognitive-services
ms.subservice: personalizer
ms.date: 02/20/2020
ms.topic: conceptual
ms.openlocfilehash: 58ce4d7593b23807f4b31e3e71cbfdcd873b1fcc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91253501"
---
# <a name="reward-scores-indicate-success-of-personalization"></a>Pontuações de recompensa indicam sucesso de personalização

A pontuação da recompensa indica o quão bem a escolha de personalização, [RewardActionID,](https://docs.microsoft.com/rest/api/cognitiveservices/personalizer/rank/rank#response)resultou para o utilizador. O valor da pontuação de recompensa é determinado pela sua lógica de negócio, com base em observações do comportamento do utilizador.

O personalizar treina os seus modelos de machine learning avaliando as recompensas.

Saiba como configurar [a](how-to-settings.md#configure-rewards-for-the-feedback-loop) pontuação de recompensa padrão no portal Azure para o seu recurso Personalizer.

## <a name="use-reward-api-to-send-reward-score-to-personalizer"></a>Use a API reward para enviar pontuação de recompensa para Personalizer

As recompensas são enviadas ao Personaler pela [API reward.](https://docs.microsoft.com/rest/api/cognitiveservices/personalizer/events/reward) Normalmente, uma recompensa é um número de 0 a 1. Uma recompensa negativa, com o valor de -1, é possível em certos cenários e só deve ser utilizada se tiver experiência com aprendizagem de reforço (RL). Personalizer treina o modelo para obter a maior soma possível de recompensas ao longo do tempo.

As recompensas são enviadas depois do comportamento do utilizador ter ocorrido, o que pode acontecer dias depois. O tempo máximo de tempo que o Personaler aguarda até que um evento seja considerado sem recompensa ou uma recompensa por defeito seja configurada com o Tempo de [Espera de Recompensa](#reward-wait-time) no portal Azure.

Se a pontuação da recompensa de um evento não tiver sido recebida dentro do **Tempo de Espera de Recompensa**, então a Recompensa **Padrão** será aplicada. Normalmente, a **[Recompensa Padrão](how-to-settings.md#configure-reward-settings-for-the-feedback-loop-based-on-use-case)** é configurada para ser zero.


## <a name="behaviors-and-data-to-consider-for-rewards"></a>Comportamentos e dados a ter em conta para recompensas

Considere estes sinais e comportamentos para o contexto da pontuação da recompensa:

* Entrada direta do utilizador para sugestões quando as opções estão envolvidas ("Quer dizer X?").
* Duração da sessão.
* Tempo entre sessões.
* Análise de sentimento das interações do utilizador.
* Perguntas diretas e mini inquéritos onde o bot pede ao utilizador feedback sobre utilidade, precisão.
* Resposta aos alertas, ou atraso na resposta aos alertas.

## <a name="composing-reward-scores"></a>Compor pontuações de recompensa

Uma pontuação Reward deve ser calculada na sua lógica de negócio. A pontuação pode ser representada como:

* Um único número enviado uma vez
* Uma pontuação enviada imediatamente (como 0.8) e uma pontuação adicional enviada mais tarde (normalmente 0.2).

## <a name="default-rewards"></a>Recompensas predefinidos

Se nenhuma recompensa for recebida dentro do [Tempo de Espera de Recompensa,](#reward-wait-time)a duração desde a chamada Rank, o Personalizer aplica implicitamente a Recompensa **Padrão** a esse evento Rank.

## <a name="building-up-rewards-with-multiple-factors"></a>Acumular recompensas com múltiplos fatores

Para uma personalização eficaz, pode acumular a pontuação da recompensa com base em múltiplos fatores.

Por exemplo, pode aplicar estas regras para personalizar uma lista de conteúdos de vídeo:

|Comportamento do utilizador|Valor parcial da pontuação|
|--|--|
|O utilizador clicou no item superior.|+0,5 recompensa|
|O utilizador abriu o conteúdo real desse item.|+0.3 recompensa|
|O utilizador assistiu a 5 minutos do conteúdo ou 30%, o que for mais longo.|+0.2 recompensa|
|||

Em seguida, pode enviar a recompensa total para a API.

## <a name="calling-the-reward-api-multiple-times"></a>Chamando a API de recompensa várias vezes

Também pode ligar para a API reward usando o mesmo ID do evento, enviando diferentes pontuações de recompensa. Quando o Personalizer recebe essas recompensas, determina a recompensa final para esse evento agregando-as conforme especificado na configuração do Personalizer.

Valores de agregação:

*  **Primeiro**: Leva a primeira pontuação de recompensa recebida para o evento, e descarta o resto.
* **Soma:** Leva todas as pontuações de recompensa recolhidas para o eventId, e junta-as.

Todas as recompensas para um evento, que são recebidos após o **Tempo de Espera do Prémio,** são descartadas e não afetam o treino dos modelos.

Ao somar os resultados das recompensas, a sua recompensa final pode estar fora do intervalo de pontuação esperado. Isto não vai fazer o serviço falhar.

## <a name="best-practices-for-calculating-reward-score"></a>Boas Práticas para calcular pontuação de recompensa

* **Considere os verdadeiros indicadores de personalização bem-sucedida**: É fácil pensar em termos de cliques, mas uma boa recompensa baseia-se no que pretende que os seus utilizadores *atinjam* em vez do que quer que as pessoas *façam.*  Por exemplo, recompensar cliques pode levar a selecionar conteúdo que é propenso a clickbait.

* **Use uma pontuação de recompensa para o quão boa a personalização funcionou**: Personalizar uma sugestão de filme resultaria, esperemos, em que o utilizador assistisse ao filme e lhe desse uma classificação elevada. Uma vez que a classificação do filme provavelmente depende de muitas coisas (a qualidade da representação, o humor do utilizador), não é um bom sinal de recompensa pelo funcionamento *da personalização.* O utilizador que assiste aos primeiros minutos do filme, no entanto, pode ser um melhor sinal de eficácia de personalização e enviar uma recompensa de 1 após 5 minutos será um sinal melhor.

* **As recompensas aplicam-se apenas ao RewardActionID**: O Personalizador aplica as recompensas para compreender a eficácia da ação especificada no RewardActionID. Se optar por exibir outras ações e o utilizador clicar nelas, a recompensa deverá ser zero.

* **Considere consequências não intencionais**: Crie funções de recompensa que conduzam a resultados responsáveis com [ética e uso responsável](ethics-responsible-use.md).

* **Utilizar recompensas incrementais**: Adicionar recompensas parciais para comportamentos menores ajuda o Personaler a obter melhores recompensas. Esta recompensa incremental permite que o algoritmo saiba que está cada vez mais perto de envolver o utilizador no comportamento final desejado.
    * Se estiver a mostrar uma lista de filmes, se o utilizador pairar sobre o primeiro durante algum tempo para ver mais informações, pode determinar que algum envolvimento do utilizador aconteceu. O comportamento pode contar com uma pontuação de recompensa de 0,1.
    * Se o utilizador abriu a página e saiu, a pontuação da recompensa pode ser de 0.2.

## <a name="reward-wait-time"></a>Recompensar o tempo de espera

O personalista irá correlacionar a informação de uma chamada rank com as recompensas enviadas nas chamadas Reward para treinar o modelo. Estes podem vir em momentos diferentes. O personalizador espera por um tempo limitado, começando quando a chamada do Rank aconteceu, mesmo que a chamada rank foi feita como um evento inativo, e ativada mais tarde.

Se o **Tempo de Espera de Recompensa** expirar e não tiver havido informação sobre recompensas, uma recompensa por defeito é aplicada a esse evento para treino. A duração máxima de espera é de 6 dias.

## <a name="best-practices-for-reward-wait-time"></a>Melhores práticas para o tempo de espera da recompensa

Siga estas recomendações para obter melhores resultados.

* Faça o tempo de espera da recompensa o mais curto possível, deixando tempo suficiente para obter feedback do utilizador.

* Não escolha uma duração mais curta do que o tempo necessário para obter feedback. Por exemplo, se algumas das suas recompensas chegarem depois de um utilizador ter assistido a 1 minuto de um vídeo, o comprimento da experiência deve ser pelo menos o dobro disso.

## <a name="next-steps"></a>Passos seguintes

* [Aprendizagem por reforço](concepts-reinforcement-learning.md)
* [Experimente a Rank API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank/console)
* [Experimente a API reward](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward)
