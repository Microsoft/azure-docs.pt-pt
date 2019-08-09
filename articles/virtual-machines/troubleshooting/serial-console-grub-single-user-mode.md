---
title: Console serial do Azure para GRUB e modo de usuário único | Microsoft Docs
description: Usando o console serial para grub em máquinas virtuais do Azure.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/06/2019
ms.author: alsin
ms.openlocfilehash: 656bc8329d6273695e4da24a7e7d13c9df6a1080
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68846601"
---
# <a name="use-serial-console-to-access-grub-and-single-user-mode"></a>Usar o console serial para acessar o GRUB e o modo de usuário único
GRUB é o carregador de inicialização unificado geral, que é provavelmente a primeira coisa que você verá ao inicializar uma VM. Como ele é exibido antes do sistema operacional ser iniciado, ele não é acessível via SSH. No GRUB, você pode modificar a configuração de inicialização para inicializar no modo de usuário único, entre outras coisas.

O modo de usuário único é um ambiente mínimo com funcionalidade mínima. Pode ser útil para investigar problemas de inicialização, problemas de sistema de arquivos ou problemas de rede. Menos serviços podem ser executados em segundo plano e, dependendo do runlevel, um sistema de arquivos pode nem mesmo ser montado automaticamente.

O modo de usuário único também é útil em situações em que sua VM só pode ser configurada para aceitar chaves SSH para fazer logon. Nesse caso, talvez você possa usar o modo de usuário único para criar uma conta com autenticação de senha. Observe que o serviço de console serial só permitirá que usuários com acesso no nível de colaborador ou superior acessem o console serial de uma VM.

Para entrar no modo de usuário único, você precisará inserir o GRUB quando sua VM estiver sendo inicializada e modificar a configuração de inicialização no GRUB. As instruções detalhadas para inserir o GRUB estão abaixo. Em geral, você pode usar o botão reiniciar no console serial da VM para reiniciar a VM e mostrar o GRUB se a VM tiver sido configurada para mostrar o GRUB.

![Botão de reinicialização do console serial do Linux](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-bar.png)

## <a name="general-grub-access"></a>Acesso de GRUB geral
Para acessar o GRUB, será necessário reinicializar sua VM enquanto mantém a folha do console serial aberta. Alguns distribuições exigirão entrada de teclado para mostrar o GRUB, enquanto outros mostrarão o GRUB automaticamente por alguns segundos e permitirá que a entrada do teclado do usuário cancele o tempo limite.

Você desejará garantir que o GRUB esteja habilitado em sua VM para poder acessar o modo de usuário único. Dependendo do seu distribuição, pode haver algum trabalho de configuração para garantir que o GRUB esteja habilitado. Informações específicas do distribuição estão disponíveis abaixo e [neste link](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/).

### <a name="restart-your-vm-to-access-grub-in-serial-console"></a>Reinicie sua VM para acessar o GRUB no console serial
Você pode reiniciar a VM no console serial navegando até o botão de energia e clicando em "reiniciar VM". Isso iniciará uma reinicialização de VM e você verá uma notificação dentro do portal do Azure em relação à reinicialização.
A reinicialização da VM também pode ser feita com um `'b'` comando SysRq se [SysRq](./serial-console-nmi-sysrq.md) estiver habilitado. Siga as instruções específicas do distribuição abaixo para saber o que esperar do GRUB ao reinicializar.

![Reinicialização do console serial do Linux](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-ubuntu.gif)

## <a name="general-single-user-mode-access"></a>Acesso geral ao modo de usuário único
O acesso manual ao modo de usuário único pode ser necessário em situações em que você não tenha configurado uma conta com autenticação de senha. Será necessário modificar a configuração do GRUB para inserir manualmente o modo de usuário único. Depois de fazer isso, consulte usar o modo de usuário único para redefinir ou adicionar uma senha para obter mais instruções.

Nos casos em que a VM não consegue inicializar, o distribuições geralmente o colocará automaticamente no modo de usuário único ou no modo de emergência. Outros, no entanto, exigem configuração adicional para que possam descartá-lo no modo de usuário único ou emergência automaticamente (como configurar uma senha raiz).

### <a name="use-single-user-mode-to-reset-or-add-a-password"></a>Usar o modo de usuário único para redefinir ou adicionar uma senha
Quando você estiver no modo de usuário único, faça o seguinte para adicionar um novo usuário com privilégios sudo:
1. Executar `useradd <username>` para adicionar um usuário
1. Executar `sudo usermod -a -G sudo <username>` para conceder os novos privilégios de raiz de usuário
1. Use `passwd <username>` para definir a senha para o novo usuário. Em seguida, será possível fazer logon como o novo usuário


