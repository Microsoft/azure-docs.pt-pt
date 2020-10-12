---
title: Consola Em Série Azure para GRUB e modo de utilizador único Microsoft Docs
description: Este artigo descreve como usar a Consola em Série para GRUB em máquinas virtuais Azure.
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
ms.openlocfilehash: 5341cc62a7d02c3072df90becf893dec18427ac2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87439541"
---
# <a name="use-serial-console-to-access-grub-and-single-user-mode"></a>Utilize a Consola Em Série para aceder ao modo GRUB e single-user
GRand Unified Bootloader (GRUB) é provavelmente a primeira coisa que vê quando inicia uma máquina virtual (VM). Como é exibido antes do sistema operativo ter começado, o GRUB não é acessível via SSH. No GRUB, pode modificar a configuração da sua bota para iniciar o seu arranque no modo de utilizador único, entre outras coisas.

O modo utilizador único é um ambiente mínimo com uma funcionalidade mínima. Pode ser útil para investigar problemas de boot, problemas no sistema de ficheiros ou problemas de rede. Menos serviços podem ser executados em segundo plano e, dependendo do nível de execução, um sistema de ficheiros pode nem sequer ser montado automaticamente.

O modo utilizador único também é útil em situações em que o seu VM pode estar configurado para aceitar apenas as teclas SSH para o início de sposição. Neste caso, poderá utilizar o modo de utilizador único para criar uma conta com autenticação de senha.

> [!NOTE]
> O serviço Serial Console permite que apenas utilizadores com nível *de contribuinte* ou permissões superiores acedam à consola em série de um VM.

Para introduzir o modo de utilizador único, insira o GRUB quando o seu VM estiver a arrancar e modifique a configuração da bota em GRUB. Consulte instruções detalhadas para introduzir o GRUB na secção seguinte. Em geral, se o seu VM tiver sido configurado para exibir GRUB, pode utilizar o botão de reinício dentro da consola em série do VM para reiniciar o VM e exibir GRUB.

![O botão de reinício da consola de série Linux](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-bar.png)

## <a name="general-grub-access"></a>Acesso geral grub
Para aceder ao GRUB, reinicie o seu VM enquanto o painel de consola em série estiver aberto. Algumas distribuições requerem entrada de teclado para mostrar GRUB, e outras mostram automaticamente GRUB por alguns segundos para permitir que a entrada do teclado do utilizador cancele o tempo limite.

Para poder aceder ao modo de utilizador único, pretende certificar-se de que o GRUB está ativado no seu VM. Dependendo da sua distribuição, poderão ser necessários alguns trabalhos de configuração para garantir que o GRUB está ativado. Para obter informações específicas da distribuição, consulte a secção seguinte.

### <a name="restart-your-vm-to-access-grub-in-serial-console"></a>Reinicie o seu VM para aceder ao GRUB na Consola Em Série
Pode reiniciar o seu VM dentro da Consola em Série, pairando sobre o botão **Reiniciar** e, em seguida, selecionando **Restart VM**. Uma notificação sobre o reinício é apresentada na parte inferior do painel.

Também pode reiniciar o seu VM executando um comando SysRq "b" se [o SysRq](./serial-console-nmi-sysrq.md) estiver ativado. Para saber o que esperar do GRUB quando reiniciar, consulte as instruções específicas da distribuição nas secções seguintes.

![Reinicia a consola em série do Linux](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-ubuntu.gif)

## <a name="general-single-user-mode-access"></a>Acesso geral ao modo de utilizador único
Poderá necessitar de acesso manual ao modo de utilizador único quando ainda não configurar uma conta com autenticação de senha. Modifique a configuração GRUB para introduzir manualmente o modo de utilizador único. Depois de o ter feito, consulte a secção "Use o modo utilizador único para reiniciar ou adicionar uma palavra-passe" para obter mais instruções.

