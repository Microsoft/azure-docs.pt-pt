---
title: Base de conhecimento de design - QnA Maker
description: Uma base de conhecimento do QnA Maker consiste num conjunto de conjuntos de perguntas e respostas (QnA) e metadados opcionais associados a cada par qnA.
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: cb71de0f6a3e372d8c1c1fede67ba0c0354532ca
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
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

![Bases de dados de conhecimento do QnA Maker](../media/qnamaker-concepts-knowledgebase/knowledgebase.png)

## <a name="editorially-add-to-knowledge-base"></a>Adicionar de forma editorial à base de dados de conhecimento

Se não tiver conteúdo pré-existente para povoar a base de conhecimentos, pode adicionar conjuntos de QnA editorialmente no portal QnA Maker. Saiba como atualizar a sua base de dados de conhecimento [aqui](../How-To/edit-knowledge-base.md).

## <a name="editing-your-knowledge-base-locally"></a>Editando sua base de dados de conhecimento localmente

Quando uma base de dados de conhecimento é criada, é recomendável que você faça edições no texto da base de dados de conhecimento no [portal de QnA Maker](https://qnamaker.ai), em vez de exportar e reimportar por meio de arquivos locais. No entanto, pode haver ocasiões em que você precisa editar uma base de dados de conhecimento localmente.

Exporte a base de dados de conhecimento na página **configurações** e edite a base de dados de conhecimento com o Microsoft Excel. Se optar por utilizar outra aplicação para editar o seu ficheiro exportado, a aplicação pode introduzir erros de sintaxe porque não é totalmente compatível com a TSV. Os arquivos TSV do Microsoft Excel geralmente não introduzem erros de formatação.

Depois de concluir suas edições, reimporte o arquivo TSV da página **configurações** . Isso substituirá completamente a base de dados de conhecimento atual pela base de dados de conhecimento importada.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Ciclo de vida base de conhecimento em QnA Maker](./development-lifecycle-knowledge-base.md)