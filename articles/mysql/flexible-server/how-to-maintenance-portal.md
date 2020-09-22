---
title: Base de Dados Azure para MySQL - Servidor Flexível (Pré-visualização) - Manutenção programada - Portal Azure
description: Saiba como configurar as definições de manutenção programadas para uma Base de Dados Azure para o MySQL - Servidor flexível a partir do portal Azure.
author: niklarin
ms.author: nlarin
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 6c3ad5fff6958bce72f876e3ff4701cf081abe60
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90940484"
---
# <a name="manage-scheduled-maintenance-settings-for-azure-database-for-mysql--flexible-server"></a>Gerir as definições de manutenção programada para Azure Database para MySQL – Servidor flexível
 
Pode especificar opções de manutenção para cada servidor Flexível na sua subscrição Azure. As opções incluem o horário de manutenção e as definições de notificação para eventos de manutenção futuros e terminados.

> [!IMPORTANT]
> A base de dados Azure para o MySQL - Servidor flexível está em pré-visualização.
 
## <a name="prerequisites"></a>Pré-requisitos
Para completar este guia, precisa:
- Uma [base de dados Azure para o MySQL - Servidor flexível](quickstart-create-server-portal.md)
 
## <a name="specify-maintenance-schedule-options"></a>Especificar opções de horário de manutenção
 
1. Na página do servidor MySQL, no título **Definições,** escolha **Manutenção** para abrir as opções de manutenção programadas.
2. O horário padrão (gerido pelo sistema) é um dia aleatório da semana, e uma janela de 60 minutos para manutenção começa entre as 23h e as 7h hora do servidor local. Se pretender personalizar este horário, escolha **o horário personalizado**. Em seguida, pode selecionar um dia preferido da semana e uma janela de 60 minutos para a hora de início da manutenção.
 
## <a name="notifications-about-scheduled-maintenance-events"></a>Notificações sobre eventos de manutenção programados
 
Pode utilizar a Azure Service Health para [visualizar notificações](/azure/service-health/service-notifications.md) sobre o futuro e realizar manutenção programada no seu servidor Flexível. Também pode [configurar](/azure/service-health/resource-health-alert-monitor-guide.md) alertas na Azure Service Health para receber notificações sobre eventos de manutenção.
 
## <a name="next-steps"></a>Passos seguintes  
 
* Saiba mais sobre [manutenção programada na Base de Dados Azure para o MySQL – Servidor flexível](concepts-maintenance.md)
* Incline-se sobre [a Azure Service Health](/azure/service-health/overview.md)
