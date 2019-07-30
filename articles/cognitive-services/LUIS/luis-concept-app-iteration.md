---
title: Design de aplicativo iterativo-LUIS
titleSuffix: Azure Cognitive Services
description: LUIS Aprende melhor num ciclo iterativo de alterações no modelo, exemplos de expressão, publicação e a recolha de dados das consultas de ponto final.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 2a540606a6f9cfa790a2244628e7f0b7bef35986
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639255"
---
# <a name="authoring-cycle-for-your-luis-app"></a>Ciclo de criação para seu aplicativo LUIS
LUIS Aprende melhor num ciclo iterativo de alterações no modelo, exemplos de expressão, publicação e a recolha de dados das consultas de ponto final. 

![Ciclo de criação](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-model"></a>Criar um modelo do LUIS
Objetivo do modelo é descobrir o que o utilizador pede para (a intenção ou a intenção) e que partes da pergunta fornecem os detalhes (entidades), que ajudam a determinar a resposta. 

O modelo tem de ser específico para o domínio de aplicativo para determinar as palavras e frases que é relevante, bem como típico word ordenação. 

O modelo requer tentativas e _deve ter_ entidades. 

## <a name="add-training-examples"></a>Adicionar exemplos de treinamento
LUIS tem expressões de exemplo dos objetivos. Os exemplos tem suficiente variação da opção de palavra e ordem das palavras para ser capaz de determinar que objetivo a expressão se destina. Cada ocorrência de pronunciação de exemplo tem de ter todos os dados necessários identificados como entidades. 

Instruir o LUIS para ignorar as expressões que não são relevantes para o domínio da sua aplicação ao atribuir a expressão para o **None** intenção. Quaisquer palavras ou frases que não precisa extraídos fora de uma expressão não é necessário ser rotulados. Não há nenhum rótulo de palavras ou frases para ignorar. 

## <a name="train-and-publish-the-app"></a>Preparar e publicar a aplicação
Depois de ter 15 a 30 declarações diferentes em cada tentativa, com as entidades necessárias rotuladas, você precisa [treinar](luis-how-to-train.md) e [publicar](luis-how-to-publish-app.md). A partir de notificação de êxito de publicar, utilize a ligação para obter os pontos finais. Certifique-se de criar e publicar seu aplicativo para que ele esteja disponível nas [regiões de ponto de extremidade](luis-reference-regions.md) de que você precisa. 

## <a name="https-endpoint-testing"></a>Teste de ponto final HTTPS
Pode testar a aplicação do LUIS partir do ponto final HTTPS. O teste do ponto de extremidade permite que o LUIS escolha qualquer declarações com baixa confiança para [revisão](luis-how-to-review-endpoint-utterances.md).  

## <a name="recycle"></a>Reciclagem

Quando tiver terminado com um ciclo de criação, pode começar novamente. Comece com a [revisão do ponto de extremidade declarações](luis-how-to-review-endpoint-utterances.md) Luis marcado com baixa confiança. Verifique estas expressões de com para o objetivo e a entidade. Depois de rever expressões com, a lista de revisão deve estar vazia.  

Considere [clonar](luis-concept-version.md#clone-a-version) a versão atual em uma nova versão e, em seguida, comece a criar alterações na nova versão. 

## <a name="batch-testing"></a>Testes em lote

O [teste em lotes](luis-concept-batch-test.md) é uma maneira de ver quantos exemplos de declarações são pontuados por Luis. Os exemplos devem estar familiarizados com o LUIS e devem ser o nome corretamente com a intenção e entidades que pretende que o LUIS para localizar. Os resultados do teste indicam a eficiência com que executará nesse conjunto de expressões com os LUIS. 

## <a name="next-steps"></a>Passos Seguintes

Conheça os conceitos [colaboração](luis-concept-collaborator.md).
