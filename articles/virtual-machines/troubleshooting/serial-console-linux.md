---
title: Consola em série Azure para Linux Microsoft Docs
description: Bi-Directional consola em série para máquinas virtuais Azure e conjuntos de balança de máquinas virtuais usando um exemplo Linux.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 5/1/2019
ms.author: alsin
ms.openlocfilehash: 9a31a22a5b037162198f594d9bcf35c91a0a4654
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91306876"
---
# <a name="azure-serial-console-for-linux"></a>Consola de Série do Azure para Linux

A Consola em Série no portal Azure fornece acesso a uma consola baseada em texto para máquinas virtuais Linux (VMs) e instâncias de conjunto de escala de máquina virtual. Esta ligação em série liga-se à porta de série ttys0 do VM ou da placa de conjunto de escala de máquina virtual, proporcionando acesso ao mesmo independentemente do estado da rede ou do sistema operativo. A consola em série só pode ser acedida através do portal Azure e só é permitida para os utilizadores que tenham uma função de acesso de Colaborador ou superior ao conjunto de escala de VM ou de máquina virtual.

A Consola em Série funciona da mesma forma para VMs e instâncias de escala de máquinas virtuais. Neste doc, todas as menções aos VMs incluirão implicitamente instâncias de conjunto de escala de máquina virtual, salvo indicação em contrário.

A Consola Em Série está geralmente disponível nas regiões globais do Azure e em pré-visualização pública no Governo de Azure. Ainda não está disponível na nuvem Azure China.

Para documentação da consola em série para windows, consulte [a Consola em Série para Windows](./serial-console-windows.md).

> [!NOTE]
> A Consola em Série é atualmente incompatível com uma conta de armazenamento de diagnóstico de arranque gerida. Para utilizar a Consola em Série, certifique-se de que está a utilizar uma conta de armazenamento personalizada.

## <a name="prerequisites"></a>Pré-requisitos

- A sua instância de definição de escala de VM ou de máquina virtual deve utilizar o modelo de implementação de gestão de recursos. As implantações clássicas não são suportadas.

- A sua conta que utiliza consola em série deve ter o [papel de Contribuinte de Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) para o VM e a conta de armazenamento de diagnóstico de [arranque](boot-diagnostics.md)

