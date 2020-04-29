---
title: Teste a sua app LUIS
titleSuffix: Azure Cognitive Services
description: O teste é o processo de fornecimento de declarações de amostra seletivas à LUIS e de obter uma resposta de intenções e entidades reconhecidas pela LUIS.
author: diberry
manager: nitinme
services: cognitive-services
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: diberry
ms.openlocfilehash: 25b360f90a0920aad2ea5e68cda31a68be5d37a9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "73486684"
---
# <a name="testing-example-utterances-in-luis"></a>Testando pronunciações de exemplo no LUIS

O teste é o processo de fornecimento de declarações de amostra seletivas à LUIS e de obter uma resposta de intenções e entidades reconhecidas pela LUIS. 

Você pode testar LUIS interativamente, uma expressão de cada vez, ou fornecer uma de proclamações. Durante os testes, pode comparar a resposta de previsão do modelo ativo atual à resposta de previsão do modelo publicado. 

<a name="A-test-score"></a>
<a name="Score-all-intents"></a>
<a name="E-(exponent)-notation"></a>

## <a name="what-is-a-score-in-testing"></a>O que é uma pontuação nos testes?
Consulte os conceitos de [pontuação de previsão](luis-concept-prediction-score.md) para saber mais sobre as pontuações de previsão.

## <a name="interactive-testing"></a>Testes interativos
Os testes interativos são feitos a partir do painel de **teste** do portal LUIS. Pode entrar numa expressão para ver como as intenções e entidades são identificadas e pontuadas. Se o LUIS não está a prever as intenções e entidades como espera numa expressão no painel de testes, copie-a para a página **Intent** como uma nova expressão. Em seguida, rotular as partes dessa expressão para entidades, e treinar LUIS. 

## <a name="batch-testing"></a>Testes em lote
Consulte os testes de [lote](luis-concept-batch-test.md) se estiver a testar mais do que uma expressão de cada vez.

## <a name="endpoint-testing"></a>Ensaio de ponto final
Pode testar o [ponto final](luis-glossary.md#endpoint) com um máximo de duas versões da sua aplicação. Com a sua versão principal ou ao vivo da sua aplicação definida como o ponto final de **produção,** adicione uma segunda versão ao ponto final de **encenação.** Esta abordagem dá-lhe três versões de uma expressão: o modelo atual no painel de teste do site [da LUIS,](luis-reference-regions.md) e as duas versões nos dois pontos finais diferentes. 

Todos os testes de ponto final contam para a sua quota de utilização. 

## <a name="do-not-log-tests"></a>Não faça log tests
Se testar contra um ponto final e não quiser que a `logging=false` expressão seja registada, lembre-se de utilizar a configuração da corda de consulta.

## <a name="where-to-find-utterances"></a>Onde encontrar expressões
A LUIS armazena todas as declarações registadas no registo de consultas, disponíveis para download no portal LUIS **a** partir da página da lista apps, bem como a AUTORA DA LUIS de [APIs](https://go.microsoft.com/fwlink/?linkid=2092087). 

Quaisquer declarações de que o LUIS não tem a certeza estão listadas na página de **[declarações](luis-how-to-review-endpoint-utterances.md)** de ponto final da Revisão do site da [LUIS.](luis-reference-regions.md) 

## <a name="remember-to-train"></a>Lembre-se de treinar
Lembre-se de [treinar](luis-how-to-train.md) o LUIS depois de fazer alterações no modelo. As alterações à aplicação LUIS não são vistas em testes até que a aplicação seja treinada. 

## <a name="best-practices"></a>Melhores práticas
Aprenda [as melhores práticas.](luis-concept-best-practices.md)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [testar](luis-interactive-test.md) as suas expressões.
