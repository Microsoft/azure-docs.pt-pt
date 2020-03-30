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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
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

## <a name="start-the-troubleshooter"></a>Inicie o resolução de problemas

Para máquinas Azure, clique no link **Troubleshoot** sob a coluna **de prontidão** do agente de atualização no portal lança a página do Agente de Atualização de **Resolução** de Problemas. Para máquinas não-Azure, o link leva-o a este artigo. Consulte as instruções offline para filmar uma máquina não Azure.

![página da lista vm](../media/update-agent-issues-linux/vm-list.png)

> [!NOTE]
> Os cheques exigem que o VM esteja a funcionar. Se o VM não estiver a funcionar, é-lhe apresentado um botão para **iniciar o VM**.

Na página do **Agente de Atualização** de Problemas, clique em **Controlos de Execução,** para iniciar o resolução de problemas. O resoluto de problemas usa o [comando run](../../virtual-machines/linux/run-command.md) para executar um script na máquina para verificar as dependências. Quando o resolução de problemas estiver completo, devolve o resultado dos controlos.

![Página de resolução de problemas](../media/update-agent-issues-linux/troubleshoot-page.png)

Quando estiver em conclusão, os resultados são devolvidos na janela. As secções de verificação fornecem informações sobre o que cada cheque procura.

![Atualizar página de verificação de agentes](../media/update-agent-issues-linux/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Verificações de pré-requisitos

### <a name="operating-system"></a>Sistema operativo

A verificação do sistema operativo verifica se o Trabalhador do Livro híbrido está a executar um dos seguintes Sistemas Operativos:

|Sistema operativo  |Notas  |
|---------|---------|
|CentOS 6 (x86/x64) e 7 (x64)      | Os agentes do Linux têm de ter acesso a um repositório de atualização. A correção baseada na classificação requer 'yum' para devolver dados de segurança que o CentOS não tem fora da caixa.         |
|Red Hat Enterprise 6 (x86/x64) e 7 (x64)     | Os agentes do Linux têm de ter acesso a um repositório de atualização.        |
|SUSE Linux Enterprise Server 11 (x86/x64) e 12 (x64)     | Os agentes do Linux têm de ter acesso a um repositório de atualização.        |
|Ubuntu 14.04 LTS, 16.04 LTS e 18.04 LTS (x86/x64)      |Os agentes do Linux têm de ter acesso a um repositório de atualização.         |

## <a name="monitoring-agent-service-health-checks"></a>Controlos de saúde do serviço de agentes de monitorização

### <a name="log-analytics-agent"></a>Agente do Log Analytics

Esta verificação garante que o agente Log Analytics para o Linux está instalado. Para obter instruções sobre como instalá-lo, consulte [Instale o agente para Linux](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux
).

### <a name="log-analytics-agent-status"></a>Estado do agente Log Analytics

Esta verificação garante que o agente Log Analytics do Linux está a funcionar. Se o agente não estiver a funcionar, pode executar o seguinte comando para tentar reiniciá-lo. Para mais informações sobre a resolução de problemas com o agente, consulte [linux Hybrid Runbook worker troubleshooting](hybrid-runbook-worker.md#linux)

```bash
sudo /opt/microsoft/omsagent/bin/service_control restart
```

### <a name="multihoming"></a>Multihoming

Esta verificação determina se o agente está reportando a vários espaços de trabalho. A Multi-homing não é suportada pela Update Management.

### <a name="hybrid-runbook-worker"></a>Função de Trabalho de Runbook Híbrida

Esta verificação verifica se o agente Log Analytics para linux tem o pacote Hybrid Runbook Worker. Este pacote é necessário para que a Atualização funcione.

### <a name="hybrid-runbook-worker-status"></a>Estatuto de Trabalhador de Runbook Híbrido

Esta verificação certifica-se de que o Trabalhador do Livro Híbrido está a funcionar na máquina. Os seguintes processos devem estar presentes se o Trabalhador do Livro híbrido estiver a funcionar corretamente. Para saber mais, consulte a [resolução de problemas do Agente de Análise de Log para o Linux](hybrid-runbook-worker.md#oms-agent-not-running).

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

## <a name="connectivity-checks"></a>Verificações de conectividade

### <a name="general-internet-connectivity"></a>Conectividade geral da Internet

Este cheque garante que a máquina tem acesso à internet.

### <a name="registration-endpoint"></a>Ponto final de inscrição

Esta verificação determina se o Trabalhador do Livro Híbrido consegue comunicar corretamente com a Azure Automation o espaço de trabalho Log Analytics.

As configurações de proxy e firewall devem permitir que o agente Hybrid Runbook Worker comunique com o ponto final de registo. Para abrir uma lista de endereços e portos, consulte [o planeamento da rede para trabalhadores híbridos](../automation-hybrid-runbook-worker.md#network-planning)

### <a name="operations-endpoint"></a>Ponto final de operações

Esta verificação determina se o agente pode comunicar corretamente com o Serviço de Dados de Funcionato de Trabalho.

As configurações de proxy e firewall devem permitir que o agente Hybrid Runbook Worker se comunique com o Serviço de Dados do Tempo de Execução de Emprego. Para abrir uma lista de endereços e portos, consulte [o planeamento da rede para trabalhadores híbridos](../automation-hybrid-runbook-worker.md#network-planning)

### <a name="log-analytics-endpoint-1"></a>Log Analytics endpoint 1

Esta verificação verifica que a sua máquina tem acesso aos pontos finais necessários pelo agente Log Analytics.

### <a name="log-analytics-endpoint-2"></a>Log Analytics endpoint 2

Esta verificação verifica que a sua máquina tem acesso aos pontos finais necessários pelo agente Log Analytics.

### <a name="log-analytics-endpoint-3"></a>Log Analytics endpoint 3

Esta verificação verifica que a sua máquina tem acesso aos pontos finais necessários pelo agente Log Analytics.

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>Resolução de problemas offline

Você pode usar o resoludor de problemas offline em um Hybrid Runbook Worker executando o script localmente. O guião python, [update_mgmt_health_check.py](https://gallery.technet.microsoft.com/scriptcenter/Troubleshooting-utility-3bcbefe6) pode ser encontrado no Script Center. Um exemplo da saída deste script é mostrado no seguinte exemplo:

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

Para resolver problemas adicionais com os seus Trabalhadores híbridos, consulte [Troubleshoot - Trabalhadores híbridos do runbook](hybrid-runbook-worker.md).
