---
title: Base de Dados Azure para PostgreSQL - Hiperescala (Citus) - Manutenção programada - Portal Azure
description: Saiba como configurar as definições de manutenção programadas para uma Base de Dados Azure para PostgreSQL - Hiperescala (Citus) a partir do portal Azure.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: how-to
ms.date: 04/07/2021
ms.openlocfilehash: 04dd37e3e9abbfbec7badb036802e645cc7732b0
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108521"
---
# <a name="manage-scheduled-maintenance-settings-for-azure-database-for-postgresql--hyperscale-citus"></a>Gerir as definições de manutenção programada para a Base de Dados Azure para PostgreSQL – Hyperscale (Citus)

Pode especificar opções de manutenção para cada grupo de servidor Hyperscale (Citus) na sua subscrição Azure. As opções incluem o horário de manutenção e as definições de notificação para eventos de manutenção futuros e terminados.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este guia, precisa:

- Uma [base de dados Azure para o grupo de servidores PostgreSQL - Hyperscale (Citus)](quickstart-create-hyperscale-portal.md)

## <a name="specify-maintenance-schedule-options"></a>Especificar opções de horário de manutenção

1. Na página do grupo do servidor Hyperscale (Citus), no título **Definições,** escolha **Manutenção** para abrir opções de manutenção programadas.
2. O horário padrão (gerido pelo sistema) é um dia aleatório da semana, e uma janela de 30 minutos para manutenção começa entre as 23h e as 7h hora da [região de Azure](https://go.microsoft.com/fwlink/?linkid=2143646)do grupo de servidores . Se pretender personalizar este horário, escolha **o horário personalizado**. Em seguida, pode selecionar um dia preferido da semana e uma janela de 30 minutos para a hora de início da manutenção.

## <a name="notifications-about-scheduled-maintenance-events"></a>Notificações sobre eventos de manutenção programados

Pode utilizar a Azure Service Health para [visualizar notificações](../service-health/service-notifications.md) sobre a manutenção programada no seu grupo de servidores Hyperscale (Citus). Também pode [configurar](../service-health/resource-health-alert-monitor-guide.md) alertas na Azure Service Health para receber notificações sobre eventos de manutenção.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [a manutenção programada na Base de Dados Azure para PostgreSQL – Hyperscale (Citus)](concepts-hyperscale-maintenance.md)
* Incline-se sobre [a Azure Service Health](../service-health/overview.md)
