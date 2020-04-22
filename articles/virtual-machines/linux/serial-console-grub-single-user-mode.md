---
title: Consola em série Azure para GRUB e Modo utilizador único
description: Utilização de consola seleção em série para a larva em máquinas virtuais Azure.
services: virtual-machines-linux
author: asinn826
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: 2aa7110ab4e52fdc5c3804bd27be5f41081fb435
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758500"
---
# <a name="use-serial-console-to-access-grub-and-single-user-mode"></a>Utilize a consola em série para aceder ao modo DE UTILIZADOR e ao modo de utilizador único
GRUB é o Porta-malas Unificado GRand. A partir da GRUB é possível modificar a configuração da sua bota para iniciar em modo de utilizador único, entre outras coisas.

O modo de utilizador único é um ambiente mínimo com uma funcionalidade mínima. Pode ser útil para investigar problemas de boot, problemas de sistema de ficheiros ou problemas de rede. Menos serviços podem ser executados em segundo plano e, dependendo do nível de execução, um sistema de ficheiros pode nem sequer ser montado automaticamente.

O modo de utilizador único também é útil em situações em que o seu VM só pode ser configurado para aceitar teclas SSH para iniciar sessão. Neste caso, poderá utilizar o modo de utilizador único para criar uma conta com autenticação de senha.

Para introduzir um modo de utilizador único, terá de introduzir grub quando o vM estiver a iniciar-se e modificar a configuração da bota em GRUB. Isto pode ser feito com a consola em série VM.

## <a name="general-grub-access"></a>Acesso geral da GRUB
Para aceder ao GRUB, terá de reiniciar o seu VM mantendo a lâmina da consola em série aberta. Alguns distros exigirão a entrada do teclado para mostrar GRUB, enquanto outros mostrarão automaticamente GRUB por alguns segundos e permitem que a entrada do teclado do utilizador cancele o tempo de saída. 

Deverá certificar-se de que a GRUB está ativada no seu VM para poder aceder ao modo de utilizador único. Dependendo da sua disção, pode haver algum trabalho de configuração para garantir que a GRUB está ativada. As informações específicas para distro estão disponíveis abaixo.

### <a name="reboot-your-vm-to-access-grub-in-serial-console"></a>Reinicie o seu VM para aceder ao GRUB em Consola em Série
Reiniciar o VM com a lâmina da consola em série `'b'` aberta pode ser feito com um comando [SysRq se o SysRq](./serial-console-nmi-sysrq.md) estiver ativado, ou clicando no botão Restart na lâmina overview (abra o VM num novo separador de navegador para reiniciar sem fechar a lâmina da consola em série). Siga as instruções específicas de distro abaixo para saber o que esperar da GRUB quando reiniciar.

## <a name="general-single-user-mode-access"></a>Acesso geral do modo de utilizador único
O acesso manual ao modo de utilizador único pode ser necessário em situações em que não tenha configurado uma conta com autenticação de senha. Terá de modificar a configuração GRUB para introduzir manualmente o modo de utilizador único. Depois de ter feito isto, consulte o Modo utilizador único para redefinir ou adicionar uma palavra-passe para mais instruções.

Nos casos em que o VM não consegue arrancar, as distros muitas vezes deixam-no automaticamente no modo de utilizador ou no modo de emergência. Outros, no entanto, requerem configuração adicional antes de poderem deixá-lo automaticamente no modo de utilizador único ou de emergência (como por exemplo, configurar uma palavra-passe de raiz).

### <a name="use-single-user-mode-to-reset-or-add-a-password"></a>Utilize o modo utilizador único para redefinir ou adicionar uma palavra-passe
Uma vez que esteja em modo de utilizador único, faça o seguinte para adicionar um novo utilizador com privilégios sudo:
1. Correr `useradd <username>` para adicionar um utilizador
1. Corra `sudo usermod -a -G sudo <username>` para conceder aos novos privilégios de raiz do utilizador
1. Utilize `passwd <username>` para definir a palavra-passe para o novo utilizador. Em seguida, poderá iniciar sessão como o novo utilizador

