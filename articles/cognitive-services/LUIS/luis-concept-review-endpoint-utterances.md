---
title: Rever a expressão do utilizador - LUIS
titleSuffix: Azure Cognitive Services
description: Com aprendizagem ativa, a sua revisão endpoint pronuncia-se para a correta intenção e entidade. LUIS escolhe as palavras de ponto final de que não tem a certeza.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: diberry
ms.openlocfilehash: 375d4b4e7c3fcafbdfde1ff447bedc3e16aff2f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219942"
---
# <a name="concepts-for-enabling-active-learning-by-reviewing-endpoint-utterances"></a>Conceitos para permitir a aprendizagem ativa através da revisão de expressões de pontos finais
A aprendizagem ativa é uma das três estratégias para melhorar a precisão da previsão e a mais fácil de implementar. Com aprendizagem ativa, a sua revisão endpoint pronuncia-se para a correta intenção e entidade. LUIS escolhe as palavras de ponto final de que não tem a certeza.

## <a name="what-is-active-learning"></a>O que é a aprendizagem ativa
A aprendizagem ativa é um processo em duas etapas. Em primeiro lugar, o LUIS seleciona as declarações que recebe no ponto final da app que precisam de validação. O segundo passo é realizado pelo proprietário ou colaborador da app para validar as declarações selecionadas para [revisão](luis-how-to-review-endpoint-utterances.md), incluindo a intenção correta e quaisquer entidades dentro da intenção. Depois de rever as palavras, treine e publique novamente a app.

## <a name="which-utterances-are-on-the-review-list"></a>Que declarações estão na lista de revisão
Luis adiciona declarações à lista de revisão quando a intenção de disparo superior tem uma pontuação baixa ou as duas primeiras intenções estão muito perto.

## <a name="single-pool-for-utterances-per-app"></a>Piscina única para expressões por app
A lista de declarações de **pontofinal** da Revisão não muda com base na versão. Há um único conjunto de expressões para rever, independentemente da versão da expressão que está ativamente a editar ou da versão da aplicação que foi publicada no ponto final.

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
