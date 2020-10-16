---
title: Configuração proativa da grub da consola de série Azure/ Microsoft Docs
description: Configure o GRUB através de várias distribuições permitindo o acesso a um único utilizador e modo de recuperação em máquinas virtuais Azure.
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: vashan
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/10/2019
ms.author: mimckitt
ms.openlocfilehash: aba47500400004c1d6a7044a266bad6f20d5d9c9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91360553"
---
# <a name="proactively-ensuring-you-have-access-to-grub-and-sysrq-could-save-you-lots-of-down-time"></a>Garantir proativamente que você tem acesso a GRUB e sysrq poderia economizar muito tempo de insudor

Ter acesso à Consola em Série e GRUB melhorará os tempos de recuperação da sua Máquina Virtual IaaS Linux na maioria dos casos. A GRUB oferece opções de recuperação que de outra forma demorariam mais tempo a recuperar o seu VM. 


As razões para realizar uma recuperação de VM são muitas e podem ser atribuídas a cenários como:

   - Sistemas de ficheiros corruptos/kernel/MBR (Master Boot Record)
   - Atualizações falhadas do núcleo
   - Parâmetros de núcleo grub incorretos
   - Configurações fstab incorretas
   - Configurações de firewall
   - Senha perdida
   - Ficheiros de configurações de sshd mutilados
   - Configurações de rede

 Muitos outros cenários como detalhado [aqui](./serial-console-linux.md#common-scenarios-for-accessing-the-serial-console)

Verifique se pode aceder ao GRUB e à consola Serial nos seus VMs implantados em Azure. 

Se for novo na Consola em Série, consulte [este link](./serial-console-linux.md).

> [!TIP]
> Certifique-se de que aceita cópias de segurança de ficheiros antes de fazer alterações

Veja este vídeo abaixo para ver como pode recuperar rapidamente o seu Linux VM uma vez que tenha acesso ao GRUB

[Recuperar vídeo de Linux VM](https://youtu.be/KevOc3d_SG4)

Há uma série de métodos para ajudar a recuperar os VMs linux. Num ambiente nublado, este processo tem sido desafiante.
Estão a ser feitos progressos contínuos na ferramenta e nas funcionalidades que garantem a recuperação rápida dos serviços.

Com a Consola Em Série Azure, podes interagir com o teu Linux VM como se estivesses na consola de um sistema.

Pode manipular muitos ficheiros de configuração, incluindo como o núcleo será iniciado. 

Os administradores mais experientes do Linux/Unix apreciarão os modos **de utilizador** e de emergência únicos que são  **acessíveis** através da Consola em Série Azure, tornando a troca de discos e a eliminação de VM para muitos cenários de recuperação redundantes.

O método de recuperação depende do problema que está a ser experimentado, por exemplo, uma palavra-passe perdida ou deslocada pode ser reiniciada através das opções do portal Azure ->  **redefinir a palavra-passe**. A **função Palavra-Passe reset** é conhecida como uma extensão e comunica com o agente Linux Guest.

Outras extensões, como o Script Personalizado, estão disponíveis no entanto estas opções requerem que o **waagent** Linux esteja de pé e em um estado saudável, o que nem sempre acontece.

![estado de agente](./media/virtual-machines-serial-console/agent-status.png)


Garantir que tem acesso à Consola em Série Azure e AO GRUB significa que uma alteração de palavra-passe ou uma configuração incorreta pode ser retificada numa questão de minutos em vez de horas. Pode até forçar o VM a arrancar a partir de um núcleo alternativo caso tenha vários núcleos no disco no cenário em que o seu núcleo primário se torne corrupto.

![multi núcleo](./media/virtual-machines-serial-console/more-kernel.png)

## <a name="suggested-order-of-recovery-methods"></a>Ordem sugerida dos métodos de recuperação:

- Consola em série Azure

- Troca de Discos – pode ser automatizado usando:

   - [Scripts de recuperação powerShell](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager)
   - [bata scripts de recuperação](https://github.com/sribs/azure-support-scripts)

- Método Legado

## <a name="disk-swap-video"></a>Vídeo de troca de discos:

Se não tiver acesso ao GRUB veja [este](https://youtu.be/m5t0GZ5oGAc) vídeo e veja como pode automatizar facilmente o procedimento de troca de discos para recuperar o seu VM

## <a name="challenges"></a>Desafios:

Nem todos os VMs Linux Azure são configurados por padrão para acesso GRUB e nem todos estão configurados para serem interrompidos com os comandos sysrq. Algumas desfigurações mais antigas, como o SLES 11, não estão configuradas para exibir o pedido de login na Consola em Série Azure

Neste artigo, vamos rever várias distribuições de Linux e configurações de documentos sobre como disponibilizar o GRUB.




## <a name="how-to-configure-linux-vm-to-accept-sysrq-keys"></a>Como configurar o Linux VM para aceitar chaves SysRq
A tecla sysrq é ativada em alguns destros Linux mais recentes por padrão, embora em outros possa ser configurado para aceitar valores apenas para certas funções SysRq.
Em distros mais velhos, pode ser completamente desativado.

A funcionalidade SysRq é útil para reiniciar um VM despenhado ou não respondendo diretamente a partir da Consola em Série Azure, também útil para obter acesso ao menu GRUB, reiniciando alternativamente um VM de outra janela do portal ou sessão de ssh pode deixar cair a sua atual ligação de consola, expirando assim os tempos GRUB que são usados para exibir o menu GRUB.
O VM deve ser configurado para aceitar um valor de 1 para o parâmetro do núcleo, que permite todas as funções de sysrq ou 128, que permite reiniciar/desligar


[Ativar o vídeo sysrq](https://youtu.be/0doqFRrHz_Mc)


Para configurar o VM para aceitar um reboot através dos comandos SysRq no portal Azure, terá de definir um valor de 1 para o parâmetro kernel kernel.sysrq

Para que esta configuração persista um reboot, adicione uma entrada no ficheiro **sysctl.conf**

`echo kernel.sysrq = 1 >> /etc/sysctl.conf`

Para configurar o parâmetro do núcleo dinamicamente

`sysctl -w kernel.sysrq=1`

Se não tiver acesso **à raiz** ou se o sudo estiver partido, não será possível configurar sysrq a partir de uma pressão de concha.

Pode ativar o sysrq neste cenário utilizando o portal Azure. Este método pode ser benéfico se o ficheiro  **sudoers.d/waagent** tiver sido quebrado ou tiver sido eliminado.

Utilizando o portal Azure Operations -> Executar o Comando -> Função RunShellScript, requer que o processo waagent seja saudável, pode injetar este comando para ativar o sysrq

`sysctl -w kernel.sysrq=1 ; echo kernel.sysrq = 1 >> /etc/sysctl.conf`

Como mostrado aqui: ![ ativar sysrq2](./media/virtual-machines-serial-console/enabling-sysrq-2.png)

Uma vez concluído, pode então tentar aceder ao **sysrq** e deve ver se é possível reiniciar.

![permitir sysrq3](./media/virtual-machines-serial-console/enabling-sysrq-3.png)

Selecione **Reboot** e Envie o Comando **SysRq**

![permitir sysrq4](./media/virtual-machines-serial-console/enabling-sysrq-4.png)

O sistema deve registar uma mensagem de reset como esta

![permitir sysrq5](./media/virtual-machines-serial-console/enabling-sysrq-5.png)


## <a name="ubuntu-grub-configuration"></a>Configuração de Ubuntu GRUB

Por predefinição, deverá poder aceder ao GRUB segurando a tecla **Esc** durante a bota VM, se o menu GRUB não for apresentado, pode forçar e manter o menu GRUB no ecrã na Consola em Série Azure utilizando uma destas opções.

**Opção 1** - Força a GRUB a ser exibida no Ecrã 

Atualize o ficheiro /etc/predefinido/grub.d/50-cloudimg-settings.cfg para manter o menu GRUB no ecrã para o TIMEOUT especificado.
Não é obrigado a bater em **Esc, pois** o GRUB será exibido imediatamente.

```
GRUB_TIMEOUT=0

change to

GRUB_TIMEOUT=5
```

**Opção 2** - Permite que a **Esc** seja pressionada antes do arranque

Comportamento semelhante pode ser experimentado fazendo alterações no ficheiro /etc/default/grub e observar um tempo limite de 3 segundos para atingir **esc**


Comentando estas duas linhas:

```
#GRUB_HIDDEN_TIMEOUT=0
#GRUB_HIDDEN_TIMEOUT_QUIET=true
```
e adicionar esta linha:

```
GRUB_TIMEOUT_STYLE=countdown
```


## <a name="ubuntu-1204"></a>Ubuntu 12 \. 04

O Ubuntu 12.04 permitirá o acesso à consola em série, mas não oferece a capacidade de interagir. Um **login:** o pedido não é visto


Para 12.04 para obter um **login:** atenção:
1. Criar um ficheiro chamado /etc/init/ttyS0.conf contendo o seguinte texto:

    ```
    # ttyS0 - getty
    #
    # This service maintains a getty on ttyS0 from the point the system is
    # started until it is shut down again.
    start on stopped rc RUNLEVEL=[12345]
    stop on runlevel [!12345]
    
    respawn
    exec /sbin/getty -L 115200 ttyS0 vt102
    ```    

2. Peça para começar o getty     
    ```
    sudo start ttyS0
    ```
 
As definições necessárias para configurar a consola em série para versões Ubuntu podem ser encontradas [aqui](https://help.ubuntu.com/community/SerialConsoleHowto)

## <a name="ubuntu-recovery-mode"></a>Modo de Recuperação Ubuntu

Opções adicionais de recuperação e limpeza estão disponíveis para Ubuntu via GRUB no entanto estas definições só são acessíveis se configurar parâmetros de kernel em conformidade.
A não configuração deste parâmetro de arranque do núcleo obrigaria o menu Recovery a ser enviado para o Azure Diagnostics e não para a Consola em Série Azure.
Pode obter acesso ao Menu de Recuperação de Ubuntu seguindo estes passos:

Interrompa o processo boot e aceda ao menu GRUB

Selecione Opções Avançadas para Ubuntu e pressione a entrada

![A screenshot mostra a consola Serial com opções Avançadas para Ubuntu selecionadas.](./media/virtual-machines-serial-console/ubunturec1.png)

Selecione a visualização da linha *(modo de recuperação)* não prima a entrada, mas prima "e"

![O Screenshot mostra a consola Serial com uma versão de modo de recuperação selecionada.](./media/virtual-machines-serial-console/ubunturec2.png)

Localize a linha que carregará o núcleo e substitua o último **nomodeset** do parâmetro com destino como **consola=ttyS0**

```
linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery nomodeset

change to

linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery console=ttyS0
```

![A screenshot mostra a consola Serial com o valor alterado.](./media/virtual-machines-serial-console/ubunturec3.png)

Pressione **ctrl-x** para iniciar e carregue o grão.
Se tudo correr bem, verá estas opções adicionais, o que pode ajudar a executar outras opções de recuperação

![O Screenshot mostra a consola Serial no Menu Recovery, que oferece opções de recuperação adicionais.](./media/virtual-machines-serial-console/ubunturec4.png)


## <a name="red-hat-grub-configuration"></a>Configuração GRUB de chapéu vermelho

## <a name="red-hat-74-grub-configuration"></a>Configuração do chapéu \. vermelho 7 4 \+ GRUB
A configuração padrão /etc/predefinição/larva nestas versões está adequadamente configurada

```
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL="serial console"
GRUB_SERIAL_COMMAND="serial"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```

Ativar a chave SysRq

```
sysctl -w kernel.sysrq=1;echo kernel.sysrq = 1 >> /etc/sysctl.conf;sysctl -a | grep -i sysrq
```

## <a name="red-hat-72-and-73-grub-configuration"></a>Configuração do Chapéu Vermelho 7 \. 2 e \. 7 3 GRUB
O ficheiro para modificar é /etc/predefinição/grub – um config padrão parece este exemplo:

```
GRUB_TIMEOUT=1
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```

Alterar as seguintes linhas em /etc/predefinição/comida

```
GRUB_TIMEOUT=1 

to

GRUB_TIMEOUT=5
```


```
GRUB_TERMINAL_OUTPUT="console"

to

GRUB_TERMINAL="serial console"
```

Adicione também esta linha:

```
GRUB_SERIAL_COMMAND=”serial –speed=115200 –unit=0 –word=8 –parity=no –stop=1″
```

/etc/default/grub deve agora parecer semelhante a este exemplo:

```
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```
 
Configuração de comida completa e atualizada usando

`grub2-mkconfig -o /boot/grub2/grub.cfg`

Definir o parâmetro do núcleo SysRq:

`sysctl -w kernel.sysrq = 1;echo kernel.sysrq = 1 >> /etc/sysctl.conf;sysctl -a | grep -i sysrq`

Em alternativa, pode configurar GRUB e SysRq utilizando uma única linha na concha ou através do Comando run. Faça cópia de segurança dos seus ficheiros antes de executar este comando:


`cp /etc/default/grub /etc/default/grub.bak; sed -i 's/GRUB_TIMEOUT=1/GRUB_TIMEOUT=5/g' /etc/default/grub; sed -i 's/GRUB_TERMINAL_OUTPUT="console"/GRUB_TERMINAL="serial console"/g' /etc/default/grub; echo "GRUB_SERIAL_COMMAND=\"serial --speed=115200 --unit=0 --word=8 --parity=no --stop=1\"" >> /etc/default/grub;grub2-mkconfig -o /boot/grub2/grub.cfg;sysctl -w kernel.sysrq=1;echo kernel.sysrq = 1 /etc/sysctl.conf;sysctl -a | grep -i sysrq`


## <a name="red-hat-6x-grub-configuration"></a>Configuração do chapéu vermelho 6 \. x GRUB
O ficheiro a modificar é /boot/grub/grub.conf. O `timeout` valor determinará quanto tempo é mostrado o GRUB.

```
#boot=/dev/vda
default=0
timeout=15
splashimage=(hd0,0)/grub/splash.xpm.gz
#hiddenmenu
serial --unit=0 --speed=9600
terminal serial
terminal --timeout=5 serial console
```


O último terminal de linha  *--timeout=5 em série* aumentará ainda mais o tempo limite **de GRUB** adicionando uma solicitação de 5 segundos mostrando **Pressione qualquer tecla para continuar.**

![A screenshot mostra uma consola com saída.](./media/virtual-machines-serial-console/rh6-1.png)

O menu GRUB deve aparecer no ecrã para o tempo limite configurado=15 sem a necessidade de pressionar o Esc. Certifique-se de clicar na Consola no Browser para tornar ativo o menu e selecione o núcleo necessário

![A screenshot mostra uma consola com duas opções Linux.](./media/virtual-machines-serial-console/rh6-2.png)

## <a name="suse"></a>Rio Suse

## <a name="sles-12-sp1"></a>SLES 12 sp1
Ou usa o bootloader YaST de acordo com os [documentos](./serial-console-grub-single-user-mode.md#grub-access-in-suse-sles) oficiais

Ou adicionar/alterar para /etc/predefinição/grub os seguintes parâmetros:

```
GRUB_TERMINAL=serial
GRUB_TIMEOUT=5
GRUB_SERIAL_COMMAND="serial --unit=0 --speed=9600 --parity=no"

```
Verifique se o ttys0 é usado no GRUB_CMDLINE_LINUX ou GRUB_CMDLINE_LINUX_DEFAULT

```
GRUB_CMDLINE_LINUX_DEFAULT="console=ttyS0,9600n"
```

Recriar a comida.cfg

`grub2-mkconfig -o /boot/grub2/grub.cfg`


## <a name="sles-11-sp4"></a>SLES 11 SP4 
A Consola em Série aparece e exibe mensagens de arranque, mas não apresenta um **login:** prompt

Abra uma sessão de ssh no VM e atualize o ficheiro **/etc/inittab** descomprimindo esta linha:

```
#S0:12345:respawn:/sbin/agetty -L 9600 ttyS0 vt102
```

Em seguida, executar o comando 

`telinit q`

Para ativar o GRUB, devem ser feitas as seguintes alterações para /boot/grub/menu.lst 

```
timeout 5
serial --unit=0 --speed=9600 --parity=no
terminal --timeout=5 serial console

root (hd0,0)
kernel /boot/vmlinuz-3.0.101-108.74-default root=/dev/disk/by-uuid/ab6b62bb--
1a8c-45eb-96b1-1fbc535b9265 disk=/dev/sda  USE_BY_UUID_DEVICE_NAMES=1 earlyprinttk=ttyS0 console=ttyS0 rootdelay=300  showopts vga=0x314
```

 Esta configuração permitirá que a mensagem **Pressione qualquer tecla para continuar** a aparecer na consola durante 5 segundos 

Em seguida, apresentará o menu GRUB por mais 5 segundos - premindo a seta para baixo, interromperá o contador e selecionará um núcleo que pretende iniciar ou apendam o **single** de palavra-chave para um único modo de utilizador que requer a definição da palavra-passe de raiz.

A anexação do comando **init=/bin/bash** carregará o núcleo, mas garante que o programa init é substituído por uma casca de bash.

Você terá acesso a uma concha sem ter que inserir uma senha. Pode então proceder à atualização da palavra-passe para contas Linux ou fazer outras alterações de configuração.


## <a name="force-the-kernel-to-a-bash-prompt"></a>Forçar o núcleo a uma batida pronta
Ter acesso ao GRUB permite-lhe interromper o processo de inicialização esta interação é útil para muitos procedimentos de recuperação.
Se não tiver uma palavra-passe de raiz e um único utilizador exigir que tenha uma senha de raiz, pode iniciar o núcleo substituindo o programa init por uma pontuação – esta interrupção pode ser conseguida através do appending init=/bin/bash para a linha de arranque do núcleo

![A screenshot mostra uma consola com a linha de arranque atualizada.](./media/virtual-machines-serial-console/bash1.png)

Remonte o seu sistema de ficheiros /(raiz) RW usando o comando

`mount -o remount,rw /`

![A screenshot mostra uma consola com uma ação de re-montagem.](./media/virtual-machines-serial-console/bash2.png)


Agora pode realizar a alteração da palavra-passe de raiz ou muitas outras alterações de configuração do Linux

![A screenshot mostra uma consola onde pode alterar a palavra-passe de raiz e outra configuração.](./media/virtual-machines-serial-console/bash3.png)

Reiniciar o VM com 

`/sbin/reboot -f`




## <a name="single-user-mode"></a>Modo utilizador único

Em alternativa, poderá ter de aceder ao VM em modo único de utilizador ou de emergência. Selecione o núcleo que pretende iniciar ou interromper utilizando teclas de seta.
Introduza o modo pretendido colocando a palavra-chave **single** ou **1** na linha de arranque do núcleo. Nos sistemas RHEL, também pode anexar **rd.break**.

Para obter mais informações sobre como aceder ao modo de utilizador único, consulte [este doc](./serial-console-grub-single-user-mode.md#general-single-user-mode-access) 


![single_user_ubuntu](./media/virtual-machines-serial-console/single-user-ubuntu.png)


## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [a Consola em Série Azure]( ./serial-console-linux.md)