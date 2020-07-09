---
title: Obtenha resposta predefinitiva - QnA Maker
description: A resposta por defeito é devolvida quando não há correspondência com a pergunta. Pode querer alterar a resposta predefinida da resposta padrão.
ms.topic: how-to
ms.date: 07/02/2020
ms.openlocfilehash: 005442938167c1bf7927603c44d6f870795cbeee
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/06/2020
ms.locfileid: "85979980"
---
# <a name="change-default-answer-for-a-qna-maker-resource"></a>Alterar a resposta padrão para um recurso do QnA Maker

A resposta padrão para uma base de conhecimento deve ser devolvida quando uma resposta não é encontrada. Se estiver a utilizar uma aplicação do cliente, como o [serviço Azure Bot,](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs#calling-qna-maker-from-your-bot)também poderá ter uma resposta por defeito separada, indicando que nenhuma resposta cumpriu o limiar de pontuação.

## <a name="set-default-answer-when-you-create-knowledge-base"></a>Desafie a resposta predefinida quando criar base de conhecimento

Quando cria uma nova base de conhecimento, o texto de resposta predefinido é uma das definições. Se optar por não o definir durante o processo de criação, pode alterá-lo mais tarde com o seguinte procedimento.

## <a name="change-default-answer-in-qna-maker-portal"></a>Alterar resposta padrão no portal QnA Maker

A resposta predefinitiva da base de conhecimento é devolvida quando nenhuma resposta é devolvida do serviço QnA Maker.

1. Inscreva-se no [portal QnA Maker](https://www.qnamaker.ai/) e selecione a sua base de conhecimentos na lista.
1. Selecione **Definições** da barra de navegação.
1. Alterar o valor do texto de **resposta predefinido** na secção **'Gerir o conhecimento'.**

    :::image type="content" source="../media/qnamaker-concepts-confidencescore/change-default-answer.png" alt-text="Screenshot do portal QnA Maker, página de Definições, com caixa de texto de resposta predefinida realçada.":::

1. **Selecione Guardar e treinar** para salvar a mudança.

## <a name="next-steps"></a>Próximos passos

* [Criar uma base de dados de conhecimento](../How-to/manage-knowledge-bases.md)