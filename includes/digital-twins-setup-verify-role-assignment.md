---
author: baanders
description: incluir ficheiros para verificar atribuição de funções na configuração da Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: e5e4bced12ffe9dc674b25a9a6513218cf8989e4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87098104"
---
Uma forma de verificar se configura com sucesso a tarefa é visualizar as atribuições de papéis para a instância Azure Digital Twins no portal Azure. Na sua página do portal de [instâncias Azure Digital Twins,](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances)selecione o nome do caso que pretende verificar. Em seguida, veja todas as suas funções atribuídas sob *controlo de acesso (IAM) > atribuições de funções*. O utilizador deve aparecer na lista com um papel de *Azure Digital Twins Owner (Preview)*. 

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/verify-role-assignment.png" alt-text="Vista das atribuições de funções para uma instância Azure Digital Twins no portal Azure":::