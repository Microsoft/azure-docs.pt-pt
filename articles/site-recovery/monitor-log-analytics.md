---
title: Monitorizar a recuperação do site azure com registos de monitores Azure
description: Saiba como monitorizar a recuperação do site azure com registos do Monitor Azure (Log Analytics)
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: raynew
ms.openlocfilehash: 0b3f5963572368cb9c884984418140b4bbc0dea3
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82131197"
---
# <a name="monitor-site-recovery-with-azure-monitor-logs"></a>Monitorizar o Site Recovery com os Registos do Azure Monitor

Este artigo descreve como monitorizar máquinas replicadas pela [Recuperação](site-recovery-overview.md)do Site Azure, utilizando [registos de monitores Azure](../azure-monitor/platform/data-platform-logs.md)e [Log Analytics.](../azure-monitor/log-query/log-query-overview.md)

O Azure Monitor Logs fornece uma plataforma de dados de registo que recolhe registos de atividade e recursos, juntamente com outros dados de monitorização. Dentro dos Registos do Monitor Azure, utiliza o Log Analytics para escrever e testar consultas de registo e analisar interativamente os dados de registo. Pode visualizar e consultar os resultados do registo e configurar alertas para tomar ações com base em dados monitorizados.

Para a recuperação do site, pode aceder a registos do Monitor Azure para o ajudar a fazer o seguinte:

- Monitorizar a **saúde e o estado**da recuperação do site. Por exemplo, pode monitorizar a saúde da replicação, o estado de failover do teste, eventos de recuperação do site, objetivos de ponto de recuperação (RPOs) para máquinas protegidas e taxas de mudança de disco/dados.
- **Configurar alertas para a recuperação**do local. Por exemplo, pode configurar alertas para a saúde da máquina, o estado de falha de teste ou o estado do trabalho de recuperação do site.

A utilização de registos de monitores Azure com recuperação do site é suportada para a replicação **de Azure a Azure,** e **VMware VM/servidor físico para** a replicação do Azure.

> [!NOTE]
> Para obter os registos de dados e os registos de taxa de carregamento para VMware e máquinas físicas, é necessário instalar um agente de monitorização da Microsoft no Servidor de Processos. Este agente envia os registos das máquinas de replicação para o espaço de trabalho. Esta capacidade está disponível apenas para a versão 9.30 do agente de mobilidade.

## <a name="before-you-start"></a>Antes de começar

Eis o que necessita:

- Pelo menos uma máquina protegida num cofre dos Serviços de Recuperação.
- Um espaço de trabalho de Log Analytics para armazenar registos de recuperação do site. [Aprenda a](../azure-monitor/learn/quick-create-workspace.md) criar um espaço de trabalho.
- Uma compreensão básica de como escrever, executar e analisar consultas de log no Log Analytics. [Saiba mais](../azure-monitor/log-query/get-started-portal.md).

Recomendamos que reveja [as questões comuns](monitoring-common-questions.md) de monitorização antes de começar.

## <a name="configure-site-recovery-to-send-logs"></a>Configurar a recuperação do site para enviar registos

1. No cofre, clique em **definições** > de**diagnóstico Adicione a definição de diagnóstico**.

    ![Selecione a exploração madeireira de recursos](./media/monitoring-log-analytics/add-diagnostic.png)

2. Nas **definições de Diagnóstico,** especifique um nome e verifique a caixa **Enviar para Registar Análises**.
3. Selecione a subscrição de Registos do Monitor Azure e o espaço de trabalho do Log Analytics.
4. Selecione **Diagnósticos Azure** no alternância.
5. Na lista de registos, selecione todos os registos com o prefixo **AzureSiteRecovery**. Em seguida, clique em **OK**.

    ![Selecionar área de trabalho](./media/monitoring-log-analytics/select-workspace.png)

Os registos de recuperação do site começam a alimentar-se numa tabela **(AzureDiagnostics**) no espaço de trabalho selecionado.

## <a name="configure-microsoft-monitoring-agent-on-the-process-server-to-send-churn-and-upload-rate-logs"></a>Configure o agente de monitorização da Microsoft no Servidor de Processos para enviar registos de taxa de carregamento e churn

Pode capturar as informações da taxa de recolha de dados e a informação da taxa de carregamento de dados de origem para os seus VMware/máquinas físicas no local. Para o ativar, é necessário instalar um agente de monitorização da Microsoft no Servidor de Processos.

