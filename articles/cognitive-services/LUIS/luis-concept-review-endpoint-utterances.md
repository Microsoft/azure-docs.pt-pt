---
title: Rever a expressão do utilizador - LUIS
description: Com aprendizagem ativa, a sua revisão endpoint pronuncia-se para a correta intenção e entidade. LUIS escolhe as palavras de ponto final de que não tem a certeza.
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 8d267fc441dc2cbf7f8ae3746486d5e7be55f135
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546857"
---
# <a name="concepts-for-enabling-active-learning-by-reviewing-endpoint-utterances"></a>Conceitos para permitir a aprendizagem ativa através da revisão de expressões de pontos finais
A aprendizagem ativa é uma das três estratégias para melhorar a precisão da previsão e a mais fácil de implementar. Com aprendizagem ativa, a sua revisão endpoint pronuncia-se para a correta intenção e entidade. LUIS escolhe as palavras de ponto final de que não tem a certeza.

## <a name="what-is-active-learning"></a>O que é a aprendizagem ativa
A aprendizagem ativa é um processo em duas etapas. Em primeiro lugar, o LUIS seleciona as declarações que recebe no ponto final da app que precisam de validação. O segundo passo é realizado pelo proprietário ou colaborador da app para validar as declarações selecionadas para [revisão](luis-how-to-review-endpoint-utterances.md), incluindo a intenção correta e quaisquer entidades dentro da intenção. Depois de rever as palavras, treine e publique novamente a app.

## <a name="which-utterances-are-on-the-review-list"></a>Que declarações estão na lista de revisão
Luis adiciona declarações à lista de revisão quando a intenção de disparo superior tem uma pontuação baixa ou as duas primeiras intenções estão muito perto.

## <a name="single-pool-for-utterances-per-app"></a>Piscina única para expressões por app
A lista de declarações de **pontofinal** da Revisão não muda com base na versão. Há um único conjunto de expressões para rever, independentemente da versão da expressão que está ativamente a editar ou da versão da aplicação que foi publicada no ponto final.

No [REST API,](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/58b6f32139e2bb139ce823c9)o nome da versão é necessário e tem de existir na aplicação, mas não é utilizado para além dessa validação. As declarações de revisão aplicam-se a uma aplicação inteira. Se remover as palavras de uma _versão,_ todas as versões são afetadas.

## <a name="where-are-the-utterances-from"></a>Onde estão as expressões de
As declarações de endpoint são retiradas de consultas de utilizador final no ponto final http da aplicação. Se a sua aplicação não for publicada ou ainda não tiver recebido acessos, não tem nenhuma expressão para rever. Se não forem recebidos acessos de ponto final para uma intenção ou entidade específica, não tem declarações para rever que os contenham.

## <a name="schedule-review-periodically"></a>Agendar revisão periodicamente
Rever as declarações sugeridas não precisa de ser feita todos os dias, mas deve fazer parte da sua manutenção regular de LUIS.

## <a name="delete-review-items-programmatically"></a>Eliminar itens de revisão programáticamente
Utilize as declarações a **[eliminar não etiquetadas](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9)** API. Faça o reforço destas declarações antes da eliminação **[exportando os ficheiros](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)** de registo .

## <a name="enable-active-learning"></a>Ativar a aprendizagem ativa

Para permitir a aprendizagem ativa, deve registar as consultas dos utilizadores. Isto é conseguido chamando a consulta `log=true` de ponto [final](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint) com o parâmetro de corda de consulta e o valor.

## <a name="next-steps"></a>Passos seguintes

* Saiba como [rever](luis-how-to-review-endpoint-utterances.md) as expressões de ponto final
