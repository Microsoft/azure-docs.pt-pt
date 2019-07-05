---
title: Base de dados de conhecimento - QnA Maker
titleSuffix: Azure Cognitive Services
description: Uma base de dados de conhecimento do QnA Maker é composta por um conjunto de pares de pergunta/resposta (FAQ) e metadados opcionais associados a cada par de QnA.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/25/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 7e40af9b2362ee52a1d00f29cdc112d3c2b9a842
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565856"
---
# <a name="what-is-a-qna-maker-knowledge-base"></a>O que é uma base QnA Maker para o conhecimento?

Uma base de dados de conhecimento do QnA Maker é composta por um conjunto de pares de pergunta/resposta (FAQ) e metadados opcionais associados a cada par de QnA.

## <a name="key-knowledge-base-concepts"></a>Conceitos chave base de dados de conhecimento

* **Perguntas** -uma pergunta contém o texto que melhor representa uma consulta de utilizador. 
* **Respostas** -uma resposta é a resposta retornada quando uma consulta de utilizador é correspondida com a pergunta associada.  
* **Metadados** -metadados são as marcas associadas com um par de QnA e são representados como pares chave-valor. Marcas de metadados são utilizadas para filtrar os pares de QnA e limitar o conjunto ao longo do que consulta correspondente é executada.

Um único QnA, representada por um ID numérico do QnA, tem várias variantes de uma pergunta (perguntas alternativas) que todos mapeiam para uma única resposta. Além disso, cada par de tal pode ter vários campos de metadados associados à mesma: uma chave e um valor.

![Bases de dados de conhecimento do QnA Maker](../media/qnamaker-concepts-knowledgebase/knowledgebase.png) 

## <a name="knowledge-base-content-format"></a>Formato de conteúdo de base de dados de conhecimento

Quando ingerir conteúdo avançado para uma base de dados de conhecimento, QnA Maker tenta converter o conteúdo em markdown. Leia [isso](https://aka.ms/qnamaker-docs-markdown-support) blogue para compreender o markdown formatos reconhecidos pela maioria dos clientes de bate-papo.

Campos de metadados são compostas por pares chave-valor, separados por vírgula **(produto: Shredder)** . Chave e valor tem de ser só de texto. A chave de metadados não pode conter quaisquer espaços. Metadados suporta apenas um valor por chave.

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>Como o QnA Maker processa uma consulta de utilizador para selecionar a melhor resposta

O preparado e [publicados](/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base#publish-the-knowledge-base) base de dados de conhecimento do QnA Maker recebe uma consulta de utilizador, de um bot ou outra aplicação de cliente, no [GenerateAnswer API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage). O diagrama seguinte ilustra o processo quando é recebida a consulta do utilizador.

![O processo de classificação para uma consulta de utilizador](../media/qnamaker-concepts-knowledgebase/rank-user-query-first-with-azure-search-then-with-qna-maker.png)

O processo é explicado na tabela a seguir:

|Passo|Objetivo|
|--|--|
|1|A aplicação cliente envia a consulta de utilizador para o [GenerateAnswer API](/azure/cognitive-services/qnamaker/how-to/metadata-generateanswer-usage).|
|2|A ferramenta QnA Maker pré-processamento a consulta do utilizador com deteção de idioma, spellers e separadores de palavras.|
|3|Este processamento prévio de está a ser utilizado para alterar a consulta de utilizador para obter melhores resultados de pesquisa.|
|4|Esta consulta alterada é enviada para o índice da Azure Search, receber a `top` o número de resultados. Se não estiver correta nesses resultados, aumente o valor de `top` um pouco. Em geral, um valor de 10 para `top` funciona em 90% das consultas.|
|5|A ferramenta QnA Maker aplica-se featurization avançada para determinar a correção nos resultados de pesquisa do Azure foram obtidas de consulta de utilizador. |
|6|O modelo de classificador preparado utiliza a classificação de recurso do passo 5, para classificar os resultados da pesquisa do Azure.|
|7|Os novos resultados são devolvidos para o aplicativo de cliente em ordem classificada.|
|||

Funcionalidades utilizadas incluem, mas não sejam limitam a semântica de nível de word, o termo de nível de importância num corpo e profunda adquiridos de modelos semânticos para determinar a semelhança e relevância entre duas cadeias de texto.


## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Ciclo de vida do desenvolvimento de uma base de dados de conhecimento](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Consulte também

[Descrição geral do Criador de FAQ](../Overview/overview.md)
