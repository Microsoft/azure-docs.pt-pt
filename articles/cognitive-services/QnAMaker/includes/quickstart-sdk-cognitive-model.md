---
ms.openlocfilehash: c87b9dc22ecd937abb27417aeddc1e30c0d724e7
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2020
ms.locfileid: "85114575"
---

## <a name="using-this-example-knowledge-base"></a>Usando este exemplo base de conhecimento

A base de conhecimento neste quickstart começa com 2 pares de QnA conversacional, isto é feito de propósito para simplificar o exemplo e ter IDs altamente previsíveis para usar no método Update, associando pedidos de acompanhamento com perguntas a novos pares. Isto foi planeado e implementado numa ordem específica para este arranque rápido.

Se planeia desenvolver a sua base de conhecimento ao longo do tempo com pedidos de seguimento que dependem dos pares QnA existentes, pode escolher:
* Para maiores bases de conhecimento, gerencie a base de conhecimentos num editor de texto ou numa ferramenta de TSV que suporte a automatização e, em seguida, substitua completamente a base de conhecimento de uma só vez por uma atualização.
* Para bases de conhecimentos mais pequenas, gerencie as solicitações de seguimento inteiramente no portal QnA Maker.

Detalhes sobre os pares QnA usados neste arranque rápido:
* Tipos de par QnA - existem 2 tipos de pares QnA nesta base de conhecimento, após a atualização: chitchat e informações específicas do domínio. Isto é típico se a sua base de conhecimentos estiver ligada a uma aplicação de conversação como um chatbot.
* Embora as respostas da base de conhecimento possam ser filtradas por metadados ou por solicitações de seguimento, este arranque rápido não mostra isso. Procure por aqueles exemplos de geração de linguagem-agnóstico [aqui.](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md)
* O texto de resposta é marcado e pode conter uma [grande variedade de marcos](../reference-markdown-format.md) como imagens (imagens publicamente disponíveis na Internet), links (para URLs publicamente disponíveis) e pontos de bala, este quickstart não usa essa variedade.
