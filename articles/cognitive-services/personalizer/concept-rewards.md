---
title: Pontuação de recompensa - Personalizer
description: A pontuação da recompensa indica o quão bem a escolha de personalização, RewardActionID, resultou para o utilizador. O valor da pontuação de recompensa é determinado pela sua lógica de negócio, com base em observações do comportamento do utilizador. A Personalizer treina os seus modelos de machine learning avaliando as recompensas.
ms.date: 02/20/2020
ms.topic: conceptual
ms.openlocfilehash: 734e4d0fdcec25884f8535ec61ccd10569fa8890
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219370"
---
# <a name="reward-scores-indicate-success-of-personalization"></a>Notas de recompensa indicam sucesso da personalização

A pontuação da recompensa indica o quão bem a escolha de personalização, [RewardActionID,](https://docs.microsoft.com/rest/api/cognitiveservices/personalizer/rank/rank#response)resultou para o utilizador. O valor da pontuação de recompensa é determinado pela sua lógica de negócio, com base em observações do comportamento do utilizador.

A Personalizer treina os seus modelos de machine learning avaliando as recompensas.

Saiba como configurar [a](how-to-settings.md#configure-rewards-for-the-feedback-loop) pontuação de recompensa padrão no portal Azure para o seu recurso Personalizer.

## <a name="use-reward-api-to-send-reward-score-to-personalizer"></a>Use a Reward API para enviar pontuação de recompensa ao Personalizer

As recompensas são enviadas ao Personalizer pela [Reward API.](https://docs.microsoft.com/rest/api/cognitiveservices/personalizer/events/reward) Tipicamente, uma recompensa é um número de 0 a 1. Uma recompensa negativa, com o valor de -1, é possível em certos cenários e só deve ser usada se for experiente com aprendizagem de reforço (RL). O Personalizer treina o modelo para alcançar a maior soma possível de recompensas ao longo do tempo.

As recompensas são enviadas após o comportamento do utilizador, o que pode ser dias depois. O tempo máximo de tempo que o Personalizer aguarda até que um evento seja considerado sem recompensa ou uma recompensa por defeito seja configurada com o Tempo de [Espera de Recompensa](#reward-wait-time) no portal Azure.

Se a pontuação de recompensa de um evento não tiver sido recebida dentro do Tempo de **Espera da Recompensa,** então a **Recompensa Padrão** será aplicada. Tipicamente, a **[Recompensa Padrão](how-to-settings.md#configure-reward-settings-for-the-feedback-loop-based-on-use-case)** está configurada como zero.


## <a name="behaviors-and-data-to-consider-for-rewards"></a>Comportamentos e dados a considerar para recompensas

Considere estes sinais e comportamentos para o contexto da pontuação da recompensa:

* Entrada direta do utilizador para sugestões quando as opções estão envolvidas ("Quer dizer X?").
* Comprimento da sessão.
* Tempo entre sessões.
* Análise de sentimento das interações do utilizador.
* Perguntas diretas e mini inquéritos onde o bot pede ao utilizador feedback sobre utilidade, precisão.
* Resposta a alertas, ou atraso na resposta aos alertas.

## <a name="composing-reward-scores"></a>Compor pontuações de recompensa

Uma pontuação de recompensa deve ser calculada na sua lógica de negócio. A pontuação pode ser representada como:

* Um único número enviado uma vez
* Uma pontuação enviada imediatamente (como 0.8) e uma pontuação adicional enviada mais tarde (tipicamente 0.2).

## <a name="default-rewards"></a>Recompensas por defeito

Se nenhuma recompensa for recebida dentro do Tempo de [Espera da Recompensa](#reward-wait-time), a duração desde a chamada rank, personalizer aplica implicitamente a Recompensa **Padrão** a esse evento Rank.

## <a name="building-up-rewards-with-multiple-factors"></a>Construindo recompensas com múltiplos fatores

Para uma personalização eficaz, pode aumentar a pontuação da recompensa com base em múltiplos fatores.

Por exemplo, pode aplicar estas regras para personalizar uma lista de conteúdos de vídeo:

|Comportamento do utilizador|Valor parcial da pontuação|
|--|--|
|O utilizador clicou no item superior.|+0,5 recompensa|
|O utilizador abriu o conteúdo real desse artigo.|+0.3 recompensa|
|O utilizador assistiu a 5 minutos do conteúdo ou 30%, o que for mais longo.|+0.2 recompensa|
|||

Pode então enviar a recompensa total para a API.

## <a name="calling-the-reward-api-multiple-times"></a>Chamando a API recompensa várias vezes

Também pode ligar para a Reward API usando o mesmo ID do evento, enviando diferentes pontuações de recompensa. Quando o Personalizer recebe essas recompensas, determina a recompensa final para esse evento, agregando-as conforme especificado na configuração personalizer.

Valores de agregação:

*  **Primeiro:** Pega na primeira nota de recompensa recebida para o evento, e descarta o resto.
* **Soma**: Pega em todas as notas de recompensa recolhidas para o eventoId, e adiciona-as juntas.

Todas as recompensas para um evento, que são recebidos após o Tempo de **Espera da Recompensa,** são descartadas e não afetam a formação de modelos.

Ao somar pontuações de recompensa, a sua recompensa final pode estar fora do intervalo de pontuação esperado. Isto não vai fazer o serviço falhar.

## <a name="best-practices-for-calculating-reward-score"></a>Boas Práticas para calcular a pontuação da recompensa

* **Considere verdadeiros indicadores de personalização bem-sucedida**: É fácil pensar em termos de cliques, mas uma boa recompensa baseia-se no que pretende que os seus utilizadores *alcancem* em vez do que quer que as pessoas *façam.*  Por exemplo, premiar em cliques pode levar à seleção de conteúdos que são propensos a cliques.

* **Use uma pontuação de recompensa pelo bom funcionamento da personalização:** Personalizar uma sugestão de filme resultaria, com sorte, em que o utilizador assistia ao filme e lhe dava uma classificação elevada. Uma vez que a classificação cinematográfica provavelmente depende de muitas coisas (a qualidade da representação, o humor do utilizador), não é um bom sinal de recompensa para o quão bem *a personalização* funcionou. O utilizador que assiste aos primeiros minutos do filme, no entanto, pode ser um melhor sinal de eficácia de personalização e enviar uma recompensa de 1 após 5 minutos será um sinal melhor.

* **As recompensas aplicam-se apenas ao RewardActionID**: Personalizer aplica as recompensas para compreender a eficácia da ação especificada no RewardActionID. Se optar por apresentar outras ações e o utilizador clicar nelas, a recompensa deve ser zero.

* **Considere as consequências não intencionais**: Criar funções de recompensa que conduzam a resultados responsáveis com [ética e utilização responsável](ethics-responsible-use.md).

* **Use Recompensas Incrementais**: Adicionar recompensas parciais para comportamentos de utilizadores mais pequenos ajuda o Personalizer a obter melhores recompensas. Esta recompensa incremental permite que o algoritmo saiba que está cada vez mais perto de envolver o utilizador no comportamento final desejado.
    * Se estiver a mostrar uma lista de filmes, se o utilizador pairar sobre o primeiro durante algum tempo para ver mais informações, pode determinar que algum envolvimento do utilizador aconteceu. O comportamento pode contar com uma pontuação de recompensa de 0.1.
    * Se o utilizador abriu a página e saiu, a pontuação da recompensa pode ser de 0,2.

## <a name="reward-wait-time"></a>Tempo de espera de recompensa

O Personalizer correlacionará a informação de uma chamada de Rank com as recompensas enviadas em chamadas Reward para treinar o modelo. Estes podem vir em momentos diferentes. O personalizer aguarda por um tempo limitado, a partir do momento em que a chamada do Rank aconteceu, mesmo que a chamada de Rank tenha sido feita como um evento inativo, e ativada mais tarde.

Se o Tempo de **Espera da Recompensa** expirar, e não houver informações de recompensa, uma recompensa padrão é aplicada a esse evento para treino. A duração máxima de espera é de 6 dias.

## <a name="best-practices-for-reward-wait-time"></a>Boas práticas para tempo de espera de recompensa

Siga estas recomendações para obter melhores resultados.

* Faça o Tempo de Espera de Recompensa o mais curto possível, deixando tempo suficiente para obter feedback do utilizador.

* Não escolha uma duração mais curta do que o tempo necessário para obter feedback. Por exemplo, se algumas das suas recompensas chegarem depois de um utilizador ter visto 1 minuto de um vídeo, o comprimento da experiência deve ser pelo menos o dobro disso.

## <a name="next-steps"></a>Passos seguintes

* [Aprendizagem por reforço](concepts-reinforcement-learning.md)
* [Experimente a API rank](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank/console)
* [Experimente a API recompensa](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward)