1. Vá ao espaço de trabalho do Log Analytics e clique em **Definições Avançadas**.
2. Clique na página **De Fontes Conectadas** e selecione ainda mais **os Servidores do Windows**.
3. Descarregue o Agente Windows (64 bits) no Servidor de Processos. 
4. [Obtenha o ID e a chave do espaço de trabalho](../azure-monitor/platform/agent-windows.md#obtain-workspace-id-and-key)
5. [Configure o agente para utilizar TLS 1.2](../azure-monitor/platform/agent-windows.md#configure-agent-to-use-tls-12)
6. [Complete a instalação do agente](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard) fornecendo o ID e a chave obtidos no espaço de trabalho.
7. Assim que a instalação estiver concluída, aceda ao espaço de trabalho do Log Analytics e clique em **Definições Avançadas**. Vá à página **dados** e clique mais nos **Balcões**de Desempenho do Windows . 
8. Clique em **'+'** para adicionar os dois contadores seguintes com intervalo de amostra de 300 segundos:

        ASRAnalytics(*)\SourceVmChurnRate 
        ASRAnalytics(*)\SourceVmThrpRate 

Os dados da taxa de carregamento começarão a alimentar-se no espaço de trabalho.


## <a name="query-the-logs---examples"></a>Consulta dos registos - exemplos

Obtém dados de registos utilizando consultas de registo escritas com a linguagem de [consulta Kusto](../azure-monitor/log-query/get-started-queries.md). Esta secção fornece alguns exemplos de consultas comuns que pode utilizar para monitorização da recuperação do site.

> [!NOTE]
> Alguns dos exemplos usam **replicationProviderName_s** definido para **A2A**. Isto recupera VMs Azure que são replicados para uma região secundária de Azure usando a Recuperação do Local. Nestes exemplos, pode substituir **a A2A** por **InMageAzureV2,** se quiser recuperar VMs no local ou servidores físicos que são replicados para Azure utilizando a Recuperação do Site.


### <a name="query-replication-health"></a>Saúde de replicação de consulta

Esta consulta traça um gráfico de tartes para a saúde de replicação atual de todos os VMs Azure protegidos, divididos em três estados: Normal, Aviso ou Crítico.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , replicationHealth_s  
| summarize count() by replicationHealth_s  
| render piechart   
```
### <a name="query-mobility-service-version"></a>Versão do serviço de Mobilidade Consulta

Esta consulta traça um gráfico de tartes para VMs Azure replicado com Recuperação do Site, dividido pela versão do agente de Mobilidade que estão a executar.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , agentVersion_s  
| summarize count() by agentVersion_s  
| render piechart 
```

### <a name="query-rpo-time"></a>Hora do RPO de consulta

Esta consulta traça um gráfico de barras de VMs Azure replicado com Recuperação do Local, dividido pelo objetivo do ponto de recuperação (RPO): Menos de 15 minutos, entre 15-30 minutos, mais de 30 minutos.

```
AzureDiagnostics 
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| extend RPO = case(rpoInSeconds_d <= 900, "<15Min",   
rpoInSeconds_d <= 1800, "15-30Min", ">30Min")  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , RPO  
| summarize Count = count() by RPO  
| render barchart 
```

![RPO de consulta](./media/monitoring-log-analytics/example1.png)

### <a name="query-site-recovery-jobs"></a>Trabalhos de recuperação de locais de consulta

Esta consulta recupera todos os trabalhos de recuperação do site (para todos os cenários de recuperação de desastres), desencadeados nas últimas 72 horas, e o seu estado de conclusão.

```
AzureDiagnostics  
| where Category == "AzureSiteRecoveryJobs"  
| where TimeGenerated >= ago(72h)   
| project JobName = OperationName , VaultName = Resource , TargetName = affectedResourceName_s, State = ResultType  
```

### <a name="query-site-recovery-events"></a>Eventos de recuperação do site de consulta

Esta consulta recupera todos os eventos de Recuperação do Site (para todos os cenários de recuperação de desastres) levantados nas últimas 72 horas, juntamente com a sua gravidade. 

```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryEvents"   
| where TimeGenerated >= ago(72h)   
| project AffectedObject=affectedResourceName_s , VaultName = Resource, Description_s = healthErrors_s , Severity = Level  
```

### <a name="query-test-failover-state-pie-chart"></a>Teste de consulta failover estado (gráfico de torta)

Esta consulta traça um gráfico de tartes para o estado de failover de teste dos VMs Azure replicados com a Recuperação do Site.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| where isnotempty(failoverHealth_s) and isnotnull(failoverHealth_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , Resource, failoverHealth_s  
| summarize count() by failoverHealth_s  
| render piechart 
```

### <a name="query-test-failover-state-table"></a>Teste de consulta failover estado (tabela)

Esta consulta traça uma tabela para o estado de falha de teste dos VMs Azure replicados com a Recuperação do Local.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where isnotempty(failoverHealth_s) and isnotnull(failoverHealth_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , VaultName = Resource , TestFailoverStatus = failoverHealth_s 
```

### <a name="query-machine-rpo"></a>Máquina de consulta RPO

Esta consulta traça um gráfico de tendência que rastreia o RPO de um Azure VM específico (ContosoVM123) nas últimas 72 horas.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where TimeGenerated > ago(72h)  
| where isnotempty(name_s) and isnotnull(name_s)   
| where name_s == "ContosoVM123"  
| project TimeGenerated, name_s , RPO_in_seconds = rpoInSeconds_d   
| render timechart 
```
![Máquina de consulta RPO](./media/monitoring-log-analytics/example2.png)

### <a name="query-data-change-rate-churn-and-upload-rate-for-an-azure-vm"></a>Taxa de alteração de dados de consulta (churn) e taxa de upload para um VM Azure

Esta consulta traça um gráfico de tendência para um Azure VM específico (ContosoVM123), que representa a taxa de mudança de dados (Write Bytes por Segundo) e a taxa de upload de dados. 

```
AzureDiagnostics   
| where Category in ("AzureSiteRecoveryProtectedDiskDataChurn", "AzureSiteRecoveryReplicationDataUploadRate")   
| extend CategoryS = case(Category contains "Churn", "DataChurn",   
Category contains "Upload", "UploadRate", "none")  
| extend InstanceWithType=strcat(CategoryS, "_", InstanceName_s)   
| where TimeGenerated > ago(24h)   
| where InstanceName_s startswith "ContosoVM123"   
| project TimeGenerated , InstanceWithType , Churn_MBps = todouble(Value_s)/1048576   
| render timechart  
```
![Alteração de dados de consulta](./media/monitoring-log-analytics/example3.png)

### <a name="query-data-change-rate-churn-and-upload-rate-for-a-vmware-or-physical-machine"></a>Taxa de alteração de dados de consulta (churn) e taxa de upload para um VMware ou máquina física

> [!Note]
> Certifique-se de que configura o agente de monitorização no Servidor de Processos para recolher estes registos. Consulte [os passos para configurar o agente de monitorização](#configure-microsoft-monitoring-agent-on-the-process-server-to-send-churn-and-upload-rate-logs).

Esta consulta traça um gráfico de tendência para um disco específico **de** um item replicado **win-9r7sfh9qlru**, que representa a taxa de mudança de dados (Write Bytes por Segundo) e a taxa de upload de dados. Pode encontrar o nome do disco na lâmina dos **Discos** do item replicado no cofre dos serviços de recuperação. O nome da instância a ser usado na consulta é o nome DNS da máquina seguido de _ e o nome do disco como neste exemplo.

```
Perf
| where ObjectName == "ASRAnalytics"
| where InstanceName contains "win-9r7sfh9qlru_disk0"
| where TimeGenerated >= ago(4h) 
| project TimeGenerated ,CounterName, Churn_MBps = todouble(CounterValue)/5242880 
| render timechart
```
O Process Server empurra estes dados a cada 5 minutos para o espaço de trabalho do Log Analytics. Estes pontos de dados representam a média calculada durante 5 minutos.

### <a name="query-disaster-recovery-summary-azure-to-azure"></a>Resumo de recuperação de desastres de consulta (Azure to Azure)

Esta consulta traça uma tabela sumária para VMs Azure replicada para uma região secundária de Azure.  Mostra o nome, o estado de replicação e proteção do VM, o RPO, o estado de falha de teste, a versão do agente de mobilidade, quaisquer erros de replicação ativas e a localização da fonte.

```
AzureDiagnostics 
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, SourceLocation = primaryFabricName_s 
```

### <a name="query-disaster-recovery-summary-vmwarephysical-servers"></a>Resumo de recuperação de desastres de consulta (VMware/servidores físicos)

Esta consulta traça uma tabela de resumo para VMware VMs e servidores físicos replicados para Azure.  Mostra o nome da máquina, o estado de replicação e proteção, o RPO, o estado de falha de teste, a versão do agente de mobilidade, quaisquer erros de replicação ativas e o servidor de processo relevante.

```
AzureDiagnostics  
| where replicationProviderName_s == "InMageAzureV2"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, ProcessServer = processServerName_g  
```

## <a name="set-up-alerts---examples"></a>Configurar alertas - exemplos

Pode configurar alertas de recuperação do site com base em dados do Monitor Do Azure. [Saiba mais](../azure-monitor/platform/alerts-log.md#managing-log-alerts-from-the-azure-portal) sobre a configuração de alertas de registo. 

> [!NOTE]
> Alguns dos exemplos usam **replicationProviderName_s** definido para **A2A**. Isto define alertas para VMs Azure que são replicados para uma região secundária de Azure. Nestes exemplos, pode substituir **a A2A** por **InMageAzureV2** se pretender definir alertas para VMware VMware no local ou servidores físicos replicados para o Azure.

### <a name="multiple-machines-in-a-critical-state"></a>Múltiplas máquinas em estado crítico

Estabeleça um alerta se mais de 20 VMs Azure replicados entrarem em estado crítico.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count() 
```
Para o alerta, detete o **valor limiar** para 20.

### <a name="single-machine-in-a-critical-state"></a>Máquina única em estado crítico

Instale um alerta se um VM Azure replicado específico entrar em estado crítico.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where name_s == "ContosoVM123"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
Para o alerta, detete o **valor limiar** para 1.

### <a name="multiple-machines-exceed-rpo"></a>Várias máquinas excedem RPO

Instale um alerta se o RPO por mais de 20 VMs Azure exceder 30 minutos.
```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where rpoInSeconds_d > 1800  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project name_s , rpoInSeconds_d   
| summarize count()  
```
Para o alerta, detete o **valor limiar** para 20.

### <a name="single-machine-exceeds-rpo"></a>Máquina única excede RPO

Instale um alerta se o RPO para um único VM Azure exceder 30 minutos.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where name_s == "ContosoVM123"  
| where rpoInSeconds_d > 1800  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project name_s , rpoInSeconds_d   
| summarize count()  
```
Para o alerta, detete o **valor limiar** para 1.

### <a name="test-failover-for-multiple-machines-exceeds-90-days"></a>Falha no teste para várias máquinas excede 90 dias

Criar um alerta se o último teste falhar foi de mais de 90 dias, para mais de 20 VMs. 

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where Category == "AzureSiteRecoveryReplicatedItems"  
| where isnotempty(name_s) and isnotnull(name_s)   
| where lastSuccessfulTestFailoverTime_t <= ago(90d)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
Para o alerta, detete o **valor limiar** para 20.

### <a name="test-failover-for-single-machine-exceeds-90-days"></a>Falha no teste para máquina única ultrapassa os 90 dias

Criar um alerta se o último teste bem sucedido falhar para um VM específico foi há mais de 90 dias.
```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where Category == "AzureSiteRecoveryReplicatedItems"  
| where isnotempty(name_s) and isnotnull(name_s)   
| where lastSuccessfulTestFailoverTime_t <= ago(90d)   
| where name_s == "ContosoVM123"  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
Para o alerta, detete o **valor limiar** para 1.

### <a name="site-recovery-job-fails"></a>Trabalho de recuperação do site falha

Configurar um alerta se um trabalho de Recuperação do Site (neste caso o trabalho de Reproteção) falhar em qualquer cenário de Recuperação do Site, durante o último dia. 
```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryJobs"   
| where OperationName == "Reprotect"  
| where ResultType == "Failed"  
| summarize count()  
```

Para o alerta, estabeleça o **valor limiar** para 1, e **periode** para 1440 minutos, para verificar falhas no último dia.

## <a name="next-steps"></a>Passos seguintes

[Saiba mais sobre](site-recovery-monitor-and-troubleshoot.md) a monitorização incorporada da recuperação do site.
