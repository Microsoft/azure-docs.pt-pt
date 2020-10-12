---
title: Monitor Azure Backup com Monitor Azure
description: Monitorize as cargas de trabalho de backup do Azure e crie alertas personalizados utilizando o Azure Monitor.
ms.topic: conceptual
ms.date: 06/04/2019
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: 3f5f663a2f0ed0f91cc414d352e975a2ff3b9649
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88827159"
---
# <a name="monitor-at-scale-by-using-azure-monitor"></a>Monitorar à escala utilizando o Monitor Azure

O Azure Backup fornece [capacidades de monitorização e alerta incorporadas](backup-azure-monitoring-built-in-monitor.md) num cofre dos Serviços de Recuperação. Estas funcionalidades estão disponíveis sem infraestruturas de gestão adicionais. Mas este serviço incorporado é limitado nos seguintes cenários:

- Se monitorizar dados de vários cofres dos Serviços de Recuperação através de subscrições
- Se o canal de notificação preferido *não* for e-mail
- Se os utilizadores querem alertas para mais cenários
- Se quiser visualizar informações de um componente no local, como o Gestor de Proteção de Dados do Centro de Sistema em Azure, que o portal não mostra em [**Backup Jobs**](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault) ou [**Backup Alerts**](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault)

## <a name="using-log-analytics-workspace"></a>Usando o espaço de trabalho Log Analytics

### <a name="create-alerts-by-using-log-analytics"></a>Criar alertas utilizando o Log Analytics

No Azure Monitor, pode criar os seus próprios alertas num espaço de trabalho log analytics. No espaço de trabalho, utiliza *grupos de ação Azure* para selecionar o seu mecanismo de notificação preferido.

