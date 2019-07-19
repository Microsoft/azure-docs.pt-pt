---
title: Agendamentos de manutenção do Azure (versão prévia) | Microsoft Docs
description: O agendamento de manutenção permite que os clientes planejem os eventos de manutenção agendados necessários que o serviço de SQL Data Warehouse do Azure usa para distribuir novos recursos, atualizações e patches.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 07/16/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 3875106e8c6301c95bc8d0fbce6a1c0400d07f78
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68278111"
---
# <a name="use-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>Usar agendamentos de manutenção para gerenciar atualizações de serviço e manutenção

Os agendamentos de manutenção agora estão disponíveis em todas as regiões do Azure SQL Data Warehouse. Esse recurso integra as notificações de manutenção planejada da integridade do serviço, Resource Health Monitor de verificação e o serviço de agendamento de manutenção do Azure SQL Data Warehouse.

Você usa o agendamento de manutenção para escolher uma janela de tempo quando é conveniente receber novos recursos, atualizações e patches. Você escolhe uma janela de manutenção primária e uma secundária dentro de um período de sete dias. Um exemplo é uma janela principal de sábado 22:00 a domingo 01:00 e uma janela secundária de quarta-feira 19:00 a 22:00. Se SQL Data Warehouse não puder realizar a manutenção durante a janela de manutenção primária, ela tentará a manutenção novamente durante a janela de manutenção secundária. A manutenção do serviço pode ocorrer durante as janelas primária e secundária. Para garantir a conclusão rápida de todas as operações de manutenção, as camadas DW400 (c) e inferior data warehouse podem concluir a manutenção fora de uma janela de manutenção designada.

Todas as instâncias de SQL Data Warehouse do Azure criadas recentemente terão um agendamento de manutenção definido pelo sistema aplicado durante a implantação. O agendamento pode ser editado assim que a implantação for concluída.

Cada janela de manutenção pode ser de três a oito horas. A manutenção pode ocorrer a qualquer momento dentro da janela. Quando a manutenção for iniciada, todas as sessões ativas serão canceladas e as transações não confirmadas serão revertidas. Você deve esperar várias perdas curtas na conectividade, pois o serviço implanta novo código em seu data warehouse. Você será notificado imediatamente após a conclusão da manutenção no seu data warehouse

Para usar esse recurso, você precisará identificar uma janela primária e secundária em intervalos de dias separados. Todas as operações de manutenção devem ser concluídas dentro das janelas de manutenção agendadas. Nenhuma manutenção ocorrerá fora das janelas de manutenção especificadas sem notificação prévia. Se seu data warehouse for pausado durante uma manutenção agendada, ele será atualizado durante a operação de retomada.  

## <a name="alerts-and-monitoring"></a>Alertas e monitoramento

A integração com notificações de integridade do serviço e o monitor de verificação de Resource Health permite que os clientes permaneçam informados sobre a atividade de manutenção iminente. A nova automação aproveita o Azure Monitor. Você pode decidir como deseja ser notificado de eventos de manutenção iminentes. Também decida quais fluxos automatizados podem ajudá-lo a gerenciar o tempo de inatividade e minimizar o impacto em suas operações.

Uma notificação antecipada de 24 horas precede todos os eventos de manutenção, com a exceção atual de DW400c e camadas inferiores. Para minimizar o tempo de inatividade de instância, verifique se o data warehouse não tem transações de execução longa antes do período de manutenção escolhido.

> [!NOTE]
> No caso de ser necessário implantar uma atualização crítica de tempo, os tempos de notificação avançados podem ser reduzidos significativamente.

Se você recebeu uma notificação antecipada de que a manutenção ocorrerá, mas SQL Data Warehouse não pode realizar a manutenção durante esse tempo, você receberá uma notificação de cancelamento. A manutenção será retomada durante o próximo período de manutenção agendado.

Todos os eventos de manutenção ativos aparecem na seção **integridade do serviço – manutenção planejada** . O histórico de integridade do serviço inclui um registro completo de eventos passados. Você pode monitorar a manutenção por meio do painel do portal de verificação de integridade do serviço do Azure durante um evento ativo.

### <a name="maintenance-schedule-availability"></a>Disponibilidade do agendamento de manutenção

Mesmo se o agendamento de manutenção não estiver disponível na região selecionada, você poderá exibir e editar seu agendamento de manutenção a qualquer momento. Quando o agendamento de manutenção se tornar disponível em sua região, a agenda identificada ficará imediatamente ativa no seu data warehouse.

## <a name="next-steps"></a>Passos seguintes

- [Saiba mais](viewing-maintenance-schedule.md) sobre como exibir um agendamento de manutenção.
- [Saiba mais](changing-maintenance-schedule.md) sobre como alterar um agendamento de manutenção.
- [Saiba mais](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage) sobre como criar, exibir e gerenciar alertas usando Azure monitor.
- [Saiba mais](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) sobre ações de webhook para regras de alerta de log.
- [Saiba mais](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) Criando e gerenciando grupos de ação.
- [Saiba mais](https://docs.microsoft.com/azure/service-health/service-health-overview) sobre a integridade do serviço do Azure.
