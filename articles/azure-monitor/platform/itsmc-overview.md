---
title: Visão geral do conector de gestão de serviços de TI
description: Este artigo fornece uma visão geral do Conector de Gestão de Serviços de TI (ITSMC).
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/16/2020
ms.custom: references_regions
ms.openlocfilehash: 93b6160b495b609e23e10b3f709d130792067423
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/29/2020
ms.locfileid: "97803787"
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

-  Crie itens de trabalho na sua ferramenta ITSM, com base nos seus alertas Azure (Alertas métricos, Alertas de Registo de Atividade e Alertas de Registos).
-  Opcionalmente, pode sincronizar o seu incidente e alterar os dados de pedido da sua ferramenta ITSM para um espaço de trabalho Azure Log Analytics.

Para obter informações sobre os termos legais e a política de privacidade, consulte [a Declaração de Privacidade da Microsoft.](https://go.microsoft.com/fwLink/?LinkID=522330&clcid=0x9)

Pode começar a utilizar o ITSMC completando os seguintes passos:

1. [Confiúde o seu AMBIENTE ITSM para aceitar alertas da Azure.](./itsmc-connections.md)
1. [Configure Azure Solução ITSM](./itsmc-definition.md#add-it-service-management-connector)
1. [Configure o conector Azure ITSM para o seu ambiente ITSM.](./itsmc-definition.md#create-an-itsm-connection)
1. [Configure o Grupo de Ação para alavancar o conector ITSM.](./itsmc-definition.md#use-itsmc)

## <a name="next-steps"></a>Passos seguintes

* [Resolver problemas do Conector do ITSM](./itsmc-resync-servicenow.md)