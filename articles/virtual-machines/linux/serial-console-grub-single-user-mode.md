---
title: Consola em série Azure para GRUB e modo de utilizador único
description: Utilização da Consola Em Série para comida em máquinas virtuais Azure.
services: virtual-machines-linux
author: asinn826
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: d594f4d8019a7c23da79506cd702adbe9f25038d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87028946"
---
# <a name="use-serial-console-to-access-grub-and-single-user-mode"></a>Utilize a Consola Em Série para aceder ao GRUB e ao Modo Único de Utilizador
Grub é o GRand Unified Bootloader. A partir de GRUB é capaz de modificar a sua configuração de arranque para iniciar em modo único utilizador, entre outras coisas.

O modo utilizador único é um ambiente mínimo com uma funcionalidade mínima. Pode ser útil para investigar problemas de boot, problemas de sistema de ficheiros ou problemas de rede. Menos serviços podem ser executados em segundo plano, e, dependendo do nível de execução, um sistema de ficheiros pode nem sequer ser montado automaticamente.

O modo de utilizador único também é útil em situações em que o seu VM só pode ser configurado para aceitar chaves SSH para iniciar sessão. Neste caso, poderá utilizar o modo de utilizador único para criar uma conta com autenticação de senha.

Para introduzir o modo de utilizador único, terá de introduzir GRUB quando o seu VM estiver a arrancar e modificar a configuração da bota em GRUB. Isto pode ser feito com a consola em série VM.

## <a name="general-grub-access"></a>Acesso geral grub
Para aceder ao GRUB, terá de reiniciar o seu VM mantendo a lâmina da consola em série aberta. Alguns distros exigirão a entrada do teclado para mostrar o GRUB, enquanto outros mostrarão automaticamente o GRUB durante alguns segundos e permitirão que a entrada do teclado do utilizador cancele o tempo limite. 

Pretende certificar-se de que o GRUB está ativado no seu VM para poder aceder ao modo de utilizador único. Dependendo do seu distro, pode haver algum trabalho de configuração para garantir que o GRUB está ativado. Informações específicas de distro estão disponíveis abaixo.

### <a name="reboot-your-vm-to-access-grub-in-serial-console"></a>Reinicie o seu VM para aceder ao GRUB na Consola Em Série
Reiniciar o seu VM com a lâmina da consola em série aberta pode ser feito com um comando SysRq `'b'` se [o SysRq](./serial-console-nmi-sysrq.md) estiver ativado, ou clicando no botão Reiniciar na lâmina Overview (abra o VM num novo separador de navegador para reiniciar sem fechar a lâmina da consola em série). Siga as instruções específicas para saber o que esperar do GRUB quando reiniciar.

## <a name="general-single-user-mode-access"></a>Acesso geral do modo de utilizador único
Poderá ser necessário o acesso manual a um único modo de utilizador em situações em que não tenha configurado uma conta com autenticação de senha. Terá de modificar a configuração GRUB para introduzir manualmente o modo de utilizador único. Depois de o ter feito, consulte use o modo de utilizador único para reiniciar ou adicionar uma palavra-passe para mais instruções.

Nos casos em que o VM não consegue arrancar, os distros deixam-no frequentemente automaticamente em modo de utilizador único ou em modo de emergência. Outros, no entanto, requerem uma configuração adicional antes de poderem deixá-lo automaticamente no modo de utilizador único ou de emergência (por exemplo, configurar uma palavra-passe de raiz).

### <a name="use-single-user-mode-to-reset-or-add-a-password"></a>Utilize o Modo Utilizador Único para reiniciar ou adicionar uma palavra-passe
Uma vez que esteja em modo de utilizador único, faça o seguinte para adicionar um novo utilizador com privilégios de sudo:
1. Correr `useradd <username>` para adicionar um utilizador
1. Corra `sudo usermod -a -G sudo <username>` para conceder aos novos privilégios de raiz do utilizador
1. Utilize `passwd <username>` para definir a palavra-passe para o novo utilizador. Poderá então fazer login como novo utilizador

