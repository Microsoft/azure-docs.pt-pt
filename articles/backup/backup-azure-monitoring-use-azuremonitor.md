---
title: 'Cópia de segurança do Azure: Monitorizar a cópia de segurança do Azure com o Azure Monitor'
description: Monitorizar cargas de trabalho de cópia de segurança do Azure e criar alertas personalizados com o Azure Monitor.
services: backup
author: pvrk
manager: shivamg
keywords: O log Analytics; Cópia de segurança do Azure; Alertas; Definições de diagnóstico; Grupos de ação
ms.service: backup
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: pullabhk
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: e2d4a235737789f2f5852c00218427613db3d558
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786320"
---
# <a name="monitor-at-scale-by-using-azure-monitor"></a>Monitorizar à escala com o Azure Monitor

O Azure Backup fornece [monitorização e alertas de recursos incorporados](backup-azure-monitoring-built-in-monitor.md) num cofre dos serviços de recuperação. Estas capacidades estão disponíveis sem qualquer infraestrutura de gestão adicionais. Mas este serviço incorporado é limitado nos seguintes cenários:

- Se o monitor de dados a partir de vários cofres de serviços de recuperação entre subscrições
- Se for o canal de notificação preferencial *não* e-mail
- Se os utilizadores quiserem alertas para cenários mais
- Se desejar exibir informações a partir de um componente no local, como o System Center Data Protection Manager no Azure, que o portal não mostra na [ **tarefas de cópia de segurança** ](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault) ou [  **Alertas de cópia de segurança**](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault)

## <a name="using-log-analytics-workspace"></a>Utilizar a área de trabalho do Log Analytics

> [!NOTE]
> Dados de cópias de segurança de VM do Azure, o agente do Azure Backup, System Center Data Protection Manager, as cópias de segurança SQL em VMs do Azure e cópias de segurança de partilha de ficheiros do Azure é colocados na área de trabalho do Log Analytics através das definições de diagnóstico. 

Para monitorizar à escala, terá das capacidades dos dois serviços do Azure. *Definições de diagnóstico* enviar dados de vários recursos do Azure Resource Manager para outro recurso. *Log Analytics* gera em que pode utilizar grupos de ação para definir a outros canais de notificação de alertas personalizados. 

As secções seguintes detalham como utilizar o Log Analytics para monitorizar a cópia de segurança do Azure em escala.

### <a name="configure-diagnostic-settings"></a>Configurar definições de diagnóstico

Recursos de Gestor de recursos do Azure, como o Cofre de serviços de recuperação, registram informações sobre as operações agendadas e as operações de utilizador que acionou como dados de diagnóstico. 

Na secção de monitorização, selecione **das definições de diagnóstico** e especifique o destino para o Cofre dos serviços de recuperação dados de diagnóstico.

![Definição de diagnóstico do Cofre de serviços de recuperação, direcionamento do Log Analytics](media/backup-azure-monitoring-laworkspace/rs-vault-diagnostic-setting.png)

Pode direcionar uma área de trabalho do Log Analytics a partir de outra subscrição. Para monitorizar cofres em várias subscrições num único local, selecione a mesma área de trabalho do Log Analytics para vários cofres de serviços de recuperação. Para todas as informações relacionadas com a cópia de segurança do Azure para a área de trabalho do Log Analytics de canal, selecione **AzureBackupReport** como o registo.

