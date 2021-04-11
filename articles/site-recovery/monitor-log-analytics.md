---
title: Monitor Azure Site Recovery with Azure Monitor Logs
description: Saiba como monitorizar a recuperação do site Azure com registos do monitor Azure (Log Analytics)
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/15/2019
ms.openlocfilehash: eb38f7c2df6a8ad00009a0b7fc9c03638c33034c
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581748"
---
# <a name="monitor-site-recovery-with-azure-monitor-logs"></a>Monitorizar o Site Recovery com os Registos do Azure Monitor

Este artigo descreve como monitorizar as máquinas replicadas pela Azure [Site Recovery](site-recovery-overview.md), utilizando [registos do Monitor Azure](../azure-monitor/logs/data-platform-logs.md)e [Log Analytics](../azure-monitor/logs/log-query-overview.md).

Os Registos do Monitor Azure fornecem uma plataforma de dados de registo que recolhe atividade e registos de recursos, juntamente com outros dados de monitorização. Nos Registos do Monitor Azure, utiliza o Log Analytics para escrever e testar consultas de registo e para analisar interativamente dados de registo. Pode visualizar e consultar os resultados dos registos e configurar alertas para tomar ações com base em dados monitorizados.

Para a recuperação do local, pode utilizar registos do Monitor Azure para ajudá-lo a fazer o seguinte:

- **Monitorizar a saúde e o estado da recuperação do local.** Por exemplo, pode monitorizar a saúde da replicação, o estado de falha do teste, os eventos de recuperação do local, os objetivos do ponto de recuperação (RPOs) para máquinas protegidas e as taxas de alteração do disco/dados.
- **Configurar alertas para a Recuperação do Local.** Por exemplo, pode configurar alertas para a saúde da máquina, para testar o estado de falha do trabalho ou para a recuperação do local.

A utilização de Registos monitores Azure com Recuperação de Locais é suportada para replicação **de Azure a Azure** e **VMware VM/servidor físico para** a replicação do Azure.

> [!NOTE]
> Para obter os registos de dados de churn e registos de taxas de carregamento para VMware e máquinas físicas, é necessário instalar um agente de monitorização da Microsoft no Process Server. Este agente envia os registos das máquinas de replicação para o espaço de trabalho. Esta capacidade está disponível apenas para a versão 9.30 do agente de mobilidade.

## <a name="before-you-start"></a>Antes de começar

Eis o que necessita:

- Pelo menos uma máquina protegida num cofre dos Serviços de Recuperação.
- Um espaço de trabalho log Analytics para armazenar registos de recuperação do site. [Saiba como](../azure-monitor/logs/quick-create-workspace.md) criar um espaço de trabalho.
- Uma compreensão básica de como escrever, executar e analisar consultas de registo em Log Analytics. [Saiba mais](../azure-monitor/logs/log-analytics-tutorial.md).

Recomendamos que reveja [questões comuns de monitorização](monitoring-common-questions.md) antes de começar.

## <a name="configure-site-recovery-to-send-logs"></a>Configurar a Recuperação do Site para enviar registos

1. No cofre, clique nas **definições de Diagnóstico**  >  **Adicione a definição de diagnóstico**.

    ![Screenshot mostrando a opção de definição de diagnóstico Adicionar.](./media/monitoring-log-analytics/add-diagnostic.png)

2. Nas **definições de Diagnóstico**, especifique um nome e verifique a caixa Enviar para Registar **Analytics**.
3. Selecione a subscrição de Registos do Monitor Azure e o espaço de trabalho Log Analytics.
4. Selecione **Azure Diagnostics** no toggle.
5. Na lista de registos, selecione todos os registos com o prefixo **AzureSiteRecovery**. Em seguida, clique em **OK**.

    ![Screenshot do ecrã de definição de Diagnóstico.](./media/monitoring-log-analytics/select-workspace.png)

Os registos de recuperação do local começam a alimentar-se numa tabela **(AzureDiagnostics)** no espaço de trabalho selecionado.

## <a name="configure-microsoft-monitoring-agent-on-the-process-server-to-send-churn-and-upload-rate-logs"></a>Configure o agente de monitorização da Microsoft no Servidor de Processos para enviar registos de tarifas de churn e upload

Pode capturar as informações sobre a taxa de bito de dados e a informação de cartão de dados de origem para as suas VMware/máquinas físicas no local. Para ativar isto, é necessário instalar um agente de monitorização da Microsoft no Servidor de Processos.

1. Vá ao espaço de trabalho do Log Analytics e clique em **Definições Avançadas**.
2. Clique na página **'Fontes Conectadas'** e selecione ainda **servidores do Windows**.
3. Descarregue o Windows Agent (64 bit) no Servidor de Processos. 
4. [Obtenha o ID e a chave do espaço de trabalho](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key)
5. [Configure o agente para utilizar o TLS 1.2](../azure-monitor/agents/agent-windows.md#configure-agent-to-use-tls-12)
6. [Complete a instalação do agente](../azure-monitor/agents/agent-windows.md#install-agent-using-setup-wizard) fornecendo o ID e a chave do espaço de trabalho obtidos.
7. Uma vez concluída a instalação, vá ao espaço de trabalho do Log Analytics e clique em **Definições Avançadas**. Aceda à página **de Dados** e clique mais em **Contadores de Desempenho do Windows**. 
8. Clique em **'+'** para adicionar os seguintes dois contadores com intervalo de amostra de 300 segundos:

    - ASRAnalytics(*)\SourceVmChurnRate
    - ASRAnalytics(*)\SourceVmThrpRate

Os dados da taxa de churn e upload começarão a alimentar-se no espaço de trabalho.


## <a name="query-the-logs---examples"></a>Consultar os registos - exemplos

Obtém dados de registos utilizando consultas de registo escritas com o [idioma de consulta kusto](../azure-monitor/logs/get-started-queries.md). Esta secção fornece alguns exemplos de consultas comuns que pode utilizar para monitorização da recuperação do local.

> [!NOTE]
> Alguns dos exemplos utilizam **replicationProviderName_s** definidos para **A2A**. Isto recupera VMs Azure que são replicados para uma região secundária de Azure usando a Recuperação do Local. Nestes exemplos, pode substituir **o A2A** pelo **InMageAzureV2,** se pretender recuperar VMware VMware no local ou servidores físicos que sejam replicados para Azure utilizando a Recuperação do Site.


### <a name="query-replication-health"></a>Saúde da replicação de consultas

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
### <a name="query-mobility-service-version"></a>Versão do serviço de mobilidade de consulta

Esta consulta traça um gráfico de tartes para VMs Azure replicados com Recuperação de Sítio, dividido pela versão do agente de Mobilidade que estão a executar.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , agentVersion_s  
| summarize count() by agentVersion_s  
| render piechart 
```

### <a name="query-rpo-time"></a>Consulta RPO tempo

Esta consulta traça um gráfico de barras de VMs Azure replicados com Recuperação do Local, dividido por objetivo de ponto de recuperação (RPO): Menos de 15 minutos, entre 15-30 minutos, mais de 30 minutos.

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

![Screenshot mostrando um gráfico de barras de VMs Azure replicados com Recuperação de Local.](./media/monitoring-log-analytics/example1.png)

### <a name="query-site-recovery-jobs"></a>Trabalhos de recuperação de locais de consulta

Esta consulta recupera todos os trabalhos de Recuperação de Locais (para todos os cenários de recuperação de desastres), desencadeados nas últimas 72 horas, e o seu estado de conclusão.

```
AzureDiagnostics  
| where Category == "AzureSiteRecoveryJobs"  
| where TimeGenerated >= ago(72h)   
| project JobName = OperationName , VaultName = Resource , TargetName = affectedResourceName_s, State = ResultType  
```

### <a name="query-site-recovery-events"></a>Eventos de recuperação do site de consulta

Esta consulta recupera todos os eventos de Recuperação do Local (para todos os cenários de recuperação de desastres) levantados nas últimas 72 horas, juntamente com a sua gravidade. 

```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryEvents"   
| where TimeGenerated >= ago(72h)   
| project AffectedObject=affectedResourceName_s , VaultName = Resource, Description_s = healthErrors_s , Severity = Level  
```

### <a name="query-test-failover-state-pie-chart"></a>Estado de falha do teste de consulta (gráfico de tarte)

Esta consulta traça um gráfico de tartes para o estado de failover de teste de VMs Azure replicados com Recuperação de Local.

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

### <a name="query-test-failover-state-table"></a>Estado de failover do teste de consulta (tabela)

Esta consulta traça uma tabela para o estado de failover de teste de VMs Azure replicados com Recuperação de Local.

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
![Screenshot de um gráfico de tendência rastreando o RPO de um VM Azure específico.](./media/monitoring-log-analytics/example2.png)

### <a name="query-data-change-rate-churn-and-upload-rate-for-an-azure-vm"></a>Taxa de alteração de dados de consulta (churn) e taxa de upload para um VM Azure

Esta consulta traça um gráfico de tendência para um VM Azure específico (ContosoVM123), que representa a taxa de alteração de dados (Write Bytes por Segundo) e taxa de upload de dados. 

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
![screenshot de um gráfico de tendência para um Azure VM específico.](./media/monitoring-log-analytics/example3.png)

### <a name="query-data-change-rate-churn-and-upload-rate-for-a-vmware-or-physical-machine"></a>Taxa de alteração de dados de consulta (churn) e taxa de upload para um VMware ou máquina física

> [!Note]
> Certifique-se de que configura o agente de monitorização no Servidor de Processos para recolher estes registos. Consulte [os passos para configurar o agente de monitorização](#configure-microsoft-monitoring-agent-on-the-process-server-to-send-churn-and-upload-rate-logs).

Esta consulta traça um gráfico de tendência para um disco específico **0** de um item replicado **win-9r7sfh9qlru,** que representa a taxa de alteração de dados (Write Bytes por Segundo) e a taxa de upload de dados. Pode encontrar o nome do disco na lâmina **dos Discos** do item replicado no cofre dos serviços de recuperação. O nome da instância a ser usado na consulta é o nome DNS da máquina seguido por _ e nome do disco como neste exemplo.

```
Perf
| where ObjectName == "ASRAnalytics"
| where InstanceName contains "win-9r7sfh9qlru_disk0"
| where TimeGenerated >= ago(4h) 
| project TimeGenerated ,CounterName, Churn_MBps = todouble(CounterValue)/5242880 
| render timechart
```
O Process Server empurra estes dados a cada 5 minutos para o espaço de trabalho do Log Analytics. Estes pontos de dados representam a média calculada durante 5 minutos.

### <a name="query-disaster-recovery-summary-azure-to-azure"></a>Resumo da recuperação de desastres de consulta (Azure to Azure)

Esta consulta traça uma tabela de resumo para VMs Azure replicados para uma região secundária de Azure.  Mostra o nome VM, o estado de replicação e proteção, RPO, estado de falha de teste, versão do agente de mobilidade, quaisquer erros de replicação ativos e a localização da fonte.

```
AzureDiagnostics 
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, SourceLocation = primaryFabricName_s 
```

### <a name="query-disaster-recovery-summary-vmwarephysical-servers"></a>Resumo da recuperação de desastres de consulta (VMware/servidores físicos)

Esta consulta traça uma tabela de resumo para VMware VMs e servidores físicos replicados para Azure.  Mostra o nome da máquina, o estado de replicação e proteção, RPO, estado de falha de teste, versão do agente de mobilidade, quaisquer erros de replicação ativos e o servidor de processo relevante.

```
AzureDiagnostics  
| where replicationProviderName_s == "InMageAzureV2"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, ProcessServer = processServerName_g  
```

## <a name="set-up-alerts---examples"></a>Configurar alertas - exemplos

Pode configurar alertas de Recuperação do Local com base nos dados do Azure Monitor. [Saiba mais](../azure-monitor/alerts/alerts-log.md#create-a-log-alert-rule-with-the-azure-portal) sobre a configuração de alertas de registo. 

> [!NOTE]
> Alguns dos exemplos utilizam **replicationProviderName_s** definidos para **A2A**. Isto estabelece alertas para VMs Azure que são replicados para uma região secundária de Azure. Nestes exemplos, pode substituir **o A2A** pelo **InMageAzureV2** se quiser definir alertas para VMware VMware no local ou servidores físicos replicados no Azure.

### <a name="multiple-machines-in-a-critical-state"></a>Várias máquinas em estado crítico

Confiúde um alerta se mais de 20 VMs Azure replicados entrarem num estado crítico.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count() 
```
Para o alerta, desa um **valor de limiar** para 20.

### <a name="single-machine-in-a-critical-state"></a>Máquina única em estado crítico

Confiúde um alerta se um VM Azure replicado específico entrar num estado crítico.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where name_s == "ContosoVM123"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
Para o alerta, desa um **valor de limiar** para 1.

### <a name="multiple-machines-exceed-rpo"></a>Várias máquinas excedem a RPO

Confiúde um alerta se o RPO por mais de 20 VMs Azure exceder 30 minutos.
```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where rpoInSeconds_d > 1800  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project name_s , rpoInSeconds_d   
| summarize count()  
```
Para o alerta, desa um **valor de limiar** para 20.

### <a name="single-machine-exceeds-rpo"></a>Máquina única excede RPO

Confiúde um alerta se o RPO para um único VM Azure exceder 30 minutos.

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
Para o alerta, desa um **valor de limiar** para 1.

### <a name="test-failover-for-multiple-machines-exceeds-90-days"></a>Teste de failover para várias máquinas excede 90 dias

Estabeleça um alerta se o último teste de sucesso falhou mais de 90 dias, para mais de 20 VMs. 

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where Category == "AzureSiteRecoveryReplicatedItems"  
| where isnotempty(name_s) and isnotnull(name_s)   
| where lastSuccessfulTestFailoverTime_t <= ago(90d)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
Para o alerta, desa um **valor de limiar** para 20.

### <a name="test-failover-for-single-machine-exceeds-90-days"></a>Teste de failover para máquina única excede 90 dias

Estabeleça um alerta se o último teste bem sucedido falhar para um VM específico foi há mais de 90 dias.
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
Para o alerta, desa um **valor de limiar** para 1.

### <a name="site-recovery-job-fails"></a>Trabalho de recuperação de local falha

Estabeleça um alerta se um trabalho de Recuperação do Site (neste caso, o trabalho de Reprotect) falhar para qualquer cenário de Recuperação do Local, durante o último dia. 
```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryJobs"   
| where OperationName == "Reprotect"  
| where ResultType == "Failed"  
| summarize count()  
```

Para o alerta, desa um **valor de limiar** para 1 e **periode** para 1440 minutos, para verificar falhas no último dia.

## <a name="next-steps"></a>Passos seguintes

[Saiba mais sobre](site-recovery-monitor-and-troubleshoot.md) a monitorização incorporada da Recuperação do Local.