## <a name="access-for-red-hat-enterprise-linux-rhel"></a>Acesso para Red Hat Enterprise Linux (RHEL)
A RHEL irá deixá-lo automaticamente no modo de utilizador único se não conseguir arrancar normalmente. No entanto, se não tiver configurado o acesso à raiz para um único modo de utilizador, não terá uma palavra-passe de raiz e não poderá iniciar sessão. Existe uma solução alternativa (ver 'Entrar manualmente no único modo de utilizador' abaixo), mas a sugestão é configurar inicialmente o acesso à raiz.

### <a name="grub-access-in-rhel"></a>Acesso GRUB em RHEL
O RHEL vem com o GRUB ativado fora da caixa. Para introduzir o GRUB, reinicie o seu VM com `sudo reboot` e prima qualquer tecla. Vais ver o ecrã grub aparecer.

> Nota: O Chapéu Vermelho também fornece documentação para iniciar o modo de salvamento, modo de emergência, modo Debug e redefinir a palavra-passe raiz. [Clique aqui para aceder a ele.](https://aka.ms/rhel7grubterminal)

### <a name="set-up-root-access-for-single-user-mode-in-rhel"></a>Configurar o acesso à raiz para o modo de utilizador único no RHEL
O modo utilizador único no RHEL requer que o utilizador raiz seja ativado, que é desativado por defeito. Se tiver necessidade de ativar o modo de utilizador único, utilize as seguintes instruções:

1. Faça login no sistema do Chapéu Vermelho via SSH
1. Mude para raiz
1. Ativar a palavra-passe para o utilizador de raiz 
    * `passwd root`(definir uma palavra-passe de raiz forte)
1. Certifique-se de que o utilizador raiz só pode fazer login através do ttyS0
    * `edit /etc/ssh/sshd_config`e garantir PermitRootLogIn está definido para não
    * `edit /etc/securetty file`para apenas permitir o login através de ttyS0 

Agora, se o sistema entrar no modo de utilizador único, pode iniciar sessão através da palavra-passe raiz.

Alternativamente para RHEL 7.4+ ou 6.9+ pode ativar o modo de utilizador único nas indicações GRUB, consulte as instruções [aqui](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single)

### <a name="manually-enter-single-user-mode-in-rhel"></a>Introduza manualmente o modo de utilizador único no RHEL
Se tiver configurado o GRUB e o acesso à raiz com as instruções acima, pode introduzir o modo de utilizador único com as seguintes instruções:

1. Prima 'Esc' ao reiniciar o VM para entrar em GRUB
1. Em GRUB, prima 'e' para editar o sistema operativo selecionado que pretende iniciar (normalmente a primeira linha)
1. Encontre a linha kernel - em Azure, isto vai começar com`linux16`
1. Pressione Ctrl + E para ir ao fim da linha
1. Adicione o seguinte ao fim da linha:`systemd.unit=rescue.target`
    * Isto irá insiná-lo em modo único de utilizador. Se quiser utilizar o modo de emergência, adicione `systemd.unit=emergency.target` ao fim da linha em vez de`systemd.unit=rescue.target`
1. Pressione Ctrl + X para sair e reiniciar com as definições aplicadas
1. Será solicitado a palavra-passe do administrador antes de poder introduzir o modo de utilizador único - esta é a mesma palavra-passe que criou nas instruções acima    

    ![Imagem animada mostrando uma interface de linha de comando. O utilizador seleciona um servidor, localiza a extremidade da linha do núcleo e, em seguida, introduz o texto especificado.](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-enter-emergency-shell.gif)

### <a name="enter-single-user-mode-without-root-account-enabled-in-rhel"></a>Introduza o modo de utilizador único sem conta raiz ativada no RHEL
Se não tiver passado pelos passos acima para ativar o utilizador raiz, ainda pode redefinir a sua palavra-passe de raiz. Utilize as seguintes instruções:

> Nota: Se estiver a utilizar o SELinux, certifique-se de que tomou as medidas adicionais descritas na documentação do Chapéu Vermelho [aqui](https://aka.ms/rhel7grubterminal) ao redefinir a palavra-passe raiz.

1. Prima 'Esc' ao reiniciar o VM para entrar em GRUB
1. Em GRUB, prima 'e' para editar o sistema operativo selecionado que pretende iniciar (normalmente a primeira linha)
1. Encontre a linha kernel - em Azure, isto vai começar com`linux16`
1. Adicione `rd.break` ao fim da linha, garantindo que há um espaço antes `rd.break` (ver exemplo abaixo)
    - Isto irá interromper o processo de arranque antes que o controlo seja passado `initramfs` `systemd` de, como descrito na documentação do Chapéu Vermelho [aqui](https://aka.ms/rhel7rootpassword).
1. Pressione Ctrl + X para sair e reiniciar com as definições aplicadas
1. Assim que iniciar o arranque, será colocado em modo de emergência com um sistema de ficheiros apenas de leitura. `mount -o remount,rw /sysroot`Introduza a concha para remontar o sistema de ficheiros de raiz com permissões de leitura/escrita
1. Assim que iniciar o modo de utilizador único, digite `chroot /sysroot` para mudar para a `sysroot` cadeia
1. Agora és a raiz. Pode redefinir a palavra-passe de raiz com `passwd` e, em seguida, utilizar as instruções acima para introduzir o modo de utilizador único. Escreva `reboot -f` para reiniciar uma vez que terminar.

![Imagem animada mostrando uma interface de linha de comando. O utilizador seleciona um servidor, localiza a extremidade da linha kernel e introduz os comandos especificados.](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-emergency-mount-no-root.gif)

> Nota: A execução das instruções acima irá deixá-lo na casca de emergência, para que também possa executar tarefas como a edição `fstab` . No entanto, a sugestão geralmente aceite é redefinir a sua palavra-passe de raiz e usá-lo para introduzir o modo de utilizador único. 


## <a name="access-for-centos"></a>Acesso para CentOS
Tal como o Red Hat Enterprise Linux, o modo de utilizador único no CentOS requer que o GRUB e o utilizador de raiz estejam ativados. 

### <a name="grub-access-in-centos"></a>Acesso GRUB no CentOS
CentOS vem com GRUB ativado fora da caixa. Para introduzir o GRUB, reinicie o seu VM com `sudo reboot` e prima qualquer tecla. Vais ver o ecrã grub aparecer.

### <a name="single-user-mode-in-centos"></a>Modo único de utilizador no CentOS
Siga as instruções do RHEL acima para ativar o modo de utilizador único no CentOS.

## <a name="access-for-ubuntu"></a>Acesso a Ubuntu 
As imagens Ubuntu não requerem uma palavra-passe de raiz. Se o sistema entrar em modo único de utilizador, pode utilizá-lo sem credenciais adicionais. 

### <a name="grub-access-in-ubuntu"></a>Acesso GRUB em Ubuntu
Para aceder ao GRUB, prima e mantenha 'Esc' enquanto o VM está a arrancar. 

Por predefinição, as imagens Ubuntu não mostrarão automaticamente o ecrã GRUB. Isto pode ser alterado com as seguintes instruções:
1. Abra `/etc/default/grub.d/50-cloudimg-settings.cfg` em um editor de texto à sua escolha
1. Alterar o `GRUB_TIMEOUT` valor para um valor não-zero
1. Abra `/etc/default/grub` em um editor de texto à sua escolha
1. Comentar a `GRUB_HIDDEN_TIMEOUT=1` linha
1. Executar `sudo update-grub`

### <a name="single-user-mode-in-ubuntu"></a>Modo de utilizador único em Ubuntu
Ubuntu irá deixá-lo automaticamente no modo de utilizador único se não conseguir arrancar normalmente. Para introduzir manualmente o modo de utilizador único, utilize as seguintes instruções:

1. Da GRUB, prima 'e' para editar a sua entrada de arranque (a entrada Ubuntu)
1. Procure a linha que começa `linux` com, em seguida, procure`ro`
1. Adicione `single` `ro` depois, garantindo que há um espaço antes e depois`single`
1. Prima Ctrl + X para reiniciar com estas definições e insira o modo de utilizador único

## <a name="access-for-coreos"></a>Acesso ao CoreOS
O modo de utilizador único no CoreOS requer que o GRUB seja ativado. 

### <a name="grub-access-in-coreos"></a>Acesso GRUB no CoreOS
Para aceder ao GRUB, prima qualquer tecla quando o seu VM estiver a arrancar.

### <a name="single-user-mode-in-coreos"></a>Modo único de utilizador no CoreOS
O CoreOS irá deixá-lo automaticamente no modo de utilizador único se não conseguir arrancar normalmente. Para introduzir manualmente o modo de utilizador único, utilize as seguintes instruções:
1. Da GRUB, prima 'e' para editar a sua entrada na bota
1. Procure a linha que começa `linux$` com. Deve haver 2, encapsulados em diferentes cláusulas se/else
1. Apêndice `coreos.autologin=ttyS0` ao fim de ambas as `linux$` linhas
1. Prima Ctrl + X para reiniciar com estas definições e insira o modo de utilizador único

## <a name="access-for-suse-sles"></a>Acesso a SUSE SLES
Imagens mais recentes do SLES 12 SP3+ permitem o acesso através da consola em série no caso de as botas do sistema entrarem em modo de emergência. 

### <a name="grub-access-in-suse-sles"></a>Acesso GRUB em SUSE SLES
O acesso GRUB em SLES requer configuração de bootloader via YaST. Para isso, siga estas instruções:

1. ssh no seu SLES VM e executar `sudo yast bootloader` . Utilize as `tab` `enter` teclas de chave, chave e seta para navegar através do menu. 
1. Navegue para `Kernel Parameters` , e verifique `Use serial console` . 
1. Adicione `serial --unit=0 --speed=9600 --parity=no` aos argumentos da Consola

1. Prima F10 para guardar as suas definições e saída
1. Para entrar no GRUB, reinicie o seu VM e pressione qualquer tecla durante a sequência de arranque para fazer com que o GRUB permaneça no ecrã
    - O tempo limite padrão para GRUB é 1s. Pode modificá-lo alterando a `GRUB_TIMEOUT` variável em`/etc/default/grub`

![Imagem animada mostrando uma interface de linha de comando. O utilizador introduz o texto especificado, seleciona a opção especificada e guarda as definições.](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-sles-yast-grub-config.gif)

### <a name="single-user-mode-in-suse-sles"></a>Modo único de utilizador em SUSE SLES
Será automaticamente atirado para a concha de emergência se o SLES não conseguir arrancar normalmente. Para introduzir manualmente a concha de emergência, utilize as seguintes instruções:

1. Da GRUB, prima 'e' para editar a sua entrada de arranque (a entrada SLES)
1. Procure a linha de núcleo com que começará`linux`
1. Apêndice `systemd.unit=emergency.target` ao fim da linha
1. Pressione ctrl + X para reiniciar com estas definições e introduza a concha de emergência
   > Note que será atirado para a casca de emergência com um sistema _de ficheiros apenas de leitura._ Se quiser fazer edições a quaisquer ficheiros, terá de voltar a montar o sistema de ficheiros com permissões de leitura-escrita. Para isso, `mount -o remount,rw /` entre na concha

## <a name="access-for-oracle-linux"></a>Acesso para Oracle Linux
Tal como o Red Hat Enterprise Linux, o modo de utilizador único no Oracle Linux requer que o GRUB e o utilizador de raiz estejam ativados. 

### <a name="grub-access-in-oracle-linux"></a>Acesso GRUB em Oracle Linux
Oracle Linux vem com GRUB ativado fora da caixa. Para entrar no GRUB, reinicie o seu VM com `sudo reboot` e prima 'Esc'. Vais ver o ecrã grub aparecer.

### <a name="single-user-mode-in-oracle-linux"></a>Modo de utilizador único no Oracle Linux
Siga as instruções do RHEL acima para ativar o modo de utilizador único no Oracle Linux.

## <a name="next-steps"></a>Passos seguintes
* A principal página de documentação da consola em série Linux está localizada [aqui.](../troubleshooting/serial-console-linux.md)
* Utilize consola em série para [chamadas NMI e SysRq](serial-console-nmi-sysrq.md)
* A Consola em Série também está disponível para [VMs windows](../troubleshooting/serial-console-windows.md)
* Saiba mais sobre [diagnósticos de arranque](../troubleshooting/boot-diagnostics.md)
