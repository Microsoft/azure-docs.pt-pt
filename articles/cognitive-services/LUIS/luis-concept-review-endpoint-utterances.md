---
title: Comentário ao utilizador - LUIS
description: Com a aprendizagem ativa, as suas declarações de ponto final de revisão para a correta intenção e entidade. LUIS escolhe declarações de ponto final que não tem a certeza.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 82f228d5e6f801539c549e16faea371782ad4b59
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91316448"
---
# <a name="concepts-for-enabling-active-learning-by-reviewing-endpoint-utterances"></a>Conceitos para permitir a aprendizagem ativa através da revisão de expressões de ponto final
A aprendizagem ativa é uma das três estratégias para melhorar a precisão da previsão e a mais fácil de implementar. Com a aprendizagem ativa, as suas declarações de ponto final de revisão para a correta intenção e entidade. LUIS escolhe declarações de ponto final que não tem a certeza.

## <a name="what-is-active-learning"></a>O que é aprendizagem ativa
A aprendizagem ativa é um processo em duas etapas. Em primeiro lugar, a LUIS seleciona as expressões que recebe no ponto final da app que precisam de validação. O segundo passo é realizado pelo proprietário ou colaborador da app para validar as declarações selecionadas para [revisão,](luis-how-to-review-endpoint-utterances.md)incluindo a intenção correta e quaisquer entidades dentro da intenção. Depois de rever as declarações, treine e publique novamente a app.

## <a name="which-utterances-are-on-the-review-list"></a>Que declarações estão na lista de revisão
LUIS adiciona declarações à lista de revisão quando a intenção de tiro de topo tem uma pontuação baixa ou as pontuações dos dois primeiros intos estão muito próximas.

## <a name="single-pool-for-utterances-per-app"></a>Piscina única para expressões por app
A lista **de declarações de ponto final** da Revisão não muda com base na versão. Há um único conjunto de expressões para rever, independentemente da versão da expressão que está ativamente a editar ou da versão da aplicação que foi publicada no ponto final.

Na [API REST,](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/58b6f32139e2bb139ce823c9)o nome da versão é necessário e tem de existir na aplicação, mas não é utilizado para além dessa validação. As declarações de revisão aplicam-se a toda uma aplicação. Se remover expressões de uma _versão,_ todas as versões são afetadas.

## <a name="where-are-the-utterances-from"></a>De onde estão as declarações
As declarações de ponto final são retiradas das consultas de utilizador final no ponto final HTTP da aplicação. Se a sua aplicação não for publicada ou ainda não tiver recebido acessos, não tem nenhuma expressão para rever. Se não forem recebidos hits de ponto final para uma determinada intenção ou entidade, não tem expressões para rever que os contenham.

## <a name="schedule-review-periodically"></a>Revisão de horários periódicas
Rever as declarações sugeridas não precisa de ser feito todos os dias, mas deve fazer parte da sua manutenção regular do LUIS.

## <a name="delete-review-items-programmatically"></a>Eliminar itens de revisão programáticamente
Utilize as **[expressões não etiquetadas](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9)** API. Faça o reforço destas expressões antes da supressão **[exportando os ficheiros de registo](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)**.

## <a name="enable-active-learning"></a>Permitir a aprendizagem ativa

Para ativar a aprendizagem ativa, tem de registar consultas de utilizador. Isto é conseguido chamando a consulta de [ponto final](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint) com o parâmetro de consulta `log=true` e valor.

## <a name="next-steps"></a>Passos seguintes

* Saiba como [rever](luis-how-to-review-endpoint-utterances.md) as expressões de ponto final
