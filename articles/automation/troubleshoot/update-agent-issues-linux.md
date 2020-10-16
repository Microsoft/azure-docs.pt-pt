---
title: Problemas de resolução de problemas problemas de agente de atualização Linux na Azure Automation
description: Este artigo diz como resolver problemas e resolver problemas com o agente de atualização Linux Windows na Gestão de Atualizações.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 12/03/2019
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: f1351b29a0102a374b75d832687d66c3b5572c75
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83680867"
---
# <a name="troubleshoot-linux-update-agent-issues"></a>Resolver problemas do agente de atualização do Linux

Pode haver muitas razões pelas quais a sua máquina não aparece tão pronta (saudável) na Gestão de Atualização. Pode verificar a saúde de um agente do Linux Hybrid Runbook Worker para determinar o problema subjacente. Seguem-se os três estados de prontidão para uma máquina:

* Pronto: O Trabalhador de Runbook Híbrido está implantado e foi visto pela última vez há menos de uma hora.
* Desligado: O Trabalhador de Runbook Híbrido está implantado e foi visto pela última vez há mais de uma hora.
* Não configurado: O Trabalhador de Runbook Híbrido não foi encontrado ou ainda não terminou a sua implantação.

> [!NOTE]
> Pode haver um ligeiro atraso entre o que o portal Azure mostra e o estado atual de uma máquina.

