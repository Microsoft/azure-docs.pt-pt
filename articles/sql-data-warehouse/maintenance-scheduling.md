---
title: Agendamentos de manutenção do Azure
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
ms.openlocfilehash: 91b202f8a5df841fa3d6aa1f0903999b395f8137
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686061"
---
# <a name="use-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>Usar agendamentos de manutenção para gerenciar atualizações de serviço e manutenção

Os agendamentos de manutenção agora estão disponíveis em todas as regiões do Azure SQL Data Warehouse. O recurso de agendamento de manutenção integra as notificações de manutenção planejada da integridade do serviço, Resource Health Monitor de verificação e o serviço de agendamento de manutenção do Azure SQL Data Warehouse.

Você usa o agendamento de manutenção para escolher uma janela de tempo quando é conveniente receber novos recursos, atualizações e patches. Você escolhe uma janela de manutenção primária e uma secundária dentro de um período de sete dias. Para usar esse recurso, você precisará identificar uma janela primária e secundária em intervalos de dias separados.

Por exemplo, você pode agendar uma janela primária de sábado 22:00 para domingo 01:00 e, em seguida, agendar uma janela secundária da quarta-feira 19:00 a 22:00. Se SQL Data Warehouse não puder realizar a manutenção durante a janela de manutenção primária, ela tentará a manutenção novamente durante a janela de manutenção secundária. A manutenção do serviço pode ocorrer durante as janelas primária e secundária. Para garantir a conclusão rápida de todas as operações de manutenção, as camadas DW400 (c) e inferior data warehouse podem concluir a manutenção fora de uma janela de manutenção designada.

Todas as instâncias de SQL Data Warehouse do Azure criadas recentemente terão um agendamento de manutenção definido pelo sistema aplicado durante a implantação. O agendamento pode ser editado assim que a implantação for concluída.

Cada janela de manutenção pode ter entre três e oito horas. A manutenção pode ocorrer a qualquer momento dentro da janela. Quando a manutenção for iniciada, todas as sessões ativas serão canceladas e as transações não confirmadas serão revertidas. Você deve esperar várias perdas curtas na conectividade, pois o serviço implanta novo código em seu data warehouse. Você será notificado imediatamente após a conclusão da manutenção do data warehouse.

 Todas as operações de manutenção devem ser concluídas dentro das janelas de manutenção agendadas. Não é realizada qualquer manutenção fora das janelas de manutenção especificadas sem notificação prévia. Se seu data warehouse for pausado durante uma manutenção agendada, ele será atualizado durante a operação de retomada. 

## <a name="alerts-and-monitoring"></a>Alertas e monitoramento

A integração com notificações de integridade do serviço e o monitor de verificação de Resource Health permite que os clientes permaneçam informados sobre a atividade de manutenção iminente. A nova automação aproveita o Azure Monitor. Você pode decidir como deseja ser notificado de eventos de manutenção iminentes. Além disso, você pode escolher quais fluxos automatizados ajudarão você a gerenciar o tempo de inatividade e minimizar o impacto operacional.

Uma notificação antecipada de 24 horas precede todos os eventos de manutenção que não são para as camadas DWC400c e inferior. Para minimizar o tempo de inatividade de instância, verifique se o data warehouse não tem transações de execução longa antes do período de manutenção escolhido.

> [!NOTE]
> No caso de ser necessário implantar uma atualização crítica de tempo, os tempos de notificação avançados podem ser reduzidos significativamente.

Se você recebeu uma notificação antecipada de que a manutenção ocorrerá, mas SQL Data Warehouse não pode realizar a manutenção durante esse tempo, você receberá uma notificação de cancelamento. A manutenção será retomada durante o próximo período de manutenção agendado.

Todos os eventos de manutenção ativos aparecem na seção **integridade do serviço – manutenção planejada** . O histórico de integridade do serviço inclui um registro completo de eventos passados. Você pode monitorar a manutenção por meio do painel do portal de verificação de integridade do serviço do Azure durante um evento ativo.

### <a name="maintenance-schedule-availability"></a>Disponibilidade do agendamento de manutenção

Mesmo se o agendamento de manutenção não estiver disponível na região selecionada, você poderá exibir e editar seu agendamento de manutenção a qualquer momento. Quando o agendamento de manutenção se tornar disponível em sua região, a agenda identificada ficará imediatamente ativa no seu data warehouse.

