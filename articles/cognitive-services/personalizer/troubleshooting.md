---
title: Resolução de problemas - Personalizante
description: Este artigo contém respostas para perguntas frequentes sobre o Personalizador.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: troubleshooting
ms.date: 02/26/2020
ms.openlocfilehash: b5f7ed1f2ded8f6ec0320d417b59bab016d75028
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91777280"
---
# <a name="personalizer-troubleshooting"></a>Resolução de problemas personalizada

Este artigo contém respostas para perguntas frequentes sobre o Personalizador.

## <a name="configuration-issues"></a>Problemas de configuração

### <a name="i-changed-a-configuration-setting-and-now-my-loop-isnt-performing-at-the-same-learning-level-what-happened"></a>Mudei uma configuração e agora o meu ciclo não está a funcionar ao mesmo nível de aprendizagem. O que aconteceu?

Algumas definições de configuração [reiniciam o seu modelo](how-to-settings.md#settings-that-include-resetting-the-model). As alterações de configuração devem ser cuidadosamente planeadas.

### <a name="when-configuring-personalizer-with-the-api-i-received-an-error-what-happened"></a>Ao configurar o Personaler com a API, recebi um erro. O que aconteceu?

Se utilizar um único pedido de API para configurar o seu serviço e alterar o seu comportamento de aprendizagem, terá um erro. Você precisa fazer duas chamadas API separadas: primeiro, para configurar o seu serviço, em seguida, para mudar o comportamento de aprendizagem.

## <a name="transaction-errors"></a>Erros de transação

### <a name="i-get-an-http-429-too-many-requests-response-from-the-service-what-can-i-do"></a>Recebo uma resposta HTTP 429 (Demasiados pedidos) do serviço. Que posso eu fazer?

Se escolheu um nível de preço gratuito quando criou a instância Personalizadora, existe um limite de quota no número de pedidos de Rank que são permitidos. Reveja a sua taxa de chamada API para o Rank API (no painel Métricas no portal Azure para o seu recurso Personalizer) e ajuste o nível de preços (no painel de preços) se o seu volume de chamadas for aumentado para além do limiar para o nível de preços escolhido.

### <a name="im-getting-a-5xx-error-on-rank-or-reward-apis-what-should-i-do"></a>Estou a receber um erro de 5xx nas APIs rank ou reward. O que devo fazer?

Estas questões devem ser transparentes. Se continuarem, contacte o suporte selecionando **novo pedido de suporte** na secção Support + **Troubleshooting,** no portal Azure para o seu recurso Personalizer.

## <a name="learning-loop"></a>Ciclo de aprendizagem

### <a name="the-learning-loop-doesnt-attain-a-100-match-to-the-system-without-personalizer-how-do-i-fix-this"></a>O ciclo de aprendizagem não alcança uma correspondência de 100% com o sistema sem o Personalizer. Como é que eu arranjo isto?

As razões pelas quais não alcança o seu objetivo com o ciclo de aprendizagem:
* Não há funcionalidades suficientes enviadas com chamada de API de grau
* Bugs nas funcionalidades enviadas - tais como o envio de dados de recursos não agregados, tais como timetamps para Rank API
* Bugs com processamento de loop - como não enviar dados de recompensa para Recompensar API para eventos

Para corrigir, é necessário alterar o processamento alterando as funcionalidades enviadas para o loop ou certifique-se de que a recompensa é uma avaliação correta da qualidade da resposta do Rank.

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>O ciclo de aprendizagem não parece aprender. Como é que eu arranjo isto?

O ciclo de aprendizagem precisa de alguns milhares de chamadas de Recompensa antes que rank chame priorize eficazmente.

Se não tem a certeza de como o seu ciclo de aprendizagem se está a comportar, faça uma [avaliação offline](concepts-offline-evaluation.md)e aplique a política de aprendizagem corrigida.

### <a name="i-keep-getting-rank-results-with-all-the-same-probabilities-for-all-items-how-do-i-know-personalizer-is-learning"></a>Estou sempre a receber resultados com todas as mesmas probabilidades para todos os itens. Como sei que o Personalizer está a aprender?

O personalização devolve as mesmas probabilidades num resultado de API de classificação quando acaba de começar e tem um modelo _vazio,_ ou quando reinicia o Circuito Personalizado, e o seu modelo ainda está dentro do período **de frequência de atualização do Modelo.**

Quando o novo período de atualização começar, o modelo atualizado é utilizado e verá as probabilidades mudarem.

### <a name="the-learning-loop-was-learning-but-seems-to-not-learn-anymore-and-the-quality-of-the-rank-results-isnt-that-good-what-should-i-do"></a>O ciclo de aprendizagem foi aprender, mas parece não aprender mais, e a qualidade dos resultados do Rank não é assim tão boa. O que devo fazer?

* Certifique-se de que completou e aplicou uma avaliação no portal Azure para esse recurso Personalizer (ciclo de aprendizagem).
* Certifique-se de que todas as recompensas são enviadas, através da API reward, e processadas.

### <a name="how-do-i-know-that-the-learning-loop-is-getting-updated-regularly-and-is-used-to-score-my-data"></a>Como é que eu sei que o ciclo de aprendizagem está a ser atualizado regularmente e é usado para obter os meus dados?

Pode encontrar a hora em que o modelo foi atualizado pela última vez na página **De Modelos e Definições** de Aprendizagem do portal Azure. Se vires uma velha hora, é provável que não estejas a enviar as chamadas Rank and Reward. Se o serviço não tiver dados de entrada, não atualiza a aprendizagem. Se vir que o ciclo de aprendizagem não está a atualizar com frequência suficiente, pode editar a frequência de **Atualização** do Modelo do loop .

## <a name="offline-evaluations"></a>Avaliações offline

### <a name="an-offline-evaluations-feature-importance-returns-a-long-list-with-hundreds-or-thousands-of-items-what-happened"></a>A importância de uma avaliação offline devolve uma longa lista com centenas ou milhares de itens. O que aconteceu?

Isto é normalmente devido a tempotampeds, IDs do utilizador ou algumas outras funcionalidades de grão fino enviadas.

### <a name="i-created-an-offline-evaluation-and-it-succeeded-almost-instantly-why-is-that-i-dont-see-any-results"></a>Criei uma avaliação offline e conseguiu quase instantaneamente. Porquê? Não vejo resultados?

A avaliação offline utiliza os dados do modelo treinado dos eventos nesse período de tempo. Se não tiver enviado quaisquer dados no período de tempo entre o início e o fim da avaliação, completará sem quaisquer resultados. Submeta uma nova avaliação offline selecionando um intervalo de tempo com eventos que sabe terem sido enviados para Personalizer.

## <a name="learning-policy"></a>Política de aprendizagem

### <a name="how-do-i-import-a-learning-policy"></a>Como importa uma política de aprendizagem?

Saiba mais sobre [conceitos de política de aprendizagem](concept-active-learning.md#understand-learning-policy-settings) e [como aplicar](how-to-manage-model.md) uma nova política de aprendizagem. Se não quiser selecionar uma política de aprendizagem, pode utilizar a [avaliação offline](how-to-offline-evaluation.md) para sugerir uma política de aprendizagem, com base nos seus eventos atuais.


## <a name="security"></a>Segurança

### <a name="the-api-key-for-my-loop-has-been-compromised-what-can-i-do"></a>A chave da API para o meu ciclo foi comprometida. Que posso eu fazer?

Pode regenerar uma chave depois de trocar os seus clientes para usar a outra chave. Ter duas teclas permite-lhe propagar a chave de forma preguiçosa sem ter de ter algum tempo de descanso. Recomendamos fazê-lo num ciclo regular como medida de segurança.


## <a name="next-steps"></a>Passos seguintes

[Configure a frequência de atualização do modelo](how-to-settings.md#model-update-frequency)