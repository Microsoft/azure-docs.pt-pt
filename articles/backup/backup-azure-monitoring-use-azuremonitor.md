---
title: Monitor Azure Backup com Monitor Azure
description: Monitorize as cargas de trabalho de backup azure e crie alertas personalizados utilizando o Monitor Azure.
ms.topic: conceptual
ms.date: 06/04/2019
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: 0673291ac6bd1692c6ebe07540e05077e3025d55
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77583878"
---
# <a name="monitor-at-scale-by-using-azure-monitor"></a>Monitor à escala utilizando o Monitor Azure

A Azure Backup fornece [capacidades de monitorização e alerta incorporadas](backup-azure-monitoring-built-in-monitor.md) num cofre dos Serviços de Recuperação. Estas capacidades estão disponíveis sem qualquer infraestrutura de gestão adicional. Mas este serviço incorporado é limitado nos seguintes cenários:

- Se monitorizar dados de vários cofres dos Serviços de Recuperação através de subscrições
- Se o canal de notificação preferido *não* for e-mail
- Se os utilizadores quiserem alertas para mais cenários
- Se quiser visualizar informações de um componente no local, como o System Center Data Protection Manager em Azure, que o portal não mostra em [**Backup Jobs**](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault) ou [**Backup Alerts**](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault)

## <a name="using-log-analytics-workspace"></a>Usando o espaço de trabalho log Analytics

### <a name="create-alerts-by-using-log-analytics"></a>Criar alertas usando log analytics

No Monitor Azure, pode criar os seus próprios alertas num espaço de trabalho de Log Analytics. No espaço de trabalho, utiliza grupos de *ação Azure* para selecionar o seu mecanismo de notificação preferido.

