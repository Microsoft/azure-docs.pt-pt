---
title: Resposta precisa usando a deteção de extensão de resposta - QnA Maker
description: Compreenda a funcionalidade de resposta precisa disponível na QnA Maker gerida.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/09/2020
ms.openlocfilehash: 6c597e8fd48eb157b69c918ae5fd5f699611ad75
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101703410"
---
# <a name="precise-answering"></a>Respostas precisas

A funcionalidade de resposta precisa introduzida no QnA Maker gerido (Preview), permite-lhe obter a resposta curta precisa a partir da passagem de resposta do melhor candidato presente na base de conhecimento para qualquer consulta do utilizador. Esta funcionalidade utiliza um modelo de aprendizagem profunda que, em tempo de execução, compreende a intenção da consulta do utilizador e deteta a resposta curta precisa a partir da passagem de resposta, se houver uma resposta curta presente como facto na passagem da resposta. 

Esta funcionalidade encontra-se por padrão no painel de teste, para que possa testar a funcionalidade específica do seu cenário. Esta funcionalidade é extremamente benéfica tanto para os desenvolvedores de conteúdos como para os utilizadores finais. Agora, os desenvolvedores de conteúdo não precisam de curadoria manualmente de pares QnA específicos para todos os factos presentes na base de conhecimento, e o utilizador final não precisa de olhar através de toda a passagem de resposta devolvida do serviço para encontrar o facto real de que responde à consulta do utilizador. 

## <a name="precise-answering-on-qna-maker-portal"></a>Resposta precisa no portal QnA Maker

No portal QnA Maker, quando abrir o painel de teste, verá uma opção para **exibir uma resposta curta** na parte superior. Esta opção será selecionada por padrão. Quando introduzir uma consulta no painel de teste, verá uma resposta curta juntamente com a passagem de resposta, se houver uma resposta curta presente na passagem de resposta. 
 
![Painel de teste gerido](../QnAMaker/media/conversational-context/test-pane-with-managed.png)

Pode desmarcar a opção **de resposta curta do Visor,** se quiser ver apenas a passagem de resposta no painel de teste. 

O serviço também devolve a pontuação de confiança da resposta exata como uma **pontuação de intervalo de resposta** que pode verificar selecionando a opção **Inspect** presente logo abaixo da consulta no painel de teste.

![Pontuação de intervalo de resposta gerida](../QnAMaker/media/conversational-context/managed-answer-span-score.png)

## <a name="publishing-a-qna-maker-bot"></a>Publicar um bot QnA Maker

Ao publicar um bot, obtém a experiência com a resposta precisa por padrão na sua aplicação, onde verá uma resposta curta juntamente com a passagem de resposta. Consulte a referência API para [Gerar Resposta](/rest/api/cognitiveservices/qnamakerv5.0-preview.1/knowledgebase/generateanswer#answerspan) para ver como usar a resposta precisa (chamada AnswerSpan) na resposta. O utilizador tem a flexibilidade de escolher outras experiências atualizando o modelo através do serviço de aplicações Bot. 

## <a name="language-support"></a>Suporte de idiomas

Atualmente, a funcionalidade de resposta precisa é suportada apenas para inglês.