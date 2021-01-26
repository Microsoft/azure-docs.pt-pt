---
title: Teste o seu aplicativo LUIS
titleSuffix: Azure Cognitive Services
description: O teste é o processo de fornecer expressões de amostras ao LUIS e obter uma resposta de intenções e entidades reconhecidas pela LUIS.
manager: nitinme
services: cognitive-services
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: b33f765e936d7c0db301a5b2fcf38ccaf137f771
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98787558"
---
# <a name="testing-example-utterances-in-luis"></a>Teste de palavras de exemplo em LUIS

O teste é o processo de fornecer expressões de amostras ao LUIS e obter uma resposta de intenções e entidades reconhecidas pela LUIS. 

Você pode testar LUIS interativamente, uma expressão de cada vez, ou fornecer um conjunto de expressões. Durante os testes, pode comparar a resposta de previsão do modelo ativo atual à resposta de previsão do modelo publicado. 

<a name="A-test-score"></a>
<a name="Score-all-intents"></a>
<a name="E-(exponent)-notation"></a>

## <a name="what-is-a-score-in-testing"></a>O que é uma pontuação nos testes?
Veja os conceitos [de pontuação de previsão](luis-concept-prediction-score.md) para saber mais sobre as pontuações de previsão.

## <a name="interactive-testing"></a>Testes interativos
Os testes interativos são feitos a partir do painel de **teste** do portal LUIS. Pode inserir uma expressão para ver como as intenções e as entidades são identificadas e pontuadas. Se a LUIS não está a prever as intenções e as entidades como espera numa expressão no painel de testes, copie-a para a página **De intenções** como uma nova expressão. Em seguida, rotular as partes dessa expressão para entidades, e treinar LUIS. 

## <a name="batch-testing"></a>Testes em lote
Consulte [os testes de lote](./luis-how-to-batch-test.md) se estiver a testar mais do que uma expressão de cada vez.

## <a name="endpoint-testing"></a>Teste de ponto final
Pode testar usando o [ponto final](luis-glossary.md#endpoint) com um máximo de duas versões da sua aplicação. Com a sua versão principal ou ao vivo da sua aplicação definida como ponto final de **produção,** adicione uma segunda versão ao ponto final de **preparação.** Esta abordagem dá-lhe três versões de uma expressão: o modelo atual no painel de teste do site [LUIS,](luis-reference-regions.md) e as duas versões nos dois pontos finais diferentes. 

Todos os testes de ponto final contam para a sua quota de utilização. 

## <a name="do-not-log-tests"></a>Não faça testes de registo
Se testar contra um ponto final e não quiser que a expressão seja registada, lembre-se de utilizar a configuração da `logging=false` cadeia de consulta.

## <a name="where-to-find-utterances"></a>Onde encontrar expressões
A LUIS armazena todas as declarações registadas no registo de consultas, disponíveis para download no portal LUIS a partir da página da lista **de Apps,** bem como nas [APIs de autoria](https://go.microsoft.com/fwlink/?linkid=2092087)do LUIS. 

Quaisquer declarações de que a LUIS não tem a certeza estão listadas na página de **[comentários finais](luis-how-to-review-endpoint-utterances.md)** do site da [LUIS.](luis-reference-regions.md) 

## <a name="remember-to-train"></a>Lembre-se de treinar
Lembre-se de [treinar](luis-how-to-train.md) o LUIS depois de fazer alterações no modelo. As alterações à aplicação LUIS não são vistas em testes até que a aplicação seja treinada. 

## <a name="best-practices"></a>Melhores práticas
Aprenda as [melhores práticas.](luis-concept-best-practices.md)

## <a name="next-steps"></a>Próximos passos

* Saiba mais sobre [testar](luis-interactive-test.md) as suas expressões.