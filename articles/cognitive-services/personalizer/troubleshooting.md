---
title: Solução de problemas-personalizador
titleSuffix: Azure Cognitive Services
description: Este artigo contém respostas para perguntas frequentes sobre solução de problemas sobre o personalizador.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: diberry
ms.openlocfilehash: 5aeda9abcebda50cf97e1473b458d8f1f9d15970
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75832166"
---
# <a name="personalizer-troubleshooting"></a>Solução de problemas do personalizador

Este artigo contém respostas para perguntas frequentes sobre solução de problemas sobre o personalizador.

## <a name="transaction-errors"></a>Erros de transação

### <a name="i-get-an-http-429-too-many-requests-response-from-the-service-what-can-i-do"></a>Recebo uma resposta HTTP 429 (número excessivo de solicitações) do serviço. O que posso fazer?

Se você escolheu uma camada de preço livre quando criou a instância de personalizador, há um limite de cota no número de solicitações de classificação permitidas. Examine sua taxa de chamada de API para a API de classificação (no painel métricas no portal do Azure para o recurso personalizado) e ajuste o tipo de preço (no painel tipo de preço) se o volume de chamada deve aumentar além do limite para o tipo de preço escolhido.

### <a name="im-getting-a-5xx-error-on-rank-or-reward-apis-what-should-i-do"></a>Estou recebendo um erro 5xx em APIs de classificação ou recompensa. O que devo fazer?

Esses problemas devem ser transparentes. Se eles continuarem, entre em contato com o suporte selecionando **nova solicitação de suporte** na seção **suporte + solução de problemas** , na portal do Azure para o recurso personalizador.


## <a name="learning-loop"></a>Loop de aprendizagem

<!--

### How do I import a learning policy?


-->

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>O loop de aprendizagem parece não aprender. Como fazer corrigir isso?

O loop de aprendizagem precisa de alguns mil pontos de recompensa para que as chamadas de classificação sejam priorizadas com eficiência.

Se você não tiver certeza sobre como seu loop de aprendizado está sendo usado no momento, execute uma [avaliação offline](concepts-offline-evaluation.md)e aplique a política de aprendizado corrigida.

### <a name="i-keep-getting-rank-results-with-all-the-same-probabilities-for-all-items-how-do-i-know-personalizer-is-learning"></a>Continuo obtendo resultados de classificação com todas as mesmas probabilidades para todos os itens. Como fazer sabe que o personalizador está aprendendo?

O personalizador retorna as mesmas probabilidades em um resultado de API de classificação quando ele acabou de ser iniciado e tem um modelo _vazio_ , ou quando você redefine o loop personalizador, e seu modelo ainda está dentro do período de **frequência de atualização do modelo** .

Quando o novo período de atualização começar, o modelo atualizado será usado e você verá as probabilidades de alteração.

### <a name="the-learning-loop-was-learning-but-seems-to-not-learn-anymore-and-the-quality-of-the-rank-results-isnt-that-good-what-should-i-do"></a>O loop de aprendizagem estava aprendendo, mas parece não saber mais, e a qualidade dos resultados da classificação não é boa. O que devo fazer?

* Verifique se você concluiu e aplicou uma avaliação no portal do Azure para esse recurso personalizado (loop de aprendizagem).
* Verifique se todas as recompensas foram enviadas, por meio da API de recompensa e processadas.

### <a name="how-do-i-know-that-the-learning-loop-is-getting-updated-regularly-and-is-used-to-score-my-data"></a>Como fazer saber que o loop de aprendizagem está sendo atualizado regularmente e é usado para pontuar meus dados?

Você pode encontrar a hora em que o modelo foi atualizado pela última vez na página de **configurações modelo e aprendizado** da portal do Azure. Se você vir um carimbo de data/hora antigo, é provável que você não esteja enviando as chamadas de classificação e recompensa. Se o serviço não tiver dados de entrada, ele não atualizará o aprendizado. Se você vir que o loop de aprendizagem não está atualizando com frequência suficiente, você poderá editar a **frequência de atualização do modelo**do loop.


## <a name="offline-evaluations"></a>Avaliações offline

### <a name="an-offline-evaluations-feature-importance-returns-a-long-list-with-hundreds-or-thousands-of-items-what-happened"></a>A importância de um recurso de uma avaliação offline retorna uma longa lista com centenas ou milhares de itens. O que aconteceu?

Isso normalmente ocorre devido a carimbos de data/hora, IDs de usuário ou alguns outros recursos refinados enviados no.

### <a name="i-created-an-offline-evaluation-and-it-succeeded-almost-instantly-why-is-that-i-dont-see-any-results"></a>Criei uma avaliação offline e ela foi bem-sucedida quase instantaneamente. Porquê? Não vejo nenhum resultado?

A avaliação offline usa os dados de modelo treinados dos eventos nesse período de tempo. Se você não enviou dados no período de tempo entre a hora de início e de término da avaliação, ele será concluído sem nenhum resultado. Envie uma nova avaliação offline selecionando um intervalo de tempo com eventos que você conhece que foram enviados ao personalizador.

## <a name="learning-policy"></a>Política de aprendizagem

### <a name="how-do-i-import-a-learning-policy"></a>Como fazer importar uma política de aprendizado?

Saiba mais sobre [conceitos de política de aprendizado](concept-active-learning.md#understand-learning-policy-settings) e [como aplicar](how-to-learning-policy.md) uma nova política de aprendizado. Se você não quiser selecionar uma política de aprendizado, poderá usar a [avaliação offline](how-to-offline-evaluation.md) para sugerir uma política de aprendizado com base em seus eventos atuais.


## <a name="security"></a>Segurança

### <a name="the-api-key-for-my-loop-has-been-compromised-what-can-i-do"></a>A chave de API para meu loop foi comprometida. O que posso fazer?

Você pode regenerar uma chave depois de trocar seus clientes para usar a outra chave. Ter duas chaves permite propagar a chave de maneira lenta sem ter que ter qualquer tempo de inatividade. É recomendável fazer isso em um ciclo regular como uma medida de segurança.


## <a name="next-steps"></a>Passos seguintes

[Configurar a frequência de atualização do modelo](how-to-settings.md#model-update-frequency)