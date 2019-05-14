---
title: Pontuação de recompensa - Personalizer
titleSuffix: Azure Cognitive Services
description: A pontuação de recompensa indica a forma como a personalização escolha, RewardActionID, resultada para o utilizador. O valor da pontuação recompensa é determinado pela sua lógica de negócio, com base nas observações do comportamento do usuário. Personalizer prepara de modelos de machine learning ao avaliar as recompensas.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/13/2019
ms.author: edjez
ms.openlocfilehash: 536aad0fac4e833cd9a30bad2cfd10e25b0f1300
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/14/2019
ms.locfileid: "65607078"
---
# <a name="reward-scores-indicate-success-of-personalization"></a>Pontuações de recompensa indicam sucesso de personalização

A pontuação de recompensa indica a forma como a escolha de personalização, [RewardActionID](https://docs.microsoft.com/rest/api/cognitiveservices/personalizer/events/rank#rankresponse), resultou para o utilizador. O valor da pontuação recompensa é determinado pela sua lógica de negócio, com base nas observações do comportamento do usuário.

Personalizer prepara seus modelos de machine learning ao avaliar as recompensas. 

## <a name="use-reward-api-to-send-reward-score-to-personalizer"></a>Utilizar a API de recompensa para enviar a pontuação de recompensa para Personalizer

Remunerações são enviadas para Personalizer pela [recompensa API](https://docs.microsoft.com/rest/api/cognitiveservices/personalizer/events/reward). Uma recompensa é um número entre -1 e 1. Personalizer prepara o modelo para alcançar a soma de possíveis mais elevada de remunerações ao longo do tempo.

Remunerações são enviadas após o comportamento do utilizador tiver ocorrido, o que poderia ser dias mais tarde. A quantidade máxima de tempo Personalizer irão esperar que um evento é considerado como não tendo nenhuma remuneração ou uma recompensa de predefinição está configurada com o [tempo de espera de recompensa](#reward-wait-time) no portal do Azure.

Se a pontuação de recompensa para um evento ainda não foi recebida dentro do **tempo de espera de recompensa**, em seguida, o **recompensa predefinido** serão aplicadas. Normalmente, o **[recompensa predefinido](how-to-settings.md#configure-reward-settings-for-the-feedback-loop-based-on-use-case)** está configurado para ser zero.

## <a name="composing-reward-scores"></a>Compor pontuações de recompensa

Uma pontuação de recompensa tem de ser computada em sua lógica de negócio. A classificação pode ser representada como:

* Um único número enviado uma vez 
* Uma pontuação enviada imediatamente (por exemplo, 0,8) e uma pontuação adicional enviados mais tarde (normalmente 0,2).

## <a name="default-rewards"></a>Remunerações predefinido

Se não for recebida nenhuma remuneração dentro a [tempo de espera de recompensa](#reward-wait-time), a duração desde a classificação chamar, Personalizer implicitamente se aplica a **recompensa predefinido** para esse evento de classificação.

## <a name="building-up-rewards-with-multiple-factors"></a>Criação de remunerações com múltiplos fatores  

Para personalização em vigor, pode criar cópia de segurança a pontuação de recompensa (qualquer número entre -1 e 1) com base em vários fatores. 

Por exemplo, pode aplicar estas regras para personalizar uma lista de conteúdo de vídeo:

|Comportamento do utilizador|Parcial valor de pontuação|
|--|--|
|O usuário clicou no item superior.|+0.5 recompensa|
|O usuário abrir o conteúdo real desse item.|+0.3 recompensa|
|O utilizador assistiu 5 minutos do conteúdo ou 30%, o que for mais longo.|+0.2 recompensa|
|||

Em seguida, pode enviar a recompensa total para a API.

## <a name="calling-the-reward-api-multiple-times"></a>Chamar a API de recompensa várias vezes

Também pode chamar a API de recompensa com o mesmo ID de evento, enviar as pontuações de recompensa diferentes. Quando Personalizer obtém esses remunerações, determina a recompensa final para esse evento ao agregá-los conforme especificado nas definições de Personalizer.

Definições de agregação:

*  **Primeira**: Usa a pontuação de recompensa primeiro recebida para o evento e descarta o resto.
* **Soma**: Leva todas as pontuações de recompensa recolhidas para o eventId e adiciona-as em conjunto.

Todas as remunerações para um evento, que são recebidos depois do **tempo de espera de recompensa**, são eliminadas e não afetam a formação de modelos.

Adicionando segurança pontuações de recompensa, sua recompensa final pode ser inferior a -1 ou superior a 1. Isso não fazer com o serviço falhar.

<!--
@edjez - is the number ignored if it is outside the acceptable range?
-->

## <a name="best-practices-for-calculating-reward-score"></a>Melhores práticas para calcular a pontuação de recompensa

* **Considere o verdadeiros indicadores de personalização concluída com êxito**: É fácil pensar em termos de cliques, mas uma boa recompensa baseia-se o que deseja que os utilizadores *alcançar* em vez do que deseja que as pessoas *fazer*.  Por exemplo, recompensar a cliques pode levar a selecionar o conteúdo que é clickbait propenso a.

* **Utilize uma recompensa de pontuação para quão bom personalização trabalhou**: Personalizar uma sugestão de filme resultaria Espero que o utilizador observando o filme e dar a ele uma classificação alta. Uma vez que a classificação do filme depende provavelmente muitas coisas (a qualidade de atuar, o comportamento do utilizador), não é um sinal de boa recompensa para saber como bem *a personalização* funcionou. O utilizador observando os primeiros minutos do filme, no entanto, pode ser um sinal melhor de eficácia de personalização e enviar uma recompensa de 1 após 5 minutos serão um sinal de melhor.

* **Remunerações só se aplicam a RewardActionID**: Personalizer aplica-se as recompensas para compreender a eficácia da ação especificada na RewardActionID. Se optar por exibir outras ações e o usuário clica neles, a recompensa deve ser zero.

* **Considere conseqüências indesejadas**: Criar funções de recompensa que levam a responsáveis resultados com [ética e de utilização responsável](ethics-responsible-use.md).

* **Utilizar remunerações Incremental**: Adição de remunerações parciais para comportamentos de utilizador mais pequenos ajuda Personalizer para alcançar melhor remunerações. Este recompensa incremental permite que o algoritmo de saber o que está ficando mais de perto para envolver o usuário o comportamento desejado final.
    * Se estiver a ser mostrada uma lista de filmes, se o usuário focalizar aquele primeiro por um tempo ver mais informações, pode determinar o que aconteceu algum envolvimento do utilizador. O comportamento pode contar com uma classificação de recompensa de 0,1. 
    * Se o utilizador abrir a página e, em seguida, encerrado, a pontuação de recompensa pode ser 0,2. 

## <a name="reward-wait-time"></a>Tempo de espera de recompensa

Personalizer irá a correlacionar as informações de uma classificação chamar com as recompensas enviadas em chamadas de recompensa para preparar o modelo. Estes podem ser em momentos diferentes. Personalizer aguarda por um período limitado, começando quando ocorreu a chamada de classificação, mesmo que a chamada de classificação foi efetuada como um evento inativo e ativada mais tarde.

Se o **tempo de espera de recompensa** expirar e não houve nenhuma informação de recompensa, uma recompensa predefinida é aplicada a esse evento para treinamento. A duração de espera máximo é de 6 dias.

## <a name="best-practices-for-setting-reward-wait-time"></a>Melhores práticas para a definição de recompensa de tempo de espera

Siga estas recomendações para obter melhores resultados.

* Tornar a recompensa de tempo de espera o mais curto possível, deixando-o tempo suficiente para obter comentários dos utilizadores. 

<!--@Edjez - storage quota? -->

* Não escolha um período em que é menor do que o tempo necessário para obter comentários. Por exemplo, se algumas das suas recompensas são fornecidos em depois de um utilizador já viu 1 minuto de um vídeo, o comprimento de experimentação deve ter, pelo menos, double que.

## <a name="next-steps"></a>Passos Seguintes

* [Aprendizagem por reforço](concepts-reinforcement-learning.md) 
* [Experimente a API de classificação](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank/console)
* [Experimente a API de recompensa](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward)
