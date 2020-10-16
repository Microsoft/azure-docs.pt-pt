---
title: Tópicos parceiros da Azure Event Grid
description: Envie eventos de parceiros de eventos de terceiros SaaS e PaaS diretamente para os serviços da Azure com a Azure Event Grid.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: e56055c55a3b30c5d13736b9838257f3c0bbec10
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87831911"
---
# <a name="partner-topics-in-azure-event-grid-preview"></a>Tópicos de parceiro na Grelha de Eventos Azure (pré-visualização)
Ao utilizar tópicos de parceiros, pode ligar fontes de eventos de terceiros diretamente à Azure Event Grid. Esta integração permite-lhe subscrever eventos de parceiros da mesma forma que subscreve eventos dos serviços Azure. 

## <a name="available-partners"></a>Parceiros disponíveis
O primeiro parceiro disponível através dos tópicos parceiros da Event Grid é o Auth0. Pode utilizar o [tópico parceiro Auth0](auth0-overview.md) para ligar as suas contas Auth0 e Azure. A integração permite-lhe reagir, registar e monitorizar eventos Auth0 em tempo real.

[Para experimentá-lo, inscreva-se](auth0-how-to.md)na sua conta Auth0 e crie uma integração de Grade de Eventos. Depois de selecionar **Criar** em Auth0, vê um tópico Auth0 pendente na sua conta Azure. **Selecione Ativar**e pode criar subscrições de Grade de Eventos para rotas, filtros e entregar os seus eventos tal como qualquer outra fonte de evento.

## <a name="pricing"></a>Preços
Os tópicos dos parceiros são cobrados à mesma taxa de operação que os tópicos do sistema.

## <a name="limits"></a>Limites
Os tópicos dos parceiros estão em visualização pública. Durante a pré-visualização pública, os tópicos dos parceiros estão sujeitos aos [mesmos limites](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits) que os tópicos do sistema e tópicos personalizados.

## <a name="how-do-i-become-an-event-grid-partner"></a>Como posso tornar-me um parceiro de Grade de Eventos?
A infraestrutura criada para apoiar este lançamento torna fácil e rápido para os novos parceiros integrarem as suas capacidades de eventos com a Event Grid. Para mais informações, consulte a [documentação do parceiro no embarque.](partner-onboarding-overview.md)

## <a name="next-steps"></a>Passos seguintes

- [Tópico parceiro Auth0](auth0-overview.md)
- [Como usar o tópico parceiro Auth0](auth0-how-to.md)
- [Torne-se um parceiro de Grade de Eventos](partner-onboarding-overview.md)