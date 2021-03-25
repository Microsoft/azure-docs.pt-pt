---
title: Manutenção programada - Base de Dados Azure para PostgreSQL - Hiperescala (Citus)
description: Este artigo descreve a funcionalidade de manutenção programada na Base de Dados Azure para PostgreSQL - Hyperscale (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: 0d11ec8815cc0f5082f95c5331321f043e86eece
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105027675"
---
# <a name="scheduled-maintenance-in-azure-database-for-postgresql--hyperscale-citus"></a>Manutenção programada na Base de Dados Azure para PostgreSQL – Hiperescala (Citus)

A Azure Database for PostgreSQL - Hyperscale (Citus) faz manutenção periódica para manter a sua base de dados gerida segura, estável e atualizada.  Durante a manutenção, todos os nós do grupo de servidor recebem novas funcionalidades, atualizações e patches.

As principais características da manutenção programada para a Hiperescala (Citus) são:

* As atualizações são aplicadas ao mesmo tempo em todos os nós do grupo de servidores
* Notificações sobre a próxima manutenção são publicadas no Serviço Azure Health com cinco dias de antecedência
* Normalmente há pelo menos 30 dias entre eventos de manutenção bem-sucedidos para um grupo de servidores

## <a name="notification-about-upcoming-maintenance"></a>Notificação sobre a próxima manutenção

As notificações sobre a próxima manutenção programada são publicadas na Azure Service Health e podem ser:

* Enviado por e-mail para um endereço específico
* Enviado por e-mail para um papel de gestor de recursos Azure
* Enviado numa mensagem de texto (SMS) para dispositivos móveis
* Push como uma notificação para uma aplicação do Azure
* Entrega como uma mensagem de voz

> [!IMPORTANT]
> Normalmente, há pelo menos 30 dias entre eventos de manutenção programados bem sucedidos para um grupo de servidores.
>
> No entanto, no caso de uma atualização de emergência crítica, como uma vulnerabilidade grave, a janela de notificação pode ter menos de cinco dias. A atualização crítica pode ser aplicada ao seu servidor, mesmo se uma manutenção agendada bem-sucedida tiver sido realizada nos últimos 30 dias.

Se a manutenção falhar ou for cancelada, o sistema criará uma notificação.
Tentará novamente a manutenção de acordo com as definições atuais de agendamento e notificá-lo-á cinco dias antes do próximo evento de manutenção.

## <a name="next-steps"></a>Passos seguintes

* Saiba como [obter notificações sobre a próxima manutenção](../service-health/service-notifications.md) usando a Azure Service Health
* Saiba como [configurar alertas sobre os próximos eventos de manutenção programada](../service-health/resource-health-alert-monitor-guide.md)
