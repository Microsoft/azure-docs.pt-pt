---
title: Diagnosticar Hybrid Runbook Worker do Linux – Gerenciamento de Atualizações do Azure
description: Saiba como solucionar e resolver problemas com o Hybrid Runbook Worker de automação do Azure no Linux que dá suporte a Gerenciamento de Atualizações.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 12/03/2019
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: e60ba71607b99f0ea97e0725ffdd0740f3e9c579
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769834"
---
# <a name="understand-and-resolve-linux-hybrid-runbook-worker-health-for-update-management"></a>Entender e resolver o Linux Hybrid Runbook Worker Health para Gerenciamento de Atualizações

Pode haver muitos motivos pelos quais seu computador não está mostrando **pronto** no gerenciamento de atualizações. No Gerenciamento de Atualizações, você pode verificar a integridade de um agente de Hybrid Runbook Worker para determinar o problema subjacente. Este artigo discute como executar a solução de problemas para computadores do Azure por meio do portal do Azure e de computadores não Azure no [cenário offline](#troubleshoot-offline).

A lista a seguir são os três Estados de preparação em que um computador pode estar:

* **Pronto** -a Hybrid runbook Worker é implantada e foi vista pela última vez há menos de 1 hora.
* **Desconectado** -a Hybrid runbook Worker é implantada e foi vista pela última vez há uma hora.
* **Não configurado** -a Hybrid runbook Worker não foi encontrada ou não terminou a integração.

> [!NOTE]
> Pode haver um pequeno atraso entre o que o portal do Azure mostra e o estado atual do computador.

## <a name="start-the-troubleshooter"></a>Iniciar a resolução de problemas

Para computadores do Azure, clicar no link **solucionar problemas** na coluna **prontidão do agente de atualização** no portal inicia a página **solucionar problemas do agente de atualização** . Para computadores não Azure, o link o leva a este artigo. Consulte as instruções offline para solucionar problemas em um computador não Azure.

![página de lista de VM](../media/update-agent-issues-linux/vm-list.png)

> [!NOTE]
> As verificações de exigem que a VM estar em execução. Se a VM não está em execução, verá um botão para **iniciar a VM**.

Sobre o **resolver problemas de agente de atualização** página, clique em **executar verifica**, para iniciar a resolução de problemas. A solução de problemas usa o [comando executar](../../virtual-machines/linux/run-command.md) para executar um script no computador para verificar as dependências. Quando a resolução de problemas estiver concluída, ela retorna o resultado das verificações.

![Resolver problemas de página](../media/update-agent-issues-linux/troubleshoot-page.png)

Quando terminar, os resultados são devolvidos na janela. As seções de verificação fornecem informações sobre o que cada verificação está procurando.

![Página de verificações de atualização de agente](../media/update-agent-issues-linux/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Verificações de pré-requisitos

### <a name="operating-system"></a>Sistema operativo

A verificação do sistema operacional verifica se o Hybrid Runbook Worker está executando um dos seguintes sistemas operacionais:

|Sistema operativo  |Notas  |
|---------|---------|
|CentOS 6 (x86/x64) e 7 (x64)      | Os agentes do Linux têm de ter acesso a um repositório de atualização. A aplicação de patches baseada em classificação requer ' yum ' para retornar dados de segurança que o CentOS não está pronto para uso.         |
|Red Hat Enterprise 6 (x86/x64) e 7 (x64)     | Os agentes do Linux têm de ter acesso a um repositório de atualização.        |
|SUSE Linux Enterprise Server 11 (x86/x64) e 12 (x64)     | Os agentes do Linux têm de ter acesso a um repositório de atualização.        |
|Ubuntu 14.04 LTS e 16.04 LTS e 18.04 LTS (x86/x64)      |Os agentes do Linux têm de ter acesso a um repositório de atualização.         |

## <a name="monitoring-agent-service-health-checks"></a>Monitorização verificações de estado de funcionamento do serviço de agente

### <a name="log-analytics-agent"></a>Agente de Log Analytics

Essa verificação garante que o agente do Log Analytics para Linux esteja instalado. Para obter instruções sobre como instalá-lo, consulte [instalar o agente para Linux](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux
).

### <a name="log-analytics-agent-status"></a>Status do agente de Log Analytics

Essa verificação garante que o agente do Log Analytics para Linux esteja em execução. Se o agente não estiver em execução, você poderá executar o comando a seguir para tentar reiniciá-lo. Para obter mais informações sobre como solucionar problemas do agente, consulte [solução de problemas do runbook Worker do Linux Hybrid](hybrid-runbook-worker.md#linux)

```bash
sudo /opt/microsoft/omsagent/bin/service_control restart
```

### <a name="multihoming"></a>Multihoming

Esta verificação determina se o agente está a comunicar para várias áreas de trabalho. Multi-homing não é suportado pelo gerenciamento de atualizações.

### <a name="hybrid-runbook-worker"></a>Função de Trabalho de Runbook Híbrida

Essa verificação verifica se o agente de Log Analytics para Linux tem o pacote Hybrid Runbook Worker. Este pacote é necessária para a gestão de atualização funcionar.

### <a name="hybrid-runbook-worker-status"></a>Estado de trabalho de Runbook híbrida

Esta verificação torna-se de que a função de trabalho de Runbook híbrida está em execução na máquina. Os seguintes processos devem estar presentes se a função de trabalho de Runbook híbrida está a ser executado corretamente. Para obter mais informações, consulte [o Log Analytics Agent de resolução de problemas para Linux](hybrid-runbook-worker.md#oms-agent-not-running).

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

## <a name="connectivity-checks"></a>Verificações de conectividade

### <a name="general-internet-connectivity"></a>Conectividade internet

Esta verificação certifica-se de que a máquina tem acesso à internet.

### <a name="registration-endpoint"></a>Ponto final do registo

Essa verificação determina se o Hybrid Runbook Worker pode se comunicar corretamente com a automação do Azure com o espaço de trabalho Log Analytics.

Configurações de proxy e de firewall têm de permitir o agente de trabalho de Runbook híbrida comunicar com o ponto final do registo. Para obter uma lista de endereços e portas para abrir, consulte [planear funções de trabalho híbridas de rede](../automation-hybrid-runbook-worker.md#network-planning)

### <a name="operations-endpoint"></a>Ponto final de operações

Esta verificação determina se o agente corretamente pode comunicar com o serviço de dados de tempo de execução de tarefa.

Configurações de proxy e de firewall têm de permitir o agente de trabalho de Runbook híbrida para comunicar com o serviço de dados de tempo de execução de tarefa. Para obter uma lista de endereços e portas para abrir, consulte [planear funções de trabalho híbridas de rede](../automation-hybrid-runbook-worker.md#network-planning)

### <a name="log-analytics-endpoint-1"></a>Ponto de extremidade do log Analytics 1

Esta verificação verifica se o seu computador tem acesso aos pontos finais necessários para o agente Log Analytics.

### <a name="log-analytics-endpoint-2"></a>Ponto de extremidade do log Analytics 2

Esta verificação verifica se o seu computador tem acesso aos pontos finais necessários para o agente Log Analytics.

### <a name="log-analytics-endpoint-3"></a>Ponto de extremidade do log Analytics 3

Esta verificação verifica se o seu computador tem acesso aos pontos finais necessários para o agente Log Analytics.

## <a name="troubleshoot-offline"></a>Solucionar problemas offline

Pode utilizar a resolução de problemas offline numa função de trabalho de Runbook híbridas executando o script localmente. O script de python [update_mgmt_health_check.py](https://gallery.technet.microsoft.com/scriptcenter/Troubleshooting-utility-3bcbefe6) podem ser encontrados no Script Center. Um exemplo da saída deste script é mostrado no exemplo a seguir:

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

Para solucionar problemas adicionais com os Hybrid runbook Workers, consulte [solução de problemas-Hybrid runbook Workers](hybrid-runbook-worker.md).
