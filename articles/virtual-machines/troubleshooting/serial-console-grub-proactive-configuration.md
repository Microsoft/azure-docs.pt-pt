---
title: Configuração do GRUB proativo do console serial do Azure | Microsoft Docs
description: Configure o GRUB em várias distribuições, permitindo o acesso de modo de usuário único e de recuperação em máquinas virtuais do Azure.
services: virtual-machines-linux
documentationcenter: ''
author: vilibert
manager: spogge
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/10/2019
ms.author: vilibert
ms.openlocfilehash: a154ab4742f0d0d7acae0376bcf894bc2b62b4cd
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186932"
---
# <a name="proactively-ensuring-you-have-access-to-grub-and-sysrq-could-save-you-lots-of-down-time"></a>Garantir proativamente que você tenha acesso ao GRUB e ao SysRq pode economizar muito tempo de inatividade

Ter acesso ao console serial e ao GRUB melhorará os tempos de recuperação de sua máquina virtual IaaS Linux na maioria dos casos. O GRUB oferece opções de recuperação que, de outra forma, demorariam mais para recuperar sua VM. 


Os motivos para executar uma recuperação de VM são muitos e podem ser atribuídos a cenários como:

   - Sistemas de arquivos corrompidos/kernel/MBR (registro mestre de inicialização)
   - Atualizações de kernel com falha
   - Parâmetros incorretos do kernel GRUB
   - Configurações incorretas de fstab
   - Configurações de firewall
   - Senha perdida
   - Arquivos de configuração sshd desconfigurados
   - Configurações de rede

 Muitos outros cenários, conforme detalhado [aqui](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux#common-scenarios-for-accessing-the-serial-console)

Verifique se você pode acessar o GRUB e o Console serial em suas VMs implantadas no Azure. 

Se você for novo no console serial, consulte [este link](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux/).

> [!TIP]
> Certifique-se de fazer backups de arquivos antes de fazer alterações

Assista a este vídeo abaixo para ver como você pode recuperar rapidamente sua VM Linux quando tiver acesso ao GRUB

[Recuperar vídeo da VM Linux](https://youtu.be/KevOc3d_SG4)

Há vários métodos para ajudar na recuperação de VMs do Linux. Em um ambiente de nuvem, esse processo foi desafiador.
O progresso está sendo continuamente feito para ferramentas e recursos para garantir que os serviços sejam recuperados rapidamente.

Com o console serial do Azure, você pode interagir com sua VM do Linux como se estivesse no console de um sistema.

Você pode manipular muitos arquivos de configuração, incluindo como o kernel será inicializado. 

Os administradores de sys Linux/Unix mais experientes apreciarão o **único usuário** e os **modos de emergência** que podem ser acessados por meio do console serial do Azure, tornando a troca de disco e a exclusão da VM para muitos cenários de recuperação redundantes.

O método de recuperação depende do problema que está sendo experimentado, por exemplo, uma senha perdida ou mal posicionada pode ser redefinida por meio de portal do Azure opções-> **senha de redefinição**. O recurso **Redefinir senha** é conhecido como uma extensão e se comunica com o agente convidado do Linux.

Outras extensões, como o script personalizado, estão disponíveis no entanto essas opções exigem que o **waagent** do Linux esteja ativo e em um estado íntegro que nem sempre é o caso.

![status do agente](./media/virtual-machines-serial-console/agent-status.png)


Garantir que você tenha acesso ao console serial do Azure e GRUB significa que uma alteração de senha ou uma configuração incorreta pode ser corrigida em questão de minutos em vez de horas. Você poderia até mesmo forçar a VM a inicializar a partir de um kernel alternativo caso você tenha vários kernels em disco no cenário em que o kernel primário se torna corrompido.

![vários kernels](./media/virtual-machines-serial-console/more-kernel.png)

## <a name="suggested-order-of-recovery-methods"></a>Ordem sugerida de métodos de recuperação:

- Consola de Série do Azure

- Troca de disco – pode ser automatizada usando:

   - [Scripts de recuperação do Power Shell](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager)
   - [Scripts de recuperação bash](https://github.com/sribs/azure-support-scripts)

- Método herdado

## <a name="disk-swap-video"></a>Vídeo de permuta de disco:

Se você não tiver acesso ao GRUB Assista a [este](https://youtu.be/m5t0GZ5oGAc) vídeo e veja como é possível automatizar facilmente o procedimento de permuta de disco para recuperar sua VM

## <a name="challenges"></a>Problemas

Nem todas as VMs do Azure do Linux são configuradas por padrão para o acesso GRUB e nenhuma delas está configurada para ser interrompida com os comandos SysRq. Alguns distribuições mais antigos, como SLES 11, não estão configurados para exibir o prompt de logon no console serial do Azure

Neste artigo, examinaremos várias distribuições do Linux e configurações de documentos sobre como tornar o GRUB disponível.




## <a name="how-to-configure-linux-vm-to-accept-sysrq-keys"></a>Como configurar a VM do Linux para aceitar chaves SysRq
A chave SysRq é habilitada em algumas distribuições mais recentes do Linux por padrão, embora em outras possa ser configurada para aceitar valores apenas para determinadas funções SysRq.
No distribuições mais antigo, ele pode ser desabilitado completamente.

O recurso SysRq é útil para reinicializar uma VM com falha ou paralisada diretamente do console serial do Azure, também útil para obter acesso ao menu GRUB, como alternativa reiniciar uma VM de outra janela do portal ou a sessão SSH pode descartar sua conexão de console atual expirando os tempos limites do GRUB para os quais são usados para exibir o menu GRUB.
A VM deve ser configurada para aceitar um valor de 1 para o parâmetro kernel, que habilita todas as funções de SysRq ou 128, que permite reboot/estado desligado


[Habilitar vídeo do SysRq](https://youtu.be/0doqFRrHz_Mc)


Para configurar a VM para aceitar uma reinicialização por meio de comandos SysRq no portal do Azure, você precisará definir um valor de 1 para o parâmetro kernel kernel. SysRq

Para que essa configuração persista uma reinicialização, adicione uma entrada ao arquivo **sysctl. conf**

`echo kernel.sysrq = 1 >> /etc/sysctl.conf`

Para configurar o parâmetro de kernel dinamicamente

`sysctl -w kernel.sysrq=1`

Se você não tiver acesso à **raiz** ou o sudo for interrompido, não será possível configurar SysRq a partir de um prompt de Shell.

Você pode habilitar o SysRq neste cenário usando o portal do Azure. Esse método pode ser benéfico se o arquivo **sudoers. d/waagent** tiver sido quebrado ou excluído.

Usando o recurso de portal do Azure operações-> executar comando-> RunShellScript, requer que o processo waagent seja íntegro. em seguida, você pode injetar este comando para habilitar o SysRq

`sysctl -w kernel.sysrq=1 ; echo kernel.sysrq = 1 >> /etc/sysctl.conf`

Conforme mostrado aqui: ![habilitar sysrq2](./media/virtual-machines-serial-console/enabling-sysrq-2.png)

Depois de concluído, você pode tentar acessar o **SysRq** e deve ver que uma reinicialização é possível.

![Habilitar sysrq3](./media/virtual-machines-serial-console/enabling-sysrq-3.png)

Selecione **reinicializar** e **Enviar comando SysRq**

![Habilitar sysrq4](./media/virtual-machines-serial-console/enabling-sysrq-4.png)

O sistema deve registrar uma mensagem de redefinição como esta

![Habilitar sysrq5](./media/virtual-machines-serial-console/enabling-sysrq-5.png)


## <a name="ubuntu-grub-configuration"></a>Configuração de GRUB do Ubuntu

Por padrão, você deve ser capaz de acessar o GRUB mantendo a tecla **ESC** pressionada durante a inicialização da VM, se o menu grub não for apresentado, você poderá forçar e manter o menu grub na tela no console serial do Azure usando uma dessas opções.

**Opção 1** – força o grub a ser exibido na tela 

Atualize o arquivo/etc/default/grub.d/50-cloudimg-Settings.cfg para manter o menu GRUB na tela para o tempo limite especificado.
Você não precisa pressionar **ESC** , pois o grub será exibido imediatamente

```
GRUB_TIMEOUT=0

change to

GRUB_TIMEOUT=5
```

**Opção 2** – permite que **ESC** seja pressionado antes da inicialização

Um comportamento semelhante pode ocorrer ao fazer alterações no arquivo/etc/default/grub e observar um tempo limite de 3 segundos para pressionar **ESC**


Comente estas duas linhas:

```
#GRUB_HIDDEN_TIMEOUT=0
#GRUB_HIDDEN_TIMEOUT_QUIET=true
```
e adicione esta linha:

```
GRUB_TIMEOUT_STYLE=countdown
```


## <a name="ubuntu-1204"></a>Ubuntu 12\.04

O Ubuntu 12, 4 permitirá o acesso ao console serial, mas não oferecerá a capacidade de interagir. Um **logon:** prompt não é visto


Para 12, 4, para obter um **logon:** prompt:
1. Crie um arquivo chamado/etc/init/ttyS0.conf contendo o seguinte texto:

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

2. Peça para começar a iniciar o Getty     
    ```
    sudo start ttyS0
    ```
 
As configurações necessárias para configurar o console serial para versões do Ubuntu podem ser encontradas [aqui](https://help.ubuntu.com/community/SerialConsoleHowto)

## <a name="ubuntu-recovery-mode"></a>Modo de recuperação do Ubuntu

As opções adicionais de recuperação e limpeza estão disponíveis para o Ubuntu via GRUB, no entanto, essas configurações só estarão acessíveis se você configurar os parâmetros do kernel adequadamente.
A falha ao configurar esse parâmetro de inicialização do kernel forçaria o envio do menu de recuperação para o Diagnóstico do Azure e não para o console serial do Azure.
Você pode obter acesso ao menu de recuperação do Ubuntu seguindo estas etapas:

Interromper o processo de inicialização e acessar o menu GRUB

Selecione opções avançadas para Ubuntu e pressione Enter

![ubunturec1](./media/virtual-machines-serial-console/ubunturec1.png)

Selecione a exibição de linha *(modo de recuperação)* não pressione Enter, mas pressione "e"

![ubunturec2](./media/virtual-machines-serial-console/ubunturec2.png)

Localize a linha que carregará o kernel e substitua o último parâmetro **nomodeset** por destino como **console = ttyS0**

```
linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery nomodeset

change to

linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery console=ttyS0
```

![ubunturec3](./media/virtual-machines-serial-console/ubunturec3.png)

Pressione **Ctrl-x** para iniciar e carregar o kernel.
Se tudo correr bem, você verá essas opções adicionais, que podem ajudar a executar outras opções de recuperação

![ubunturec4](./media/virtual-machines-serial-console/ubunturec4.png)


## <a name="red-hat-grub-configuration"></a>Configuração do Red Hat GRUB

## <a name="red-hat-74-grub-configuration"></a>Red Hat 7\.4\+ configuração de GRUB
A configuração padrão do/etc/default/grub nessas versões está configurada adequadamente

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

Habilitar a chave SysRq

```
sysctl -w kernel.sysrq=1;echo kernel.sysrq = 1 >> /etc/sysctl.conf;sysctl -a | grep -i sysrq
```

## <a name="red-hat-72-and-73-grub-configuration"></a>Configuração de GRUB do Red Hat 7\.2 e 7\.3
O arquivo a ser modificado é/etc/default/grub – uma configuração padrão é parecida com este exemplo:

```
GRUB_TIMEOUT=1
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```

Altere as linhas a seguir em/etc/default/grub

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

/etc/default/grub agora deve ser semelhante a este exemplo:

```
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```
 
Concluir e atualizar a configuração do grub usando

`grub2-mkconfig -o /boot/grub2/grub.cfg`

Defina o parâmetro de kernel SysRq:

`sysctl -w kernel.sysrq = 1;echo kernel.sysrq = 1 >> /etc/sysctl.conf;sysctl -a | grep -i sysrq`

Como alternativa, você pode configurar o GRUB e o SysRq usando uma única linha no Shell ou por meio do comando executar. Faça backup de seus arquivos antes de executar este comando:


`cp /etc/default/grub /etc/default/grub.bak; sed -i 's/GRUB_TIMEOUT=1/GRUB_TIMEOUT=5/g' /etc/default/grub; sed -i 's/GRUB_TERMINAL_OUTPUT="console"/GRUB_TERMINAL="serial console"/g' /etc/default/grub; echo "GRUB_SERIAL_COMMAND=\"serial --speed=115200 --unit=0 --word=8 --parity=no --stop=1\"" >> /etc/default/grub;grub2-mkconfig -o /boot/grub2/grub.cfg;sysctl -w kernel.sysrq=1;echo kernel.sysrq = 1 /etc/sysctl.conf;sysctl -a | grep -i sysrq`


## <a name="red-hat-6x-grub-configuration"></a>Configuração de GRUB do Red Hat 6\.x
O arquivo a ser modificado é/boot/grub/grub.conf. O valor `timeout` determinará por quanto tempo o GRUB é mostrado para.

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


A última linha *terminal--Timeout = 5 console serial* aumentará ainda mais o tempo limite do **grub** adicionando um prompt de 5 segundos exibindo **pressione qualquer tecla para continuar.**

![RH6-1](./media/virtual-machines-serial-console/rh6-1.png)

O menu GRUB deve aparecer na tela para o tempo limite configurado = 15 sem a necessidade de pressionar ESC. Certifique-se de clicar no console no navegador para ativar o menu e selecionar o kernel necessário

![RH6-2](./media/virtual-machines-serial-console/rh6-2.png)

## <a name="suse"></a>SuSE

## <a name="sles-12-sp1"></a>SLES 12 SP1
Use o carregador de carregado de YaST de acordo com os [documentos](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-single-user-mode#grub-access-in-suse-sles) oficiais

Ou adicionar/alterar para/etc/default/grub os seguintes parâmetros:

```
GRUB_TERMINAL=serial
GRUB_TIMEOUT=5
GRUB_SERIAL_COMMAND="serial --unit=0 --speed=9600 --parity=no"

```
Verifique se ttyS0 é usado no GRUB_CMDLINE_LINUX ou GRUB_CMDLINE_LINUX_DEFAULT

```
GRUB_CMDLINE_LINUX_DEFAULT="console=ttyS0,9600n"
```

Recriar o grub. cfg

`grub2-mkconfig -o /boot/grub2/grub.cfg`


## <a name="sles-11-sp4"></a>SLES 11 SP4 
O console serial é exibido e exibe mensagens de inicialização, mas não exibe um **logon:** prompt

Abra uma sessão SSH na VM e atualize o arquivo **/etc/inittab** ao remover o comentário desta linha:

```
#S0:12345:respawn:/sbin/agetty -L 9600 ttyS0 vt102
```

Em seguida, execute o comando 

`telinit q`

Para habilitar o GRUB, as seguintes alterações devem ser feitas no/boot/grub/menu.lst 

```
timeout 5
serial --unit=0 --speed=9600 --parity=no
terminal --timeout=5 serial console

root (hd0,0)
kernel /boot/vmlinuz-3.0.101-108.74-default root=/dev/disk/by-uuid/ab6b62bb--
1a8c-45eb-96b1-1fbc535b9265 disk=/dev/sda  USE_BY_UUID_DEVICE_NAMES=1 earlyprinttk=ttyS0 console=ttyS0 rootdelay=300  showopts vga=0x314
```

 Essa configuração permitirá que a mensagem **pressione qualquer tecla para continuar** a aparecer no console por 5 segundos 

Em seguida, ele exibirá o menu GRUB por um adicional 5 segundos-pressionando a seta para baixo, você interromperá o contador e selecionará um kernel que você deseja inicializar e acrescentará a palavra-chave **single** para o modo de usuário único que exige a definição da senha raiz.

Acrescentar o comando **init =/bin/bash** carregará o kernel, mas garantirá que o programa init seja substituído por um shell bash.

Você obterá acesso a um shell sem precisar inserir uma senha. Em seguida, você pode continuar a atualizar a senha para contas do Linux ou fazer outras alterações de configuração.


## <a name="force-the-kernel-to-a-bash-prompt"></a>Forçar o kernel a um prompt do bash
Ter acesso ao GRUB permite interromper o processo de inicialização essa interação é útil para muitos procedimentos de recuperação.
Se você não tiver a senha raiz e o usuário único exigir que você tenha uma senha raiz, você poderá inicializar o kernel, substituindo o programa init por um prompt bash – essa interrupção pode ser obtida acrescentando init =/bin/bash à linha de inicialização do kernel

![bash1](./media/virtual-machines-serial-console/bash1.png)

Remonte o sistema de arquivos/(raiz) RW usando o comando

`mount -o remount,rw /`

![bash2](./media/virtual-machines-serial-console/bash2.png)


Agora você pode executar a alteração de senha raiz ou muitas outras alterações de configuração do Linux

![bash3](./media/virtual-machines-serial-console/bash3.png)

Reinicie a VM com 

`/sbin/reboot -f`




## <a name="single-user-mode"></a>Modo de usuário único

Como alternativa, talvez seja necessário acessar a VM no modo de usuário único ou de emergência. Selecione o kernel que você deseja inicializar ou interromper usando as teclas de direção.
Insira o modo desejado acrescentando a palavra-chave **single** ou **1** à linha de inicialização do kernel. Em sistemas RHEL, você também pode acrescentar **Rd. Break**.

Para obter mais informações sobre como acessar o modo de usuário único, consulte [este documento](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-single-user-mode#general-single-user-mode-access) 


![single_user_ubuntu](./media/virtual-machines-serial-console/single-user-ubuntu.png)


## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre o [console serial do Azure]( https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux)