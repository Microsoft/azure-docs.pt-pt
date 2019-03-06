---
title: 'Cópia de segurança do Azure: Monitorizar a cópia de segurança do Azure com o Azure Monitor'
description: Monitorizar cargas de trabalho de cópia de segurança do Azure e criar alertas personalizados através do Azure Monitor
services: backup
author: pvrk
manager: shivamg
keywords: O log Analytics; Cópia de segurança do Azure; Alertas; Definições de diagnóstico; Grupos de ação
ms.service: backup
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: pullabhk
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: 7daf76605d40495d6c9cb5076f0403a78819adfc
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57439168"
---
# <a name="monitoring-at-scale-using-azure-monitor"></a>Monitorização em escala com o Azure Monitor

O [incorporadas de monitorização e alertas artigo](backup-azure-monitoring-built-in-monitor.md) listados a monitorização e alertas de recursos num único Cofre de serviços de recuperação e o que é fornecido sem qualquer infraestrutura de gestão adicionais. No entanto, o serviço é limitado nos seguintes cenários.

- Dados de monitorização de vários cofres de RS entre subscrições
- Se o e-mail não é o canal de notificação preferencial
- Se os usuários querem receber alertas para cenários mais
- Se desejar exibir informações a partir de componentes no local, como o System Center DPM (SC-DPM) no Azure, que não é mostrado na [tarefas de cópia de segurança](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault) ou [alertas de cópia de segurança](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) no portal.

## <a name="using-log-analytics-workspace"></a>Utilizar a área de trabalho do Log Analytics

> [!NOTE]
> Estão a ser colocados dados a partir de backups de VM do Azure, o agente de MAB, System Center DPM (SC-DPM) para a área de trabalho do Log Analytics através das definições de diagnóstico. Suporte para cópias de segurança SQL em VMs do Azure, cópias de segurança de partilha de ficheiros do Azure, Microsoft Azure Backup Server (MABS) está disponível em breve.

Podemos são tirar partido das capacidades de dois serviços do Azure - **das definições de diagnóstico** (para enviar dados de vários recursos do Azure Resource Manager para outro recurso) e **do Log Analytics** (LA - gerar alertas personalizados em que pode definir outros canais de notificação através de grupos de ação) para a monitorização em escala. Os seguintes detalhes de secções sobre como utilizá-la para monitorizar a cópia de segurança do Azure em escala.

### <a name="configuring-diagnostic-settings"></a>Configurar definições de diagnóstico

Um recurso do Azure Resource Manager, como o Cofre dos serviços de recuperação do Azure regista todas as informações possíveis sobre operações agendadas e as operações de utilizador que acionou como dados de diagnóstico. Clique em "definições diagnóstico" na secção monitorização e especifique o destino de dados de diagnóstico do cofre RS.

![Cofre de RS definição de diagnóstico com LA como destino](media/backup-azure-monitoring-laworkspace/rs-vault-diagnostic-setting.png)

Pode selecionar uma área de trabalho LA a partir de outra subscrição como destino. *Ao selecionar a mesma área de trabalho de LA para vários cofres RS, pode monitorizar cofres em várias subscrições num único local.* Selecione 'AzureBackupReport' como o registo de canal todas as informações para a área de trabalho LA relacionadas com o Azure Backup.

> [!IMPORTANT]
> Depois de concluir a configuração, deve aguardar até 24 horas para push de dados inicial concluir. Depois disso, todos os eventos são enviados, conforme mencionado na [secção de frequência](#diagnostic-data-update-frequency).

### <a name="deploying-solution-to-log-analytics-workspace"></a>Implementar a solução à área de trabalho do Log Analytics

Quando os dados estiverem no interior da área de trabalho LA [implementar um modelo de github](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) em LA para visualizar os dados. Certifique-se de que fornecer o mesmo grupo de recursos, o nome de área de trabalho e localização da área de trabalho para identificar corretamente a área de trabalho e, em seguida, instale este modelo no mesmo.

### <a name="view-azure-backup-data-using-log-analytics-la"></a>Ver dados de cópia de segurança do Azure com o Log Analytics (LA)

Assim que o modelo é implementado, a solução para a monitorização do Azure Backup serão apresentados na região de resumo de área de trabalho. Pode percorrer através de

- O Azure Monitor -> "Mais" na seção de "Informações" e escolha a área de trabalho relevante ou
- Áreas de trabalho do log Analytics -> selecione a área de trabalho relevante -> "Resumo do Workspace" na secção Geral.

![AzureBackupLAMonitoringTile](media/backup-azure-monitoring-laworkspace/la-azurebackup-azuremonitor-tile.png)

Clicando no mosaico, o modelo de designer abre-se uma série de gráficos sobre os dados de monitorização básicos de cópia de segurança do Azure, tais como

#### <a name="all-backup-jobs"></a>Todas as tarefas de cópia de segurança

![LABackupJobs](media/backup-azure-monitoring-laworkspace/la-azurebackup-allbackupjobs.png)

#### <a name="restore-jobs"></a>Tarefas de restauro

![LARestoreJobs](media/backup-azure-monitoring-laworkspace/la-azurebackup-restorejobs.png)

#### <a name="inbuilt-azure-backup-alerts-for-azure-resources"></a>Alertas de cópia de segurança do Azure incorporadas para recursos do Azure

![LAInbuiltAzureBackupAlertsForAzureResources](media/backup-azure-monitoring-laworkspace/la-azurebackup-activealerts.png)

#### <a name="inbuilt-azure-backup-alerts-for-on-prem-resources"></a>Alertas de cópia de segurança do Azure incorporadas para recursos no local

![LAInbuiltAzureBackupAlertsForOnPremResources](media/backup-azure-monitoring-laworkspace/la-azurebackup-activealerts-onprem.png)

#### <a name="active-datasources"></a>Origens de dados Active Directory

![LAActiveBackedUpEntities](media/backup-azure-monitoring-laworkspace/la-azurebackup-activedatasources.png)

#### <a name="rs-vault-cloud-storage"></a>Armazenamento na Cloud de cofre RS

![LARSVaultCloudStorage](media/backup-azure-monitoring-laworkspace/la-azurebackup-cloudstorage-in-gb.png)

Os gráficos acima são fornecidos com o modelo e o cliente está livre para adicionar/editar gráficos mais.

> [!IMPORTANT]
> Quando implementar o modelo, ele cria essencialmente um bloqueio só de leitura e terá de removê-lo para editar o modelo e guardar. Para remover bloqueios, procure, no painel de "Bloqueios" na secção de "Definições" da área de trabalho do Log Analytics.

### <a name="create-alerts-using-log-analytics"></a>Criar alertas com o Log Analytics

O Azure Monitor permite aos utilizadores criar seus próprios alertas da área de trabalho LA onde pode *tirar partido dos grupos de ação do Azure para selecionar o mecanismo de notificação preferencial*. Clique em qualquer um dos gráficos acima para abrir a seção de 'Registos de' da área de trabalho de LA ***onde pode editar as consultas e criar alertas com base no-los***.

![LACreateAlerts](media/backup-azure-monitoring-laworkspace/la-azurebackup-customalerts.png)

Clicar em "Nova regra de alerta" conforme mostrado acima, será aberto o ecrã de criação do alerta do Monitor do Azure.

Como pode observar abaixo, o recurso já está marcado como área de trabalho LA e integração de grupo de ação é fornecida.

![LAAzureBackupCreateAlert](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

> [!IMPORTANT]
> Tenha em atenção que o impacto de preços relevante de criar esta consulta é fornecido [aqui](https://azure.microsoft.com/pricing/details/monitor/).

#### <a name="alert-condition"></a>Condição do alerta

O principal aspecto é a condição de acionamento do alerta. Clicar em "Condition" carregará automaticamente a consulta de Kusto no ecrã de "Registos", conforme mostrado abaixo, e pode editá-lo de acordo com seu cenário. Alguns exemplos de consultas de Kusto são fornecidos na [secção abaixo](#sample-kusto-queries).

![LAAzureBackupAlertCondition](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

Editar a consulta de Kusto, se necessário, selecione o limite direito (o que vai decidir quando o alerta será disparado), o período correto (janela de tempo para o qual a consulta é executada) e a frequência. Para por exemplo: Para por exemplo: Se o limiar for superior a 0, o período de é de 5 minutos e a frequência é de 5 minutos, em seguida, a regra é traduzida como "Execute a consulta a cada 5 minutos para os últimos 5 minutos e se o número de resultados for maior que 0, notificar-me através do grupo de ação selecionada"

#### <a name="action-group-integration"></a>Integração do grupo de ação

Grupos de ação de especificar os canais de notificação disponíveis para o utilizador. Clicar em "Criar nova" em "Grupos de ação" secção mostra a lista de mecanismos de notificação disponíveis.

![LAAzureBackupNewActionGroup](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

Saiba mais sobre o [alertas da área de trabalho LA](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) e sobre [grupos de ação](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) da documentação do Azure Monitor.

Por conseguinte, pode atender a todos os alertas e monitorização de requisitos de LA sozinho ou utilizá-la como uma técnica suplementar para mecanismos de notificação incorporados.

### <a name="sample-kusto-queries"></a>Exemplos de consultas de Kusto

Os gráficos padrão lhe Kusto consultas para cenários básicos em que pode criar alertas. Também pode modificá-los para obter os dados que pretende receber alertas em. Aqui fornecemos alguns exemplos de consultas de Kusto que podem colar na janela de "Registos" e, em seguida, criar um alerta na consulta.

#### <a name="all-successful-backup-jobs"></a>Todas as tarefas de cópia de segurança com êxito

````Kusto
AzureDiagnostics
| where Category == "AzureBackupReport"
| where SchemaVersion_s == "V2"
| where OperationName == "Job" and JobOperation_s == "Backup"
| where JobStatus_s == "Completed"
````

#### <a name="all-failed-backup-jobs"></a>Todos os falhados tarefas de cópia de segurança

````Kusto
AzureDiagnostics
| where Category == "AzureBackupReport"
| where SchemaVersion_s == "V2"
| where OperationName == "Job" and JobOperation_s == "Backup"
| where JobStatus_s == "Failed"
````

#### <a name="all-successful-azure-vm-backup-jobs"></a>Todas as tarefas de cópia de segurança de VM do Azure com êxito

````Kusto
AzureDiagnostics
| where Category == "AzureBackupReport"
| where SchemaVersion_s == "V2"
| extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
| where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
| join kind=inner
(
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where OperationName == "BackupItem"
    | where SchemaVersion_s == "V2"
    | where BackupItemType_s == "VM" and BackupManagementType_s == "IaaSVM"
    | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
    | project BackupItemUniqueId_s , BackupItemFriendlyName_s
)
on BackupItemUniqueId_s
| extend Vault= Resource
| project-away Resource
````

#### <a name="all-successful-sql-log-backup-jobs"></a>Todas as tarefas de cópia de segurança de registo do SQL com êxito

````Kusto
AzureDiagnostics
| where Category == "AzureBackupReport"
| where SchemaVersion_s == "V2"
| extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
| where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s == "Log"
| join kind=inner
(
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where OperationName == "BackupItem"
    | where SchemaVersion_s == "V2"
    | where BackupItemType_s == "SQLDataBase" and BackupManagementType_s == "AzureWorkload"
    | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
    | project BackupItemUniqueId_s , BackupItemFriendlyName_s
)
on BackupItemUniqueId_s
| extend Vault= Resource
| project-away Resource
````

#### <a name="all-successful-mab-agent-backup-jobs"></a>Todas as tarefas de cópia de segurança de agente de MAB com êxito

````Kusto
AzureDiagnostics
| where Category == "AzureBackupReport"
| where SchemaVersion_s == "V2"
| extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
| where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
| join kind=inner
(
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where OperationName == "BackupItem"
    | where SchemaVersion_s == "V2"
    | where BackupItemType_s == "FileFolder" and BackupManagementType_s == "MAB"
    | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
    | project BackupItemUniqueId_s , BackupItemFriendlyName_s
)
on BackupItemUniqueId_s
| extend Vault= Resource
| project-away Resource
````

### <a name="diagnostic-data-update-frequency"></a>Frequência de atualização de dados de diagnóstico

Os dados de diagnóstico do Cofre é colocados na área de trabalho LA com algum atraso. Cada evento chegar à área de trabalho LA ***com um atraso de 20 a 30 minutos depois é enviada por push do cofre RS.***

- Os alertas incorporadas do serviço de cópia de segurança (em todas as soluções) são enviados por push assim que forem criadas. Isto significa que eles geralmente aparecem na área de trabalho LA após um atraso de 20 a 30 minutos.
- Tarefas de cópia de segurança ad hoc e tarefas de restauro (em todas as soluções) são enviados por push logo que eles ***são concluídas***.
- As tarefas de cópia de segurança agendadas de todas as soluções (exceto a cópia de segurança do SQL) são enviados por push logo que eles ***são concluídas***.
- Para cópia de segurança do SQL, uma vez que estamos pode ter backups de log a cada 15 minutos, para todos os concluído trabalhos de backup agendados, incluindo registos, as informações é colocar em lote e enviados por push a cada 6 horas.
- Todas as outras informações como item de cópia de segurança, política, pontos de recuperação, etc. em todas as soluções de armazenamento são enviados por push **, pelo menos, uma vez por dia.**
- Uma alteração na configuração de cópia de segurança, tal como alterar a edição de política, etc. de política aciona um push de todas as respetivas informações de cópia de segurança.

## <a name="using-rs-vaults-activity-logs"></a>Utilizar o Cofre de RS registos de atividades

Também pode utilizar os registos de atividades para receber a notificação de eventos, tais como o sucesso de cópia de segurança.

> [!CAUTION]
> **Tenha em atenção de que isso só é aplicável para cópias de segurança de VM do Azure.** Não é possível usar isso para outras soluções, como o agente de cópia de segurança do Azure, cópias de segurança do SQL no Azure, Azure arquivos etc.

### <a name="sign-in-into-azure-portal"></a>Iniciar sessão no portal do Azure

Iniciar sessão no portal do Azure e continuar para o Cofre de serviços de recuperação do Azure relevante e clique em "secção registo de atividades" nas propriedades.

### <a name="identify-appropriate-log-and-create-alert"></a>Identificar o registo adequado e criar alerta

Aplica os filtros mostrados na imagem seguinte para verificar se está a receber os registos de atividades para cópias de segurança com êxito. Altere o período de tempo em conformidade para ver registos.

![Registos de atividades para cópias de segurança de VM do Azure](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

Pode clicar o segmento de "JSON" para obter mais detalhes e visualizá-lo ao copiar-colá-lo num editor de texto. Deverá apresentar os detalhes do cofre e o item que disparou a atividade de registo ou seja, o item de cópia de segurança.

Em seguida, clique em "Adicionar alerta de registo de atividade" para gerar alertas para todos os tais logs.

Pode clicar em "Adicionar alerta registo de atividades" mostrada acima, e ele será aberto o ecrã de criação do alerta que é semelhante ao ecrã de criação do alerta [conforme descrito acima](#create-alerts-using-log-analytics).

Eis o recurso cofre RS em si e, por conseguinte, terá de repetir a mesma ação para todos os cofres em que pretende que a notificação através de registos de atividades. A condição não terão qualquer limiar, o período, a frequência, uma vez que este é um alerta com base em eventos. Assim que o registo de atividades relevantes for gerado, o alerta é acionado.

## <a name="recommendation"></a>Recomendação

***Todos os alertas criados a partir de registos de atividades e áreas de trabalho LA podem ser visualizadas no Azure Monitor no painel de "Alertas" para a esquerda.***

Embora possa utilizar a notificação através de registos de atividades, ***serviço de cópia de segurança do Azure recomenda vivamente que utilize LA para a monitorização em escala e não registos de atividades pelos seguintes motivos***.

- **Cenários limitados:** Aplicável apenas para cópias de segurança de VM do Azure e deve ser repetido para cada cofre RS.
- **Definição de acordo com:** A atividade de cópia de segurança agendada não se ajusta com a definição mais recente de registos de atividade e se alinha com [registos de diagnóstico](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview#what-are-azure-monitor-diagnostic-logs). Isso levava a inesperado impacto quando os dados bombeando através do canal de registo de atividade são alterados conforme indicado abaixo.
- **Problemas com canal de registo de atividade:** Foi movido para um novo modelo de bombeando os registos de atividade de cópia de segurança do Azure nos cofres dos serviços de recuperação. Infelizmente, a mudança afetou a geração de registos de atividade em Clouds soberanas do Azure. Se os utilizadores de Cloud soberanas do Azure criou ou não está configurado todos os alertas de registos de Atividades através do Azure Monitor, eles não seriam acionados. Além disso, em todas as regiões públicas do Azure, se um utilizador está a recolher registos de atividades de serviços de recuperação numa área de trabalho de análise de registo como mencionado [aqui](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs), estes registos também não iria aparecer.

Por conseguinte, é altamente recomendável utilizar a área de trabalho de análise de registo para monitorização e alertas em escala, para todos os a cópia de segurança do Azure de cargas de trabalho protegidas.

## <a name="next-steps"></a>Passos Seguintes

- Consulte a [modelo de dados do Log analytics](backup-azure-log-analytics-data-model.md) para criar consultas personalizadas.
