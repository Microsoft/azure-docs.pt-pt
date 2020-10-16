---
title: Horários de manutenção da piscina Sinaapse SQL
description: O agendamento de manutenção permite que os clientes planeiem em torno dos eventos de manutenção programados necessários que o Azure Synapse Analytics utiliza para lançar novas funcionalidades, upgrades e patches.
services: synapse-analytics
author: antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/02/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 1c5bc4400e99fb1c24e321e623aaee523b9c7383
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85210988"
---
# <a name="use-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>Utilize horários de manutenção para gerir atualizações de serviços e manutenção

A funcionalidade de programação de manutenção integra as notificações de manutenção planeadas para a saúde do serviço, o Monitor de Verificação de Saúde de Recursos e o serviço de agendamento de manutenção para o pool Sinaapse SQL (armazém de dados) dentro do Azure Synapse Analytics.

Deve utilizar o agendamento de manutenção para escolher uma janela de tempo quando é conveniente receber novas funcionalidades, upgrades e patches. Terá de escolher uma janela de manutenção primária e secundária num período de sete dias, cada janela deve estar dentro de intervalos de dia separados.

Por exemplo, pode agendar uma janela primária das 22:00 para domingo às 01:00, e depois agendar uma janela secundária de quarta-feira das 19:00 às 22:00. Se a manutenção não puder ser efetuada durante a sua janela de manutenção primária, tentará novamente a manutenção durante a sua janela de manutenção secundária. A manutenção do serviço pode ocorrer ocasionalmente durante as janelas primárias e secundárias. Para garantir a rápida conclusão de todas as operações de manutenção, o DW400c e os níveis mais baixos do armazém de dados poderiam completar a manutenção fora de uma janela de manutenção designada.

Todas as instâncias de armazém de dados recentemente criadas terão um programa de manutenção definido pelo sistema aplicado durante a implantação. O horário pode ser editado assim que a implementação estiver concluída.

Embora uma janela de manutenção possa ser entre três e oito horas, isso não significa que o armazém de dados fique offline durante o período de duração. A manutenção pode ocorrer a qualquer momento dentro dessa janela e deve esperar uma única desconexão durante esse período que dure ~5-6 minutos à medida que o serviço implementa novo código no seu armazém de dados. DW400c e inferior podem sofrer múltiplas perdas breves de conectividade em vários momentos durante a janela de manutenção. Quando a manutenção começar, todas as sessões ativas serão canceladas e as transações não comprometidas serão revertidas. Para minimizar o tempo de inatividade de casos, certifique-se de que o seu armazém de dados não tem transações de longa duração antes do período de manutenção escolhido.

Todas as operações de manutenção devem terminar dentro das janelas de manutenção especificadas, a menos que seja necessário implementar uma atualização sensível ao tempo. Se o seu armazém de dados for interrompido durante uma manutenção programada, será atualizado durante a operação do currículo. Será notificado imediatamente após a manutenção do seu armazém de dados.

## <a name="alerts-and-monitoring"></a>Alertas e monitorização

A integração com as notificações de Saúde do Serviço e o Monitor de Verificação de Saúde de Recursos permite que os clientes se mantenham informados da atividade de manutenção iminente. Esta automatização tira partido do Azure Monitor. Pode decidir como quer ser notificado de eventos de manutenção iminentes. Além disso, pode escolher quais os fluxos automatizados que o ajudarão a gerir o tempo de inatividade e minimizar o impacto operacional.

Uma notificação antecipada de 24 horas precede todos os eventos de manutenção que não são para os níveis DWC400c e inferiores.

> [!NOTE]
> No caso de sermos obrigados a implementar uma atualização crítica de tempo, os tempos de notificação avançados podem ser significativamente reduzidos.

Se recebeu a notificação antecipada de que a manutenção será feita, mas a manutenção não pode ser realizada durante o período de tempo na notificação, receberá uma notificação de cancelamento. A manutenção será retomada durante o próximo período de manutenção programado.

Todos os eventos de manutenção ativa aparecem na secção de Saúde do **Serviço - Manutenção Planeada.** O histórico de Saúde de Serviço inclui um registo completo de eventos passados. Pode monitorizar a manutenção através do painel do portal de verificação do serviço Azure Health durante um evento ativo.

### <a name="maintenance-schedule-availability"></a>Disponibilidade de horário de manutenção

