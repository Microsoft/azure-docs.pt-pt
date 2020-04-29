---
title: Configuração de GRUB proactiva da Consola Em Série Azure Microsoft Docs
description: Configure o GRUB em várias distribuições permitindo o acesso de um único utilizador e modo de recuperação em máquinas virtuais Azure.
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
ms.openlocfilehash: 573bd0797e63fc512e59b0e0882c718e4569111c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81262898"
---
# <a name="proactively-ensuring-you-have-access-to-grub-and-sysrq-could-save-you-lots-of-down-time"></a>Garantir proativamente que você tem acesso a GRUB e sysrq poderia poupar-lhe muito tempo de descanso

Ter acesso à Consola Em Série e ÀRirá melhorará os tempos de recuperação da sua Máquina Virtual IaaS Linux na maioria dos casos. A GRUB oferece opções de recuperação que de outra forma demorariam mais tempo a recuperar o seu VM. 


As razões para realizar uma recuperação vm são muitas e podem ser atribuídas a cenários como:

   - Sistemas de ficheiros corruptos/kernel/MBR (Master Boot Record)
   - Melhorias de kernel falhadas
   - Parâmetros de kernel GRUB incorretos
   - Configurações de fstab incorretas
   - Configurações de firewall
   - Senha perdida
   - Ficheiros de configurações de sshd mutilados
   - Configurações de rede

 Muitos outros cenários detalhados [aqui](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux#common-scenarios-for-accessing-the-serial-console)

Verifique se pode aceder à GRUB e à consola Serial nos seus VMs implantados no Azure. 

Se for novo na Consola em Série, consulte [este link](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux/).

> [!TIP]
> Certifique-se de que aceita cópias de segurança dos ficheiros antes de efazer alterações

Veja este vídeo abaixo para ver como pode recuperar rapidamente o seu VM Linux assim que tiver acesso à GRUB

[Recuperar vídeo VM Linux](https://youtu.be/KevOc3d_SG4)

Há uma série de métodos para ajudar na recuperação de VMs Linux. Num ambiente cloud, este processo tem sido desafiante.
Estão a ser feitos progressos contínuos na ferramenta e nas funcionalidades para garantir que os serviços sejam recuperados rapidamente.

Com a Consola Em Série Azure, podes interagir com o teu VM Linux como se estivesses na consola de um sistema.

Pode manipular muitos ficheiros de configuração, incluindo a forma como o kernel irá arrancar. 

Os administradores de sys Linux/Unix mais experientes apreciarão os modos **de utilizador único** e de emergência que são **acessíveis** através da Consola De Série Azure que faz a troca de discos e a eliminação vM para muitos cenários de recuperação redundantes.

O método de recuperação depende do problema que está a ser experimentado, por exemplo, uma palavra-passe perdida ou deslocada pode ser redefinida através das opções do portal Azure - > **Redefinir a Palavra-passe**. A função **De Reset Password** é conhecida como Extensão e comunica com o agente Convidado Linux.

Outras extensões como o Custom Script estão disponíveis no entanto estas opções exigem que o **waagent** Linux esteja em pé e em um estado saudável, o que nem sempre é o caso.

![estado do agente](./media/virtual-machines-serial-console/agent-status.png)


Garantir que tem acesso à Consola Série Azure e ao GRUB significa que uma alteração de senha ou uma configuração incorreta podem ser retificadas em questão de minutos em vez de horas. Pode até forçar o VM a arrancar de um núcleo alternativo se tiver vários núcleos no disco no cenário em que o seu núcleo primário se torna corrupto.

![núcleo multi](./media/virtual-machines-serial-console/more-kernel.png)

## <a name="suggested-order-of-recovery-methods"></a>Ordem sugerida de métodos de recuperação:

- Consola em série Azure

- Troca de Discos – pode ser automatizado usando qualquer um deles:

   - [Scripts de recuperação da concha de potência](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager)
   - [bash Scripts de Recuperação](https://github.com/sribs/azure-support-scripts)

- Método Legado

## <a name="disk-swap-video"></a>Vídeo de troca de disco:

Se não tem acesso à GRUB veja [este](https://youtu.be/m5t0GZ5oGAc) vídeo e veja, como pode facilmente automatizar o procedimento de troca de discos para recuperar o seu VM

## <a name="challenges"></a>Desafios:

Nem todos os VMs Linux Azure são configurados por padrão para acesso GRUB e nem todos estão configurados para serem interrompidos com os comandos sysrq. Alguns distros mais antigos, como o SLES 11, não estão configurados para exibir o pedido de Login na Consola Série Azure

Neste artigo, vamos rever várias distribuições linux e configurações de documentos sobre como disponibilizar a GRUB.




## <a name="how-to-configure-linux-vm-to-accept-sysrq-keys"></a>Como configurar o Linux VM para aceitar as teclas SysRq
A chave sysrq está ativada em alguns distros linux mais recentes por padrão, embora em outros possa ser configurado para aceitar valores apenas para certas funções SysRq.
Em distros mais antigos, pode ser completamente desativado.

A funcionalidade SysRq é útil para reiniciar um VM despenhado ou pendurado diretamente a partir da Consola Série Azure, também útil para obter acesso ao menu GRUB, em alternativa reiniciar um VM a partir de outra janela ou sessão de ssh do portal pode deixar cair a sua ligação de consola atual, expirando assim os Timeouts GRUB aos quais são utilizados para exibir o menu GRUB.
O VM deve ser configurado para aceitar um valor de 1 para o parâmetro kernel, que permite todas as funções de sysrq ou 128, o que permite reiniciar/desligar


[Ativar vídeo sysrq](https://youtu.be/0doqFRrHz_Mc)


Para configurar o VM para aceitar um reboot através de comandos SysRq no portal Azure, você precisará definir um valor de 1 para o kernel de parâmetro de kernel.sysrq

Para que esta configuração persista um reboot, adicione uma entrada no ficheiro **sysctl.conf**

`echo kernel.sysrq = 1 >> /etc/sysctl.conf`

Para configurar o parâmetro de kernel dinamicamente

`sysctl -w kernel.sysrq=1`

Se não tiver acesso à **raiz** ou o sudo estiver partido, não será possível configurar sysrq a partir de um pedido de concha.

Neste cenário pode ativar o portal Azure. Este método pode ser benéfico se o ficheiro **sudoers.d/waagent** tiver sido quebrado ou tiver sido eliminado.

Utilizando o portal Azure Operações -> executar comando -> runShellScript funcionalidade, requer que o processo waagent seja saudável, pode então injetar este comando para ativar o sysrq

`sysctl -w kernel.sysrq=1 ; echo kernel.sysrq = 1 >> /etc/sysctl.conf`

Como mostrado aqui: ![ativar sysrq2](./media/virtual-machines-serial-console/enabling-sysrq-2.png)

Uma vez concluído, pode então tentar aceder ao **sysrq** e deve ver se é possível reiniciar.

![permitir sysrq3](./media/virtual-machines-serial-console/enabling-sysrq-3.png)

Selecione **Reiniciar** e enviar comando **SysRq**

![permitir sysrq4](./media/virtual-machines-serial-console/enabling-sysrq-4.png)

O sistema deve registar uma mensagem de reset como esta

![permitir sysrq5](./media/virtual-machines-serial-console/enabling-sysrq-5.png)


## <a name="ubuntu-grub-configuration"></a>Configuração Ubuntu GRUB

Por predefinição, deverá conseguir aceder ao GRUB segurando a tecla **Esc** durante a bota VM, se o menu GRUB não for apresentado, pode forçar e manter o menu GRUB no ecrã na Consola Série Azure utilizando uma destas opções.

**Opção 1** - Força a GRUB a ser exibida no Ecrã 

Atualize o ficheiro /etc/default/grub.d/50-cloudimg-settings.cfg para manter o menu GRUB no ecrã para o TIMEOUT especificado.
Você não é obrigado a bater **Esc** como GRUB será exibido imediatamente

```
GRUB_TIMEOUT=0

change to

GRUB_TIMEOUT=5
```

**Opção 2** - Permite que **a Esc** seja pressionada antes de arrancar

Comportamento semelhante pode ser experimentado fazendo alterações no ficheiro /etc/padrão/grub e observar um intervalo de 3 segundos para atingir **Esc**


Comente estas duas linhas:

```
#GRUB_HIDDEN_TIMEOUT=0
#GRUB_HIDDEN_TIMEOUT_QUIET=true
```
e adicionar esta linha:

```
GRUB_TIMEOUT_STYLE=countdown
```


## <a name="ubuntu-1204"></a>Ubuntu 12\.04

Ubuntu 12.04 permitirá o acesso à consola em série, mas não oferece a capacidade de interagir. Um **login:** o pedido não é visto


Para 12.04 para obter um **login:** prompt:
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

2. Peça upstart para começar o getty     
    ```
    sudo start ttyS0
    ```
 
As definições necessárias para configurar a consola em série para versões Ubuntu podem ser encontradas [aqui](https://help.ubuntu.com/community/SerialConsoleHowto)

## <a name="ubuntu-recovery-mode"></a>Modo de Recuperação de Ubuntu

Estão disponíveis opções adicionais de recuperação e limpeza para Ubuntu via GRUB, no entanto estas definições só são acessíveis se configurar os parâmetros do kernel em conformidade.
A não configuração deste parâmetro de arranque de kernel obrigaria o menu Recovery a ser enviado para o Azure Diagnostics e não para a Consola Em Série Azure.
Pode obter acesso ao Menu de Recuperação ubuntu seguindo estes passos:

Interromper o processo boot e aceder ao menu GRUB

Selecione Opções Avançadas para Ubuntu e pressione entrar

![ubunturec1](./media/virtual-machines-serial-console/ubunturec1.png)

Selecione a linha de visualização *(modo de recuperação)* não pressione introduzir, mas prima "e"

![ubunturec2](./media/virtual-machines-serial-console/ubunturec2.png)

Localize a linha que carregará o núcleo e substitua o último parâmetro **nomodeset** com destino como **consola=ttyS0**

```
linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery nomodeset

change to

linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery console=ttyS0
```

![ubunturec3](./media/virtual-machines-serial-console/ubunturec3.png)

Pressione **Ctrl-x** para iniciar e carregar o núcleo.
Se tudo correr bem verá estas opções adicionais, o que pode ajudar a executar outras opções de recuperação

![ubunturec4](./media/virtual-machines-serial-console/ubunturec4.png)


## <a name="red-hat-grub-configuration"></a>Configuração red hat GRUB

## <a name="red-hat-74-grub-configuration"></a>Configuração\.do\+ Chapéu Vermelho 7 4 GRUB
A configuração padrão /etc/predefinido/larva nestas versões está adequadamente configurada

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

Ativar a tecla SysRq

```
sysctl -w kernel.sysrq=1;echo kernel.sysrq = 1 >> /etc/sysctl.conf;sysctl -a | grep -i sysrq
```

## <a name="red-hat-72-and-73-grub-configuration"></a>Vermelho Chapéu\.7\.2 e 7 3 CONFIGURAção GRUB
O ficheiro a modificar é /etc/predefinido/grub – um config predefinido parece com este exemplo:

```
GRUB_TIMEOUT=1
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```

Alterar as seguintes linhas em /etc/padrão/grub

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

/etc/padrão/grub deve agora ser semelhante a este exemplo:

```
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```
 
Configuração completa e atualizada da comida usando

`grub2-mkconfig -o /boot/grub2/grub.cfg`

Defina o parâmetro de kernel SysRq:

`sysctl -w kernel.sysrq = 1;echo kernel.sysrq = 1 >> /etc/sysctl.conf;sysctl -a | grep -i sysrq`

Pode configurar o GRUB e o SysRq utilizando uma única linha, quer na concha, quer através do Comando de Execução. Faça backup dos seus ficheiros antes de executar este comando:


`cp /etc/default/grub /etc/default/grub.bak; sed -i 's/GRUB_TIMEOUT=1/GRUB_TIMEOUT=5/g' /etc/default/grub; sed -i 's/GRUB_TERMINAL_OUTPUT="console"/GRUB_TERMINAL="serial console"/g' /etc/default/grub; echo "GRUB_SERIAL_COMMAND=\"serial --speed=115200 --unit=0 --word=8 --parity=no --stop=1\"" >> /etc/default/grub;grub2-mkconfig -o /boot/grub2/grub.cfg;sysctl -w kernel.sysrq=1;echo kernel.sysrq = 1 /etc/sysctl.conf;sysctl -a | grep -i sysrq`


## <a name="red-hat-6x-grub-configuration"></a>Configuração\.do Chapéu Vermelho 6 x GRUB
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


O terminal de última linha *--timeout=5 consola* de série aumentará ainda mais o tempo **de tempo GRUB** adicionando uma chamada de 5 segundos a visualizar **pressione qualquer tecla para continuar.**

![rh6-1](./media/virtual-machines-serial-console/rh6-1.png)

O menu GRUB deve aparecer no ecrã para o tempo de tempo configurado=15 sem a necessidade de pressionar Esc. Certifique-se de clicar na Consola no Browser para tornar ativo o menu e selecione o kernel necessário

![rh6-2](./media/virtual-machines-serial-console/rh6-2.png)

## <a name="suse"></a>SuSE

## <a name="sles-12-sp1"></a>SLES 12 sp1
Ou usa o bootloader yast de acordo com os [médicos](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-single-user-mode#grub-access-in-suse-sles) oficiais

Ou adicionar/alterar para /etc/predefinido/grub os seguintes parâmetros:

```
GRUB_TERMINAL=serial
GRUB_TIMEOUT=5
GRUB_SERIAL_COMMAND="serial --unit=0 --speed=9600 --parity=no"

```
Verifique se o ttys0 é utilizado no GRUB_CMDLINE_LINUX ou GRUB_CMDLINE_LINUX_DEFAULT

```
GRUB_CMDLINE_LINUX_DEFAULT="console=ttyS0,9600n"
```

Recrie a comida.cfg

`grub2-mkconfig -o /boot/grub2/grub.cfg`


## <a name="sles-11-sp4"></a>SLES 11 SP4 
A Consola série aparece e exibe mensagens de arranque, mas não apresenta um **login:** prompt

Abra uma sessão ssh no VM e atualize o ficheiro **/etc/inittab** descoando comentários sobre esta linha:

```
#S0:12345:respawn:/sbin/agetty -L 9600 ttyS0 vt102
```

Em seguida, executar o comando 

`telinit q`

Para ativar a GRUB, devem ser feitas as seguintes alterações para /boot/grub/menu.lst 

```
timeout 5
serial --unit=0 --speed=9600 --parity=no
terminal --timeout=5 serial console

root (hd0,0)
kernel /boot/vmlinuz-3.0.101-108.74-default root=/dev/disk/by-uuid/ab6b62bb--
1a8c-45eb-96b1-1fbc535b9265 disk=/dev/sda  USE_BY_UUID_DEVICE_NAMES=1 earlyprinttk=ttyS0 console=ttyS0 rootdelay=300  showopts vga=0x314
```

 Esta configuração permitirá que a mensagem **Prima qualquer tecla para continuar** a aparecer na consola durante 5 segundos 

Em seguida, irá exibir o menu GRUB por mais 5 segundos - premindo a seta para baixo, interromperá o contador e selecionará um núcleo que pretende arrancar da palavra-chave **para** um modo de utilizador único que requer a definição da palavra-passe raiz.

A adesão ao comando **init=/bin/bash** carregará o núcleo, mas garante que o programa de inité é substituído por uma concha de esmaga.

Terá acesso a uma concha sem ter de introduzir uma senha. Em seguida, pode proceder à atualização da palavra-passe para as contas do Linux ou fazer outras alterações de configuração.


## <a name="force-the-kernel-to-a-bash-prompt"></a>Forçar o núcleo a um golpe de estado
Ter acesso a GRUB permite interromper o processo de inicialização esta interação é útil para muitos procedimentos de recuperação.
Se não tiver uma palavra-passe de raiz e um único utilizador exigir que tenha uma palavra-passe de raiz, pode iniciar o kernel substituindo o programa de entrada por um aviso de batida – esta interrupção pode ser conseguida através da inscrição init=/bin/bash para a linha de arranque do núcleo

![bash1](./media/virtual-machines-serial-console/bash1.png)

Remonte o seu sistema de ficheiros RW /(raiz) utilizando o comando

`mount -o remount,rw /`

![bash2](./media/virtual-machines-serial-console/bash2.png)


Agora pode executar alterações de palavra-passe raiz ou muitas outras alterações de configuração do Linux

![bash3](./media/virtual-machines-serial-console/bash3.png)

Reiniciar o VM com 

`/sbin/reboot -f`




## <a name="single-user-mode"></a>Modo utilizador único

Em alternativa, poderá ter de aceder ao VM num único modo de utilizador ou de emergência. Selecione o núcleo que deseja arrancar ou interromper com as teclas de seta.
Introduza o modo desejado, afuniando a palavra-chave **simples** ou **1** à linha de arranque do núcleo. Nos sistemas RHEL, também pode anexar **rd.break**.

Para obter mais informações sobre como aceder ao modo de utilizador único, consulte [este doc](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-single-user-mode#general-single-user-mode-access) 


![single_user_ubuntu](./media/virtual-machines-serial-console/single-user-ubuntu.png)


## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre a [Consola Em Série Azure]( https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux)
