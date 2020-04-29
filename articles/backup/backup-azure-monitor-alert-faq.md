---
title: Alerta de monitorização e relatórios FAQ
description: Neste artigo, descubra respostas a perguntas comuns sobre os relatórios azure backup monitoring alert e Azure Backup.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 07/08/2019
ms.openlocfilehash: f5be97458ba658f315c31ae34e540842b64e3ec4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76989574"
---
# <a name="azure-backup-monitoring-alert---faq"></a>Alerta de monitorização de backup azure - FAQ

Este artigo responde a perguntas comuns sobre monitorização e reporte de backup do Azure.

## <a name="configure-azure-backup-reports"></a>Configurar relatórios do Azure Backup

### <a name="how-do-i-check-if-reporting-data-has-started-flowing-into-a-log-analytics-la-workspace"></a>Como posso verificar se os dados de reporte começaram a fluir para um espaço de trabalho de Log Analytics (LA) ?

Navegue para o espaço de trabalho de LA configurado, navegue para o item do menu **Logs** e faça a consulta CoreAzureBackup [ tomar 1. Se vir um registo a ser devolvido, significa que os dados começaram a fluir para o espaço de trabalho. O impulso inicial de dados pode demorar até 24 horas.

### <a name="what-is-the-frequency-of-data-push-to-an-la-workspace"></a>Qual é a frequência dos dados para um espaço de trabalho de LA?

Os dados de diagnóstico do cofre são bombeados para o espaço de trabalho log Analytics com algum atraso. Todos os eventos chegam ao espaço de trabalho log Analytics 20 a 30 minutos depois de ser empurrado do cofre dos Serviços de Recuperação. Aqui estão mais detalhes sobre o atraso:

* Em todas as soluções, os alertas integrados do serviço de reserva são empurrados assim que são criados. Então, geralmente aparecem no espaço de trabalho log Analytics após 20 a 30 minutos.
* Em todas as soluções, os postos de trabalho a pedido e a restauração dos postos de trabalho são empurrados assim que terminam.
* Para todas as soluções, com exceção do backup SQL, os trabalhos de backup programados são empurrados assim que terminam.
* Para a cópia de segurança SQL, porque as cópias de segurança de log podem ocorrer a cada 15 minutos, a informação para todos os trabalhos de backup programados completos, incluindo registos, é loteada e empurrada a cada 6 horas.
* Em todas as soluções, outras informações como o item de backup, política, pontos de recuperação, armazenamento, e assim por diante, é empurrada pelo menos uma vez por dia.
* Uma mudança na configuração de backup (como mudar a política ou editar a política) desencadeia um impulso de todas as informações de backup relacionadas.

### <a name="how-long-can-i-retain-reporting-data"></a>Quanto tempo posso reter dados de reporte?

Depois de criar um espaço de trabalho em LA, pode optar por reter dados por um período máximo de 2 anos. Por padrão, um espaço de trabalho de LA retém dados durante 31 dias.

### <a name="will-i-see-all-my-data-in-reports-after-i-configure-the-la-workspace"></a>Verei todos os meus dados em relatórios depois de configurar o espaço de trabalho de LA?

 Todos os dados gerados após configurar as definições de diagnóstico são empurrados para o Espaço de Trabalho de LA e estão disponíveis em relatórios. Os trabalhos em curso não são pressionados para reportar. Após o trabalho terminar ou falhar, é enviado para relatórios.

### <a name="can-i-view-reports-across-vaults-and-subscriptions"></a>Posso ver relatórios através de cofres e assinaturas?

Sim, pode ver relatórios em cofres e subscrições, bem como em regiões. Os seus dados podem residir num único espaço de trabalho de LA ou num grupo de espaços de trabalho de LA.

### <a name="can-i-view-reports-across-tenants"></a>Posso ver relatórios entre inquilinos?

Se for um utilizador do [Farol Azure](https://azure.microsoft.com/services/azure-lighthouse/) com acesso delegado às subscrições dos seus clientes ou espaços de trabalho de LA, pode utilizar relatórios de backup para visualizar dados em todos os seus inquilinos.

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Quanto tempo demora o estatuto de agente de apoio Azure a refletir no portal?

O portal Azure pode demorar até 15 minutos a refletir o estatuto de agente de backup Azure.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>When a backup job fails, how long does it take to raise an alert? (Quanto tempo demora a emitir um alerta quando um trabalho de criação de cópia de segurança falha?)

Um alerta é levantado dentro de 20 minutos da falha de reserva azure.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>Há algum caso em que um e-mail não será enviado se as notificações forem configuradas?

Sim. Nas seguintes situações, as notificações não são enviadas.

* Se as notificações forem configuradas de hora em hora, e um alerta for levantado e resolvido dentro de uma hora
* Quando um trabalho é cancelado
* Se um segundo trabalho de reserva falhar porque o trabalho original de backup está em andamento

## <a name="recovery-services-vault"></a>Cofre dos Serviços de Recuperação

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Quanto tempo demora o estatuto de agente de apoio Azure a refletir no portal?

O portal Azure pode demorar até 15 minutos a refletir o estatuto de agente de backup Azure.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>When a backup job fails, how long does it take to raise an alert? (Quanto tempo demora a emitir um alerta quando um trabalho de criação de cópia de segurança falha?)

Um alerta é levantado dentro de 20 minutos da falha de reserva azure.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>Há algum caso em que um e-mail não será enviado se as notificações forem configuradas?

Sim. Nas seguintes situações, não são enviadas notificações:

* Se as notificações forem configuradas de hora em hora, e um alerta for levantado e resolvido dentro de uma hora
* Quando um trabalho é cancelado
* Se um segundo trabalho de reserva falhar porque o trabalho original de backup está em andamento

## <a name="next-steps"></a>Passos seguintes

Leia as outras FAQs:

* [Perguntas comuns](backup-azure-vm-backup-faq.md) sobre cópias de segurança de VMs do Azure.
* [Perguntas comuns](backup-azure-file-folder-backup-faq.md) sobre o agente do Azure Backup
