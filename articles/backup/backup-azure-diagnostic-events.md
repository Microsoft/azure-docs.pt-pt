---
title: Use definições de diagnóstico para cofres de serviços de recuperação
description: Este artigo descreve como usar os antigos e novos eventos de diagnóstico para Azure Backup.
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: b2130f06e17dd2b5cf8461d4e58342ee41c14f96
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100575416"
---
# <a name="use-diagnostics-settings-for-recovery-services-vaults"></a>Use definições de diagnóstico para cofres de serviços de recuperação

A Azure Backup envia eventos de diagnóstico que podem ser recolhidos e usados para efeitos de análise, alerta e reporte.

Pode configurar as definições de diagnóstico de um cofre dos Serviços de Recuperação através do portal Azure, indo ao cofre e selecionando **as definições de Diagnóstico .** Selecionar **+ Adicionar Definição de Diagnóstico** permite-lhe enviar um ou mais eventos de diagnóstico para uma conta de armazenamento, um centro de eventos ou um espaço de trabalho Log Analytics.

![Painel de definições de diagnóstico](./media/backup-azure-diagnostics-events/diagnostics-settings-blade.png)

## <a name="diagnostics-events-available-for-azure-backup-users"></a>Eventos de diagnóstico disponíveis para utilizadores do Azure Backup

A Azure Backup fornece os seguintes eventos de diagnóstico. Cada evento fornece dados detalhados sobre um conjunto específico de artefactos relacionados com backup:

* CoreAzureBackup
* AddonAzureBackupAlerts
* AddonAzureBackupProtectedInstance
* AddonAzureBackupJobs
* AddonAzureBackupPolicy
* AddonAzureBackupstorage

