---
title: Criar e carregar um VHD do Red Hat Enterprise Linux para utilização no Azure | Documentos da Microsoft
description: Saiba como criar e carregar um Azure disco rígido virtual (VHD) que contém um sistema operativo Red Hat Linux.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 6c6b8f72-32d3-47fa-be94-6cb54537c69f
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/27/2018
ms.author: szark
ms.openlocfilehash: 01acdf23c3113c3c4d185263b5cab75f3efd34a2
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58001660"
---
# <a name="prepare-a-red-hat-based-virtual-machine-for-azure"></a>Preparar uma máquina virtual baseada em Red Hat para o Azure
Neste artigo, aprenderá a preparar uma máquina virtual do Red Hat Enterprise Linux (RHEL) para utilização no Azure. As versões do RHEL que são abordadas neste artigo são 6.7 + e 7.1 +. Os hipervisores de preparação que são abordados neste artigo são máquinas de virtuais de Hyper-V, com base em kernel (KVM) e VMware. Para obter mais informações sobre os requisitos de elegibilidade para participar no programa de acesso à nuvem da Red Hat, consulte [Web site do Red Hat Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) e [RHEL em execução no Azure](https://access.redhat.com/ecosystem/ccsp/microsoft-azure).

## <a name="prepare-a-red-hat-based-virtual-machine-from-hyper-v-manager"></a>Preparar uma máquina de virtual baseada em Red Hat a partir do Gestor de Hyper-V

### <a name="prerequisites"></a>Pré-requisitos
Esta secção assume que já tiver obtido um arquivo ISO a partir do site da Red Hat e instalado na imagem RHEL para um disco rígido virtual (VHD). Para obter mais detalhes sobre como utilizar o Gestor de Hyper-V para instalar uma imagem de sistema operativo, consulte [instalar a função Hyper-V e configurar uma Máquina Virtual](https://technet.microsoft.com/library/hh846766.aspx).

**Observações de instalação do RHEL**

* Azure não suporta o formato VHDX. O Azure suporta apenas corrigido VHD. Pode utilizar o Gestor de Hyper-V para converter o disco para o formato VHD, ou pode utilizar o cmdlet convert-vhd. Se usar o VirtualBox, selecione **fixos de tamanho** em oposição a predefinição atribuído dinamicamente a opção ao criar o disco.
* O Azure suporta apenas máquinas de virtuais de geração 1. Pode converter uma máquina virtual de geração 1 de VHDX para o formato de ficheiro VHD e de expansão dinâmica para um disco de tamanho fixo. Não é possível alterar a geração de uma máquina virtual. Para obter mais informações, consulte [deve criar máquinas virtuais de geração 1 ou 2 no Hyper-V?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).
* O tamanho máximo permitido para o VHD é 1,023 GB.
* Gestor de volumes lógicos (LVM) é suportado e pode ser utilizado no disco do SO ou discos de dados em máquinas virtuais do Azure. No entanto, em geral é recomendado para utilizar partições padrão no disco do SO em vez de LVM. Esta prática evitará LVM nome entra em conflito com as máquinas virtuais Clonadas, especialmente se alguma vez precisar de anexar um disco de sistema operativo para outra máquina de virtual idêntica para resolução de problemas. Consulte também [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) e [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) documentação.
* É necessário suporte de kernel de montagem de sistemas de ficheiros de formato de disco Universal (UDF). No primeiro arranque no Azure, o suporte de dados formatado para UDF, que está ligado para o convidado transmite a configuração de aprovisionamento para a máquina virtual Linux. O agente Linux do Azure tem de ser capaz de montar o sistema de ficheiros UDF para ler a respetiva configuração e aprovisionar a máquina virtual.
* Não configure uma partição de troca no disco do sistema operativo. O agente Linux pode ser configurado para criar um ficheiro de troca no disco de recursos temporário.  Podem encontrar mais informações sobre este assunto nos passos seguintes.
* Todos os VHDs no Azure tem de ter um tamanho virtual alinhado para 1MB. Ao converter a partir de um disco não processado para o VHD tem de se certificar de que o tamanho de disco bruto é um múltiplo de 1MB antes da conversão. Podem encontrar mais detalhes nos passos abaixo. Consulte também [observações de instalação de Linux](create-upload-generic.md#general-linux-installation-notes) para obter mais informações.

### <a name="prepare-a-rhel-6-virtual-machine-from-hyper-v-manager"></a>Preparar uma máquina virtual do RHEL 6 a partir do Gestor de Hyper-V

1. No Gestor de Hyper-V, selecione a máquina virtual.

1. Clique em **Connect** para abrir uma janela de consola para a máquina virtual.

1. No RHEL 6, NetworkManager pode interferir com o agente Linux do Azure. Desinstale este pacote, executando o seguinte comando:
   
        # sudo rpm -e --nodeps NetworkManager

1. Criar ou editar o `/etc/sysconfig/network` de ficheiros e adicione o seguinte texto:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Criar ou editar o `/etc/sysconfig/network-scripts/ifcfg-eth0` de ficheiros e adicione o seguinte texto:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

1. Mover (ou remover) as regras de udev para evitar a geração de regras estáticas para a interface de Ethernet. Estas regras causam problemas quando clona uma máquina virtual no Microsoft Azure ou o Hyper-v:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

1. Certifique-se de que o serviço de rede será iniciado no momento da inicialização, executando o seguinte comando:

        # sudo chkconfig network on

1. Registe-se a sua subscrição do Red Hat para permitir a instalação de pacotes do repositório RHEL, executando o seguinte comando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. O pacote de WALinuxAgent `WALinuxAgent-<version>`, tiver sido enviado para o repositório de recursos extras do Red Hat. Ative o repositório de extras ao executar o seguinte comando:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

1. Modificar a linha de arranque de kernel em sua configuração de grub para incluir parâmetros de kernel adicionais para o Azure. Para fazer essa modificação, abra `/boot/grub/menu.lst` num editor de texto e certifique-se de que o kernel padrão inclui os seguintes parâmetros:
    
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    
    Isto também irá garantir que todas as mensagens de consola sejam enviadas para a primeira porta serial, que pode ajudá-lo Azure suporte com a depuração de problemas.
    
    Além disso, recomendamos que remova os seguintes parâmetros:
    
        rhgb quiet crashkernel=auto
    
    Arranque gráfica e quieto não são úteis num ambiente de nuvem onde queremos todos os registos a serem enviados para a porta serial.  Pode deixar o `crashkernel` opção configurada se assim o desejar. Tenha em atenção que este parâmetro reduz a quantidade de memória disponível na máquina virtual por 128 MB ou mais. Esta configuração pode ser problemática em tamanhos de máquinas virtuais mais pequenos.


1. Certifique-se de que o servidor de secure shell (SSH) está instalado e configurado para iniciar no momento da inicialização, que é normalmente o padrão. Modificar /etc/ssh/sshd_config para incluir a seguinte linha:

        ClientAliveInterval 180

1. Instale o agente Linux do Azure ao executar o seguinte comando:

        # sudo yum install WALinuxAgent

        # sudo chkconfig waagent on

    Instalar o pacote de WALinuxAgent remove o NetworkManager e pacotes de NetworkManager gnome se eles não foram removidos já no passo 3.

1. Não crie espaço de comutação em disco do sistema operativo.

    O agente Linux do Azure podem configurar automaticamente o espaço de comutação utilizando o disco de recurso local que está ligado à máquina virtual depois da máquina virtual é aprovisionada no Azure. Tenha em atenção que o disco de recurso local é um disco temporário e que ele pode de ser esvaziado quando a máquina virtual é desaprovisionada. Depois de instalar o agente Linux do Azure no passo anterior, modifique os parâmetros seguintes na /etc/waagent.Conf. adequadamente:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Anular o registo da subscrição (se necessário) ao executar o seguinte comando:

        # sudo subscription-manager unregister

1. Execute os seguintes comandos para desaprovisionar a máquina virtual e prepará-lo para o aprovisionamento no Azure:

        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Clique em **ação** > **Encerrar** no Gestor de Hyper-V. O VHD do Linux está agora pronto para ser carregado para o Azure.


### <a name="prepare-a-rhel-7-virtual-machine-from-hyper-v-manager"></a>Preparar uma máquina virtual do RHEL 7 a partir do Gestor de Hyper-V

1. No Gestor de Hyper-V, selecione a máquina virtual.

1. Clique em **Connect** para abrir uma janela de consola para a máquina virtual.

1. Criar ou editar o `/etc/sysconfig/network` de ficheiros e adicione o seguinte texto:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Criar ou editar o `/etc/sysconfig/network-scripts/ifcfg-eth0` de ficheiros e adicione o seguinte texto:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

1. Certifique-se de que o serviço de rede será iniciado no momento da inicialização, executando o seguinte comando:

        # sudo systemctl enable network

1. Registe-se a sua subscrição do Red Hat para permitir a instalação de pacotes do repositório RHEL, executando o seguinte comando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. Modificar a linha de arranque de kernel em sua configuração de grub para incluir parâmetros de kernel adicionais para o Azure. Para fazer essa modificação, abra `/etc/default/grub` num editor de texto e editar o `GRUB_CMDLINE_LINUX` parâmetro. Por exemplo:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Isto também irá garantir que todas as mensagens de consola sejam enviadas para a primeira porta serial, que pode ajudá-lo Azure suporte com a depuração de problemas. Esta configuração também se desligar as convenções de nomenclatura do RHEL 7 novo para NICs. Além disso, recomendamos que remova os seguintes parâmetros:
   
        rhgb quiet crashkernel=auto
   
    Arranque gráfica e quieto não são úteis num ambiente de nuvem onde queremos todos os registos a serem enviados para a porta serial. Pode deixar o `crashkernel` opção configurada se assim o desejar. Tenha em atenção que este parâmetro reduz a quantidade de memória disponível na máquina virtual por 128 MB ou mais, que pode ser problemático em tamanhos de máquinas virtuais mais pequenos.

1. Depois de terminar edição `/etc/default/grub`, execute o seguinte comando para recriar a configuração do grub:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

1. Certifique-se de que o servidor SSH está instalado e configurado para iniciar no momento da inicialização, que é normalmente o padrão. Modificar `/etc/ssh/sshd_config` para incluir a seguinte linha:

        ClientAliveInterval 180

1. O pacote de WALinuxAgent `WALinuxAgent-<version>`, tiver sido enviado para o repositório de recursos extras do Red Hat. Ative o repositório de extras ao executar o seguinte comando:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

1. Instale o agente Linux do Azure ao executar o seguinte comando:

        # sudo yum install WALinuxAgent

        # sudo systemctl enable waagent.service

1. Não crie espaço de comutação em disco do sistema operativo.

    O agente Linux do Azure podem configurar automaticamente o espaço de comutação utilizando o disco de recurso local que está ligado à máquina virtual depois da máquina virtual é aprovisionada no Azure. Tenha em atenção que o disco de recurso local é um disco temporário, e ele poderá ser esvaziado quando a máquina virtual é desaprovisionada. Depois de instalar o agente Linux do Azure no passo anterior, modificar os parâmetros seguintes na `/etc/waagent.conf` adequadamente:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Se desejar anular o registo da subscrição, execute o seguinte comando:

        # sudo subscription-manager unregister

1. Execute os seguintes comandos para desaprovisionar a máquina virtual e prepará-lo para o aprovisionamento no Azure:

        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Clique em **ação** > **Encerrar** no Gestor de Hyper-V. O VHD do Linux está agora pronto para ser carregado para o Azure.


## <a name="prepare-a-red-hat-based-virtual-machine-from-kvm"></a>Preparar uma máquina de virtual baseada em Red Hat a partir de KVM
### <a name="prepare-a-rhel-6-virtual-machine-from-kvm"></a>Preparar uma máquina virtual do RHEL 6 do KVM

1. Transferir a imagem KVM do RHEL 6 a partir do site da Red Hat.

1. Defina uma palavra-passe de raiz.

    Gerar uma palavra-passe encriptada e copie a saída do comando:

        # openssl passwd -1 changeme

    Defina uma palavra-passe de raiz com guestfish:
        
        # guestfish --rw -a <image-name>
        > <fs> run
        > <fs> list-filesystems
        > <fs> mount /dev/sda1 /
        > <fs> vi /etc/shadow
        > <fs> exit

   Altere o segundo campo do utilizador de raiz "!!" a palavra-passe encriptada.

1. Crie uma máquina virtual no KVM a partir da imagem de qcow2. Defina o tipo de disco para **qcow2**e defina o modelo de dispositivo de interface de rede virtual para **virtio**. Em seguida, iniciar a máquina virtual e inicie sessão como raiz.

1. Criar ou editar o `/etc/sysconfig/network` de ficheiros e adicione o seguinte texto:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Criar ou editar o `/etc/sysconfig/network-scripts/ifcfg-eth0` de ficheiros e adicione o seguinte texto:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

1. Mover (ou remover) as regras de udev para evitar a geração de regras estáticas para a interface de Ethernet. Estas regras causam problemas quando clona uma máquina virtual no Azure ou do Hyper-v:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

1. Certifique-se de que o serviço de rede será iniciado no momento da inicialização, executando o seguinte comando:

        # chkconfig network on

1. Registe-se a sua subscrição do Red Hat para permitir a instalação de pacotes do repositório RHEL, executando o seguinte comando:

        # subscription-manager register --auto-attach --username=XXX --password=XXX

1. Modificar a linha de arranque de kernel em sua configuração de grub para incluir parâmetros de kernel adicionais para o Azure. Para fazer esta configuração, abra `/boot/grub/menu.lst` num editor de texto e certifique-se de que o kernel padrão inclui os seguintes parâmetros:
    
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    
    Isto também irá garantir que todas as mensagens de consola sejam enviadas para a primeira porta serial, que pode ajudá-lo Azure suporte com a depuração de problemas.
    
    Além disso, recomendamos que remova os seguintes parâmetros:
    
        rhgb quiet crashkernel=auto
    
    Arranque gráfica e quieto não são úteis num ambiente de nuvem onde queremos todos os registos a serem enviados para a porta serial. Pode deixar o `crashkernel` opção configurada se assim o desejar. Tenha em atenção que este parâmetro reduz a quantidade de memória disponível na máquina virtual por 128 MB ou mais, que pode ser problemático em tamanhos de máquinas virtuais mais pequenos.


1. Adicione módulos do Hyper-V para initramfs:  

    Editar `/etc/dracut.conf`e adicione o seguinte conteúdo:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Recrie initramfs:

        # dracut -f -v

1. Desinstale o cloud-init:

        # yum remove cloud-init

1. Certifique-se de que o servidor SSH está instalado e configurado para iniciar no momento da inicialização:

        # chkconfig sshd on

    Modificar /etc/ssh/sshd_config para incluir as seguintes linhas:

        PasswordAuthentication yes
        ClientAliveInterval 180

1. O pacote de WALinuxAgent `WALinuxAgent-<version>`, tiver sido enviado para o repositório de recursos extras do Red Hat. Ative o repositório de extras ao executar o seguinte comando:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

1. Instale o agente Linux do Azure ao executar o seguinte comando:

        # yum install WALinuxAgent

        # chkconfig waagent on

1. O agente Linux do Azure podem configurar automaticamente o espaço de comutação utilizando o disco de recurso local que está ligado à máquina virtual depois da máquina virtual é aprovisionada no Azure. Tenha em atenção que o disco de recurso local é um disco temporário, e ele poderá ser esvaziado quando a máquina virtual é desaprovisionada. Depois de instalar o agente Linux do Azure no passo anterior, modificar os parâmetros seguintes na **/etc/waagent.Conf.** adequadamente:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Anular o registo da subscrição (se necessário) ao executar o seguinte comando:

        # subscription-manager unregister

1. Execute os seguintes comandos para desaprovisionar a máquina virtual e prepará-lo para o aprovisionamento no Azure:

        # waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Encerre a máquina virtual numa KVM.

1. Converta a imagem de qcow2 para o formato VHD.

> [!NOTE]
> Há um bug conhecido em versões de qemu img > = 2.2.1 do buildship que resulta num VHD formatado incorretamente. Foi corrigido o problema em QEMU 2.6. Recomenda-se para utilizar qemu-img 2.2.0 ou inferior, ou atualizar para o 2.6 ou superior. Referência: https://bugs.launchpad.net/qemu/+bug/1490611.
>


    First convert the image to raw format:

        # qemu-img convert -f qcow2 -O raw rhel-6.9.qcow2 rhel-6.9.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.9.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.9.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.9.raw rhel-6.9.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-6.9.raw rhel-6.9.vhd

        
### <a name="prepare-a-rhel-7-virtual-machine-from-kvm"></a>Preparar uma máquina virtual do RHEL 7 a partir de KVM

1. Transferir a imagem KVM do RHEL 7 a partir do site da Red Hat. Este procedimento utiliza RHEL 7, como o exemplo.

1. Defina uma palavra-passe de raiz.

    Gerar uma palavra-passe encriptada e copie a saída do comando:

        # openssl passwd -1 changeme

    Defina uma palavra-passe de raiz com guestfish:

        # guestfish --rw -a <image-name>
        > <fs> run
        > <fs> list-filesystems
        > <fs> mount /dev/sda1 /
        > <fs> vi /etc/shadow
        > <fs> exit

   Altere o segundo campo de utilizador de raiz "!!" a palavra-passe encriptada.

1. Crie uma máquina virtual no KVM a partir da imagem de qcow2. Defina o tipo de disco para **qcow2**e defina o modelo de dispositivo de interface de rede virtual para **virtio**. Em seguida, iniciar a máquina virtual e inicie sessão como raiz.

1. Criar ou editar o `/etc/sysconfig/network` de ficheiros e adicione o seguinte texto:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Criar ou editar o `/etc/sysconfig/network-scripts/ifcfg-eth0` de ficheiros e adicione o seguinte texto:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

1. Certifique-se de que o serviço de rede será iniciado no momento da inicialização, executando o seguinte comando:

        # sudo systemctl enable network

1. Registe-se a sua subscrição do Red Hat para ativar a instalação de pacotes do repositório RHEL, executando o seguinte comando:

        # subscription-manager register --auto-attach --username=XXX --password=XXX

1. Modificar a linha de arranque de kernel em sua configuração de grub para incluir parâmetros de kernel adicionais para o Azure. Para fazer esta configuração, abra `/etc/default/grub` num editor de texto e editar o `GRUB_CMDLINE_LINUX` parâmetro. Por exemplo:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Este comando também garante que todas as mensagens de consola sejam enviadas para a primeira porta serial, que pode ajudá-lo Azure suporte com a depuração de problemas. O comando também se desligar as convenções de nomenclatura do RHEL 7 novo para NICs. Além disso, recomendamos que remova os seguintes parâmetros:
   
        rhgb quiet crashkernel=auto
   
    Arranque gráfica e quieto não são úteis num ambiente de nuvem onde queremos todos os registos a serem enviados para a porta serial. Pode deixar o `crashkernel` opção configurada se assim o desejar. Tenha em atenção que este parâmetro reduz a quantidade de memória disponível na máquina virtual por 128 MB ou mais, que pode ser problemático em tamanhos de máquinas virtuais mais pequenos.

1. Depois de terminar edição `/etc/default/grub`, execute o seguinte comando para recriar a configuração do grub:

        # grub2-mkconfig -o /boot/grub2/grub.cfg

1. Adicione módulos do Hyper-V initramfs.

    Editar `/etc/dracut.conf` e adicionar conteúdo:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Recrie initramfs:

        # dracut -f -v

1. Desinstale o cloud-init:

        # yum remove cloud-init

1. Certifique-se de que o servidor SSH está instalado e configurado para iniciar no momento da inicialização:

        # systemctl enable sshd

    Modificar /etc/ssh/sshd_config para incluir as seguintes linhas:

        PasswordAuthentication yes
        ClientAliveInterval 180

1. O pacote de WALinuxAgent `WALinuxAgent-<version>`, tiver sido enviado para o repositório de recursos extras do Red Hat. Ative o repositório de extras ao executar o seguinte comando:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

1. Instale o agente Linux do Azure ao executar o seguinte comando:

        # yum install WALinuxAgent

    Ative o serviço de waagent:

        # systemctl enable waagent.service

1. Não crie espaço de comutação em disco do sistema operativo.

    O agente Linux do Azure podem configurar automaticamente o espaço de comutação utilizando o disco de recurso local que está ligado à máquina virtual depois da máquina virtual é aprovisionada no Azure. Tenha em atenção que o disco de recurso local é um disco temporário, e ele poderá ser esvaziado quando a máquina virtual é desaprovisionada. Depois de instalar o agente Linux do Azure no passo anterior, modificar os parâmetros seguintes na `/etc/waagent.conf` adequadamente:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Anular o registo da subscrição (se necessário) ao executar o seguinte comando:

        # subscription-manager unregister

1. Execute os seguintes comandos para desaprovisionar a máquina virtual e prepará-lo para o aprovisionamento no Azure:

        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Encerre a máquina virtual numa KVM.

1. Converta a imagem de qcow2 para o formato VHD.

> [!NOTE]
> Há um bug conhecido em versões de qemu img > = 2.2.1 do buildship que resulta num VHD formatado incorretamente. Foi corrigido o problema em QEMU 2.6. Recomenda-se para utilizar qemu-img 2.2.0 ou inferior, ou atualizar para o 2.6 ou superior. Referência: https://bugs.launchpad.net/qemu/+bug/1490611.
>


    First convert the image to raw format:

        # qemu-img convert -f qcow2 -O raw rhel-7.4.qcow2 rhel-7.4.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-7.4.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd


## <a name="prepare-a-red-hat-based-virtual-machine-from-vmware"></a>Preparar uma máquina de virtual baseada em Red Hat do VMware
### <a name="prerequisites"></a>Pré-requisitos
Esta secção assume que já instalou uma máquina virtual do RHEL no VMware. Para obter detalhes sobre como instalar um sistema operativo no VMware, consulte [guia de instalação do sistema operativo de convidado do VMware](https://partnerweb.vmware.com/GOSIG/home.html).

* Quando instala o sistema operativo Linux, recomendamos que utilize partições padrão em vez de LVM, que é muitas vezes o padrão para muitas instalações. Isso evitará LVM nome entra em conflito com a máquina virtual clonada, especialmente se o disco de sistema operativo já tem de ser ligado a outra máquina virtual para resolução de problemas. LVM ou RAID pode ser utilizada em discos de dados, se preferirem.
* Não configure uma partição de troca no disco do sistema operativo. Pode configurar o agente do Linux para criar um ficheiro de troca no disco de recursos temporário. Pode encontrar mais informações sobre este nos passos que se seguem.
* Quando cria o disco rígido virtual, selecione **disco virtual de Store como um único arquivo**.

### <a name="prepare-a-rhel-6-virtual-machine-from-vmware"></a>Preparar uma máquina virtual do RHEL 6 do VMware
1. No RHEL 6, NetworkManager pode interferir com o agente Linux do Azure. Desinstale este pacote, executando o seguinte comando:
   
        # sudo rpm -e --nodeps NetworkManager

1. Crie um ficheiro denominado **rede** no /etc/sysconfig/diretório que contém o seguinte texto:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Criar ou editar o `/etc/sysconfig/network-scripts/ifcfg-eth0` de ficheiros e adicione o seguinte texto:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

1. Mover (ou remover) as regras de udev para evitar a geração de regras estáticas para a interface de Ethernet. Estas regras causam problemas quando clona uma máquina virtual no Azure ou do Hyper-v:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

1. Certifique-se de que o serviço de rede será iniciado no momento da inicialização, executando o seguinte comando:

        # sudo chkconfig network on

1. Registe-se a sua subscrição do Red Hat para permitir a instalação de pacotes do repositório RHEL, executando o seguinte comando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. O pacote de WALinuxAgent `WALinuxAgent-<version>`, tiver sido enviado para o repositório de recursos extras do Red Hat. Ative o repositório de extras ao executar o seguinte comando:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

1. Modificar a linha de arranque de kernel em sua configuração de grub para incluir parâmetros de kernel adicionais para o Azure. Para tal, abra `/etc/default/grub` num editor de texto e editar o `GRUB_CMDLINE_LINUX` parâmetro. Por exemplo:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0"
   
   Isto também irá garantir que todas as mensagens de consola sejam enviadas para a primeira porta serial, que pode ajudá-lo Azure suporte com a depuração de problemas. Além disso, recomendamos que remova os seguintes parâmetros:
   
        rhgb quiet crashkernel=auto
   
    Arranque gráfica e quieto não são úteis num ambiente de nuvem onde queremos todos os registos a serem enviados para a porta serial. Pode deixar o `crashkernel` opção configurada se assim o desejar. Tenha em atenção que este parâmetro reduz a quantidade de memória disponível na máquina virtual por 128 MB ou mais, que pode ser problemático em tamanhos de máquinas virtuais mais pequenos.

1. Adicione módulos do Hyper-V para initramfs:

    Editar `/etc/dracut.conf`e adicione o seguinte conteúdo:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Recrie initramfs:

        # dracut -f -v

1. Certifique-se de que o servidor SSH está instalado e configurado para iniciar no momento da inicialização, que é normalmente o padrão. Modificar `/etc/ssh/sshd_config` para incluir a seguinte linha:

    ClientAliveInterval 180

1. Instale o agente Linux do Azure ao executar o seguinte comando:

        # sudo yum install WALinuxAgent

        # sudo chkconfig waagent on

1. Não crie espaço de comutação em disco do sistema operativo.

    O agente Linux do Azure podem configurar automaticamente o espaço de comutação utilizando o disco de recurso local que está ligado à máquina virtual depois da máquina virtual é aprovisionada no Azure. Tenha em atenção que o disco de recurso local é um disco temporário, e ele poderá ser esvaziado quando a máquina virtual é desaprovisionada. Depois de instalar o agente Linux do Azure no passo anterior, modificar os parâmetros seguintes na `/etc/waagent.conf` adequadamente:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Anular o registo da subscrição (se necessário) ao executar o seguinte comando:

        # sudo subscription-manager unregister

1. Execute os seguintes comandos para desaprovisionar a máquina virtual e prepará-lo para o aprovisionamento no Azure:

        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Encerre a máquina virtual e converter o arquivo VMDK num ficheiro. vhd.

> [!NOTE]
> Há um bug conhecido em versões de qemu img > = 2.2.1 do buildship que resulta num VHD formatado incorretamente. Foi corrigido o problema em QEMU 2.6. Recomenda-se para utilizar qemu-img 2.2.0 ou inferior, ou atualizar para o 2.6 ou superior. Referência: https://bugs.launchpad.net/qemu/+bug/1490611.
>


    First convert the image to raw format:

        # qemu-img convert -f vmdk -O raw rhel-6.9.vmdk rhel-6.9.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.9.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.9.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.9.raw rhel-6.9.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-6.9.raw rhel-6.9.vhd


### <a name="prepare-a-rhel-7-virtual-machine-from-vmware"></a>Preparar uma máquina virtual do RHEL 7 do VMware
1. Criar ou editar o `/etc/sysconfig/network` de ficheiros e adicione o seguinte texto:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Criar ou editar o `/etc/sysconfig/network-scripts/ifcfg-eth0` de ficheiros e adicione o seguinte texto:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

1. Certifique-se de que o serviço de rede será iniciado no momento da inicialização, executando o seguinte comando:

        # sudo systemctl enable network

1. Registe-se a sua subscrição do Red Hat para permitir a instalação de pacotes do repositório RHEL, executando o seguinte comando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. Modificar a linha de arranque de kernel em sua configuração de grub para incluir parâmetros de kernel adicionais para o Azure. Para fazer essa modificação, abra `/etc/default/grub` num editor de texto e editar o `GRUB_CMDLINE_LINUX` parâmetro. Por exemplo:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Esta configuração também garante que todas as mensagens de consola sejam enviadas para a primeira porta serial, que pode ajudá-lo Azure suporte com a depuração de problemas. Ele também desativa as convenções de nomenclatura do RHEL 7 novo para NICs. Além disso, recomendamos que remova os seguintes parâmetros:
   
        rhgb quiet crashkernel=auto
   
    Arranque gráfica e quieto não são úteis num ambiente de nuvem onde queremos todos os registos a serem enviados para a porta serial. Pode deixar o `crashkernel` opção configurada se assim o desejar. Tenha em atenção que este parâmetro reduz a quantidade de memória disponível na máquina virtual por 128 MB ou mais, que pode ser problemático em tamanhos de máquinas virtuais mais pequenos.

1. Depois de terminar edição `/etc/default/grub`, execute o seguinte comando para recriar a configuração do grub:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

1. Adicione módulos do Hyper-V para initramfs.

    Editar `/etc/dracut.conf`, adicionar conteúdo:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Recrie initramfs:

        # dracut -f -v

1. Certifique-se de que o servidor SSH está instalado e configurado para iniciar no momento da inicialização. Esta definição é normalmente o padrão. Modificar `/etc/ssh/sshd_config` para incluir a seguinte linha:

        ClientAliveInterval 180

1. O pacote de WALinuxAgent `WALinuxAgent-<version>`, tiver sido enviado para o repositório de recursos extras do Red Hat. Ative o repositório de extras ao executar o seguinte comando:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

1. Instale o agente Linux do Azure ao executar o seguinte comando:

        # sudo yum install WALinuxAgent

        # sudo systemctl enable waagent.service

1. Não crie espaço de comutação em disco do sistema operativo.

    O agente Linux do Azure podem configurar automaticamente o espaço de comutação utilizando o disco de recurso local que está ligado à máquina virtual depois da máquina virtual é aprovisionada no Azure. Tenha em atenção que o disco de recurso local é um disco temporário, e ele poderá ser esvaziado quando a máquina virtual é desaprovisionada. Depois de instalar o agente Linux do Azure no passo anterior, modificar os parâmetros seguintes na `/etc/waagent.conf` adequadamente:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Se desejar anular o registo da subscrição, execute o seguinte comando:

        # sudo subscription-manager unregister

1. Execute os seguintes comandos para desaprovisionar a máquina virtual e prepará-lo para o aprovisionamento no Azure:

        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Encerre a máquina virtual e converter o arquivo VMDK para formato VHD.

> [!NOTE]
> Há um bug conhecido em versões de qemu img > = 2.2.1 do buildship que resulta num VHD formatado incorretamente. Foi corrigido o problema em QEMU 2.6. Recomenda-se para utilizar qemu-img 2.2.0 ou inferior, ou atualizar para o 2.6 ou superior. Referência: https://bugs.launchpad.net/qemu/+bug/1490611.
>


    First convert the image to raw format:

        # qemu-img convert -f vmdk -O raw rhel-7.4.vmdk rhel-7.4.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-7.4.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd


## <a name="prepare-a-red-hat-based-virtual-machine-from-an-iso-by-using-a-kickstart-file-automatically"></a>Preparar uma máquina de virtual baseada em Red Hat a partir de um ISO utilizando um ficheiro de kickstart automaticamente
### <a name="prepare-a-rhel-7-virtual-machine-from-a-kickstart-file"></a>Preparar uma máquina virtual do RHEL 7 a partir de um ficheiro de kickstart

1.  Crie um ficheiro de kickstart que inclui o seguinte conteúdo e guarde o ficheiro. Para obter detalhes sobre a instalação de dar, consulte a [guia de instalação de dar](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html).

        # Kickstart for provisioning a RHEL 7 Azure VM

        # System authorization information
          auth --enableshadow --passalgo=sha512

        # Use graphical install
        text

        # Do not run the Setup Agent on first boot
        firstboot --disable

        # Keyboard layouts
        keyboard --vckeymap=us --xlayouts='us'

        # System language
        lang en_US.UTF-8

        # Network information
        network  --bootproto=dhcp

        # Root password
        rootpw --plaintext "to_be_disabled"

        # System services
        services --enabled="sshd,waagent,NetworkManager"

        # System timezone
        timezone Etc/UTC --isUtc --ntpservers 0.rhel.pool.ntp.org,1.rhel.pool.ntp.org,2.rhel.pool.ntp.org,3.rhel.pool.ntp.org

        # Partition clearing information
        clearpart --all --initlabel

        # Clear the MBR
        zerombr

        # Disk partitioning information
        part /boot --fstype="xfs" --size=500
        part / --fstyp="xfs" --size=1 --grow --asprimary

        # System bootloader configuration
        bootloader --location=mbr

        # Firewall configuration
        firewall --disabled

        # Enable SELinux
        selinux --enforcing

        # Don't configure X
        skipx

        # Power down the machine after install
        poweroff

        %packages
        @base
        @console-internet
        chrony
        sudo
        parted
        -dracut-config-rescue

        %end

        %post --log=/var/log/anaconda/post-install.log

        #!/bin/bash

        # Register Red Hat Subscription
        subscription-manager register --username=XXX --password=XXX --auto-attach --force

        # Install latest repo update
        yum update -y

        # Enable extras repo
        subscription-manager repos --enable=rhel-7-server-extras-rpms

        # Install WALinuxAgent
        yum install -y WALinuxAgent

        # Unregister Red Hat subscription
        subscription-manager unregister

        # Enable waaagent at boot-up
        systemctl enable waagent

        # Disable the root account
        usermod root -p '!!'

        # Configure swap in WALinuxAgent
        sed -i 's/^\(ResourceDisk\.EnableSwap\)=[Nn]$/\1=y/g' /etc/waagent.conf
        sed -i 's/^\(ResourceDisk\.SwapSizeMB\)=[0-9]*$/\1=2048/g' /etc/waagent.conf

        # Set the cmdline
        sed -i 's/^\(GRUB_CMDLINE_LINUX\)=".*"$/\1="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"/g' /etc/default/grub

        # Enable SSH keepalive
        sed -i 's/^#\(ClientAliveInterval\).*$/\1 180/g' /etc/ssh/sshd_config

        # Build the grub cfg
        grub2-mkconfig -o /boot/grub2/grub.cfg

        # Configure network
        cat << EOF > /etc/sysconfig/network-scripts/ifcfg-eth0
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no
        EOF

        # Deprovision and prepare for Azure
        waagent -force -deprovision

        %end

1. Coloque o ficheiro de kickstart que o sistema de instalação pode aceder.

1. No Gestor de Hyper-V, crie uma nova máquina virtual. Sobre o **ligar disco rígido Virtual** página, selecione **anexar um disco rígido virtual mais tarde**e conclua o Assistente de Nova Máquina Virtual.

1. Abra as definições de máquina virtual:

    a.  Anexe um disco rígido virtual novo à máquina virtual. Verifique se seleciona **formato VHD** e **tamanho fixo**.

    b.  Anexe a instalação ISO à unidade de DVD.

    c.  Defina o BIOS para arrancar a partir do CD.

1. Inicia a máquina virtual. Quando o guia de instalação for apresentada, prima **separador** para configurar as opções de arranque.

1. ENTER `inst.ks=<the location of the kickstart file>` no final das opções de inicialização e prima **Enter**.

1. Aguarde a conclusão da instalação. Quando estiver concluído, a máquina virtual será encerrada automaticamente. O VHD do Linux está agora pronto para ser carregado para o Azure.

## <a name="known-issues"></a>Problemas conhecidos
### <a name="the-hyper-v-driver-could-not-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>O driver de Hyper-V não poderia estar incluído no disco de RAM inicial ao usar um hipervisor não Hyper-V

Em alguns casos, instaladores de Linux podem não incluir os controladores para o Hyper-V no disco RAM inicial (initrd ou initramfs), a menos que o Linux Deteta que está em execução num ambiente de Hyper-V.

Quando estiver a utilizar um sistema de Virtualização diferentes (ou seja, Virtualbox, Xen, etc.) para preparar sua imagem do Linux, poderá ter de recriar initrd para garantir que, pelo menos, os módulos de kernel hv_vmbus e hv_storvsc estão disponíveis no disco de RAM inicial. Este é um problema conhecido, pelo menos, em sistemas que são baseiam-se a distribuição do Red Hat a montante.

Para resolver este problema, adicione módulos do Hyper-V para initramfs e reconstrui-lo:

Editar `/etc/dracut.conf`e adicione o seguinte conteúdo:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

Recrie initramfs:

        # dracut -f -v

Para obter mais detalhes, veja as informações [reconstruir initramfs](https://access.redhat.com/solutions/1958).

## <a name="next-steps"></a>Passos Seguintes
Agora, está pronto para utilizar o seu disco rígido virtual do Red Hat Enterprise Linux para criar novas máquinas virtuais no Azure. Se esta for a primeira vez que está a carregar o ficheiro. vhd para o Azure, veja [criar uma VM do Linux a partir de um disco personalizado](upload-vhd.md#option-1-upload-a-vhd).

Para obter mais detalhes sobre os hipervisores que são certificados para executar o Red Hat Enterprise Linux, consulte [o site do Red Hat](https://access.redhat.com/certified-hypervisors).
