---
title: Horários de manutenção azure
description: O agendamento de manutenção permite que os clientes planeiem em torno dos eventos de manutenção programados necessários que o serviço Azure SQL Data Warehouse utiliza para lançar novas funcionalidades, atualizações e patches.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 02/02/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 1cf4cc9cf4d98dfca59e01cc264549af3a4d5cb4
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471793"
---
# <a name="use-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>Utilize horários de manutenção para gerir atualizações de serviços e manutenção

O cronograma de manutenção integra as Notificações de Manutenção Planeadas para a Saúde do Serviço, o Monitor de Verificação de Saúde de Recursos e o serviço de manutenção do Armazém de Dados Azure SQL.

Deve utilizar o agendamento de manutenção para escolher uma janela de tempo quando é conveniente receber novas funcionalidades, upgrades e patches. Você precisará escolher uma janela de manutenção primária e secundária dentro de um período de sete dias, cada janela deve estar dentro de intervalos de dia separados.

Por exemplo, pode agendar uma janela primária de sábado das 22:00 às 01:00 de domingo, e depois agendar uma janela secundária de quarta-feira das 19:00 às 22:00. Se o SQL Data Warehouse não conseguir realizar a manutenção durante a janela de manutenção primária, tentará novamente a manutenção durante a janela de manutenção secundária. A manutenção do serviço pode ocorrer ocasionalmente durante as janelas primárias e secundárias. Para garantir a rápida conclusão de todas as operações de manutenção, os níveis de depósito dW400c e de dados mais baixos poderiam completar a manutenção fora de uma janela de manutenção designada.

Todas as instâncias recém-criadas do Armazém de Dados Azure SQL terão um calendário de manutenção definido pelo sistema aplicado durante a implantação. A programação pode ser editada assim que a implementação estiver concluída.

Embora uma janela de manutenção possa estar entre três a oito horas, isso não significa que o armazém de dados ficará offline durante toda a duração. A manutenção pode ocorrer a qualquer momento dentro dessa janela e deve esperar uma única desconexão durante esse período que dure ~5 -6 minutos à medida que o serviço implementa um novo código para o seu armazém de dados. DW400c e inferior pode sofrer múltiplas perdas breves na conectividade em vários momentos durante a janela de manutenção. Quando a manutenção começar, todas as sessões ativas serão canceladas e as transações não comprometidas serão reativadas. Para minimizar o tempo de inatividade de instâncias, certifique-se de que o seu armazém de dados não tem transações de longa duração antes do período de manutenção escolhido.

Todas as operações de manutenção devem terminar dentro das janelas de manutenção especificadas, a menos que sejamos obrigados a implementar uma atualização sensível ao tempo. Se o seu armazém de dados for interrompido durante uma manutenção programada, este será atualizado durante a operação de retoma. Será notificado imediatamente após a manutenção do armazém de dados estar concluída.

## <a name="alerts-and-monitoring"></a>Alertas e monitorização

A integração com notificações de saúde de serviço e o Monitor de Verificação de Saúde de Recursos permite que os clientes se mantenham informados da atividade de manutenção iminente. Esta automatização tira partido do Monitor Azure. Pode decidir como pretende ser notificado de eventos de manutenção iminentes. Além disso, pode escolher quais os fluxos automatizados que o ajudarão a gerir o tempo de inatividade e minimizar o impacto operacional.
Uma notificação antecipada de 24 horas precede todos os eventos de manutenção que não são para os níveis DWC400c e inferiores.

> [!NOTE]
> No caso de sermos obrigados a implementar uma atualização crítica de tempo, os tempos de notificação avançados podem ser significativamente reduzidos.

Se recebeu uma notificação antecipada de que a manutenção terá lugar, mas o SQL Data Warehouse não pode realizar manutenção durante esse período, receberá uma notificação de cancelamento. A manutenção será retomada durante o próximo período de manutenção programado.

Todos os eventos de manutenção ativa aparecem na secção **de Manutenção de Serviços - Manutenção Planeada.** A história da Saúde de Serviço inclui um registo completo de eventos passados. Pode monitorizar a manutenção através do portal de verificação de saúde do serviço Azure durante um evento ativo.

### <a name="maintenance-schedule-availability"></a>Disponibilidade de horáriode manutenção

