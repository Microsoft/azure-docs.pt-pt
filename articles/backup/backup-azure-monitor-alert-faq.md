---
title: Perguntas frequentes sobre monitoramento de alertas e relatórios
description: Neste artigo, descubra respostas para perguntas comuns sobre o alerta de monitoramento de backup do Azure e os relatórios de backup do Azure.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 07/08/2019
ms.openlocfilehash: 9cf7bf49d29b5faa9811a591b45179fe83c1d483
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172917"
---
# <a name="azure-backup-monitoring-alert---faq"></a>Alerta de monitoramento do backup do Azure-perguntas frequentes

Este artigo responde a perguntas comuns sobre o alerta de monitoramento do Azure.

## <a name="configure-azure-backup-reports"></a>Configurar relatórios do Azure Backup

### <a name="how-do-i-check-if-reporting-data-has-started-flowing-into-a-storage-account"></a>Como fazer verificar se os dados de relatório começaram a fluir para uma conta de armazenamento?

Vá para a conta de armazenamento que você configurou e selecione contêineres. Se o contêiner tiver uma entrada para insights-logs-azurebackupreport, isso indica que os dados de relatório começaram a fluir.

### <a name="what-is-the-frequency-of-data-push-to-a-storage-account-and-the-azure-backup-content-pack-in-power-bi"></a>Qual é a frequência de envio de dados por push para uma conta de armazenamento e o pacote de conteúdo do backup do Azure no Power BI?

  Para usuários do dia 0, leva cerca de 24 horas para enviar dados por push a uma conta de armazenamento. Após a conclusão desse Push inicial, os dados são atualizados com a frequência mostrada na figura a seguir.

* Os dados relacionados a **trabalhos**, **alertas**, **itens de backup**, **cofres**, **servidores protegidos**e **políticas** são enviados por push para uma conta de armazenamento de cliente como e quando são registrados.

* Os dados relacionados ao **armazenamento** são enviados por push para uma conta de armazenamento do cliente a cada 24 horas.

    ![Frequência de envio de dados de relatórios de backup do Azure](./media/backup-azure-configure-reports/reports-data-refresh-cycle.png)

* Power BI tem uma [atualização agendada uma vez por dia](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/#what-can-be-refreshed). Você pode executar uma atualização manual dos dados em Power BI para o pacote de conteúdo.

### <a name="how-long-can-i-retain-reports"></a>Por quanto tempo posso reter relatórios?

Ao configurar uma conta de armazenamento, você pode selecionar um período de retenção para dados de relatório na conta de armazenamento. Siga a etapa 6 na seção [configurar conta de armazenamento para relatórios](backup-azure-configure-reports.md#configure-storage-account-for-reports) . Você também pode [analisar relatórios no Excel](https://powerbi.microsoft.com/documentation/powerbi-service-analyze-in-excel/) e salvá-los por um período de retenção mais longo, com base em suas necessidades.

### <a name="will-i-see-all-my-data-in-reports-after-i-configure-the-storage-account"></a>Eu verá todos os meus dados em relatórios depois de configurar a conta de armazenamento?

 Todos os dados gerados após a configuração de uma conta de armazenamento são enviados por push para a conta de armazenamento e estão disponíveis em relatórios. Trabalhos em andamento não são enviados por push para relatórios. Depois que o trabalho for concluído ou falhar, ele será enviado aos relatórios.

### <a name="if-i-already-configured-the-storage-account-to-view-reports-can-i-change-the-configuration-to-use-another-storage-account"></a>Se eu já configurei a conta de armazenamento para exibir relatórios, posso alterar a configuração para usar outra conta de armazenamento?

Sim, você pode alterar a configuração para apontar para uma conta de armazenamento diferente. Use a conta de armazenamento recém configurada enquanto você se conecta ao pacote de conteúdo do backup do Azure. Além disso, após a configuração de uma conta de armazenamento diferente, novos fluxos de dados nessa conta de armazenamento. Os dados mais antigos (antes de alterar a configuração) ainda permanecem na conta de armazenamento mais antiga.

### <a name="can-i-view-reports-across-vaults-and-subscriptions"></a>Posso exibir relatórios em cofres e assinaturas?

Sim, você pode configurar a mesma conta de armazenamento em vários cofres para exibir relatórios de cofres cruzados. Além disso, você pode configurar a mesma conta de armazenamento para cofres entre assinaturas. Em seguida, você pode usar essa conta de armazenamento enquanto se conecta ao pacote de conteúdo do backup do Azure no Power BI para exibir os relatórios. A conta de armazenamento selecionada deve estar na mesma região que o cofre dos serviços de recuperação.

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Quanto tempo leva para o status do trabalho do agente de backup do Azure refletir no portal?

O portal do Azure pode levar até 15 minutos para refletir o status do trabalho do agente de backup do Azure.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>Quando um trabalho de backup falha, quanto tempo leva para gerar um alerta?

Um alerta é gerado dentro de 20 minutos da falha do backup do Azure.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>Há um caso em que um email não será enviado se as notificações forem configuradas?

Sim. Nas situações a seguir, as notificações não são enviadas.

* Se as notificações estiverem configuradas por hora, e um alerta for gerado e resolvido dentro da hora
* Quando um trabalho é cancelado
* Se um segundo trabalho de backup falhar porque o trabalho de backup original está em andamento

## <a name="recovery-services-vault"></a>Cofre dos serviços de recuperação

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Quanto tempo leva para o status do trabalho do agente de backup do Azure refletir no portal?

O portal do Azure pode levar até 15 minutos para refletir o status do trabalho do agente de backup do Azure.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>Quando um trabalho de backup falha, quanto tempo leva para gerar um alerta?

Um alerta é gerado dentro de 20 minutos da falha do backup do Azure.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>Há um caso em que um email não será enviado se as notificações forem configuradas?

Sim. Nas situações a seguir, as notificações não são enviadas:

* Se as notificações estiverem configuradas por hora, e um alerta for gerado e resolvido dentro da hora
* Quando um trabalho é cancelado
* Se um segundo trabalho de backup falhar porque o trabalho de backup original está em andamento

## <a name="next-steps"></a>Passos seguintes

Leia as outras perguntas frequentes:

* [Perguntas comuns](backup-azure-vm-backup-faq.md) sobre backups de VM do Azure.
* [Perguntas comuns](backup-azure-file-folder-backup-faq.md) sobre o agente de backup do Azure