Se o VM não for capaz de arrancar, as distribuições deixam-no automaticamente no modo de utilizador único ou no modo de emergência. Outras distribuições, no entanto, requerem configuração adicional, como configurar uma palavra-passe de raiz, antes que possam deixá-lo automaticamente no modo de utilizador único ou de emergência.

### <a name="use-single-user-mode-to-reset-or-add-a-password"></a>Utilize o modo de utilizador único para reiniciar ou adicionar uma palavra-passe
Depois de estar em modo de utilizador único, adicione um novo utilizador com privilégios de sudo fazendo o seguinte:
1. Corra `useradd <username>` para adicionar um utilizador.
1. Corra `sudo usermod -a -G sudo <username>` para conceder aos novos privilégios de raiz do utilizador.
1. Utilize `passwd <username>` para definir a palavra-passe para o novo utilizador. Em seguida, pode iniciar sôm-se como o novo utilizador.


## <a name="access-for-red-hat-enterprise-linux-rhel"></a>Acesso para Red Hat Enterprise Linux (RHEL)
Se o RHEL não conseguir arrancar normalmente, deixa-o automaticamente no modo de utilizador único. No entanto, se ainda não criou o acesso à raiz para o modo de utilizador único, não tem uma palavra-passe de raiz e não pode iniciar sôm. Existe uma solução alternativa (ver o modo "Inserir manualmente o modo de utilizador único na RHEL"), mas sugerimos que tenha configurado inicialmente o acesso à raiz.

