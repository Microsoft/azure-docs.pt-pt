---
title: Resolver problemas de extensão de VM do Azure Log Analytics no Azure Monitor | Documentos da Microsoft
description: Descreva os sintomas, causas e resolução de problemas mais comuns com a extensão de VM do Log Analytics para Windows e VMs do Linux do Azure.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/08/2018
ms.author: magoedte
ms.openlocfilehash: 17a52cf17c62c930dc3922ded48d659f56016688
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370504"
---
# <a name="troubleshooting-the-log-analytics-vm-extension-in-azure-monitor"></a>Resolução de problemas a extensão de VM do Log Analytics no Azure Monitor
Este artigo disponibiliza ajuda a resolver problemas de erros que poderá deparar-se com a extensão de VM do Log Analytics para Windows e Linux máquinas de virtuais em execução no Microsoft Azure e sugere possíveis soluções para resolvê-los.

Para verificar o estado da extensão, execute os seguintes passos no portal do Azure.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. No portal do Azure, clique em **All services** (Todos os serviços). Na lista de recursos, escreva **máquinas virtuais**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **máquinas virtuais**.
3. Na sua lista de máquinas virtuais, localize e selecione-o.
3. Na máquina virtual, clique em **extensões**.
4. Na lista, verifique se a extensão do Log Analytics está ativada ou não.  Para o Linux, o agente está listado como **OMSAgentforLinux** e para Windows, o agente está listado como **MicrosoftMonitoringAgent**.

   ![Vista de extensão VM](./media/vmext-troubleshoot/log-analytics-vmview-extensions.png)

4. Clique na extensão para ver os detalhes. 

   ![Detalhes da extensão de VM](./media/vmext-troubleshoot/log-analytics-vmview-extensiondetails.png)

## <a name="troubleshooting-azure-windows-vm-extension"></a>Resolução de problemas de extensão de VM do Windows Azure

Se o *Microsoft Monitoring Agent* extensão de VM não está a instalar ou relatórios, pode realizar os seguintes passos para resolver o problema.

1. Verifique se o agente de VM do Azure está instalado e a funcionar corretamente ao utilizar os passos em [KB 2965986](https://support.microsoft.com/kb/2965986#mt1).
   * Também pode rever o ficheiro de registo do agente VM `C:\WindowsAzure\logs\WaAppAgent.log`
   * Se o registo não existir, o agente da VM não está instalado.
   * [Instalar o agente da VM do Azure](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Confirme que a tarefa de heartbeat de extensão do Microsoft Monitoring Agent está em execução com os seguintes passos:
   * Inicie sessão na máquina virtual
   * Abra o agendador de tarefas e localize o `update_azureoperationalinsight_agent_heartbeat` tarefas
   * Confirmar a tarefa está ativada e está em execução a cada minuto
   * Verifique o ficheiro de registo de heartbeat `C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\heartbeat.log`
3. Reveja os ficheiros de registo da extensão de VM de agente de monitorização da Microsoft em `C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent`
4. Certifique-se de que a máquina virtual pode executar scripts do PowerShell
5. Certifique-se de que as permissões no C:\Windows\temp ainda não foram alteradas
6. Ver o estado do Microsoft Monitoring Agent, escrevendo o seguinte numa janela elevada do PowerShell na máquina virtual `  (New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`
7. Reveja os ficheiros de registo de configuração do Microsoft Monitoring Agent no `C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs`

Para obter mais informações, consulte [resolução de problemas de extensões do Windows](../../virtual-machines/extensions/oms-windows.md).

## <a name="troubleshooting-linux-vm-extension"></a>Resolução de problemas de extensão de VM do Linux
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 
Se o *agente do Log Analytics para Linux* extensão de VM não está a instalar ou relatórios, pode realizar os seguintes passos para resolver o problema.

1. Se o estado da extensão *desconhecido* Verifique se o agente de VM do Azure está instalado e a funcionar corretamente ao rever o ficheiro de registo do agente VM `/var/log/waagent.log`
   * Se o registo não existir, o agente da VM não está instalado.
   * [Instalar o agente da VM do Azure em VMs do Linux](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Para outros Estados de mau estado de funcionamento, reveja o agente Log Analytics para ficheiros de registo de extensão de VM do Linux `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log` e `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log`
3. Se o estado da extensão está em bom estado, mas não estão a ser carregados dados reveja o agente do Log Analytics para ficheiros de registo do Linux no `/var/opt/microsoft/omsagent/log/omsagent.log`

Para obter mais informações, consulte [resolução de problemas de extensões de Linux](../../virtual-machines/extensions/oms-linux.md).

## <a name="next-steps"></a>Passos Seguintes

Para orientações adicionais de resolução de problemas relacionados com o agente do Log Analytics para o Linux alojada em computadores fora do Azure, consulte [resolver problemas relacionados com o Azure Log Analytics Linux Agent](agent-linux-troubleshoot.md).  
