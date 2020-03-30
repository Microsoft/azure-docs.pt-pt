---
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 11/09/2018
ms.author: sngun
ms.openlocfilehash: 99dddd86c9348c9791d3012b382298bb020e63c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67184010"
---
**1. Como é que os clientes serão notificados do SDK reformado?**

A Microsoft fornecerá uma notificação antecipada de 12 meses para o fim do suporte do SDK reformado, a fim de facilitar uma transição suave para um SDK suportado. Além disso, os clientes serão notificados através de vários canais de comunicação – Portal de Gestão Azure, Centro de Desenvolvimento, post de blog e comunicação direta aos administradores de serviços atribuídos.

**2. Os clientes podem ser autores de aplicações utilizando um Azure Cosmos DB SDK reformado durante o período de 12 meses?** 

Sim, os clientes terão acesso total ao autor, implementam e modificam aplicações usando o "a ser" aposentado Azure Cosmos DB SDK durante o período de 12 meses de carência. Durante o período de graça de 12 meses, os clientes são aconselhados a migrar para uma versão mais recente suportada do Azure Cosmos DB SDK conforme apropriado.

**3. Os clientes podem autor e modificar aplicações utilizando um Azure Cosmos DB SDK reformado após o período de notificação de 12 meses?**

Após o período de notificação de 12 meses, o SDK será retirado. Qualquer acesso ao Azure Cosmos DB por uma aplicação que utilize um SDK reformado não será permitido pela plataforma Azure Cosmos DB. Além disso, a Microsoft não fornecerá apoio ao cliente no SDK reformado.

**4. O que acontece às aplicações de execução do Cliente que estão a usar a versão SDK DB SDK do Azure Cosmos não suportada?**

Quaisquer tentativas feitas para se ligar ao serviço Azure Cosmos DB com uma versão SDK aposentada serão rejeitadas. 

**5. Serão aplicadas novas funcionalidades e funcionalidades a todos os SDKs não aposentados?**

Novas funcionalidades e funcionalidades só serão adicionadas às novas versões. Se estiver a utilizar uma versão antiga e não aposentada do SDK, os seus pedidos ao Azure Cosmos DB continuarão a funcionar como anteriores, mas não terá acesso a novas capacidades.  

**6. O que devo fazer se não conseguir atualizar a minha candidatura antes de uma data limite?**

Recomendamos que faça o upgrade para o mais recente SDK o mais cedo possível. Uma vez marcado um SDK para a reforma, terá 12 meses para atualizar a sua aplicação. Se, por qualquer motivo, não conseguir completar a atualização da sua aplicação dentro deste prazo, contacte a [Equipa DB cosmos](mailto:askcosmosdb@microsoft.com) e solicite a sua assistência antes da data limite.

