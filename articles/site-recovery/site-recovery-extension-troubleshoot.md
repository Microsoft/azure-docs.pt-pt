---
title: Resolução de problemas da extensão do Azure VM para recuperação de desastres com recuperação do local de Azure
description: Problemas de resolução de problemas com a extensão Azure VM para recuperação de desastres com recuperação do local de Azure.
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.openlocfilehash: c1915d108bf9465d3e5b8d6a55053b583ee4f580
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96009709"
---
# <a name="troubleshoot-azure-vm-extension-issues"></a>Resolver problemas da extensão da VM do Azure

Este artigo fornece medidas de resolução de problemas que podem ajudá-lo a resolver erros de recuperação do site Azure relacionados com o agente VM e extensão.

## <a name="low-system-resources"></a>Baixos recursos do sistema

Este problema ocorre quando o sistema tem pouca memória disponível, e não é capaz de alocar memória para instalação de serviço de mobilidade. Certifique-se de que a memória foi libertada para que a instalação prossiga e complete com sucesso.

## <a name="azure-site-recovery-extension-time-out"></a>Tempo de tempo de recuperação do local de Azure  

Error message: "Task execution has timed out while tracking for extension operation to start"<br>
Código de erro: "151076"

 A Azure Site Recovery instalou uma extensão na máquina virtual como parte de um trabalho de proteção ativa. Qualquer uma das seguintes condições pode impedir que a proteção seja ativada e fazer com que o trabalho falhe. Complete as seguintes etapas de resolução de problemas e, em seguida, relemisse a sua operação:

- [O agente está instalado no VM, mas não responde (para VMs windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [O agente instalado no VM está desatualizado (para Os VMs Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
- [A extensão de Recuperação do Site não atualiza ou carrega](#the-site-recovery-extension-fails-to-update-or-load)

Error message: "A operação de extensão de recuperação do local anterior está a demorar mais tempo do que o esperado."<br>
Código de erro: "150066"

- [O agente está instalado no VM, mas não responde (para VMs windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [O agente instalado no VM está desatualizado (para Os VMs Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
- [O estado de extensão da recuperação do local está incorreto](#the-site-recovery-extension-fails-to-update-or-load)

## <a name="protection-fails-because-the-vm-agent-is-unresponsive"></a>A proteção falha porque o agente VM não responde

Error message: "A execução da tarefa foi cronometrada enquanto rastreia a operação de extensão a ser iniciada."<br>
Código de erro: "151099"

Este erro pode acontecer se o agente convidado Azure na máquina virtual não estiver no estado de preparação.

Pode verificar o estado do agente convidado da Azure no [portal Azure](https://portal.azure.com/). Vá à máquina virtual que está a tentar proteger e verifique o estado do agente de propriedades  >  **de definições** VM  >    >  . Na maior parte do tempo, o estado do agente está pronto depois de reiniciar a máquina virtual. No entanto, se não conseguir reiniciar ou ainda estiver de frente para o problema, então complete os seguintes passos de resolução de problemas:

- [O agente está instalado no VM, mas não responde (para VMs windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [O agente instalado no VM está desatualizado (para Os VMs Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)


Error message: "A execução da tarefa foi cronometrada enquanto rastreia a operação de extensão a ser iniciada."<br>
Código de erro: "151095"

Este erro ocorre quando a versão do agente na máquina Linux está desatualizada. Complete o seguinte passo de resolução de problemas:

- [O agente instalado no VM está desatualizado (para Os VMs Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)  

## <a name="causes-and-solutions"></a>Causas e soluções

### <a name="the-agent-is-installed-in-the-vm-but-its-unresponsive-for-windows-vms"></a><a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>O agente está instalado no VM, mas não responde (para VMs windows)

#### <a name="solution"></a>Solução
O agente VM pode ter sido corrompido, ou o serviço pode ter sido parado. Reinstalar o agente VM ajuda a obter a versão mais recente. Também ajuda a reiniciar a comunicação com o serviço.

1. Determine se o serviço de Agente Convidado Windows Azure está em execução nos serviços VM (services.msc). Reinicie o serviço de Agente Convidado Windows Azure.    
1. Se o serviço de Agente Convidado Windows Azure não estiver visível nos serviços, abra o Painel de Controlo. Vá a **Programas e Funcionalidades** para ver se o serviço de Agente Convidado do Windows está instalado.
1. Se o Windows Azure Guest Agent aparecer em **Programas e Funcionalidades, desinstale** o Windows Azure Guest Agent.
1. Descarregue e instale a [versão mais recente do agente MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Precisa de direitos de administrador para completar a instalação.
1. Verifique se o serviço de Agente Convidado Do Windows Azure aparece nos serviços.
1. Reinicie o trabalho de proteção.

Além disso, verifique se [o Microsoft .NET 4.5 está instalado](/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) no VM. Precisa de .NET 4.5 para que o agente VM comunique com o serviço.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>O agente instalado no VM está desatualizado (para Os VMs Linux)

#### <a name="solution"></a>Solução
A maioria das falhas relacionadas com o agente ou relacionadas com extensões para os VMs do Linux são causadas por problemas que afetam um agente VM desatualizado. Para resolver este problema, siga estas orientações gerais:

1. Siga as instruções de [atualização do agente Linux VM](../virtual-machines/extensions/update-linux-agent.md).

   > [!NOTE]
   > *Recomendamos vivamente* que atualize o agente apenas através de um repositório de distribuição. Não recomendamos baixar o código do agente diretamente do GitHub e atualizá-lo. Se o último agente para a sua distribuição não estiver disponível, contacte o suporte de distribuição para obter instruções sobre como instalá-lo. Para verificar o agente mais recente, vá à página do [agente Do Windows Azure Linux](https://github.com/Azure/WALinuxAgent/releases) no repositório do GitHub.

1. Certifique-se de que o agente Azure está a funcionar no VM executando o seguinte comando: `ps -e`

   Se o processo não estiver em curso, reinicie-o utilizando os seguintes comandos:

   - Para Ubuntu: `service walinuxagent start`
   - Para outras distribuições: `service waagent start`

1. [Configure o agente de reinício automático](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
1. Ativar a proteção da máquina virtual.

### <a name="the-site-recovery-extension-fails-to-update-or-load"></a>A extensão de Recuperação do Site não atualiza ou carrega

O estado de extensão mostra como "Vazio", "NãoReady" ou "Transição".

#### <a name="solution"></a>Solução

Desinstale a extensão e reinicie novamente a operação.

Para desinstalar a extensão:

1. No [portal Azure,](https://portal.azure.com/)vá ao VM que está a sofrer falha de Backup.
1. Selecione **Definições**.
1. Selecione **Extensões**.
1. Selecione **a extensão de recuperação do site**.
1. Selecione **Desinstalar**.

Para o Linux VM, se a extensão VMSnapshot não aparecer no portal Azure, [atualize o Agente Azure Linux](../virtual-machines/extensions/update-linux-agent.md). Em seguida, executar a proteção.

Quando completar estes passos, faz com que a extensão seja reinstalada durante a proteção.
