---
title: Compreender os resultados de verificação de agente do Linux no gerenciamento de atualizações do Azure
description: Saiba como resolver problemas com o agente de gestão de atualizações.
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 04/22/2019
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: 075cf254dbf7c5d03f1afac64315f6c6f773159c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60597793"
---
# <a name="understand-the-linux-agent-check-results-in-update-management"></a>Compreender os resultados de verificação de agente do Linux no gerenciamento de atualizações

Podem existir muitos motivos pelos quais sua máquina não estiver visível **pronto** na gestão de atualizações. Gerenciamento de atualizações, pode verificar o estado de funcionamento de um agente de função de trabalho híbrida para determinar o problema subjacente. Este artigo descreve como executar a resolução de problemas para as máquinas do Azure a partir do portal do Azure e máquinas não Azure no [cenário offline](#troubleshoot-offline).

A lista a seguir são os Estados de preparação de três uma máquina pode estar em:

* **Pronto** -o agente de atualização é implementado e foi-visto pela última vez há menos de 1 hora.
* **Desligado** -o agente de atualização é implementado e foi-visto pela última vez há mais de 1 hora.
* **Não configurado** -o agente de atualização não for encontrado ou não foi concluída de integração.

> [!NOTE]
> Pode haver um ligeiro atraso entre o que mostra o portal do Azure e o estado atual da máquina.

## <a name="start-the-troubleshooter"></a>Iniciar a resolução de problemas

Para máquinas do Azure, ao clicar o **resolução de problemas** ligação sob o **preparação do agente de atualização** coluna em lançamentos de portais a **resolver problemas de agente de atualização** página. Para as máquinas não Azure, a ligação leva a este artigo. Veja as instruções de offline para resolver problemas de uma máquina não Azure.

![página de lista de VM](../media/update-agent-issues-linux/vm-list.png)

> [!NOTE]
> As verificações de exigem que a VM estar em execução. Se a VM não está em execução, verá um botão para **iniciar a VM**.

Sobre o **resolver problemas de agente de atualização** página, clique em **executar verifica**, para iniciar a resolução de problemas. Utiliza a resolução de problemas [execute o comando](../../virtual-machines/linux/run-command.md) para executar um script no computador para verificar as dependências que tenha o agente. Quando a resolução de problemas estiver concluída, ela retorna o resultado das verificações.

![Resolver problemas de página](../media/update-agent-issues-linux/troubleshoot-page.png)

Quando terminar, os resultados são devolvidos na janela. As secções de verificação fornecem informações sobre o que cada verificação de que está procurando.

![Página de verificações de atualização de agente](../media/update-agent-issues-linux/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Verificações de pré-requisitos

### <a name="operating-system"></a>Sistema operativo

A verificação do sistema operacional, verifica se a função de trabalho de Runbook híbrida está a executar um dos seguintes sistemas operativos:

|Sistema operativo  |Notas  |
|---------|---------|
|CentOS 6 (x86/x64) e 7 (x64)      | Os agentes do Linux têm de ter acesso a um repositório de atualização. Com base na classificação de aplicação de patches requer "yum" para devolver dados de segurança que não têm a CentOS prontos a utilizar.         |
|Red Hat Enterprise 6 (x86/x64) e 7 (x64)     | Os agentes do Linux têm de ter acesso a um repositório de atualização.        |
|SUSE Linux Enterprise Server 11 (x86/x64) e 12 (x64)     | Os agentes do Linux têm de ter acesso a um repositório de atualização.        |
|Ubuntu 14.04 LTS e 16.04 LTS e 18.04 LTS (x86/x64)      |Os agentes do Linux têm de ter acesso a um repositório de atualização.         |

## <a name="monitoring-agent-service-health-checks"></a>Monitorização verificações de estado de funcionamento do serviço de agente

### <a name="oms-agent"></a>Agente do OMS

Esta verificação garante que o agente do OMS para Linux está instalado. Para obter instruções sobre como instalá-lo, consulte [instalar o agente para Linux](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux
).

### <a name="oms-agent-status"></a>Estado do agente do OMS

Esta verificação garante que o agente do OMS para Linux está em execução. Se o agente não está em execução, pode executar o seguinte comando para tentar reiniciá-lo. Para obter mais informações sobre o agente de resolução de problemas, consulte [resolução de problemas de trabalho de Runbook híbrida de Linux](hybrid-runbook-worker.md#linux)

```bash
sudo /opt/microsoft/omsagent/bin/service_control restart
```

### <a name="multihoming"></a>Multihoming

Esta verificação determina se o agente está a comunicar para várias áreas de trabalho. Multi-homing não é suportado pelo gerenciamento de atualizações.

### <a name="hybrid-runbook-worker"></a>Função de Trabalho de Runbook Híbrida

Esta verificação verifica se o agente do OMS para Linux tem o pacote de trabalho de Runbook híbrida. Este pacote é necessária para a gestão de atualização funcionar.

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

Esta verificação determina se o agente corretamente pode comunicar com o serviço de agente.

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

## <a name="troubleshoot-offline"></a>Resolução de problemas offline

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

## <a name="next-steps"></a>Passos Seguintes

Para resolver problemas adicionais com suas funções de trabalho de Runbook híbridas, consulte [resolução de problemas - os Runbook Workers híbridos](hybrid-runbook-worker.md)