## <a name="view-a-maintenance-schedule"></a>Exibir um agendamento de manutenção 

### <a name="portal"></a>Portal

Por predefinição, todas as instâncias do Azure SQL Data Warehouse recém-criadas têm uma janela de manutenção primária e secundária de oito horas aplicada durante a implementação. Conforme indicado acima, você pode alterar o Windows assim que a implantação for concluída. Não é realizada qualquer manutenção fora das janelas de manutenção especificadas sem notificação prévia.

Para ver a agenda de manutenção que foi aplicada ao seu armazém de dados, conclua os seguintes passos:

1.  Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2.  Selecione o data warehouse que você deseja exibir. 
3.  O data warehouse selecionado é aberto na folha visão geral. O agendamento de manutenção que é aplicado ao data warehouse aparece abaixo do **agendamento de manutenção**.

![Folha de visão geral](media/sql-data-warehouse-maintenance-scheduling/clear-overview-blade.PNG)

## <a name="change-a-maintenance-schedule"></a>Alterar um agendamento de manutenção 

### <a name="portal"></a>Portal
Uma agenda de manutenção pode ser atualizada ou alterada a qualquer momento. Se a instância selecionada estiver passando por um ciclo de manutenção ativo, as configurações serão salvas. Eles se tornarão ativos durante o próximo período de manutenção identificado. [Saiba mais](https://docs.microsoft.com/azure/service-health/resource-health-overview) sobre como monitorar seu data warehouse durante um evento de manutenção ativo. 

### <a name="identifying-the-primary-and-secondary-windows"></a>Identificando as janelas primária e secundária

As janelas primária e secundária devem ter intervalos de dias separados. Um exemplo é uma janela principal de terça-feira e um secundário da janela de sábado – domingo.

Para alterar o agendamento de manutenção do seu data warehouse, conclua as seguintes etapas:
1.  Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2.  Selecione o data warehouse que você deseja atualizar. A página é aberta na folha visão geral. 
3.  Abra a página para configurações de agendamento de manutenção selecionando o link de **Resumo agendamento de manutenção (versão prévia)** na folha visão geral. Ou então, selecione a opção **agendamento de manutenção** no menu de recursos do lado esquerdo.  

    ![Opções de folha de visão geral](media/sql-data-warehouse-maintenance-scheduling/maintenance-change-option.png)

4. Identifique o intervalo de dias preferenciais para sua janela de manutenção primária usando as opções na parte superior da página. Essa seleção determina se a sua janela principal ocorrerá em um dia da semana ou no fim de semana. Sua seleção atualizará os valores suspensos. Durante a visualização, algumas regiões podem ainda não oferecer suporte ao conjunto completo de opções de **dia** disponíveis.

   ![Folha configurações de manutenção](media/sql-data-warehouse-maintenance-scheduling/maintenance-settings-page.png)

5. Escolha suas janelas de manutenção primárias e secundárias preferenciais usando as caixas de listagem suspensas:
   - **Day**: dia preferencial para executar a manutenção durante a janela selecionada.
   - **Hora de início**: hora de início preferencial para a janela de manutenção.
   - **Janela de tempo**: duração preferida da janela de tempo.

   A área de **Resumo da agenda** na parte inferior da folha é atualizada com base nos valores que você selecionou. 
  
6. Selecione **Guardar**. Uma mensagem é exibida, confirmando se a nova agenda está ativa agora. 

   Se você estiver salvando uma agenda em uma região que não dá suporte ao agendamento de manutenção, a seguinte mensagem será exibida. Suas configurações são salvas e ficam ativas quando o recurso fica disponível na região selecionada.    

   ![Mensagem sobre disponibilidade de região](media/sql-data-warehouse-maintenance-scheduling/maintenance-notactive-toast.png)

## <a name="next-steps"></a>Passos seguintes
- [Saiba mais](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage) sobre como criar, exibir e gerenciar alertas usando Azure monitor.
- [Saiba mais](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) sobre ações de webhook para regras de alerta de log.
- [Saiba mais](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) Criando e gerenciando grupos de ação.
- [Saiba mais](https://docs.microsoft.com/azure/service-health/service-health-overview) sobre a integridade do serviço do Azure.