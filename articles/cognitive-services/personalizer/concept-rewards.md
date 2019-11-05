---
title: Pontuação de recompensa-personalizador
titleSuffix: Azure Cognitive Services
description: A pontuação de recompensa indica quão bem a opção de personalização, RewardActionID, resultou para o usuário. O valor da Pontuação de recompensa é determinado pela lógica de negócios, com base nas observações do comportamento do usuário. O personalizado treina seus modelos de aprendizado de máquina avaliando as recompensas.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: diberry
ms.openlocfilehash: a47d6014e51dce81c9caf82f8624896c439f050d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490893"
---
# <a name="reward-scores-indicate-success-of-personalization"></a>As pontuações de recompensa indicam o sucesso da personalização

A pontuação de recompensa indica quão bem a opção de personalização, [RewardActionID](https://docs.microsoft.com/rest/api/cognitiveservices/personalizer/rank/rank#response), resultou para o usuário. O valor da Pontuação de recompensa é determinado pela lógica de negócios, com base nas observações do comportamento do usuário.

O personalizado treina seus modelos de aprendizado de máquina avaliando as recompensas. 

## <a name="use-reward-api-to-send-reward-score-to-personalizer"></a>Usar a API de recompensa para enviar a pontuação de recompensa para o personalizador

As recompensas são enviadas ao personalizador pela [API de recompensa](https://docs.microsoft.com/rest/api/cognitiveservices/personalizer/events/reward). Normalmente, um prêmio é um número de 0 e 1. Um prêmio negativo, com o valor de-1, é possível em determinados cenários e só deve ser usado se você tiver experiência com o reforço Learning (RL). O personalizado treina o modelo para alcançar a maior soma possível de recompensas ao longo do tempo.

As recompensas são enviadas após o comportamento do usuário, que pode ser dias mais tarde. A quantidade máxima de tempo que o personalizado aguardará até que um evento seja considerado sem recompensa ou uma recompensa padrão seja configurada com o [tempo de espera de recompensa](#reward-wait-time) no portal do Azure.

Se a pontuação de recompensa de um evento não tiver sido recebida dentro do **tempo de espera da recompensa**, a **recompensa padrão** será aplicada. Normalmente, a **[recompensa padrão](how-to-settings.md#configure-reward-settings-for-the-feedback-loop-based-on-use-case)** é configurada como zero.


## <a name="behaviors-and-data-to-consider-for-rewards"></a>Comportamentos e dados a serem considerados para recompensas

Considere estes sinais e comportamentos para o contexto da Pontuação de recompensa:

* Entrada direta do usuário para sugestões quando as opções estão envolvidas ("você significa X?").
* Comprimento da sessão.
* Tempo entre as sessões.
* Análise de sentimentos das interações do usuário.
* Perguntas diretas e mini-pesquisas em que o bot pede ao usuário comentários sobre a utilidade, a precisão.
* Resposta a alertas ou atraso para resposta a alertas.

## <a name="composing-reward-scores"></a>Compondo pontuações de recompensa

Uma pontuação de recompensa deve ser calculada em sua lógica de negócios. A pontuação pode ser representada como:

* Um único número enviado uma vez 
* Uma pontuação enviada imediatamente (como 0,8) e uma pontuação adicional enviada posteriormente (geralmente 0,2).

## <a name="default-rewards"></a>Recompensas padrão

Se nenhum recompensa for recebido dentro do [tempo de espera da recompensa](#reward-wait-time), a duração desde a chamada de classificação, o personalizador aplicará implicitamente a **recompensa padrão** a esse evento de classificação.

## <a name="building-up-rewards-with-multiple-factors"></a>Criando recompensas com vários fatores  

Para uma personalização efetiva, você pode criar a pontuação de recompensa com base em vários fatores. 

Por exemplo, você pode aplicar essas regras para personalizar uma lista de conteúdo de vídeo:

|Comportamento do usuário|Valor de Pontuação parcial|
|--|--|
|O usuário clicou no item superior.|\+ 0,5 recompensa|
|O usuário abriu o conteúdo real desse item.|\+ 0,3 recompensa|
|O usuário monitorou 5 minutos do conteúdo ou 30%, o que for maior.|\+ 0,2 recompensa|
|||

Em seguida, você pode enviar a recompensa total para a API.

## <a name="calling-the-reward-api-multiple-times"></a>Chamar a API de recompensa várias vezes

Você também pode chamar a API de recompensa usando a mesma ID de evento, enviando pontuações de recompensa diferentes. Quando o personalizador obtém essas recompensas, ele determina a recompensa final para esse evento, agregando-os conforme especificado na configuração do personalizador.

Valores de agregação:

*  **Primeiro**: usa a primeira pontuação de recompensa recebida para o evento e descarta o restante.
* **Sum**: Obtém todas as pontuações de recompensa coletadas para o EventID e as adiciona.

Todas as recompensas de um evento, que são recebidas após o **tempo de espera da recompensa**, são descartadas e não afetam o treinamento dos modelos.

Ao adicionar pontuações de recompensa, sua recompensa final pode estar fora do intervalo de Pontuação esperado. Isso não fará com que o serviço falhe.

## <a name="best-practices-for-calculating-reward-score"></a>Práticas recomendadas para calcular a pontuação de recompensa

* **Considere os indicadores verdadeiros de personalização bem-sucedida**: é fácil pensar em termos de cliques, mas um bom prêmio é baseado no que você deseja que os usuários *obtenham* em vez do que você deseja que as pessoas *façam*.  Por exemplo, recompensa em cliques pode levar à seleção de conteúdo que é propenso a clickbait.

* **Use uma pontuação de recompensa para saber a qualidade da personalização**: personalizar uma sugestão de filme esperaria fazer com que o usuário Assista ao filme e dando a ele uma classificação alta. Como a classificação do filme provavelmente depende de muitas coisas (a qualidade da ação, o humor do usuário), não é um bom sinal para o quão bem *a personalização* funcionou. O usuário assistindo aos primeiros minutos do filme, no entanto, pode ser um sinal melhor de eficácia da personalização e enviar um prêmio de 1 após 5 minutos será um sinal melhor.

* As **recompensas se aplicam somente a RewardActionID**: o personalizador aplica as recompensas para entender a eficácia da ação especificada em RewardActionID. Se você optar por exibir outras ações e o usuário clicar nelas, a recompensa deverá ser zero.

* **Considere consequências indesejadas**: Crie funções de recompensa que levam a resultados responsáveis com [ética e uso responsável](ethics-responsible-use.md).

* **Usar recompensas incrementais**: a adição de recompensas parciais para comportamentos menores de usuário ajuda a personalizar a obtenção de melhores recompensas. Essa recompensa incremental permite que o algoritmo saiba que está ficando mais próximo de envolver o usuário no comportamento final desejado.
    * Se você estiver mostrando uma lista de filmes, se o usuário passar o mouse sobre o primeiro por um tempo para ver mais informações, você poderá determinar que algum envolvimento do usuário aconteceu. O comportamento pode contar com uma pontuação de recompensa de 0,1. 
    * Se o usuário abriu a página e, em seguida, saiu, a pontuação de recompensa pode ser 0,2. 

## <a name="reward-wait-time"></a>tempo de espera de recompensa

O personalizador correlacionará as informações de uma chamada de classificação com as recompensas enviadas em chamadas de recompensa para treinar o modelo. Eles podem surgir em momentos diferentes. O personalizador aguarda um tempo limitado, iniciando quando a chamada de classificação ocorreu, mesmo que a chamada de classificação tenha sido feita como um evento inativo e ativada mais tarde.

Se o **tempo de espera de recompensa** expirar e não houver informações de recompensa, um prêmio padrão será aplicado a esse evento para treinamento. A duração máxima de espera é de seis dias.

## <a name="best-practices-for-reward-wait-time"></a>Práticas recomendadas para recompensar o tempo de espera

Siga estas recomendações para obter melhores resultados.

* Torne o tempo de espera da recompensa o mais curto possível, enquanto deixa tempo suficiente para obter comentários do usuário. 

* Não escolha uma duração que seja menor do que o tempo necessário para obter comentários. Por exemplo, se surgirem algumas das suas recompensas depois que um usuário tiver observado 1 minuto de um vídeo, o comprimento do experimento deverá ser pelo menos duplo.

## <a name="next-steps"></a>Passos seguintes

* [Aprendizado de reforço](concepts-reinforcement-learning.md) 
* [Experimente a API de classificação](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank/console)
* [Experimente a API de recompensa](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward)
