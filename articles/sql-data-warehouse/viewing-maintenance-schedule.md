---
title: Agendas de manutenção do Azure (pré-visualização) | Documentos da Microsoft
description: Agendamento de manutenção permite aos clientes planejar os eventos de manutenção agendada necessário que o serviço do Azure SQL Data warehouse utiliza para implementar novas funcionalidades, atualizações e patches.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/27/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: f0b9b59ec09445a170d1f93181c2b432eafb60bf
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839987"
---
# <a name="view-a-maintenance-schedule"></a>Ver um agendamento de manutenção 

## <a name="portal"></a>Portal

Por predefinição, todas as instâncias do Azure SQL Data Warehouse recentemente criadas têm uma janela de manutenção de primário e secundário de oito horas aplicada durante a implementação. Pode alterar as janelas que em breve a implementação estiver concluída. Sem manutenção terá lugar fora das janelas de manutenção especificado sem notificação anterior.

Para ver a agenda de manutenção que foi aplicada ao seu armazém de dados, conclua os seguintes passos:

1.  Inicie sessão no [portal do Azure](https://portal.azure.com/).
2.  Selecione o armazém de dados que pretende ver. 
3.  O armazém de dados selecionada abre-se no painel de descrição geral. A agenda de manutenção que é aplicada ao armazém de dados é apresentada abaixo **agenda de manutenção**.

![Painel de descrição geral](media/sql-data-warehouse-maintenance-scheduling/clear-overview-blade.PNG)

## <a name="next-steps"></a>Passos Seguintes
- [Saiba mais](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage) sobre como criar, visualizar e gerir alertas com o Azure Monitor.
- [Saiba mais](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) sobre as ações de webhook para regras de alerta de registo.
- [Saiba mais](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) criando e gerenciando grupos de ação.
- [Saiba mais](https://docs.microsoft.com/azure/service-health/service-health-overview) sobre o Azure Service Health.


