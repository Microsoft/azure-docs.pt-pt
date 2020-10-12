---
author: baanders
description: incluir ficheiros para verificar atribuição de funções na configuração da Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: fdb3bd034d93e623037be9fa0721a805924af5c3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88864775"
---
Uma forma de verificar se conseguiu configurar a tarefa de função com sucesso é visualizar as atribuições de papéis para a instância Azure Digital Twins no [portal Azure](https://portal.azure.com). Aceda à sua instância Azure Digital Twins no portal Azure (pode pesquisar na página de instâncias da [Azure Digital Twins](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) ou pesquisar o seu nome na barra de pesquisa do portal).

Em seguida, veja todas as suas funções atribuídas sob *controlo de acesso (IAM) > atribuições de funções*. O utilizador deve aparecer na lista com um papel de *Azure Digital Twins Owner (Preview)*. 

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Vista das atribuições de funções para uma instância Azure Digital Twins no portal Azure":::