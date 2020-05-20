---
title: Problemas de resolução de problemas de problemas com problemas de agente de atualização linux na Azure Automation
description: Este artigo diz como resolver problemas com o agente de atualização do Linux Windows em Gestão de Atualizações.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 12/03/2019
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: f1351b29a0102a374b75d832687d66c3b5572c75
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680867"
---
# <a name="troubleshoot-linux-update-agent-issues"></a>Problemas problemas com problemas de problemas com problemas de problemas com problemas de agente de atualização linux

Pode haver muitas razões pelas quais a sua máquina não aparece tão pronta (saudável) na Gestão de Atualizações. Pode verificar a saúde de um agente do Trabalhador do Livro de Corridas Híbrido sinuoso linux para determinar o problema subjacente. Seguem-se os três estados de prontidão para uma máquina:

* Ready: The Hybrid Runbook Worker está implantado e foi visto pela última vez há menos de uma hora.
* Desligado: O Trabalhador híbrido do livro de corridas está implantado e foi visto pela última vez há mais de uma hora.
* Não configurado: O Trabalhador híbrido do runbook não é encontrado ou ainda não terminou a implantação.

> [!NOTE]
> Pode haver um ligeiro atraso entre o que o portal Azure mostra e o estado atual de uma máquina.

Este artigo discute como executar o resolução de problemas para máquinas Azure a partir do portal Azure e máquinas não-Azure no [cenário offline](#troubleshoot-offline). 

> [!NOTE]
> O script de resolução de problemas atualmente não encaminha o tráfego através de um servidor proxy se um estiver configurado.

## <a name="start-the-troubleshooter"></a>Inicie o resolução de problemas

Para as máquinas Azure, selecione o link de **resolução** de problemas sob a coluna **de prontidão** do agente de atualização no portal para abrir a página do Agente de Atualização de Resolução de Problemas. Para máquinas não-Azure, o link leva-o a este artigo. Para resolver problemas com uma máquina não-Azure, consulte as instruções na secção "Troubleshoot offline".

![Página da lista VM](../media/update-agent-issues-linux/vm-list.png)

> [!NOTE]
> Os cheques exigem que o VM esteja a funcionar. Se o VM não estiver a funcionar, inicie a corrente **do VM.**

Na página do Agente de Atualização de Problemas, selecione **'Verificação de execução'** para iniciar o resolução de problemas. O resoluto de problemas usa o [comando run](../../virtual-machines/linux/run-command.md) para executar um script na máquina para verificar as dependências. Quando o resoluto de problemas estiver terminado, devolve o resultado dos cheques.

![Página de resolução de problemas](../media/update-agent-issues-linux/troubleshoot-page.png)

Quando os cheques estiverem terminados, os resultados são devolvidos na janela. As secções de verificação fornecem informações sobre o que cada cheque procura.

![Atualizar página de verificação de agentes](../media/update-agent-issues-linux/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Verificações de pré-requisitos

### <a name="operating-system"></a>Sistema operativo

A verificação do sistema operativo verifica se o Trabalhador do Livro híbrido está a executar um dos seguintes sistemas operativos.

|Sistema operativo  |Notas  |
|---------|---------|
|CentOS 6 (x86/x64) e 7 (x64)      | Os agentes do Linux têm de ter acesso a um repositório de atualização. A correção baseada na classificação requer 'yum' para devolver dados de segurança, que o CentOS não tem fora da caixa.         |
|Red Hat Enterprise 6 (x86/x64) e 7 (x64)     | Os agentes do Linux têm de ter acesso a um repositório de atualização.        |
|SUSE Linux Enterprise Server 11 (x86/x64) e 12 (x64)     | Os agentes do Linux têm de ter acesso a um repositório de atualização.        |
|Ubuntu 14.04 LTS, 16.04 LTS e 18.04 LTS (x86/x64)      |Os agentes do Linux têm de ter acesso a um repositório de atualização.         |

## <a name="monitoring-agent-service-health-checks"></a>Controlos de saúde do serviço de agentes de monitorização

### <a name="log-analytics-agent"></a>Agente do Log Analytics

Esta verificação garante que o agente Log Analytics para o Linux está instalado. Para obter instruções sobre como instalá-lo, consulte [Instale o agente para Linux](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux).

### <a name="log-analytics-agent-status"></a>Estado do agente Log Analytics

Esta verificação garante que o agente Log Analytics do Linux está a funcionar. Se o agente não estiver a funcionar, pode executar o seguinte comando para tentar reiniciá-lo. Para obter mais informações sobre problemas de resolução de problemas com o agente, consulte [os problemas do Linux - Troubleshoot Hybrid Runbook Worker](hybrid-runbook-worker.md#linux).

```bash
sudo /opt/microsoft/omsagent/bin/service_control restart
```

### <a name="multihoming"></a>Multihoming

Esta verificação determina se o agente está reportando a vários espaços de trabalho. A Atualização de Gestão não suporta multihoming.

### <a name="hybrid-runbook-worker"></a>Função de Trabalho de Runbook Híbrida

Esta verificação verifica se o agente Log Analytics para linux tem o pacote Hybrid Runbook Worker. Este pacote é necessário para que a Atualização funcione. Para saber mais, consulte o [agente log Analytics para o Linux não está a funcionar.](hybrid-runbook-worker.md#oms-agent-not-running)

Update Management descarrega pacotes Híbridos Runbook Worker a partir do ponto final de operações. Portanto, se o Trabalhador do Livro de Execução Híbrido não estiver em execução e a verificação final de [operações](#operations-endpoint) falhar, a atualização pode falhar.

### <a name="hybrid-runbook-worker-status"></a>Estatuto de Trabalhador de Runbook Híbrido

Esta verificação certifica-se de que o Trabalhador do Livro Híbrido está a funcionar na máquina. Os processos no exemplo abaixo devem estar presentes se o Trabalhador do Livro Híbrido estiver a funcionar corretamente.


```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

## <a name="connectivity-checks"></a>Verificações de conectividade

### <a name="general-internet-connectivity"></a>Conectividade geral da Internet

Este cheque garante que a máquina tem acesso à internet.

### <a name="registration-endpoint"></a>Ponto final de inscrição

Esta verificação determina se o Trabalhador do Livro Híbrido consegue comunicar corretamente com a Automação Azure no espaço de trabalho log Analytics.

As configurações de proxy e firewall devem permitir que o agente Hybrid Runbook Worker comunique com o ponto final de registo. Para que uma lista de endereços e portas seja aberta, consulte [o planeamento da rede.](../automation-hybrid-runbook-worker.md#network-planning)

### <a name="operations-endpoint"></a>Ponto final de operações

Esta verificação determina se o agente Log Analytics pode comunicar corretamente com o Serviço de Dados do Funcionador de Emprego.

As configurações de proxy e firewall devem permitir que o agente Hybrid Runbook Worker se comunique com o Serviço de Dados do Tempo de Execução de Emprego. Para que uma lista de endereços e portas seja aberta, consulte [o planeamento da rede.](../automation-hybrid-runbook-worker.md#network-planning)

### <a name="log-analytics-endpoint-1"></a>Log Analytics endpoint 1

Esta verificação verifica que a sua máquina tem acesso aos pontos finais necessários pelo agente Log Analytics.

### <a name="log-analytics-endpoint-2"></a>Log Analytics endpoint 2

Esta verificação verifica que a sua máquina tem acesso aos pontos finais necessários pelo agente Log Analytics.

### <a name="log-analytics-endpoint-3"></a>Log Analytics endpoint 3

Esta verificação verifica que a sua máquina tem acesso aos pontos finais necessários pelo agente Log Analytics.

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>Resolução de problemas offline

Você pode usar o resoludor de problemas offline em um Hybrid Runbook Worker executando o script localmente. O guião python, [update_mgmt_health_check.py,](https://gallery.technet.microsoft.com/scriptcenter/Troubleshooting-utility-3bcbefe6)pode ser encontrado no Script Center. Um exemplo da saída deste script é mostrado no seguinte exemplo:

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

[Problemas problemas híbridos do trabalhador](hybrid-runbook-worker.md)do livro de corridas.
