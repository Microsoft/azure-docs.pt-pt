---
title: Resolução de problemas VM insights saúde dos hóspedes (pré-visualização)
description: Descreve os passos de resolução de problemas que pode tomar quando tem problemas com a saúde dos conhecimentos VM.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/25/2021
ms.openlocfilehash: 834c70e02ab25fa6dcadb5f6c997be09aaf5e353
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105932782"
---
# <a name="troubleshoot-vm-insights-guest-health-preview"></a>Resolução de problemas VM insights saúde dos hóspedes (pré-visualização)
Este artigo descreve medidas de resolução de problemas que pode tomar quando tem problemas com a saúde dos conhecimentos VM.


## <a name="upgrade-available-message-is-still-displayed-after-upgrading-guest-health"></a>A mensagem disponível de upgrade ainda é exibida após atualizar a saúde dos hóspedes 

- Verifique se a VM está a funcionar no Azure global. Os servidores ativados pelo Arco ainda não estão suportados.
- Verifique se a versão da região e do sistema operativo da máquina virtual são suportadas como descrito no [Enable Azure Monitor para saúde de hóspedes VMs (pré-visualização)](vminsights-health-enable.md).
- Verifique se a extensão de saúde do hóspede instalada com sucesso com o código de saída 0.
- Verifique se a extensão do agente do Monitor Azure foi instalada com sucesso.
- Verifique se a identidade gerida atribuída pelo sistema está ativada para a máquina virtual.
- Verifique se não são especificadas identidades geridas atribuídas pelo utilizador para a máquina virtual.
- Verifique se as máquinas virtuais do Windows que localizar são *us english*. A localização não é atualmente suportada pelo agente Azure Monitor.
- Verifique se a máquina virtual não está a utilizar o representante de rede. O agente Azure Monitor não suporta atualmente os proxies.
- Verifique se o agente de extensão de saúde começou sem erros. Se o agente não puder ligar, o estado do agente pode ser corrupto. Elimine o conteúdo da pasta de estado do agente e reinicie o agente.
  - Para Linux: Daemon é *vmGuestHealthAgent*. A pasta de Estado é */var/opt/vmGuestHealthAgent/**
  - Para windows: O serviço é *o agente VM Guest Health.* A pasta de Estado é _%ProgramData%\Microsoft\Microsoft\VMGuestHealthAgent \\ *_.
- Verifique se o agente Azure Monitor tem conectividade de rede. 
  - Da máquina virtual, tente ping _<region> .handler.control.monitor.azure.com_. Por exemplo, para uma máquina virtual na westeurope, tente _pingar westeurope.handler.control.monitor.azure.com:443_.
- Verifique se a máquina virtual tem uma associação com uma regra de recolha de dados na mesma região que o espaço de trabalho Log Analytics.
  -  Consulte a **regra de recolha de dados (DCR)** no Enable [Azure Monitor para a saúde dos hóspedes VMs (pré-visualização)](vminsights-health-enable.md) para garantir que a estrutura do DCR está correta. Preste especial atenção à presença da secção de fonte de dados do *PerformanceCounters* configurada para amostras de três contadores e presença da secção *InputDataSources* na configuração da extensão de saúde para enviar contadores para a extensão.
-  Verifique se a máquina virtual não vê os erros de extensão de saúde dos hóspedes.
   -  Para o Linux: Verifique registos em _/var/log/azure/Microsoft.Azure.Monitor.VirtualMachines.GuestHealthLinuxAgent/*.log_.
   -  Para windows: Verifique registos em _C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Monitor.VirtualMachines.GuestHealthWindowsAgent \{ versão de extensão} \* .log_.
-  Verifique se a máquina virtual tem erros no agente do Azure Monitor.
   -  Para o Linux: Verifique os registos em _/var/log/mdsd.*_.
   -  Para windows: Verifique registos em _C:\WindowsAzure\Resources \* {vmName}. AMADataStore_.
 



## <a name="error-message-that-no-data-is-available"></a>Mensagem de erro de que não há dados disponíveis 

![Sem dados](media/vminsights-health-troubleshoot/no-data.png)


### <a name="verify-that-the-virtual-machine-meets-configuration-requirements"></a>Verifique se a máquina virtual cumpre os requisitos de configuração

1. Verifique se a máquina virtual é uma máquina virtual do Azure. Neste momento, não há suporte para o Azure Arc para servidores.
2. Verifique se a máquina virtual está a executar um [sistema operativo suportado](vminsights-health-enable.md?current-limitations.md).
3. Verifique se a máquina virtual está instalada numa [região suportada](vminsights-health-enable.md?current-limitations.md).
4. Verifique se a área de trabalho do Log Analytics está instalada numa [região suportada](vminsights-health-enable.md?current-limitations.md).

### <a name="verify-that-the-vm-is-properly-onboarded"></a>Verifique se o VM está devidamente a bordo
Verifique se a extensão do agente Azure Monitor e o agente de Saúde VM convidado estão a ser a provisionados com sucesso na máquina virtual. Selecione **Extensões** do menu da máquina virtual no portal Azure e certifique-se de que os dois agentes estão listados.

![Extensões de VM](media/vminsights-health-troubleshoot/extensions.png)

### <a name="verify-the-system-assigned-identity-is-enabled-on-the-virtual-machine"></a>Verifique se a identidade atribuída ao sistema está ativada na máquina virtual
Verifique se a identidade atribuída ao sistema está ativada na máquina virtual. Selecione **identidade** do menu da máquina virtual no portal Azure. Se a identidade gerida pelo utilizador estiver ativada, independentemente do estado da identidade gerida pelo sistema, o agente Azure Monitor não poderá comunicar com o serviço de configuração e a extensão de saúde do hóspede não funcionará.

![Identidade atribuída ao sistema](media/vminsights-health-troubleshoot/system-identity.png)

### <a name="verify-data-collection-rule"></a>Verificar regra de recolha de dados
Verifique se a regra de recolha de dados especificando a extensão de saúde como fonte de dados está associada à máquina virtual.

## <a name="error-message-for-bad-request-due-to-insufficient-permissions"></a>Mensagem de erro por mau pedido devido a permissões insuficientes
Este erro indica que o fornecedor de recursos **Microsoft.WorkloadMonitor** não estava registado na subscrição. Consulte [os fornecedores e tipos de recursos Azure](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider) para obter informações sobre o registo deste fornecedor de recursos. 

![Mau pedido](media/vminsights-health-troubleshoot/bad-request.png)

## <a name="health-shows-as-unknown-after-guest-health-is-enabled"></a>A saúde mostra-se "desconhecida" após a saúde dos hóspedes estar ativada.

### <a name="verify-that-performance-counters-on-windows-nodes-are-working-correctly"></a>Verifique se os contadores de desempenho nos nos nómdos windows estão a funcionar corretamente 
A saúde dos hóspedes depende do agente poder recolher contadores de desempenho do nó. base de bibliotecas de contadores de desempenho pode ficar corrompido e pode ter de ser reconstruído. Siga as instruções na [reconsopeção de desempenho manualmente](/troubleshoot/windows-server/performance/rebuild-performance-counter-library-values) para reconstruir os contadores de desempenho.





## <a name="next-steps"></a>Passos seguintes

- [Obtenha uma visão geral da característica de saúde do hóspede de insights VM](vminsights-health-overview.md)