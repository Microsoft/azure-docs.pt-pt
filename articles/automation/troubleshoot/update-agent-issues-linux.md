---
title: Entender a integridade do Linux Hybrid Runbook Worker no Azure Gerenciamento de Atualizações
description: Saiba como solucionar problemas com o Hybrid Runbook Worker no Linux que oferece suporte a Gerenciamento de Atualizações.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 12/03/2019
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: def0ac11edfa5a17a8e506c79d91885dd3c9ab66
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770402"
---
# <a name="understand-the-linux-hybrid-runbook-worker-health-in-update-management"></a>Entenda a integridade do Linux Hybrid Runbook Worker no Gerenciamento de Atualizações

Pode haver muitos motivos pelos quais seu computador não está mostrando **pronto** no gerenciamento de atualizações. No Gerenciamento de Atualizações, você pode verificar a integridade de um agente de Hybrid Runbook Worker para determinar o problema subjacente. Este artigo discute como executar a solução de problemas para computadores do Azure por meio do portal do Azure e de computadores não Azure no [cenário offline](#troubleshoot-offline).

A lista a seguir são os três Estados de preparação em que um computador pode estar:

* **Pronto** -a Hybrid runbook Worker é implantada e foi vista pela última vez há menos de 1 hora.
* **Desconectado** -a Hybrid runbook Worker é implantada e foi vista pela última vez há uma hora.
* **Não configurado** -a Hybrid runbook Worker não foi encontrada ou não terminou a integração.

> [!NOTE]
> Pode haver um pequeno atraso entre o que o portal do Azure mostra e o estado atual do computador.

## <a name="start-the-troubleshooter"></a>Iniciar a solução de problemas

Para computadores do Azure, clicar no link **solucionar problemas** na coluna **prontidão do agente de atualização** no portal inicia a página **solucionar problemas do agente de atualização** . Para computadores não Azure, o link o leva a este artigo. Consulte as instruções offline para solucionar problemas em um computador não Azure.

![página lista de VMs](../media/update-agent-issues-linux/vm-list.png)

> [!NOTE]
> As verificações exigem que a VM esteja em execução. Se a VM não estiver em execução, você verá um botão para **iniciar a VM**.

Na página **solucionar problemas do agente de atualização** , clique em **executar verificações**para iniciar a solução de problemas. A solução de problemas usa o [comando executar](../../virtual-machines/linux/run-command.md) para executar um script no computador para verificar as dependências. Quando a solução de problemas estiver concluída, ela retornará o resultado das verificações.

![Página de solução de problemas](../media/update-agent-issues-linux/troubleshoot-page.png)

Ao concluir, os resultados são retornados na janela. As seções de verificação fornecem informações sobre o que cada verificação está procurando.

![Página de verificações do agente de atualização](../media/update-agent-issues-linux/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Verificações de pré-requisitos

### <a name="operating-system"></a>Sistema operativo

A verificação do sistema operacional verifica se o Hybrid Runbook Worker está executando um dos seguintes sistemas operacionais:

|Sistema operativo  |Notas  |
|---------|---------|
|CentOS 6 (x86/x64) e 7 (x64)      | Os agentes do Linux têm de ter acesso a um repositório de atualização. A aplicação de patches baseada em classificação requer ' yum ' para retornar dados de segurança que o CentOS não está pronto para uso.         |
|Red Hat Enterprise 6 (x86/x64) e 7 (x64)     | Os agentes do Linux têm de ter acesso a um repositório de atualização.        |
|SUSE Linux Enterprise Server 11 (x86/x64) e 12 (x64)     | Os agentes do Linux têm de ter acesso a um repositório de atualização.        |
|Ubuntu 14, 4 LTS, 16, 4 LTS e 18, 4 LTS (x86/x64)      |Os agentes do Linux têm de ter acesso a um repositório de atualização.         |

## <a name="monitoring-agent-service-health-checks"></a>Monitoramento de verificações de integridade do serviço do agente

### <a name="log-analytics-agent"></a>Agente de Log Analytics

Essa verificação garante que o agente do Log Analytics para Linux esteja instalado. Para obter instruções sobre como instalá-lo, consulte [instalar o agente para Linux](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux
).

### <a name="log-analytics-agent-status"></a>Status do agente de Log Analytics

Essa verificação garante que o agente do Log Analytics para Linux esteja em execução. Se o agente não estiver em execução, você poderá executar o comando a seguir para tentar reiniciá-lo. Para obter mais informações sobre como solucionar problemas do agente, consulte [solução de problemas do runbook Worker do Linux Hybrid](hybrid-runbook-worker.md#linux)

```bash
sudo /opt/microsoft/omsagent/bin/service_control restart
```

### <a name="multihoming"></a>Hospedagem múltipla

Essa verificação determina se o agente está se comunicando com vários espaços de trabalho. A hospedagem múltipla não tem suporte pelo Gerenciamento de Atualizações.

### <a name="hybrid-runbook-worker"></a>Função de Trabalho de Runbook Híbrida

Essa verificação verifica se o agente de Log Analytics para Linux tem o pacote Hybrid Runbook Worker. Este pacote é necessário para que Gerenciamento de Atualizações funcionem.

### <a name="hybrid-runbook-worker-status"></a>Status de Hybrid Runbook Worker

Essa verificação verifica se o Hybrid Runbook Worker está em execução no computador. Os processos a seguir devem estar presentes se o Hybrid Runbook Worker estiver sendo executado corretamente. Para saber mais, confira [solução de problemas do agente de log Analytics para Linux](hybrid-runbook-worker.md#oms-agent-not-running).

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

## <a name="connectivity-checks"></a>Verificações de conectividade

### <a name="general-internet-connectivity"></a>Conectividade geral com a Internet

Essa verificação verifica se o computador tem acesso à Internet.

### <a name="registration-endpoint"></a>Ponto de extremidade de registro

Essa verificação determina se o Hybrid Runbook Worker pode se comunicar corretamente com a automação do Azure com o espaço de trabalho Log Analytics.

As configurações de proxy e firewall devem permitir que o agente de Hybrid Runbook Worker se comunique com o ponto de extremidade de registro. Para obter uma lista de endereços e portas a serem abertas, consulte [planejamento de rede para Hybrid Workers](../automation-hybrid-runbook-worker.md#network-planning)

### <a name="operations-endpoint"></a>Ponto de extremidade de operações

Essa verificação determina se o agente pode se comunicar corretamente com o serviço de dados de tempo de execução de trabalho.

As configurações de proxy e firewall devem permitir que o agente de Hybrid Runbook Worker se comunique com o serviço de dados de tempo de execução de trabalho. Para obter uma lista de endereços e portas a serem abertas, consulte [planejamento de rede para Hybrid Workers](../automation-hybrid-runbook-worker.md#network-planning)

### <a name="log-analytics-endpoint-1"></a>Ponto de extremidade Log Analytics 1

Essa verificação verifica se seu computador tem acesso aos pontos de extremidade necessários para o agente de Log Analytics.

### <a name="log-analytics-endpoint-2"></a>Ponto de extremidade Log Analytics 2

Essa verificação verifica se seu computador tem acesso aos pontos de extremidade necessários para o agente de Log Analytics.

### <a name="log-analytics-endpoint-3"></a>Ponto de extremidade de Log Analytics 3

Essa verificação verifica se seu computador tem acesso aos pontos de extremidade necessários para o agente de Log Analytics.

## <a name="troubleshoot-offline"></a>Solucionar problemas offline

Você pode usar o solucionador de problemas offline em um Hybrid Runbook Worker executando o script localmente. O script Python, [update_mgmt_health_check. py](https://gallery.technet.microsoft.com/scriptcenter/Troubleshooting-utility-3bcbefe6) , pode ser encontrado no script Center. Um exemplo da saída desse script é mostrado no exemplo a seguir:

```output
Debug: Machine Information:   Static hostname: LinuxVM2
         Icon name: computer-vm
           Chassis: vm
        Machine ID: 00000000000000000000000000000000
           Boot ID: 00000000000000000000000000000000
    Virtualization: microsoft
  Operating System: Ubuntu 16.04.5 LTS
            Kernel: Linux 4.15.0-1025-azure
      Architecture: x86-64


Passed: Operating system version is supported

Passed: Microsoft Monitoring agent is installed

Debug: omsadmin.conf file contents:
        WORKSPACE_ID=00000000-0000-0000-0000-000000000000
        AGENT_GUID=00000000-0000-0000-0000-000000000000
        LOG_FACILITY=local0
        CERTIFICATE_UPDATE_ENDPOINT=https://00000000-0000-0000-0000-000000000000.oms.opinsights.azure.com/ConfigurationService.Svc/RenewCertificate
        URL_TLD=opinsights.azure.com
        DSC_ENDPOINT=https://scus-agentservice-prod-1.azure-automation.net/Accou            nts/00000000-0000-0000-0000-000000000000/Nodes\(AgentId='00000000-0000-0000-0000-000000000000'\)
        OMS_ENDPOINT=https://00000000-0000-0000-0000-000000000000.ods.opinsights            .azure.com/OperationalData.svc/PostJsonDataItems
        AZURE_RESOURCE_ID=/subscriptions/00000000-0000-0000-0000-000000000000/re            sourcegroups/myresourcegroup/providers/microsoft.compute/virtualmachines/linuxvm            2
        OMSCLOUD_ID=0000-0000-0000-0000-0000-0000-00
        UUID=00000000-0000-0000-0000-000000000000


Passed: Microsoft Monitoring agent is running

Passed: Machine registered with log analytics workspace:['00000000-0000-0000-0000-000000000000']

Passed: Hybrid worker package is present

Passed: Hybrid worker is running

Passed: Machine is connected to internet

Passed: TCP test for {scus-agentservice-prod-1.azure-automation.net} (port 443)             succeeded

Passed: TCP test for {eus2-jobruntimedata-prod-su1.azure-automation.net} (port 4            43) succeeded

Passed: TCP test for {00000000-0000-0000-0000-000000000000.ods.opinsights.azure.            com} (port 443) succeeded

Passed: TCP test for {00000000-0000-0000-0000-000000000000.oms.opinsights.azure.            com} (port 443) succeeded

Passed: TCP test for {ods.systemcenteradvisor.com} (port 443) succeeded

```

## <a name="next-steps"></a>Passos seguintes

Para solucionar problemas adicionais com os Hybrid runbook Workers, consulte [solução de problemas-Hybrid runbook Workers](hybrid-runbook-worker.md)