---
title: Utilize definições de diagnóstico para cofres de Serviços de Recuperação
description: Este artigo descreve como usar os eventos de diagnóstico antigos e novos para o Azure Backup.
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: f11c9d2a5b48b9cd27ac6e6f8a00eb5ac0ac7a9d
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617296"
---
# <a name="use-diagnostics-settings-for-recovery-services-vaults"></a>Utilize definições de diagnóstico para cofres de Serviços de Recuperação

A Azure Backup envia eventos de diagnóstico que podem ser recolhidos e utilizados para efeitos de análise, alerta e reporte.

Pode configurar as definições de diagnóstico para um cofre de Serviços de Recuperação através do portal Azure, indo ao cofre e selecionando **as definições**de Diagnóstico . Selecionar **+ Adicionar Definição de Diagnóstico** permite-lhe enviar um ou mais eventos de diagnóstico para uma conta de armazenamento, um hub de eventos ou um espaço de trabalho log Analytics.

![Painel de definições de diagnóstico](./media/backup-azure-diagnostics-events/diagnostics-settings-blade.png)

## <a name="diagnostics-events-available-for-azure-backup-users"></a>Eventos de diagnóstico disponíveis para utilizadores de Backup Azure

A Azure Backup fornece os seguintes eventos de diagnóstico. Cada evento fornece dados detalhados sobre um conjunto específico de artefactos relacionados com a cópia de segurança:

* CoreAzureBackup
* AddonAzureBackupAlerts
* AddonAzureBackupProtectedInstance
* AddonAzureBackupJobs
* Política addonAzureBackup
* AddonAzureBackupStorage

Para mais informações, consulte o [modelo de dados para eventos](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)de diagnóstico de backup azure .

Os dados para estes eventos podem ser enviados para uma conta de armazenamento, um espaço de trabalho log Analytics ou um centro de eventos. Se estiver a enviar estes dados para um espaço de trabalho de Log Analytics, selecione o toggle específico do **Recurso** no ecrã de definições de **Diagnóstico.** Para mais informações, consulte as seguintes secções.

## <a name="use-diagnostics-settings-with-log-analytics"></a>Utilize as definições de diagnóstico com log Analytics

