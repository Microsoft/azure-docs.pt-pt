---
title: Agendas de manutenção do Azure (pré-visualização) | Documentos da Microsoft
description: Agendamento de manutenção permite aos clientes planejar os eventos de manutenção agendada necessário que o serviço do Azure SQL Data Warehouse utiliza para implementar novas funcionalidades, atualizações e patches.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/14/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: 4409cf5e6e4827af86892b51697a6acced2cb135
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56675301"
---
# <a name="change-a-maintenance-schedule"></a>Alterar uma agenda de manutenção 

## <a name="portal"></a>Portal
Um agendamento de manutenção pode ser atualizado ou alterado em qualquer altura. Se a instância selecionada está a passar por um ciclo de manutenção do Active Directory, as definições serão guardadas. Eles serão fique ativos durante o período de manutenção identificado seguinte. [Saiba mais](https://docs.microsoft.com/azure/service-health/resource-health-overview) sobre como monitorizar o seu armazém de dados durante um evento de manutenção do Active Directory. 

Para utilizar os agendamentos de manutenção, tem de selecionar duas janelas de manutenção durante um período de sete dias. Cada janela de manutenção pode ser três a oito horas. Manutenção pode ocorrer em qualquer altura dentro de uma janela de manutenção, mas não ocorre fora das janelas de tempo sem notificação anterior. Também irá ocorrer uma breve perda de conectividade como o service implementa o novo código para o armazém de dados. 

## <a name="identifying-the-primary-and-secondary-windows"></a>Identificando as janelas principais e secundárias

Os windows primários e secundários têm de ter os intervalos de dias separados. Um exemplo é uma janela principal de Terça-feira, Quinta-feira e um secundário da janela de Sábado, Domingo.

Para alterar a agenda de manutenção para o seu armazém de dados, conclua os seguintes passos:
1.  Inicie sessão no [portal do Azure](https://portal.azure.com/).
2.  Selecione o armazém de dados que pretende atualizar. É aberta a página do painel Descrição geral. 
3.  Abra a página de definições de agendamento de manutenção selecionando os **resumo de agenda de manutenção (pré-visualização)** ligação no painel de descrição geral. Em alternativa, selecione o **agenda de manutenção** opção no menu de recursos do lado esquerdo.  

    ![Opções do painel de descrição geral](media/sql-data-warehouse-maintenance-scheduling/maintenance-change-option.png)

4. Identifique o intervalo de dias preferencial para a janela de manutenção primário, utilizando as opções na parte superior da página. Esta seleção determina se a janela principal irá ocorrer num dia da semana ou no fim de semana. A seleção irá atualizar os valores de lista pendente. Durante a pré-visualização, algumas regiões poderão ainda não suporta o conjunto completo de disponíveis **dia** opções.

   ![Painel de definições de manutenção](media/sql-data-warehouse-maintenance-scheduling/maintenance-settings-page.png)

5. Escolha as janelas de manutenção preferencial de primário e secundário utilizando as caixas de lista pendente:
   - **Dia**: Dia preferencial para realizar a manutenção durante o período selecionado.
   - **Hora de início**: Hora de início para a janela de manutenção de preferência.
   - **Janela de tempo**: Duração preferencial da sua janela de tempo.

   O **resumo de agenda** área na parte inferior do painel é atualizada com base nos valores que selecionou. 
  
6. Selecione **Guardar**. É apresentada uma mensagem a confirmar que a sua nova agenda agora está ativa. 

   Se estará economizando uma agenda numa região que não suporta o agendamento de manutenção, é apresentada a seguinte mensagem. As definições são guardadas e fique ativas quando o recurso se torna disponível na sua região selecionada.    

   ![Mensagem sobre a disponibilidade de região](media/sql-data-warehouse-maintenance-scheduling/maintenance-notactive-toast.png)

## <a name="next-steps"></a>Passos Seguintes
- [Saiba mais](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) sobre as ações de webhook para regras de alerta de registo.
- [Saiba mais](https://docs.microsoft.com/azure/service-health/service-health-overview) sobre o Azure Service Health.


