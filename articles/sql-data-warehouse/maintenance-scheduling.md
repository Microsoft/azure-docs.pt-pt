---
title: Agendas de manutenção do Azure (pré-visualização) | Documentos da Microsoft
description: Agendamento de manutenção permite aos clientes planejar os eventos de manutenção agendada necessário que o serviço do Azure SQL Data Warehouse utiliza para implementar novas funcionalidades, atualizações e patches.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 03/13/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 2f76b0a6565e5ba7c34d88a271e9770f809669dd
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58007785"
---
# <a name="use-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>Use planos de manutenção para gerir atualizações de serviço e manutenção

Agendamentos de manutenção estão agora disponíveis em todas as regiões do Azure SQL Data Warehouse. Esta funcionalidade integra-se as notificações de manutenção planeada do serviço de estado de funcionamento do Monitor de verificação de estado de funcionamento de recursos e o serviço de agendamento de manutenção do Azure SQL Data Warehouse.

Utilize para escolher uma janela de tempo quando é conveniente receber as novas funcionalidades, atualizações e patches de agendamento de manutenção. Escolha um servidor principal e uma janela de manutenção secundário dentro de um período de sete dias. Um exemplo é uma janela principal do Sábado 22:00 para Domingo 01:00 e uma janela secundária de quarta-feira 19: a 22 00:00. Se o SQL Data Warehouse não é possível efetuar a manutenção durante a janela de manutenção primário, tentará a manutenção novamente durante a janela de manutenção secundário. Manutenção do serviço pode ocorrer durante a primária e os windows secundários.

Todos os recentemente criado Azure SQL Data Warehouse instâncias terá uma agenda de manutenção definidas pelo sistema aplicada durante a implementação. O agendamento pode ser editado, assim como a implementação estiver concluída.

Cada janela de manutenção pode ser três a oito horas. Manutenção pode ocorrer em qualquer altura dentro da janela. Deve esperar uma breve perda de conectividade, como o service implementa o novo código para o armazém de dados.

Para utilizar esta funcionalidade que tem de identificar uma janela primária e secundária nos intervalos de dias separados. Todas as operações de manutenção devem ser concluído dentro as janelas de manutenção agendada. Sem manutenção terá lugar fora das janelas de manutenção especificado sem notificação anterior. Se o seu armazém de dados está em pausa durante a manutenção agendada, ele será atualizado durante a operação de retoma.  

## <a name="alerts-and-monitoring"></a>Alertas e monitorização

Integração com notificações de estado de funcionamento do serviço e o Monitor de verificação de estado de funcionamento de recursos permite aos clientes para se manter informado da atividade de manutenção iminente. A nova automação tira partido do Azure Monitor. Pode decidir como pretende ser notificado sobre eventos de manutenção iminente. Também pode decida quais fluxos automatizados podem ajudá-lo a gerenciar o tempo de inatividade e minimizar o impacto para as suas operações.

Uma notificação de avanço de 24 horas precede todos os eventos de manutenção. Para minimizar o período de indisponibilidade de instância, certifique-se de que o seu armazém de dados não tem nenhuma transação de longa execução antes do período de manutenção escolhido. Quando é iniciado a manutenção, serão canceladas todas as sessões ativas. Reversão de não será revertida e o armazém de dados terão uma pequena perda de conectividade. Será notificado imediatamente após a conclusão da manutenção no seu armazém de dados.

Se tiver recebido uma notificação antecipada que manutenção ocorrerá, mas o SQL Data Warehouse não é possível efetuar a manutenção durante esse período, receberá uma notificação de cancelamento. Em seguida, retoma-se manutenção durante o período de manutenção agendada seguinte.

Todos os eventos de manutenção do Active Directory são apresentados no **estado de funcionamento do serviço, a manutenção planeada** secção. O histórico de estado de funcionamento do serviço inclui um registo completo dos acontecimentos passados. Pode monitorizar a manutenção por meio do dashboard do portal do Azure Service Health verificação durante um evento de Active Directory.

### <a name="maintenance-schedule-availability"></a>Disponibilidade de agenda de manutenção

Mesmo que o agendamento de manutenção não está disponível na sua região selecionada, pode ver e editar a sua agenda de manutenção em qualquer altura. Quando o agendamento de manutenção se torna disponível na sua região, a agenda identificada ficarão imediatamente Active Directory no seu armazém de dados.

## <a name="next-steps"></a>Passos Seguintes

- [Saiba mais](viewing-maintenance-schedule.md) sobre a visualização de um agendamento de manutenção.
- [Saiba mais](changing-maintenance-schedule.md) sobre como alterar uma agenda de manutenção.
- [Saiba mais](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage) sobre como criar, visualizar e gerir alertas com o Azure Monitor.
- [Saiba mais](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) sobre as ações de webhook para regras de alerta de registo.
- [Saiba mais](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) criando e gerenciando grupos de ação.
- [Saiba mais](https://docs.microsoft.com/azure/service-health/service-health-overview) sobre o Azure Service Health.
