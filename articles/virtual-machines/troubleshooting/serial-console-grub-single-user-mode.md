---
title: Consola de série do Azure para GRUB e modo de utilizador único | Documentos da Microsoft
description: A utilizar a consola de série para grub em máquinas virtuais do Azure.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: ca2523a1101a21740a318a304f9bec491d4de2f9
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58106241"
---
# <a name="use-serial-console-to-access-grub-and-single-user-mode"></a>Utilizar a consola de série para aceder a GRUB e modo de utilizador único
GRUB é o GRand Unified carregador de inicialização, que é provável que a primeira coisa que verá quando se reinicia uma VM. Uma vez que ele exibe antes do sistema operativo foi iniciado, não está acessível através de SSH. Em GRUB está capaz de modificar a configuração de arranque para inicializar em modo de utilizador único, entre outras coisas.

Modo de utilizador único é um ambiente mínimo com a funcionalidade mínima. Pode ser útil para investigar problemas de arranque, problemas de sistema de ficheiros ou problemas de rede. Menos de serviços podem ser executadas em segundo plano e, consoante o runlevel, um sistema de ficheiros poderá não até mesmo ser automaticamente montado.

Também é útil em situações em que a VM só pode ser configurada para aceitar as chaves SSH para iniciar sessão no modo de utilizador único. Neste caso, poderá utilizar o modo de utilizador único para criar uma conta com a autenticação de palavra-passe. Tenha em atenção que o serviço de consola de série será apenas permitir que os utilizadores com acesso de nível de Contribuidor ou superior para aceder à consola de série de uma VM.

Para entrar no modo de utilizador único, terá de introduzir o GRUB quando a VM está a arrancar e modificar a configuração da inicialização no GRUB. Instruções detalhadas para a inserção de GRUB estão abaixo. Em geral, pode utilizar o botão de reinício dentro da consola de série de VM para reiniciar a VM e Mostrar GRUB se a VM foi configurada para mostrar o GRUB.

![Botão de Linux Serial consola reinicie](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-bar.png)

## <a name="general-grub-access"></a>Acesso GRUB geral
Para acessar o GRUB, terá de reiniciar a VM, mantendo o painel de consola de série aberto. Algumas distribuições irão exigir a entrada de teclado para mostrar o GRUB, enquanto outros automaticamente mostrará GRUB por alguns segundos e permitir a entrada de teclado do usuário cancelar o tempo limite.

Convém garantir que o GRUB está ativado na sua VM para que seja possível para o modo de utilizador único de acesso. Dependendo de sua distribuição, pode haver algum trabalho de configuração para se certificar de que o GRUB está ativado. Informações específicas de distribuição estão disponíveis abaixo e no [esta ligação](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/).

### <a name="restart-your-vm-to-access-grub-in-serial-console"></a>Reinicie a VM para aceder a GRUB na consola de série
Pode reiniciar a VM dentro da consola de série ao navegar para o botão de energia e clicar em "VM reiniciar". Isto irá iniciar um reinício VM, e verá uma notificação no portal do Azure sobre o reinício.
Reiniciar a VM também pode ser feito com uma SysRq `'b'` comando se [SysRq](./serial-console-nmi-sysrq.md) está ativada. Siga as instruções de distribuição específicos abaixo para saber o que esperar de GRUB quando reiniciar.

![Reinício da consola de série do Linux](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-ubuntu.gif)

## <a name="general-single-user-mode-access"></a>Acesso de modo de utilizador único geral
Acesso manual para o modo de utilizador único pode ser necessários em situações onde não tiver configurado uma conta com a autenticação de palavra-passe. Terá de modificar a configuração de GRUB para introduzir manualmente o modo de utilizador único. Uma vez que o tiver feito isso, consulte Utilizar modo de utilizador único para repor ou adicionar uma palavra-passe para obter mais instruções.

Em casos em que a VM não consegue arranque, Distribuições, muitas vezes, automaticamente irão remover em modo de utilizador único ou modo de emergência. No entanto, outras pessoas, exigem configuração adicional antes de pode soltar no modo de utilizador único ou de emergência automaticamente (como configurar uma palavra-passe de raiz).

### <a name="use-single-user-mode-to-reset-or-add-a-password"></a>Utilizar o modo de utilizador único para repor ou adicionar uma palavra-passe
Assim que estiver no modo de utilizador único, faça o seguinte para adicionar um novo utilizador com privilégios sudo:
1. Executar `useradd <username>` para adicionar um utilizador
1. Executar `sudo usermod -a -G sudo <username>` deve conceder ao utilizador novo privilégios de raiz
1. Utilize `passwd <username>` para definir a palavra-passe para o novo utilizador. Em seguida, poderá iniciar sessão como o novo utilizador


