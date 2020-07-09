---
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 11/09/2018
ms.author: sngun
ms.openlocfilehash: 99dddd86c9348c9791d3012b382298bb020e63c9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "67184010"
---
**1. Como é que os clientes serão notificados da reforma do SDK?**

A Microsoft fornecerá uma notificação antecipada de 12 meses para o fim do suporte do SDK reformado, de forma a facilitar uma transição suave para um SDK suportado. Além disso, os clientes serão notificados através de vários canais de comunicação – Portal de Gestão Azure, Centro de Desenvolvimento, blog post e comunicação direta aos administradores de serviços atribuídos.

**2. Os clientes podem autoritar aplicações utilizando um Azure Cosmos DB SDK reformado reformado durante o período de 12 meses?** 

Sim, os clientes terão acesso total ao autor, implementar e modificar aplicações usando o "a ser" reformado Azure Cosmos DB SDK durante o período de carência de 12 meses. Durante o período de carência de 12 meses, os clientes são aconselhados a migrar para uma versão mais recente suportada da Azure Cosmos DB SDK, conforme apropriado.

**3. Os clientes podem autoritar e modificar aplicações utilizando um Azure Cosmos DB SDK reformado após o período de notificação de 12 meses?**

Após o período de notificação de 12 meses, o SDK será retirado. Qualquer acesso ao Azure Cosmos DB através de uma aplicação que utilize um SDK reformado não será permitido pela plataforma DB Azure Cosmos. Além disso, a Microsoft não fornecerá suporte ao cliente no SDK reformado.

**4. O que acontece com as aplicações de execução do Cliente que estão a utilizar a versão DDK DB SDK não suportada da Azure Cosmos?**

Quaisquer tentativas feitas para ligar ao serviço DB da Azure Cosmos com uma versão SDK reformada serão rejeitadas. 

**5. Serão aplicadas novas funcionalidades e funcionalidades a todos os SDKs não reformados?**

Novas funcionalidades e funcionalidades apenas serão adicionadas às novas versões. Se estiver a utilizar uma versão antiga e não aposentada do SDK, os seus pedidos para Azure Cosmos DB continuarão a funcionar como anteriores, mas não terás acesso a quaisquer novas capacidades.  

**6. O que devo fazer se não conseguir atualizar o meu pedido antes de uma data limite?**

Recomendamos que atualize para o SDK mais cedo possível. Uma vez que um SDK tenha sido marcado para a aposentadoria, terá 12 meses para atualizar a sua aplicação. Se, por qualquer motivo, não conseguir completar a atualização da sua aplicação dentro deste prazo, contacte a [Equipa DB cosmos](mailto:askcosmosdb@microsoft.com) e solicite a sua assistência antes da data limite.