> [!IMPORTANT]
> Para obter informações sobre o custo de criação desta consulta, consulte [os preços do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

Abra a secção **de Registos** do espaço de trabalho Log Analytics e crie uma consulta para os seus próprios Registos. Quando selecionar **Nova Regra de Alerta,** abre-se a página de criação de alerta do Monitor Azure, como mostra a seguinte imagem.

![Crie um alerta num espaço de trabalho log analytics](media/backup-azure-monitoring-laworkspace/custom-alert.png)

Aqui o recurso já está marcado como o espaço de trabalho Log Analytics, e a integração do grupo de ação é fornecida.

![A página de criação de alerta Log Analytics](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

#### <a name="alert-condition"></a>Estado de alerta

A característica determinante de um alerta é a sua condição de desencadeamento. Selecione **Condição** para carregar automaticamente a consulta kusto na página **'Logs',** como mostrado na imagem seguinte. Aqui pode editar a condição de acordo com as suas necessidades. Para mais informações, consulte [as consultas de Sample Kusto.](#sample-kusto-queries)

![Configuração de uma condição de alerta](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

Se necessário, pode editar a consulta kusto. Escolha um limiar, período e frequência. O limiar determina quando o alerta será levantado. O período é a janela de tempo em que a consulta é executada. Por exemplo, se o limiar for superior a 0, o período é de 5 minutos, e a frequência é de 5 minutos, então a regra executa a consulta a cada 5 minutos, revendo os 5 minutos anteriores. Se o número de resultados for superior a 0, é notificado através do grupo de ação selecionado.

> [!NOTE]
> Para executar a regra de alerta uma vez por dia, em todos os eventos/registos que foram criados no dia dado, altere o valor tanto do 'período' como da 'frequência' para 1440, ou seja, 24 horas.

#### <a name="alert-action-groups"></a>Grupos de ação de alerta

Utilize um grupo de ação para especificar um canal de notificação. Para ver os mecanismos de notificação disponíveis, nos **grupos action,** selecione **Create New**.

![Mecanismos de notificação disponíveis na janela "Adicionar grupo de ação"](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

Pode satisfazer todos os requisitos de alerta e monitorização apenas a partir do Log Analytics, ou pode utilizar o Log Analytics para complementar notificações incorporadas.

Para obter mais informações, consulte [Criar, visualizar e gerir alertas de registo utilizando o Monitor E](../azure-monitor/platform/alerts-log.md) Criar e gerir [grupos de ação no portal Azure](../azure-monitor/platform/action-groups.md).

### <a name="sample-kusto-queries"></a>Experimente consultas kusto

Os gráficos predefinidos dão-lhe consultas kusto para cenários básicos sobre os quais pode construir alertas. Também pode modificar as consultas para obter os dados que pretende ser alertados. Cole as seguintes consultas de Kusto na página **De Registos** e, em seguida, crie alertas nas consultas:

- Todos os trabalhos de backup bem sucedidos

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
    | where JobStatus=="Completed"
    ````

- Todos os trabalhos de reserva falhados

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
    | where JobStatus=="Failed"
    ````

- Todos os trabalhos de backup da Azure VM

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="VM" and BackupManagementType=="IaaSVM"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- Todos os trabalhos de backup de registos sql bem sucedidos

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup" and JobOperationSubType=="Log"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="SQLDataBase" and BackupManagementType=="AzureWorkload"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- Todos os trabalhos bem sucedidos de agente de backup da Azure

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="FileFolder" and BackupManagementType=="MAB"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- Armazenamento de backup consumido por item de backup

    ````Kusto
    CoreAzureBackup
    //Get all Backup Items
    | where OperationName == "BackupItem"
    //Get distinct Backup Items
    | distinct BackupItemUniqueId, BackupItemFriendlyName
    | join kind=leftouter
    (AddonAzureBackupStorage
    | where OperationName == "StorageAssociation"
    //Get latest record for each Backup Item
    | summarize arg_max(TimeGenerated, *) by BackupItemUniqueId
    | project BackupItemUniqueId , StorageConsumedInMBs)
    on BackupItemUniqueId
    | project BackupItemUniqueId , BackupItemFriendlyName , StorageConsumedInMBs
    | sort by StorageConsumedInMBs desc
    ````

### <a name="diagnostic-data-update-frequency"></a>Frequência de atualização de dados de diagnóstico

Os dados de diagnóstico do cofre são bombeados para o espaço de trabalho Log Analytics com algum atraso. Cada evento chega ao espaço de trabalho do Log Analytics *20 a 30 minutos* depois de ser empurrado do cofre dos Serviços de Recuperação. Aqui estão mais detalhes sobre o atraso:

- Em todas as soluções, os alertas incorporados do serviço de backup são empurrados assim que são criados. Por isso, normalmente aparecem no espaço de trabalho do Log Analytics após 20 a 30 minutos.
- Em todas as soluções, os postos de trabalho de apoio a pedido e o restabelecemento de postos de trabalho são empurrados assim que *terminam.*
- Para todas as soluções, exceto o backup SQL, os trabalhos de backup programados são empurrados assim que *terminam*.
- Para a cópia de segurança SQL, como as cópias de segurança podem ocorrer a cada 15 minutos, as informações para todos os trabalhos de backup programados, incluindo registos, são lotadas e empurradas a cada 6 horas.
- Em todas as soluções, outras informações como o item de backup, política, pontos de recuperação, armazenamento, e assim por diante, são empurradas pelo menos *uma vez por dia.*
- Uma alteração na configuração de backup (como mudar a política ou a política de edição) desencadeia um impulso de todas as informações de backup relacionadas.

## <a name="using-the-recovery-services-vaults-activity-logs"></a>Usando os registos de atividade do cofre dos Serviços de Recuperação

> [!CAUTION]
> Os seguintes passos aplicam-se apenas às *cópias de segurança Azure VM.* Não é possível utilizar estes passos para soluções como o agente Azure Backup, backups SQL dentro do Azure ou ficheiros Azure.

Também pode usar registos de atividades para receber a notificação de eventos como o sucesso de backup. Para começar, siga estes passos:

1. Inicie sessão no Portal do Azure.
2. Abra o cofre dos Serviços de Recuperação relevantes.
3. Nas propriedades do cofre, abra a secção **de registo de atividade.**

Para identificar o registo adequado e criar um alerta:

1. Verifique se está a receber registos de atividade para cópias de segurança bem sucedidas aplicando os filtros apresentados na seguinte imagem. Altere o valor **timespan** conforme necessário para visualizar os registos.

   ![Filtragem para encontrar registos de atividade para backups Azure VM](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

2. Selecione o nome da operação para ver os detalhes relevantes.
3. Selecione **Nova regra de alerta** para abrir a página de regra **Criar.**
4. Crie um alerta seguindo os passos em [Criar, ver e gerir alertas de registo de atividade utilizando o Azure Monitor](../azure-monitor/platform/alerts-activity-log.md).

   ![Nova regra de alerta](media/backup-azure-monitoring-laworkspace/new-alert-rule.png)

Aqui o recurso é o cofre dos Serviços de Recuperação. Repita os mesmos passos para todos os cofres em que pretende ser notificado através de registos de atividade. A condição não terá um limiar, período ou frequência porque este alerta é baseado em eventos. Assim que o registo de atividade relevante é gerado, o alerta é levantado.

## <a name="using-log-analytics-to-monitor-at-scale"></a>Utilização de Log Analytics para monitorizar à escala

Pode ver todos os alertas criados a partir de registos de atividades e espaços de trabalho log analytics no Azure Monitor. Basta abrir o **painel de alertas** à esquerda.

Embora possa obter notificações através de registos de atividade, recomendamos vivamente a utilização de Log Analytics em vez de registos de atividade para monitorização em escala. Eis o porquê:

- **Cenários limitados**: As notificações através de registos de atividade aplicam-se apenas às cópias de segurança Azure VM. As notificações devem ser criadas para cada cofre dos Serviços de Recuperação.
- **Ajuste de definição**: A atividade de backup programada não se enquadra na definição mais recente de registos de atividade. Em vez disso, alinha-se com [registos de recursos.](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace) Este alinhamento causa efeitos inesperados quando os dados que fluem através do canal de registo de atividade mudam.
- **Problemas com o canal de registo de atividades**: Nos cofres dos Serviços de Recuperação, os registos de atividade que são bombeados a partir do Azure Backup seguem um novo modelo. Infelizmente, esta mudança afeta a geração de registos de atividades no Governo de Azure, na Alemanha e na Azure China 21Vianet. Se os utilizadores destes serviços na nuvem criarem ou configurarem quaisquer alertas de registos de atividade no Azure Monitor, os alertas não são acionados. Além disso, em todas as regiões públicas do Azure, se um utilizador [recolher registos de atividade dos Serviços de Recuperação num espaço de trabalho do Log Analytics,](../azure-monitor/platform/activity-log.md)estes registos não aparecem.

Utilize um espaço de trabalho Log Analytics para monitorizar e alertar à escala para todas as suas cargas de trabalho protegidas por Azure Backup.

## <a name="next-steps"></a>Passos seguintes

Para criar consultas personalizadas, consulte [o modelo de dados do Log Analytics.](backup-azure-reports-data-model.md)
