---
title: Integrar com outras aplicações - QnA Maker
description: A QnA Maker integra-se em aplicações de clientes, como bots de chat, bem como com outros serviços de processamento de linguagem natural, como o Language Understanding (LUIS).
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: c1edbfb6badfb73ce08a99709da0f8bfb61b7dc3
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804193"
---
# <a name="design-knowledge-base-for-client-applications"></a>Base de conhecimento de design para aplicações de clientes

A QnA Maker integra-se em aplicações de clientes, como bots de chat, bem como com outros serviços de processamento de linguagem natural, como o Language Understanding (LUIS).

## <a name="integration-with-a-conversational-client"></a>Integração com um cliente conversacional

A QnA Maker integra-se com aplicações de clientes de conversação, como [o Microsoft Bot Framework](https://dev.botframework.com/). O texto enviado ao QnA Maker não precisa de ser limpo ou transformado. O QnA Maker aceita línguas naturais e devolve a melhor resposta.

## <a name="create-a-bot-without-writing-any-code"></a>Criar um bot sem escrever qualquer código

Depois de publicar a sua base de conhecimentos, crie um bot a partir da página **Publicar,** selecionando o bot **Create.** Utilize o tutorial de [bot](../Quickstarts/create-publish-knowledge-base.md) para saber o que acontece depois de selecionar o botão.

## <a name="providing-multi-turn-conversations"></a>Proporcionando conversas multi-turn

Um cliente bot fornece a melhor resposta selecionada a partir da sua base de conhecimento, e pode fornecer instruções de seguimento se a resposta fizer parte de um par QnA multi-turn. Aprenda [a](../how-to/multiturn-conversation.md) adicionar conjuntos de perguntas e respostas de conversação multi-turn à sua base de conhecimento.

## <a name="natural-language-processing"></a>Processamento de linguagem natural

Enquanto o QnA Maker processa questões que usam o processamento de linguagem natural, também pode ser usado uma parte de um sistema maior que responde a perguntas de múltiplas bases de conhecimento. Você pode combinar QnA Maker com outro Serviço Cognitivo, Compreensão da Linguagem (LUIS), para fornecer processamento de linguagem natural antes de chegar a uma base de conhecimento específica. Saiba mais sobre quando e como usar o LUIS e o [QnA Maker](../../luis/choose-natural-language-processing-service.md?toc=/azure/cognitive-services/qnamaker/toc.json) juntos.

## <a name="next-steps"></a>Passos seguintes

Aprenda [conceitos](development-lifecycle-knowledge-base.md) de ciclo de desenvolvimento para o QnA Maker.