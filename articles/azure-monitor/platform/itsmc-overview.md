---
title: Visão geral do conector de gestão de serviços de TI
description: Este artigo fornece uma visão geral do Conector de Gestão de Serviços de TI (ITSMC).
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/16/2020
ms.custom: references_regions
ms.openlocfilehash: 93759cf239a2e7ef79c719c83299740ea3722130
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97614562"
---
# <a name="it-service-management-connector-overview"></a>Visão geral do conector de gestão de serviços de TI

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

O It Service Management Connector (ITSMC) permite-lhe ligar o Azure a um produto ou serviço de Gestão de Serviços de TI suportado (ITSM).

Serviços Azure como o Azure Log Analytics e o Azure Monitor fornecem ferramentas para detetar, analisar e resolver problemas com os seus recursos Azure e não-Azure. Mas os itens de trabalho relacionados com um problema normalmente residem num produto ou serviço ITSM. O ITSMC fornece uma ligação bidis entre as ferramentas Azure e ITSM para ajudá-lo a resolver problemas mais rapidamente.

## <a name="configuration-steps"></a>Passos de configuração

A ITSMC suporta ligações com as seguintes ferramentas ITSM:

-   ServiceNow
-   Gestor de serviços do Centro de Sistema
-   Provance
-   Cherwell

   >[!NOTE]
> Propomos aos nossos clientes Cherwell e Provance que utilizem a [ação Webhook](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#webhook) para cherwell e provance como outra solução para a integração.

Com o ITSMC, pode:

-  Crie itens de trabalho na sua ferramenta ITSM, com base nos seus alertas Azure (alertas métricos, alertas de registo de atividade e alertas de Log Analytics).
-  Opcionalmente, pode sincronizar o seu incidente e alterar os dados de pedido da sua ferramenta ITSM para um espaço de trabalho Azure Log Analytics.

Para obter informações sobre os termos legais e a política de privacidade, consulte [a Declaração de Privacidade da Microsoft.](https://go.microsoft.com/fwLink/?LinkID=522330&clcid=0x9)

Pode começar a utilizar o ITSMC completando os seguintes passos:

1. [Ligue os produtos/serviços ITSM com o It Service Management Connector.](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-connections)
2. [Adicione ITSMC.](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-overview#add-it-service-management-connector)
3. [Criar uma ligação ITSM.](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-overview#create-an-itsm-connection)
4. [Use a ligação.](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-overview#use-itsmc)

## <a name="next-steps"></a>Passos seguintes

[Adicione produtos/serviços ITSM ao Conector de Gestão de Serviços de TI](./itsmc-connections.md)