Agora pode utilizar o Azure Backup para enviar dados de diagnóstico de cofre para tabelas dedicadas de Log Analytics para backup. Estas tabelas são chamadas [tabelas específicas de recursos.](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#resource-specific)

Para enviar os dados de diagnóstico do seu cofre para o Log Analytics:

1. Vá ao seu cofre e selecione **Definições de Diagnóstico**. Selecione **+ Adicione a definição de diagnóstico**.
1. Dê um nome à definição de diagnóstico.
1. Selecione a caixa de verificação **Enviar para Registar Analytics** e selecione um espaço de trabalho de Log Analytics.
1. Selecione **Recurso específico** no alternância e selecione os seguintes seis eventos: **CoreAzureBackup,** **AddonAzureBackupJobs,** **AddonAzureBackupAlerts,** **AddonAzureBackupPolicy,** **AddonAzureBackupStorage**e **AddonAzureBackupProtectedInstance**.
1. Selecione **Guardar**.

   ![Modo específico de recursos](./media/backup-azure-diagnostics-events/resource-specific-blade.png)

Depois de os dados entrarem no espaço de trabalho do Log Analytics, são criadas tabelas dedicadas para cada um destes eventos no seu espaço de trabalho. Pode consultar qualquer uma destas mesas diretamente. Pode também realizar uniões ou sindicatos entre estas mesas, se necessário.

> [!IMPORTANT]
> Os seis eventos, nomeadamente, CoreAzureBackup, AddonAzureBackupJobs, AddonAzureBackupAlerts, AddonAzureBackupPolicy, AddonAzureBackupStorage e AddonAzureBackupProtectedInstance, são suportados *apenas* no modo específico de recursos nos [relatórios de backup.](https://docs.microsoft.com/azure/backup/configure-reports) *Se tentar enviar dados para estes seis eventos no modo de diagnóstico Azure, nenhum dado será visível nos relatórios de Backup.*

## <a name="legacy-event"></a>Evento legado

Tradicionalmente, todos os dados de diagnóstico relacionados com a cópia de segurança para um cofre foram contidos num único evento chamado AzureBackupReport. Os seis eventos aqui descritos são, na sua essência, uma decomposição de todos os dados contidos no AzureBackupReport. 

Atualmente, continuamos a apoiar o evento AzureBackupReport para retrocompatibilidade nos casos em que os utilizadores têm consultas personalizadas existentes neste evento. Exemplos são alertas de registo personalizados e visualizações personalizadas. *Recomendamos que se mude para os [novos eventos](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events#diagnostics-events-available-for-azure-backup-users) o mais cedo possível.* Os novos eventos:

- Tornar os dados muito mais fáceis de trabalhar em consultas de registo.
- Proporcionar uma melhor descoberta dos esquemas e da sua estrutura.
- Melhorar o desempenho em ambos os tempos de latência de ingestão e consultas. 

O evento legado no modo de *diagnóstico Azure acabará por ser depreciado. Escolher os novos eventos pode ajudá-lo a evitar migrações complexas mais tarde*. A nossa [solução de reporte](https://docs.microsoft.com/azure/backup/configure-reports) que utiliza o Log Analytics também deixará de suportar dados do evento legado.

### <a name="steps-to-move-to-new-diagnostics-settings-for-a-log-analytics-workspace"></a>Passos para avançar para novas definições de diagnóstico para um espaço de trabalho log Analytics

1. Identifique quais cofres estão a enviar dados para os espaços de trabalho do Log Analytics utilizando o evento legado e as subscrições a que pertencem. Executar os seguintes espaços de trabalho para identificar estes cofres e subscrições.

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
        // Some Workspaces will not have AzureDiagnostics Table, hence you need to use isFuzzy
    let CombinedVaultTable = (){
        CombinedTable | union isfuzzy = true 
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

1. Utilize a [política azure incorporada](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics) em Azure Backup para adicionar uma nova definição de diagnóstico para todos os cofres num âmbito especificado. Esta política adiciona uma nova definição de diagnóstico a cofres que ou não têm uma definição de diagnóstico ou têm apenas uma definição de diagnóstico legado. Esta política pode ser atribuída a toda uma subscrição ou grupo de recursos de cada vez. Deve ter acesso do Proprietário a cada subscrição para a qual a apólice é atribuída.

Pode optar por ter configurações de diagnóstico separadas para o AzureBackupReport e os seis novos eventos até que tenha migrado todas as suas consultas personalizadas para utilizar dados das novas tabelas. A imagem que se segue mostra um exemplo de um cofre que tem duas configurações de diagnóstico. A primeira definição, denominada **Setting1,** envia dados de um evento AzureBackupReport para um espaço de trabalho de Log Analytics no modo de diagnóstico Azure. A segunda definição, denominada **Setting2,** envia dados dos seis novos eventos de backup do Azure para um espaço de trabalho de Log Analytics no modo específico de recursos.

![Duas configurações](./media/backup-azure-diagnostics-events/two-settings-example.png)

> [!IMPORTANT]
> O evento AzureBackupReport é suportado *apenas* no modo de diagnóstico Azure. *Se tentar enviar dados para este evento no modo específico de recursos, nenhum dado fluirá para o espaço de trabalho do Log Analytics.*

> [!NOTE]
> O alternância para **diagnósticos Azure** ou **específico de Recursos** só aparece se o utilizador selecionar Enviar para Registar **Analytics**. Para enviar dados para uma conta de armazenamento ou um centro de eventos, um utilizador seleciona o destino necessário e seleciona as caixas de verificação para qualquer um dos eventos pretendidos, sem qualquer entrada adicional. Mais uma vez, recomendamos que não escolha o evento legado AzureBackupReport em curso.

## <a name="send-azure-site-recovery-events-to-log-analytics"></a>Envie eventos de recuperação de sites azure para registar análises

Os eventos de backup azure e de recuperação do local azure são enviados a partir do mesmo cofre dos Serviços de Recuperação. A Recuperação do Site Azure não está atualmente disponível para tabelas específicas de recursos. Os utilizadores que pretendam enviar eventos de recuperação de sites Azure para o Log Analytics são direcionados para utilizar *apenas*o modo de diagnóstico Azure , como mostra a imagem. *A escolha do modo específico de recursos para eventos de recuperação do site Azure impedirá que os dados necessários sejam enviados para o espaço*de trabalho do Log Analytics .

![Eventos de Recuperação do Site](./media/backup-azure-diagnostics-events/site-recovery-settings.png)

Em resumo:

* Se já tiver diagnósticos de Log Analytics configurados com o Azure Diagnostics e tiver escrito consultas personalizadas em cima dele, mantenha essa definição *intacta* até migrar as suas consultas para utilizar dados dos novos eventos.
* Se também quiser embarcar em novas tabelas, como recomendamos, crie uma **nova** definição de diagnóstico, selecione **Recurso específico,** e selecione os seis novos eventos.
* Se está atualmente a enviar eventos de recuperação de sites azure para log analytics, *não* escolha o modo específico de recursos para estes eventos. Caso contrário, os dados para estes eventos não fluirão para o seu espaço de trabalho Log Analytics. Em vez disso, crie uma definição de diagnóstico adicional, **selecione diagnósticos Azure,** e selecione os eventos de recuperação do site Azure relevantes.

A imagem que se segue mostra um exemplo de um utilizador que tem três definições de diagnóstico para um cofre. A primeira definição, denominada **Setting1,** envia dados de um evento AzureBackupReport para um espaço de trabalho de Log Analytics no modo de diagnóstico Azure. A segunda definição, denominada **Setting2,** envia dados dos seis novos eventos de backup do Azure para um espaço de trabalho de Log Analytics no modo específico de recursos. A terceira definição, denominada **Setting3,** envia dados dos eventos de Recuperação do Site Azure para um espaço de trabalho de Log Analytics no modo de diagnóstico Azure.

![Três configurações](./media/backup-azure-diagnostics-events/three-settings-example.png)

## <a name="next-steps"></a>Passos seguintes

[Conheça o modelo de dados log Analytics para os eventos de diagnóstico](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)
