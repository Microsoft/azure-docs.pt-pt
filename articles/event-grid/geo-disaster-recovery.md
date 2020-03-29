---
title: Recuperação geo-desastre na Grelha de Eventos de Azure Microsoft Docs
description: Descreve como a Azure Event Grid suporta automaticamente a recuperação de geodesastres (GeoDR).
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: spelluru
ms.openlocfilehash: 5b5c973a8daa8776efb0909092c569ea46902265
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "66307321"
---
# <a name="server-side-geo-disaster-recovery-in-azure-event-grid"></a>Recuperação de desastregeográfico do lado do servidor na Grelha de Eventos De Azure
A Event Grid tem agora uma recuperação automática de geo-desastres (GeoDR) de meta-dados não só para novos, mas para todos os domínios, tópicos e subscrições de eventos existentes. Se toda uma região do Azure cair, a Rede de Eventos já terá todos os metadados de infraestrutura relacionados com eventos sincronizados com uma região emparelhada. Os teus novos eventos voltarão a fluir sem nenhuma intervenção tua. 

A recuperação de desastres é medida com duas métricas:

- Objetivo do Ponto de [Recuperação (RPO):](https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_Point_Objective)as atas ou horas de dados que podem ser perdidos.
- Objetivo do Tempo de [Recuperação (RTO):](https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_time_objective)as atas de horas de serviço podem estar em baixo.

O failover automático da Rede de Eventos tem RPOs e RTOs diferentes para os seus metadados (subscrições de eventos, etc.) e dados (eventos). Se precisar de especificações diferentes das seguintes, ainda pode implementar o seu próprio cliente falha ao utilizar os apis de [saúde tópicos.](custom-disaster-recovery.md)

## <a name="recovery-point-objective-rpo"></a>Objetivo de ponto de recuperação (RPO)
- **RPO de metadados:** zero minutos. Sempre que um recurso é criado em Event Grid, é instantaneamente replicado em regiões. Quando ocorre uma falha, não se perdem metadados.
- **RPO**de dados : Se o seu sistema estiver saudável e apanhado no tráfego existente no momento da falha regional, o RPO para eventos é de cerca de 5 minutos.

## <a name="recovery-time-objective-rto"></a>Objetivo de tempo de recuperação (RTO)
- **Metadados RTO**: Embora geralmente aconteça muito mais rapidamente, dentro de 60 minutos, a Rede de Eventos começará a aceitar a criação/atualização/exclusão de chamadas para tópicos e subscrições.
- **Data RTO**: Tal como os metadados, geralmente acontece muito mais rapidamente, no entanto, dentro de 60 minutos, a Rede de Eventos começará a aceitar um novo tráfego após uma falha regional.

> [!NOTE]
> O custo dos metadados GeoDR na Grelha de Eventos é: $0.


## <a name="next-steps"></a>Passos seguintes
If you want to implement you own client-side failover logic, see [# Build your own disaster recovery for custom topics in Event Grid](custom-disaster-recovery.md)
