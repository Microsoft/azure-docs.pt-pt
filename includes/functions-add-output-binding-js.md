---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 098aaba0a357c13a059d3c4042a19715c6b5ad42
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78191101"
---
Adicione o código que utiliza o objeto de ligação de saída `msg` `context.bindings` criar uma mensagem de fila. Adicione este código antes da declaração de `context.res`.

:::code language="javascript" range="7" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/index.js":::

Neste ponto, a sua função deve ser a seguinte:

:::code language="javascript" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/index.js":::