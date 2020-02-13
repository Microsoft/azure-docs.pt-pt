---
title: Consola em série Azure para Linux Microsoft Docs
description: Consola em série bidirecional para máquinas virtuais azure e conjuntos de escala de máquina virtual.
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
ms.openlocfilehash: b1f7708c9bd213e201ba4eb8837a191dca68ca9e
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77167021"
---
# <a name="azure-serial-console-for-linux"></a>Consola em série Azure para Linux

A Consola Em Série no portal Azure fornece acesso a uma consola baseada em texto para máquinas virtuais Linux (VMs) e instâncias de conjunto de escala de máquinavirtual. Esta ligação em série liga-se à porta de série ttys0 da instância de conjunto de métricas vm ou máquina virtual, proporcionando-lhe acesso independentemente da rede ou do estado do sistema operativo. A consola em série só pode ser acedida através do portal Azure e só é permitida para os utilizadores que tenham uma função de acesso de Colaborador ou superior ao conjunto de escala de máquinas VM ou virtual.

A Consola Série funciona da mesma forma para VMs e instâncias de conjunto de métricas de máquinas virtuais. Neste doc, todas as menções aos VMs incluirão implicitamente instâncias de conjunto de máquinas virtuais, salvo indicação em contrário.

Para obter documentação da consola em série para Windows, consulte [a Consola em Série para Windows](../windows/serial-console.md).

> [!NOTE]
> A Consola Em Série está geralmente disponível nas regiões globais do Azure e em antevisão pública no Governo azure. Ainda não está disponível na nuvem azure china.


## <a name="prerequisites"></a>Pré-requisitos

- A sua configuração de conjunto de métricas vm ou virtual deve utilizar o modelo de implementação da gestão de recursos. Implementações clássicas não são suportadas.

- A sua conta que utiliza a consola em série deve ter a [função de Colaborador de Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) para o VM e a conta de armazenamento de diagnóstico de [arranque](boot-diagnostics.md)

- A sua configuração de conjunto de métricas vM ou virtual deve ter um utilizador baseado em palavra-passe. Pode criar um com a função de [palavra-passe redefinida](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) da extensão de acesso VM. Selecione **Redefinir a palavra-passe** da secção Suporte + resolução de **problemas.**