Mesmo que o agendamento de manutenção não esteja disponível na sua região selecionada, pode ver e editar o seu horário de manutenção a qualquer momento. Quando o agendamento de manutenção estiver disponível na sua região, o horário identificado tornar-se-á imediatamente ativo no seu armazém de dados.

## <a name="view-a-maintenance-schedule"></a>Ver um horário de manutenção 

### <a name="portal"></a>Portal

Por predefinição, todas as instâncias do Azure SQL Data Warehouse recém-criadas têm uma janela de manutenção primária e secundária de oito horas aplicada durante a implementação. Como indicado acima, pode alterar as janelas assim que a implementação estiver completa. Não é realizada qualquer manutenção fora das janelas de manutenção especificadas sem notificação prévia.

Para ver a agenda de manutenção que foi aplicada ao seu armazém de dados, conclua os seguintes passos:

1.  Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2.  Selecione o armazém de dados que pretende ver. 
3.  O armazém de dados selecionado abre na lâmina de visão geral. O horário de manutenção que é aplicado ao armazém de dados aparece abaixo do horário de **manutenção.**

![Lâmina de visão geral](media/sql-data-warehouse-maintenance-scheduling/clear-overview-blade.PNG)

## <a name="change-a-maintenance-schedule"></a>Alterar um horário de manutenção 

### <a name="portal"></a>Portal
Um horário de manutenção pode ser atualizado ou alterado a qualquer momento. Se a instância selecionada estiver a passar por um ciclo de manutenção ativa, as definições serão guardadas. Tornar-se-ão ativos durante o próximo período de manutenção identificado. [Saiba mais](https://docs.microsoft.com/azure/service-health/resource-health-overview) sobre a monitorização do seu armazém de dados durante um evento de manutenção ativa. 

### <a name="identifying-the-primary-and-secondary-windows"></a>Identificação das janelas primárias e secundárias

As janelas primárias e secundárias devem ter intervalos diurnos separados. Um exemplo é uma janela primária de terça a quinta-feira e uma janela secundária de sábado a domingo.

Para alterar o horário de manutenção do seu armazém de dados, preencha os seguintes passos:
1.  Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2.  Selecione o armazém de dados que pretende atualizar. A página abre na lâmina de visão geral. 
3.  Abra a página para configurações de horáriode manutenção selecionando o link resumo do Horário de **Manutenção** na lâmina de visão geral. Ou, selecione a opção Agenda de **Manutenção** no menu de recursos do lado esquerdo.  

    ![Opções de lâmina de visão geral](media/sql-data-warehouse-maintenance-scheduling/maintenance-change-option.png)

4. Identifique a gama de dias preferida para a sua janela de manutenção primária utilizando as opções na parte superior da página. Esta seleção determina se a sua janela primária ocorrerá num dia de semana ou durante o fim de semana. A sua seleção atualizará os valores de queda. Durante a pré-visualização, algumas regiões podem ainda não apoiar todo o conjunto de opções disponíveis **do Dia.**

   ![Lâmina de regulação de manutenção](media/sql-data-warehouse-maintenance-scheduling/maintenance-settings-page.png)

5. Escolha as suas janelas de manutenção primária e secundária preferidas utilizando as caixas de listas de lançamento:
   - **Dia**: Dia preferido para realizar a manutenção durante a janela selecionada.
   - **Início**: Hora de início preferencial para a janela de manutenção.
   - **Janela de tempo**: Duração preferida da janela de tempo.

   A área **de resumo** de Agenda na parte inferior da lâmina é atualizada com base nos valores que selecionou. 
  
6. Selecione **Guardar**. Uma mensagem aparece, confirmando que o seu novo horário está agora ativo. 

   Se estiver a guardar um horário numa região que não suporta o agendamento de manutenção, aparece a seguinte mensagem. As suas definições são guardadas e tornam-se ativas quando a funcionalidade estiver disponível na sua região selecionada.    

   ![Mensagem sobre a disponibilidade da região](media/sql-data-warehouse-maintenance-scheduling/maintenance-notactive-toast.png)

## <a name="next-steps"></a>Passos seguintes
- [Saiba mais](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage) sobre a criação, visualização e gestão de alertas utilizando o Monitor Azure.
- [Saiba mais](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) sobre as ações do webhook para regras de alerta de log.
- [Saiba mais](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) Criação e gestão de Grupos de Ação.
- [Saiba mais](https://docs.microsoft.com/azure/service-health/service-health-overview) sobre a Azure Service Health.
