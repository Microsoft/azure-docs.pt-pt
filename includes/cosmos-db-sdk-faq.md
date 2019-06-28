---
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 11/09/2018
ms.author: sngun
ms.openlocfilehash: 99dddd86c9348c9791d3012b382298bb020e63c9
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184010"
---
**1. Como serão clientes notificado sobre o SDK obsoletos?**

A Microsoft fornecerá 12 meses notificação antecipada para o fim do suporte do SDK obsoletos para facilitar uma transição tranquila para um SDK suportado. Além disso, os clientes serão notificados por meio de vários comunicação canais – Portal de gestão do Azure, Developer Center, postagem de blog, e atribuída de comunicação direta para os administradores de serviços.

**2. Podem aos clientes criar aplicativos usando um "restaurar" extinto SDK do Azure Cosmos DB durante o período de 12 meses?** 

Sim, os clientes terão acesso total para criar, implementar e modificar aplicativos usando o "restaurar" extinto SDK do Azure Cosmos DB durante o período de tolerância de 12 meses. Durante o período de tolerância de 12 meses, os clientes são aconselhados a migrar para uma versão suportada mais recente do SDK do Azure Cosmos DB, conforme apropriado.

**3. Os clientes podem criar e modificar aplicativos usando um SDK do Azure Cosmos DB extinto após o período de notificação de 12 meses?**

Após o período de notificação de 12 meses, o SDK será retirado. Qualquer acesso ao Azure Cosmos DB por um aplicativos com um SDK extinto não será permitido pela plataforma do Azure Cosmos DB. Além disso, o Microsoft não irá fornecer suporte ao cliente sobre o SDK extinto.

**4. O que acontece ao cliente da execução de aplicações que estão a utilizar a versão não suportada do SDK do Azure Cosmos DB?**

Quaisquer tentativas de estabelecer ligação ao serviço do Azure Cosmos DB com uma versão SDK extinto serão rejeitadas. 

**5. Serão aplicadas novos recursos e funcionalidades para todos os SDKs de não descontinuadas?**

Novos recursos e funcionalidades apenas serão adicionadas para novas versões. Se estiver a utilizar uma versão antiga, descontinuadas, do SDK seus pedidos ao Azure Cosmos DB irão funcionar como anterior mas não terá acesso a quaisquer novos recursos.  

**6. O que devo fazer se eu não é possível atualizar o meu aplicativo antes de uma data de truncado?**

Recomendamos que Atualize para o SDK mais recente mais cedo possível. Depois de um SDK tem sido marcado para descontinuação terá 12 meses para atualizar a sua aplicação. Se, por algum motivo, não é possível concluir a atualização da aplicação durante este período de tempo, entre em contato com o [Cosmos DB equipe](mailto:askcosmosdb@microsoft.com) e sua assistência antes da data limite do pedido.