Mesmo que o agendamento de manutenção não esteja disponível na sua região selecionada, pode ver e editar o seu horário de manutenção a qualquer momento. Quando o agendamento de manutenção ficar disponível na sua região, o horário identificado tornar-se-á imediatamente ativo na sua piscina Synapse SQL.

## <a name="view-a-maintenance-schedule"></a>Ver um horário de manutenção

Por padrão, todas as instâncias de armazém de dados recentemente criadas têm uma janela de manutenção primária e secundária de oito horas aplicada durante a implantação. Como indicado acima, pode alterar as janelas assim que a colocação estiver concluída. Não é realizada qualquer manutenção fora das janelas de manutenção especificadas sem notificação prévia.

Para visualizar o calendário de manutenção que foi aplicado à sua piscina Synapse SQL, complete os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Selecione a piscina Synapse SQL que deseja ver.
3. A piscina Sinaapse SQL selecionada abre na lâmina geral. O calendário de manutenção que é aplicado ao armazém de dados aparece abaixo do **horário de manutenção.**

![Lâmina de visão geral](./media/maintenance-scheduling/clear-overview-blade.PNG)

## <a name="change-a-maintenance-schedule"></a>Alterar um horário de manutenção

Um horário de manutenção pode ser atualizado ou alterado a qualquer momento. Se a instância selecionada estiver a passar por um ciclo de manutenção ativo, as definições serão guardadas. Ficarão ativos durante o próximo período de manutenção identificado. [Saiba mais](../../service-health/resource-health-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) sobre a monitorização do seu armazém de dados durante um evento de manutenção ativa.

## <a name="identifying-the-primary-and-secondary-windows"></a>Identificação das janelas primárias e secundárias

As janelas primárias e secundárias devem ter intervalos diurnos separados. Um exemplo é uma janela primária de terça-feira a quinta-feira e uma segunda janela de sábado a domingo.

Para alterar o calendário de manutenção da sua piscina Synapse SQL, complete os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Selecione a piscina Synapse SQL que pretende atualizar. A página abre na lâmina geral.
Abra a página para configurações de agendamento de manutenção selecionando o link de resumo do **Programa de Manutenção** na lâmina de visão geral. Ou, selecione a opção **Agenda de Manutenção** no menu de recursos do lado esquerdo.

    ![Opções de lâmina de visão geral](./media/maintenance-scheduling/maintenance-change-option.png)

3. Identifique o intervalo de dia preferido para a sua janela de manutenção primária utilizando as opções no topo da página. Esta seleção determina se a sua janela primária ocorrerá num dia de semana ou durante o fim de semana. A sua seleção atualizará os valores de queda.
Durante a pré-visualização, algumas regiões podem ainda não suportar o conjunto completo de opções **disponíveis do Dia.**

   ![Lâmina de regulação de manutenção](./media/maintenance-scheduling/maintenance-settings-page.png)

4. Escolha as suas janelas de manutenção primárias e secundárias preferidas utilizando as caixas de lista de entrega:
   - **Dia**: Dia preferencial para efetuar a manutenção durante a janela selecionada.
   - **Hora de início**: Hora de início preferida para a janela de manutenção.
   - **Janela do tempo**: Duração preferencial da janela do tempo.

   A área **de resumo da Agenda** na parte inferior da lâmina é atualizada com base nos valores que selecionou.
  
5. Selecione **Guardar**. Aparece uma mensagem, confirmando que o seu novo horário está agora ativo.

   Se está a guardar um horário numa região que não suporta agendamento de manutenção, aparece a seguinte mensagem. As suas definições são guardadas e tornam-se ativas quando a funcionalidade estiver disponível na região selecionada.

   ![Mensagem sobre disponibilidade da região](./media/maintenance-scheduling/maintenance-not-active-toast.png)

## <a name="next-steps"></a>Passos seguintes

- [Saiba mais](../../azure-monitor/platform/alerts-metric.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) sobre a criação, visualização e gestão de alertas utilizando o Azure Monitor.
- [Saiba mais](../..//azure-monitor/platform/alerts-log-webhook.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) sobre as ações de webhook para regras de alerta de registo.
- [Saiba mais](../..//azure-monitor/platform/action-groups.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Criar e gerir grupos de ação.
- [Saiba mais](../../service-health/service-health-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) sobre a Azure Service Health.
