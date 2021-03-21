---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2021
ms.author: glenga
ms.openlocfilehash: 121b10cc568cce089c90e66b9c6f292c74f4acbe
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99809574"
---
## <a name="run-the-function-in-azure"></a>Executar a função em Azure

1. De volta ao **Azure: Funções** na barra lateral, expandir a sua subscrição, a sua nova aplicação de funções e **funções**. Clique à direita (Windows) ou <kbd>Ctrl -</kbd> clique (macOS) na `HttpExample` função e escolha **Executar Função Agora...**.

    :::image type="content" source="media/functions-vs-code-run-remote/execute-function-now.png" alt-text="Executar função agora em Azure a partir do Código do Estúdio Visual":::

1. In **Enter request body** você vê o valor do corpo da mensagem de pedido de `{ "name": "Azure" }` . Prima Insira para enviar esta mensagem de pedido para a sua função.  

1. Quando a função executa em Azure e devolve uma resposta, uma notificação é levantada no Código do Estúdio Visual.
