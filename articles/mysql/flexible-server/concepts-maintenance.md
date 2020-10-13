---
title: Manutenção programada - Base de Dados Azure para MySQL - Servidor flexível
description: Este artigo descreve a funcionalidade de manutenção programada na Base de Dados Azure para o MySQL - Servidor Flexível.
author: niklarin
ms.author: nlarin
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: a2e99440a7c8f33eee9d3c9fe2276ac3868ff4b6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91331765"
---
# <a name="scheduled-maintenance-in-azure-database-for-mysql--flexible-server"></a>Manutenção agendada na Base de Dados do Azure para MySQL – Servidor flexível

Azure Database for MySQL - Servidor flexível realiza manutenção periódica para manter a sua base de dados gerida segura, estável e atualizada. Durante a manutenção, o servidor obtém novas funcionalidades, atualizações e patches.

> [!IMPORTANT]
> A base de dados Azure para o MySQL - Servidor flexível está em pré-visualização.

## <a name="select-a-maintenance-window"></a>Selecione uma janela de manutenção

Pode agendar a manutenção durante um dia específico da semana e uma janela de tempo dentro desse dia. Ou pode deixar o sistema escolher um dia e um tempo de janela para si automaticamente. De qualquer forma, o sistema irá alertá-lo cinco dias antes de executar qualquer manutenção. O sistema também o informará quando a manutenção for iniciada e quando estiver concluída com sucesso.

As notificações sobre a próxima manutenção programada podem ser:

* Enviado por e-mail para um endereço específico
* Enviado por e-mail para um papel de gestor de recursos Azure
* Enviado numa mensagem de texto (SMS) para dispositivos móveis
* Empurrado como uma notificação para uma aplicação Azure
* Entregue como uma mensagem de voz

Ao especificar preferências para o agendamento de manutenção, pode escolher um dia da semana e uma janela de tempo. Se não especificar, o sistema escolherá os horários entre 23:00 e as 07:00 no horário da região do servidor. Pode definir diferentes horários para cada servidor flexível na sua subscrição Azure.

> [!IMPORTANT]
> Normalmente, há, pelo menos, 30 dias entre eventos de manutenção agendada bem-sucedidos para um servidor.
>
> No entanto, no caso de uma atualização de emergência crítica, como uma vulnerabilidade grave, a janela de notificação pode ter menos de cinco dias. A atualização crítica pode ser aplicada ao seu servidor, mesmo se uma manutenção agendada bem-sucedida tiver sido realizada nos últimos 30 dias.

Pode atualizar as definições de agendamento a qualquer momento. Se houver uma manutenção programada para o seu servidor Flexível e atualizar as preferências de agendamento, o evento atual irá proceder conforme programado e a alteração das definições de agendamento tornar-se-á eficaz após a sua conclusão bem sucedida.

Em casos raros, o evento de manutenção pode ser cancelado pelo sistema ou pode não conseguir completar com sucesso. Neste caso, o sistema criará uma notificação sobre eventos de manutenção cancelados ou falhados, respectivamente. A próxima tentativa de efetuar a manutenção será agendada de acordo com as definições de agendamento atuais e receberá a notificação com cinco dias de antecedência.

## <a name="next-steps"></a>Passos seguintes

* Saiba como [alterar o horário de manutenção](how-to-maintenance-portal.md)
* Saiba como [obter notificações sobre a próxima manutenção](../../service-health/service-notifications.md) usando a Azure Service Health
* Saiba como [configurar alertas sobre os próximos eventos de manutenção programada](../../service-health/resource-health-alert-monitor-guide.md)
