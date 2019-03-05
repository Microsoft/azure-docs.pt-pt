---
title: Base de dados de conhecimento - QnA Maker
titleSuffix: Azure Cognitive Services
description: Uma base de dados de conhecimento do QnA Maker é composta por um conjunto de pares de pergunta/resposta (FAQ) e metadados opcionais associados a cada par de QnA.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/04/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 02111ac90fe97ddaddbd41ad42410e7e76f1c405
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57311113"
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

Campos de metadados são compostas por pares chave-valor, separados por vírgula **(produto: Shredder)**. Chave e valor tem de ser só de texto. A chave de metadados não pode conter quaisquer espaços. Metadados suporta apenas um valor por chave.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Ciclo de vida do desenvolvimento de uma base de dados de conhecimento](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Consulte também

[Descrição geral do Criador de FAQ](../Overview/overview.md)