### <a name="grub-access-in-rhel"></a>Acesso GRUB em RHEL
O RHEL vem com o GRUB ativado fora da caixa. Para introduzir o GRUB, reinicie o seu VM executando `sudo reboot` e, em seguida, prima qualquer tecla. O painel GRUB deve ser exibido. Se não for, certifique-se de que as seguintes linhas estão presentes no seu ficheiro GRUB `/etc/default/grub` (

**Para RHEL 8**

>[!NOTE]
> A Red Hat recomenda a utilização de Grubby para configurar os parâmetros da linha de comando do núcleo em RHEL 8+. Atualmente, não é possível atualizar o tempo limite de comida e os parâmetros terminais utilizando o grubby. Para modificar a atualização do argumento GRUB_CMDLINE_LINUX para todas as entradas de arranque, corra `grubby --update-kernel=ALL --args="console=ttyS0,115200 console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"` . Mais detalhes estão disponíveis [aqui.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/managing_monitoring_and_updating_the_kernel/configuring-kernel-command-line-parameters_managing-monitoring-and-updating-the-kernel)

```
GRUB_TIMEOUT=5
GRUB_TERMINAL="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
```

**Para RHEL 7**

```
GRUB_TIMEOUT=5
GRUB_TERMINAL_OUTPUT="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300 net.ifnames=0"
```

> [!NOTE]
> O Red Hat também fornece documentação para iniciar o modo de resgate, modo de emergência ou modo Debug, e para redefinir a palavra-passe de raiz. Para obter instruções, consulte [a edição do menu Terminal durante o arranque](https://aka.ms/rhel7grubterminal).

### <a name="set-up-root-access-for-single-user-mode-in-rhel"></a>Configurar o acesso à raiz para o modo de utilizador único no RHEL
O utilizador raiz é desativado por defeito. O modo de utilizador único no RHEL requer que o utilizador raiz seja ativado. Se necessitar de ativar o modo de utilizador único, utilize as seguintes instruções:

1. Inscreva-se no sistema do Chapéu Vermelho via SSH.
1. Mude para a raiz.
1. Ativar a palavra-passe para o utilizador raiz, fazendo o seguinte:
    * Executar `passwd root` (definir uma palavra-passe de raiz forte).
1. Certifique-se de que o utilizador de raiz só pode iniciar screva através do ttyS0 fazendo o seguinte: a. Executar `edit /etc/ssh/sshd_config` , e certifique-se de que o PermitRootLogIn está definido para `no` .
    b. Corra `edit /etc/securetty file` para permitir a entrada apenas via ttyS0.

Agora, se o sistema entrar no modo de utilizador único, pode iniciar sôr com a palavra-passe raiz.

Alternativamente, para RHEL 7.4+ ou 6.9+, para ativar o modo de utilizador único nas indicações GRUB, consulte [o Booting no modo de utilizador único](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single).

### <a name="manually-enter-single-user-mode-in-rhel"></a>Introduza manualmente o modo de utilizador único no RHEL
Se tiver configurado o GRUB e o acesso à raiz utilizando as instruções anteriores, pode introduzir o modo de utilizador único fazendo o seguinte:

1. Para introduzir o GRUB, prima esc à medida que reinicia o VM.
1. Em GRUB, prima E para editar o SISTEMA em que pretende iniciar. O SO é geralmente listado na primeira linha.
1. Encontre a linha do núcleo. Em Azure, começa com *linux16*.
1. Pressione Ctrl+E para ir até ao fim da linha.
1. No final da linha, adicione *systemd.unit=rescue.target*.

    Esta ação insuem-no no modo de utilizador único. Se pretender utilizar o modo de emergência, adicione *systemd.unit=emergency.target* ao fim da linha (em vez de *systemd.unit=rescue.target*).

1. Prima Ctrl+X para sair e reiniciar com as definições aplicadas.

   Será solicitado a palavra-passe do administrador antes de poder introduzir o modo de utilizador único. Esta palavra-passe é a que criou nas instruções anteriores.

    ![Imagem animada mostrando uma interface de linha de comando. O utilizador seleciona um servidor, localiza a extremidade da linha do núcleo e, em seguida, introduz o texto especificado.](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-enter-emergency-shell.gif)

### <a name="enter-single-user-mode-without-root-account-enabled-in-rhel"></a>Introduza o modo de utilizador único sem conta raiz ativada no RHEL
Se não ativou o utilizador de raiz seguindo as instruções anteriores, ainda pode redefinir a sua palavra-passe de raiz fazendo o seguinte:

> [!NOTE]
> Se estiver a utilizar o SELinux, quando redefinir a palavra-passe de raiz, certifique-se de seguir os passos adicionais descritos na [documentação](https://aka.ms/rhel7grubterminal)do Chapéu Vermelho .

1. Para introduzir o GRUB, prima esc à medida que reinicia o VM.

1. Em GRUB, prima E para editar o SISTEMA em que pretende iniciar. O SO é geralmente listado na primeira linha.
1. Encontre a linha do núcleo. Em Azure, começa com *linux16*.
1. No final da linha, adicione *rd.break* ao fim da linha. Deixe um espaço entre a linha do núcleo e *rd.break*.

    Esta ação interrompe o processo de arranque antes de passar o controlo `initramfs` `systemd` para, conforme descrito na [documentação](https://aka.ms/rhel7rootpassword)do Chapéu Vermelho .
1. Prima Ctrl+X para sair e reiniciar com as definições aplicadas.

   Depois de reiniciar, é colocado em modo de emergência com um sistema de ficheiros só de leitura.

1. Na concha, `mount -o remount,rw /sysroot` introduza para voltar a montar o sistema de ficheiros radi ao ler/escrever permissões.
1. Depois de iniciar o seu arranque no modo de utilizador único, entre `chroot /sysroot` para mudar para a `sysroot` cadeia.
1. Agora estás na raiz. Pode redefinir a palavra-passe de raiz introduzindo e, em `passwd` seguida, utilizar as instruções anteriores para introduzir o modo de utilizador único.
1. Depois de terminar, entre `reboot -f` para reiniciar.

![Imagem animada mostrando uma interface de linha de comando. O utilizador seleciona um servidor, localiza a extremidade da linha kernel e introduz os comandos especificados.](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-emergency-mount-no-root.gif)

> [!NOTE]
> A execução das instruções anteriores deixa-o cair na concha de emergência para que também possa executar tarefas como a edição `fstab` . No entanto, normalmente sugerimos que reinicie a sua palavra-passe de raiz e a utilize para introduzir o modo de utilizador único.

## <a name="access-for-centos"></a>Acesso para CentOS
Tal como o Red Hat Enterprise Linux, o modo de utilizador único no CentOS requer que o GRUB e o utilizador de raiz estejam ativados.

### <a name="grub-access-in-centos"></a>Acesso GRUB no CentOS
CentOS vem com GRUB ativado fora da caixa. Para introduzir o GRUB, reinicie o seu VM introduzindo e, em `sudo reboot` seguida, prima qualquer tecla. Esta ação exibe o painel GRUB.

### <a name="single-user-mode-in-centos"></a>Modo de utilizador único no CentOS
Para ativar o modo de utilizador único no CentOS, siga as instruções anteriores para o RHEL.

## <a name="access-for-ubuntu"></a>Acesso a Ubuntu
As imagens Ubuntu não requerem uma senha de raiz. Se o sistema entrar no modo de utilizador único, pode utilizá-lo sem credenciais adicionais.

### <a name="grub-access-in-ubuntu"></a>Acesso GRUB em Ubuntu
Para aceder ao GRUB, prima e mantenha o Esc enquanto o VM está a arrancar.

Por predefinição, as imagens Ubuntu podem não exibir automaticamente o painel GRUB. Pode alterar a definição fazendo o seguinte:
1. Num editor de texto, abra o ficheiro */etc/default/grub.d/50-cloudimg-settings.cfg.*

1. Altere o `GRUB_TIMEOUT` valor para um valor não zero.
1. Num editor de texto, aberto */etc/predefinido/grub*.
1. Comentar a `GRUB_HIDDEN_TIMEOUT=1` linha.
1. Certifique-se de que há uma `GRUB_TIMEOUT_STYLE=menu` linha.
1. Execute `sudo update-grub`.

### <a name="single-user-mode-in-ubuntu"></a>Modo de utilizador único em Ubuntu
Se o Ubuntu não conseguir arrancar normalmente, deixa-o automaticamente no modo de utilizador único. Para introduzir o modo de utilizador único manualmente, faça o seguinte:

1. Em GRUB, prima E para editar a sua entrada de arranque (a entrada Ubuntu).
1. Procure a linha que começa com *linux,* e depois procure *por ro*.
1. Adicione *single* after *ro*, garantindo que há um espaço antes e depois *de single*.
1. Prima Ctrl+X para reiniciar com estas definições e introduza o modo de utilizador único.

### <a name="use-grub-to-invoke-bash-in-ubuntu"></a>Use GRUB para invocar bash em Ubuntu
Depois de ter experimentado as instruções anteriores, pode haver uma situação (como uma palavra-passe de raiz esquecida) em que ainda não consegue aceder ao modo de utilizador único no seu VM Ubuntu. Também pode dizer ao núcleo para funcionar `/bin/bash` como init, em vez do sistema. Esta ação dá-lhe uma casca de pancada e permite a manutenção do sistema. Utilize as seguintes instruções:

1. Em GRUB, prima E para editar a sua entrada de arranque (a entrada Ubuntu).

1. Procure a linha que começa com *linux,* e depois procure *por ro*.
1. Substitua *ro* por *rw init=/bin/bash*.

    Esta ação monta o seu sistema de ficheiros como leitura-escrita e utiliza `/bin/bash` como processo init.
1. Prima Ctrl+X para reiniciar com estas definições.

## <a name="access-for-coreos"></a>Acesso ao CoreOS
O modo de utilizador único no CoreOS requer que o GRUB seja ativado.

### <a name="grub-access-in-coreos"></a>Acesso GRUB no CoreOS
Para aceder ao GRUB, prima qualquer tecla enquanto o seu VM está a arrancar.

### <a name="single-user-mode-in-coreos"></a>Modo de utilizador único no CoreOS
Se o CoreOS não conseguir arrancar normalmente, deixa-o automaticamente no modo de utilizador único. Para introduzir o modo de utilizador único manualmente, faça o seguinte:

1. Em GRUB, prima E para editar a sua entrada de arranque.

1. Procure a linha que começa com *linux$*. Deve haver dois casos da linha, cada um encapsulado num diferente *se... outra* cláusula.
1. Apêndice *coreos.autologin=ttyS0* até ao final de cada linha *linux$.*
1. Prima Ctrl+X para reiniciar com estas definições e introduza o modo de utilizador único.

## <a name="access-for-suse-sles"></a>Acesso a SUSE SLES
Imagens mais recentes do SLES 12 SP3+ permitem o acesso através da consola em série se o sistema entrar em modo de emergência.

### <a name="grub-access-in-suse-sles"></a>Acesso GRUB em SUSE SLES
O acesso GRUB em SLES requer uma configuração de bootloader via YaST. Para criar a configuração, faça o seguinte:

1. Utilize o SSH para iniciar sles VM e, em seguida, executar `sudo yast bootloader` . Prima o separador, prima Enter e, em seguida, use as teclas de seta para navegar através do menu.

1. Vá para **os Parâmetros Kernel**e, em seguida, selecione a caixa **de verificação da consola em série Use.**
1. Adicione `serial --unit=0 --speed=9600 --parity=no` aos argumentos da **Consola.**
1. Prima F10 para guardar as suas definições e saída.
1. Para introduzir o GRUB, reinicie o seu VM e pressione qualquer tecla durante a sequência de arranque para manter o painel GRUB visualizado.

    O tempo limite padrão para GRUB é **1s**. Pode modificar esta definição alterando a `GRUB_TIMEOUT` variável no ficheiro */etc/predefinido/grub.*

![Inicialização da configuração do bootloader](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-sles-yast-grub-config.gif)

### <a name="single-user-mode-in-suse-sles"></a>Modo utilizador único em SUSE SLES
Se o SLES não pode arrancar normalmente, és automaticamente atirado para a concha de emergência. Para entrar na concha de emergência manualmente, faça o seguinte:

1. Em GRUB, prima E para editar a sua entrada de arranque (a entrada SLES).

1. Procure a linha de núcleo que começa com *linux.*
1. Apend *systemd.unit=emergency.target* to the end of the kernel line.
1. Prima Ctrl+X para reiniciar com estas definições e introduza a concha de emergência.

   > [!NOTE]
   > Esta ação atira-te para a concha de emergência com um sistema de ficheiros só de leitura. Para editar quaisquer ficheiros, remonte o sistema de ficheiros com permissões de leitura-escrita. Para tal, entre `mount -o remount,rw /` na concha.

## <a name="access-for-oracle-linux"></a>Acesso para Oracle Linux
Tal como o Red Hat Enterprise Linux, o modo de utilizador único no Oracle Linux requer que o GRUB e o utilizador de raiz estejam ativados.

### <a name="grub-access-in-oracle-linux"></a>Acesso GRUB em Oracle Linux
Oracle Linux vem com GRUB ativado fora da caixa. Para introduzir o GRUB, reinicie o seu VM executando `sudo reboot` e, em seguida, prima Esc. Esta ação exibe o painel GRUB. Se o painel GRUB não for apresentado, certifique-se de que o valor da `GRUB_TERMINAL` linha contém consola em *série* (isto é, `GRUB_TERMINAL="serial console"` ). Reconstruir GRUB com `grub2-mkconfig -o /boot/grub/grub.cfg` .

### <a name="single-user-mode-in-oracle-linux"></a>Modo de utilizador único no Oracle Linux
Para ativar o modo de utilizador único no Oracle Linux, siga as instruções anteriores para o RHEL.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre a Consola em Série, consulte:
* [Documentação da consola em série do Linux](serial-console-linux.md)
* [Utilize a Consola Em Série para permitir o GRUB em várias distribuições](http://linuxonazure.azurewebsites.net/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/)
* [Utilize consola em série para chamadas NMI e SysRq](serial-console-nmi-sysrq.md)
* [Consola em série para VMs do Windows](serial-console-windows.md)
* [Diagnósticos de arranque.](boot-diagnostics.md)
