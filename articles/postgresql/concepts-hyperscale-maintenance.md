---
title: Manutenção programada - Base de Dados Azure para PostgreSQL - Hiperescala (Citus)
description: Este artigo descreve a funcionalidade de manutenção programada na Base de Dados Azure para PostgreSQL - Hyperscale (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: dee7257a296f523dbc9040d65fe68c14edf928f6
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107106790"
---
# <a name="scheduled-maintenance-in-azure-database-for-postgresql--hyperscale-citus"></a>Manutenção programada na Base de Dados Azure para PostgreSQL – Hiperescala (Citus)

A Azure Database for PostgreSQL - Hyperscale (Citus) faz manutenção periódica para manter a sua base de dados gerida segura, estável e atualizada.  Durante a manutenção, todos os nós do grupo de servidor recebem novas funcionalidades, atualizações e patches.

As principais características da manutenção programada para a Hiperescala (Citus) são:

* As atualizações são aplicadas ao mesmo tempo em todos os nós do grupo de servidores
* Notificações sobre a próxima manutenção são publicadas no Serviço Azure Health com cinco dias de antecedência
* Normalmente há pelo menos 30 dias entre eventos de manutenção bem-sucedidos para um grupo de servidores
* O dia preferido da semana e a janela de tempo dentro desse dia para o início da manutenção podem ser definidos individualmente para cada grupo de servidores

## <a name="selecting-a-maintenance-window-and-notification-about-upcoming-maintenance"></a>Selecionando uma janela de manutenção e notificação sobre a próxima manutenção

Pode agendar a manutenção durante um dia específico da semana e uma janela de tempo dentro desse dia. Ou pode deixar o sistema escolher um dia e uma janela de tempo para si automaticamente. De qualquer forma, o sistema irá alertá-lo cinco dias antes de executar qualquer manutenção. O sistema também o informará quando a manutenção for iniciada, e quando estiver concluída com sucesso.

As notificações sobre a próxima manutenção programada são publicadas na Azure Service Health e podem ser:

* Enviado por e-mail para um endereço específico
* Enviado por e-mail para um papel de gestor de recursos Azure
* Enviado numa mensagem de texto (SMS) para dispositivos móveis
* Push como uma notificação para uma aplicação do Azure
* Entrega como uma mensagem de voz

Ao especificar preferências para o agendamento de manutenção, pode escolher um dia da semana e uma janela de tempo. Se não especificar, o sistema escolherá os horários entre as 23h e as 7h na hora do grupo de servidores. Pode definir diferentes horários para cada grupo de servidor Hyperscale (Citus) na sua subscrição Azure.

> [!IMPORTANT]
> Normalmente, há pelo menos 30 dias entre eventos de manutenção programados bem sucedidos para um grupo de servidores.
>
> No entanto, no caso de uma atualização de emergência crítica, como uma vulnerabilidade grave, a janela de notificação pode ter menos de cinco dias. A atualização crítica pode ser aplicada ao seu servidor, mesmo se uma manutenção agendada bem-sucedida tiver sido realizada nos últimos 30 dias.

Pode atualizar as definições de agendamento a qualquer momento. Se houver manutenção programada para o seu grupo de servidores Hyperscale (Citus) e atualizar o horário, os eventos existentes continuarão como originalmente programado. A alteração das definições entrará em vigor após a conclusão com sucesso dos eventos existentes.

Se a manutenção falhar ou for cancelada, o sistema criará uma notificação.
Tentará novamente a manutenção de acordo com as definições atuais de agendamento e notificá-lo-á cinco dias antes do próximo evento de manutenção.

## <a name="next-steps"></a>Passos seguintes

* Saiba como [alterar o horário de manutenção](howto-hyperscale-maintenance.md)
* Saiba como [obter notificações sobre a próxima manutenção](../service-health/service-notifications.md) usando a Azure Service Health
* Saiba como [configurar alertas sobre os próximos eventos de manutenção programada](../service-health/resource-health-alert-monitor-guide.md)
