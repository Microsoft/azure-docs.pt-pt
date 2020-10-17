---
title: Resolução de problemas Azure Log Analytics VM Extensão
description: Descreva os sintomas, as causas e a resolução para os problemas mais comuns com a extensão VM do Log Analytics para Windows e Linux Azure VMs.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/06/2019
ms.openlocfilehash: 024fa2f86890c6e8e791b5cf66a4e67328f62f63
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92143887"
---
# <a name="troubleshooting-the-log-analytics-vm-extension-in-azure-monitor"></a>Resolver problemas da extensão de VM do Log Analytics no Azure Monitor
Este artigo fornece falhas de resolução de problemas que pode experimentar com a extensão VM do Log Analytics para máquinas virtuais Windows e Linux em funcionamento no Microsoft Azure, e sugere possíveis soluções para resolvê-los.

Para verificar o estado da extensão, execute os seguintes passos a partir do portal Azure.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No portal do Azure, clique em **Todos os serviços**. Na lista de recursos, escreva **máquinas virtuais.** À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **Máquinas virtuais**.
3. Na sua lista de máquinas virtuais, encontre-as e selecione-as.
3. Na máquina virtual, clique em **Extensões**.
4. A partir da lista, verifique se a extensão Log Analytics está ativada ou não.  Para o Linux, o agente está listado como **OMSAgentforLinux** e para o Windows, o agente está listado como **MicrosoftMonitoringAgent**.

   ![Vista de extensão VM](./media/vmext-troubleshoot/log-analytics-vmview-extensions.png)

4. Clique na extensão para ver detalhes. 

   ![Detalhes da extensão VM](./media/vmext-troubleshoot/log-analytics-vmview-extensiondetails.png)

## <a name="troubleshooting-azure-windows-vm-extension"></a>Extensão Azure Windows VM de resolução de problemas

Se a extensão VM *do Agente de Monitorização* da Microsoft não estiver a instalar ou reportar, pode executar os seguintes passos para resolver o problema.

1. Verifique se o agente Azure VM está instalado e funciona corretamente utilizando os passos em [KB 2965986](https://support.microsoft.com/kb/2965986#mt1).
   * Também pode rever o ficheiro de registo de agente VM `C:\WindowsAzure\logs\WaAppAgent.log`
   * Se o registo não existir, o agente VM não está instalado.
   * [Instale o Agente Azure VM](../learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Reveja os ficheiros de registo de extensão do Agente de Monitorização da Microsoft em `C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent`
3. Certifique-se de que a máquina virtual pode executar scripts PowerShell
4. Garantir que as permissões em C:\Windows\temp não foram alteradas
5. Ver o estado do Agente de Monitorização da Microsoft digitando o seguinte numa janela PowerShell elevada na máquina virtual `(New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`
6. Reveja os ficheiros de registo de configuração do Agente de Monitorização da Microsoft `C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs`

Para obter mais informações, consulte [as extensões do Windows que resolvam problemas.](../../virtual-machines/extensions/oms-windows.md)

## <a name="troubleshooting-linux-vm-extension"></a>Resolução de problemas extensão Linux VM
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 
Se o agente Log Analytics para a extensão *Linux* VM não estiver a instalar ou reportar, pode executar os seguintes passos para resolver o problema.

1. Se o estado da extensão for *desconhecido,* verifique se o agente Azure VM está instalado e funciona corretamente, revendo o ficheiro de registo do agente VM `/var/log/waagent.log`
   * Se o registo não existir, o agente VM não está instalado.
   * [Instale o Agente VM Azure em VMs Linux](../../virtual-machines/extensions/agent-linux.md#installation)
2. Para outros estados insalubres, reveja o agente Log Analytics para ficheiros de registos de extensão Linux VM `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log` e `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log`
3. Se o estado de extensão for saudável, mas os dados não estão a ser carregados, reveja o agente Log Analytics para ficheiros de registo Linux `/var/opt/microsoft/omsagent/log/omsagent.log`

Para obter mais informações, consulte [as extensões linux de resolução de problemas.](../../virtual-machines/extensions/oms-linux.md)

## <a name="next-steps"></a>Passos seguintes

Para obter orientações adicionais de resolução de problemas relacionadas com o agente Log Analytics para Linux hospedado em computadores fora de Azure, consulte [o Relatório Linux do Log Analytics de Troubleshoot Azure Log.](agent-linux-troubleshoot.md)