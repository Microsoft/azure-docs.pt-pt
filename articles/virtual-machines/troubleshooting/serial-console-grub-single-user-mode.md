---
title: Consola em série Azure para GRUB e modo de utilizador único [ Microsoft Docs
description: Este artigo descreve como usar a Consola Em Série para GRUB em máquinas virtuais Azure.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/06/2019
ms.author: alsin
ms.openlocfilehash: 06cb3fe5d551ddfc95fcbd37cd9620adebd825c5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "70883926"
---
# <a name="use-serial-console-to-access-grub-and-single-user-mode"></a>Utilize a Consola em Série para aceder ao modo GRUB e ao modo de utilizador único
GRand Botão Unificado (GRUB) é provavelmente a primeira coisa que se vê quando se inicia uma máquina virtual (VM). Como é exibido antes do sistema operativo ter começado, o GRUB não é acessível via SSH. No GRUB, pode modificar a configuração da sua bota para iniciar o modo de utilizador único, entre outras coisas.

O modo de utilizador único é um ambiente mínimo com uma funcionalidade mínima. Pode ser útil para investigar problemas de boot, problemas de sistema de ficheiros ou problemas de rede. Menos serviços podem ser executados em segundo plano e, dependendo do nível de execução, um sistema de ficheiros pode nem sequer ser montado automaticamente.

O modo de utilizador único também é útil em situações em que o seu VM pode estar configurado para aceitar apenas chaves SSH para iniciar sessão. Neste caso, poderá utilizar o modo de utilizador único para criar uma conta com autenticação de senha. 

> [!NOTE]
> O serviço de Consola seletiva permite que apenas os utilizadores com nível *de contribuinte* ou permissões superiores acedam à consola em série de um VM.

Para introduzir o modo de utilizador único, introduza o GRUB quando o vM estiver a arrancar e modifique a configuração da bota em GRUB. Consulte as instruções detalhadas para introduzir a GRUB na secção seguinte. Em geral, se o seu VM tiver sido configurado para exibir GRUB, pode utilizar o botão de reinício dentro da consola de série do VM para reiniciar o VM e exibir GRUB.

![O botão de reinício da consola em série Linux](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-bar.png)

## <a name="general-grub-access"></a>Acesso geral da GRUB
Para aceder ao GRUB, reinicie o seu VM enquanto o painel da Consola Em Série está aberto. Algumas distribuições requerem entrada de teclado para mostrar GRUB, e outras mostram automaticamente GRUB por alguns segundos para permitir que a entrada do teclado do utilizador cancele o tempo de tempo.

Para poder aceder ao modo de utilizador único, pretende garantir que o GRUB está ativado no seu VM. Dependendo da sua distribuição, alguns trabalhos de configuração podem ser necessários para garantir que a GRUB está ativada. Para obter informações específicas de distribuição, consulte a secção seguinte e o nosso Suporte para Linux na página [Azure.](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/)

### <a name="restart-your-vm-to-access-grub-in-serial-console"></a>Reinicie o seu VM para aceder ao GRUB em Consola em Série
Pode reiniciar o vM dentro da Consola de Série pairando sobre o botão **Restart** e, em seguida, selecionando **Restart VM**. Uma notificação sobre o reinício é apresentada na parte inferior do painel.

Também pode reiniciar o seu VM executando um comando SysRq b se o [SysRq](./serial-console-nmi-sysrq.md) estiver ativado. Para saber o que esperar da GRUB quando reiniciar, consulte as instruções específicas de distribuição nas próximas secções.

![Consola em série linux reinicia](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-ubuntu.gif)

## <a name="general-single-user-mode-access"></a>Acesso geral ao modo de utilizador único
Pode necessitar de acesso manual ao modo de utilizador único quando ainda não configurar uma conta com autenticação de senha. Modifique a configuração GRUB para introduzir manualmente o modo de utilizador único. Depois de ter feito isto, consulte a secção "Use o modo de utilizador único para redefinir ou adicionar uma palavra-passe" para obter mais instruções.

Se o VM não conseguir arrancar, as distribuições muitas vezes o deixam automaticamente no modo de utilizador único ou no modo de emergência. Outras distribuições, no entanto, requerem configuração adicional, como a configuração de uma palavra-passe raiz, antes que possam deixá-lo em modo de utilizador único ou modo de emergência automaticamente.

### <a name="use-single-user-mode-to-reset-or-add-a-password"></a>Utilize o modo de utilizador único para redefinir ou adicionar uma palavra-passe
Depois de se estar em modo de utilizador único, adicione um novo utilizador com privilégios sudo, fazendo o seguinte:
1. Corra `useradd <username>` para adicionar um utilizador.
1. Corra `sudo usermod -a -G sudo <username>` para conceder ao novo utilizador privilégios de raiz.
1. Utilize `passwd <username>` para definir a palavra-passe para o novo utilizador. Em seguida, pode iniciar sessão como novo utilizador.


## <a name="access-for-red-hat-enterprise-linux-rhel"></a>Acesso para Red Hat Enterprise Linux (RHEL)
Se o RHEL não conseguir arrancar normalmente, deixa-o automaticamente no modo de utilizador único. No entanto, se não tiver configurado o acesso à raiz para o modo de utilizador único, não tem uma palavra-passe de raiz e não pode iniciar sessão. Existe uma salição (consulte a secção "Entrar manualmente no modo de utilizador único no RHEL"), mas sugerimos que instale inicialmente o acesso à raiz.

### <a name="grub-access-in-rhel"></a>Acesso GRUB em RHEL
O RHEL vem com a GRUB ativada para fora da caixa. Para entrar no GRUB, reinicie o vM em execução `sudo reboot`e, em seguida, pressione qualquer tecla. O painel GRUB deve ser exibido. Se não estiver, certifique-se de que as seguintes linhas estão presentes no seu ficheiro GRUB (`/etc/default/grub`):

**Para rhel 8**

```
GRUB_TIMEOUT=5
GRUB_TERMINAL="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
```

**Para rhel 7**

```
GRUB_TIMEOUT=5
GRUB_TERMINAL_OUTPUT="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300 net.ifnames=0"
```

> [!NOTE]
> A Red Hat também fornece documentação para o arranque no modo de resgate, modo de emergência ou modo Debug, e para redefinir a palavra-passe de raiz. Para obter instruções, consulte [a edição do menu Terminal durante](https://aka.ms/rhel7grubterminal)o arranque .

### <a name="set-up-root-access-for-single-user-mode-in-rhel"></a>Configurar o acesso à raiz para o modo de utilizador único no RHEL
O utilizador da raiz é desativado por defeito. O modo de utilizador único no RHEL requer ativação do utilizador radicular. Se precisar de ativar o modo de utilizador único, utilize as seguintes instruções:

1. Inscreva-se no sistema Red Hat via SSH.
1. Mude para raiz.
1. Ativar a palavra-passe para o utilizador raiz fazendo o seguinte:
    * Executar `passwd root` (definir uma senha de raiz forte).
1. Certifique-se de que o utilizador da raiz só pode iniciar sessão através do ttyS0 fazendo o seguinte:  
    a. Executar, `edit /etc/ssh/sshd_config`e certifique-se de que `no`a PermitRootLogIn está definida para .  
    b. Corra `edit /etc/securetty file` para permitir o insessão apenas via ttyS0.

Agora, se o sistema entrar no modo de utilizador único, pode iniciar sessão com a palavra-passe raiz.

Alternativamente, para RHEL 7.4+ ou 6.9+, para ativar o modo de utilizador único nas instruções GRUB, consulte o [Booting no modo de utilizador único](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single).

### <a name="manually-enter-single-user-mode-in-rhel"></a>Introduza manualmente o modo de utilizador único no RHEL
Se tiver configurado o acesso ao GRUB e à raiz utilizando as instruções anteriores, pode introduzir o modo de utilizador único fazendo o seguinte:

1. Para entrar no GRUB, prima Esc enquanto reinicia o VM.
1. Na GRUB, pressione E para editar o Sistema operativo em que pretende iniciar. O SO é geralmente listado na primeira linha.
1. Encontre a linha de núcleo. Em Azure, começa com *linux16*.
1. Pressione Ctrl+E para ir até ao fim da linha.
1. No final da linha, adicione *sistemad.unit=rescue.target*.
    
    Esta ação insere-o no modo de utilizador único. Se pretender utilizar o modo de emergência, adicione o *systemd.unit=emergency.target* até ao fim da linha (em vez de *sistemad.unit=rescue.target*).

1. Prima Ctrl+X para sair e reiniciar com as definições aplicadas.

   Será solicitado para a palavra-passe do administrador antes de poder introduzir o modo de utilizador único. Esta palavra-passe é a que criou nas instruções anteriores.

    ![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-enter-emergency-shell.gif)

### <a name="enter-single-user-mode-without-root-account-enabled-in-rhel"></a>Introduza o modo de utilizador único sem conta raiz ativada no RHEL
Se não ativar o utilizador da raiz seguindo as instruções anteriores, ainda pode redefinir a sua palavra-passe raiz fazendo o seguinte:

> [!NOTE]
> Se estiver a usar o SELinux, quando redefinir a palavra-passe de raiz, certifique-se de seguir os passos adicionais descritos na [documentação](https://aka.ms/rhel7grubterminal)do Chapéu Vermelho .

1. Para entrar no GRUB, prima Esc enquanto reinicia o VM.

1. Na GRUB, pressione E para editar o Sistema operativo em que pretende iniciar. O SO é geralmente listado na primeira linha.
1. Encontre a linha de núcleo. Em Azure, começa com *linux16*.
1. No final da linha, adicione *rd.break* ao fim da linha. Deixe um espaço entre a linha do núcleo e *rd.break.*

    Esta ação interrompe o processo de `initramfs` arranque `systemd`antes que o controlo seja passado de , conforme descrito na documentação do [Chapéu Vermelho.](https://aka.ms/rhel7rootpassword)
1. Prima Ctrl+X para sair e reiniciar com as definições aplicadas.

   Depois de reiniciar, é deixado em modo de emergência com um sistema de ficheiros só para leitura. 
   
1. Na concha, `mount -o remount,rw /sysroot` introduza para remontar o sistema de ficheiros radicular com permissões de leitura/escrita.
1. Depois de iniciar o modo `chroot /sysroot` de utilizador `sysroot` único, introduza para mudar para a cadeia.
1. Está sem raízes. Pode redefinir a sua palavra-passe raiz inserindo `passwd` e, em seguida, utilizar as instruções anteriores para introduzir o modo de utilizador único. 
1. Depois de terminar, `reboot -f` entre para reiniciar.

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-emergency-mount-no-root.gif)

> [!NOTE]
> O funcionamento das instruções anteriores deixa-o na casca de emergência para `fstab`que possa também executar tarefas como a edição . No entanto, normalmente sugerimos que reponha a sua palavra-passe de raiz e a utilize para introduzir o modo de utilizador único.

## <a name="access-for-centos"></a>Acesso ao CentOS
Tal como o Red Hat Enterprise Linux, o modo de utilizador único no CentOS requer que a GRUB e o utilizador de raiz sejam ativados.

### <a name="grub-access-in-centos"></a>Acesso grub no CentOS
O CentOS vem com a GRUB ativada para fora da caixa. Para introduzir o GRUB, reinicie `sudo reboot`o vM entrando e, em seguida, pressione qualquer tecla. Esta ação exibe o painel GRUB.

### <a name="single-user-mode-in-centos"></a>Modo de utilizador único no CentOS
Para ativar o modo de utilizador único no CentOS, siga as instruções anteriores para o RHEL.

## <a name="access-for-ubuntu"></a>Acesso a Ubuntu
As imagens ubuntu não requerem uma senha de raiz. Se o sistema entrar no modo de utilizador único, pode utilizá-lo sem credenciais adicionais.

### <a name="grub-access-in-ubuntu"></a>Acesso grub em Ubuntu
Para aceder à GRUB, prima e segure a Esc enquanto o VM está a arrancar.

Por predefinição, as imagens Ubuntu podem não exibir automaticamente o painel GRUB. Pode alterar a definição fazendo o seguinte:
1. Num editor de texto, abra o ficheiro */etc/default/grub.d/50-cloudimg-settings.cfg.*

1. Mude `GRUB_TIMEOUT` o valor para um valor não zero.
1. Num editor de texto, abra */etc/predefinido/grub*.
1. Comentar a `GRUB_HIDDEN_TIMEOUT=1` linha.
1. Certifique-se de `GRUB_TIMEOUT_STYLE=menu` que há uma linha.
1. Execute `sudo update-grub`.

### <a name="single-user-mode-in-ubuntu"></a>Modo de utilizador único em Ubuntu
Se o Ubuntu não conseguir arrancar normalmente, deixa-o automaticamente no modo de utilizador único. Para introduzir manualmente o modo de utilizador único, faça o seguinte:

1. No GRUB, prima E para editar a sua entrada de arranque (a entrada Ubuntu).
1. Procure a linha que começa com *linux,* e depois procure *ro*.
1. Adicione *um single* após *ro,* certificando-se de que há um espaço antes e depois *de solteiro*.
1. Prima Ctrl+X para reiniciar com estas definições e introduzir o modo de utilizador único.

### <a name="use-grub-to-invoke-bash-in-ubuntu"></a>Use grub para invocar bash em Ubuntu
Depois de ter experimentado as instruções anteriores, pode haver uma situação (como uma palavra-passe de raiz esquecida) em que ainda não consegue aceder ao modo de utilizador único no seu Ubuntu VM. Também pode dizer ao núcleo `/bin/bash` para correr como init, em vez do sistema init. Esta ação dá-lhe uma concha de festa e permite a manutenção do sistema. Utilize as seguintes instruções:

1. No GRUB, prima E para editar a sua entrada de arranque (a entrada Ubuntu).

1. Procure a linha que começa com *linux,* e depois procure *ro*.
1. Substitua *ro* por *rw init=/bin/bash*.

    Esta ação monta o seu sistema `/bin/bash` de ficheiros como leitura-escrita e utiliza como processo de início.
1. Prima Ctrl+X para reiniciar com estas definições.

## <a name="access-for-coreos"></a>Acesso para CoreOS
O modo de utilizador único no CoreOS requer que a GRUB seja ativada.

### <a name="grub-access-in-coreos"></a>Acesso GRUB no CoreOS
Para aceder ao GRUB, prima qualquer tecla enquanto o VM estiver a arrancar.

### <a name="single-user-mode-in-coreos"></a>Modo de utilizador único no CoreOS
Se o CoreOS não conseguir arrancar normalmente, deixa-o automaticamente no modo de utilizador único. Para introduzir manualmente o modo de utilizador único, faça o seguinte:

1. Na GRUB, pressione E para editar a sua entrada de botas.

1. Procure a linha que começa com *linux$*. Deve haver dois casos da linha, cada um encapsulado num diferente *se... cláusula de outra pessoa.*
1. Apêndice *coreos.autologin=ttyS0* até ao fim de cada linha *linux$.*
1. Prima Ctrl+X para reiniciar com estas definições e introduzir o modo de utilizador único.

## <a name="access-for-suse-sles"></a>Acesso para SUSE SLES
Imagens mais recentes do SLES 12 SP3+ permitem o acesso através da consola em série se o sistema entrar em modo de emergência.

### <a name="grub-access-in-suse-sles"></a>Acesso grub em SUSE SLES
O acesso GRUB em SLES requer uma configuração de bootloader via YaST. Para criar a configuração, faça o seguinte:

1. Utilize o SSH para iniciar sessão no `sudo yast bootloader`seu SLES VM e, em seguida, executar . Pressione o Tab, pressione Enter e, em seguida, use as teclas de seta para navegar através do menu.

1. Vá aos **Parâmetros Kernel**e, em seguida, selecione a caixa de verificação de consola série **Use.**
1. Adicione `serial --unit=0 --speed=9600 --parity=no` aos argumentos da **Consola.**
1. Pressione o F10 para guardar as definições e sair.
1. Para introduzir o GRUB, reinicie o VM e pressione qualquer tecla durante a sequência de arranque para manter a vidraça GRUB exibida.

    O tempo de tempo padrão para GRUB é **de 1s**. Pode modificar esta definição `GRUB_TIMEOUT` alterando a variável no ficheiro */etc/predefinido/grub.*

![Inicializando a configuração do bootloader](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-sles-yast-grub-config.gif)

### <a name="single-user-mode-in-suse-sles"></a>Modo de utilizador único nas SUSE SLES
Se o SLES não conseguir arrancar normalmente, és automaticamente atirado para a concha de emergência. Para introduzir manualmente a concha de emergência, faça o seguinte:

1. No GRUB, prima E para editar a sua entrada de arranque (a entrada SLES).

1. Procure a linha de kernel que começa com *linux*.
1. Apêndice *sistemad.unit=emergency.target* até ao fim da linha do núcleo.
1. Prima Ctrl+X para reiniciar com estas definições e introduzir a casca de emergência.

   > [!NOTE]
   > Esta ação deixa-te na concha de emergência com um sistema de ficheiros só para leituras. Para editar quaisquer ficheiros, remonte o sistema de ficheiros com permissões de leitura. Para fazê-lo, entre `mount -o remount,rw /` na concha.

## <a name="access-for-oracle-linux"></a>Acesso para Oracle Linux
Tal como o Red Hat Enterprise Linux, o modo de utilizador único no Oracle Linux requer que a GRUB e o utilizador de raiz sejam ativados.

### <a name="grub-access-in-oracle-linux"></a>Acesso grub em Oracle Linux
O Oracle Linux vem com GRUB ativado para fora da caixa. Para entrar no GRUB, reinicie o vM correndo `sudo reboot`e, em seguida, prima Esc. Esta ação exibe o painel GRUB. Se o painel GRUB não estiver apresentado, certifique-se de que o valor da `GRUB_TERMINAL` linha contém *uma consola em série* (isto é, `GRUB_TERMINAL="serial console"`). Reconstruir grub `grub2-mkconfig -o /boot/grub/grub.cfg`com .

### <a name="single-user-mode-in-oracle-linux"></a>Modo de utilizador único no Oracle Linux
Para ativar o modo de utilizador único no Oracle Linux, siga as instruções anteriores para o RHEL.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre a Consola Em Série, consulte:
* [Documentação da consola em série linux](serial-console-linux.md)
* [Utilize a Consola Em Série para ativar a GRUB em várias distribuições](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/)
* [Utilize consola em série para chamadas NMI e SysRq](serial-console-nmi-sysrq.md)
* [Consola em série para VMs do Windows](serial-console-windows.md)
* [Diagnósticos de arranque](boot-diagnostics.md)
