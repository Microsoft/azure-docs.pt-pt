---
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 8/12/2020
ms.author: sngun
ms.openlocfilehash: 5c5ed4e1bbe54c642202c19e1beb61de94b4f751
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88515245"
---
**1. Como é que os clientes serão notificados da reforma do SDK?**

A Microsoft fornecerá uma notificação antecipada de 12 meses para o fim do suporte do SDK reformado, de forma a facilitar uma transição suave para um SDK suportado. Além disso, os clientes serão notificados através de vários canais de comunicação – portal Azure, Azure Updates e comunicação direta aos administradores de serviços atribuídos.

**2. Os clientes podem autoritar aplicações utilizando um Azure Cosmos DB SDK reformado reformado durante o período de 12 meses?** 

Sim, os clientes terão acesso total ao autor, implementar e modificar aplicações usando o "a ser" reformado Azure Cosmos DB SDK durante o período de pré-aviso de 12 meses. Durante o período de pré-aviso de 12 meses, os clientes são aconselhados a migrar para uma versão mais recente suportada da Azure Cosmos DB SDK, conforme apropriado. 

**3. Após a data da reforma, o que acontece com as aplicações que estão a utilizar o Azure Cosmos DB SDK não suportado?** 

Após a data de aposentadoria, a Azure Cosmos DB deixará de fazer correções de bugs, adicionar novas funcionalidades e fornecer suporte às versões SDK reformadas. Se preferir não fazer upgrade, os pedidos enviados a partir das versões aposentadas do SDK continuarão a ser servidos pelo serviço DB Azure Cosmos. 

**4. Quais as versões SDK que terão as mais recentes funcionalidades e atualizações?**

Novas funcionalidades e atualizações só serão adicionadas à versão menor mais recente da mais recente versão SDK suportada. Recomenda-se sempre utilizar a versão mais recente para tirar partido de novas funcionalidades, melhorias de desempenho e correções de bugs. Se estiver a utilizar uma versão antiga e não aposentada do SDK, os seus pedidos para Azure Cosmos DB continuarão a funcionar, mas não terá acesso a quaisquer novas capacidades.  

**5. O que devo fazer se não conseguir atualizar o meu pedido antes de uma data limite?**

Recomendamos que atualize para o SDK mais cedo possível. Uma vez que um SDK tenha sido marcado para a aposentadoria, terá 12 meses para atualizar a sua aplicação. Se não conseguir atualizar até à data de aposentadoria, os pedidos enviados a partir das versões aposentadas do SDK continuarão a ser servidos pela Azure Cosmos DB, pelo que as suas aplicações de execução continuarão a funcionar. No entanto, a Azure Cosmos DB deixará de fazer correções de bugs, adicionar novas funcionalidades e fornecer suporte às versões SDK reformadas. 

Se tiver um plano de apoio e necessitar de apoio técnico, [contacte-nos](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) através da apresentação de um bilhete de apoio.
    


