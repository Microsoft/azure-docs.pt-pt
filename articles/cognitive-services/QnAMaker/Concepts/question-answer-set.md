---
title: Base de conhecimento de design - QnA Maker
description: Uma base de conhecimento do QnA Maker consiste num conjunto de conjuntos de perguntas e respostas (QnA) e metadados opcionais associados a cada par qnA.
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: cb71de0f6a3e372d8c1c1fede67ba0c0354532ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "76844326"
---
# <a name="question-and-answer-set-concepts"></a>Conceitos de conjunto de perguntas e respostas

Uma base de conhecimento consiste em conjuntos de perguntas e respostas (QnA).  Cada conjunto tem uma resposta e um conjunto contém todas as informações _associadas_a essa resposta . Uma resposta pode assemelhar-se vagamente a uma linha de base de dados ou a uma instância de estrutura de dados.

## <a name="question-and-answer-sets"></a>Conjuntos de perguntas e respostas

As definições **necessárias** num conjunto de perguntas e respostas (QnA) são:

* uma **pergunta** - texto de consulta do utilizador, usado para a aprendizagem automática do QnA Maker, para alinhar com texto da pergunta do utilizador com formulações diferentes, mas a mesma resposta
* a **resposta** - a resposta do conjunto é a resposta que é devolvida quando uma consulta de utilizador é acompanhada da pergunta associada

Cada conjunto é representado por um **ID**.

As definições **opcionais** para um conjunto incluem:

* **Formas alternativas da pergunta** - isto ajuda qnA Maker devolver a resposta correta para uma maior variedade de frases de perguntas
* **Metadados**: Os metadados são etiquetas associadas a um par QnA e são representadas como pares de valor-chave. As etiquetas de metadados são usadas para filtrar os pares QnA e limitar o conjunto sobre o qual a correspondência de consulta é realizada.
* **Solicitações de volta,** usadas para continuar uma conversa multi-turn

![Bases de conhecimento do Fabricante qna](../media/qnamaker-concepts-knowledgebase/knowledgebase.png)

## <a name="editorially-add-to-knowledge-base"></a>Adicione editorialmente à base de conhecimento

Se não tiver conteúdo pré-existente para povoar a base de conhecimentos, pode adicionar conjuntos de QnA editorialmente no portal QnA Maker. Saiba como atualizar a sua base de conhecimentos [aqui](../How-To/edit-knowledge-base.md).

## <a name="editing-your-knowledge-base-locally"></a>Editar a sua base de conhecimentos localmente

Uma vez criada uma base de conhecimento, recomenda-se que evoque edificações para o texto base de conhecimento no [portal QnA Maker](https://qnamaker.ai), em vez de exportar e reimportar através de ficheiros locais. No entanto, pode haver momentos em que precisa editar uma base de conhecimento localmente.

Exporte a base de conhecimentos a partir da página **Definições** e, em seguida, edite a base de conhecimentos com o Microsoft Excel. Se optar por utilizar outra aplicação para editar o seu ficheiro exportado, a aplicação pode introduzir erros de sintaxe porque não é totalmente compatível com a TSV. Os ficheiros TSV do Microsoft Excel geralmente não introduzem erros de formatação.

Uma vez terminada a sua edificação, reimporte o ficheiro TSV da página **Definições.** Isto substituirá completamente a atual base de conhecimentos pela base de conhecimentos importada.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Ciclo de vida base de conhecimento em QnA Maker](./development-lifecycle-knowledge-base.md)