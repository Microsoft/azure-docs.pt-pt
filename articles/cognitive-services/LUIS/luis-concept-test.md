---
title: Testar seu aplicativo LUIS
titleSuffix: Azure Cognitive Services
description: O teste é o processo de fornecer declarações de exemplo para LUIS e obter uma resposta de tentativas e entidades reconhecidas por LUIS.
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
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73486684"
---
# <a name="testing-example-utterances-in-luis"></a>Testando exemplo declarações em LUIS

O teste é o processo de fornecer declarações de exemplo para LUIS e obter uma resposta de tentativas e entidades reconhecidas por LUIS. 

Você pode testar LUIS interativamente, um expressão por vez ou fornecer um de declarações. Durante o teste, você pode comparar a resposta de previsão do modelo ativo atual com a resposta de previsão do modelo publicado. 

<a name="A-test-score"></a>
<a name="Score-all-intents"></a>
<a name="E-(exponent)-notation"></a>

## <a name="what-is-a-score-in-testing"></a>O que é uma pontuação no teste?
Consulte conceitos de [Pontuação de previsão](luis-concept-prediction-score.md) para saber mais sobre as pontuações de previsão.

## <a name="interactive-testing"></a>Testes interativos
O teste interativo é feito no painel de **teste** do portal do Luis. Você pode inserir um expressão para ver como as intenções e as entidades são identificadas e pontuadas. Se LUIS não estiver prevendo as intenções e entidades esperadas em um expressão no painel de teste, copie-as para a página de **intenção** como um novo expressão. Em seguida, rotule as partes do expressão para entidades e treine o LUIS. 

## <a name="batch-testing"></a>Testes em lote
Consulte [teste em lotes](luis-concept-batch-test.md) se você estiver testando mais de um expressão de cada vez.

## <a name="endpoint-testing"></a>Teste de ponto de extremidade
Você pode testar usando o [ponto de extremidade](luis-glossary.md#endpoint) com um máximo de duas versões de seu aplicativo. Com sua versão principal ou dinâmica do seu aplicativo definido como o ponto de extremidade de **produção** , adicione uma segunda versão ao ponto de extremidade de **preparo** . Essa abordagem oferece três versões de um expressão: o modelo atual no painel de teste do site [Luis](luis-reference-regions.md) e as duas versões nos dois pontos de extremidade diferentes. 

Todos os testes de ponto de extremidade contam para sua cota de uso. 

## <a name="do-not-log-tests"></a>Não registrar testes
Se você testar em relação a um ponto de extremidade e não quiser que o expressão seja registrado, lembre-se de usar a configuração de cadeia de caracteres de consulta `logging=false`.

## <a name="where-to-find-utterances"></a>Onde encontrar declarações
O LUIS armazena todos os declarações registrados no log de consultas, disponível para download no portal do LUIS na página de lista de **aplicativos** , bem como as [APIs de criação](https://go.microsoft.com/fwlink/?linkid=2092087)do Luis. 

Qualquer LUIS declarações não tem certeza de estar listado na página **[examinar ponto de extremidade declarações](luis-how-to-review-endpoint-utterances.md)** do site do [Luis](luis-reference-regions.md) . 

## <a name="remember-to-train"></a>Lembre-se de treinar
Lembre-se de [treinar](luis-how-to-train.md) o Luis depois de fazer alterações no modelo. As alterações no aplicativo LUIS não são vistas no teste até que o aplicativo seja treinado. 

## <a name="best-practices"></a>Melhores práticas
Conheça [as práticas recomendadas](luis-concept-best-practices.md).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre como [testar](luis-interactive-test.md) seu declarações.