> [!IMPORTANT]
> Para obter informações sobre o custo da criação desta consulta, consulte os preços do [Monitor Azure](https://azure.microsoft.com/pricing/details/monitor/).

Abra a secção **de Registos** do espaço de trabalho log analytics e escreva uma consulta aos seus próprios Registos. Quando selecionar a Nova Regra de **Alerta,** abre-se a página de criação de alerta Do Monitor Azure, como mostra a seguinte imagem.

![Criar um alerta num espaço de trabalho de Log Analytics](media/backup-azure-monitoring-laworkspace/custom-alert.png)

Aqui o recurso já está marcado como o espaço de trabalho log Analytics, e a integração do grupo de ação é fornecida.

![A página de criação de alerta log Analytics](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

#### <a name="alert-condition"></a>Estado de alerta

A característica determinante de um alerta é a sua condição de desencadeamento. Selecione **Condição** para carregar automaticamente a consulta Kusto na página **DeRegistos,** como mostrado na imagem seguinte. Aqui pode editar a condição para atender às suas necessidades. Para mais informações, consulte [as consultas da Sample Kusto.](#sample-kusto-queries)

![Configuração de uma condição de alerta](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

Se necessário, pode editar a consulta de Kusto. Escolha um limiar, período e frequência. O limiar determina quando o alerta será levantado. O período é a janela de tempo em que a consulta é executada. Por exemplo, se o limiar for superior a 0, o período é de 5 minutos, e a frequência é de 5 minutos, então a regra executa a consulta a cada 5 minutos, revendo os 5 minutos anteriores. Se o número de resultados for superior a 0, é notificado através do grupo de ação selecionado.

#### <a name="alert-action-groups"></a>Grupos de ação de alerta

Utilize um grupo de ação para especificar um canal de notificação. Para ver os mecanismos de notificação disponíveis, em **grupos action,** selecione **Create New**.

![Mecanismos de notificação disponíveis na janela "Adicionar grupo de ação"](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

Só pode satisfazer todos os requisitos de alerta e monitorização do Log Analytics, ou pode utilizar o Log Analytics para complementar as notificações incorporadas.

Para mais informações, consulte [Criar, visualizar e gerir alertas](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) de registo utilizando o Monitor Azure e criar e gerir grupos de [ação no portal Azure.](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)

### <a name="sample-kusto-queries"></a>Amostra de consultas kusto

Os gráficos padrão dão-lhe consultas kusto para cenários básicos nos quais pode construir alertas. Também pode modificar as consultas para obter os dados que pretende ser alertado. Colhe a seguinte amostra de consultas Kusto na página **DeRegistos** e, em seguida, criar alertas sobre as consultas:

- Todos os trabalhos de backup bem sucedidos

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | where JobStatus=="Completed"
    ````

- Todos os trabalhos de apoio falhados

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | where JobStatus=="Failed"
    ````

- Todos os empregos de backup azure vm bem-sucedidos

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
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

- Todos os trabalhos de backup de log SQL bem sucedidos

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup" and JobOperationSubType=="Log"
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

- Todos os empregos bem sucedidos do agente azure backup

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
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

Os dados de diagnóstico do cofre são bombeados para o espaço de trabalho log Analytics com algum atraso. Todos os eventos chegam ao espaço de trabalho log Analytics *20 a 30 minutos* depois de ser empurrado do cofre dos Serviços de Recuperação. Aqui estão mais detalhes sobre o atraso:

- Em todas as soluções, os alertas integrados do serviço de reserva são empurrados assim que são criados. Então, geralmente aparecem no espaço de trabalho log Analytics após 20 a 30 minutos.
- Em todas as soluções, os postos de trabalho a pedido e a restauração dos postos de trabalho são empurrados assim que *terminam.*
- Para todas as soluções, com exceção do backup SQL, os trabalhos de backup programados são empurrados assim que *terminam*.
- Para a cópia de segurança SQL, porque as cópias de segurança de log podem ocorrer a cada 15 minutos, a informação para todos os trabalhos de backup programados completos, incluindo registos, é loteada e empurrada a cada 6 horas.
- Em todas as soluções, outras informações como o item de backup, política, pontos de recuperação, armazenamento, e assim por diante, é empurrada pelo menos *uma vez por dia.*
- Uma mudança na configuração de backup (como mudar a política ou editar a política) desencadeia um impulso de todas as informações de backup relacionadas.

## <a name="using-the-recovery-services-vaults-activity-logs"></a>Utilização dos registos de atividade do cofre dos Serviços de Recuperação

> [!CAUTION]
> Os seguintes passos aplicam-se apenas às *cópias de segurança Do VM Azure.* Não é possível utilizar estes passos para soluções como o agente de backup Azure, cópias de segurança SQL dentro do Azure ou do Azure Files.

Também pode utilizar registos de atividade para obter notificação para eventos como o sucesso de backup. Para começar, siga estes passos:

1. Inscreva-se no portal Azure.
1. Abra o cofre relevante dos Serviços de Recuperação.
1. Nas propriedades do cofre, abra a secção de registo de **atividade.**

Para identificar o registo adequado e criar um alerta:

1. Verifique se está a receber registos de atividade para cópias de segurança bem sucedidas aplicando os filtros mostrados na imagem seguinte. Mude o valor **timespan** conforme necessário para ver os registos.

   ![Filtragem para encontrar registos de atividade para backups De VM Azure](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

1. Selecione o nome de operação para ver os detalhes relevantes.
1. Selecione **Nova regra de alerta** para abrir a página de regra **Criar.**
1. Crie um alerta seguindo os passos em [Criar, visualizar e gerir alertas](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)de registo de atividade utilizando o Monitor Azure .

   ![Nova regra de alerta](media/backup-azure-monitoring-laworkspace/new-alert-rule.png)

Aqui o recurso é o cofre dos Serviços de Recuperação em si. Repita os mesmos passos para todos os cofres em que pretende ser notificado através de registos de atividade. A condição não terá um limiar, período ou frequência porque este alerta é baseado em eventos. Assim que o registo de atividade relevante é gerado, o alerta é levantado.

## <a name="using-log-analytics-to-monitor-at-scale"></a>Usando log analytics para monitorizar à escala

Pode ver todos os alertas criados a partir de registos de atividade e espaços de trabalho de Log Analytics no Monitor Azure. Basta abrir o painel de **alertas** à esquerda.

Embora possa obter notificações através de registos de atividade, recomendamos vivamente a utilização de log Analytics em vez de registos de atividade para monitorização em escala. Aqui está o porquê:

- **Cenários limitados**: As notificações através de registos de atividade aplicam-se apenas a cópias de segurança Do VM Do Azure. As notificações devem ser configuradas para todos os cofres dos Serviços de Recuperação.
- **Ajuste de definição**: A atividade de backup programada não se enquadra na definição mais recente de registos de atividade. Em vez disso, alinha-se com [registos](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#what-you-can-do-with-platform-logs-in-a-workspace)de recursos . Este alinhamento causa efeitos inesperados quando os dados que fluem através do canal de registo de atividade mudam.
- **Problemas com o canal de registo de atividades**: Nos cofres dos Serviços de Recuperação, os registos de atividade que são bombeados do Azure Backup seguem um novo modelo. Infelizmente, esta mudança afeta a geração de registos de atividades no Governo Azure, na Alemanha Azure e na Azure China 21Vianet. Se os utilizadores destes serviços na nuvem criarem ou configurarem quaisquer alertas de registos de atividade no Monitor Azure, os alertas não são desencadeados. Além disso, em todas as regiões públicas do Azure, se um utilizador recolhe os registos de atividade dos Serviços de [Recuperação num espaço](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs)de trabalho do Log Analytics, estes registos não aparecem.

Utilize um espaço de trabalho de Log Analytics para monitorização e alerta em escala para todas as suas cargas de trabalho protegidas por Backup Azure.

## <a name="next-steps"></a>Passos seguintes

Para criar consultas personalizadas, consulte o modelo de [dados Log Analytics](backup-azure-reports-data-model.md).