Este artigo discute como executar o resolução de problemas para máquinas Azure a partir do portal Azure e máquinas não-Azure no [cenário offline](#troubleshoot-offline). 

> [!NOTE]
> O script de resolução de problemas não é atualmente encaminhado o tráfego através de um servidor proxy se um estiver configurado.

## <a name="start-the-troubleshooter"></a>Inicie o resolução de problemas

Para as máquinas Azure, selecione o link **de resolução de problemas** sob a coluna **'Actualização's's Readiness** in the portal para abrir a página 'Relatório de Atualização de Resolução de Problemas'. Para máquinas não-Azure, o link leva-o a este artigo. Para resolver problemas com uma máquina não-Azure, consulte as instruções na secção "Resolução de problemas offline".

![Página da lista VM](../media/update-agent-issues-linux/vm-list.png)

> [!NOTE]
> Os cheques requerem que o VM esteja a funcionar. Se o VM não estiver a funcionar, **aparece o VM.**

Na página 'Relatório de Atualização de Resolução de Problemas', selecione **Run Checks** para iniciar o resolução de problemas. O resolução de problemas usa [o comando Run](../../virtual-machines/linux/run-command.md) para executar um script na máquina para verificar as dependências. Quando a resolução de problemas estiver terminada, devolve o resultado dos controlos.

![Página de resolução de problemas](../media/update-agent-issues-linux/troubleshoot-page.png)

Quando os cheques estiverem terminados, os resultados são devolvidos na janela. As secções de verificação fornecem informações sobre o que cada verificação procura.

![Página de verificação de agente de atualização](../media/update-agent-issues-linux/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Verificações de pré-requisitos

### <a name="operating-system"></a>Sistema operativo

O sistema operativo verifica se o Trabalhador de Runbook Híbrido está a executar um dos seguintes sistemas operativos.

|Sistema operativo  |Notas  |
|---------|---------|
|CentOS 6 (x86/x64) e 7 (x64)      | Os agentes do Linux têm de ter acesso a um repositório de atualização. O patching baseado na classificação requer 'yum' para devolver dados de segurança, que o CentOS não tem fora da caixa.         |
|Red Hat Enterprise 6 (x86/x64) e 7 (x64)     | Os agentes do Linux têm de ter acesso a um repositório de atualização.        |
|SUSE Linux Enterprise Server 11 (x86/x64) e 12 (x64)     | Os agentes do Linux têm de ter acesso a um repositório de atualização.        |
|Ubuntu 14.04 LTS, 16.04 LTS e 18.04 LTS (x86/x64)      |Os agentes do Linux têm de ter acesso a um repositório de atualização.         |

## <a name="monitoring-agent-service-health-checks"></a>Controlo do serviço de monitorização dos controlos de saúde

### <a name="log-analytics-agent"></a>Agente do Log Analytics

Esta verificação garante a instalação do agente Log Analytics para o Linux. Para obter instruções sobre como instalá-lo, consulte [instalar o agente para o Linux](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux).

### <a name="log-analytics-agent-status"></a>Estado do agente do Log Analytics

Esta verificação garante que o agente Log Analytics do Linux está em funcionamento. Se o agente não estiver a funcionar, podes executar o seguinte comando para tentar reiniciá-lo. Para obter mais informações sobre a resolução de problemas do agente, consulte os problemas do [Linux - Troubleshoot Hybrid Runbook Worker](hybrid-runbook-worker.md#linux).

```bash
sudo /opt/microsoft/omsagent/bin/service_control restart
```

### <a name="multihoming"></a>Multihoming

Esta verificação determina se o agente está a reportar a vários espaços de trabalho. A Atualização Gestão não suporta multihoming.

### <a name="hybrid-runbook-worker"></a>Função de Trabalho de Runbook Híbrida

Esta verificação verifica se o agente Log Analytics da Linux tem o pacote Híbrido Runbook Worker. Este pacote é necessário para que a Gestão de Atualização funcione. Para saber mais, consulte [o agente do Log Analytics para o Linux não está a funcionar.](hybrid-runbook-worker.md#oms-agent-not-running)

A Update Management descarrega pacotes híbridos de trabalhador runbook do ponto final de operações. Portanto, se o Trabalhador do Runbook Híbrido não estiver em funcionamento e a verificação do ponto final de [operações](#operations-endpoint) falhar, a atualização pode falhar.

### <a name="hybrid-runbook-worker-status"></a>Estatuto de trabalhador de runbook híbrido

Esta verificação garante que o Trabalhador de Runbook Híbrido está a funcionar na máquina. Os processos no exemplo abaixo devem estar presentes se o Trabalhador de Runbook Híbrido estiver a funcionar corretamente.


```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

## <a name="connectivity-checks"></a>Verificações de conectividade

### <a name="general-internet-connectivity"></a>Conectividade geral da internet

Esta verificação garante que a máquina tem acesso à internet.

### <a name="registration-endpoint"></a>Ponto final de inscrição

Esta verificação determina se o Trabalhador de Runbook Híbrido pode comunicar corretamente com a Azure Automation no espaço de trabalho Log Analytics.

As configurações de procuração e firewall devem permitir que o agente híbrido runbook worker comunique com o ponto final de registo. Para que uma lista de endereços e portas abra, consulte [o planeamento da Rede](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="operations-endpoint"></a>Ponto final de operações

Esta verificação determina se o agente Log Analytics pode comunicar corretamente com o Serviço de Dados de Tempo de Trabalho.

As configurações de procuração e firewall devem permitir que o agente híbrido do Trabalhador do Runbook comunique com o Serviço de Dados de Tempo de Trabalho. Para que uma lista de endereços e portas abra, consulte [o planeamento da Rede](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="log-analytics-endpoint-1"></a>Ponto final do Log Analytics 1

Esta verificação verifica se a sua máquina tem acesso aos pontos finais necessários pelo agente Log Analytics.

### <a name="log-analytics-endpoint-2"></a>Log Analytics ponto final 2

Esta verificação verifica se a sua máquina tem acesso aos pontos finais necessários pelo agente Log Analytics.

### <a name="log-analytics-endpoint-3"></a>Log Analytics ponto final 3

Esta verificação verifica se a sua máquina tem acesso aos pontos finais necessários pelo agente Log Analytics.

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>Resolução de problemas offline

Pode utilizar o resolução de problemas offline num Trabalhador De Runbook Híbrido executando o script localmente. O script Python, [update_mgmt_health_check.py,](https://gallery.technet.microsoft.com/scriptcenter/Troubleshooting-utility-3bcbefe6)pode ser encontrado no Script Center. Um exemplo da saída deste script é mostrado no seguinte exemplo:

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

[Problemas com o trabalhador do runbook híbrido.](hybrid-runbook-worker.md)