> [!IMPORTANT]
> Depois de concluir a configuração, é necessário aguardar o envio de dados inicial ao fim de 24 horas. Depois que a inicial push de dados, todos os eventos são enviados por push conforme descrito neste artigo, o [secção de frequência](#diagnostic-data-update-frequency).

### <a name="deploy-a-solution-to-the-log-analytics-workspace"></a>Implantar uma solução para a área de trabalho do Log Analytics

Depois dos dados no interior da área de trabalho do Log Analytics [implementar um modelo de GitHub](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) ao Log Analytics para visualizar os dados. Para identificar corretamente a área de trabalho, certifique-se de que dê a ele o mesmo grupo de recursos, o nome de área de trabalho e a localização da área de trabalho. Em seguida, instale este modelo na área de trabalho.

> [!NOTE]
> Se não tiver alertas, tarefas de cópia de segurança ou tarefas de restauro na sua área de trabalho do Log Analytics, poderá ver um código de erro "BadArgumentError" no portal. Ignorar este erro e continuar a utilizar a solução. Depois dos dados relevantes escreva começa a ser encaminhados para a área de trabalho, as visualizações irão refletir o mesmo e não vir o erro mais.

### <a name="view-azure-backup-data-by-using-log-analytics"></a>Ver dados de cópia de segurança do Azure através da utilização do Log Analytics

Depois do modelo é implementado, a solução para a monitorização do Azure Backup serão apresentados na região de resumo de área de trabalho. Para ir para o resumo, siga um destes caminhos:

- **Azure Monitor**: Na **Insights** secção, selecione **mais** e, em seguida, escolha a área de trabalho relevante.
- **Áreas de trabalho de análise de registo**: Selecione a área de trabalho relevante e, em **gerais**, selecione **resumo de área de trabalho**.

![O mosaico monitorização do Log Analytics](media/backup-azure-monitoring-laworkspace/la-azurebackup-azuremonitor-tile.png)

Ao selecionar o mosaico monitorização, o modelo de designer é aberta uma série de gráficos sobre os dados de monitorização básicos da cópia de segurança do Azure. Aqui estão alguns dos gráficos, que verá:

* Todas as tarefas de cópia de segurança

   ![Gráficos de análise de registo para tarefas de cópia de segurança](media/backup-azure-monitoring-laworkspace/la-azurebackup-allbackupjobs.png)

* Tarefas de restauro

   ![Gráfico de análise de registo para tarefas de restauro](media/backup-azure-monitoring-laworkspace/la-azurebackup-restorejobs.png)

* Alertas de cópia de segurança do Azure incorporados para recursos do Azure

   ![Gráfico de análise de registo para os alertas de cópia de segurança do Azure incorporadas para recursos do Azure](media/backup-azure-monitoring-laworkspace/la-azurebackup-activealerts.png)

* Alertas de cópia de segurança do Azure incorporados para recursos no local

   ![Gráfico de análise de registo para os alertas de cópia de segurança do Azure incorporadas para recursos no local](media/backup-azure-monitoring-laworkspace/la-azurebackup-activealerts-onprem.png)

* Origens de dados do Active Directory

   ![Gráfico de análise de registo para entidades de cópia de segurança Active Directory](media/backup-azure-monitoring-laworkspace/la-azurebackup-activedatasources.png)

* Armazenamento na cloud do cofre dos serviços de recuperação

   ![Gráfico de análise de registo para o armazenamento de nuvem de Cofre de serviços de recuperação](media/backup-azure-monitoring-laworkspace/la-azurebackup-cloudstorage-in-gb.png)

Estes gráficos são fornecidos com o modelo. Pode editar os gráficos ou adicionar mais gráficos, se for necessário.

> [!IMPORTANT]
> Quando implementar o modelo, essencialmente está criando um bloqueio só de leitura. Para editar e guardar o modelo, terá de remover o bloqueio. Pode remover um bloqueio no **configurações** secção da área de trabalho do Log Analytics, no **bloqueia** painel.

### <a name="create-alerts-by-using-log-analytics"></a>Criar alertas com o Log Analytics

No Azure Monitor, pode criar seus próprios alertas numa área de trabalho do Log Analytics. Na área de trabalho, utilize *grupos de ação do Azure* para selecionar o mecanismo de notificação preferencial. 

> [!IMPORTANT]
> Para informações sobre o custo de criar essa consulta, consulte [preços do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

Selecione um dos gráficos para abrir o **registos** secção da área de trabalho do Log Analytics. Na **registos** secção, editar as consultas e criar alertas nos mesmos.

![Criar um alerta numa área de trabalho do Log Analytics](media/backup-azure-monitoring-laworkspace/la-azurebackup-customalerts.png)

Quando seleciona **nova regra de alerta**, abre a página de criação do alerta do Monitor do Azure, conforme mostrado na imagem seguinte. Aqui o recurso já está marcado como área de trabalho do Log Analytics e integração de grupo de ação é fornecida.

![A página de criação do alerta do Log Analytics](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

#### <a name="alert-condition"></a>Condição do alerta

O traço que define de um alerta é sua condição de acionamento. Selecione **condição** para carregar automaticamente a consulta de Kusto na **registos** página conforme mostrado na imagem seguinte. Aqui pode editar a condição de acordo com as suas necessidades. Para obter mais informações, consulte [consultas de exemplo Kusto](#sample-kusto-queries).

![Como configurar uma condição de alerta](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

Se necessário, pode editar a consulta de Kusto. Escolha um limiar, o período e a frequência. O limiar determina quando o alerta vai ser gerado. O período é o período de tempo em que a consulta é executada. Por exemplo, se o limiar for superior a 0, o período de é de 5 minutos e a frequência é de 5 minutos, em seguida, a regra é executada a consulta a cada 5 minutos, revisão de cinco minutos anteriores. Se o número de resultados for maior que 0, receberá uma notificação através do grupo de ação selecionado.

#### <a name="alert-action-groups"></a>Grupos de ação do alerta

Utilize um grupo de ação para especificar um canal de notificação. Para ver os mecanismos de notificação de disponibilidade, em **grupos de ação**, selecione **criar nova**.

![Mecanismos de notificação disponíveis na janela "Adicionar grupo de ação"](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

Pode atender a todos os alertas e monitorização de requisitos do Log Analytics individualmente ou pode utilizar o Log Analytics para complementar notificações incorporadas.

Para obter mais informações, consulte [criar, ver e gerir alertas de registo com o Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) e [criar e gerir grupos de ação no portal do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups).

### <a name="sample-kusto-queries"></a>Exemplos de consultas de Kusto

Os gráficos padrão dão-lhe Kusto consultas para cenários básicos em que pode criar alertas. Também pode modificar as consultas para obter os dados que pretende ser alertado no. Cole as seguintes consultas de Kusto de exemplo no **registos** página e, em seguida, criar alertas nas consultas:

* Todas as tarefas de cópia de segurança com êxito

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Completed"
    ````
    
* Todos os falhados tarefas de cópia de segurança

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Failed"
    ````
    
* Todas as tarefas de cópia de segurança de VM do Azure com êxito

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

* Todas as tarefas concluídas com êxito SQL log cópia de segurança

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

* Todas as tarefas de agente de cópia de segurança do Azure com êxito

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

Os dados de diagnóstico do Cofre é colocados na área de trabalho do Log Analytics com algum atraso. Cada evento chega à área de trabalho do Log Analytics *20 a 30 minutos* depois de esta é instalada a partir do Cofre de serviços de recuperação. Seguem-se mais detalhes sobre o atraso:

- Em todas as soluções, alertas incorporados do serviço de cópia de segurança são enviados por push assim que forem criadas. Para que serem normalmente apresentados na área de trabalho do Log Analytics após 20 a 30 minutos.
- Em todas as soluções, tarefas de cópia de segurança ad hoc e as tarefas de restauro são enviados por push logo que eles *concluir*.
- Para todas as soluções, exceto a cópia de segurança do SQL, tarefas de cópia de segurança agendadas são enviados por push logo que eles *concluir*.
- Para cópia de segurança do SQL, como backups de log podem ocorrer a cada 15 minutos, informações para todos os concluído trabalhos de backup agendados, incluindo registos, é em lotes e enviadas a cada 6 horas.
- Em todas as soluções, outras informações como o item de cópia de segurança, política, pontos de recuperação, armazenamento e assim por diante são emitidos via push, pelo menos, *uma vez por dia.*
- Uma alteração na configuração de cópia de segurança (por exemplo, alterar a política ou editar a política) aciona um push de todas as respetivas informações de cópia de segurança.

## <a name="using-the-recovery-services-vaults-activity-logs"></a>Utilizar registos de atividades o Cofre dos serviços de recuperação

> [!CAUTION]
> Os seguintes passos aplicam-se apenas ao *cópias de segurança de VM do Azure.* Não é possível utilizar estes passos para obterem soluções como o agente de cópia de segurança do Azure, cópias de segurança do SQL no Azure ou ficheiros do Azure.

Também pode utilizar os registos de atividades para receber a notificação de eventos, tais como o sucesso de cópia de segurança. Para começar, siga estes passos:

1. Inicie sessão no portal do Azure.
1. Abra o Cofre de serviços de recuperação relevante. 
1. Nas propriedades do cofre, abra a **registo de atividades** secção.

Para identificar o registo adequado e criar um alerta:

1. Certifique-se de que está a receber os registos de atividades para cópias de segurança com êxito ao aplicar os filtros mostrados na imagem seguinte. Alteração da **Timespan** valor conforme necessário, para ver os registos.

   ![Filtragem para localizar os registos de atividades para cópias de segurança de VM do Azure](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

1. Selecione o nome da operação para ver os detalhes relevantes.
1. Selecione **nova regra de alerta** para abrir o **criar regra** página. 
1. Criar um alerta ao seguir os passos em [criar, ver e gerir alertas de registo de atividade com o Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log).

   ![Nova regra de alerta](media/backup-azure-monitoring-laworkspace/new-alert-rule.png)

Aqui, o recurso é o próprio Cofre de serviços de recuperação. Tem de repetir os mesmos passos para todos os cofres nos quais pretende ser notificado através de registos de atividade. A condição não terá um limiar, o período ou a frequência, uma vez que este alerta é baseado em eventos. Assim que o registo de atividades relevantes for gerado, o alerta é acionado.

## <a name="using-log-analytics-to-monitor-at-scale"></a>Utilizar o Log Analytics para monitorizar à escala

Pode ver todos os alertas criados a partir de registos de atividades e áreas de trabalho do Log Analytics no Azure Monitor. Basta abrir o **alertas** painel à esquerda.

Embora possa obter notificações através de registos de atividades, é altamente recomendável utilizar o Log Analytics, em vez de registos de atividades para monitorizar à escala. Eis o porquê:

- **Limitado a cenários**: Notificações através de registos de atividades aplicam-se apenas a cópias de segurança de VM do Azure. As notificações tem de ser definidas para cada Cofre de serviços de recuperação.
- **Definição de acordo com as**: A atividade de cópia de segurança agendada não se ajusta com a definição mais recente de registos de atividade. Em vez disso, se alinha com [registos de diagnóstico](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview#what-you-can-do-with-diagnostic-logs). Este alinhamento faz com que os efeitos inesperados quando os dados que fluem através da atividade Registrar alterações de canal.
- **Problemas com o canal de registo de atividade**: Nos cofres de serviços de recuperação, os registos de atividades que são colocados de cópia de segurança do Azure, siga um novo modelo. Infelizmente, essa alteração afeta a geração de registos de atividade no Azure Government, Azure Alemanha e Azure China 21Vianet. Se os utilizadores um destes serviços cloud criarem ou configurar alertas de registos de atividades no Azure Monitor, os alertas não são acionados. Além disso, em todas as regiões públicas do Azure, se um usuário [recolhe registos de atividades de serviços de recuperação numa área de trabalho do Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs), estes registos não são apresentados.

Utilize uma área de trabalho do Log Analytics para monitorização e alertas em escala para todas as suas cargas de trabalho que estão protegidos pelo Azure Backup.

## <a name="next-steps"></a>Passos Seguintes

Para criar consultas personalizadas, consulte [modelo de dados do Log Analytics](backup-azure-log-analytics-data-model.md).