- A sua configuração de conjunto de métricas vm ou máquina virtual deve ter [diagnósticos](boot-diagnostics.md) de arranque ativados.

    ![Definições de diagnóstico de arranque](./media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

- Para configurações específicas das distribuições do Linux, consulte a disponibilidade de distribuição do [Linux](#serial-console-linux-distribution-availability)em série .

- A sua definição de vm ou de conjunto de máquinavirtual deve ser configurada para uma saída em série no `ttys0`. Este é o padrão para imagens Azure, mas você vai querer verificar novamente isso em imagens personalizadas. Detalhes [abaixo](#custom-linux-images).


> [!NOTE]
> A consola em série requer um utilizador local com uma senha configurada. VMs ou conjuntos de escala de máquinavirtual configurados apenas com uma chave pública SSH não serão capazes de iniciar sessão na consola em série. Para criar um utilizador local com uma palavra-passe, utilize a [Extensão VMAccess](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension), que está disponível no portal selecionando **a palavra-passe Reset** no portal Azure, e criar um utilizador local com uma palavra-passe.
> Também pode redefinir a palavra-passe do administrador na sua conta [utilizando o GRUB para iniciar o modo de utilizador único](./serial-console-grub-single-user-mode.md).

## <a name="serial-console-linux-distribution-availability"></a>Disponibilidade de distribuição de Consola sonorizada de linhaux
Para que a consola em série funcione corretamente, o sistema operativo do hóspede deve ser configurado para ler e escrever mensagens de consola para a porta de série. A maioria das [distribuições endossadas do Azure Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) têm a consola em série configurada por padrão. Selecionar **a consola série** na secção **suporte + resolução** de problemas do portal Azure proporciona acesso à consola em série.

> [!NOTE]
> Se não estiver a ver nada na consola em série, certifique-se de que o diagnóstico de arranque está ativado no seu VM. Hit **Enter** irá muitas vezes corrigir problemas em que nada aparece na consola em série.

Distribuição      | Acesso à consola de série
:-----------|:---------------------
Red Hat Enterprise Linux    | Acesso de consola em série ativado por padrão.
CentOS      | Acesso de consola em série ativado por padrão.
Debian      | Acesso de consola em série ativado por padrão.
Ubuntu      | Acesso de consola em série ativado por padrão.
CoreOS      | Acesso de consola em série ativado por padrão.
SUSE        | As imagens SLES mais recentes disponíveis no Azure têm acesso à consola em série ativada por padrão. Se estiver a utilizar versões mais antigas (10 ou mais cedo) de SLES no Azure, consulte o artigo da [KB](https://www.novell.com/support/kb/doc.php?id=3456486) para permitir a consola em série.
Oracle Linux        | Acesso de consola em série ativado por padrão.

### <a name="custom-linux-images"></a>Imagens personalizadas de Linux
Para ativar a consola em série para a sua imagem Personalizada De VM Linux, ative o acesso à consola no ficheiro */etc/inittab* para executar um terminal em `ttyS0`. Por exemplo: `S0:12345:respawn:/sbin/agetty -L 115200 console vt102`. Também pode precisar de dar um getty no ttyS0. Isto pode ser feito com `systemctl start serial-getty@ttyS0.service`.

Também vai querer adicionar ttys0 como destino para a saída em série. Para obter mais informações sobre a configuração de uma imagem personalizada para trabalhar com a consola em série, consulte os requisitos gerais do sistema na [Create e carregue um Linux VHD em Azure](https://aka.ms/createuploadvhd#general-linux-system-requirements).

Se estiver a construir um núcleo personalizado, considere permitir estas bandeiras de núcleo: `CONFIG_SERIAL_8250=y` e `CONFIG_MAGIC_SYSRQ_SERIAL=y`. O ficheiro de configuração está tipicamente localizado no *caminho /boot/.*

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Cenários comuns para aceder à Consola Em Série

Cenário          | Ações na Consola Em Série
:------------------|:-----------------------------------------
*Ficheiro FSTAB* quebrado | Prima a tecla **Enter** para continuar e utilizar um editor de texto para corrigir o ficheiro *FSTAB.* Pode ser necessário estar em modo de utilizador único para o fazer. Para mais informações, consulte a secção de consola em série de [Como corrigir problemas](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) de fstab e utilize a [consola em série para aceder ao MODO GRUB e](serial-console-grub-single-user-mode.md)ao modo de utilizador único .
Regras de firewall incorreta |  Se tiver configurado os iptables para bloquear a conectividade SSH, pode utilizar a consola em série para interagir com o seu VM sem precisar de SSH. Mais detalhes podem ser encontrados na página do [homem iptables](https://linux.die.net/man/8/iptables).<br>Da mesma forma, se o seu firewall estiver a bloquear o acesso sSH, pode aceder ao VM através de uma consola em série e reconfigurar a firewall. Mais detalhes podem ser encontrados na [documentação firewalld](https://firewalld.org/documentation/).
Danos/verificação de sistema de ficheiros | Por favor, veja que a secção de consola seleção em série do [Azure Linux VM não pode arrancar devido a erros no sistema de ficheiros](https://support.microsoft.com/en-us/help/3213321/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck) para mais detalhes sobre sistemas de ficheiros corrompidos com problemas utilizando a consola em série.
Problemas de configuração do SSH | Aceder à consola de série e alterar as definições. A consola em série pode ser utilizada independentemente da configuração SSH de um VM, uma vez que não requer que o VM tenha conectividade de rede para funcionar. Um guia de resolução de problemas está disponível nas [ligações SSH da Troubleshoot a um VM Azure Linux que falha, falha ou é recusado](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-ssh-connection). Mais detalhes estão disponíveis em etapas detalhadas de resolução de [problemas da SSH para questões que ligam a um Linux VM em Azure](./detailed-troubleshoot-ssh-connection.md)
Interagir com o carregador de inicialização | Reinicie o vM a partir da lâmina da consola em série para aceder à GRUB no seu VM Linux. Para mais detalhes e informações específicas para distro, consulte [Utilize a consola em série para aceder ao MODO GRUB e](serial-console-grub-single-user-mode.md)ao modo de utilizador único .

## <a name="disable-the-serial-console"></a>Desativar a consola em série

Por padrão, todas as subscrições têm acesso à consola em série ativada. Pode desativar a consola em série ao nível de subscrição ou ao nível de conjunto de escala vm/máquina virtual. Para obter instruções detalhadas, visite [Enable e desative a consola em série Azure](./serial-console-enable-disable.md).

## <a name="serial-console-security"></a>Segurança da consola de série

### <a name="access-security"></a>Segurança de acesso
O acesso à consola em série está limitado aos utilizadores que tenham uma função de acesso ao [Virtual Machine Contributor](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) ou superior à máquina virtual. Se o seu inquilino do Diretório Ativo Azure necessitar de autenticação multi-factor (MFA), então o acesso à consola em série também necessitará de MFA porque o acesso da consola em série é através do [portal Azure.](https://portal.azure.com)

### <a name="channel-security"></a>Segurança de canal
Todos os dados que são enviados e volta são encriptados na conexão.

### <a name="audit-logs"></a>Registos de auditoria
Todo o acesso à consola em série está atualmente registado nos [registos](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) de diagnóstico de arranque da máquina virtual. Acesso a estes registos são propriedade e controlado pelo administrador de máquina virtual do Azure.

> [!CAUTION]
> Sem palavras-passe de acesso para a consola são registadas. No entanto, se os comandos são executados dentro da consola contém ou palavras-passe, segredos, os nomes de utilizador ou qualquer outra forma de informações de identificação pessoal (PII) de saída, aqueles serão escritos nos registos de diagnóstico de arranque VM. Eles serão escritos, juntamente com todos os outro texto visível, como parte da implementação de volta a rolagem da consola de série função. Estes registos são circulares e apenas indivíduos com permissões de leitura à conta de armazenamento de diagnóstico tem acesso aos mesmos. Se estiver a inserir quaisquer comandos de dataor que contenham segredos ou PII, recomendamos a utilização de SSH a menos que a consola em série seja absolutamente necessária.

### <a name="concurrent-usage"></a>Utilização em simultâneo
Se um usuário estiver conectado à consola de série e outro utilizador com êxito pedidos de acesso a essa mesma máquina virtual, o primeiro utilizador será desligado e o segundo utilizador ligado à mesma sessão.

> [!CAUTION]
> Isto significa que um utilizador desligado não será desligado. A capacidade de impor um logout após a desconexão (utilizando sIGHUP ou mecanismo semelhante) ainda está no roteiro. Para windows existe um tempo de tempo automático ativado na Consola Administrativa Especial (SAC); no entanto, para o Linux pode configurar o intervalo de tempo terminal. Para isso, adicione `export TMOUT=600` no ficheiro *.bash_profile* ou *.profile* para o utilizador que utiliza para iniciar sessão na consola. Esta definição irá cronometre a sessão após 10 minutos.

## <a name="accessibility"></a>Acessibilidade
A acessibilidade é um foco chave para a Consola Em Série Azure. Para tal, garantimos que a consola em série está totalmente acessível.

### <a name="keyboard-navigation"></a>Navegação do teclado
Utilize a tecla **Tab** no seu teclado para navegar na interface de consola em série a partir do portal Azure. Sua localização será realçada na tela. Para deixar o foco da janela da consola em série, prima **CTRL**+**F6** no teclado.

### <a name="use-serial-console-with-a-screen-reader"></a>Utilize a Consola Em Série com um leitor de ecrã
A consola de série tem suporte de leitor de ecrã incorporado. Navegação com um leitor de ecrã ativado, permitirá que o texto alternativo para o botão selecionado atualmente a ser lido em voz alta pelo leitor de ecrã.

## <a name="known-issues"></a>Problemas conhecidos
Estamos cientes de alguns problemas com a consola em série e o sistema operativo da VM. Aqui está uma lista destas questões e passos para mitigação para os VMs Linux. Estas questões e atenuações aplicam-se tanto para os VMs como para as instâncias de conjunto de máquinas virtuais. Se estes não corresponderem ao erro que está a ver, veja os erros comuns de serviço da consola em série nos [erros da Consola Série Comum](./serial-console-errors.md).

Problema                           |   Mitigação
:---------------------------------|:--------------------------------------------|
Pressionar **Introduza** depois de o banner de ligação não causar a visualização de uma solicitação de entrada. | A GRUB pode não ser configurada corretamente. Executar os seguintes comandos: `grub2-mkconfig -o /etc/grub2-efi.cfg` e/ou `grub2-mkconfig -o /etc/grub2.cfg`. Para mais informações, consulte [hit enter não faz nada](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Este problema pode ocorrer se estiver a executar um VM personalizado, um aparelho endurecido ou config GRUB que faz com que o Linux não se conectem à porta de série.
O texto da consola em série ocupa apenas uma parte do tamanho do ecrã (muitas vezes depois de usar um editor de texto). | As consolas em série não suportam negociar sobre o tamanho da janela[(RFC 1073),](https://www.ietf.org/rfc/rfc1073.txt)o que significa que não haverá nenhum sinal SIGWINCH enviado para atualizar o tamanho do ecrã e o VM não terá conhecimento do tamanho do seu terminal. Instale xterm ou um utilitário semelhante para lhe fornecer o comando `resize` e, em seguida, executar `resize`.
Colar longas seqüências de caracteres não funciona. | A consola de série limita o comprimento de cadeias de caracteres colado no terminal para 2048 carateres para evitar sobrecarregar a largura de banda da porta serial.
Entrada de teclado errático nas imagens SLES BYOS. A entrada do teclado é apenas esporadicamente reconhecida. | Este é um problema com o pacote Plymouth. O Plymouth não deve ser executado em Azure, uma vez que não precisa de um ecrã de respingo e o Plymouth interfere com a capacidade da plataforma de utilizar a Consola Em Série. Retire Plymouth com `sudo zypper remove plymouth` e, em seguida, reiniciar. Em alternativa, modifique a linha de núcleo do seu config GRUB, aperto `plymouth.enable=0` até ao fim da linha. Pode fazê-lo [editando a entrada de arranque no momento](https://aka.ms/serialconsolegrub#single-user-mode-in-suse-sles)do arranque , ou editando a linha GRUB_CMDLINE_LINUX em `/etc/default/grub`, reconstruindo a GRUB com `grub2-mkconfig -o /boot/grub2/grub.cfg`, e depois reiniciando.


## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**Q. Como posso enviar feedback?**

R. Fornecer feedback criando um problema GitHub em https://aka.ms/serialconsolefeedback. Alternativamente (menos preferencial), pode enviar feedback através de azserialhelp@microsoft.com ou na categoria de máquina virtual de https://feedback.azure.com.

**Q. A consola em série suporta cópia/pasta?**

R. Sim. Utilize **o Turno+CTRL**+**C** e **Ctrl**+**Shift**+**V** para copiar e colar no terminal.

**Q. Posso usar uma consola em série em vez de uma ligação SSH?**

R. Embora esta utilização possa parecer tecnicamente possível, a consola em série destina-se a ser usada principalmente como uma ferramenta de resolução de problemas em situações em que a conectividade via SSH não é possível. Recomendamos que não utilize a consola em série como substituição de SSH pelas seguintes razões:

- A consola em série não tem tanta largura de banda como a SSH. Porque é uma ligação só por texto, mais interações gui-pesadas são difíceis.
- O acesso à consola em série só é possível utilizando um nome de utilizador e uma palavra-passe. Uma vez que as teclas SSH são muito mais seguras do que as combinações de username/password, do ponto de vista de segurança de entrada, recomendamos sSH em vez de consola em série.

**Q. Quem pode ativar ou desativar a consola em série para a minha subscrição?**

R. Para ativar ou desativar a consola de série a um nível de toda a subscrição, tem de ter permissões de escrita para a subscrição. As funções que tem permissão de escrita incluem funções de administrador ou proprietário. Funções personalizadas também podem ter permissões de escrita.

**Q. Quem pode aceder à consola em série para o meu conjunto de escala de máquinas VM/virtual?**

R. Deve ter a função de Colaborador de Máquina Virtual ou superior para um conjunto de escala vm ou máquina virtual para aceder à consola em série.

**Q. A minha consola em série não está a exibir nada, o que faço?**

R. A imagem é provavelmente mal configurada para acesso à consola de série. Para obter informações sobre a configuração da sua imagem para ativar a consola em série, consulte a disponibilidade de distribuição da [consola em série Linux](#serial-console-linux-distribution-availability).

**Q. A consola em série está disponível para conjuntos de escala de máquinas virtuais?**

R. Sim, é! Ver [consola em série para conjuntos](serial-console-overview.md#serial-console-for-virtual-machine-scale-sets) de escala de máquina virtual

**P. Se eu configurar a minha balança de máquinas VM ou virtual utilizando apenas a autenticação da chave SSH, ainda posso usar a consola em série para ligar à minha instância de conjunto de métricas vM/máquina virtual?**

R. Sim. Uma vez que a consola em série não necessita de teclas SSH, basta configurar uma combinação de nome de utilizador/palavra-passe. Pode fazê-lo selecionando **a palavra-passe Reset** no portal Azure e utilizando essas credenciais para iniciar sessão na consola em série.

## <a name="next-steps"></a>Passos seguintes
* Utilize a consola em série para aceder ao MODO GRUB e ao [modo de utilizador único](serial-console-grub-single-user-mode.md).
* Utilize a consola em série para [chamadas NMI e SysRq](serial-console-nmi-sysrq.md).
* Aprenda a usar a consola em série para [permitir a GRUB em vários distros](serial-console-grub-proactive-configuration.md)
* A consola em série também está disponível para [VMs do Windows.](../windows/serial-console.md)
* Saiba mais sobre diagnósticos de [arranque.](boot-diagnostics.md)

