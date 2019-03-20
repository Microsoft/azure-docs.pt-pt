---
title: Resolver problemas com agentes do Azure Site Recovery | Documentos da Microsoft
description: Fornece informações sobre os sintomas, causas e resoluções de falhas de agente do Azure Site Recovery.
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.author: asgang
ms.openlocfilehash: 5ea701682c03370cea46f9126ecf78427a776371
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58103541"
---
# <a name="troubleshoot-issues-with-the-azure-site-recovery-agent"></a>Resolver problemas com o agente do Azure Site Recovery

Este artigo fornece passos de resolução de problemas que podem ajudar a resolverem erros de Azure Site Recovery relacionados com o agente da VM e a extensão.


## <a name="azure-site-recovery-extension-time-out"></a>Limite de tempo de extensão do Azure Site Recovery  

Mensagem de erro: "A execução da tarefa foi excedido durante o controlo de operação de extensão ser iniciado"<br>
Código do erro: "151076"

 O Azure Site Recovery instalar uma extensão na máquina virtual como parte da tarefa de proteção de ativação. Qualquer uma das seguintes condições poderá impedir a proteção de acionamento e a tarefa a falhar. Conclua os seguintes passos de resolução de problemas e, em seguida, repita a operação:

**Fazer com que 1: [O agente está instalado na VM, mas ele não está a responder (para VMs do Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**Causa 2: [O agente instalado na VM está desatualizado (para VMs do Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Causa 3: [A extensão do Site Recovery não consegue atualizar ou de carga](#the-site-recovery-extension-fails-to-update-or-load)**  

Mensagem de erro: "A operação de extensão de recuperação de sites anterior está a demorar mais tempo do que o esperado."<br>
Código do erro: "150066"<br>

**Fazer com que 1: [O agente está instalado na VM, mas ele não está a responder (para VMs do Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**Causa 2: [O agente instalado na VM está desatualizado (para VMs do Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Causa 3: [O estado da extensão Site Recovery está incorreto](#the-site-recovery-extension-fails-to-update-or-load)**  

## <a name="protection-fails-because-the-vm-agent-is-unresponsive"></a>A proteção falha porque o agente da VM não está a responder

Mensagem de erro: "A execução da tarefa foi excedido durante o controlo de operação de extensão de ser iniciadas."<br>
Código do erro: "151099"<br>

Este erro pode ocorrer se o agente convidado do Azure na máquina virtual não está no estado pronto.
Pode verificar o estado do agente convidado do Azure no [portal do Azure](https://portal.azure.com/). Vá para a máquina virtual está a tentar proteger e verificar o estado em "VM > Definições > propriedades > Estado do agente". Na maioria das vezes o estado do agente fique pronto após reiniciar a máquina virtual. No entanto, se o reinício não é uma opção viável ou que ainda está enfrentando o problema, em seguida, conclua os seguintes passos de resolução de problemas.

**Fazer com que 1: [O agente está instalado na VM, mas ele não está a responder (para VMs do Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**Causa 2: [O agente instalado na VM está desatualizado (para VMs do Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  


Mensagem de erro: "A execução da tarefa foi excedido durante o controlo de operação de extensão de ser iniciadas."<br>
Código do erro: "151095"<br>

Esta situação ocorrer quando a versão do agente na máquina Linux é antiga. Conclua o passo seguinte de resolução de problemas.<br>
  **Fazer com que 1: [O agente instalado na VM está desatualizado (para VMs do Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
## <a name="causes-and-solutions"></a>Causas e soluções

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>O agente está instalado na VM, mas ele não está a responder (para VMs do Windows)

#### <a name="solution"></a>Solução
O agente da VM pode ter sido corrompido ou o serviço pode ter sido parado. Reinstalar o agente da VM ajuda a obter a versão mais recente. Ele também ajuda a reiniciar a comunicação com o serviço.

1. Determinar se o "Windows serviço de agente convidado do Azure" está em execução nos serviços de VM (Services. msc). Tente reiniciar o "Windows serviço de agente convidado do Azure".    
2. Se o serviço de agente de convidado do Windows Azure não está visível nos serviços, no painel de controlo, aceda a **programas e funcionalidades** para determinar se o serviço de agente de convidado do Windows está instalado.
4. Se o agente de convidado do Windows Azure é apresentado na **programas e funcionalidades**, desinstale o agente convidado do Windows.
5. Transferir e instalar o [mais recente versão do agente do MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Tem de ter direitos de administrador para concluir a instalação.
6. Certifique-se de que os serviços do agente de convidado do Windows Azure é apresentado nos serviços.
7. Reinicie a tarefa de proteção.

Além disso, certifique-se de que [Microsoft .NET 4.5 está instalado](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) na VM. .NET 4.5 é necessário para o agente da VM comunicar com o serviço.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>O agente instalado na VM está desatualizado (para VMs do Linux)

#### <a name="solution"></a>Solução
Mais relacionados com o agente ou relacionados com a extensão de falhas para VMs do Linux são causadas por problemas que afetam um agente VM desatualizado. Para resolver este problema, siga estas Diretrizes gerais:

1. Siga as instruções para [atualizar o agente da VM do Linux](../virtual-machines/linux/update-agent.md).

   > [!NOTE]
   > Estamos *altamente recomendável* que Atualize o agente apenas por meio de um repositório de distribuição. Não é recomendável baixar o código do agente diretamente a partir do GitHub e a atualizá-los. Se o agente mais recente para a sua distribuição não é suportada de distribuição disponíveis, contacte para obter instruções sobre como instalá-lo. Para verificar se o agente mais recente, vá para o [agente do Windows Azure Linux](https://github.com/Azure/WALinuxAgent/releases) página no repositório do GitHub.

2. Certifique-se de que o agente do Azure está em execução a VM ao executar o seguinte comando: `ps -e`

   Se o processo não está em execução, reinicie-o usando os seguintes comandos:

   * Para o Ubuntu: `service walinuxagent start`
   * Para outras distribuições: `service waagent start`

3. [Configurar o agente de reinício automático](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Ative a proteção da máquina virtual.



### <a name="the-site-recovery-extension-fails-to-update-or-load"></a>A extensão do Site Recovery não consegue atualizar ou de carga
Se o estado das extensões é "vazia", "NotReady" ou transitar.

#### <a name="solution"></a>Solução

Desinstale a extensão e reinicie a operação de novo.

Para desinstalar a extensão:

1. Na [portal do Azure](https://portal.azure.com/), vá para a VM que está com falhas de cópia de segurança.
2. Selecione **definições**.
3. Selecione **Extensions** (Extensões).
4. Selecione **extensão de recuperação de Site**.
5. Selecione **desinstalar**.

Para VM do Linux, se a extensão do VMSnapshot não mostra no portal do Azure, [atualizar o agente do Linux do Azure](../virtual-machines/linux/update-agent.md), e, em seguida, execute a proteção. 

Concluir estes passos, faz com que a extensão de ser reinstalados durante a proteção.


