---
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 8/12/2020
ms.author: sngun
ms.openlocfilehash: 42d1e0f056457ba54e0102a4d23f42a81ebea08d
ms.sourcegitcommit: 1fe5127fb5c3f43761f479078251242ae5688386
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90068745"
---
**Como serei notificado do SDK reformado?**

A Microsoft fornecerá um pré-aviso de 12 meses antes do fim do suporte do SDK reformado para facilitar uma transição suave para um SDK suportado. Iremos notificá-lo através de vários canais de comunicação: o portal Azure, atualizações Azure e comunicação direta aos administradores de serviços designados.

**Posso autoritar aplicações usando um Azure Cosmos DB SDK a ser reformado durante o período de 12 meses?** 

Sim, poderás ser autor, implementar e modificar aplicações usando o Azure Cosmos DB SDK reformado durante o período de pré-aviso de 12 meses. Recomendamos que emigre para uma versão mais recente suportada do Azure Cosmos DB SDK durante o período de pré-aviso de 12 meses, conforme apropriado. 

**Após a data da reforma, o que acontece com as aplicações que usam o Azure Cosmos DB SDK não apoiado?** 

Após a data de aposentadoria, a Azure Cosmos DB deixará de fazer correções de bugs, adicionar novas funcionalidades ou fornecer suporte às versões SDK reformadas. Se preferir não fazer upgrade, os pedidos enviados a partir das versões aposentadas do SDK continuarão a ser servidos pelo serviço DB Azure Cosmos. 

**Quais as versões SDK que terão as mais recentes funcionalidades e atualizações?**

Novas funcionalidades e atualizações serão adicionadas apenas à versão menor mais recente da versão SDK suportada mais recentemente. Recomendamos que utilize sempre a versão mais recente para tirar partido de novas funcionalidades, melhorias de desempenho e correções de bugs. Se estiver a usar uma versão antiga e não aposentada do SDK, os seus pedidos para Azure Cosmos DB continuarão a funcionar, mas não terá acesso a quaisquer novas capacidades.  

**O que devo fazer se não conseguir atualizar a minha candidatura antes de uma data limite?**

Recomendamos que atualize para o SDK mais cedo possível. Depois de um SDK ser marcado para a reforma, terá 12 meses para atualizar a sua aplicação. Se não conseguir atualizar até à data da reforma, os pedidos enviados a partir das versões aposentadas do SDK continuarão a ser servidos pela Azure Cosmos DB, pelo que as suas aplicações de execução continuarão a funcionar. Mas o Azure Cosmos DB deixará de fazer correções de bugs, adicionar novas funcionalidades ou fornecer suporte às versões SDK reformadas. 

Se tiver um plano de apoio e necessitar de apoio técnico, [contacte-nos](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) através da apresentação de um bilhete de apoio.
    


