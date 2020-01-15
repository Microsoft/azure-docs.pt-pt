---
title: Solucionar problemas da extensão de VM do Azure para recuperação de desastre com Azure Site Recovery
description: Solucionar problemas com a extensão de VM do Azure para recuperação de desastre com Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.openlocfilehash: 7f9ae32b95d629ef79f085ed590d9057b0414911
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75941544"
---
# <a name="troubleshoot-azure-vm-extension-issues"></a>Solucionar problemas de extensão de VM do Azure

Este artigo fornece etapas de solução de problemas que podem ajudá-lo a resolver erros de Azure Site Recovery relacionados ao agente de VM e à extensão.


## <a name="azure-site-recovery-extension-time-out"></a>Tempo limite da extensão de Azure Site Recovery  

Mensagem de erro: "a execução da tarefa atingiu o tempo limite durante o rastreamento da operação de extensão a ser iniciada"<br>
Código de erro: "151076"

 Azure Site Recovery instalar uma extensão na máquina virtual como parte do trabalho de habilitação de proteção. Qualquer uma das condições a seguir pode impedir que a proteção seja disparada e o trabalho falhe. Conclua as seguintes etapas de solução de problemas e repita a operação:

**Causa 1: [o agente está instalado na VM, mas não está respondendo (para VMs do Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**     
**Causa 2: [o agente instalado na VM está desatualizado (para VMs do Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Causa 3: [a extensão de site Recovery falha ao atualizar ou carregar](#the-site-recovery-extension-fails-to-update-or-load)**  

Mensagem de erro: "a operação de extensão de Site Recovery anterior está levando mais tempo do que o esperado".<br>
Código de erro: "150066"<br>

**Causa 1: [o agente está instalado na VM, mas não está respondendo (para VMs do Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**     
**Causa 2: [o agente instalado na VM está desatualizado (para VMs do Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Causa 3: [o status da extensão de site Recovery está incorreto](#the-site-recovery-extension-fails-to-update-or-load)**  

## <a name="protection-fails-because-the-vm-agent-is-unresponsive"></a>A proteção falha porque o agente de VM não está respondendo

Mensagem de erro: "a execução da tarefa atingiu o tempo limite durante o rastreamento da operação de extensão a ser iniciada".<br>
Código de erro: "151099"<br>

Esse erro pode ocorrer se o agente convidado do Azure na máquina virtual não estiver no estado pronto.
Você pode verificar o status do agente convidado do Azure no [portal do Azure](https://portal.azure.com/). Vá para a máquina virtual que você está tentando proteger e verifique o status em "configurações de VM > > Propriedades > status do agente". Na maioria das vezes, o status do agente fica pronto após a reinicialização da máquina virtual. No entanto, se a reinicialização não for uma opção possível ou se você ainda estiver enfrentando o problema, conclua as seguintes etapas de solução de problemas.

**Causa 1: [o agente está instalado na VM, mas não está respondendo (para VMs do Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**     
**Causa 2: [o agente instalado na VM está desatualizado (para VMs do Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  


Mensagem de erro: "a execução da tarefa atingiu o tempo limite durante o rastreamento da operação de extensão a ser iniciada".<br>
Código de erro: "151095"<br>

Isso ocorre quando a versão do agente no computador Linux é antiga. Execute a seguinte etapa de solução de problemas.<br>
  **Causa 1: [o agente instalado na VM está desatualizado (para VMs do Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
## <a name="causes-and-solutions"></a>Causas e soluções

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>O agente está instalado na VM, mas não está respondendo (para VMs do Windows)

#### <a name="solution"></a>Solução
O agente de VM pode ter sido corrompido ou o serviço pode ter sido interrompido. Reinstalar o agente de VM ajuda a obter a versão mais recente. Ele também ajuda a reiniciar a comunicação com o serviço.

1. Determine se o "serviço do agente convidado do Windows Azure" está em execução nos serviços de VM (Services. msc). Tente reiniciar o "serviço do agente convidado do Windows Azure".    
2. Se o serviço do agente convidado do Windows Azure não estiver visível em serviços, no painel de controle, vá para **programas e recursos** para determinar se o serviço do agente convidado do Windows está instalado.
4. Se o agente convidado do Windows Azure aparecer em **programas e recursos**, desinstale o agente convidado do Windows.
5. Baixe e instale a [versão mais recente do MSI do agente](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Você deve ter direitos de administrador para concluir a instalação.
6. Verifique se os serviços do agente convidado do Windows Azure aparecem em serviços.
7. Reinicie o trabalho de proteção.

Além disso, verifique se o [Microsoft .NET 4,5 está instalado](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) na VM. O .NET 4,5 é necessário para que o agente de VM se comunique com o serviço.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>O agente instalado na VM está desatualizado (para VMs do Linux)

#### <a name="solution"></a>Solução
A maioria das falhas relacionadas ao agente ou relacionadas à extensão para VMs do Linux são causadas por problemas que afetam um agente de VM desatualizado. Para solucionar esse problema, siga estas diretrizes gerais:

1. Siga as instruções para [atualizar o agente de VM do Linux](../virtual-machines/linux/update-agent.md).

   > [!NOTE]
   > É *altamente recomendável* que você atualize o agente somente por meio de um repositório de distribuição. Não é recomendável baixar o código do agente diretamente do GitHub e atualizá-lo. Se o agente mais recente para sua distribuição não estiver disponível, entre em contato com o suporte de distribuição para obter instruções sobre como instalá-lo. Para verificar o agente mais recente, acesse a página do [agente Linux do Windows Azure](https://github.com/Azure/WALinuxAgent/releases) no repositório github.

2. Verifique se o agente do Azure está em execução na VM executando o seguinte comando: `ps -e`

   Se o processo não estiver em execução, reinicie-o usando os seguintes comandos:

   * Para o Ubuntu: `service walinuxagent start`
   * Para outras distribuições: `service waagent start`

3. [Configure o agente de reinicialização automática](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Habilite a proteção da máquina virtual.



### <a name="the-site-recovery-extension-fails-to-update-or-load"></a>A extensão de Site Recovery falha ao atualizar ou carregar
Se o status das extensões for "Empty", "ilegível" ou em transição.

#### <a name="solution"></a>Solução

Desinstale a extensão e reinicie a operação.

Para desinstalar a extensão:

1. Na [portal do Azure](https://portal.azure.com/), vá para a VM que está apresentando falha de backup.
2. Selecione **definições**.
3. Selecione **Extensions** (Extensões).
4. Selecione a **extensão site Recovery**.
5. Selecione **Desinstalar**.

Para a VM do Linux, se a extensão VMSnapshot não aparecer na portal do Azure, [atualize o agente Linux do Azure](../virtual-machines/linux/update-agent.md)e, em seguida, execute a proteção. 

A conclusão dessas etapas faz com que a extensão seja reinstalada durante a proteção.


