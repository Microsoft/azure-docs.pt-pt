---
title: Recuperação de desastres de Georreplicação no Azure Event Grid | Documentos da Microsoft
description: Descreve como o Azure Event Grid suporta recuperação de desastre georredundante (GeoDR) automaticamente.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: spelluru
ms.openlocfilehash: 5b5c973a8daa8776efb0909092c569ea46902265
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66307321"
---
# <a name="server-side-geo-disaster-recovery-in-azure-event-grid"></a>Recuperação de desastres de georreplicação do lado do servidor no Azure Event Grid
Grelha de eventos tem agora uma recuperação de desastre geográfico automática (GeoDR) de metadados não apenas para a nova, mas todos os domínios existentes, tópicos e subscrições de eventos. Se a toda uma região do Azure ficar inativo, o Event Grid será já tem todos os metadados de infraestrutura relacionada com eventos sincronizados com uma região emparelhada. Os novos eventos começará a fluir novamente sem qualquer intervenção por si. 

Recuperação após desastre é medida com duas métricas:

- [Objetivo de ponto de recuperação (RPO)](https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_Point_Objective): os minutos ou horas de dados que podem ser perdidas.
- [Objetivo de tempo de recuperação (RTO)](https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_time_objective): os minutos de horas, o serviço pode estar inativo.

A ativação pós-falha automática do Event Grid tem diferentes RpO e Rto para os seus metadados (subscrições de eventos etc.) e dados (eventos). Se precisar de especificação diferente entre as seguintes, pode ainda implementar sua própria [lado do cliente efetuar a ativação pós-falha utilizando o tópico apis de estado de funcionamento](custom-disaster-recovery.md).

## <a name="recovery-point-objective-rpo"></a>Objetivo de ponto de recuperação (RPO)
- **Metadados RPO**: zero minutos. Sempre que um recurso é criado no Event Grid, de forma instantânea é replicado em várias regiões. Quando ocorre uma ativação pós-falha, sem metadados são perdidos.
- **Dados RPO**: Se o seu sistema estiver em bom estado de funcionamento e tiver apanhado o tráfego existente no momento da ativação pós-falha regional, o RPO para eventos será de cinco minutos.

## <a name="recovery-time-objective-rto"></a>Objetivo de tempo de recuperação (RTO)
- **RTO de metadados**: Embora em geral, ele ocorre muito mais rapidamente, no prazo de 60 minutos, o Event Grid será iniciada aceitar chamadas de criar/atualizar/eliminar para tópicos e subscrições.
- **Dados RTO**: Como metadados, isso geralmente acontece muito mais rapidamente, no entanto no prazo de 60 minutos, Event Grid começará a aceitar novo tráfego após uma ativação pós-falha regional.

> [!NOTE]
> O custo para metadados GeoDR no Event Grid é: US $0.


## <a name="next-steps"></a>Passos Seguintes
Para implementar a lógica do próprio de lado do cliente de ativação pós-falha, consulte [# criar seu próprio recuperação após desastre para tópicos personalizados no Event Grid](custom-disaster-recovery.md)
