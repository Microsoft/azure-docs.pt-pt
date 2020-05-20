---
title: Tópicos parceiros da Azure Event Grid
description: Envie eventos de parceiros de eventos de terceiros Grid SaaS e PaaS diretamente para os serviços Azure com a Azure Event Grid.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: babanisa
ms.openlocfilehash: 4546874b8a6cd8a7e45c3e6957a5181d66c7433f
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83691125"
---
# <a name="partner-topics-in-azure-event-grid-preview"></a>Tópicos de Parceiros na Grelha de Eventos Azure (Pré-visualização)
Os Tópicos do Parceiro permitem-lhe ligar fontes de eventos de terceiros diretamente à Grelha de Eventos. Esta integração permite-lhe subscrever eventos de parceiros da mesma forma que subscreve eventos dos serviços Azure. 

## <a name="available-partners"></a>Parceiros Disponíveis
O primeiro parceiro disponível através do Event Grid Partner Topics é o Auth0. O tópico parceiro [Auth0](auth0-overview.md) permite-lhe ligar as suas contas Auth0 e Azure. A integração para reagir, registar e monitorizar eventos auth0 em tempo real.

[Experimente hoje,](auth0-how-to.md) iniciando sessão na sua conta Auth0 e criando uma integração da Rede de Eventos. Assim que clicar em criar em Auth0, verá um Tópico Auth0 pendente na sua conta Azure. Clique em ativar e pode criar Subscrições de Eventos, rota, filtrar e entregar os seus eventos tal como faz com qualquer outra fonte de evento.

## <a name="pricing"></a>Preços
Os tópicos dos parceiros são cobrados ao mesmo ritmo de funcionamento dos tópicos do sistema.

## <a name="limits"></a>Limites
Os Tópicos de Parceiro estão em Pré-Visualização Pública. Durante a pré-visualização pública, os tópicos dos Parceiros estão sujeitos aos [mesmos limites](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#event-grid-limits) que os tópicos do sistema e tópicos personalizados.

## <a name="how-do-i-become-an-event-grid-partner"></a>Como me tornar um parceiro da Grelha de Eventos?
A infraestrutura criada para apoiar este lançamento torna fácil e rápido para novos parceiros integraras as suas capacidades de eventos com a Event Grid. Leia a [documentação sobre](partner-onboarding-overview.md) o embarque do parceiro para mais informações.

## <a name="next-steps"></a>Passos seguintes

- [Tópico parceiro auth0](auth0-overview.md)
- [Como usar o Tópico do Parceiro Auth0](auth0-how-to.md)
- [Torne-se um parceiro da Grelha de Eventos](partner-onboarding-overview.md)