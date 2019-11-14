---
title: Criar e carregar um VHD Red Hat Enterprise Linux para uso no Azure
description: Saiba como criar e carregar um VHD (disco rígido virtual) do Azure que contém um sistema operacional Red Hat Linux.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: gwallace
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 6c6b8f72-32d3-47fa-be94-6cb54537c69f
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 05/17/2019
ms.author: szark
ms.openlocfilehash: 7c03271dc5fda5cee0b210370a965a45a6a7ef42
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035164"
---
# <a name="prepare-a-red-hat-based-virtual-machine-for-azure"></a>Preparar uma máquina virtual baseada em Red Hat para o Azure
Neste artigo, você aprenderá a preparar uma máquina virtual Red Hat Enterprise Linux (RHEL) para uso no Azure. As versões do RHEL abordadas neste artigo são 6.7 + e 7.1 +. Os hipervisores para preparação abordados neste artigo são Hyper-V, máquina virtual baseada em kernel (KVM) e VMware. Para obter mais informações sobre os requisitos de qualificação para participar do programa de acesso à nuvem do Red Hat, consulte o [site de acesso à nuvem do Red Hat](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) e [executando o RHEL no Azure](https://access.redhat.com/ecosystem/ccsp/microsoft-azure). Para obter maneiras de automatizar a criação de imagens RHEL, consulte o [Construtor de imagens do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-overview).

## <a name="prepare-a-red-hat-based-virtual-machine-from-hyper-v-manager"></a>Preparar uma máquina virtual baseada em Red Hat do Gerenciador do Hyper-V

### <a name="prerequisites"></a>Pré-requisitos
Esta seção pressupõe que você já tenha obtido um arquivo ISO do site do Red Hat e instalado a imagem do RHEL em um VHD (disco rígido virtual). Para obter mais detalhes sobre como usar o Gerenciador do Hyper-V para instalar uma imagem do sistema operacional, consulte [instalar a função do Hyper-v e configurar uma máquina virtual](https://technet.microsoft.com/library/hh846766.aspx).

**Notas de instalação do RHEL**

* O Azure não oferece suporte ao formato VHDX. O Azure dá suporte apenas ao VHD fixo. Você pode usar o Gerenciador do Hyper-V para converter o disco em formato VHD ou pode usar o cmdlet Convert-VHD. Se você usar VirtualBox, selecione **tamanho fixo** em oposição à opção padrão alocada dinamicamente ao criar o disco.
* O Azure dá suporte apenas a máquinas virtuais de geração 1. Você pode converter uma máquina virtual de geração 1 do VHDX para o formato de arquivo VHD e de expandir dinamicamente para um disco de tamanho fixo. Não é possível alterar a geração de uma máquina virtual. Para obter mais informações, consulte [devo criar uma máquina virtual de geração 1 ou 2 no Hyper-V?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).
* O tamanho máximo permitido para o VHD é 1.023 GB.
* O LVM (Gerenciador de volumes lógicos) tem suporte e pode ser usado no disco do sistema operacional ou nos discos de dados nas máquinas virtuais do Azure. No entanto, em geral, é recomendável usar partições padrão no disco do sistema operacional em vez de LVM. Essa prática evitará conflitos de nome LVM com máquinas virtuais clonadas, especialmente se você precisar anexar um disco do sistema operacional a outra máquina virtual idêntica para solução de problemas. Consulte também a documentação do [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) e do [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) .
* É necessário suporte de kernel para montar sistemas de arquivos de formato de disco universal (UDF). Na primeira inicialização no Azure, a mídia formatada por UDF anexada ao convidado passa a configuração de provisionamento para a máquina virtual do Linux. O agente Linux do Azure deve ser capaz de montar o sistema de arquivos UDF para ler sua configuração e provisionar a máquina virtual.
* Não configure uma partição de permuta no disco do sistema operacional. O agente do Linux pode ser configurado para criar um arquivo de permuta no disco de recursos temporário.  Mais informações sobre isso podem ser encontradas nas etapas a seguir.
* Todos os VHDs no Azure devem ter um tamanho virtual alinhado a 1 MB. Ao converter de um disco bruto para VHD, você deve garantir que o tamanho do disco bruto seja um múltiplo de 1MB antes da conversão. Mais detalhes podem ser encontrados nas etapas abaixo. Consulte também [notas de instalação do Linux](create-upload-generic.md#general-linux-installation-notes) para obter mais informações.

### <a name="prepare-a-rhel-6-virtual-machine-from-hyper-v-manager"></a>Preparar uma máquina virtual RHEL 6 do Gerenciador do Hyper-V

1. No Gerenciador do Hyper-V, selecione a máquina virtual.

1. Clique em **conectar** para abrir uma janela de console para a máquina virtual.

1. No RHEL 6, o NetworkManager pode interferir no agente Linux do Azure. Desinstale este pacote executando o seguinte comando:
   
        # sudo rpm -e --nodeps NetworkManager

1. Crie ou edite o arquivo de `/etc/sysconfig/network` e adicione o seguinte texto:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Crie ou edite o arquivo de `/etc/sysconfig/network-scripts/ifcfg-eth0` e adicione o seguinte texto:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

1. Mova (ou remova) as regras udev para evitar a geração de regras estáticas para a interface Ethernet. Essas regras causam problemas ao clonar uma máquina virtual no Microsoft Azure ou no Hyper-V:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

1. Verifique se o serviço de rede será iniciado no momento da inicialização executando o seguinte comando:

        # sudo chkconfig network on

1. Registre sua assinatura do Red Hat para habilitar a instalação de pacotes do repositório do RHEL executando o seguinte comando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. O pacote WALinuxAgent, `WALinuxAgent-<version>`, foi enviado por push para o repositório extras do Red Hat. Habilite o repositório de extras executando o seguinte comando:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

1. Modifique a linha de inicialização do kernel em sua configuração do grub para incluir parâmetros de kernel adicionais para o Azure. Para fazer essa modificação, abra `/boot/grub/menu.lst` em um editor de texto e verifique se o kernel padrão inclui os seguintes parâmetros:
    
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    
    Isso também garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, o que pode auxiliar o suporte do Azure com problemas de depuração.
    
    Além disso, recomendamos que você remova os seguintes parâmetros:
    
        rhgb quiet crashkernel=auto
    
    A inicialização gráfica e silenciosa não são úteis em um ambiente de nuvem onde queremos que todos os logs sejam enviados para a porta serial.  Você pode deixar a opção `crashkernel` configurada se desejar. Observe que esse parâmetro reduz a quantidade de memória disponível na máquina virtual por 128 MB ou mais. Essa configuração pode ser problemática em tamanhos menores de máquina virtual.


1. Verifique se o servidor SSH (Secure Shell) está instalado e configurado para iniciar no momento da inicialização, que geralmente é o padrão. Modifique sshd_config/etc/ssh/para incluir a seguinte linha:

        ClientAliveInterval 180

1. Instale o agente Linux do Azure executando o seguinte comando:

        # sudo yum install WALinuxAgent

        # sudo chkconfig waagent on

    A instalação do pacote WALinuxAgent remove os pacotes NetworkManager e NetworkManager-gnome se eles ainda não foram removidos na etapa 3.

1. Não crie espaço de permuta no disco do sistema operacional.

    O agente Linux do Azure pode configurar automaticamente o espaço de permuta usando o disco de recurso local que é anexado à máquina virtual depois que a máquina virtual é provisionada no Azure. Observe que o disco de recurso local é um disco temporário e pode ser esvaziado se a máquina virtual for desprovisionada. Depois de instalar o agente Linux do Azure na etapa anterior, modifique os seguintes parâmetros no/etc/waagent.conf adequadamente:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Cancele o registro da assinatura (se necessário) executando o seguinte comando:

        # sudo subscription-manager unregister

1. Execute os seguintes comandos para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Clique em **ação** > **desligar** no Gerenciador do Hyper-V. Seu VHD do Linux agora está pronto para ser carregado no Azure.


### <a name="prepare-a-rhel-7-virtual-machine-from-hyper-v-manager"></a>Preparar uma máquina virtual RHEL 7 do Gerenciador do Hyper-V

1. No Gerenciador do Hyper-V, selecione a máquina virtual.

1. Clique em **conectar** para abrir uma janela de console para a máquina virtual.

1. Crie ou edite o arquivo de `/etc/sysconfig/network` e adicione o seguinte texto:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Crie ou edite o arquivo de `/etc/sysconfig/network-scripts/ifcfg-eth0` e adicione o seguinte texto:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

1. Verifique se o serviço de rede será iniciado no momento da inicialização executando o seguinte comando:

        # sudo systemctl enable network

1. Registre sua assinatura do Red Hat para habilitar a instalação de pacotes do repositório do RHEL executando o seguinte comando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. Modifique a linha de inicialização do kernel em sua configuração do grub para incluir parâmetros de kernel adicionais para o Azure. Para fazer essa modificação, abra `/etc/default/grub` em um editor de texto e edite o parâmetro `GRUB_CMDLINE_LINUX`. Por exemplo:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Isso também garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, o que pode auxiliar o suporte do Azure com problemas de depuração. Essa configuração também desativa as novas convenções de nomenclatura do RHEL 7 para NICs. Além disso, recomendamos que você remova os seguintes parâmetros:
   
        rhgb quiet crashkernel=auto
   
    A inicialização gráfica e silenciosa não são úteis em um ambiente de nuvem onde queremos que todos os logs sejam enviados para a porta serial. Você pode deixar a opção `crashkernel` configurada se desejar. Observe que esse parâmetro reduz a quantidade de memória disponível na máquina virtual em 128 MB ou mais, o que pode ser problemático em tamanhos menores de máquina virtual.

1. Depois de concluir a edição `/etc/default/grub`, execute o seguinte comando para recompilar a configuração do grub:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

1. Verifique se o servidor SSH está instalado e configurado para iniciar no momento da inicialização, que geralmente é o padrão. Modifique `/etc/ssh/sshd_config` para incluir a seguinte linha:

        ClientAliveInterval 180

1. O pacote WALinuxAgent, `WALinuxAgent-<version>`, foi enviado por push para o repositório extras do Red Hat. Habilite o repositório de extras executando o seguinte comando:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

1. Instale o agente Linux do Azure executando o seguinte comando:

        # sudo yum install WALinuxAgent

        # sudo systemctl enable waagent.service

1. Não crie espaço de permuta no disco do sistema operacional.

    O agente Linux do Azure pode configurar automaticamente o espaço de permuta usando o disco de recurso local que é anexado à máquina virtual depois que a máquina virtual é provisionada no Azure. Observe que o disco de recurso local é um disco temporário e poderá ser esvaziado se a máquina virtual for desprovisionada. Depois de instalar o agente Linux do Azure na etapa anterior, modifique os seguintes parâmetros no `/etc/waagent.conf` adequadamente:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Se você quiser cancelar o registro da assinatura, execute o seguinte comando:

        # sudo subscription-manager unregister

1. Execute os seguintes comandos para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Clique em **ação** > **desligar** no Gerenciador do Hyper-V. Seu VHD do Linux agora está pronto para ser carregado no Azure.


## <a name="prepare-a-red-hat-based-virtual-machine-from-kvm"></a>Preparar uma máquina virtual baseada em Red Hat do KVM
### <a name="prepare-a-rhel-6-virtual-machine-from-kvm"></a>Preparar uma máquina virtual RHEL 6 do KVM

1. Baixe a imagem KVM do RHEL 6 do site do Red Hat.

1. Defina uma senha raiz.

    Gere uma senha criptografada e copie a saída do comando:

        # openssl passwd -1 changeme

    Defina uma senha raiz com guestfish:
        
        # guestfish --rw -a <image-name>
        > <fs> run
        > <fs> list-filesystems
        > <fs> mount /dev/sda1 /
        > <fs> vi /etc/shadow
        > <fs> exit

   Alterar o segundo campo do usuário raiz de "!!" para a senha criptografada.

1. Crie uma máquina virtual no KVM a partir da imagem do qcow2. Defina o tipo de disco como **qcow2**e defina o modelo de dispositivo de interface de rede virtual como **virtio**. Em seguida, inicie a máquina virtual e entre como raiz.

1. Crie ou edite o arquivo de `/etc/sysconfig/network` e adicione o seguinte texto:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Crie ou edite o arquivo de `/etc/sysconfig/network-scripts/ifcfg-eth0` e adicione o seguinte texto:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

1. Mova (ou remova) as regras udev para evitar a geração de regras estáticas para a interface Ethernet. Essas regras causam problemas quando você clona uma máquina virtual no Azure ou no Hyper-V:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

1. Verifique se o serviço de rede será iniciado no momento da inicialização executando o seguinte comando:

        # chkconfig network on

1. Registre sua assinatura do Red Hat para habilitar a instalação de pacotes do repositório do RHEL executando o seguinte comando:

        # subscription-manager register --auto-attach --username=XXX --password=XXX

1. Modifique a linha de inicialização do kernel em sua configuração do grub para incluir parâmetros de kernel adicionais para o Azure. Para fazer essa configuração, abra `/boot/grub/menu.lst` em um editor de texto e verifique se o kernel padrão inclui os seguintes parâmetros:
    
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    
    Isso também garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, o que pode auxiliar o suporte do Azure com problemas de depuração.
    
    Além disso, recomendamos que você remova os seguintes parâmetros:
    
        rhgb quiet crashkernel=auto
    
    A inicialização gráfica e silenciosa não são úteis em um ambiente de nuvem onde queremos que todos os logs sejam enviados para a porta serial. Você pode deixar a opção `crashkernel` configurada se desejar. Observe que esse parâmetro reduz a quantidade de memória disponível na máquina virtual em 128 MB ou mais, o que pode ser problemático em tamanhos menores de máquina virtual.


1. Adicione os módulos do Hyper-V ao initramfs:  

    Edite `/etc/dracut.conf`e adicione o seguinte conteúdo:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Recriar initramfs:

        # dracut -f -v

1. Desinstalar Cloud-init:

        # yum remove cloud-init

1. Verifique se o servidor SSH está instalado e configurado para iniciar no momento da inicialização:

        # chkconfig sshd on

    Modifique sshd_config/etc/ssh/para incluir as seguintes linhas:

        PasswordAuthentication yes
        ClientAliveInterval 180

1. O pacote WALinuxAgent, `WALinuxAgent-<version>`, foi enviado por push para o repositório extras do Red Hat. Habilite o repositório de extras executando o seguinte comando:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

1. Instale o agente Linux do Azure executando o seguinte comando:

        # yum install WALinuxAgent

        # chkconfig waagent on

1. O agente Linux do Azure pode configurar automaticamente o espaço de permuta usando o disco de recurso local que é anexado à máquina virtual depois que a máquina virtual é provisionada no Azure. Observe que o disco de recurso local é um disco temporário e poderá ser esvaziado se a máquina virtual for desprovisionada. Depois de instalar o agente Linux do Azure na etapa anterior, modifique os seguintes parâmetros no **/etc/waagent.conf** adequadamente:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Cancele o registro da assinatura (se necessário) executando o seguinte comando:

        # subscription-manager unregister

1. Execute os seguintes comandos para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Desligue a máquina virtual no KVM.

1. Converta a imagem qcow2 no formato VHD.

> [!NOTE]
> Há um bug conhecido nas versões QEMU-img > = 2.2.1 que resulta em um VHD formatado incorretamente. O problema foi corrigido no QEMU 2,6. É recomendável usar QEMU-img 2.2.0 ou inferior ou atualizar para 2,6 ou superior. Referência: https://bugs.launchpad.net/qemu/+bug/1490611.
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

        
### <a name="prepare-a-rhel-7-virtual-machine-from-kvm"></a>Preparar uma máquina virtual RHEL 7 do KVM

1. Baixe a imagem KVM do RHEL 7 do site do Red Hat. Este procedimento usa o RHEL 7 como exemplo.

1. Defina uma senha raiz.

    Gere uma senha criptografada e copie a saída do comando:

        # openssl passwd -1 changeme

    Defina uma senha raiz com guestfish:

        # guestfish --rw -a <image-name>
        > <fs> run
        > <fs> list-filesystems
        > <fs> mount /dev/sda1 /
        > <fs> vi /etc/shadow
        > <fs> exit

   Alterar o segundo campo do usuário raiz de "!!" para a senha criptografada.

1. Crie uma máquina virtual no KVM a partir da imagem do qcow2. Defina o tipo de disco como **qcow2**e defina o modelo de dispositivo de interface de rede virtual como **virtio**. Em seguida, inicie a máquina virtual e entre como raiz.

1. Crie ou edite o arquivo de `/etc/sysconfig/network` e adicione o seguinte texto:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Crie ou edite o arquivo de `/etc/sysconfig/network-scripts/ifcfg-eth0` e adicione o seguinte texto:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

1. Verifique se o serviço de rede será iniciado no momento da inicialização executando o seguinte comando:

        # sudo systemctl enable network

1. Registre sua assinatura do Red Hat para habilitar a instalação de pacotes do repositório do RHEL executando o seguinte comando:

        # subscription-manager register --auto-attach --username=XXX --password=XXX

1. Modifique a linha de inicialização do kernel em sua configuração do grub para incluir parâmetros de kernel adicionais para o Azure. Para fazer essa configuração, abra `/etc/default/grub` em um editor de texto e edite o parâmetro `GRUB_CMDLINE_LINUX`. Por exemplo:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Esse comando também garante que todas as mensagens do console sejam enviadas para a primeira porta serial, o que pode auxiliar o suporte do Azure com problemas de depuração. O comando também desativa as novas convenções de nomenclatura do RHEL 7 para NICs. Além disso, recomendamos que você remova os seguintes parâmetros:
   
        rhgb quiet crashkernel=auto
   
    A inicialização gráfica e silenciosa não são úteis em um ambiente de nuvem onde queremos que todos os logs sejam enviados para a porta serial. Você pode deixar a opção `crashkernel` configurada se desejar. Observe que esse parâmetro reduz a quantidade de memória disponível na máquina virtual em 128 MB ou mais, o que pode ser problemático em tamanhos menores de máquina virtual.

1. Depois de concluir a edição `/etc/default/grub`, execute o seguinte comando para recompilar a configuração do grub:

        # grub2-mkconfig -o /boot/grub2/grub.cfg

1. Adicione os módulos do Hyper-V em initramfs.

    Editar `/etc/dracut.conf` e adicionar conteúdo:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Recriar initramfs:

        # dracut -f -v

1. Desinstalar Cloud-init:

        # yum remove cloud-init

1. Verifique se o servidor SSH está instalado e configurado para iniciar no momento da inicialização:

        # systemctl enable sshd

    Modifique sshd_config/etc/ssh/para incluir as seguintes linhas:

        PasswordAuthentication yes
        ClientAliveInterval 180

1. O pacote WALinuxAgent, `WALinuxAgent-<version>`, foi enviado por push para o repositório extras do Red Hat. Habilite o repositório de extras executando o seguinte comando:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

1. Instale o agente Linux do Azure executando o seguinte comando:

        # yum install WALinuxAgent

    Habilite o serviço waagent:

        # systemctl enable waagent.service

1. Não crie espaço de permuta no disco do sistema operacional.

    O agente Linux do Azure pode configurar automaticamente o espaço de permuta usando o disco de recurso local que é anexado à máquina virtual depois que a máquina virtual é provisionada no Azure. Observe que o disco de recurso local é um disco temporário e poderá ser esvaziado se a máquina virtual for desprovisionada. Depois de instalar o agente Linux do Azure na etapa anterior, modifique os seguintes parâmetros no `/etc/waagent.conf` adequadamente:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Cancele o registro da assinatura (se necessário) executando o seguinte comando:

        # subscription-manager unregister

1. Execute os seguintes comandos para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Desligue a máquina virtual no KVM.

1. Converta a imagem qcow2 no formato VHD.

> [!NOTE]
> Há um bug conhecido nas versões QEMU-img > = 2.2.1 que resulta em um VHD formatado incorretamente. O problema foi corrigido no QEMU 2,6. É recomendável usar QEMU-img 2.2.0 ou inferior ou atualizar para 2,6 ou superior. Referência: https://bugs.launchpad.net/qemu/+bug/1490611.
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


## <a name="prepare-a-red-hat-based-virtual-machine-from-vmware"></a>Preparar uma máquina virtual baseada em Red Hat do VMware
### <a name="prerequisites"></a>Pré-requisitos
Esta seção pressupõe que você já tenha instalado uma máquina virtual RHEL no VMware. Para obter detalhes sobre como instalar um sistema operacional no VMware, consulte [Guia de instalação do sistema operacional convidado VMware](https://partnerweb.vmware.com/GOSIG/home.html).

* Ao instalar o sistema operacional Linux, recomendamos que você use partições padrão em vez de LVM, que geralmente é o padrão para muitas instalações. Isso evitará conflitos de nome LVM com a máquina virtual clonada, especialmente se um disco do sistema operacional precisar ser anexado a outra máquina virtual para solução de problemas. O LVM ou o RAID podem ser usados em discos de dados, se preferíveis.
* Não configure uma partição de permuta no disco do sistema operacional. Você pode configurar o agente do Linux para criar um arquivo de permuta no disco de recursos temporário. Você pode encontrar mais informações sobre isso nas etapas a seguir.
* Ao criar o disco rígido virtual, selecione **armazenar disco virtual como um único arquivo**.

### <a name="prepare-a-rhel-6-virtual-machine-from-vmware"></a>Preparar uma máquina virtual RHEL 6 do VMware
1. No RHEL 6, o NetworkManager pode interferir no agente Linux do Azure. Desinstale este pacote executando o seguinte comando:
   
        # sudo rpm -e --nodeps NetworkManager

1. Crie um arquivo chamado **Network** no diretório/etc/sysconfig/que contém o seguinte texto:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Crie ou edite o arquivo de `/etc/sysconfig/network-scripts/ifcfg-eth0` e adicione o seguinte texto:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

1. Mova (ou remova) as regras udev para evitar a geração de regras estáticas para a interface Ethernet. Essas regras causam problemas quando você clona uma máquina virtual no Azure ou no Hyper-V:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

1. Verifique se o serviço de rede será iniciado no momento da inicialização executando o seguinte comando:

        # sudo chkconfig network on

1. Registre sua assinatura do Red Hat para habilitar a instalação de pacotes do repositório do RHEL executando o seguinte comando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. O pacote WALinuxAgent, `WALinuxAgent-<version>`, foi enviado por push para o repositório extras do Red Hat. Habilite o repositório de extras executando o seguinte comando:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

1. Modifique a linha de inicialização do kernel em sua configuração do grub para incluir parâmetros de kernel adicionais para o Azure. Para fazer isso, abra `/etc/default/grub` em um editor de texto e edite o parâmetro `GRUB_CMDLINE_LINUX`. Por exemplo:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0"
   
   Isso também garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, o que pode auxiliar o suporte do Azure com problemas de depuração. Além disso, recomendamos que você remova os seguintes parâmetros:
   
        rhgb quiet crashkernel=auto
   
    A inicialização gráfica e silenciosa não são úteis em um ambiente de nuvem onde queremos que todos os logs sejam enviados para a porta serial. Você pode deixar a opção `crashkernel` configurada se desejar. Observe que esse parâmetro reduz a quantidade de memória disponível na máquina virtual em 128 MB ou mais, o que pode ser problemático em tamanhos menores de máquina virtual.

1. Adicione os módulos do Hyper-V ao initramfs:

    Edite `/etc/dracut.conf`e adicione o seguinte conteúdo:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Recriar initramfs:

        # dracut -f -v

1. Verifique se o servidor SSH está instalado e configurado para iniciar no momento da inicialização, que geralmente é o padrão. Modifique `/etc/ssh/sshd_config` para incluir a seguinte linha:

    ClientAliveInterval 180

1. Instale o agente Linux do Azure executando o seguinte comando:

        # sudo yum install WALinuxAgent

        # sudo chkconfig waagent on

1. Não crie espaço de permuta no disco do sistema operacional.

    O agente Linux do Azure pode configurar automaticamente o espaço de permuta usando o disco de recurso local que é anexado à máquina virtual depois que a máquina virtual é provisionada no Azure. Observe que o disco de recurso local é um disco temporário e poderá ser esvaziado se a máquina virtual for desprovisionada. Depois de instalar o agente Linux do Azure na etapa anterior, modifique os seguintes parâmetros no `/etc/waagent.conf` adequadamente:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Cancele o registro da assinatura (se necessário) executando o seguinte comando:

        # sudo subscription-manager unregister

1. Execute os seguintes comandos para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Desligue a máquina virtual e converta o arquivo VMDK em um arquivo. vhd.

> [!NOTE]
> Há um bug conhecido nas versões QEMU-img > = 2.2.1 que resulta em um VHD formatado incorretamente. O problema foi corrigido no QEMU 2,6. É recomendável usar QEMU-img 2.2.0 ou inferior ou atualizar para 2,6 ou superior. Referência: https://bugs.launchpad.net/qemu/+bug/1490611.
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


### <a name="prepare-a-rhel-7-virtual-machine-from-vmware"></a>Preparar uma máquina virtual RHEL 7 do VMware
1. Crie ou edite o arquivo de `/etc/sysconfig/network` e adicione o seguinte texto:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Crie ou edite o arquivo de `/etc/sysconfig/network-scripts/ifcfg-eth0` e adicione o seguinte texto:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

1. Verifique se o serviço de rede será iniciado no momento da inicialização executando o seguinte comando:

        # sudo systemctl enable network

1. Registre sua assinatura do Red Hat para habilitar a instalação de pacotes do repositório do RHEL executando o seguinte comando:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. Modifique a linha de inicialização do kernel em sua configuração do grub para incluir parâmetros de kernel adicionais para o Azure. Para fazer essa modificação, abra `/etc/default/grub` em um editor de texto e edite o parâmetro `GRUB_CMDLINE_LINUX`. Por exemplo:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Essa configuração também garante que todas as mensagens do console sejam enviadas para a primeira porta serial, o que pode auxiliar o suporte do Azure com problemas de depuração. Ele também desativa as novas convenções de nomenclatura do RHEL 7 para NICs. Além disso, recomendamos que você remova os seguintes parâmetros:
   
        rhgb quiet crashkernel=auto
   
    A inicialização gráfica e silenciosa não são úteis em um ambiente de nuvem onde queremos que todos os logs sejam enviados para a porta serial. Você pode deixar a opção `crashkernel` configurada se desejar. Observe que esse parâmetro reduz a quantidade de memória disponível na máquina virtual em 128 MB ou mais, o que pode ser problemático em tamanhos menores de máquina virtual.

1. Depois de concluir a edição `/etc/default/grub`, execute o seguinte comando para recompilar a configuração do grub:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

1. Adicione os módulos do Hyper-V ao initramfs.

    Editar `/etc/dracut.conf`, adicionar conteúdo:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Recriar initramfs:

        # dracut -f -v

1. Verifique se o servidor SSH está instalado e configurado para iniciar no momento da inicialização. Essa configuração geralmente é o padrão. Modifique `/etc/ssh/sshd_config` para incluir a seguinte linha:

        ClientAliveInterval 180

1. O pacote WALinuxAgent, `WALinuxAgent-<version>`, foi enviado por push para o repositório extras do Red Hat. Habilite o repositório de extras executando o seguinte comando:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

1. Instale o agente Linux do Azure executando o seguinte comando:

        # sudo yum install WALinuxAgent

        # sudo systemctl enable waagent.service

1. Não crie espaço de permuta no disco do sistema operacional.

    O agente Linux do Azure pode configurar automaticamente o espaço de permuta usando o disco de recurso local que é anexado à máquina virtual depois que a máquina virtual é provisionada no Azure. Observe que o disco de recurso local é um disco temporário e poderá ser esvaziado se a máquina virtual for desprovisionada. Depois de instalar o agente Linux do Azure na etapa anterior, modifique os seguintes parâmetros no `/etc/waagent.conf` adequadamente:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Se você quiser cancelar o registro da assinatura, execute o seguinte comando:

        # sudo subscription-manager unregister

1. Execute os seguintes comandos para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Desligue a máquina virtual e converta o arquivo VMDK no formato VHD.

> [!NOTE]
> Há um bug conhecido nas versões QEMU-img > = 2.2.1 que resulta em um VHD formatado incorretamente. O problema foi corrigido no QEMU 2,6. É recomendável usar QEMU-img 2.2.0 ou inferior ou atualizar para 2,6 ou superior. Referência: https://bugs.launchpad.net/qemu/+bug/1490611.
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


## <a name="prepare-a-red-hat-based-virtual-machine-from-an-iso-by-using-a-kickstart-file-automatically"></a>Preparar uma máquina virtual baseada em Red Hat de um ISO usando um arquivo início rápido automaticamente
### <a name="prepare-a-rhel-7-virtual-machine-from-a-kickstart-file"></a>Preparar uma máquina virtual RHEL 7 de um arquivo início rápido

1.  Crie um arquivo início rápido que inclua o conteúdo a seguir e salve o arquivo. Para obter detalhes sobre a instalação do início rápido, consulte o [Guia de instalação do início rápido](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html).

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

        # Deprovision and prepare for Azure if you are creating a generalized image
        waagent -force -deprovision

        %end

1. Coloque o arquivo início rápido em que o sistema de instalação pode acessá-lo.

1. No Gerenciador do Hyper-V, crie uma nova máquina virtual. Na página **conectar disco rígido virtual** , selecione **anexar um disco rígido virtual mais tarde**e conclua o assistente de nova máquina virtual.

1. Abra as configurações da máquina virtual:

    a.  Anexe um novo disco rígido virtual à máquina virtual. Certifique-se de selecionar **formato VHD** e **tamanho fixo**.

    b.  Anexe o ISO de instalação à unidade de DVD.

    c.  Defina o BIOS para inicializar do CD.

1. Inicie a máquina virtual. Quando o guia de instalação for exibido, pressione **Tab** para configurar as opções de inicialização.

1. Insira `inst.ks=<the location of the kickstart file>` no final das opções de inicialização e pressione **Enter**.

1. Aguarde a conclusão da instalação. Quando tiver terminado, a máquina virtual será desligada automaticamente. Seu VHD do Linux agora está pronto para ser carregado no Azure.

## <a name="known-issues"></a>Problemas conhecidos
### <a name="the-hyper-v-driver-could-not-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>Não foi possível incluir o driver do Hyper-V no disco RAM inicial ao usar um hipervisor não Hyper-V

Em alguns casos, os instaladores do Linux podem não incluir os drivers do Hyper-V no disco RAM inicial (initrd ou initramfs), a menos que o Linux detecte que está sendo executado em um ambiente Hyper-V.

Quando você estiver usando um sistema de virtualização diferente (ou seja, VirtualBox, Xen, etc.) para preparar sua imagem do Linux, talvez seja necessário recompilar a initrd para garantir que pelo menos os módulos de kernel hv_vmbus e hv_storvsc estejam disponíveis no disco RAM inicial. Esse é um problema conhecido pelo menos em sistemas baseados na distribuição Red Hat upstream.

Para resolver esse problema, adicione os módulos do Hyper-V ao initramfs e recrie-os:

Edite `/etc/dracut.conf`e adicione o seguinte conteúdo:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

Recriar initramfs:

        # dracut -f -v

Para obter mais detalhes, consulte as informações sobre a [recriação de initramfs](https://access.redhat.com/solutions/1958).

## <a name="next-steps"></a>Passos seguintes
Agora você está pronto para usar seu Red Hat Enterprise Linux disco rígido virtual para criar novas máquinas virtuais no Azure. Se esta for a primeira vez que você está carregando o arquivo. vhd no Azure, consulte [criar uma VM do Linux a partir de um disco personalizado](upload-vhd.md#option-1-upload-a-vhd).

Para obter mais detalhes sobre os hipervisores que são certificados para executar Red Hat Enterprise Linux, consulte [o site da Red Hat](https://access.redhat.com/certified-hypervisors).