## <a name="access-for-red-hat-enterprise-linux-rhel"></a>Acesso para Red Hat Enterprise Linux (RHEL)
O RHEL colocará você no modo de usuário único automaticamente se ele não puder ser inicializado normalmente. No entanto, se você não tiver configurado o acesso de raiz para o modo de usuário único, não terá uma senha raiz e não poderá fazer logon. Há uma solução alternativa (consulte ' inserindo manualmente o modo de usuário único ' abaixo), mas a sugestão é configurar o acesso à raiz inicialmente.

### <a name="grub-access-in-rhel"></a>Acesso do GRUB no RHEL
O RHEL vem com o GRUB habilitado para uso. Para entrar no grub, reinicialize `sudo reboot` sua VM com e pressione qualquer tecla. Você verá a tela GRUB aparecer.

> Nota: A Red Hat também fornece documentação para inicialização no modo de resgate, no modo de emergência, no modo de depuração e na redefinição da senha raiz. [Clique aqui para acessá-lo](https://aka.ms/rhel7grubterminal).

### <a name="set-up-root-access-for-single-user-mode-in-rhel"></a>Configurar o acesso à raiz para o modo de usuário único no RHEL
O modo de usuário único no RHEL requer que o usuário raiz seja habilitado, o que está desabilitado por padrão. Se você tiver a necessidade de habilitar o modo de usuário único, use as seguintes instruções:

1. Faça logon no sistema Red Hat via SSH
1. Alternar para raiz
1. Habilitar senha para usuário raiz
    * `passwd root`(definir uma senha raiz forte)
1. Verifique se o usuário raiz só pode fazer logon via ttyS0
    * `edit /etc/ssh/sshd_config`e verifique se PermitRootLogIn está definido como não
    * `edit /etc/securetty file`para permitir o logon somente via ttyS0

Agora, se o sistema for inicializado em modo de usuário único, você poderá fazer logon por meio da senha raiz.

Como alternativa para o RHEL 7.4 + ou 6,9 + você pode habilitar o modo de usuário único nos prompts do GRUB, consulte as instruções [aqui](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single)

### <a name="manually-enter-single-user-mode-in-rhel"></a>Inserir manualmente o modo de usuário único no RHEL
Se você tiver configurado o acesso de GRUB e raiz com as instruções acima, poderá inserir o modo de usuário único com as seguintes instruções:

1. Pressione ' Esc ' ao reiniciar a VM para entrar no GRUB
1. No GRUB, pressione ' e ' para editar o sistema operacional selecionado no qual você deseja inicializar (geralmente a primeira linha)
1. Localizar a linha de kernel no Azure, isso será iniciado com`linux16`
1. Pressione Ctrl + E para ir para o final da linha
1. Adicione o seguinte ao final da linha:`systemd.unit=rescue.target`
    * Isso irá inicializá-lo no modo de usuário único. Se você quiser usar o modo de emergência, `systemd.unit=emergency.target` adicione ao final da linha em vez de`systemd.unit=rescue.target`
1. Pressione CTRL + X para sair e reinicializar com as configurações aplicadas
1. Você será solicitado a fornecer a senha de administrador antes de poder entrar no modo de usuário único – essa é a mesma senha que você criou nas instruções acima

    ![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-enter-emergency-shell.gif)

### <a name="enter-single-user-mode-without-root-account-enabled-in-rhel"></a>Entrar no modo de usuário único sem a conta raiz habilitada no RHEL
Se você não passou pelas etapas acima para habilitar o usuário raiz, você ainda pode redefinir sua senha raiz. Use as seguintes instruções:

> Nota: Se você estiver usando o SELinux, verifique se executou as etapas adicionais descritas na documentação do Red Hat [aqui](https://aka.ms/rhel7grubterminal) ao redefinir a senha raiz.

1. Pressione ' Esc ' ao reiniciar a VM para entrar no GRUB
1. No GRUB, pressione ' e ' para editar o sistema operacional selecionado no qual você deseja inicializar (geralmente a primeira linha)
1. Localizar a linha de kernel no Azure, isso será iniciado com`linux16`
1. Adicione `rd.break` ao final da linha, garantindo que há um espaço antes `rd.break` (Veja o exemplo abaixo)
    - Isso interromperá o processo de inicialização antes de o controle `initramfs` ser `systemd`passado de para, conforme descrito na documentação do Red Hat [aqui](https://aka.ms/rhel7rootpassword).
1. Pressione CTRL + X para sair e reinicializar com as configurações aplicadas
1. Depois de inicializar, você será colocado no modo de emergência com um sistema de arquivos somente leitura. Entrar `mount -o remount,rw /sysroot` no Shell para remontar o sistema de arquivos raiz com permissões de leitura/gravação
1. Depois de inicializar o modo de usuário único, digite `chroot /sysroot` para alternar para o `sysroot` com Jail Break
1. Agora você é raiz. Você pode redefinir sua senha raiz com `passwd` o e, em seguida, usar as instruções acima para entrar no modo de usuário único. Digite `reboot -f` para reinicializar quando terminar.

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-emergency-mount-no-root.gif)

> Nota: A execução das instruções acima irá descartá-lo no Shell de emergência, para que você também possa executar tarefas `fstab`como edição. No entanto, a sugestão geralmente aceita é redefinir a senha raiz e usá-la para entrar no modo de usuário único.


## <a name="access-for-centos"></a>Acesso para CentOS
Assim como Red Hat Enterprise Linux, o modo de usuário único no CentOS requer GRUB e o usuário raiz a ser habilitado.

### <a name="grub-access-in-centos"></a>Acesso de GRUB no CentOS
O CentOS vem com o GRUB habilitado para uso. Para entrar no grub, reinicialize `sudo reboot` sua VM com e pressione qualquer tecla. Você verá a tela GRUB aparecer.

### <a name="single-user-mode-in-centos"></a>Modo de usuário único no CentOS
Siga as instruções para RHEL acima para habilitar o modo de usuário único no CentOS.

## <a name="access-for-ubuntu"></a>Acesso ao Ubuntu
As imagens do Ubuntu não exigem uma senha raiz. Se o sistema for inicializado no modo de usuário único, você poderá usá-lo sem credenciais adicionais.

### <a name="grub-access-in-ubuntu"></a>Acesso de GRUB no Ubuntu
Para acessar o GRUB, pressione e segure ' Esc ' enquanto a VM estiver inicializando.

Por padrão, imagens do Ubuntu podem não mostrar automaticamente a tela GRUB. Isso pode ser alterado com as seguintes instruções:
1. Abrir `/etc/default/grub.d/50-cloudimg-settings.cfg` em um editor de texto de sua escolha
1. Alterar o `GRUB_TIMEOUT` valor para um valor diferente de zero
1. Abrir `/etc/default/grub` em um editor de texto de sua escolha
1. Comentar a `GRUB_HIDDEN_TIMEOUT=1` linha
1. Verifique se há uma linha que diz`GRUB_TIMEOUT_STYLE=menu`
1. Execute `sudo update-grub`

### <a name="single-user-mode-in-ubuntu"></a>Modo de usuário único no Ubuntu
O Ubuntu o colocará no modo de usuário único automaticamente se ele não puder ser inicializado normalmente. Para inserir manualmente o modo de usuário único, use as seguintes instruções:

1. No GRUB, pressione ' e ' para editar a entrada de inicialização (a entrada do Ubuntu)
1. Procure a linha que começa com `linux`e procure por`ro`
1. Adicionar `single` depois`ro`, garantindo que há um espaço antes e depois`single`
1. Pressione CTRL + X para reinicializar com essas configurações e insira o modo de usuário único

### <a name="using-grub-to-invoke-bash-in-ubuntu"></a>Usando o GRUB para invocar o bash no Ubuntu
Pode haver situações (como uma senha raiz esquecida) em que talvez você ainda não consiga acessar o modo de usuário único em sua VM do Ubuntu depois de tentar as instruções acima. Você também pode instruir o kernel a executar/bin/bash como init, em vez da inicialização do sistema, que fornecerá um shell bash e permitirá a manutenção do sistema. Use as seguintes instruções:

1. No GRUB, pressione ' e ' para editar a entrada de inicialização (a entrada do Ubuntu)
1. Procure a linha que começa com `linux`e procure por`ro`
1. Substituir `ro` por`rw init=/bin/bash`
    - Isso montará seu sistema de arquivos como leitura/gravação e usará/bin/bash como o processo de inicialização
1. Pressione CTRL + X para reinicializar com essas configurações

## <a name="access-for-coreos"></a>Acesso para CoreOS
O modo de usuário único no CoreOS exige que o GRUB esteja habilitado.

### <a name="grub-access-in-coreos"></a>Acesso de GRUB em CoreOS
Para acessar o GRUB, pressione qualquer tecla quando sua VM estiver inicializando.

### <a name="single-user-mode-in-coreos"></a>Modo de usuário único no CoreOS
O CoreOS o colocará automaticamente no modo de usuário único se ele não puder ser inicializado normalmente. Para inserir manualmente o modo de usuário único, use as seguintes instruções:
1. No GRUB, pressione ' e ' para editar a entrada de inicialização
1. Procure a linha que começa com `linux$`. Deve haver 2, encapsulado em cláusulas if/else diferentes
1. Acrescentar `coreos.autologin=ttyS0` ao final de ambas `linux$` as linhas
1. Pressione CTRL + X para reinicializar com essas configurações e insira o modo de usuário único

## <a name="access-for-suse-sles"></a>Acesso para SUSE SLES
Imagens mais recentes do SLES 12 SP3 + permitem o acesso por meio do console serial, caso o sistema seja inicializado no modo de emergência.

### <a name="grub-access-in-suse-sles"></a>Acesso de GRUB no SUSE SLES
O acesso GRUB no SLES requer a configuração do carregador de configurações via YaST. Para fazer isso, siga estas instruções:

1. SSH em sua VM SLES e execute `sudo yast bootloader`. Use as `tab` teclas chave `enter` , chave e seta para navegar pelo menu.
1. Navegue até `Kernel Parameters`e marque `Use serial console`.
1. Adicionar `serial --unit=0 --speed=9600 --parity=no` aos argumentos do console

1. Pressione F10 para salvar as configurações e sair
1. Para entrar no GRUB, reinicialize sua VM e pressione qualquer tecla durante a sequência de inicialização para tornar o GRUB permanecer na tela
    - O tempo limite padrão para GRUB é 1S. Você pode modificar isso alterando a `GRUB_TIMEOUT` variável em`/etc/default/grub`

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-sles-yast-grub-config.gif)

### <a name="single-user-mode-in-suse-sles"></a>Modo de usuário único no SUSE SLES
Você será automaticamente Descartado no Shell de emergência se o SLES não puder ser inicializado normalmente. Para inserir manualmente o Shell de emergência, use as seguintes instruções:

1. No GRUB, pressione ' e ' para editar a entrada de inicialização (a entrada SLES)
1. Procure a linha de kernel-ela começará com`linux`
1. Anexar `systemd.unit=emergency.target` ao final da linha
1. Pressione CTRL + X para reinicializar com essas configurações e insira o Shell de emergência
   > Observe que você será Descartado no Shell de emergência com um sistema de arquivos _somente leitura_ . Se você quiser fazer qualquer edição em qualquer arquivo, será necessário remontar o sistema de arquivos com permissões de leitura/gravação. Para fazer isso, insira `mount -o remount,rw /` no Shell

## <a name="access-for-oracle-linux"></a>Acesso para Oracle Linux
Assim como Red Hat Enterprise Linux, o modo de usuário único no Oracle Linux requer GRUB e o usuário raiz a ser habilitado.

### <a name="grub-access-in-oracle-linux"></a>Acesso de GRUB no Oracle Linux
Oracle Linux vem com o GRUB habilitado pronto para uso. Para entrar no grub, reinicialize `sudo reboot` sua VM com e pressione ' Esc '. Você verá a tela GRUB aparecer. Se você não vir o grub, verifique se o valor da `GRUB_TERMINAL` linha contém "console serial", desta forma:. `GRUB_TERMINAL="serial console"`

### <a name="single-user-mode-in-oracle-linux"></a>Modo de usuário único no Oracle Linux
Siga as instruções para RHEL acima para habilitar o modo de usuário único no Oracle Linux.

## <a name="next-steps"></a>Passos Seguintes
* A página principal de documentação do Linux do console serial está localizada [aqui](serial-console-linux.md).
* Saiba como usar o console serial para [habilitar o grub em vários distribuições](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/)
* Usar o console serial para [chamadas NMI e SysRq](serial-console-nmi-sysrq.md)
* O console serial também está disponível para VMs do [Windows](serial-console-windows.md)
* Saiba mais sobre o [diagnóstico de inicialização](boot-diagnostics.md)
