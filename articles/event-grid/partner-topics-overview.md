---
title: Tópicos parceiros da Azure Event Grid
description: Envie eventos de parceiros de eventos de terceiros SaaS e PaaS diretamente para os serviços da Azure com a Azure Event Grid.
services: event-grid
author: femila
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: femila
ms.openlocfilehash: 5327efea8af734c723ba76d1a00b72c08f5c88b9
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84560273"
---
# <a name="partner-topics-in-azure-event-grid-preview"></a>Tópicos de parceiro na Grelha de Eventos Azure (pré-visualização)
Ao utilizar tópicos de parceiros, pode ligar fontes de eventos de terceiros diretamente à Azure Event Grid. Esta integração permite-lhe subscrever eventos de parceiros da mesma forma que subscreve eventos dos serviços Azure. 

## <a name="available-partners"></a>Parceiros disponíveis
O primeiro parceiro disponível através dos tópicos parceiros da Event Grid é o Auth0. Pode utilizar o [tópico parceiro Auth0](auth0-overview.md) para ligar as suas contas Auth0 e Azure. A integração permite-lhe reagir, registar e monitorizar eventos Auth0 em tempo real.

[Para experimentá-lo, inscreva-se](auth0-how-to.md)na sua conta Auth0 e crie uma integração de Grade de Eventos. Depois de selecionar **Criar** em Auth0, vê um tópico Auth0 pendente na sua conta Azure. **Selecione Ativar**e pode criar subscrições de Grade de Eventos para rotas, filtros e entregar os seus eventos tal como qualquer outra fonte de evento.

## <a name="pricing"></a>Preços
Os tópicos dos parceiros são cobrados à mesma taxa de operação que os tópicos do sistema.

## <a name="limits"></a>Limites
Os tópicos dos parceiros estão em visualização pública. Durante a pré-visualização pública, os tópicos dos parceiros estão sujeitos aos [mesmos limites](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#event-grid-limits) que os tópicos do sistema e tópicos personalizados.

## <a name="how-do-i-become-an-event-grid-partner"></a>Como posso tornar-me um parceiro de Grade de Eventos?
A infraestrutura criada para apoiar este lançamento torna fácil e rápido para os novos parceiros integrarem as suas capacidades de eventos com a Event Grid. Para mais informações, consulte a [documentação do parceiro no embarque.](partner-onboarding-overview.md)

## <a name="next-steps"></a>Passos seguintes

- [Tópico parceiro Auth0](auth0-overview.md)
- [Como usar o tópico parceiro Auth0](auth0-how-to.md)
- [Torne-se um parceiro de Grade de Eventos](partner-onboarding-overview.md)