---
title: Registos de Gestão de Atualização De Consulta Azure
description: Este artigo descreve como consultar os registos para Gestão de Atualizações no seu espaço de trabalho Log Analytics.
services: automation
ms.subservice: update-management
ms.date: 04/06/2020
ms.topic: conceptual
ms.openlocfilehash: 09eacb42eff6ecf3a3fca2d7fb401f52195f5f2d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617429"
---
# <a name="query-update-records-for-update-management-in-azure-monitor-logs"></a>Registos de atualizações de consulta para Gestão de Atualizações em Registos de Monitores Azure

Além dos detalhes fornecidos na solução De Gestão de Atualizações, pode pesquisar contra os registos armazenados no seu espaço de trabalho Log Analytics. A partir da página de solução, no painel esquerdo, selecione **Logs**. A página de Pesquisa de Registos abre.

Também pode aprender a personalizar as consultas ou usá-las de diferentes clientes. Consulte [log Analytics pesquisar documentação DaPI](https://dev.loganalytics.io/).

## <a name="update-records"></a>Registos de atualizações

Update Management recolhe registos de VMs Windows e Linux e os tipos de dados que aparecem nos resultados de pesquisa de registo. As seguintes secções descrevem os registos.

### <a name="required-updates"></a>Atualizações necessárias

É criado um `RequiredUpdate` registo com um tipo de maquete que representa as atualizações exigidas por uma máquina. Estes registos têm as propriedades na tabela seguinte:

| Propriedade | Descrição | 
|----------|-------------|
| Computador | Nome de domínio totalmente qualificado da máquina de reportagem. |
| KBID | Id do artigo base de conhecimento para a atualização do Windows. |
| ManagementGroupName | Nome do grupo de gestão do Gestor de Operações ou do espaço de trabalho log Analytics. | 
| Produto | Os produtos para os quais a atualização é aplicável. | 
| PublishDate | A data em que a atualização está pronta para ser descarregada e instalada a partir do Windows Update. |
| Server | | 
| SourceHealthServiceId | Identificador único que representa o ID do agente do Windows de Log Analytics. |
| SourceSystem | *OperationsManager* | 
| TenantId | Identificador único que representa as suas organizações exemplo de Diretório Ativo Azure. | 
| TimeGenerated | Data e hora em que o disco foi criado. | 
| Tipo | *Atualizar* | 
| Classificação de Atualização | Indica o tipo de atualizações que podem ser aplicadas. Para Windows:<br> *Atualizações críticas*<br> *Atualizações de segurança*<br> *Update rollups*<br> *Pacotes de funcionalidades*<br> *Service packs*<br> *Atualizações de definições*<br> *Ferramentas*<br> *Atualizações*. Para Linux:<br> *Atualizações críticas e de segurança*<br> *Outros* |
| AtualizaçõesGravidade | Classificação de gravidade para a vulnerabilidade. Os valores são:<br> *Crítica*<br> *Importante*<br> *Moderado*<br> *Baixa* |
| Título de atualização | O título da atualização.|

### <a name="update"></a>Atualizar

É criado um `Update` registo com um tipo de maquete que representa as atualizações disponíveis e o seu estado de instalação para uma máquina. Estes registos têm as propriedades na tabela seguinte:

| Propriedade | Descrição | 
|----------|-------------|
| ApprovalSource | Aplica-se apenas ao sistema operativo Windows. Fonte de aprovação para o registo. O valor é Microsoft Update. |
| Aprovado | É verdade que o registo é aprovado, ou Falso de outra forma. |
| Classificação | Classificação de aprovação. O valor é Updates. |
| Computador | Nome de domínio totalmente qualificado da máquina de reportagem. |
| ComputadorAmbiente | O ambiente. Os valores possíveis são Azure ou Non-Azure. |
| MSRCBulletinID | Número de identificação do boletim de segurança. | 
| MSRCSeverity | Classificação de gravidade para a vulnerabilidade. Os valores são:<br> Crítica<br> Importante<br> Moderado<br> Baixa |  
| KBID | Id do artigo base de conhecimento para a atualização do Windows. |
| ManagementGroupName | Nome do grupo de gestão do Gestor de Operações ou do espaço de trabalho log Analytics. |
| UpdateID | Identificador único da atualização de software. |
| RevisionNumber | O número de revisão de uma revisão específica de uma atualização. |
| Opcional | É verdade que o registo é opcional, ou Falso de outra forma. | 
| RebootBehavior | O comportamento de reinicialização após a instalação/desinstalação de uma atualização. |
| _ResourceId | Identificador único para o recurso associado ao registo. |
| Tipo | Tipo de disco. O valor é Update. |
| VMUUID | Identificador único para a máquina virtual. |
| MG | Identificador único para o grupo de gestão ou espaço de trabalho Log Analytics. | 
| TenantId | Identificador único que representa o exemplo da sua organização de Diretório Ativo Azure. | 
| SourceSystem | O sistema de origem para o registo. O valor `OperationsManager`é. | 
| TimeGenerated | Data e hora da criação de discos. | 
| SourceComputerId | Identificador único que representa o computador de origem. | 
| Título | O título da atualização. |
| Data publicada (UTC) | A data em que a atualização está pronta para ser descarregada e instalada a partir do Windows Update.  |
| UpdateState | O estado atual da atualização. | 
| Produto | Os produtos para os quais a atualização é aplicável. |
| SubscriptionId | Identificador exclusivo da subscrição do Azure. | 
| ResourceGroup | Nome do grupo de recursos a que pertence o recurso. | 
| ResourceProvider | O fornecedor de recursos. | 
| Recurso | Nome do recurso. | 
| ResourceType | O tipo de recurso. | 

### <a name="update-agent"></a>Agente de Atualização

É criado um `UpdateAgent` registo com um tipo de que fornece detalhes do agente de atualização na máquina. Estes registos têm as propriedades na tabela seguinte:

| Propriedade | Descrição | 
|----------|-------------|
| AgeofOldestMissingRequiredUpdate | | 
| Atualização AutomáticaAtiva | | 
| Computador | Nome de domínio totalmente qualificado da máquina de reportagem. |
| DaysinceLastUpdateBucket | | 
| ManagementGroupName | Nome do grupo de gestão do Gestor de Operações ou do espaço de trabalho log Analytics. |
| OSVersion | A versão do sistema operativo. |
| Server | |
| SourceHealthServiceId | Identificador único que representa o ID do agente do Windows de Log Analytics. |
| SourceSystem | O sistema de origem para o registo. O valor `OperationsManager`é. | 
| TenantId | Identificador único que representa o exemplo da sua organização de Diretório Ativo Azure. |
| TimeGenerated | Data e hora da criação de discos. |
| Tipo | Tipo de disco. O valor é Update. | 
| WindowsUpdateAgentVersion | Versão do agente Windows Update. |
| WSUSServer | Erros se o agente windows Update tiver algum problema, para ajudar na resolução de problemas. |

### <a name="update-deployment-status"></a>Estado de implementação de atualização 

É criado um `UpdateRunProgress` registo com um tipo de que fornece o estado de implementação da atualização de uma implementação programada por máquina. Estes registos têm as propriedades na tabela seguinte:

| Propriedade | Descrição | 
|----------|-------------|
| Computador | Nome de domínio totalmente qualificado da máquina de reportagem. |
| ComputadorAmbiente | O ambiente. Os valores são Azure ou Non-Azure. | 
| CorrelationId | Identificador único do trabalho do livro de recortes corre para a atualização. |
| EndTime | O tempo em que o processo de sincronização terminou. | 
| ErrorResult | Código de erro do Windows Update gerado se uma atualização não for instalada. | 
| Estado de Instalação | Os possíveis estados de instalação de uma atualização no computador cliente,<br> `NotStarted`- trabalho ainda não desencadeado.<br> `FailedToStart`- incapaz de iniciar o trabalho na máquina.<br> `Failed`- o trabalho começou, mas falhou com uma exceção.<br> `InProgress`- trabalho em curso.<br> `MaintenanceWindowExceeded`- se a execução restava, mas o intervalo da janela de manutenção chegou.<br> `Succeeded`- o trabalho foi bem sucedido.<br> `InstallFailed`- a atualização não foi instalada com sucesso.<br> `NotIncluded`<br> `Excluded` |
| KBID | Id do artigo base de conhecimento para a atualização do Windows. | 
| ManagementGroupName | Nome do grupo de gestão do Gestor de Operações ou do espaço de trabalho log Analytics. |
| OSType | Tipo de sistema operativo. Os valores são Windows ou Linux. | 
| Produto | Os produtos para os quais a atualização é aplicável. |
| Recurso | Nome do recurso. | 
| ResourceId | Identificador único para o recurso associado ao registo. |
| ResourceProvider | O fornecedor de recursos. | 
| ResourceType | Tipo de recurso. | 
| SourceComputerId | Identificador único que representa o computador de origem. | 
| SourceSystem | Sistema de origem para o registo. O valor `OperationsManager`é. |
| StartTime | Hora da atualização estar programada para ser instalada. |
| SubscriptionId | Identificador exclusivo da subscrição do Azure. | 
| SucceededOnRetry | Valor indicando se a execução da atualização falhou na primeira tentativa e a operação atual é uma tentativa de retry. |
| TimeGenerated | Data e hora da criação de discos. |
| Título | O título da atualização. |
| Tipo | O tipo de atualização. O valor `UpdateRunProgress`é. |
| Atualização | Identificador único da atualização de software. |
| VMUUID | Identificador único para a máquina virtual. |
| ResourceId | Identificador único para o recurso associado ao registo. |

### <a name="update-summary"></a>Resumo da atualização 

É criado um `UpdateSummary` registo com um tipo de máquina que fornece resumo de atualização por máquina. Estes registos têm as propriedades na tabela seguinte:

| Propriedade | Descrição | 
|----------|-------------|
| Computador | Nome de domínio totalmente qualificado da máquina de reportagem. |
| ComputadorAmbiente | O ambiente. Os valores são Azure ou Non-Azure. | 
| CriticalUpdatesMissing | Número de atualizações críticas aplicáveis que faltam. | 
| ManagementGroupName | Nome do grupo de gestão do Gestor de Operações ou do espaço de trabalho log Analytics. |
| NETRuntimeVersion | Versão da .NET Framework instalada no computador Windows. |
| OldestMissingSecurityUpdateBucket | Especificador do balde de segurança mais antigo desaparecido. Os valores são:<br> Recente se o valor for inferior a 30 dias<br> Há 30 dias.<br> Há 60 dias<br> Há 90 dias.<br> Há 120 dias<br> Há 150 dias<br> Há 180 dias<br> Mais velho quando o valor é superior a 180 dias. | 
| OldestMissingSecurityUpdateInDays | Número total de dias para a atualização mais antiga detetada conforme aplicável que não foi instalada. |
| OsVersion | A versão do sistema operativo. |
| OtherUpdatesMissing | Contagem de atualizações detetadas em falta. |
| Recurso | Nome do recurso para o registo. | 
| ResourceGroup | Nome do grupo de recursos que contém o recurso. |
| ResourceId | Identificador único para o recurso associado ao registo. |
| ResourceProvider | O fornecedor de recursos. |
| ResourceType | Tipo de recurso. |
| Reiniciar gastos | É verdade que se estiver pendente um recomeço, ou falso de outra forma. |
| SecurityUpdatesMissing | Contagem de atualizações de segurança em falta que são aplicáveis.| 
| SourceComputerId | Identificador único para a máquina virtual. |
| SourceSystem | Sistema de origem para o registo. O valor `OpsManager`é. | 
| SubscriptionId | Identificador exclusivo da subscrição do Azure. |
| TimeGenerated | Data e hora da criação de discos. |
| TotalUpdatesMissing | Número total de atualizações em falta aplicáveis. | 
| Tipo | Tipo de disco. O valor `UpdateSummary`é. |
| VMUUID | Identificador único para a máquina virtual. |
| WindowsUpdateAgentVersion | Versão do agente Windows Update. |
| WindowsUpdateSetting | Estado do agente Deactualização do Windows. Os valores possíveis são:<br> `Scheduled installation`<br> `Notify before installation`<br> `Error returned from unhealthy WUA agent` | 
| WSUSServer | Erros se o agente windows Update tiver algum problema, para ajudar na resolução de problemas. |
| _ResourceId | Identificador único para o recurso associado ao registo. |

## <a name="sample-queries"></a>Consultas de exemplo

As seguintes secções fornecem consultas de registo de amostras para registos de atualização que são recolhidos para Gestão de Atualizações.

### <a name="confirm-that-non-azure-machines-are-onboarded"></a>Confirme que as máquinas não-Azure estão a bordo

Para confirmar que as máquinas ligadas diretamente estão a comunicar com os registos do Monitor Azure, faça uma das seguintes pesquisas de registo.

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

Num computador Windows, pode rever as seguintes informações para verificar a conectividade do agente com os registos do Monitor Do Azure:

1. No Painel de Controlo, abra **o Agente de Monitorização da Microsoft**. No separador **Azure Log Analytics,** o agente apresenta a seguinte mensagem: O Agente de Monitorização da **Microsoft ligou-se com sucesso ao Log Analytics**.
2. Abra o Registo de Eventos do Windows. Vá ao Gestor de Registos de **Aplicações e Serviços\Operations Manager** e procure o Id de evento 3000 e o Id de evento 5002 a partir do **Conector**de Serviço de origem . Estes eventos indicam que o computador foi registado na área de trabalho do Log Analytics e que está a receber a configuração.

Se o agente não conseguir comunicar com os registos do Monitor Azure e o agente estiver configurado para comunicar com a internet através de um servidor de firewall ou proxy, confirme que a firewall ou servidor proxy está corretamente configurada. Para saber como verificar se a firewall ou o servidor proxy está corretamente configurado, consulte a [configuração da rede para o agente Windows](../azure-monitor/platform/agent-windows.md) ou [configuração de rede para o agente Linux](../log-analytics/log-analytics-agent-linux.md).

> [!NOTE]
> Se os seus sistemas Linux estiverem configurados para comunicar com um proxy ou Log Analytics Gateway e estiver a embarcar nesta solução, atualize as permissões *proxy.conf* para conceder ao grupo omiuser a permissão de leitura no ficheiro utilizando os seguintes comandos:
>
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

Os agentes do Linux recentemente adicionados mostram um estado de **Atualização** após a avaliação ter sido realizada. Este processo pode demorar até seis horas.

Para confirmar que um grupo de gestão de Gestor de Operações está a comunicar com os registos do Monitor De Operações, consulte a integração do Gestor de [Operações validada com os registos do Monitor Azure.](../azure-monitor/platform/om-agents.md#validate-operations-manager-integration-with-azure-monitor)

### <a name="single-azure-vm-assessment-queries-windows"></a>Consultas únicas de avaliação de VM Azure (Windows)

Substitua o valor VMUUID pelo VM GUID da máquina virtual que está a consultar. Pode encontrar o VMUUID que deve ser utilizado executando a seguinte consulta nos registos do Monitor Azure:`Update | where Computer == "<machine name>" | summarize by Computer, VMUUID`

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

### <a name="single-azure-vm-assessment-queries-linux"></a>Consultas de avaliação de VM Azure únicas (Linux)

Para alguns distros Linux, existe uma desfasamento de [endiatividade](https://en.wikipedia.org/wiki/Endianness) com o valor VMUUID que vem do Azure Resource Manager e o que está armazenado em registos do Monitor Azure. A consulta seguinte verifica uma correspondência em qualquer endianness. Substitua os valores VMUUID pelo formato big-endian e pouco endian do GUID para devolver corretamente os resultados. Pode encontrar o VMUUID que deve ser utilizado executando a seguinte consulta nos registos do Monitor Azure:`Update | where Computer == "<machine name>"
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

* Utilize pesquisas de registo nos [registos do Monitor Azure](../log-analytics/log-analytics-log-searches.md) para visualizar dados de atualização detalhados.
* [Crie alertas](automation-tutorial-update-management.md#configure-alerts) para o estado de implementação da atualização.
