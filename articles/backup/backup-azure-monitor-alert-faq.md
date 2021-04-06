---
title: Alerta de monitorização e relatórios FAQ
description: Neste artigo, descubra respostas a perguntas comuns sobre o Alerta de Monitorização de Backup Azure e relatórios de Backup Azure.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 07/08/2019
ms.openlocfilehash: 4e2585009b531512e54ffa889a05ec50038dd619
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98805867"
---
# <a name="azure-backup-monitoring-alert---faq"></a>Alerta de monitorização de backup Azure - FAQ

Este artigo responde a perguntas comuns sobre monitorização e reporte de backup da Azure.

## <a name="configure-azure-backup-reports"></a>Configurar relatórios do Azure Backup

### <a name="how-do-i-check-if-reporting-data-has-started-flowing-into-a-log-analytics-la-workspace"></a>Como posso verificar se os dados de reporte começaram a fluir para um espaço de trabalho log analytics (LA) ?

Navegue para o espaço de trabalho de LA que configuraste. Navegue para o item do menu **'Registos'** e faça a `CoreAzureBackup | take 1` consulta. Se vires um registo a ser devolvido, significa que os dados começaram a fluir para o espaço de trabalho. O impulso inicial de dados pode demorar até 24 horas.

### <a name="what-is-the-frequency-of-data-push-to-an-la-workspace"></a>Qual é a frequência dos dados que empurram para um espaço de trabalho em LA?

Os dados de diagnóstico do cofre são bombeados para o espaço de trabalho Log Analytics com algum atraso. Cada evento chega ao espaço de trabalho do Log Analytics 20 a 30 minutos depois de ser empurrado do cofre dos Serviços de Recuperação. Aqui estão mais detalhes sobre o atraso:

* Em todas as soluções, os alertas incorporados do serviço de backup são empurrados assim que são criados. Por isso, normalmente aparecem no espaço de trabalho do Log Analytics após 20 a 30 minutos.
* Em todas as soluções, os postos de trabalho de apoio a pedido e o restabelecemento de postos de trabalho são empurrados assim que terminam.
* Para todas as soluções, exceto o backup SQL, os trabalhos de backup programados são empurrados assim que terminam.
* Para a cópia de segurança SQL, como as cópias de segurança podem ocorrer a cada 15 minutos, as informações para todos os trabalhos de backup programados, incluindo registos, são lotadas e empurradas a cada 6 horas.
* Em todas as soluções, outras informações como o item de backup, política, pontos de recuperação, armazenamento, e assim por diante, são empurradas pelo menos uma vez por dia.
* Uma alteração na configuração de backup (como mudar a política ou a política de edição) desencadeia um impulso de todas as informações de backup relacionadas.

### <a name="how-long-can-i-retain-reporting-data"></a>Por quanto tempo posso reter dados de reporte?

Depois de criar um espaço de trabalho em LA, pode optar por reter dados por um máximo de 2 anos. Por padrão, um ESPAÇO DE TRABALHO DE LA retém dados durante 31 dias.

### <a name="will-i-see-all-my-data-in-reports-after-i-configure-the-la-workspace"></a>Verei todos os meus dados em relatórios depois de configurar o espaço de trabalho de LA?

 Todos os dados gerados após configurar as definições de diagnóstico são empurrados para o espaço de trabalho de LA e estão disponíveis em relatórios. Os empregos em curso não são pressionados para reportar. Depois que o trabalho termina ou falha, é enviado para relatórios.

### <a name="can-i-view-reports-across-vaults-and-subscriptions"></a>Posso ver relatórios através de cofres e assinaturas?

Sim, pode ver relatórios através de cofres e assinaturas, bem como de regiões. Os seus dados podem residir num único espaço de trabalho de LA ou num grupo de espaços de trabalho de LA.

### <a name="can-i-view-reports-across-tenants"></a>Posso ver relatórios entre inquilinos?

Se é um utilizador do [Azure Lighthouse](https://azure.microsoft.com/services/azure-lighthouse/) com acesso delegado às subscrições dos seus clientes ou espaços de trabalho de LA, pode utilizar relatórios de backup para visualizar dados em todos os seus inquilinos.

## <a name="recovery-services-vault"></a>Cofre dos Serviços de Recuperação

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Quanto tempo demora o estatuto de agente de backup do Azure a refletir no portal?

O portal Azure pode demorar até 15 minutos para refletir o estado de trabalho do agente Azure Backup.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>When a backup job fails, how long does it take to raise an alert? (Quanto tempo demora a emitir um alerta quando um trabalho de criação de cópia de segurança falha?)

Um alerta é levantado dentro de 20 minutos da falha do Backup Azure.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>Existe algum caso em que um e-mail não será enviado se as notificações estiverem configuradas?

Sim. Nas seguintes situações, as notificações não são enviadas:

* Se as notificações forem configuradas de hora em hora, e um alerta for levantado e resolvido dentro de uma hora
* Quando um trabalho é cancelado
* Se um segundo trabalho de reserva falhar porque o trabalho de reserva original está em andamento

## <a name="next-steps"></a>Passos seguintes

Leia as outras FAQs:

* [Perguntas comuns](backup-azure-vm-backup-faq.yml) sobre cópias de segurança de VMs do Azure.
* [Perguntas comuns](backup-azure-file-folder-backup-faq.md) sobre o agente do Azure Backup
