---
title: Cópia de segurança do Azure, FAQ de alerta de monitorização
description: 'Respostas a perguntas comuns sobre: O alerta de monitorização de cópia de segurança do Azure'
services: backup
author: srinathvasireddy
manager: sivan
ms.service: backup
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: srinathv
ms.openlocfilehash: bb684f65539b4429862b2dce0e378d8f659d2975
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705035"
---
# <a name="azure-backup-monitoring-alert---faq"></a>Cópia de segurança do Azure alerta - FAQ de monitorização
Este artigo responde a perguntas comuns sobre o alerta de monitorização do Azure.

## <a name="configure-azure-backup-reports"></a>Configurar relatórios do Azure Backup

### <a name="how-do-i-check-if-reporting-data-has-started-flowing-into-a-storage-account"></a>Como posso ver se os dados de relatórios foi iniciada a ser encaminhados para uma conta de armazenamento?
Vá para a conta de armazenamento que configurou e selecionar contentores. Se o contentor tem uma entrada para insights-logs-azurebackupreport, ele indica que dados de relatórios foi iniciada a fluir.

### <a name="what-is-the-frequency-of-data-push-to-a-storage-account-and-the-azure-backup-content-pack-in-power-bi"></a>O que é a frequência de push de dados para uma conta de armazenamento e o pacote de conteúdos de cópia de segurança do Azure no Power BI?
  Para os utilizadores de dia 0, demora cerca de 24 horas para enviar dados para uma conta de armazenamento. Após a conclusão deste push inicial, os dados são atualizados com frequência mostrada na figura a seguir.

  * Dados relacionados com **trabalhos**, **alertas**, **itens de cópia de segurança**, **cofres**, **servidores protegidos**e  **Políticas** são emitidos via push para uma conta de armazenamento do cliente como e quando é registado.

  * Dados relacionados com **armazenamento** são emitidos via push para uma conta de armazenamento do cliente a cada 24 horas.

       ![Frequência de push de dados de relatórios de cópia de segurança do Azure](./media/backup-azure-configure-reports/reports-data-refresh-cycle.png)

  * O Power BI tem um [atualização agendada uma vez por dia](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/#what-can-be-refreshed). Pode efetuar uma atualização manual dos dados no Power BI para o pacote de conteúdos.

### <a name="how-long-can-i-retain-reports"></a>Quanto posso manter relatórios?
Ao configurar uma conta de armazenamento, pode selecionar um período de retenção de dados do relatório na conta de armazenamento. Siga etapa 6 a [configurar a conta de armazenamento para relatórios](backup-azure-configure-reports.md#configure-storage-account-for-reports) secção. Também pode [analise relatórios no Excel](https://powerbi.microsoft.com/documentation/powerbi-service-analyze-in-excel/) e salvá-los por um período de retenção mais longo, com base nas suas necessidades.

### <a name="will-i-see-all-my-data-in-reports-after-i-configure-the-storage-account"></a>Irei ver todos os meus dados em relatórios depois de configurar a conta de armazenamento?
 Todos os dados gerados depois de configurar uma conta de armazenamento são emitidos via push para a conta de armazenamento e está disponíveis nos relatórios. Tarefas em curso não são enviados por push para relatórios. Depois da tarefa é concluída ou falha, ele é enviado para relatórios.

### <a name="if-i-already-configured-the-storage-account-to-view-reports-can-i-change-the-configuration-to-use-another-storage-account"></a>Se eu já configurado a conta de armazenamento para ver relatórios, posso alterar a configuração para utilizar outra conta de armazenamento?
Sim, pode alterar a configuração para apontar para uma conta de armazenamento diferente. Utilize a conta de armazenamento recentemente configurado enquanto se conectar ao pacote de conteúdos do Azure Backup. Além disso, após a configuração de uma conta de armazenamento diferentes, novos fluxos de dados nesta conta de armazenamento. Os dados mais antigos (antes de alterar a configuração) ainda permanecem na conta de armazenamento mais antiga.

### <a name="can-i-view-reports-across-vaults-and-subscriptions"></a>Pode visualizar relatórios em cofres e subscrições?
Sim, pode configurar a mesma conta de armazenamento em vários cofres para ver relatórios de cross-cofre. Além disso, pode configurar a mesma conta de armazenamento para cofres entre subscrições. Em seguida, pode utilizar esta conta de armazenamento enquanto se conectar ao pacote de conteúdos de cópia de segurança do Azure no Power BI para visualizar os relatórios. A conta de armazenamento selecionada tem de ser na mesma região que o Cofre de serviços de recuperação.

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Quanto tempo demora para o estado da tarefa de agente de cópia de segurança do Azure refletir no portal?
O portal do Azure pode demorar até 15 minutos para refletir o estado da tarefa de agente de cópia de segurança do Azure.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>Quando uma tarefa de cópia de segurança falhar, o tempo que demora para emitir um alerta?
Um alerta é gerado dentro de 20 minutos da falha de cópia de segurança do Azure.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>Existe um caso onde não ser enviado um e-mail se estiverem configuradas notificações?
Sim. Nas seguintes situações, não são enviadas notificações.

* Se as notificações estão configuradas por hora e um alerta for emitido e resolvido dentro da hora
* Quando uma tarefa é cancelada
* Se uma segunda tarefa de cópia de segurança falhar porque a tarefa de cópia de segurança original está em curso

## <a name="recovery-services-vault"></a>Cofre dos serviços de recuperação

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Quanto tempo demora para o estado da tarefa de agente de cópia de segurança do Azure refletir no portal?
O portal do Azure pode demorar até 15 minutos para refletir o estado da tarefa de agente de cópia de segurança do Azure.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>Quando uma tarefa de cópia de segurança falhar, o tempo que demora para emitir um alerta?
Um alerta é gerado dentro de 20 minutos da falha de cópia de segurança do Azure.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>Existe um caso onde não ser enviado um e-mail se estiverem configuradas notificações?
Sim. Nas seguintes situações, não são enviadas notificações.

* Se as notificações estão configuradas por hora e um alerta for emitido e resolvido dentro da hora
* Quando uma tarefa é cancelada
* Se uma segunda tarefa de cópia de segurança falhar porque a tarefa de cópia de segurança original está em curso

## <a name="next-steps"></a>Passos Seguintes

Leia as perguntas frequentes:

- [Perguntas comuns](backup-azure-vm-backup-faq.md) sobre cópias de segurança de VM do Azure.
- [Perguntas comuns](backup-azure-file-folder-backup-faq.md) sobre o agente de cópia de segurança do Azure
