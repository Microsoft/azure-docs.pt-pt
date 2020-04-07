---
title: Utilização de definições de diagnóstico para cofres de serviços de recuperação
description: Um artigo que descreve como usar os eventos de diagnóstico antigos e novos para o Azure Backup
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: d10bedf3818559971eff12624152d0e797f6c3cc
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80672788"
---
# <a name="using-diagnostics-settings-for-recovery-services-vaults"></a>Utilizar Definições de Diagnóstico para os Cofres dos Serviços de Recuperação

A Azure Backup envia eventos de diagnóstico que podem ser recolhidos e utilizados para efeitos de análise, alerta e reporte. 

Pode configurar as definições de diagnóstico para um Cofre de Serviços de Recuperação através do portal Azure, navegando até ao cofre e clicando no item do menu Definições de **Diagnóstico.** Clicar em **+ Adicionar Definição de Diagnóstico** permite-lhe enviar um ou mais eventos de diagnóstico para uma Conta de Armazenamento, Hub de Eventos ou um Espaço de Trabalho de Log Analytics (LA).

![Lâmina de definições de diagnóstico](./media/backup-azure-diagnostics-events/diagnostics-settings-blade.png)

## <a name="diagnostics-events-available-for-azure-backup-users"></a>Eventos de diagnóstico disponíveis para utilizadores de backup azure

A Azure Backup fornece os seguintes eventos de diagnóstico, cada um dos quais fornece dados detalhados sobre um conjunto específico de artefactos relacionados com a cópia de segurança:

* CoreAzureBackup
* AddonAzureBackupAlerts
* AddonAzureBackupProtectedInstance
* AddonAzureBackupJobs
* Política addonAzureBackup
* AddonAzureBackupStorage

[Modelo de dados para eventos de diagnóstico de backup azure](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)

Os dados para estes eventos podem ser enviados para uma conta de armazenamento, espaço de trabalho de LA ou um Hub de Eventos. Se estiver a enviar estes dados para um espaço de trabalho de LA, tem de selecionar o toggle Específico do **Recurso** no ecrã de Definição de **Diagnósticos** (ver mais informações nas secções abaixo).

## <a name="using-diagnostics-settings-with-log-analytics-la"></a>Utilização de Definições de Diagnóstico com Log Analytics (LA)

