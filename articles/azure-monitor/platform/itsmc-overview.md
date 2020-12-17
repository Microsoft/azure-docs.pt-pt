---
title: Visão geral do conector de gestão de serviços de TI
description: Este artigo fornece uma visão geral do Conector de Gestão de Serviços de TI (ITSMC).
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/16/2020
ms.custom: references_regions
ms.openlocfilehash: aaaeb23ef24f0d8a0fa4b38139fed57cda6fa63d
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657044"
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

1. [Ligue os produtos/serviços ITSM com o It Service Management Connector.](./itsmc-connections.md)
1. [Adicione ITSMC.](/.itsmc-definition.md#add-it-service-management-connector)
1. [Criar uma ligação ITSM.](./itsmc-definition.md#create-an-itsm-connection)
1. [Use a ligação.](./itsmc-definition.md#use-itsmc)

## <a name="next-steps"></a>Passos seguintes

[Adicione produtos/serviços ITSM ao Conector](./itsmc-connections.md) 
 de Gestão de Serviços de TI [Adicionar conector](./itsmc-definition.md) 
 ITSM [Problemas de resolução de problemas no conector ITSM](./itsmc-resync-servicenow.md)