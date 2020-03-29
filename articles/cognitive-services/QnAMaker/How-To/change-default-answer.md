---
title: Obtenha resposta padrão - QnA Maker
description: A resposta padrão é devolvida quando não há correspondência com a pergunta. Pode querer alterar a resposta predefinida da resposta padrão.
ms.topic: conceptual
ms.date: 01/10/2020
ms.openlocfilehash: fae5c38fd64435a3fae56862bad04e000916e88b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "76843280"
---
# <a name="set-default-answer-for-a-knowledge-base"></a>Definir resposta padrão para uma base de conhecimento

A resposta padrão é devolvida quando não há correspondência com a pergunta. Pode querer alterar a resposta predefinida da resposta padrão.

## <a name="change-default-answer"></a>Alterar A Resposta padrão

1. Vá ao [portal Azure](https://portal.azure.com) e navegue para o grupo de recursos que representa o serviço QnA Maker que criou.

2. Clique para abrir o Serviço de **Aplicações**.

    ![No portal Azure, aceda ao serviço de aplicações para o QnA Maker](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Clique nas Definições de **Aplicação** e edite o campo **DefaultAnswer** para a resposta padrão desejada. Clique em **Guardar**.

    ![Selecione Definições de Aplicação e, em seguida, editar DefaultAnswer para O Criador de QnA](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Reinicie o seu serviço de Aplicações

    ![Depois de alterar o DefaultAnswer, reinicie o serviço de aplicações QnA Maker](../media/qnamaker-faq/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>Passos seguintes

* [Crie um bot com QnA Maker e LUIS](../tutorials/integrate-qnamaker-luis.md)