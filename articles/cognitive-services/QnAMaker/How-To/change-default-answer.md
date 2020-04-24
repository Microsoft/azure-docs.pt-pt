---
title: Obtenha resposta padrão - QnA Maker
description: A resposta padrão é devolvida quando não há correspondência com a pergunta. Pode querer alterar a resposta predefinida da resposta padrão.
ms.topic: how-to
ms.date: 04/22/2020
ms.openlocfilehash: db5a79ec612a73066ac37365a1815841fafb3862
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82097103"
---
# <a name="change-default-answer-for-a-qna-maker-resource"></a>Alterar a resposta padrão para um recurso QnA Maker

A resposta padrão é devolvida quando não há correspondência com a pergunta. Pode querer alterar a resposta predefinida da resposta padrão.

## <a name="change-default-answer-in-the-azure-portal"></a>Alterar Resposta padrão no portal Azure

1. Vá ao [portal Azure](https://portal.azure.com) e navegue para o grupo de recursos que representa o serviço QnA Maker que criou.

2. Clique para abrir o Serviço de **Aplicações**.

    ![No portal Azure, aceda ao serviço de aplicações para o QnA Maker](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Clique nas Definições de **Aplicação** e edite o campo **DefaultAnswer** para a resposta padrão desejada. Clique em **Guardar**.

    ![Selecione Definições de Aplicação e, em seguida, editar DefaultAnswer para O Criador de QnA](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Reinicie o seu serviço de Aplicações

    ![Depois de alterar o DefaultAnswer, reinicie o serviço de aplicações QnA Maker](../media/qnamaker-faq/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>Passos seguintes

* [Criar uma base de dados de conhecimento](../How-to/manage-knowledge-bases.md)