Alinhando-se com o roteiro azure Log Analytics, o Azure Backup permite-lhe agora enviar dados de diagnóstico de cofre para tabelas de LA dedicadas para Backup. Estes são referidos como [tabelas Específicas](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#resource-specific)de Recursos.

Para enviar os dados de diagnóstico do seu cofre para LA:

1.    Navegue até ao seu cofre e clique em **Definições de Diagnóstico**. Clique **+ Adicione a definição de diagnóstico**.
2.    Dê um nome à definição de Diagnósticos.
3.    Verifique a caixa **Enviar para Registar Analytics** e selecione um espaço de trabalho de Log Analytics.
4.    Selecione **Recurso Específico** no alternância e verifique os seis eventos seguintes - **CoreAzureBackup,** **AddonAzureBackupAlerts,** **AddonAzureBackupProtectedInstance**, **AddonAzureBackupJobs,** **AddonAzureBackupPolicy**e **AddonAzureBackupStorage**.
5.    Clique em **Guardar**.

![Modo específico de recursos](./media/backup-azure-diagnostics-events/resource-specific-blade.png)

Uma vez que os dados fluem para o Espaço de Trabalho de LA, tabelas dedicadas para cada um destes eventos são criadas no seu espaço de trabalho. Pode consultar qualquer uma destas tabelas diretamente e também realizar uniões ou sindicatos entre estas mesas, se necessário.

> [!IMPORTANT]
> Os seis eventos acima referidos, nomeadamente, CoreAzureBackup, AddonAzureBackupAlerts, AddonAzureBackupProtectedInstance, AddonAzureBackupJobs, AddonAzureBackupPolicy e AddonAzureBackupStorage, são suportados **apenas** no Modo Específico de Recursos em [Relatórios](https://docs.microsoft.com/azure/backup/configure-reports)de Backup . **Por favor, note que se tentar enviar dados para estes seis eventos no Modo De Diagnóstico Azure, nenhum dado será visível em Relatórios de Backup.**

## <a name="legacy-event"></a>Evento Legacy

Tradicionalmente, todos os dados de diagnóstico relacionados com a cópia de segurança de um cofre foram contidos num único evento chamado 'AzureBackupReport'. Os seis eventos acima descritos são, na sua essência, uma decomposição de todos os dados contidos no AzureBackupReport. 

Atualmente, continuamos a apoiar o evento AzureBackupReport para retrocompatibilidade, nos casos em que os utilizadores têm consultas personalizadas existentes neste evento, por exemplo, alertas de registo personalizados, visualizações personalizadas, etc. No entanto, **recomendamos a mudança para os [novos eventos](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events#diagnostics-events-available-for-azure-backup-users) o mais cedo possível,** uma vez que isso torna os dados muito mais fáceis de trabalhar em consultas de registo, proporciona uma melhor descoberta dos schemas e da sua estrutura, melhorando o desempenho tanto em termos de latência de ingestão como de tempos de consulta. 

**O evento legado no modo Azure Diagnostics acabará por ser depreciado e, portanto, escolher os novos eventos poderá ajudá-lo a evitar migrações complexas mais tarde.** A nossa [solução de reporte](https://docs.microsoft.com/azure/backup/configure-reports) que alavanca o Log Analytics também deixará de suportar dados do evento legado.

### <a name="steps-to-move-to-new-diagnostics-settings-to-log-analytics-workspace"></a>Passos para avançar para novas definições de diagnóstico (para log Analytics workspace)

1. Identifique quais cofres estão a enviar dados para o Log Analytics Workspace(s) usando o evento legado, e as subscrições a que pertencem. Executar os espaços de trabalho abaixo para identificar estes cofres e subscrições:

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

2. Utilize a Política [Azure in embutida](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics) da Azure Backup para adicionar uma nova definição de diagnóstico para todos os cofres num âmbito especificado. Esta política adiciona uma nova definição de diagnóstico aos cofres que ou não têm uma definição de diagnóstico (ou) têm apenas uma definição de diagnóstico legado. Esta política pode ser atribuída a toda uma subscrição ou grupo de recursos de cada vez. Note que necessitará de acesso 'Proprietário' a cada subscrição para a qual a apólice é atribuída.

Pode optar por ter configurações de diagnóstico separadas para o AzureBackupReport e os seis novos eventos, até que tenha migrado todas as suas consultas personalizadas para utilizar dados das novas tabelas. A imagem abaixo mostra um exemplo de um cofre com duas configurações de diagnóstico. A primeira definição, denominada **Setting1** envia dados do evento AzureBackupReport para um espaço de trabalho de LA no modo AzureDiagnostics. A segunda definição, denominada **Setting2** envia dados dos seis novos eventos de backup Azure para um espaço de trabalho de LA no modo Específico de Recursos.

![Duas Definições](./media/backup-azure-diagnostics-events/two-settings-example.png)

> [!IMPORTANT]
> O evento AzureBackupReport é suportado **apenas** no Modo de Diagnóstico Azure. **Por favor, note que se tentar enviar dados para este evento no Modo Específico de Recursos, nenhum dado fluirá para o espaço de trabalho de LA.**

> [!NOTE]
> O alternância para Diagnósticos Azure / Específico de Recursos só aparece se o utilizador verificar **Enviar para Log Analytics**. Para enviar dados para uma Conta de Armazenamento ou um Hub de Eventos, um utilizador pode simplesmente selecionar o destino necessário e verificar qualquer um dos eventos pretendidos, sem quaisquer inputs adicionais. Mais uma vez, recomenda-se não escolher o evento legado AzureBackupReport, avançando.

## <a name="users-sending-azure-site-recovery-events-to-log-analytics-la"></a>Utilizadores que enviam Eventos de Recuperação de Sites Azure para Registar Analytics (LA)

Os eventos de backup de backup e de recuperação do site Azure são enviados a partir do mesmo Cofre de Serviços de Recuperação. Como a Recuperação do Site Azure não está atualmente a bordo em tabelas específicas de recursos, os utilizadores que pretendam enviar eventos de recuperação do site Azure para LA são direcionados para usar **apenas** o Modo de Diagnóstico Azure (ver imagem abaixo). A escolha do modo específico de recursos para os Eventos de Recuperação do **Site Azure impedirá que os dados necessários sejam enviados para o espaço**de trabalho de LA .

![Eventos de Recuperação do Site](./media/backup-azure-diagnostics-events/site-recovery-settings.png)

Para resumir as nuances acima:

* Se já tiver diagnósticos de LA configurados com o Azure Diagnostics e tiver consultas personalizadas escritas em cima dela, mantenha essa definição **intacta**, até migrar as suas consultas para utilizar dados dos novos eventos.
* Se também quiser embarcar em novas tabelas (conforme recomendado), crie uma **nova** definição de diagnóstico, escolha O Específico do **Recurso** e selecione os seis novos eventos acima especificados.
* Se está atualmente a enviar Eventos de Recuperação de Sítios Azure para LA, **não** escolha o modo Específico de Recursos para estes eventos, caso contrário, os dados para estes eventos não irão fluir para o seu espaço de trabalho de LA. Em vez disso, crie uma **definição de diagnóstico adicional**, selecione **Diagnósticos Azure,** e escolha os eventos de recuperação do site Azure relevantes.

A imagem abaixo mostra um exemplo de um utilizador com três configurações de diagnóstico para um cofre. A primeira definição, denominada **Setting1** envia dados do evento AzureBackupReport para um espaço de trabalho de LA no modo AzureDiagnostics. A segunda definição, denominada **Setting2** envia dados dos seis novos eventos de backup Azure para um espaço de trabalho de LA no modo Específico de Recursos. A terceira definição, denominada **Setting3,** envia dados dos eventos de recuperação do site Azure para um espaço de trabalho de LA no Modo de Diagnóstico Azure.

![Três Configurações](./media/backup-azure-diagnostics-events/three-settings-example.png)

## <a name="next-steps"></a>Passos seguintes

[Conheça o Modelo de Dados de Análise de Log para os Eventos de Diagnóstico](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)
