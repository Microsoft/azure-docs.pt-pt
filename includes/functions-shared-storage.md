---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.openlocfilehash: 82d122ed236dc72ced7ebafe2301ef5f1143897f
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963665"
---
Para maximizar o desempenho, use uma conta de armazenamento separada para cada aplicativo de funções. Isso é particularmente importante quando você tem Durable Functions ou funções disparadas pelo hub de eventos, que geram um alto volume de transações de armazenamento. Quando a lógica do aplicativo interage com o armazenamento do Azure, seja diretamente (usando o SDK de armazenamento) ou por meio de uma das associações de armazenamento, você deve usar uma conta de armazenamento dedicada. Por exemplo, se você tiver uma função disparada por Hub de eventos gravando alguns dados no armazenamento de BLOBs, use duas contas de armazenamento&mdash;uma para o aplicativo de funções e outra para os blobs que estão sendo armazenados pela função.