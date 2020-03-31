---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.openlocfilehash: 82d122ed236dc72ced7ebafe2301ef5f1143897f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "76963665"
---
Para maximizar o desempenho, utilize uma conta de armazenamento separada para cada aplicação de função. Isto é particularmente importante quando se tem funções duradouras ou funções desencadeadas pelo Event Hub, que ambos geram um elevado volume de transações de armazenamento. Quando a sua lógica de aplicação interage com o Armazenamento Azure, quer diretamente (utilizando o SDK de Armazenamento) quer através de uma das encadernações de armazenamento, deverá utilizar uma conta de armazenamento dedicada. Por exemplo, se tiver uma função desencadeada pelo Event Hub escrevendo alguns&mdash;dados para o armazenamento de blob, utilize duas contas de armazenamento uma para a aplicação de função e outra para as bolhas que estão a ser armazenadas pela função.