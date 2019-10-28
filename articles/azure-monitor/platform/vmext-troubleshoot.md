---
title: Solucionar problemas de extensão de VM do Azure Log Analytics no Azure Monitor | Microsoft Docs
description: Descreva os sintomas, as causas e a resolução para os problemas mais comuns com a extensão de VM Log Analytics para VMs do Azure para Windows e Linux.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 06/06/2019
ms.openlocfilehash: 9ec0d5036632c575415a7de19b9ea35eb2a28118
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72931936"
---
# <a name="troubleshooting-the-log-analytics-vm-extension-in-azure-monitor"></a>Solucionando problemas da extensão de VM Log Analytics no Azure Monitor
Este artigo fornece ajuda para solucionar erros que podem ocorrer com a extensão de VM Log Analytics para máquinas virtuais Windows e Linux em execução no Microsoft Azure e sugere possíveis soluções para resolvê-las.

Para verificar o status da extensão, execute as etapas a seguir no portal do Azure.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. No portal do Azure, clique em **Todos os serviços**. Na lista de recursos, digite **máquinas virtuais**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **máquinas virtuais**.
3. Na lista de máquinas virtuais, localize e selecione-a.
3. Na máquina virtual, clique em **extensões**.
4. Na lista, verifique se a extensão de Log Analytics está habilitada ou não.  Para o Linux, o agente é listado como **OMSAgentforLinux** e para Windows, o agente é listado como **extensão microsoftmonitoringagent**.

   ![Exibição de extensão de VM](./media/vmext-troubleshoot/log-analytics-vmview-extensions.png)

4. Clique na extensão para exibir os detalhes. 

   ![Detalhes da extensão da VM](./media/vmext-troubleshoot/log-analytics-vmview-extensiondetails.png)

## <a name="troubleshooting-azure-windows-vm-extension"></a>Solucionando problemas da extensão de VM do Windows do Azure

Se a extensão de VM *Microsoft Monitoring Agent* não estiver instalando ou relatando, você poderá executar as etapas a seguir para solucionar o problema.

1. Verifique se o agente de VM do Azure está instalado e funcionando corretamente usando as etapas em [KB 2965986](https://support.microsoft.com/kb/2965986#mt1).
   * Você também pode examinar o arquivo de log do agente de VM `C:\WindowsAzure\logs\WaAppAgent.log`
   * Se o log não existir, o agente de VM não será instalado.
   * [Instalar o agente de VM do Azure](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Examine os arquivos de log da extensão de VM Microsoft Monitoring Agent no `C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent`
3. Verifique se a máquina virtual pode executar scripts do PowerShell
4. Verifique se as permissões no C:\Windows\temp não foram alteradas
5. Exiba o status do Microsoft Monitoring Agent digitando o seguinte em uma janela do PowerShell com privilégios elevados na máquina virtual `(New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`
6. Examine os arquivos de log da Microsoft Monitoring Agent instalação no `C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs`

Para obter mais informações, consulte [Solucionando problemas de extensões do Windows](../../virtual-machines/extensions/oms-windows.md).

## <a name="troubleshooting-linux-vm-extension"></a>Solucionando problemas de extensão de VM do Linux
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 
Se o *agente de log Analytics para extensão de VM do Linux* não estiver instalando ou relatando, você poderá executar as etapas a seguir para solucionar o problema.

1. Se o status da extensão for *desconhecido* , verifique se o agente de VM do Azure está instalado e funcionando corretamente examinando o arquivo de log do agente de VM `/var/log/waagent.log`
   * Se o log não existir, o agente de VM não será instalado.
   * [Instalar o agente de VM do Azure em VMs Linux](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Para outros status não íntegros, examine os arquivos do agente de Log Analytics para os logs de extensão de VM do Linux em `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log` e `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log`
3. Se o status da extensão for íntegro, mas os dados não estiverem sendo carregados, examine os arquivos de log do Log Analytics Agent para Linux no `/var/opt/microsoft/omsagent/log/omsagent.log`

Para obter mais informações, consulte [Solucionando problemas de extensões do Linux](../../virtual-machines/extensions/oms-linux.md).

## <a name="next-steps"></a>Passos seguintes

Para obter diretrizes adicionais de solução de problemas relacionadas ao agente de Log Analytics para Linux hospedado em computadores fora do Azure, consulte [solucionar problemas do agente do azure log Analytics Linux](agent-linux-troubleshoot.md).  
