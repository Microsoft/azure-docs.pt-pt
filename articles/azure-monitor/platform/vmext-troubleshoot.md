---
title: Extensão De Slog Azure Log Analytics VM no Monitor Azure  Microsoft Docs
description: Descreva os sintomas, causas e resolução de problemas mais comuns com a extensão de VM do Log Analytics para Windows e VMs do Linux do Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/06/2019
ms.openlocfilehash: 88d76fc0c215653cf732ba7b827d82187d738fd9
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77658477"
---
# <a name="troubleshooting-the-log-analytics-vm-extension-in-azure-monitor"></a>Resolução de problemas da extensão VM log analytics no Monitor Azure
Este artigo disponibiliza ajuda a resolver problemas de erros que poderá deparar-se com a extensão de VM do Log Analytics para Windows e Linux máquinas de virtuais em execução no Microsoft Azure e sugere possíveis soluções para resolvê-los.

Para verificar o estado da extensão, execute os seguintes passos no portal do Azure.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. No portal do Azure, clique em **All services** (Todos os serviços). Na lista de recursos, digite **máquinas virtuais.** À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **máquinas Virtuais**.
3. Na sua lista de máquinas virtuais, localize e selecione-o.
3. Na máquina virtual, clique em **Extensões**.
4. Na lista, verifique se a extensão do Log Analytics está ativada ou não.  Para o Linux, o agente está listado como **OMSAgentforLinux** e para windows, o agente está listado como **MicrosoftMonitoringAgent**.

   ![Vista de extensão VM](./media/vmext-troubleshoot/log-analytics-vmview-extensions.png)

4. Clique na extensão para ver os detalhes. 

   ![Detalhes da extensão de VM](./media/vmext-troubleshoot/log-analytics-vmview-extensiondetails.png)

## <a name="troubleshooting-azure-windows-vm-extension"></a>Resolução de problemas de extensão de VM do Windows Azure

Se a extensão VM do *Microsoft Monitoring Agent* não estiver a instalar ou a reportar, pode executar os seguintes passos para resolver o problema.

1. Verifique se o agente Azure VM está instalado e funciona corretamente utilizando os passos em [KB 2965986](https://support.microsoft.com/kb/2965986#mt1).
   * Também pode rever o ficheiro de registo do agente VM `C:\WindowsAzure\logs\WaAppAgent.log`
   * Se o registo não existir, o agente da VM não está instalado.
   * [Instale o Agente VM Azure](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Reveja os ficheiros de registo de extensão VM do Microsoft Monitoring Agent em `C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent`
3. Certifique-se de que a máquina virtual pode executar scripts do PowerShell
4. Certifique-se de que as permissões no C:\Windows\temp ainda não foram alteradas
5. Veja o estado do Agente de Monitorização da Microsoft digitando o seguinte numa janela elevada da PowerShell na máquina virtual `(New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`
6. Reveja os ficheiros de registo do Microsoft Monitoring Agent em `C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs`

Para mais informações, consulte [as extensões do Windows de resolução de problemas](../../virtual-machines/extensions/oms-windows.md).

## <a name="troubleshooting-linux-vm-extension"></a>Resolução de problemas de extensão de VM do Linux
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 
Se o agente Log Analytics para a extensão *VM linux* não estiver a instalar ou reportar, pode executar os seguintes passos para resolver o problema.

1. Se o estado de extensão for *desconhecido,* verifique se o agente Azure VM está instalado e funciona corretamente, revendo o ficheiro de registo do agente VM `/var/log/waagent.log`
   * Se o registo não existir, o agente da VM não está instalado.
   * [Instale o Agente Azure VM em VMs Linux](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Para outros estados pouco saudáveis, reveja o agente Log Analytics para ficheiros de registode extensões De VM Linux em `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log` e `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log`
3. Se o estado de extensão for saudável, mas os dados não estão a ser enviados, reveja o agente Log Analytics para ficheiros de log Linux em `/var/opt/microsoft/omsagent/log/omsagent.log`

Para mais informações, consulte [as extensões do Linux de resolução de problemas.](../../virtual-machines/extensions/oms-linux.md)

## <a name="next-steps"></a>Passos seguintes

Para obter orientações adicionais relacionadas com o agente Log Analytics para o Linux hospedado em computadores fora do Azure, consulte [Troubleshoot Azure Log Analytics Linux Agent](agent-linux-troubleshoot.md).  
