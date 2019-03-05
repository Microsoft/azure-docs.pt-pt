---
title: Estrutura da aplicação iterativo
titleSuffix: Language Understanding - Azure Cognitive Services
description: LUIS Aprende melhor num ciclo iterativo de alterações no modelo, exemplos de expressão, publicação e a recolha de dados das consultas de ponto final.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: diberry
ms.openlocfilehash: 67bcb33727bc808f5e5bea701daffc77dde736ff
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57337486"
---
# <a name="authoring-cycle-for-your-luis-app"></a>Ciclo de criação para a sua aplicação LUIS
LUIS Aprende melhor num ciclo iterativo de alterações no modelo, exemplos de expressão, publicação e a recolha de dados das consultas de ponto final. 

![Ciclo de criação](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-model"></a>Criar um modelo do LUIS
Objetivo do modelo é descobrir o que o utilizador pede para (a intenção ou a intenção) e que partes da pergunta fornecem os detalhes (entidades), que ajudam a determinar a resposta. 

O modelo tem de ser específico para o domínio de aplicativo para determinar as palavras e frases que é relevante, bem como típico word ordenação. 

O modelo inclui intenção, entidades. 

## <a name="add-training-examples"></a>Adicionar exemplos de treinamento
LUIS tem expressões de exemplo dos objetivos. Os exemplos tem suficiente variação da opção de palavra e ordem das palavras para ser capaz de determinar que objetivo a expressão se destina. Cada ocorrência de pronunciação de exemplo tem de ter todos os dados necessários identificados como entidades. 

Instruir o LUIS para ignorar as expressões que não são relevantes para o domínio da sua aplicação ao atribuir a expressão para o **None** intenção. Quaisquer palavras ou frases que não precisa extraídos fora de uma expressão não é necessário ser rotulados. Não há nenhum rótulo de palavras ou frases para ignorar. 

## <a name="train-and-publish-the-app"></a>Preparar e publicar a aplicação
Depois de ter expressões com diferentes de 10 a 15 em intenção, cada, com as entidades necessárias o nome, formar e publicar. A partir de notificação de êxito de publicar, utilize a ligação para obter os pontos finais. Certifique-se criar a sua aplicação e publique a sua aplicação para que ele está disponível na [regiões de ponto final](luis-reference-regions.md) que precisa. 

## <a name="https-endpoint-testing"></a>Teste de ponto final HTTPS
Pode testar a aplicação do LUIS partir do ponto final HTTPS. Teste a partir do ponto final permite que o LUIS escolher qualquer expressões com confiança de baixo para revisão.  

## <a name="recycle"></a>Reciclagem
Quando tiver terminado com um ciclo de criação, pode começar novamente. Comece por ler as expressões de ponto final que Luis marcado com confiança de baixa. Verifique estas expressões de com para o objetivo e a entidade. Depois de rever expressões com, a lista de revisão deve estar vazia.  

## <a name="batch-testing"></a>Testes em lote
Teste de batch é uma forma de ver quantos expressões de exemplo são classificadas por LUIS. Os exemplos devem estar familiarizados com o LUIS e devem ser o nome corretamente com a intenção e entidades que pretende que o LUIS para localizar. Os resultados do teste indicam a eficiência com que executará nesse conjunto de expressões com os LUIS. 

## <a name="next-steps"></a>Passos Seguintes

Conheça os conceitos [colaboração](luis-concept-collaborator.md).
