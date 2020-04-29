---
title: Resolução de problemas a extensão Azure VM para recuperação de desastres com recuperação do site Azure
description: Problemas com a extensão Azure VM para recuperação de desastres com a Recuperação do Site Azure.
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.openlocfilehash: a780a42179a0bacf0e4a12ba1e75ae84943539b4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77190730"
---
# <a name="troubleshoot-azure-vm-extension-issues"></a>Problemas de resolução de problemas De tedição de VM Azure

Este artigo fornece passos de resolução de problemas que podem ajudá-lo a resolver erros de recuperação do site azure relacionados com o agente VM e extensão.


## <a name="azure-site-recovery-extension-time-out"></a>Tempo de extensão de extensão do site azure  

Mensagem de erro: "A execução da tarefa tem esgotado enquanto rastreia a operação de extensão a ser iniciada"<br>
Código de erro: "151076"

 A Azure Site Recovery instalou uma extensão na máquina virtual como parte de um trabalho de proteção ativa. Qualquer uma das seguintes condições pode impedir que a proteção seja acionada e fazer com que o trabalho falhe. Complete os seguintes passos de resolução de problemas e, em seguida, tente novamente a sua operação:

- [O agente está instalado no VM, mas não responde (para VMs do Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [O agente instalado no VM está desatualizado (para VMs Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
- [A extensão de recuperação do site não atualiza ou carrega](#the-site-recovery-extension-fails-to-update-or-load)

Mensagem de erro: "A operação de extensão de recuperação do local anterior está a demorar mais tempo do que o esperado."<br>
Código de erro: "150066"

- [O agente está instalado no VM, mas não responde (para VMs do Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [O agente instalado no VM está desatualizado (para VMs Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
- [O estado de extensão de recuperação do site está incorreto](#the-site-recovery-extension-fails-to-update-or-load)

## <a name="protection-fails-because-the-vm-agent-is-unresponsive"></a>A proteção falha porque o agente VM não responde

Mensagem de erro: "A execução da tarefa tem esgotado enquanto rastreia a operação de extensão a ser iniciada."<br>
Código de erro: "151099"

Este erro pode acontecer se o agente convidado azure na máquina virtual não estiver em estado de preparação.

Pode verificar o estado do agente convidado Azure no [portal Azure.](https://portal.azure.com/) Vá à máquina virtual que está a tentar proteger e verifique o estado no estado do**Agente****de Propriedades** > de**Definições** >  **VM** > . Na maior parte do tempo, o estado do agente está pronto depois de reiniciar a máquina virtual. No entanto, se não conseguir reiniciar ou ainda estiver de frente para o problema, então complete os seguintes passos de resolução de problemas:

- [O agente está instalado no VM, mas não responde (para VMs do Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [O agente instalado no VM está desatualizado (para VMs Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)


Mensagem de erro: "A execução da tarefa tem esgotado enquanto rastreia a operação de extensão a ser iniciada."<br>
Código de erro: "151095"

Este erro ocorre quando a versão do agente na máquina Linux está desatualizada. Complete o seguinte passo de resolução de problemas:

- [O agente instalado no VM está desatualizado (para VMs Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)  

## <a name="causes-and-solutions"></a>Causas e soluções

### <a name="the-agent-is-installed-in-the-vm-but-its-unresponsive-for-windows-vms"></a><a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>O agente está instalado no VM, mas não responde (para VMs do Windows)

#### <a name="solution"></a>Solução
O agente vm pode ter sido corrompido, ou o serviço pode ter sido interrompido. Reinstalar o agente VM ajuda a obter a versão mais recente. Também ajuda a reiniciar a comunicação com o serviço.

1. Determine se o serviço de Agente Convidado Windows Azure está a funcionar nos serviços VM (services.msc). Reinicie o serviço de Agente Convidado Windows Azure.    
1. Se o serviço de Agente Convidado Windows Azure não estiver visível nos serviços, abra o Painel de Controlo. Vá a **Programas e Funcionalidades** para ver se o serviço de Agente Convidado do Windows está instalado.
1. Se o Agente Convidado do Windows Azure aparecer em **Programas e Funcionalidades,** desinstale o Agente Convidado do Windows Azure.
1. Descarregue e instale a [versão mais recente do agente MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Precisa dos direitos do administrador para completar a instalação.
1. Verifique se o serviço de Agente Convidado Windows Azure aparece nos serviços.
1. Reinicie o trabalho de proteção.

Além disso, verifique se o [Microsoft .NET 4.5 está instalado](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) no VM. Precisa de .NET 4.5 para que o agente VM se comunique com o serviço.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>O agente instalado no VM está desatualizado (para VMs Linux)

#### <a name="solution"></a>Solução
A maioria das falhas relacionadas com o agente ou as falhas relacionadas com a extensão dos VMs linux são causadas por problemas que afetam um agente VM desatualizado. Para resolver esta questão, siga estas orientações gerais:

1. Siga as instruções para [atualizar o agente Linux VM](../virtual-machines/linux/update-agent.md).

   > [!NOTE]
   > *Recomendamos vivamente* que atualize o agente apenas através de um repositório de distribuição. Não recomendamos descarregar o código do agente diretamente do GitHub e atualizá-lo. Se o mais recente agente para a sua distribuição não estiver disponível, contacte o suporte de distribuição para obter instruções sobre como instalá-lo. Para verificar o agente mais recente, vá à página de [agente do Windows Azure Linux](https://github.com/Azure/WALinuxAgent/releases) no repositório GitHub.

1. Certifique-se de que o agente Azure está a funcionar no VM executando o seguinte comando:`ps -e`

   Se o processo não estiver em execução, reinicie-o utilizando os seguintes comandos:

   - Para Ubuntu:`service walinuxagent start`
   - Para outras distribuições:`service waagent start`

1. [Configure o reinício automático](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
1. Ativar a proteção da máquina virtual.

### <a name="the-site-recovery-extension-fails-to-update-or-load"></a>A extensão de recuperação do site não atualiza ou carrega

O estado de extensão mostra como "Vazio", "Não Pronto" ou "Transição".

#### <a name="solution"></a>Solução

Desinstale a extensão e reinicie a operação.

Para desinstalar a extensão:

1. No [portal Azure,](https://portal.azure.com/)vá ao VM que está a sofrer uma falha de backup.
1. Selecione **Definições**.
1. Selecione **Extensions** (Extensões).
1. **Selecione extensão**de recuperação do site .
1. Selecione **Desinstalar**.

Para o Linux VM, se a extensão VMSnapshot não aparecer no portal Azure, [atualize o Agente Azure Linux](../virtual-machines/linux/update-agent.md). Então, dirija a proteção.

Quando completa estes passos, faz com que a extensão seja reinstalada durante a proteção.
