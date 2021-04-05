---
title: Base de conhecimento de design - conceitos do QnA Maker
description: Aprenda a desenhar uma base de conhecimento - QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: c21c82b5fd024598da30f4ac7b1ed01e64561e3b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "96000118"
---
# <a name="question-and-answer-pair-concepts"></a>Conceitos de pares de perguntas e respostas

Uma base de conhecimento consiste em pares de perguntas e respostas (QnA).  Cada par tem uma resposta e um par contém todas as informações associadas a essa _resposta._ Uma resposta pode assemelhar-se vagamente a uma linha de base de dados ou a uma instância de estrutura de dados.

## <a name="question-and-answer-pairs"></a>Pares de perguntas e respostas

As definições **necessárias** num par de perguntas e respostas (QnA) são:

* uma **pergunta** - texto de consulta do utilizador, usado para a aprendizagem automática do QnA Maker, para alinhar com o texto da pergunta do utilizador com diferentes formulações, mas a mesma resposta
* a **resposta** - a resposta do par é a resposta que é devolvida quando uma consulta do utilizador é compatível com a pergunta associada

Cada par é representado por um **ID**.

As definições **opcionais** para um par incluem:

* **Formas alternativas da pergunta** - isto ajuda a QnA Maker a devolver a resposta correta para uma maior variedade de frases de perguntas
* **Metadados**: Os metadados são tags associadas a um par QnA e são representados como pares de valor-chave. As tags de metadados são usadas para filtrar pares QnA e limitar o conjunto sobre o qual a correspondência de consulta é realizada.
* **Pedidos multi-turn, usados** para continuar uma conversa multi-volta

![Bases de conhecimento do QnA Maker](../media/qnamaker-concepts-knowledgebase/knowledgebase.png)

## <a name="editorially-add-to-knowledge-base"></a>Editorialmente adicionar à base de conhecimento

Se não tiver conteúdo pré-existente para preencher a base de conhecimento, pode adicionar pares QnA editorialmente no portal QnA Maker. Saiba como atualizar a sua base de [conhecimentos aqui.](../How-To/edit-knowledge-base.md)

## <a name="editing-your-knowledge-base-locally"></a>Editar a sua base de conhecimento localmente

Uma vez criada uma base de conhecimento, recomenda-se que faça edições para o texto base de conhecimento no [portal QnA Maker](https://qnamaker.ai), em vez de exportar e reimportar através de ficheiros locais. No entanto, pode haver momentos em que é necessário editar uma base de conhecimento localmente.

Exporte a base de conhecimento a partir da página **Definições** e, em seguida, edite a base de conhecimento com o Microsoft Excel. Se optar por utilizar outra aplicação para editar o seu ficheiro exportado, a aplicação pode introduzir erros de sintaxe porque não está totalmente em conformidade com a TSV. Os ficheiros TSV do Microsoft Excel geralmente não introduzem erros de formatação.

Assim que terminar as suas edições, reiporte o ficheiro TSV da página **Definições.** Isto substituirá completamente a base de conhecimentos atual pela base de conhecimentos importada.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Ciclo de vida base de conhecimento no Fabricante QnA](./development-lifecycle-knowledge-base.md)