Se ainda estiver a utilizar o [evento legado](#legacy-event) AzureBackupReport, recomendamos que mude para usar os eventos acima.

Para obter mais informações, consulte [o modelo de dados para eventos de diagnóstico de backup Azure](./backup-azure-reports-data-model.md).

Os dados para estes eventos podem ser enviados para uma conta de armazenamento, um espaço de trabalho Log Analytics ou um centro de eventos. Se estiver a enviar estes dados para um espaço de trabalho do Log Analytics, selecione o **recurso específico** para alternar no ecrã de **definições de Diagnóstico.** Para mais informações, consulte as seguintes secções.

## <a name="use-diagnostics-settings-with-log-analytics"></a>Use definições de diagnóstico com Log Analytics

Agora pode utilizar o Azure Backup para enviar dados de diagnóstico de cofre para tabelas dedicadas do Log Analytics para cópias de segurança. Estas tabelas são chamadas [tabelas específicas de recursos.](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)

Para enviar os dados de diagnóstico do seu cofre para registar analítica:

1. Vá ao seu cofre e selecione **Definições de Diagnóstico**. Selecione **+ Adicionar definição de diagnóstico**.
1. Dê um nome à definição de diagnóstico.
1. Selecione a caixa de verificação **Enviar para Registar Analytics** e selecione um espaço de trabalho Log Analytics.
1. Selecione **Recursos específicos** no toggle, e selecione os seguintes seis eventos: **CoreAzureBackup**, **AddonAzureBackupJobs**, **AddonAzureBackupAlerts**, **AddonAzureBackupPolicy,** **AddonAzureBackupStorage**, e **AddonAzureBackupProtectedInstance**.
1. Selecione **Guardar**.

   ![Modo específico de recursos](./media/backup-azure-diagnostics-events/resource-specific-blade.png)

Após o fluxo de dados para o espaço de trabalho Log Analytics, são criadas tabelas dedicadas para cada um destes eventos no seu espaço de trabalho. Pode consultar qualquer uma destas mesas diretamente. Você também pode realizar juntas ou sindicatos entre estas mesas, se necessário.

> [!IMPORTANT]
> Os seis eventos, nomeadamente CoreAzureBackup, AddonAzureBackupJobs, AddonAzureBackupAlerts, AddonAzureBackupPolice, AddonAzureBackupPolicy, AddonAzureBackupStorage e AddonAzureBackupProtectedInstance, são suportados *apenas* no modo específico de recursos nos [relatórios backup.](./configure-reports.md) *Se tentar enviar dados para estes seis eventos no modo de diagnóstico Azure, nenhum dado será visível nos relatórios de Backup.*

## <a name="legacy-event"></a>Evento legado

Tradicionalmente, todos os dados de diagnóstico relacionados com backup para um cofre foram contidos num único evento chamado AzureBackupReport. Os seis eventos aqui descritos são, na sua essência, uma decomposição de todos os dados contidos no AzureBackupReport.

Atualmente, continuamos a apoiar o evento AzureBackupReport para retrocompatibilidade nos casos em que os utilizadores tenham consultas personalizadas existentes neste evento. Exemplos são alertas de registo personalizados e visualizações personalizadas. *Recomendamos que se mude para os [novos eventos](#diagnostics-events-available-for-azure-backup-users) o mais cedo possível.* Os novos eventos:

* Torne os dados muito mais fáceis de trabalhar em consultas de registo.
* Proporcionar uma melhor descoberta dos esquemas e da sua estrutura.
* Melhore o desempenho em ambos os tempos de latência e consulta.

*O evento legado no modo de diagnóstico Azure acabará por ser depreciado. Escolher os novos eventos pode ajudá-lo a evitar migrações complexas mais tarde.* A nossa [solução de reporte](./configure-reports.md) que utiliza o Log Analytics também deixará de suportar dados do evento legado.

### <a name="steps-to-move-to-new-diagnostics-settings-for-a-log-analytics-workspace"></a>Passos para passar para novas definições de diagnóstico para um espaço de trabalho Log Analytics

1. Identifique quais os cofres que estão a enviar dados para os espaços de trabalho do Log Analytics utilizando o evento legado e as subscrições a que pertencem. Faça a seguinte consulta em cada um dos seus espaços de trabalho para identificar estes cofres e subscrições.

    ````Kusto
    let RangeStart = startofday(ago(3d));
    let VaultUnderAzureDiagnostics = (){
        AzureDiagnostics
        | where TimeGenerated >= RangeStart | where Category == "AzureBackupReport" and OperationName == "Vault" and SchemaVersion_s == "V2"
        | summarize arg_max(TimeGenerated, *) by ResourceId
        | project ResourceId, Category};
    let VaultUnderResourceSpecific = (){
        CoreAzureBackup
        | where TimeGenerated >= RangeStart | where OperationName == "Vault"
        | summarize arg_max(TimeGenerated, *) by ResourceId
        | project ResourceId, Category};
        // Some Workspaces will not have AzureDiagnostics Table, so you need to use isFuzzy
    let CombinedVaultTable = (){
        union isfuzzy = true
        (VaultUnderAzureDiagnostics() ),
        (VaultUnderResourceSpecific() )
        | distinct ResourceId, Category};
    CombinedVaultTable | where Category == "AzureBackupReport"
    | join kind = leftanti (
    CombinedVaultTable | where Category == "CoreAzureBackup"
    ) on ResourceId
    | parse ResourceId with * "SUBSCRIPTIONS/" SubscriptionId:string "/RESOURCEGROUPS" * "MICROSOFT.RECOVERYSERVICES/VAULTS/" VaultName:string
    | project ResourceId, SubscriptionId, VaultName
    ````

    Abaixo está uma imagem da consulta que está sendo executada em um dos espaços de trabalho:

    ![Consulta de espaço de trabalho](./media/backup-azure-diagnostics-events/workspace-query.png)

2. Utilize as [definições de Política Azure incorporadas](./azure-policy-configure-diagnostics.md) no Azure Backup para adicionar uma nova definição de diagnóstico para todos os cofres num âmbito especificado. Esta política adiciona uma nova definição de diagnóstico a cofres que não têm uma definição de diagnóstico ou têm apenas uma definição de diagnóstico legado. Esta política pode ser atribuída a toda uma subscrição ou grupo de recursos de cada vez. Tem de ter acesso ao Proprietário a cada subscrição para a qual a apólice é atribuída.

Pode optar por ter configurações de diagnóstico separadas para o AzureBackupReport e os seis novos eventos até migrar todas as suas consultas personalizadas para utilizar dados das novas tabelas. A imagem a seguir mostra um exemplo de um cofre que tem duas configurações de diagnóstico. A primeira definição, denominada **Setting1,** envia dados de um evento AzureBackupReport para um espaço de trabalho Log Analytics no modo de diagnóstico Azure. A segunda definição, denominada **Setting2,** envia dados dos seis novos eventos de Backup Azure para um espaço de trabalho Log Analytics no modo específico de recursos.

![Duas configurações](./media/backup-azure-diagnostics-events/two-settings-example.png)

> [!IMPORTANT]
> O evento AzureBackupReport é suportado *apenas* no modo diagnóstico Azure. *Se tentar enviar dados para este evento no modo específico de recursos, nenhum dado fluirá para o espaço de trabalho do Log Analytics.*

> [!NOTE]
> O toggle para **diagnósticos Azure** ou **especificação de recursos** só aparece se o utilizador selecionar **Enviar para Registar Analytics**. Para enviar dados para uma conta de armazenamento ou um centro de eventos, um utilizador seleciona o destino pretendido e seleciona as caixas de verificação para qualquer um dos eventos pretendidos, sem entradas adicionais. Mais uma vez, recomendamos que não escolha o evento legado AzureBackupReport em curso.

## <a name="send-azure-site-recovery-events-to-log-analytics"></a>Enviar eventos de recuperação do site Azure para registar análises

Os eventos de Backup e Azure Site Recovery são enviados a partir do mesmo cofre dos Serviços de Recuperação. A recuperação do site Azure não está atualmente disponível para tabelas específicas de recursos. Os utilizadores que pretendam enviar eventos de Recuperação do Site Azure para o Log Analytics são direcionados *apenas* para utilizar o modo de diagnóstico Azure , como mostra a imagem. *A escolha do modo específico de recursos para eventos de recuperação do local de Azure impedirá que os dados necessários sejam enviados para o espaço de trabalho Do Log Analytics*.

![Eventos de recuperação do local](./media/backup-azure-diagnostics-events/site-recovery-settings.png)

Para resumir:

* Se já tiver diagnósticos de Log Analytics configurados com Azure Diagnostics e tiver consultas personalizadas escritas em cima dele, mantenha essa definição *intacta* até migrar as suas consultas para utilizar dados dos novos eventos.
* Se também quiser embarcar em novas tabelas, como recomendamos, crie uma **nova** definição de diagnóstico, selecione **Recursos específicos** e selecione os seis novos eventos.
* Se está neste momento a enviar eventos de Recuperação do Site Azure para registar analytics, *não* escolha o modo específico de recursos para estes eventos. Caso contrário, os dados para estes eventos não fluirão para o seu espaço de trabalho Log Analytics. Em vez disso, crie uma definição de diagnóstico adicional, selecione **diagnósticos Azure** e selecione os eventos relevantes de Recuperação do Local de Azure.

A imagem a seguir mostra um exemplo de um utilizador que tem três configurações de diagnóstico para um cofre. A primeira definição, denominada **Setting1,** envia dados de um evento AzureBackupReport para um espaço de trabalho Log Analytics no modo de diagnóstico Azure. A segunda definição, denominada **Setting2,** envia dados dos seis novos eventos de Backup Azure para um espaço de trabalho Log Analytics no modo específico de recursos. A terceira definição, denominada **Setting3,** envia dados dos eventos de Recuperação do Local Azure para um espaço de trabalho Log Analytics no modo de diagnóstico Azure.

![Três configurações](./media/backup-azure-diagnostics-events/three-settings-example.png)

## <a name="next-steps"></a>Passos seguintes

[Aprenda o modelo de dados do Log Analytics para os eventos de diagnóstico](./backup-azure-reports-data-model.md)
