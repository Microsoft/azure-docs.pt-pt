---
author: baanders
description: incluir ficheiros para verificar atribuição de funções na configuração da Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: e651b02bf72ced58b6cba637a68ace3258514176
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87405600"
---
Uma forma de verificar se conseguiu configurar a tarefa de função com sucesso é visualizar as atribuições de papéis para a instância Azure Digital Twins no [portal Azure](https://portal.azure.com). Aceda à página do portal de [instâncias Azure Digital Twins](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) (pode utilizar este link ou pesquisar na barra de pesquisa do portal) e selecione o nome da instância que pretende verificar. 

Em seguida, veja todas as suas funções atribuídas sob *controlo de acesso (IAM) > atribuições de funções*. O utilizador deve aparecer na lista com um papel de *Azure Digital Twins Owner (Preview)*. 

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Vista das atribuições de funções para uma instância Azure Digital Twins no portal Azure":::