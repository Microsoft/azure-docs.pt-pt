---
title: Integrar-se com outras aplicações - QnA Maker
description: O QnA Maker integra-se em aplicações de clientes como bots de chat, bem como em outros serviços de processamento de linguagem natural, como o Language Understanding (LUIS).
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: ff5e64dd31542b1e31df6d9ca709567b322995ce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776532"
---
# <a name="design-knowledge-base-for-client-applications"></a>Base de conhecimento de design para aplicações de clientes

O QnA Maker integra-se em aplicações de clientes como bots de chat, bem como em outros serviços de processamento de linguagem natural, como o Language Understanding (LUIS).

## <a name="integration-with-a-conversational-client"></a>Integração com um cliente de conversação

O QnA Maker integra-se com aplicações de clientes conversais como [o Microsoft Bot Framework](https://dev.botframework.com/). O texto enviado à QnA Maker não precisa de ser limpo ou transformado. O QnA Maker aceita línguas naturais e devolve a melhor resposta.

## <a name="create-a-bot-without-writing-any-code"></a>Criar um bot sem escrever nenhum código

Depois de publicar a sua base de conhecimento, crie um bot a partir da página **Publicar,** selecionando o bot **Create.** Utilize o [tutorial de bot](../Quickstarts/create-publish-knowledge-base.md) para saber o que acontece depois de selecionar o botão.

## <a name="providing-multi-turn-conversations"></a>Proporcionando conversas multi-voltas

Um cliente bot fornece a melhor resposta selecionada a partir da sua base de conhecimento, e pode fornecer indicações de seguimento se a resposta for parte de um par QnA multi-turn. Saiba [como](../how-to/multiturn-conversation.md) adicionar perguntas de conversação multi-voltas e conjuntos de resposta à sua base de conhecimento.

## <a name="natural-language-processing"></a>Processamento de linguagem natural

Enquanto o QnA Maker processa questões que usam o processamento de linguagem natural, também pode ser usado uma parte de um sistema maior que responde a perguntas de várias bases de conhecimento. Você pode combinar QnA Maker com outro Serviço Cognitivo, Compreensão de Linguagem (LUIS), para fornecer processamento de linguagem natural antes de chegar a uma base de conhecimento específica. Saiba mais sobre quando e como usar [luis e QnA Maker](../../luis/choose-natural-language-processing-service.md?toc=/azure/cognitive-services/qnamaker/toc.json) juntos.

## <a name="next-steps"></a>Passos seguintes

Aprenda [conceitos de](development-lifecycle-knowledge-base.md) ciclo de desenvolvimento para o QnA Maker.