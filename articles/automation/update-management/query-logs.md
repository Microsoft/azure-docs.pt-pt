---
title: Registos de gestão de atualização de automação de consulta Azure
description: Este artigo diz como consultar os registos de Gestão de Atualização no seu espaço de trabalho Log Analytics.
services: automation
ms.subservice: update-management
ms.date: 09/24/2020
ms.topic: conceptual
ms.openlocfilehash: 5eb0c7d72896cc9a27907743b1b9c3d5a40614dd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100592866"
---
# <a name="query-update-management-logs"></a>Consultar os registos da Gestão de Atualizações

Além dos detalhes fornecidos durante a implementação de Gestão de Atualização, pode pesquisar os registos armazenados no seu espaço de trabalho Log Analytics. Para pesquisar os registos a partir da sua conta Dem automação, selecione **a gestão** de Atualização e abra o espaço de trabalho Log Analytics associado à sua implementação.

Também pode personalizar as consultas de registo ou usá-las a partir de diferentes clientes. Consulte [a documentação da API de pesquisa de Log Analytics](https://dev.loganalytics.io/).

## <a name="query-update-records"></a>Registos de atualização de consultas

A Atualização Management recolhe registos de VMs Windows e Linux e os tipos de dados que aparecem nos resultados da pesquisa de registo. As seguintes secções descrevem os registos.

### <a name="query-required-updates"></a>Consulta necessária atualizações

É criado um registo com um tipo de `RequiredUpdate` espécie que representa as atualizações exigidas por uma máquina. Estes registos têm as propriedades na tabela seguinte:

| Propriedade | Descrição |
|----------|-------------|
| Computador | Nome de domínio totalmente qualificado da máquina de reporte. |
| KBID | ID de base de conhecimento para a atualização do Windows. |
| ManagementGroupName | Nome do grupo de gestão de gestores de operações ou espaço de trabalho Log Analytics. |
| Produto | Os produtos para os quais a atualização é aplicável. |
| PublishDate | A data em que a atualização está pronta para ser descarregada e instalada a partir do Windows Update. |
| Servidor | | 
| SourceHealthServiceId | Identificador único que representa o ID do agente do Log Analytics Windows. |
| SourceSystem | *OperationsManager* |
| TenantId | Identificador único que representa a sua instância de azure Ative Directory. |
| TimeGenerated | Data e hora que o disco foi criado. |
| Tipo | *Atualizar* |
| ActualizaçãoClassificação | Indica o tipo de atualizações que podem ser aplicadas. Para Windows:<br> *Atualizações críticas*<br> *Atualizações de segurança*<br> *Update rollups*<br> *Pacotes de funcionalidades*<br> *Service packs*<br> *Atualizações de definições*<br> *Ferramentas*<br> *Atualizações*. Para Linux:<br> *Atualizações críticas e de segurança*<br> *Outro* |
| AtualizaçõesEverity | Classificação de severidade para a vulnerabilidade. Os valores são:<br> *Crítico*<br> *Importante*<br> *Moderado*<br> *Baixo* |
| UpdateTle | O título da atualização.|

### <a name="query-update-record"></a>Registo de atualização de consultas

É criado um registo com um tipo de espécie `Update` que representa atualizações disponíveis e o seu estado de instalação para uma máquina. Estes registos têm as propriedades na tabela seguinte:

| Propriedade | Descrição |
|----------|-------------|
| Fonte de aprovação | Aplica-se apenas ao sistema operativo Windows. Fonte de aprovação para o registo. O valor é Microsoft Update. |
| Aprovado | É verdade se o registo for aprovado, ou falso de outra forma. |
| Classificação | Classificação de aprovação. O valor é Atualizações. |
| Computador | Nome de domínio totalmente qualificado da máquina de reporte. |
| Ambiente informático | O ambiente. Os valores possíveis são Azure ou Non-Azure. |
| MSRCBulletinID | Número de identificação do boletim de segurança. |
| MSRCSeverity | Classificação de severidade para a vulnerabilidade. Os valores são:<br> Crítico<br> Importante<br> Moderado<br> Baixo |  
| KBID | ID de base de conhecimento para a atualização do Windows. |
| ManagementGroupName | Nome do grupo de gestão de Gestores de Operações ou do espaço de trabalho Log Analytics. |
| UpdateID | Identificador único da atualização de software. |
| RevisionNumber | O número de revisão de uma revisão específica de uma atualização. |
| Opcional | É verdade se o registo é opcional, ou falso de outra forma. |
| RebootBehavior | O comportamento de reinicialização após a instalação/desinstalação de uma atualização. |
| _ResourceId | Identificador único para o recurso associado ao registo. |
| Tipo | Tipo de gravação. O valor é Update. |
| VMUUID | Identificador único para a máquina virtual. |
| MG | Identificador exclusivo para o grupo de gestão ou espaço de trabalho Log Analytics. |
| TenantId | Identificador único que representa o exemplo da sua organização de Azure Ative Directory. |
| SourceSystem | O sistema de origem para o registo. O valor é `OperationsManager`. |
| TimeGenerated | Data e hora da criação de discos. |
| SourceComputerId | Identificador único representando o computador de origem. |
| Título | O título da atualização. |
| PublicadoDate (UTC) | A data em que a atualização está pronta para ser descarregada e instalada a partir do Windows Update.  |
| UpdateState | O estado atual da atualização. |
| Produto | Os produtos para os quais a atualização é aplicável. |
| SubscriptionId | Identificador exclusivo da subscrição do Azure. |
| ResourceGroup | Nome do grupo de recursos a que pertence o recurso. |
| ResourceProvider | O fornecedor de recursos. |
| Recurso | Nome do recurso. |
| ResourceType | O tipo de recurso. |

### <a name="query-update-agent-record"></a>Registo do Agente de Atualização de Consulta

É criado um registo com um tipo de tipo de `UpdateAgent` que fornece detalhes do agente de atualização na máquina. Estes registos têm as propriedades na tabela seguinte:

| Propriedade | Descrição |
|----------|-------------|
| AgeofOldestMissingRequiredUpdate | |
| AutomaticUpdateEnabled | |
| Computador | Nome de domínio totalmente qualificado da máquina de reporte. |
| DaySinceLastUpdateBucket | |
| ManagementGroupName | Nome do grupo de gestão de gestores de operações ou espaço de trabalho Log Analytics. |
| OSVersion | A versão do sistema operativo. |
| Servidor | |
| SourceHealthServiceId | Identificador único que representa o ID do agente do Log Analytics Windows. |
| SourceSystem | O sistema de origem para o registo. O valor é `OperationsManager`. |
| TenantId | Identificador único que representa o exemplo da sua organização de Azure Ative Directory. |
| TimeGenerated | Data e hora da criação de discos. |
| Tipo | Tipo de gravação. O valor é Update. |
| WindowsUpdateAgentVersion | Versão do agente de atualização do Windows. |
| WSUSServer | Erros se o agente do Windows Update tiver um problema, para ajudar na resolução de problemas. |

### <a name="query-update-deployment-status-record"></a>Registo do estado de implementação da atualização de consulta

É criado um registo com um tipo de tipo de `UpdateRunProgress` atualização que fornece o estado de implementação de uma implementação programada por máquina. Estes registos têm as propriedades na tabela seguinte:

| Propriedade | Descrição |
|----------|-------------|
| Computador | Nome de domínio totalmente qualificado da máquina de reporte. |
| Ambiente informático | O ambiente. Os valores são Azure ou Non-Azure. |
| CorrelationId | Identificador único do runbook job runbook run run run for the update. |
| EndTime | O tempo em que o processo de sincronização terminou. *Esta propriedade não é atualmente usada. Ver TempoGerertado.* |
| Resultado do erro | Código de erro do Windows Update gerado se uma atualização não for instalada. |
| InstalaçãoStatus | Os possíveis estados de instalação de uma atualização no computador cliente,<br> `NotStarted` - trabalho ainda não desencadeado.<br> `FailedToStart` - incapaz de iniciar o trabalho na máquina.<br> `Failed` - o trabalho começou, mas falhou com uma exceção.<br> `InProgress` - trabalho em curso.<br> `MaintenanceWindowExceeded` - se a execução se restava, mas o intervalo da janela de manutenção chegou.<br> `Succeeded` - trabalho bem sucedido.<br> `InstallFailed` - a atualização não foi instalada com sucesso.<br> `NotIncluded`<br> `Excluded` |
| KBID | ID de base de conhecimento para a atualização do Windows. |
| ManagementGroupName | Nome do grupo de gestão de gestores de operações ou espaço de trabalho Log Analytics. |
| OSType | Tipo de sistema operativo. Os valores são Windows ou Linux. |
| Produto | Os produtos para os quais a atualização é aplicável. |
| Recurso | Nome do recurso. |
| ResourceId | Identificador único para o recurso associado ao registo. |
| ResourceProvider | O fornecedor de recursos. |
| ResourceType | Tipo de recurso. |
| SourceComputerId | Identificador único representando o computador de origem. |
| SourceSystem | Sistema de origem para o registo. O valor é `OperationsManager`. |
| StartTime | Hora de instalar a atualização. *Esta propriedade não é atualmente usada. Ver TempoGerertado.* |
| SubscriptionId | Identificador exclusivo da subscrição do Azure. |
| Sucedeu ARetry | Valor indicando se a execução da atualização falhou na primeira tentativa e a operação atual é uma tentativa de repetição. |
| TimeGenerated | Data e hora da criação de discos. |
| Título | O título da atualização. |
| Tipo | O tipo de atualização. O valor é `UpdateRunProgress`. |
| UpdateId | Identificador único da atualização de software. |
| VMUUID | Identificador único para a máquina virtual. |
| ResourceId | Identificador único para o recurso associado ao registo. |

### <a name="query-update-summary-record"></a>Registo de resumo de atualização de consulta

Um registo com um tipo de `UpdateSummary` é criado que fornece resumo de atualização por máquina. Estes registos têm as propriedades na tabela seguinte:

| Propriedade | Descrição |
|----------|-------------|
| Computador | Nome de domínio totalmente qualificado da máquina de reporte. |
| Ambiente informático | O ambiente. Os valores são Azure ou Non-Azure. |
| CriticalUpdatesMissing | Número de atualizações críticas aplicáveis que faltam. |
| ManagementGroupName | Nome do grupo de gestão de gestores de operações ou espaço de trabalho Log Analytics. |
| NETRuntimeVersion | Versão do Quadro .NET instalado no computador Windows. |
| OldestMissingSecurityUpdateBucket | Especificador do mais antigo balde de segurança desaparecido. Os valores são:<br> Se o valor for inferior a 30 dias<br> Há 30 dias<br> Há 60 dias<br> Há 90 dias<br> Há 120 dias<br> Há 150 dias<br> Há 180 dias<br> Mais velho quando o valor é superior a 180 dias. |
| OldestMissingSecurityUpdateInDays | Número total de dias para a mais antiga atualização detetada conforme aplicável que não foi instalada. |
| OsVersion | A versão do sistema operativo. |
| OtherUpdatesMissing | Contagem de atualizações detetadas em falta. |
| Recurso | O nome do recurso para o registo. |
| ResourceGroup | Nome do grupo de recursos que contém o recurso. |
| ResourceId | Identificador único para o recurso associado ao registo. |
| ResourceProvider | O fornecedor de recursos. |
| ResourceType | Tipo de recurso. |
| Reiniciar Gastos | Verdade se um recomeço estiver pendente, ou falso de outra forma. |
| SecurityUpdatesMissing | Contagem de atualizações de segurança em falta que são aplicáveis.|
| SourceComputerId | Identificador único para a máquina virtual. |
| SourceSystem | Sistema de origem para o registo. O valor é `OpsManager`. |
| SubscriptionId | Identificador exclusivo da subscrição do Azure. |
| TimeGenerated | Data e hora da criação de discos. |
| TotalUpdatesMissing | Número total de atualizações em falta que são aplicáveis. |
| Tipo | Tipo de gravação. O valor é `UpdateSummary`. |
| VMUUID | Identificador único para a máquina virtual. |
| WindowsUpdateAgentVersion | Versão do agente de atualização do Windows. |
| WindowsUpdateSetting | Estado do agente windows update. Os valores possíveis são:<br> `Scheduled installation`<br> `Notify before installation`<br> `Error returned from unhealthy WUA agent` |
| WSUSServer | Erros se o agente do Windows Update tiver um problema, para ajudar na resolução de problemas. |
| _ResourceId | Identificador único para o recurso associado ao registo. |

## <a name="sample-queries"></a>Consultas de exemplo

As secções seguintes fornecem consultas de registo de amostras para registos de atualização que são recolhidos para a Gestão de Atualização.

### <a name="confirm-that-non-azure-machines-are-enabled-for-update-management"></a>Confirme que as máquinas não-Azure estão ativadas para a Gestão de Atualização

Para confirmar que as máquinas ligadas diretamente estão a comunicar com os registos do Azure Monitor, faça uma das seguintes pesquisas de registo.

#### <a name="linux"></a>Linux

```loganalytics
Heartbeat
| where OSType == "Linux" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

#### <a name="windows"></a>Windows

```loganalytics
Heartbeat
| where OSType == "Windows" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

Num computador Windows, pode rever as seguintes informações para verificar a conectividade do agente com os registos do Azure Monitor:

1. No Painel de Controlo, abra o **Agente de Monitorização da Microsoft.** No **separador Azure Log Analytics,** o agente apresenta a seguinte mensagem: **O Agente de Monitorização da Microsoft ligou-se com sucesso ao Log Analytics**.

1. Abra o Registo de Eventos do Windows. Aceda aos **Registos de Aplicações e Serviços\Gestor de Operações** e procure o ID do evento 3000 e o ID do evento 5002 a partir do **Conector** de Serviço de origem . Estes eventos indicam que o computador foi registado na área de trabalho do Log Analytics e que está a receber a configuração.

Se o agente não conseguir comunicar com os registos do Azure Monitor e o agente estiver configurado para comunicar com a internet através de uma firewall ou servidor de procuração, confirme que a firewall ou o servidor proxy estão corretamente configurados. Para saber como verificar se a firewall ou o servidor proxy estão corretamente configurados, consulte [a configuração da Rede para o agente Windows](../../azure-monitor/agents/agent-windows.md) ou para a [configuração da rede para o agente Linux](../../azure-monitor/vm/quick-collect-linux-computer.md).

> [!NOTE]
> Se os seus sistemas Linux estiverem configurados para comunicar com um proxy ou Log Analytics Gateway e estiver a permitir a Gestão de Atualização, atualize as `proxy.conf` permissões para conceder ao grupo omiuser a permissão de leitura no ficheiro utilizando os seguintes comandos:
>
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

Os novos agentes Linux adicionados mostram um estado de **atualização** após a avaliação. Este processo pode demorar até seis horas.

Para confirmar que um grupo de gestão de Gestores de Operações está a comunicar com os registos do Azure Monitor, consulte [a integração do Gestor de Operações Validado com os registos do Azure Monitor](../../azure-monitor/agents/om-agents.md#validate-operations-manager-integration-with-azure-monitor).

### <a name="single-azure-vm-assessment-queries-windows"></a>Consultas únicas de avaliação de VM Azure (Windows)

Substitua o valor VMUUID pelo VM GUID da máquina virtual que está a consultar. Pode encontrar o VMUUID que deve ser utilizado executando a seguinte consulta nos registos do Monitor Azure: `Update | where Computer == "<machine name>" | summarize by Computer, VMUUID`

#### <a name="missing-updates-summary"></a>Resumo das atualizações em falta

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"b08d5afa-1471-4b52-bd95-a44fea6e4ca8"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="missing-updates-list"></a>Lista de atualizações em falta

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"8bf1ccc6-b6d3-4a0b-a643-23f346dfdf82"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

### <a name="single-azure-vm-assessment-queries-linux"></a>Consultas únicas de avaliação de Azure VM (Linux)

Para alguns destros do Linux, existe um desfasamento [de endianness](https://en.wikipedia.org/wiki/Endianness) com o valor VMUUID que vem do Azure Resource Manager e o que é armazenado nos registos do Azure Monitor. A seguinte consulta verifica se há uma correspondência em qualquer endianness. Substitua os valores VMUUID pelo formato de grande ponta e pouco endian do GUID para devolver corretamente os resultados. Pode encontrar o VMUUID que deve ser utilizado executando a seguinte consulta nos registos do Monitor Azure: `Update | where Computer == "<machine name>"
| summarize by Computer, VMUUID`

#### <a name="missing-updates-summary"></a>Resumo das atualizações em falta

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="missing-updates-list"></a>Lista de atualizações em falta

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl

```

### <a name="multi-vm-assessment-queries"></a>Consultas de avaliação multi-VM

#### <a name="computers-summary"></a>Resumo dos computadores

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Approved, Optional, Classification) by SourceComputerId, UpdateID
    | distinct SourceComputerId, Classification, UpdateState, Approved, Optional
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed" and (Optional==false or Classification has "Critical" or Classification has "Security") and Approved!=false, iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity
| union (Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by SourceComputerId, Product, ProductArch
    | distinct SourceComputerId, Classification, UpdateState
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed", iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity)
| summarize assessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity>-1), notAssessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==-1), computersNeedCriticalUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==4), computersNeedSecurityUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==2), computersNeedOtherUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==1), upToDateComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==0)
| summarize assessedComputersCount=sum(assessedComputersCount), computersNeedCriticalUpdatesCount=sum(computersNeedCriticalUpdatesCount),  computersNeedSecurityUpdatesCount=sum(computersNeedSecurityUpdatesCount), computersNeedOtherUpdatesCount=sum(computersNeedOtherUpdatesCount), upToDateComputersCount=sum(upToDateComputersCount), notAssessedComputersCount=sum(notAssessedComputersCount)
| extend allComputersCount=assessedComputersCount+notAssessedComputersCount
```

#### <a name="missing-updates-summary"></a>Resumo das atualizações em falta

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| union (Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification )
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="computers-list"></a>Lista de computadores

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Product, Computer, ComputerEnvironment) by SourceComputerId, Product, ProductArch
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed"), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed"), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed"), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2)
| union(Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, Optional, Approved, Computer, ComputerEnvironment) by Computer, SourceComputerId, UpdateID
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed" and Approved!=false), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed" and Approved!=false), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed" and Optional==false and Approved!=false), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2) )
| order by ComplianceOrder asc, missingCriticalUpdatesCount desc, missingSecurityUpdatesCount desc, missingOtherUpdatesCount desc, displayName asc
| project-away ComplianceOrder
```

#### <a name="missing-updates-list"></a>Lista de atualizações em falta

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| union(Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2)
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre os registos do [Monitor Azure, consulte os registos do Monitor Azure](../../azure-monitor/logs/log-query-overview.md).
* Para obter ajuda com alertas, consulte [alertas de configuração.](configure-alerts.md)
