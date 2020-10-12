---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.openlocfilehash: 82d122ed236dc72ced7ebafe2301ef5f1143897f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "76963665"
---
Para maximizar o desempenho, utilize uma conta de armazenamento separada para cada aplicação de função. Isto é particularmente importante quando você tem funções duráveis ou Event Hub desencadeado funções, que ambos geram um grande volume de transações de armazenamento. Quando a sua lógica de aplicação interage com o Azure Storage, quer diretamente (utilizando o SDK de armazenamento) quer através de uma das ligações de armazenamento, deverá utilizar uma conta de armazenamento dedicada. Por exemplo, se tiver uma função desencadeada pelo Event Hub escrevendo alguns dados para o armazenamento de bolhas, utilize duas contas &mdash; de armazenamento, uma para a aplicação de função e outra para as bolhas armazenadas pela função.