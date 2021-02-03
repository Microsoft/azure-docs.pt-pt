---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2021
ms.author: glenga
ms.openlocfilehash: 4b15fec0f22db740bbd7c24fcc0acf2ad1a2d1cd
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493514"
---
## <a name="run-the-function-in-azure"></a>Executar a função em Azure

1. De volta ao **Azure: Área de funções** na barra lateral, expandir funções **de projeto**  >  **locais**. Clique à direita (Windows) ou <kbd>Ctrl -</kbd> clique (macOS) na `HttpExample` função e escolha **Executar Função Agora...**.

    :::image type="content" source="media/functions-vs-code-run-remote/execute-function-now.png" alt-text="Executar função agora em Azure a partir do Código do Estúdio Visual":::

1. In **Enter request body** você vê o valor do corpo da mensagem de pedido de `{ "name": "Azure" }` . Prima Insira para enviar esta mensagem de pedido para a sua função.  

1. Quando a função executa em Azure e devolve uma resposta, uma notificação é levantada no Código do Estúdio Visual.
