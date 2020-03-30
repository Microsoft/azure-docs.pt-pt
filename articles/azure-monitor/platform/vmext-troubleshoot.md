---
title: Extensão VM de Log Azure
description: Descreva os sintomas, causas e resolução para os problemas mais comuns com a extensão VM log Analytics para Windows e VMs Linux Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/06/2019
ms.openlocfilehash: e16531484505f055c1383aff5adb40518719d98a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80054587"
---
# <a name="troubleshooting-the-log-analytics-vm-extension-in-azure-monitor"></a>Resolver problemas da extensão de VM do Log Analytics no Azure Monitor
Este artigo fornece erros de resolução de problemas que poderá experimentar com a extensão VM de Log Analytics para máquinas virtuais Windows e Linux em execução no Microsoft Azure, e sugere possíveis soluções para os resolver.

Para verificar o estado da extensão, execute os seguintes passos do portal Azure.

1. Assine no [portal Azure.](https://portal.azure.com)
2. No portal do Azure, clique em **Todos os serviços**. Na lista de recursos, digite **máquinas virtuais.** À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **Máquinas virtuais**.
3. Na sua lista de máquinas virtuais, encontre-a e selecione-a.
3. Na máquina virtual, clique em **Extensões**.
4. A partir da lista, verifique se a extensão Log Analytics está ativada ou não.  Para o Linux, o agente está listado como **OMSAgentforLinux** e para windows, o agente está listado como **MicrosoftMonitoringAgent**.

   ![Vista de extensão VM](./media/vmext-troubleshoot/log-analytics-vmview-extensions.png)

4. Clique na extensão para ver detalhes. 

   ![Detalhes da extensão vm](./media/vmext-troubleshoot/log-analytics-vmview-extensiondetails.png)

## <a name="troubleshooting-azure-windows-vm-extension"></a>Extensão De resolução de problemas Do Windows VM

Se a extensão VM do *Microsoft Monitoring Agent* não estiver a instalar ou a reportar, pode executar os seguintes passos para resolver o problema.

1. Verifique se o agente Azure VM está instalado e funciona corretamente utilizando os passos em [KB 2965986](https://support.microsoft.com/kb/2965986#mt1).
   * Também pode rever o ficheiro de registo do agente VM`C:\WindowsAzure\logs\WaAppAgent.log`
   * Se o registo não existir, o agente VM não está instalado.
   * [Instale o Agente VM Azure](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Reveja os ficheiros de registo de extensão vm do agente de monitorização da Microsoft em`C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent`
3. Certifique-se de que a máquina virtual pode executar scripts PowerShell
4. Garantir permissões em C:\Windows\temp não foram alterados
5. Veja o estado do Agente de Monitorização da Microsoft digitando o seguinte numa janela elevada da PowerShell na máquina virtual`(New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`
6. Reveja os ficheiros de registo do Microsoft Monitoring Agent`C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs`

Para mais informações, consulte [as extensões do Windows de resolução de problemas](../../virtual-machines/extensions/oms-windows.md).

## <a name="troubleshooting-linux-vm-extension"></a>Extensão de Linux VM de resolução de problemas
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 
Se o agente Log Analytics para a extensão *VM linux* não estiver a instalar ou reportar, pode executar os seguintes passos para resolver o problema.

1. Se o estado de extensão for *desconhecido,* verifique se o agente Azure VM está instalado e funciona corretamente, revendo o ficheiro de registo do agente VM`/var/log/waagent.log`
   * Se o registo não existir, o agente VM não está instalado.
   * [Instale o Agente Azure VM em VMs Linux](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Para outros estados pouco saudáveis, reveja o agente Log `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log` Analytics para ficheiros de registo de extensão De VM Linux e`/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log`
3. Se o estado de extensão for saudável, mas os dados não estão a ser carregados, reveja o agente Log Analytics para ficheiros de log linux em`/var/opt/microsoft/omsagent/log/omsagent.log`

Para mais informações, consulte [as extensões do Linux de resolução de problemas.](../../virtual-machines/extensions/oms-linux.md)

## <a name="next-steps"></a>Passos seguintes

Para obter orientações adicionais relacionadas com o agente Log Analytics para o Linux hospedado em computadores fora do Azure, consulte [Troubleshoot Azure Log Analytics Linux Agent](agent-linux-troubleshoot.md).  