## <a name="access-for-red-hat-enterprise-linux-rhel"></a>Acesso para Red Hat Enterprise Linux (RHEL)
O RHEL irá deixá-lo no modo de utilizador individual automaticamente se não conseguir arrancar normalmente. No entanto, se não tiver configurado o acesso à raiz para o modo de utilizador único, não terá uma palavra-passe de raiz e não poderá fazer login. Existe uma salição (ver "Entrar manualmente no modo de utilizador único" abaixo), mas a sugestão é configurar inicialmente o acesso à raiz.

### <a name="grub-access-in-rhel"></a>Acesso GRUB em RHEL
O RHEL vem com a GRUB ativada para fora da caixa. Para entrar no GRUB, `sudo reboot` reinicie o vM e prima qualquer tecla. Verá o ecrã grub aparecer.

> Nota: A Red Hat também fornece documentação para o arranque no modo de resgate, modo de emergência, modo de depuração e redefinição da palavra-passe da raiz. [Clique aqui para aceder a ele](https://aka.ms/rhel7grubterminal).

### <a name="set-up-root-access-for-single-user-mode-in-rhel"></a>Configurar o acesso à raiz para o modo de utilizador único no RHEL
O modo de utilizador único no RHEL requer ativar o utilizador da raiz, que é desativado por defeito. Se tiver necessidade de ativar o modo de utilizador único, utilize as seguintes instruções:

1. Inicie sessão no sistema Red Hat via SSH
1. Mude para raiz
1. Ativar palavra-passe para o utilizador de raiz 
    * `passwd root`(definir uma palavra-passe de raiz forte)
1. Certifique-se de que o utilizador raiz só pode fazer login via ttyS0
    * `edit /etc/ssh/sshd_config`e garantir que a PermitRootLogIn está definida para não
    * `edit /etc/securetty file`apenas permitir o login via ttyS0 

Agora, se o sistema entrar no modo de utilizador único, pode iniciar sessão através de uma palavra-passe raiz.

Alternativamente para RHEL 7.4+ ou 6.9+ pode ativar o modo de utilizador único nas instruções GRUB, consulte as instruções [aqui](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single)

### <a name="manually-enter-single-user-mode-in-rhel"></a>Introduza manualmente o modo de utilizador único no RHEL
Se tiver configurado o GRUB e o acesso à raiz com as instruções acima, pode introduzir o modo de utilizador único com as seguintes instruções:

1. Prima 'Esc' ao reiniciar o VM para entrar no GRUB
1. No GRUB, prima 'e' para editar o SISTEMA selecionado que pretende iniciar (normalmente a primeira linha)
1. Encontre a linha kernel - em Azure, isto começará com`linux16`
1. Prima Ctrl + E para ir até ao fim da linha
1. Adicione o seguinte ao fim da linha:`systemd.unit=rescue.target`
    * Isto irá inseri-lo no modo de utilizador único. Se quiser utilizar o modo `systemd.unit=emergency.target` de emergência, adicione ao fim da linha em vez de`systemd.unit=rescue.target`
1. Prima Ctrl + X para sair e reiniciar com as definições aplicadas
1. Será solicitado para a palavra-passe do administrador antes de poder entrar no modo de utilizador único - esta é a mesma palavra-passe que criou nas instruções acima    

    ![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-enter-emergency-shell.gif)

### <a name="enter-single-user-mode-without-root-account-enabled-in-rhel"></a>Introduza o modo de utilizador único sem conta raiz ativada no RHEL
Se não passou pelos passos acima para ativar o utilizador da raiz, ainda pode redefinir a sua palavra-passe de raiz. Utilize as seguintes instruções:

> Nota: Se estiver a utilizar o SELinux, certifique-se de que tomou as medidas adicionais descritas na documentação do Chapéu Vermelho [aqui](https://aka.ms/rhel7grubterminal) ao redefinir a palavra-passe de raiz.

1. Prima 'Esc' ao reiniciar o VM para entrar no GRUB
1. No GRUB, prima 'e' para editar o SISTEMA selecionado que pretende iniciar (normalmente a primeira linha)
1. Encontre a linha kernel - em Azure, isto começará com`linux16`
1. Adicione `rd.break` ao fim da linha, garantindo que `rd.break` há um espaço antes (ver exemplo abaixo)
    - Isto interromperá o processo de `initramfs` arranque `systemd`antes que o controlo seja passado para, como descrito na documentação do Chapéu Vermelho [aqui.](https://aka.ms/rhel7rootpassword)
1. Prima Ctrl + X para sair e reiniciar com as definições aplicadas
1. Assim que iniciar, será deixado em modo de emergência com um sistema de ficheiros só para leitura. Introduza `mount -o remount,rw /sysroot` na concha para remontar o sistema de ficheiros radiculares com permissões de leitura/escrita
1. Assim que iniciar o modo `chroot /sysroot` de utilizador `sysroot` único, escreva para mudar para a cadeia
1. Agora estás na raiz. Pode redefinir a sua `passwd` palavra-passe de raiz e, em seguida, utilizar as instruções acima para introduzir um único modo de utilizador. Escreva `reboot -f` para reiniciar uma vez que esteja feito.

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-emergency-mount-no-root.gif)

> Nota: O funcionamento das instruções acima irá deixá-lo em concha de `fstab`emergência, para que também possa executar tarefas como a edição . No entanto, a sugestão geralmente aceite é redefinir a sua palavra-passe de raiz e usá-lo para introduzir o modo de utilizador único. 


## <a name="access-for-centos"></a>Acesso ao CentOS
Tal como o Red Hat Enterprise Linux, o modo de utilizador único no CentOS requer que a GRUB e o utilizador de raiz sejam ativados. 

### <a name="grub-access-in-centos"></a>Acesso grub no CentOS
O CentOS vem com a GRUB ativada para fora da caixa. Para entrar no GRUB, `sudo reboot` reinicie o vM e prima qualquer tecla. Verá o ecrã grub aparecer.

### <a name="single-user-mode-in-centos"></a>Modo de utilizador único no CentOS
Siga as instruções para o RHEL acima para ativar o modo de utilizador único no CentOS.

## <a name="access-for-ubuntu"></a>Acesso a Ubuntu 
As imagens Ubuntu não requerem uma palavra-passe de raiz. Se o sistema entrar no modo de utilizador único, pode utilizá-lo sem credenciais adicionais. 

### <a name="grub-access-in-ubuntu"></a>Acesso grub em Ubuntu
Para aceder à GRUB, prima e segure 'Esc' enquanto o VM está a arrancar. 

Por predefinição, as imagens Ubuntu não mostrarão automaticamente o ecrã GRUB. Isto pode ser alterado com as seguintes instruções:
1. Abra `/etc/default/grub.d/50-cloudimg-settings.cfg` em um editor de texto à sua escolha
1. Alterar `GRUB_TIMEOUT` o valor para um valor não zero
1. Abra `/etc/default/grub` em um editor de texto à sua escolha
1. Comentar a `GRUB_HIDDEN_TIMEOUT=1` linha
1. Execute `sudo update-grub`

### <a name="single-user-mode-in-ubuntu"></a>Modo de utilizador único em Ubuntu
O Ubuntu irá deixá-lo no modo de utilizador individual automaticamente se não conseguir arrancar normalmente. Para introduzir manualmente o modo de utilizador único, utilize as seguintes instruções:

1. Da GRUB, prima 'e' para editar a sua entrada de arranque (a entrada ubuntu)
1. Procure a linha que `linux`começa com, em seguida, procure`ro`
1. Adicione `single` `ro`depois, garantindo que há um espaço antes e depois`single`
1. Prima Ctrl + X para reiniciar com estas definições e introduzir o modo de utilizador único

## <a name="access-for-coreos"></a>Acesso para CoreOS
O modo de utilizador único no CoreOS requer que a GRUB seja ativada. 

### <a name="grub-access-in-coreos"></a>Acesso GRUB no CoreOS
Para aceder ao GRUB, prima qualquer tecla quando o vM estiver a arrancar.

### <a name="single-user-mode-in-coreos"></a>Modo de utilizador único no CoreOS
O CoreOS irá deixá-lo automaticamente no modo de utilizador único se não conseguir arrancar normalmente. Para introduzir manualmente o modo de utilizador único, utilize as seguintes instruções:
1. Da GRUB, prima 'e' para editar a sua entrada de botas
1. Procure a linha que `linux$`começa com. Deve haver 2, encapsulado em diferentes cláusulas se/outros
1. Apêndice `coreos.autologin=ttyS0` ao fim de `linux$` ambas as linhas
1. Prima Ctrl + X para reiniciar com estas definições e introduzir o modo de utilizador único

## <a name="access-for-suse-sles"></a>Acesso para SUSE SLES
Imagens mais recentes do SLES 12 SP3+ permitem o acesso através da consola em série no caso de o sistema entrar no modo de emergência. 

### <a name="grub-access-in-suse-sles"></a>Acesso grub em SUSE SLES
O acesso GRUB em SLES requer configuração de bootloader via YaST. Para isso, siga estas instruções:

1. ssh no seu SLES `sudo yast bootloader`VM e correr . Utilize `tab` as `enter` teclas, chaves e setas para navegar através do menu. 
1. Navegue `Kernel Parameters`para. `Use serial console` 
1. Adicione `serial --unit=0 --speed=9600 --parity=no` aos argumentos da Consola

1. Prima F10 para salvar as suas definições e saída
1. Para entrar no GRUB, reinicie o vM e prima qualquer tecla durante a sequência de arranque para que a GRUB permaneça no ecrã
    - O tempo de paragem padrão para GRUB é 1s. Pode modificá-lo `GRUB_TIMEOUT` alterando a variável em`/etc/default/grub`

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-sles-yast-grub-config.gif)

### <a name="single-user-mode-in-suse-sles"></a>Modo de utilizador único em SUSE SLES
Será automaticamente atirado para a casca de emergência se o SLES não conseguir arrancar normalmente. Para introduzir manualmente a casca de emergência, utilize as seguintes instruções:

1. Da GRUB, prima 'e' para editar a sua entrada de arranque (a entrada SLES)
1. Procure a linha de kernel que começará com`linux`
1. Apêndice `systemd.unit=emergency.target` até ao fim da linha
1. Prima Ctrl + X para reiniciar com estas definições e introduzir a concha de emergência
   > Note que será lançado numa concha de emergência com um sistema de ficheiros _só para leitura._ Se quiser fazer alguma editida em quaisquer ficheiros, terá de remontar o sistema de ficheiros com permissões de leitura. Para fazer isto, entre `mount -o remount,rw /` na concha

## <a name="access-for-oracle-linux"></a>Acesso para Oracle Linux
Tal como o Red Hat Enterprise Linux, o modo de utilizador único no Oracle Linux requer que a GRUB e o utilizador de raiz sejam ativados. 

### <a name="grub-access-in-oracle-linux"></a>Acesso grub em Oracle Linux
O Oracle Linux vem com GRUB ativado para fora da caixa. Para entrar no GRUB, `sudo reboot` reinicie o vM e prima 'Esc'. Verá o ecrã grub aparecer.

### <a name="single-user-mode-in-oracle-linux"></a>Modo de utilizador único no Oracle Linux
Siga as instruções para o RHEL acima para ativar o modo de utilizador único no Oracle Linux.

## <a name="next-steps"></a>Passos seguintes
* A página de documentação da consola em série Linux está localizada [aqui.](serial-console.md)
* Utilize consola em série para [chamadas NMI e SysRq](serial-console-nmi-sysrq.md)
* A Consola em Série também está disponível para [VMs do Windows](../windows/serial-console.md)
* Saiba mais sobre [diagnósticos de botas](boot-diagnostics.md)
