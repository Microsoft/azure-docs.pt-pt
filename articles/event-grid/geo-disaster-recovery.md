---
title: Recuperação de desastres geo na grelha de eventos Azure | Microsoft Docs
description: Descreve como a Azure Event Grid suporta automaticamente a recuperação de geo desastres (GeoDR).
ms.topic: conceptual
ms.date: 11/19/2020
ms.openlocfilehash: 10beaf0ae25f3ed9b7bcda5961a89494b18b84d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94980858"
---
# <a name="server-side-geo-disaster-recovery-in-azure-event-grid"></a>Recuperação de desastres geo-side do servidor na Grelha de Eventos Azure
A Event Grid tem agora uma recuperação automática de geo-desastres (GeoDR) de meta-dados não só para novos, mas para todos os domínios, tópicos e subscrições de eventos existentes. Se toda uma região do Azure cair, a Event Grid já terá todos os seus metadados de infraestrutura relacionados com eventos sincronizados numa região emparelhada. Os seus novos eventos voltarão a fluir sem intervenção tua. 

A recuperação de desastres é medida com duas métricas:

- Objetivo do Ponto de Recuperação (RPO): as atas ou horas de dados que podem ser perdidos.
- Objetivo do tempo de recuperação (RTO): as minutos ou horas em que o serviço pode estar em baixo.

A falha automática da Event Grid tem diferentes RPOs e RTOs para os seus metadados (subscrições de eventos, etc.) e dados (eventos). Se precisar de especificações diferentes das seguintes, ainda pode implementar o seu próprio fracasso do lado do [cliente sobre a utilização do tópico health apis](custom-disaster-recovery.md).

## <a name="recovery-point-objective-rpo"></a>Objetivo de ponto de recuperação (RPO)
- **Metadados RPO:** zero minutos. Sempre que um recurso é criado em Event Grid, é replicado instantaneamente em regiões. Quando ocorre uma falha, não se perdem metadados.
- **Dados RPO**: Se o seu sistema estiver saudável e estiver a ser apanhado no tráfego existente no momento da falha regional, o RPO para eventos é de cerca de 5 minutos.

## <a name="recovery-time-objective-rto"></a>Objetivo de tempo de recuperação (RTO)
- **Metadados RTO**: Embora geralmente aconteça muito mais rapidamente, dentro de 60 minutos, a Grade de Evento começará a aceitar chamadas de criar/atualizar/eliminar chamadas para tópicos e subscrições.
- **Dados RTO**: Tal como os metadados, geralmente acontece muito mais rapidamente, no entanto, dentro de 60 minutos, a Grade de Eventos começará a aceitar novo tráfego após uma falha regional.

> [!NOTE]
> O custo para os metadados GeoDR na Grelha de Eventos é: $0.


## <a name="next-steps"></a>Passos seguintes
Se quiser implementar a sua própria lógica de failover do lado do cliente, consulte [# Construa a sua própria recuperação de desastres para tópicos personalizados na Grade de Eventos](custom-disaster-recovery.md)
