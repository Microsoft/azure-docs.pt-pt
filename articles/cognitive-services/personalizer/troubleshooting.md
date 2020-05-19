---
title: Resolução de problemas - Personalizer
description: Este artigo contém respostas para perguntas frequentes sobre o Personalizer.
ms.topic: troubleshooting
ms.date: 02/26/2020
ms.author: diberry
ms.openlocfilehash: ca19fbfc505e3e46338a0930773b1879dce788c1
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83586257"
---
# <a name="personalizer-troubleshooting"></a>Resolução de problemas personalizador

Este artigo contém respostas para perguntas frequentes sobre o Personalizer.

## <a name="configuration-issues"></a>Problemas de configuração

### <a name="i-changed-a-configuration-setting-and-now-my-loop-isnt-performing-at-the-same-learning-level-what-happened"></a>Mudei uma configuração e agora o meu loop não está a atuar ao mesmo nível de aprendizagem. O que aconteceu?

Algumas definições de configuração [redefinir o seu modelo](how-to-settings.md#settings-that-include-resetting-the-model). As alterações de configuração devem ser cuidadosamente planeadas.

### <a name="when-configuring-personalizer-with-the-api-i-received-an-error-what-happened"></a>Ao configurar o Personalizer com a API, recebi um erro. O que aconteceu?

Se utilizar um único pedido de API para configurar o seu serviço e alterar o seu comportamento de aprendizagem, terá um erro. Precisa de fazer duas chamadas API separadas: primeiro, configurar o seu serviço e, em seguida, mudar o comportamento de aprendizagem.

## <a name="transaction-errors"></a>Erros de transação

### <a name="i-get-an-http-429-too-many-requests-response-from-the-service-what-can-i-do"></a>Recebo uma resposta HTTP 429 (muitos pedidos) do serviço. Que posso eu fazer?

Se escolheu um nível de preço gratuito quando criou a instância Personalizer, existe um limite de quota no número de pedidos de Rank que são permitidos. Reveja a sua taxa de chamada API para o Rank API (no painel de métricas no portal Azure para o seu recurso Personalizer) e ajuste o nível de preços (no painel de nível de preços) se se esperar que o seu volume de chamadas aumente para além do limiar para o nível de preços escolhido.

### <a name="im-getting-a-5xx-error-on-rank-or-reward-apis-what-should-i-do"></a>Estou a ter um erro de 5xx no Rank ou na Reward APIs. O que devo fazer?

Estas questões devem ser transparentes. Se continuarem, contacte o suporte selecionando **Novo pedido** de suporte na secção Suporte + resolução de **problemas,** no portal Azure para o seu recurso Personalizer.

## <a name="learning-loop"></a>Ciclo de aprendizagem

### <a name="the-learning-loop-doesnt-attain-a-100-match-to-the-system-without-personalizer-how-do-i-fix-this"></a>O ciclo de aprendizagem não corresponde a 100% do sistema sem o Personalizer. Como posso resolver isto?

As razões pelas quais não alcança o seu objetivo com o ciclo de aprendizagem:
* Não há funcionalidades suficientes enviadas com chamada API rank
* Bugs nas funcionalidades enviadas - tais como o envio de dados de funcionalidades não agregados, tais como selos de tempo para rank API
* Bugs com processamento de loop - como não enviar dados de recompensa para Reward API para eventos

Para corrigir, é necessário alterar o processamento alterando as funcionalidades enviadas para o loop, ou certificar-se de que a recompensa é uma avaliação correta da qualidade da resposta do Rank.

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>O ciclo de aprendizagem não parece aprender. Como posso resolver isto?

O ciclo de aprendizagem precisa de alguns milhares de chamadas de Recompensa antes que as chamadas de Rank priorizem eficazmente.

Se não tem a certeza de como o seu ciclo de aprendizagem se está a comportar, faça uma [avaliação offline](concepts-offline-evaluation.md)e aplique a política de aprendizagem corrigida.

### <a name="i-keep-getting-rank-results-with-all-the-same-probabilities-for-all-items-how-do-i-know-personalizer-is-learning"></a>Continuo a obter resultados de classificação com todas as mesmas probabilidades para todos os itens. Como sei que o Personalizer está a aprender?

O Personalizer devolve as mesmas probabilidades num resultado da Rank API quando acaba de começar e tem um modelo _vazio,_ ou quando reinicia o Personalizer Loop, e o seu modelo ainda se encontra dentro do período de frequência de atualização do **Modelo.**

Quando o novo período de atualização começar, o modelo atualizado é utilizado e verá que as probabilidades mudam.

### <a name="the-learning-loop-was-learning-but-seems-to-not-learn-anymore-and-the-quality-of-the-rank-results-isnt-that-good-what-should-i-do"></a>O ciclo de aprendizagem foi aprendendo, mas parece não aprender mais, e a qualidade dos resultados do Rank não é tão boa. O que devo fazer?

* Certifique-se de que concluiu e aplicou uma avaliação no portal Azure para esse recurso Personalizer (ciclo de aprendizagem).
* Certifique-se de que todas as recompensas são enviadas, através da API recompensa, e processadas.

### <a name="how-do-i-know-that-the-learning-loop-is-getting-updated-regularly-and-is-used-to-score-my-data"></a>Como sei que o ciclo de aprendizagem está a ser atualizado regularmente e é usado para marcar os meus dados?

Pode encontrar a hora em que o modelo foi atualizado pela última vez na página de Definições de **Modelo e Aprendizagem** do portal Azure. Se vir um carimbo de tempo antigo, é provável que não esteja a enviar as chamadas de Rank and Reward. Se o serviço não tiver dados de entrada, não atualiza a aprendizagem. Se vir que o ciclo de aprendizagem não está a ser atualizado com frequência, pode editar a frequência de **Atualização**do Modelo do loop .

## <a name="offline-evaluations"></a>Avaliações offline

### <a name="an-offline-evaluations-feature-importance-returns-a-long-list-with-hundreds-or-thousands-of-items-what-happened"></a>A importância da funcionalidade de uma avaliação offline devolve uma longa lista com centenas ou milhares de itens. O que aconteceu?

Isto deve-se tipicamente a carimbos de tempo, iDs de utilizador ou algumas outras funcionalidades de grãos finos enviadas.

### <a name="i-created-an-offline-evaluation-and-it-succeeded-almost-instantly-why-is-that-i-dont-see-any-results"></a>Criei uma avaliação offline e conseguiquase instantaneamente. Porquê? Não vejo resultados?

A avaliação offline utiliza os dados do modelo treinados dos eventos nesse período de tempo. Se não tiver enviado quaisquer dados no período de tempo entre o início e o fim da avaliação, irá completar sem quaisquer resultados. Envie uma nova avaliação offline selecionando um intervalo de tempo com eventos que sabe terem sido enviados para personalizer.

## <a name="learning-policy"></a>Política de aprendizagem

### <a name="how-do-i-import-a-learning-policy"></a>Como posso importar uma política de aprendizagem?

Saiba mais sobre conceitos de política de [aprendizagem](concept-active-learning.md#understand-learning-policy-settings) e [como aplicar](how-to-manage-model.md) uma nova política de aprendizagem. Se não quiser selecionar uma política de aprendizagem, pode usar a [avaliação offline](how-to-offline-evaluation.md) para sugerir uma política de aprendizagem, com base nos seus eventos atuais.


## <a name="security"></a>Segurança

### <a name="the-api-key-for-my-loop-has-been-compromised-what-can-i-do"></a>A chave API para o meu loop foi comprometida. Que posso eu fazer?

Pode regenerar uma chave depois de trocar os seus clientes para usar a outra tecla. Ter duas chaves permite-lhe propagar a chave de forma preguiçosa sem ter de ter tempo de descanso. Recomendamos que o faça num ciclo regular como medida de segurança.


## <a name="next-steps"></a>Passos seguintes

[Configure a frequência de atualização do modelo](how-to-settings.md#model-update-frequency)