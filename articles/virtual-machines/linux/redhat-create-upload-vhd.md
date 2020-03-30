---
title: Crie e carregue um VHD Linux Da Empresa de Chapéu Vermelho para uso em Azure
description: Aprenda a criar e carregar um disco rígido virtual Azure (VHD) que contém um sistema operativo Red Hat Linux.
services: virtual-machines-linux
documentationcenter: ''
author: gbowerman
manager: gwallace
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 6c6b8f72-32d3-47fa-be94-6cb54537c69f
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 05/17/2019
ms.author: guybo
ms.openlocfilehash: cd0a71c60930e3eb659255a23cdb03360730f2a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060720"
---
# <a name="prepare-a-red-hat-based-virtual-machine-for-azure"></a>Prepare a Red Hat-based virtual machine for Azure (Preparar uma máquina virtual baseada em Red Hat para o Azure)
Neste artigo, você aprenderá a preparar uma máquina virtual Red Hat Enterprise Linux (RHEL) para utilização em Azure. As versões do RHEL que estão abrangidas por este artigo são de 6,7+ e 7.1+. Os hipervisores para preparação que estão cobertos neste artigo são Hiper-V, máquina virtual baseada em kernel (KVM) e VMware. Para obter mais informações sobre os requisitos de elegibilidade para participar no programa de acesso à nuvem da Red Hat, consulte o [site cloud access da Red Hat](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) e o Running [RHEL no Azure](https://access.redhat.com/ecosystem/ccsp/microsoft-azure). Para formas de automatizar a construção de imagens RHEL, consulte o [Azure Image Builder](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-overview).

## <a name="prepare-a-red-hat-based-virtual-machine-from-hyper-v-manager"></a>Prepare uma máquina virtual baseada em Chapéu Vermelho do Hiper-V Manager

### <a name="prerequisites"></a>Pré-requisitos
Esta secção pressupõe que já obteve um ficheiro ISO do website da Red Hat e instalou a imagem RHEL num disco rígido virtual (VHD). Para mais detalhes sobre como usar o Hyper-V Manager para instalar uma imagem do sistema operativo, consulte [Instalar a Função Hyper-V e configurar uma máquina virtual](https://technet.microsoft.com/library/hh846766.aspx).

**Notas de instalação RHEL**

* O Azure não suporta o formato VHDX. O Azure suporta apenas VHD fixo. Pode utilizar o Hyper-V Manager para converter o disco em formato VHD, ou pode utilizar o cmdlet convert-vhd. Se utilizar o VirtualBox, selecione **o tamanho Fixo** em oposição à opção dinamicamente atribuída ao criar o disco.
* O Azure suporta máquinas virtuais gen1 (bota BIOS) & Gen2 (bota UEFI).
* O tamanho máximo que é permitido para o VHD é de 1.023 GB.
* O Gestor de VolumeLógico (LVM) é suportado e pode ser utilizado no disco osso ou nos discos de dados em máquinas virtuais Azure. No entanto, em geral, recomenda-se a utilização de divisórias padrão no disco OS em vez de LVM. Esta prática evitará conflitos de nome lvm com máquinas virtuais clonadas, especialmente se alguma vez precisar de anexar um disco do sistema operativo a outra máquina virtual idêntica para resolução de problemas. Consulte também [documentação LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) e [RAID.](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* É necessário suporte kernel para a montagem de sistemas de ficheiros Universal Disk Format (UDF). No início do Azure, os meios de comunicação formados da UDF que estão ligados ao hóspede passam a configuração de provisionamento à máquina virtual Linux. O Agente Azure Linux deve ser capaz de montar o sistema de ficheiros UDF para ler a sua configuração e fornecer a máquina virtual.
* Não configure uma divisória de troca no disco do sistema operativo. O Agente Linux pode ser configurado para criar um ficheiro de troca no disco de recursos temporários.  Mais informações sobre este caso podem ser encontradas nos seguintes passos.
* Todos os VHDs em Azure devem ter um tamanho virtual alinhado a 1MB. Ao converter de um disco cru para VHD, deve certificar-se de que o tamanho do disco bruto é um múltiplo de 1MB antes da conversão. Mais detalhes podem ser encontrados nos degraus abaixo. Consulte também as Notas de [Instalação do Linux](create-upload-generic.md#general-linux-installation-notes) para obter mais informações.

### <a name="prepare-a-rhel-6-virtual-machine-from-hyper-v-manager"></a>Prepare uma máquina virtual RHEL 6 do Hyper-V Manager

1. No Hyper-V Manager, selecione a máquina virtual.

1. Clique em **Ligar** para abrir uma janela de consola para a máquina virtual.

1. No RHEL 6, o NetworkManager pode interferir com o agente Azure Linux. Desinstale esta embalagem executando o seguinte comando:
   
        # sudo rpm -e --nodeps NetworkManager

1. Crie ou `/etc/sysconfig/network` edite o ficheiro e adicione o seguinte texto:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Crie ou `/etc/sysconfig/network-scripts/ifcfg-eth0` edite o ficheiro e adicione o seguinte texto:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

1. Mover (ou remover) as regras udev para evitar gerar regras estáticas para a interface Ethernet. Estas regras causam problemas quando se clona uma máquina virtual no Microsoft Azure ou Hyper-V:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

1. Certifique-se de que o serviço de rede começará no momento do arranque executando o seguinte comando:

        # sudo chkconfig network on

1. Registe a subscrição do Chapéu Vermelho para permitir a instalação de pacotes no repositório RHEL executando o seguinte comando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. O pacote WALinuxAgent, `WALinuxAgent-<version>`foi empurrado para o repositório de extras red hat. Ativar o repositório extras executando o seguinte comando:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

1. Modifique a linha de arranque do núcleo na sua configuração de larva para incluir parâmetros adicionais de kernel para o Azure. Para fazer esta `/boot/grub/menu.lst` modificação, abra num editor de texto e certifique-se de que o núcleo predefinido inclui os seguintes parâmetros:
    
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    
    Isto também garantirá que todas as mensagens de consola são enviadas para a primeira porta de série, que pode ajudar o suporte do Azure com problemas de depuração.
    
    Além disso, recomendamos que remova os seguintes parâmetros:
    
        rhgb quiet crashkernel=auto
    
    A bota gráfica e tranquila não é útil num ambiente de nuvem onde queremos que todos os troncos sejam enviados para a porta de série.  Pode deixar `crashkernel` a opção configurada se desejar. Note que este parâmetro reduz a quantidade de memória disponível na máquina virtual em 128 MB ou mais. Esta configuração pode ser problemática em tamanhos de máquinavirtual mais pequenos.


1. Certifique-se de que o servidor de concha seleção (SSH) está instalado e configurado para iniciar no momento do arranque, que normalmente é a predefinição. Modificar /etc/ssh/sshd_config para incluir a seguinte linha:

        ClientAliveInterval 180

1. Instale o Agente Azure Linux executando o seguinte comando:

        # sudo yum install WALinuxAgent

        # sudo chkconfig waagent on

    A instalação do pacote WALinuxAgent remove os pacotes NetworkManager e NetworkManager-gnose se ainda não foram removidos no passo 3.

1. Não crie espaço de troca no disco do sistema operativo.

    O Agente Azure Linux pode configurar automaticamente o espaço de troca utilizando o disco de recursos locais que está ligado à máquina virtual após a aprovisionação da máquina virtual no Azure. Note que o disco de recursos locais é um disco temporário e que pode ser esvaziado se a máquina virtual for desprovisionada. Depois de instalar o Agente Azure Linux na etapa anterior, modifique adequadamente os seguintes parâmetros em /etc/waagent.conf:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Desregistre a subscrição (se necessário) executando o seguinte comando:

        # sudo subscription-manager unregister

1. Executar os seguintes comandos para desprovisionar a máquina virtual e prepará-la para o fornecimento em Azure:

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Clique **Action** > **em** desligar a ação em Hyper-V Manager. O seu VHD Linux está agora pronto para ser enviado para o Azure.


### <a name="prepare-a-rhel-7-virtual-machine-from-hyper-v-manager"></a>Prepare uma máquina virtual RHEL 7 do Hyper-V Manager

1. No Hyper-V Manager, selecione a máquina virtual.

1. Clique em **Ligar** para abrir uma janela de consola para a máquina virtual.

1. Crie ou `/etc/sysconfig/network` edite o ficheiro e adicione o seguinte texto:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Crie ou `/etc/sysconfig/network-scripts/ifcfg-eth0` edite o ficheiro e adicione o seguinte texto:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
    PERSISTENT_DHCLIENT=sim NM_CONTROLLED=sim

1. Certifique-se de que o serviço de rede começará no momento do arranque executando o seguinte comando:

        # sudo systemctl enable network

1. Registe a subscrição do Chapéu Vermelho para permitir a instalação de pacotes no repositório RHEL executando o seguinte comando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. Modifique a linha de arranque do núcleo na sua configuração de larva para incluir parâmetros adicionais de kernel para o Azure. Para fazer esta `/etc/default/grub` modificação, abra num `GRUB_CMDLINE_LINUX` editor de texto e edite o parâmetro. Por exemplo:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Isto também garantirá que todas as mensagens de consola são enviadas para a primeira porta de série, que pode ajudar o suporte do Azure com problemas de depuração. Esta configuração também desliga as novas convenções de nomeação RHEL 7 para NICs. Além disso, recomendamos que remova os seguintes parâmetros:
   
        rhgb quiet crashkernel=auto
   
    A bota gráfica e tranquila não é útil num ambiente de nuvem onde queremos que todos os troncos sejam enviados para a porta de série. Pode deixar `crashkernel` a opção configurada se desejar. Note que este parâmetro reduz a quantidade de memória disponível na máquina virtual em 128 MB ou mais, o que pode ser problemático em tamanhos de máquina virtual mais pequenos.

1. Depois de terminar `/etc/default/grub`a edição, faça o seguinte comando para reconstruir a configuração da comida:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

1. Certifique-se de que o servidor SSH está instalado e configurado para iniciar no momento do arranque, que normalmente é a falha. Modificar `/etc/ssh/sshd_config` para incluir a seguinte linha:

        ClientAliveInterval 180

1. O pacote WALinuxAgent, `WALinuxAgent-<version>`foi empurrado para o repositório de extras red hat. Ativar o repositório extras executando o seguinte comando:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

1. Instale o Agente Azure Linux executando o seguinte comando:

        # sudo yum install WALinuxAgent

        # sudo systemctl enable waagent.service

1. Não crie espaço de troca no disco do sistema operativo.

    O Agente Azure Linux pode configurar automaticamente o espaço de troca utilizando o disco de recursos locais que está ligado à máquina virtual após a aprovisionação da máquina virtual no Azure. Note que o disco de recursos locais é um disco temporário, e pode ser esvaziado se a máquina virtual for desprovisionada. Depois de instalar o Agente Azure Linux na etapa `/etc/waagent.conf` anterior, modifique adequadamente os seguintes parâmetros:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Se pretender desmarcar a subscrição, execute o seguinte comando:

        # sudo subscription-manager unregister

1. Executar os seguintes comandos para desprovisionar a máquina virtual e prepará-la para o fornecimento em Azure:

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Clique **Action** > **em** desligar a ação em Hyper-V Manager. O seu VHD Linux está agora pronto para ser enviado para o Azure.


## <a name="prepare-a-red-hat-based-virtual-machine-from-kvm"></a>Prepare uma máquina virtual baseada em Chapéu Vermelho da KVM
### <a name="prepare-a-rhel-6-virtual-machine-from-kvm"></a>Prepare uma máquina virtual RHEL 6 da KVM

1. Descarregue a imagem KVM de RHEL 6 do site red hat.

1. Dete toda a palavra-passe.

    Gere uma palavra-passe encriptada e copie a saída do comando:

        # openssl passwd -1 changeme

    Detete uma palavra-passe de raiz com peixe-hóspede:
        
        # guestfish --rw -a <image-name>
        > <fs> run
        > <fs> list-filesystems
        > <fs> mount /dev/sda1 /
        > <fs> vi /etc/shadow
        > <fs> exit

   Mude o segundo campo do utilizador raiz de "!!" para a senha encriptada.

1. Crie uma máquina virtual em KVM a partir da imagem qcow2. Desloque o tipo de disco para **qcow2**, e desloque o modelo do dispositivo de interface de rede virtual para **virtio**. Em seguida, inicie a máquina virtual e inscreva-se como raiz.

1. Crie ou `/etc/sysconfig/network` edite o ficheiro e adicione o seguinte texto:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Crie ou `/etc/sysconfig/network-scripts/ifcfg-eth0` edite o ficheiro e adicione o seguinte texto:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

1. Mover (ou remover) as regras udev para evitar gerar regras estáticas para a interface Ethernet. Estas regras causam problemas quando se clona uma máquina virtual em Azure ou Hyper-V:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

1. Certifique-se de que o serviço de rede começará no momento do arranque executando o seguinte comando:

        # chkconfig network on

1. Registe a subscrição do Chapéu Vermelho para permitir a instalação de pacotes no repositório RHEL executando o seguinte comando:

        # subscription-manager register --auto-attach --username=XXX --password=XXX

1. Modifique a linha de arranque do núcleo na sua configuração de larva para incluir parâmetros adicionais de kernel para o Azure. Para fazer esta `/boot/grub/menu.lst` configuração, abra num editor de texto e certifique-se de que o núcleo predefinido inclui os seguintes parâmetros:
    
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    
    Isto também garantirá que todas as mensagens de consola são enviadas para a primeira porta de série, que pode ajudar o suporte do Azure com problemas de depuração.
    
    Além disso, recomendamos que remova os seguintes parâmetros:
    
        rhgb quiet crashkernel=auto
    
    A bota gráfica e tranquila não é útil num ambiente de nuvem onde queremos que todos os troncos sejam enviados para a porta de série. Pode deixar `crashkernel` a opção configurada se desejar. Note que este parâmetro reduz a quantidade de memória disponível na máquina virtual em 128 MB ou mais, o que pode ser problemático em tamanhos de máquina virtual mais pequenos.


1. Adicione módulos Hyper-V aos initramfs:  

    Editar `/etc/dracut.conf`e adicionar os seguintes conteúdos:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Reconstruir initramfs:

        # dracut -f -v

1. Desinstale o cloud-init:

        # yum remove cloud-init

1. Certifique-se de que o servidor SSH está instalado e configurado para iniciar no momento do arranque:

        # chkconfig sshd on

    Modificar /etc/ssh/sshd_config para incluir as seguintes linhas:

        PasswordAuthentication yes
        ClientAliveInterval 180

1. O pacote WALinuxAgent, `WALinuxAgent-<version>`foi empurrado para o repositório de extras red hat. Ativar o repositório extras executando o seguinte comando:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

1. Instale o Agente Azure Linux executando o seguinte comando:

        # yum install WALinuxAgent

        # chkconfig waagent on

1. O Agente Azure Linux pode configurar automaticamente o espaço de troca utilizando o disco de recursos locais que está ligado à máquina virtual após a aprovisionação da máquina virtual no Azure. Note que o disco de recursos locais é um disco temporário, e pode ser esvaziado se a máquina virtual for desprovisionada. Depois de instalar o Agente Azure Linux na etapa anterior, modifique adequadamente os seguintes parâmetros em **/etc/waagent.conf:**

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Desregistre a subscrição (se necessário) executando o seguinte comando:

        # subscription-manager unregister

1. Executar os seguintes comandos para desprovisionar a máquina virtual e prepará-la para o fornecimento em Azure:

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Desligue a máquina virtual em KVM.

1. Converta a imagem qcow2 para o formato VHD.

> [!NOTE]
> Existe um bug conhecido nas versões qemu-img >=2.2.1 que resulta num VHD mal formatado. A questão foi corrigida no QEMU 2.6. Recomenda-se a utilização de qemu-img 2.2.0 ou inferior, ou atualização para 2.6 ou superior. Referência: https://bugs.launchpad.net/qemu/+bug/1490611.
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

        
### <a name="prepare-a-rhel-7-virtual-machine-from-kvm"></a>Prepare uma máquina virtual RHEL 7 da KVM

1. Descarregue a imagem KVM de RHEL 7 a partir do site red hat. Este procedimento utiliza o RHEL 7 como exemplo.

1. Dete toda a palavra-passe.

    Gere uma palavra-passe encriptada e copie a saída do comando:

        # openssl passwd -1 changeme

    Detete uma palavra-passe de raiz com peixe-hóspede:

        # guestfish --rw -a <image-name>
        > <fs> run
        > <fs> list-filesystems
        > <fs> mount /dev/sda1 /
        > <fs> vi /etc/shadow
        > <fs> exit

   Mude o segundo campo de utilizador raiz de "!!" para a senha encriptada.

1. Crie uma máquina virtual em KVM a partir da imagem qcow2. Desloque o tipo de disco para **qcow2**, e desloque o modelo do dispositivo de interface de rede virtual para **virtio**. Em seguida, inicie a máquina virtual e inscreva-se como raiz.

1. Crie ou `/etc/sysconfig/network` edite o ficheiro e adicione o seguinte texto:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Crie ou `/etc/sysconfig/network-scripts/ifcfg-eth0` edite o ficheiro e adicione o seguinte texto:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
    PERSISTENT_DHCLIENT=sim NM_CONTROLLED=sim

1. Certifique-se de que o serviço de rede começará no momento do arranque executando o seguinte comando:

        # sudo systemctl enable network

1. Registe a subscrição do Chapéu Vermelho para permitir a instalação de pacotes no repositório RHEL, executando o seguinte comando:

        # subscription-manager register --auto-attach --username=XXX --password=XXX

1. Modifique a linha de arranque do núcleo na sua configuração de larva para incluir parâmetros adicionais de kernel para o Azure. Para fazer esta `/etc/default/grub` configuração, abra num `GRUB_CMDLINE_LINUX` editor de texto e edite o parâmetro. Por exemplo:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Este comando também garante que todas as mensagens de consola são enviadas para a primeira porta de série, que pode ajudar o suporte do Azure com problemas de depuração. O comando também desliga as novas convenções de nomeação RHEL 7 para NICs. Além disso, recomendamos que remova os seguintes parâmetros:
   
        rhgb quiet crashkernel=auto
   
    A bota gráfica e tranquila não é útil num ambiente de nuvem onde queremos que todos os troncos sejam enviados para a porta de série. Pode deixar `crashkernel` a opção configurada se desejar. Note que este parâmetro reduz a quantidade de memória disponível na máquina virtual em 128 MB ou mais, o que pode ser problemático em tamanhos de máquina virtual mais pequenos.

1. Depois de terminar `/etc/default/grub`a edição, faça o seguinte comando para reconstruir a configuração da comida:

        # grub2-mkconfig -o /boot/grub2/grub.cfg

1. Adicione os módulos Hyper-V em initramfs.

    Editar `/etc/dracut.conf` e adicionar conteúdo:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Reconstruir initramfs:

        # dracut -f -v

1. Desinstale o cloud-init:

        # yum remove cloud-init

1. Certifique-se de que o servidor SSH está instalado e configurado para iniciar no momento do arranque:

        # systemctl enable sshd

    Modificar /etc/ssh/sshd_config para incluir as seguintes linhas:

        PasswordAuthentication yes
        ClientAliveInterval 180

1. O pacote WALinuxAgent, `WALinuxAgent-<version>`foi empurrado para o repositório de extras red hat. Ativar o repositório extras executando o seguinte comando:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

1. Instale o Agente Azure Linux executando o seguinte comando:

        # yum install WALinuxAgent

    Ativar o serviço waagent:

        # systemctl enable waagent.service

1. Não crie espaço de troca no disco do sistema operativo.

    O Agente Azure Linux pode configurar automaticamente o espaço de troca utilizando o disco de recursos locais que está ligado à máquina virtual após a aprovisionação da máquina virtual no Azure. Note que o disco de recursos locais é um disco temporário, e pode ser esvaziado se a máquina virtual for desprovisionada. Depois de instalar o Agente Azure Linux na etapa `/etc/waagent.conf` anterior, modifique adequadamente os seguintes parâmetros:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Desregistre a subscrição (se necessário) executando o seguinte comando:

        # subscription-manager unregister

1. Executar os seguintes comandos para desprovisionar a máquina virtual e prepará-la para o fornecimento em Azure:

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Desligue a máquina virtual em KVM.

1. Converta a imagem qcow2 para o formato VHD.

> [!NOTE]
> Existe um bug conhecido nas versões qemu-img >=2.2.1 que resulta num VHD mal formatado. A questão foi corrigida no QEMU 2.6. Recomenda-se a utilização de qemu-img 2.2.0 ou inferior, ou atualização para 2.6 ou superior. Referência: https://bugs.launchpad.net/qemu/+bug/1490611.
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


## <a name="prepare-a-red-hat-based-virtual-machine-from-vmware"></a>Prepare uma máquina virtual baseada em Chapéu Vermelho a partir de VMware
### <a name="prerequisites"></a>Pré-requisitos
Esta secção pressupõe que já instalou uma máquina virtual RHEL em VMware. Para mais informações sobre como instalar um sistema operativo em VMware, consulte o Guia de [Instalação do Sistema Operativo VMware Guest](https://partnerweb.vmware.com/GOSIG/home.html).

* Ao instalar o sistema operativo Linux, recomendamos que utilize divisórias padrão em vez de LVM, o que é muitas vezes o padrão para muitas instalações. Isto evitará conflitos de nome LVM com máquina virtual clonada, especialmente se um disco do sistema operativo precisar de ser ligado a outra máquina virtual para resolução de problemas. LVM ou RAID podem ser usados em discos de dados, se preferir.
* Não configure uma divisória de troca no disco do sistema operativo. Pode configurar o agente Linux para criar um ficheiro de troca no disco de recursos temporários. Pode encontrar mais informações sobre isso nos passos que se seguem.
* Quando criar o disco rígido virtual, selecione **o disco virtual da Loja como um único ficheiro**.

### <a name="prepare-a-rhel-6-virtual-machine-from-vmware"></a>Prepare uma máquina virtual RHEL 6 da VMware
1. No RHEL 6, o NetworkManager pode interferir com o agente Azure Linux. Desinstale esta embalagem executando o seguinte comando:
   
        # sudo rpm -e --nodeps NetworkManager

1. Crie um ficheiro chamado **rede** no /etc/sysconfig/diretório que contenha o seguinte texto:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Crie ou `/etc/sysconfig/network-scripts/ifcfg-eth0` edite o ficheiro e adicione o seguinte texto:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

1. Mover (ou remover) as regras udev para evitar gerar regras estáticas para a interface Ethernet. Estas regras causam problemas quando se clona uma máquina virtual em Azure ou Hyper-V:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

1. Certifique-se de que o serviço de rede começará no momento do arranque executando o seguinte comando:

        # sudo chkconfig network on

1. Registe a subscrição do Chapéu Vermelho para permitir a instalação de pacotes no repositório RHEL executando o seguinte comando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. O pacote WALinuxAgent, `WALinuxAgent-<version>`foi empurrado para o repositório de extras red hat. Ativar o repositório extras executando o seguinte comando:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

1. Modifique a linha de arranque do núcleo na sua configuração de larva para incluir parâmetros adicionais de kernel para o Azure. Para isso, `/etc/default/grub` abra num editor de `GRUB_CMDLINE_LINUX` texto e edite o parâmetro. Por exemplo:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0"
   
   Isto também garantirá que todas as mensagens de consola são enviadas para a primeira porta de série, que pode ajudar o suporte do Azure com problemas de depuração. Além disso, recomendamos que remova os seguintes parâmetros:
   
        rhgb quiet crashkernel=auto
   
    A bota gráfica e tranquila não é útil num ambiente de nuvem onde queremos que todos os troncos sejam enviados para a porta de série. Pode deixar `crashkernel` a opção configurada se desejar. Note que este parâmetro reduz a quantidade de memória disponível na máquina virtual em 128 MB ou mais, o que pode ser problemático em tamanhos de máquina virtual mais pequenos.

1. Adicione módulos Hyper-V aos initramfs:

    Editar `/etc/dracut.conf`e adicionar os seguintes conteúdos:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Reconstruir initramfs:

        # dracut -f -v

1. Certifique-se de que o servidor SSH está instalado e configurado para iniciar no momento do arranque, que normalmente é a falha. Modificar `/etc/ssh/sshd_config` para incluir a seguinte linha:

    ClienteAliveInterval 180

1. Instale o Agente Azure Linux executando o seguinte comando:

        # sudo yum install WALinuxAgent

        # sudo chkconfig waagent on

1. Não crie espaço de troca no disco do sistema operativo.

    O Agente Azure Linux pode configurar automaticamente o espaço de troca utilizando o disco de recursos locais que está ligado à máquina virtual após a aprovisionação da máquina virtual no Azure. Note que o disco de recursos locais é um disco temporário, e pode ser esvaziado se a máquina virtual for desprovisionada. Depois de instalar o Agente Azure Linux na etapa `/etc/waagent.conf` anterior, modifique adequadamente os seguintes parâmetros:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Desregistre a subscrição (se necessário) executando o seguinte comando:

        # sudo subscription-manager unregister

1. Executar os seguintes comandos para desprovisionar a máquina virtual e prepará-la para o fornecimento em Azure:

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Desligue a máquina virtual e converta o ficheiro VMDK num ficheiro .vhd.

> [!NOTE]
> Existe um bug conhecido nas versões qemu-img >=2.2.1 que resulta num VHD mal formatado. A questão foi corrigida no QEMU 2.6. Recomenda-se a utilização de qemu-img 2.2.0 ou inferior, ou atualização para 2.6 ou superior. Referência: https://bugs.launchpad.net/qemu/+bug/1490611.
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


### <a name="prepare-a-rhel-7-virtual-machine-from-vmware"></a>Prepare uma máquina virtual RHEL 7 da VMware
1. Crie ou `/etc/sysconfig/network` edite o ficheiro e adicione o seguinte texto:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Crie ou `/etc/sysconfig/network-scripts/ifcfg-eth0` edite o ficheiro e adicione o seguinte texto:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
    PERSISTENT_DHCLIENT=sim NM_CONTROLLED=sim

1. Certifique-se de que o serviço de rede começará no momento do arranque executando o seguinte comando:

        # sudo systemctl enable network

1. Registe a subscrição do Chapéu Vermelho para permitir a instalação de pacotes no repositório RHEL executando o seguinte comando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. Modifique a linha de arranque do núcleo na sua configuração de larva para incluir parâmetros adicionais de kernel para o Azure. Para fazer esta `/etc/default/grub` modificação, abra num `GRUB_CMDLINE_LINUX` editor de texto e edite o parâmetro. Por exemplo:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Esta configuração também garante que todas as mensagens de consola são enviadas para a primeira porta de série, que pode ajudar o suporte do Azure com problemas de depuração. Também desliga as novas convenções de nomeação RHEL 7 para NICs. Além disso, recomendamos que remova os seguintes parâmetros:
   
        rhgb quiet crashkernel=auto
   
    A bota gráfica e tranquila não é útil num ambiente de nuvem onde queremos que todos os troncos sejam enviados para a porta de série. Pode deixar `crashkernel` a opção configurada se desejar. Note que este parâmetro reduz a quantidade de memória disponível na máquina virtual em 128 MB ou mais, o que pode ser problemático em tamanhos de máquina virtual mais pequenos.

1. Depois de terminar `/etc/default/grub`a edição, faça o seguinte comando para reconstruir a configuração da comida:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

1. Adicione os módulos Hyper-V aos initramfs.

    Editar, `/etc/dracut.conf`adicionar conteúdo:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Reconstruir initramfs:

        # dracut -f -v

1. Certifique-se de que o servidor SSH está instalado e configurado para iniciar no momento do arranque. Esta definição é geralmente a predefinição. Modificar `/etc/ssh/sshd_config` para incluir a seguinte linha:

        ClientAliveInterval 180

1. O pacote WALinuxAgent, `WALinuxAgent-<version>`foi empurrado para o repositório de extras red hat. Ativar o repositório extras executando o seguinte comando:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

1. Instale o Agente Azure Linux executando o seguinte comando:

        # sudo yum install WALinuxAgent

        # sudo systemctl enable waagent.service

1. Não crie espaço de troca no disco do sistema operativo.

    O Agente Azure Linux pode configurar automaticamente o espaço de troca utilizando o disco de recursos locais que está ligado à máquina virtual após a aprovisionação da máquina virtual no Azure. Note que o disco de recursos locais é um disco temporário, e pode ser esvaziado se a máquina virtual for desprovisionada. Depois de instalar o Agente Azure Linux na etapa `/etc/waagent.conf` anterior, modifique adequadamente os seguintes parâmetros:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Se pretender desmarcar a subscrição, execute o seguinte comando:

        # sudo subscription-manager unregister

1. Executar os seguintes comandos para desprovisionar a máquina virtual e prepará-la para o fornecimento em Azure:

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Desligue a máquina virtual e converta o ficheiro VMDK para o formato VHD.

> [!NOTE]
> Existe um bug conhecido nas versões qemu-img >=2.2.1 que resulta num VHD mal formatado. A questão foi corrigida no QEMU 2.6. Recomenda-se a utilização de qemu-img 2.2.0 ou inferior, ou atualização para 2.6 ou superior. Referência: https://bugs.launchpad.net/qemu/+bug/1490611.
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


## <a name="prepare-a-red-hat-based-virtual-machine-from-an-iso-by-using-a-kickstart-file-automatically"></a>Prepare uma máquina virtual baseada em Chapéu Vermelho a partir de um ISO utilizando um ficheiro kickstart automaticamente
### <a name="prepare-a-rhel-7-virtual-machine-from-a-kickstart-file"></a>Prepare uma máquina virtual RHEL 7 a partir de um ficheiro kickstart

1.  Crie um ficheiro kickstart que inclua o seguinte conteúdo e guarde o ficheiro. Para mais informações sobre a instalação do kickstart, consulte o Guia de [Instalação do Kickstart](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html).

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
    PERSISTENT_DHCLIENT=sim NM_CONTROLLED=sim EOF

        # Deprovision and prepare for Azure if you are creating a generalized image
        waagent -force -deprovision

        %end

1. Coloque o ficheiro de arranque onde o sistema de instalação pode aceder-lhe.

1. Em Hyper-V Manager, crie uma nova máquina virtual. Na página **Connect Virtual Hard Disk,** selecione **Fixe um disco rígido virtual mais tarde**e complete o Novo Assistente de Máquina Virtual.

1. Abra as definições da máquina virtual:

    a.  Fixe um novo disco rígido virtual à máquina virtual. Certifique-se de selecionar **formato VHD** e **tamanho fixo.**

    b.  Prenda a instalação ISO à unidade de DVD.

    c.  Desloque o BIOS para arrancar a partir de CD.

1. Inicie a máquina virtual. Quando o guia de instalação aparecer, prima **O separador** para configurar as opções de arranque.

1. Introduza `inst.ks=<the location of the kickstart file>` no final das opções de arranque e prima **Enter**.

1. Aguarde a instalação para terminar. Quando estiver terminado, a máquina virtual será desligada automaticamente. O seu VHD Linux está agora pronto para ser enviado para o Azure.

## <a name="known-issues"></a>Problemas conhecidos
### <a name="the-hyper-v-driver-could-not-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>O condutor hyper-V não podia ser incluído no disco RAM inicial quando se utiliza vavisor não hiper-V

Em alguns casos, os instaladores Linux podem não incluir os controladores de Hyper-V no disco RAM inicial (initrd ou initramfs) a menos que o Linux detete que está a funcionar num ambiente Hiper-V.

Quando estiver a utilizar um sistema de virtualização diferente (isto é, VirtualBox, Xen, etc.) para preparar a sua imagem Linux, poderá ter de reconstruir o initrd para garantir que pelo menos os módulos de hv_vmbus e hv_storvsc núcleo estão disponíveis no disco RAM inicial. Esta é uma questão conhecida, pelo menos, em sistemas baseados na distribuição a montante do Chapéu Vermelho.

Para resolver este problema, adicione os módulos Hyper-V aos initramfs e reconstruí-lo:

Editar `/etc/dracut.conf`e adicionar os seguintes conteúdos:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

Reconstruir initramfs:

        # dracut -f -v

Para mais detalhes, consulte a informação sobre a reconstrução dos [initramfs](https://access.redhat.com/solutions/1958).

## <a name="next-steps"></a>Passos seguintes
* Está agora pronto para usar o seu disco rígido virtual Red Hat Enterprise Linux para criar novas máquinas virtuais em Azure. Se esta for a primeira vez que está a enviar o ficheiro .vhd para o Azure, consulte [Create a Linux VM a partir de um disco personalizado](upload-vhd.md#option-1-upload-a-vhd).
* Para mais detalhes sobre os hipervisores certificados para executar Red Hat Enterprise Linux, consulte [o site red hat](https://access.redhat.com/certified-hypervisors).
* Para saber mais sobre a utilização de imagens RHEL BYOS prontas para produção, vá à página de documentação para [BYOS](../workloads/redhat/byos.md).
