---
title: Diagnosticar O Trabalhador do Livro híbrido do Linux - Gestão de Atualização Azure
description: Saiba como resolver problemas com o Trabalhador de Runbook Híbrido de Automação Azure no Linux que suporta a Gestão de Atualizações.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 12/03/2019
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: e60ba71607b99f0ea97e0725ffdd0740f3e9c579
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79278301"
---
# <a name="understand-and-resolve-linux-hybrid-runbook-worker-health-for-update-management"></a>Compreender e resolver a saúde do trabalhador do livro de corridas híbrido linux para gestão de atualizações

Pode haver muitas razões para a sua máquina não mostrar **Ready** in Update Management. Na Gestão de Atualizações, pode verificar a saúde de um agente híbrido do Runbook Worker para determinar o problema subjacente. Este artigo discute como executar o resolução de problemas para máquinas Azure a partir do portal Azure e máquinas não-Azure no [cenário offline](#troubleshoot-offline).

A lista que se segue são os três estados de prontidão em que uma máquina pode estar:

* **Ready** - O Trabalhador híbrido do livro está implantado e foi visto pela última vez há menos de uma hora.
* **Desligado** - O Trabalhador híbrido do livro de corridas está implantado e foi visto pela última vez há mais de uma hora.
* **Não configurado** - O Trabalhador híbrido do runbook não é encontrado ou ainda não terminou o embarque.

> [!NOTE]
> Pode haver um ligeiro atraso entre o que o portal Azure mostra e o estado atual da máquina.

## <a name="start-the-troubleshooter"></a>Iniciar a resolução de problemas

Para máquinas Azure, clique no link **Troubleshoot** sob a coluna **de prontidão** do agente de atualização no portal lança a página do Agente de Atualização de **Resolução** de Problemas. Para máquinas não-Azure, o link leva-o a este artigo. Consulte as instruções offline para filmar uma máquina não Azure.

![página de lista de VM](../media/update-agent-issues-linux/vm-list.png)

> [!NOTE]
> As verificações de exigem que a VM estar em execução. Se o VM não estiver a funcionar, é-lhe apresentado um botão para **iniciar o VM**.

Na página do **Agente de Atualização** de Problemas, clique em **Controlos de Execução,** para iniciar o resolução de problemas. O resoluto de problemas usa o [comando run](../../virtual-machines/linux/run-command.md) para executar um script na máquina para verificar as dependências. Quando a resolução de problemas estiver concluída, ela retorna o resultado das verificações.

![Resolver problemas de página](../media/update-agent-issues-linux/troubleshoot-page.png)

Quando terminar, os resultados são devolvidos na janela. As secções de verificação fornecem informações sobre o que cada cheque procura.

![Página de verificações de atualização de agente](../media/update-agent-issues-linux/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Verificações de pré-requisitos

### <a name="operating-system"></a>Sistema operativo

A verificação do sistema operativo verifica se o Trabalhador do Livro híbrido está a executar um dos seguintes Sistemas Operativos:

|Sistema operativo  |Notas  |
|---------|---------|
|CentOS 6 (x86/x64) e 7 (x64)      | Os agentes do Linux têm de ter acesso a um repositório de atualização. A correção baseada na classificação requer 'yum' para devolver dados de segurança que o CentOS não tem fora da caixa.         |
|Red Hat Enterprise 6 (x86/x64) e 7 (x64)     | Os agentes do Linux têm de ter acesso a um repositório de atualização.        |
|SUSE Linux Enterprise Server 11 (x86/x64) e 12 (x64)     | Os agentes do Linux têm de ter acesso a um repositório de atualização.        |
|Ubuntu 14.04 LTS e 16.04 LTS e 18.04 LTS (x86/x64)      |Os agentes do Linux têm de ter acesso a um repositório de atualização.         |

## <a name="monitoring-agent-service-health-checks"></a>Monitorização verificações de estado de funcionamento do serviço de agente

### <a name="log-analytics-agent"></a>Agente do Log Analytics

Esta verificação garante que o agente Log Analytics para o Linux está instalado. Para obter instruções sobre como instalá-lo, consulte [Instale o agente para Linux](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux
).

### <a name="log-analytics-agent-status"></a>Estado do agente Log Analytics

Esta verificação garante que o agente Log Analytics do Linux está a funcionar. Se o agente não estiver a funcionar, pode executar o seguinte comando para tentar reiniciá-lo. Para mais informações sobre a resolução de problemas com o agente, consulte [linux Hybrid Runbook worker troubleshooting](hybrid-runbook-worker.md#linux)

```bash
sudo /opt/microsoft/omsagent/bin/service_control restart
```

### <a name="multihoming"></a>Multihoming

Esta verificação determina se o agente está a comunicar para várias áreas de trabalho. Multi-homing não é suportado pelo gerenciamento de atualizações.

### <a name="hybrid-runbook-worker"></a>Função de Trabalho de Runbook Híbrida

Esta verificação verifica se o agente Log Analytics para linux tem o pacote Hybrid Runbook Worker. Este pacote é necessária para a gestão de atualização funcionar.

### <a name="hybrid-runbook-worker-status"></a>Estado de trabalho de Runbook híbrida

Esta verificação torna-se de que a função de trabalho de Runbook híbrida está em execução na máquina. Os seguintes processos devem estar presentes se a função de trabalho de Runbook híbrida está a ser executado corretamente. Para saber mais, consulte a [resolução de problemas do Agente de Análise de Log para o Linux](hybrid-runbook-worker.md#oms-agent-not-running).

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

## <a name="connectivity-checks"></a>Verificações de conectividade

### <a name="general-internet-connectivity"></a>Conectividade internet

Esta verificação certifica-se de que a máquina tem acesso à internet.

### <a name="registration-endpoint"></a>Ponto final do registo

Esta verificação determina se o Trabalhador do Livro Híbrido consegue comunicar corretamente com a Azure Automation o espaço de trabalho Log Analytics.

Configurações de proxy e de firewall têm de permitir o agente de trabalho de Runbook híbrida comunicar com o ponto final do registo. Para abrir uma lista de endereços e portos, consulte [o planeamento da rede para trabalhadores híbridos](../automation-hybrid-runbook-worker.md#network-planning)

### <a name="operations-endpoint"></a>Ponto final de operações

Esta verificação determina se o agente corretamente pode comunicar com o serviço de dados de tempo de execução de tarefa.

Configurações de proxy e de firewall têm de permitir o agente de trabalho de Runbook híbrida para comunicar com o serviço de dados de tempo de execução de tarefa. Para abrir uma lista de endereços e portos, consulte [o planeamento da rede para trabalhadores híbridos](../automation-hybrid-runbook-worker.md#network-planning)

### <a name="log-analytics-endpoint-1"></a>Ponto de extremidade do log Analytics 1

Esta verificação verifica se o seu computador tem acesso aos pontos finais necessários para o agente Log Analytics.

### <a name="log-analytics-endpoint-2"></a>Ponto de extremidade do log Analytics 2

Esta verificação verifica se o seu computador tem acesso aos pontos finais necessários para o agente Log Analytics.

### <a name="log-analytics-endpoint-3"></a>Ponto de extremidade do log Analytics 3

Esta verificação verifica se o seu computador tem acesso aos pontos finais necessários para o agente Log Analytics.

## <a name="troubleshoot-offline"></a>Resolução de problemas offline

Pode utilizar a resolução de problemas offline numa função de trabalho de Runbook híbridas executando o script localmente. O guião python, [update_mgmt_health_check.py](https://gallery.technet.microsoft.com/scriptcenter/Troubleshooting-utility-3bcbefe6) pode ser encontrado no Script Center. Um exemplo da saída deste script é mostrado no exemplo a seguir:

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

## <a name="next-steps"></a>Passos Seguintes

Para resolver problemas adicionais com os seus Trabalhadores híbridos, consulte [Troubleshoot - Trabalhadores híbridos do runbook](hybrid-runbook-worker.md).
