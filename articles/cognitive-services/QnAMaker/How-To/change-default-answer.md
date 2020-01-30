---
title: Obtenha resposta padrão - QnA Maker
description: A resposta padrão é devolvida quando não há correspondência com a pergunta. Pode querer alterar a resposta predefinida da resposta padrão.
ms.topic: conceptual
ms.date: 01/10/2020
ms.openlocfilehash: fae5c38fd64435a3fae56862bad04e000916e88b
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843280"
---
# <a name="set-default-answer-for-a-knowledge-base"></a>Definir resposta padrão para uma base de conhecimento

A resposta padrão é devolvida quando não há correspondência com a pergunta. Pode querer alterar a resposta predefinida da resposta padrão.

## <a name="change-default-answer"></a>Resposta de padrão de alteração

1. Vá para o [portal do Azure](https://portal.azure.com) e navegue para o grupo de recursos que representa o serviço QnA Maker que criou.

2. Clique para abrir o **serviço de aplicações**.

    ![No portal do Azure, aceder ao serviço de aplicações para o QnA Maker](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Clique em **as configurações do aplicativo** e editar a **DefaultAnswer** campo para a resposta padrão pretendido. Clique em **Guardar**.

    ![Selecione as definições da aplicação e, em seguida, editar DefaultAnswer para o QnA Maker](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Reinicie o serviço de aplicações

    ![Depois de alterar o DefaultAnswer, reinicie o serviço de aplicações do QnA Maker](../media/qnamaker-faq/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>Passos seguintes

* [Crie um bot com QnA Maker e LUIS](../tutorials/integrate-qnamaker-luis.md)