- A sua instância de definição de escala de VM ou de máquina virtual deve ter um utilizador baseado em palavra-passe. Pode criar uma com a função [de senha de reset](../extensions/vmaccess.md#reset-password) da extensão de acesso VM. Selecione Redefinir a **palavra-passe** a partir da secção **'Suporte + resolução de problemas'.**

- A sua instância de definição de balança de VM ou de máquina virtual deve ter [diagnósticos de arranque](boot-diagnostics.md) ativados.

    ![Definições de diagnóstico de arranque](./media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

- Para configurações específicas das distribuições do Linux, consulte [a disponibilidade de distribuição da consola em série Linux](#serial-console-linux-distribution-availability).

- A sua instância de definição de balança de VM ou de máquina virtual deve ser configurada para a saída em série `ttys0` . Este é o padrão para imagens Azure, mas você vai querer verificar duas vezes isto em imagens personalizadas. Detalhes [abaixo](#custom-linux-images).


> [!NOTE]
> A consola em série requer um utilizador local com uma palavra-passe configurada. VMs ou conjuntos de balança de máquinas virtuais configurados apenas com uma chave pública SSH não poderão iniciar súpido na consola em série. Para criar um utilizador local com uma palavra-passe, utilize a [Extensão VMAccess](../extensions/vmaccess.md), que está disponível no portal selecionando **a palavra-passe Reset** no portal Azure e crie um utilizador local com uma palavra-passe.
> Também pode redefinir a palavra-passe do administrador na sua conta [utilizando o GRUB para iniciar o modo de utilizador único](./serial-console-grub-single-user-mode.md).

## <a name="serial-console-linux-distribution-availability"></a>Disponibilidade de distribuição em Série Consola Linux
Para que a consola em série funcione corretamente, o sistema operativo do hóspede deve ser configurado para ler e escrever mensagens de consola para a porta em série. A maioria das [distribuições de Azure Linux endossada](../linux/endorsed-distros.md) têm a consola em série configurada por padrão. Selecionar a **consola serial** na secção de resolução de **problemas Do Suporte +** do portal Azure permite o acesso à consola em série.

> [!NOTE]
> Se não estiver a ver nada na consola em série, certifique-se de que os diagnósticos de arranque estão ativados no seu VM. O Hit **Enter** irá frequentemente corrigir problemas em que nada aparece na consola em série.

Distribuição      | Acesso à consola de série
:-----------|:---------------------
Red Hat Enterprise Linux    | Acesso à consola em série ativado por predefinição.
CentOS      | Acesso à consola em série ativado por predefinição.
Debian      | Acesso à consola em série ativado por predefinição.
Ubuntu      | Acesso à consola em série ativado por predefinição.
CoreOS      | Acesso à consola em série ativado por predefinição.
SUSE        | As imagens SLES mais recentes disponíveis no Azure têm acesso a consola em série ativado por padrão. Se estiver a utilizar versões mais antigas (10 ou mais) de SLES no Azure, consulte o [artigo KB](https://www.novell.com/support/kb/doc.php?id=3456486) para ativar a consola em série.
Oracle Linux        | Acesso à consola em série ativado por predefinição.

### <a name="custom-linux-images"></a>Imagens personalizadas do Linux
Para ativar a consola em série para a sua imagem Personalizada Linux VM, permita o acesso à consola no ficheiro */etc/inittab* para executar um terminal ligado `ttyS0` . Por exemplo: `S0:12345:respawn:/sbin/agetty -L 115200 console vt102`. Também pode precisar de gerar um getty no ttyS0. Isto pode ser feito `systemctl start serial-getty@ttyS0.service` com.

Também vai querer adicionar ttys0 como o destino para a saída em série. Para obter mais informações sobre a configuração de uma imagem personalizada para trabalhar com a consola em série, consulte os requisitos gerais do sistema na [Create e carreque um Linux VHD em Azure](https://aka.ms/createuploadvhd#general-linux-system-requirements).

Se estiver a construir um núcleo personalizado, considere permitir estas bandeiras do núcleo: `CONFIG_SERIAL_8250=y` e `CONFIG_MAGIC_SYSRQ_SERIAL=y` . O ficheiro de configuração está tipicamente localizado no caminho */boot/.*

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Cenários comuns para aceder à Consola em Série

Cenário          | Ações na Consola em Série
:------------------|:-----------------------------------------
Arquivo *FSTAB* quebrado | Prima a tecla **'Inserir'** para continuar e utilize um editor de texto para corrigir o ficheiro *FSTAB.* Pode ser necessário estar em modo de utilizador único para o fazer. Para obter mais informações, consulte a secção de consola em série de [Como corrigir problemas de fstab](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) e [Use a consola em série para aceder ao GRUB e ao modo de utilizador único](serial-console-grub-single-user-mode.md).
Regras de firewall incorretas |  Se tiver configurados iptables para bloquear a conectividade SSH, pode utilizar a consola em série para interagir com o seu VM sem precisar de SSH. Mais detalhes podem ser encontrados na [página do homem do iptables](https://linux.die.net/man/8/iptables).<br>Da mesma forma, se a sua firewall estiver a bloquear o acesso SSH, pode aceder ao VM através da consola em série e reconfigurar a firewall. Mais detalhes podem ser encontrados na [documentação firewall.](https://firewalld.org/documentation/)
Corrupção/verificação do sistema de ficheiros | Consulte a secção de consola em série do [Azure Linux VM não pode ser iniciada devido a erros no sistema de ficheiros](https://support.microsoft.com/en-us/help/3213321/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck) para obter mais detalhes sobre a resolução de problemas de sistemas de ficheiros corrompidos utilizando a consola em série.
Problemas de configuração SSH | Aceda à consola em série e altere as definições. A consola em série pode ser utilizada independentemente da configuração SSH de um VM, uma vez que não requer que o VM tenha conectividade de rede para funcionar. Um guia de resolução de problemas está disponível nas [ligações SSH de resolução de problemas a um VM Azure Linux que falha, falha ou é recusado](./troubleshoot-ssh-connection.md). Mais detalhes estão disponíveis em [etapas detalhadas de resolução de problemas SSH para problemas ligados a um Linux VM em Azure](./detailed-troubleshoot-ssh-connection.md)
Interagindo com o bootloader | Reinicie o seu VM a partir da lâmina da consola em série para aceder ao GRUB no seu Linux VM. Para obter mais detalhes e informações específicas, consulte [use a consola em série para aceder ao GRUB e ao modo de utilizador único](serial-console-grub-single-user-mode.md).

## <a name="disable-the-serial-console"></a>Desativar a Consola em Série

Por padrão, todas as subscrições têm acesso à consola em série ativado. Pode desativar a consola em série ao nível de subscrição ou ao nível de escala de máquina vm/virtual. Para obter instruções detalhadas, visite [Ativar e desativar a Consola em Série Azure](./serial-console-enable-disable.md).

## <a name="serial-console-security"></a>Segurança da consola em série

### <a name="access-security"></a>Segurança de acesso
O acesso à consola em série está limitado a utilizadores que tenham uma função de acesso do [Virtual Machine Contributor](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) ou superior à máquina virtual. Se o seu inquilino do Azure Ative Directory necessitar de autenticação multi-factor (MFA), então o acesso à consola em série também necessitará de MFA porque o acesso da consola em série é através do [portal Azure.](https://portal.azure.com)

### <a name="channel-security"></a>Segurança do canal
Todos os dados enviados de um lado para o outro estão encriptados no fio.

### <a name="audit-logs"></a>Registos de auditoria
Todo o acesso à consola em série está atualmente registado nos [registos](./boot-diagnostics.md) de diagnóstico de arranque da máquina virtual. O acesso a estes registos é propriedade e controlado pelo administrador de máquina virtual Azure.

> [!CAUTION]
> Não são registadas senhas de acesso para a consola. No entanto, se os comandos executados dentro da consola contiverem ou derramar palavras-passe, segredos, nomes de utilizador ou qualquer outra forma de informação pessoalmente identificável (PII), esses serão escritos para os registos de diagnóstico de arranque VM. Serão escritas juntamente com todos os outros textos visíveis, como parte da implementação da função de retrocesso da consola em série. Estes registos são circulares e apenas os indivíduos com permissões de leitura para a conta de armazenamento de diagnósticos têm acesso aos mesmos. Se estiver a inserir quaisquer comandos de dados ou de pii que contenham segredos ou PII, recomendamos a utilização de SSH a menos que a consola em série seja absolutamente necessária.

### <a name="concurrent-usage"></a>Utilização simultânea
Se um utilizador estiver ligado à consola em série e outro utilizador solicitar com sucesso o acesso à mesma máquina virtual, o primeiro utilizador será desligado e o segundo utilizador ligado à mesma sessão.

> [!CAUTION]
> Isto significa que um utilizador desligado não será desligado. A capacidade de impor um logout após a desconexão (utilizando SIGHUP ou mecanismo semelhante) ainda está no roteiro. Para o Windows existe um tempo limite automático ativado na Consola Administrativa Especial (SAC); no entanto, para o Linux pode configurar a definição de tempo limite de terminal. Para tal, adicione `export TMOUT=600` o seu *.bash_profile* ou ficheiro *.perfil* para o utilizador que utiliza para iniciar sessão na consola. Esta definição irá esgotar a sessão após 10 minutos.

## <a name="accessibility"></a>Acessibilidade
A acessibilidade é um foco chave para a Consola em Série Azure. Para tal, garantimos que a consola em série está totalmente acessível.

### <a name="keyboard-navigation"></a>Navegação do teclado
Utilize a tecla **'Separador'** no teclado para navegar na interface da consola em série a partir do portal Azure. A sua localização será destacada no ecrã. Para deixar o foco da janela da consola em série, prima **Ctrl** + **F6** no seu teclado.

### <a name="use-serial-console-with-a-screen-reader"></a>Use a Consola em Série com um leitor de ecrã
A consola em série tem suporte para leitores de ecrã incorporado. Navegar por aí com um leitor de ecrã ligado permitirá que o texto alt para o botão atualmente selecionado seja lido em voz alta pelo leitor de ecrã.

## <a name="known-issues"></a>Problemas conhecidos
Estamos cientes de alguns problemas com a consola em série e o sistema operativo do VM. Aqui está uma lista destas questões e passos para mitigação para os VMs Linux. Estas questões e mitigações aplicam-se tanto aos VMs como aos casos de escala de máquinas virtuais. Se estes não corresponderem ao erro que está a ver, consulte os erros comuns de serviço de consola em série nos [erros da Consola De Série Comum](./serial-console-errors.md).

Problema                           |   Mitigação
:---------------------------------|:--------------------------------------------|
Pressionar **Insira** depois do banner de ligação não fazer com que seja visualizada uma indicação de entrada. | O GRUB não pode ser configurado corretamente. Executar os seguintes comandos: `grub2-mkconfig -o /etc/grub2-efi.cfg` e/ou `grub2-mkconfig -o /etc/grub2.cfg` . Para mais informações, ver [Hitting enter não faz nada.](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md) Este problema pode ocorrer se estiver a executar um VM personalizado, um aparelho endurecido ou um config GRUB que faz com que o Linux não se conecte à porta de série.
O texto da consola em série ocupa apenas uma parte do tamanho do ecrã (muitas vezes após a utilização de um editor de texto). | As consolas em série não suportam negociar sobre o tamanho da janela[(RFC 1073),](https://www.ietf.org/rfc/rfc1073.txt)o que significa que não haverá sinal SIGWINCH enviado para atualizar o tamanho do ecrã e o VM não terá conhecimento do tamanho do seu terminal. Instale xterm ou um utilitário similar para fornecer-lhe o `resize` comando e, em seguida, executar `resize` .
Colar cordas compridas não funciona. | A consola em série limita o comprimento das cordas coladas no terminal aos caracteres de 2048 para evitar sobrecargas na largura de banda da porta em série.
Entrada de teclado errático em imagens SLES BYOS. A entrada do teclado só é esporadicamente reconhecida. | Este é um problema com o pacote Plymouth. Plymouth não deve ser executado em Azure, uma vez que não precisa de um ecrã de respingo e Plymouth interfere com a capacidade da plataforma de usar a Consola Em Série. Remova Plymouth com `sudo zypper remove plymouth` e, em seguida, reinicie. Em alternativa, modifique a linha de núcleo do seu config GRUB, `plymouth.enable=0` apimindo-se até ao fim da linha. Pode fazê-lo [editando a entrada de arranque na hora do arranque,](https://aka.ms/serialconsolegrub#single-user-mode-in-suse-sles)ou editando a linha GRUB_CMDLINE_LINUX `/etc/default/grub` em , reconstruindo GRUB com , e, em `grub2-mkconfig -o /boot/grub2/grub.cfg` seguida, reiniciando.


## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**Q. Como posso enviar feedback?**

A. Fornecer feedback criando um problema gitHub em  https://aka.ms/serialconsolefeedback . Alternativamente (menos preferencial), pode enviar feedback através azserialhelp@microsoft.com ou na categoria de máquina virtual de https://feedback.azure.com .

**Q. A consola em série suporta a cópia/pasta?**

A. Sim. Utilize **ctrl** + **Shift** + **C** e **Ctrl** + **Shift** + **V** para copiar e colar no terminal.

**Q. Posso usar a consola em série em vez de uma ligação SSH?**

A. Embora esta utilização possa parecer tecnicamente possível, a consola em série destina-se a ser usada principalmente como uma ferramenta de resolução de problemas em situações em que a conectividade via SSH não é possível. Recomendamos que não utilize a consola em série como substituto do SSH pelas seguintes razões:

- A consola em série não tem tanta largura de banda como a SSH. Porque é uma ligação só de texto, mais interações pesadas do GUI são difíceis.
- O acesso à consola em série só é possível utilizando um nome de utilizador e senha. Como as teclas SSH são muito mais seguras do que as combinações username/password, do ponto de vista da segurança de entrada, recomendamos SSH sobre consola em série.

**Q. Quem pode ativar ou desativar a consola em série para a minha subscrição?**

A. Para ativar ou desativar a consola em série a um nível de subscrição, tem de escrever permissões para a subscrição. As funções que têm permissão de escrita incluem funções de administrador ou proprietário. As funções personalizadas também podem ter permissões de escrita.

**Q. Quem pode aceder à consola em série para o meu conjunto de escala de máquinas VM/virtual?**

A. Tem de ter o papel de Contribuinte de Máquina Virtual ou superior para uma balança de máquinas VM ou virtual definida para aceder à consola em série.

**Q. A minha consola em série não está a mostrar nada, o que faço?**

A. A sua imagem está provavelmente mal configurada para o acesso à consola em série. Para obter informações sobre a configuração da sua imagem para ativar a consola em série, consulte a [disponibilidade de distribuição da consola em série Linux](#serial-console-linux-distribution-availability).

**Q. A consola em série está disponível para conjuntos de escala de máquinas virtuais?**

A. Sim, é! Ver [consola em série para conjuntos de escala de máquinas virtuais](serial-console-overview.md#serial-console-for-virtual-machine-scale-sets)

**Q. Se eu configurar o meu VM ou a escala de máquina virtual definida usando apenas a autenticação da chave SSH, ainda posso usar a consola em série para ligar à minha instância de conjunto de escala de vm/máquina virtual?**

A. Sim. Como a consola em série não necessita de chaves SSH, basta configurar uma combinação username/password. Pode fazê-lo selecionando a **palavra-passe Reset** no portal Azure e utilizando essas credenciais para iniciar súbs na consola em série.

## <a name="next-steps"></a>Passos seguintes
* Utilize a consola em série para aceder ao [GRUB e ao modo de utilizador único](serial-console-grub-single-user-mode.md).
* Utilize a consola em série para [chamadas NMI e SysRq](serial-console-nmi-sysrq.md).
* Saiba como usar a consola em série para permitir grub [em vários distros](serial-console-grub-proactive-configuration.md)
* A consola em série também está disponível para [VMs windows](./serial-console-windows.md).
* Saiba mais sobre [diagnósticos de arranque.](boot-diagnostics.md)
