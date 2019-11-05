---
title: Examinar usuário expressão-LUIS
titleSuffix: Azure Cognitive Services
description: Com o Active Learning, seu ponto de extremidade de revisão declarações para a intenção e a entidade corretas. LUIS escolhe ponto de extremidade declarações não tem certeza de.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: diberry
ms.openlocfilehash: 772d2bdff0a5e1c6cd999482ec4ef6be29dc2bb1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73486760"
---
# <a name="concepts-for-enabling-active-learning-by-reviewing-endpoint-utterances"></a>Conceitos para habilitar o aprendizado ativo examinando o ponto de extremidade declarações
O aprendizado ativo é uma das três estratégias para melhorar a precisão da previsão e a mais fácil de implementar. Com o Active Learning, seu ponto de extremidade de revisão declarações para a intenção e a entidade corretas. LUIS escolhe ponto de extremidade declarações não tem certeza de.

## <a name="what-is-active-learning"></a>O que é o aprendizado ativo
O aprendizado ativo é um processo de duas etapas. Primeiro, LUIS seleciona declarações que recebe no ponto de extremidade do aplicativo que precisa de validação. A segunda etapa é executada pelo proprietário ou colaborador do aplicativo para validar o declarações selecionado para [revisão](luis-how-to-review-endpoint-utterances.md), incluindo a intenção correta e todas as entidades dentro da intenção. Depois de revisar o declarações, treine e publique o aplicativo novamente. 

## <a name="which-utterances-are-on-the-review-list"></a>Quais declarações estão na lista de revisão
LUIS adiciona declarações à lista de revisão quando a principal tentativa de acionamento tem uma pontuação baixa ou as duas pontuações principais não são muito próximas. 

## <a name="single-pool-for-utterances-per-app"></a>Pool único para declarações por aplicativo
A lista **revisar declarações do ponto de extremidade** não é alterada com base na versão. Há um único conjunto de expressões para rever, independentemente da versão da expressão que está ativamente a editar ou da versão da aplicação que foi publicada no ponto final. 

## <a name="where-are-the-utterances-from"></a>Onde estão os declarações de
Os declarações do ponto de extremidade são obtidos de consultas do usuário final no ponto de extremidade HTTP do aplicativo. Se seu aplicativo não for publicado ou ainda não tiver recebido ocorrências, você não terá nenhum declarações para revisar. Se não forem recebidas ocorrências de ponto de extremidade para uma intenção ou entidade específica, você não terá declarações para revisar que as contêm. 

## <a name="schedule-review-periodically"></a>Agendar revisão periodicamente
A revisão da declarações sugerida não precisa ser feita todos os dias, mas deve fazer parte da manutenção regular do LUIS. 

## <a name="delete-review-items-programmatically"></a>Excluir itens de revisão programaticamente
Use a API **[Excluir não rotulada do declarações](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9)** . Faça backup desses declarações antes da exclusão **[exportando os arquivos de log](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)** .

## <a name="next-steps"></a>Passos seguintes

* Saiba como [examinar](luis-how-to-review-endpoint-utterances.md) o ponto de extremidade declarações