## <a name="access-for-red-hat-enterprise-linux-rhel"></a>Acesso do Red Hat Enterprise Linux (RHEL)
RHEL cairá em modo de utilizador único automaticamente se ele não consegue arrancar normalmente. No entanto, se não tiver definido o acesso de raiz para o modo de utilizador único, não terá uma palavra-passe de raiz e será possível iniciar sessão. Existe uma solução (consulte "Introduzir manualmente o modo de utilizador único" abaixo), mas a sugestão é definida inicialmente o acesso de raiz.

### <a name="grub-access-in-rhel"></a>Acesso GRUB no RHEL
RHEL vem com GRUB ativada na. Para introduzir GRUB, reiniciar a VM com `sudo reboot` e pressione qualquer tecla. Verá a tela GRUB aparecer.

> Nota: Red Hat também fornece documentação para inicialização no modo de entra em ação, de modo de emergência, de modo de depuração e repor a palavra-passe de raiz. [Clique aqui para aceder ao mesmo](https://aka.ms/rhel7grubterminal).

### <a name="set-up-root-access-for-single-user-mode-in-rhel"></a>Configurar o acesso de raiz para o modo de utilizador único no RHEL
Modo de utilizador único no RHEL exige que o utilizador de raiz ser ativada, o que está desabilitado por predefinição. Se tiver uma necessidade para ativar o modo de utilizador único, utilize as instruções seguintes:

1. Iniciar sessão sistema de Red Hat através de SSH
1. Mude para a raiz
1. Ativar a palavra-passe de utilizador de raiz
    * `passwd root` (definido uma palavra-passe de raiz fortes)
1. Certifique-se de que o utilizador de raiz pode apenas iniciar sessão através de ttyS0
    * `edit /etc/ssh/sshd_config` e certifique-se de que PermitRootLogIn está definido ou nenhum
    * `edit /etc/securetty file` para permitir apenas o início de sessão por meio de ttyS0

Agora se o sistema for inicializado no modo de utilizador único pode iniciar sessão através de palavra-passe de raiz.

Como alternativa para o modo de utilizador único RHEL 7.4 + ou 6.9 + pode ativar no GRUB pede-lhe, veja as instruções [aqui](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single)

### <a name="manually-enter-single-user-mode-in-rhel"></a>Introduzir manualmente o modo de utilizador único no RHEL
Se tiver configurado o GRUB e raiz aceder com as instruções acima, em seguida, pode entrar no modo de utilizador único com as instruções seguintes:

1. Prima "Esc" ao reiniciar a VM para introduzir o GRUB
1. No GRUB, prima "e" para editar o sistema operacional selecionado que pretende efetuar o arranque em (normalmente, a primeira linha)
1. Localize a linha de kernel - a no Azure, isto irá iniciar com `linux16`
1. Prima Ctrl + E para ir para o final da linha
1. Adicione o seguinte ao final da linha: `systemd.unit=rescue.target`
    * Isso será inicializado em modo de utilizador único. Se pretender utilizar o modo de emergência, adicionar `systemd.unit=emergency.target` ao final da linha em vez de `systemd.unit=rescue.target`
1. Prima Ctrl + X para sair e reiniciar com as definições aplicadas
1. Será solicitado a palavra-passe de administrador antes de poder entrar no modo de utilizador único – Esta é a mesma palavra-passe que criou nas instruções acima

    ![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-enter-emergency-shell.gif)

### <a name="enter-single-user-mode-without-root-account-enabled-in-rhel"></a>Introduza o modo de utilizador único sem conta raiz habilitada no RHEL
Se não passou os passos acima para permitir que o utilizador de raiz, ainda pode repor a palavra-passe de raiz. Utilize as instruções seguintes:

> Nota: Se estiver a utilizar SELinux, certifique-se seguiu os passos adicionais descritos na documentação do Red Hat [aqui](https://aka.ms/rhel7grubterminal) ao repor a palavra-passe de raiz.

1. Prima "Esc" ao reiniciar a VM para introduzir o GRUB
1. No GRUB, prima "e" para editar o sistema operacional selecionado que pretende efetuar o arranque em (normalmente, a primeira linha)
1. Localize a linha de kernel - a no Azure, isto irá iniciar com `linux16`
1. Adicione `rd.break` no final da linha, aí é garantir um espaço antes `rd.break` (veja o exemplo abaixo)
    - Isto interromperá o processo de inicialização antes de controlo é transmitido a partir `initramfs` para `systemd`, conforme descrito na documentação do Red Hat [aqui](https://aka.ms/rhel7rootpassword).
1. Prima Ctrl + X para sair e reiniciar com as definições aplicadas
1. Depois de efetuar o arranque, será largado em modo de emergência com um sistema de ficheiros só de leitura. Introduza `mount -o remount,rw /sysroot` no shell para voltar a montar o sistema de ficheiros de raiz com permissões de leitura/escrita
1. Depois de efetuar o arranque no modo de utilizador único, escreva `chroot /sysroot` alternar para o `sysroot` desbloqueado por
1. Agora, está raiz. Pode repor a palavra-passe de raiz com `passwd` e, em seguida, utilize as instruções acima para entrar no modo de utilizador único. Tipo de `reboot -f` para reiniciar o computador quando tiver terminado.

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-emergency-mount-no-root.gif)

> Nota: A executar as instruções acima irá diminuir o no shell de emergência, pelo que também pode executar tarefas como edição `fstab`. No entanto, a sugestão geralmente aceita é para repor a palavra-passe de raiz e usá-lo para entrar no modo de utilizador único.


## <a name="access-for-centos"></a>Acesso para CentOS
Muito como Red Hat Enterprise Linux, modo de utilizador único no CentOS requer GRUB e o utilizador raiz seja ativado.

### <a name="grub-access-in-centos"></a>Acesso GRUB no CentOS
CentOS vem com GRUB ativada na. Para introduzir GRUB, reiniciar a VM com `sudo reboot` e pressione qualquer tecla. Verá a tela GRUB aparecer.

### <a name="single-user-mode-in-centos"></a>Modo de utilizador único no CentOS
Siga as instruções para RHEL acima para ativar o modo de utilizador único no CentOS.

## <a name="access-for-ubuntu"></a>Acesso para Ubuntu
Imagens do Ubuntu não necessitam de uma palavra-passe de raiz. Se o sistema efetua o arranque no modo de utilizador único, pode utilizá-la sem credenciais adicionais.

### <a name="grub-access-in-ubuntu"></a>Acesso GRUB no Ubuntu
Para acessar o GRUB, prima e mantenha premido "Esc" enquanto a VM está a arrancar.

Por predefinição, o Ubuntu imagens poderão não mostrar automaticamente a tela GRUB. Isso pode ser alterado com as instruções seguintes:
1. Abra `/etc/default/grub.d/50-cloudimg-settings.cfg` num editor de texto à sua escolha
1. Alterar o `GRUB_TIMEOUT` valor para um valor diferente de zero
1. Abra `/etc/default/grub` num editor de texto à sua escolha
1. Comente o `GRUB_HIDDEN_TIMEOUT=1` linha
1. Execute `sudo update-grub`

### <a name="single-user-mode-in-ubuntu"></a>Modo de utilizador único no Ubuntu
Ubuntu cairá em modo de utilizador único automaticamente se ele não consegue arrancar normalmente. Para introduzir manualmente o modo de utilizador único, utilize as instruções seguintes:

1. Do GRUB, prima "e" para editar sua entrada de arranque (a entrada do Ubuntu)
1. Procure a linha que começa com `linux`, em seguida, procure `ro`
1. Adicione `single` depois de `ro`, garantir que existe um espaço antes e depois `single`
1. Prima Ctrl + X para reiniciar com estas definições e introduza o modo de utilizador único

### <a name="using-grub-to-invoke-bash-in-ubuntu"></a>Usando o GRUB para invocar o bash no Ubuntu
Poderão existir situações (por exemplo, uma palavra-passe de raiz esquecidas) em que ainda pode ser não é possível aceder de modo de utilizador único na sua VM do Ubuntu depois de tentar as instruções descritas acima. Também pode informar o kernel para executar /bin/bash como init, em vez do sistema init, que irá dar-lhe uma shell bash e permitir a manutenção do sistema. Utilize as instruções seguintes:

1. Do GRUB, prima "e" para editar sua entrada de arranque (a entrada do Ubuntu)
1. Procure a linha que começa com `linux`, em seguida, procure `ro`
1. Substitua `ro` com `rw init=/bin/bash`
    - Esta ação irá montar o sistema de ficheiros como leitura / escrita e utilizar /bin/bash como o processo de inicialização
1. Prima Ctrl + X para reiniciar o computador com estas definições

## <a name="access-for-coreos"></a>Acesso para CoreOS
Modo de utilizador único no CoreOS requer GRUB seja ativado.

### <a name="grub-access-in-coreos"></a>Acesso GRUB no CoreOS
Para acessar o GRUB, pressione qualquer tecla quando a VM está a arrancar.

### <a name="single-user-mode-in-coreos"></a>Modo de utilizador único no CoreOS
CoreOS cairá em modo de utilizador único automaticamente se ele não consegue arrancar normalmente. Para introduzir manualmente o modo de utilizador único, utilize as instruções seguintes:
1. Do GRUB, prima "e" para editar sua entrada de arranque
1. Procure a linha que começa com `linux$`. Deve haver 2, encapsulado em diferentes if/else cláusulas
1. Acrescentar `coreos.autologin=ttyS0` no final de ambos `linux$` linhas
1. Prima Ctrl + X para reiniciar com estas definições e introduza o modo de utilizador único

## <a name="access-for-suse-sles"></a>Acesso para SUSE SLES
Imagens mais recentes do SLES 12 SP3 + permitem o acesso através da consola de série, no caso do sistema for inicializado no modo de emergência.

### <a name="grub-access-in-suse-sles"></a>Acesso GRUB no SUSE SLES
Acesso GRUB no SLES requer a configuração do carregador de inicialização por meio do YaST. Para tal, siga estas instruções:

1. SSH para a sua VM de SLES e execute `sudo yast bootloader`. Utilize o `tab` chave, `enter` chave e teclas de seta para navegar pelo menu.
1. Navegue para `Kernel Parameters`e verificar `Use serial console`.
1. Adicionar `serial --unit=0 --speed=9600 --parity=no` para os argumentos de consola

1. Pressione F10 para guardar as definições e sair
1. Para introduzir GRUB, reinicie a VM e pressione qualquer tecla durante a seqüência de inicialização para fazer o GRUB permanecer no ecrã
    - O tempo limite predefinido para GRUB é 1s. Pode modificar esta alterando o `GRUB_TIMEOUT` variável no `/etc/default/grub`

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-sles-yast-grub-config.gif)

### <a name="single-user-mode-in-suse-sles"></a>Modo de utilizador único no SUSE SLES
Se o SLES não conseguem arrancar normalmente será automaticamente colocada numa shell de emergência. Para introduzir manualmente o shell de emergência, utilize as instruções seguintes:

1. Do GRUB, prima "e" para editar sua entrada de arranque (a entrada SLES)
1. Procure a linha de kernel iniciará com `linux`
1. Acrescentar `systemd.unit=emergency.target` ao final da linha
1. Prima Ctrl + X para reiniciar com estas definições e introduza o shell de emergência
   > Tenha em atenção que irá ser deixado no shell de emergência com um _só de leitura_ sistema de ficheiros. Se desejar fazer qualquer edições em todos os ficheiros, terá de voltar a montar o sistema de ficheiros com permissões de leitura / escrita. Para tal, introduza `mount -o remount,rw /` no shell

## <a name="access-for-oracle-linux"></a>Acesso para Oracle Linux
Muito como Red Hat Enterprise Linux, modo de utilizador único no Oracle Linux requer GRUB e o utilizador raiz seja ativado.

### <a name="grub-access-in-oracle-linux"></a>Acesso GRUB no Oracle Linux
Oracle Linux vem com GRUB ativada na. Para introduzir GRUB, reiniciar a VM com `sudo reboot` e prima "Esc". Verá a tela GRUB aparecer.

### <a name="single-user-mode-in-oracle-linux"></a>Modo de utilizador único no Oracle Linux
Siga as instruções para RHEL acima para ativar o modo de utilizador único no Oracle Linux.

## <a name="next-steps"></a>Passos Seguintes
* A página de documentação do Linux de consola de série principal está localizada [aqui](serial-console-linux.md).
* Saiba como utilizar a consola de série para [ativar GRUB em várias distribuições](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/)
* Utilizar a consola de série para [chamadas NMI e SysRq](serial-console-nmi-sysrq.md)
* Também está disponível para a consola de série [Windows](serial-console-windows.md) VMs
* Saiba mais sobre [diagnósticos de arranque](boot-diagnostics.md)
