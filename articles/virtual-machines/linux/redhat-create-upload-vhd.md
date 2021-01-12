---
title: Crie e carrele um Red Hat Enterprise Linux VHD para uso em Azure
description: Aprenda a criar e carregar um disco rígido virtual Azure (VHD) que contenha um sistema operativo Red Hat Linux.
author: danielsollondon
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: how-to
ms.date: 12/01/2020
ms.author: danis
ms.openlocfilehash: 751d447c164c602b9b1524d4945d61556bf71932
ms.sourcegitcommit: 02b1179dff399c1aa3210b5b73bf805791d45ca2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98127299"
---
# <a name="prepare-a-red-hat-based-virtual-machine-for-azure"></a>Prepare a Red Hat-based virtual machine for Azure (Preparar uma máquina virtual baseada em Red Hat para o Azure)
Neste artigo, você aprenderá a preparar uma máquina virtual Red Hat Enterprise Linux (RHEL) para uso em Azure. As versões do RHEL que estão abrangidas por este artigo são 6.7+ e 7.1+. Os hipervisores para a preparação que estão cobertos neste artigo são Hyper-V, máquina virtual baseada em kernel (KVM) e VMware. Para obter mais informações sobre os requisitos de elegibilidade para participar no programa Red Hat's Cloud Access, consulte o [site do Red Hat's Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) e o Running [RHEL no Azure](https://access.redhat.com/ecosystem/ccsp/microsoft-azure). Para formas de automatizar a construção de imagens RHEL consulte o [Azure Image Builder](./image-builder-overview.md).

## <a name="hyper-v-manager"></a>Gestor de Hiper-V

Esta secção mostra-lhe como preparar uma máquina virtual [RHEL 6](#rhel-6-using-hyper-v-manager) ou [RHEL 7](#rhel-7-using-hyper-v-manager) utilizando o Hyper-V Manager.

### <a name="prerequisites"></a>Pré-requisitos
Esta secção pressupõe que já obteve um ficheiro ISO do site do Red Hat e instalou a imagem RHEL num disco rígido virtual (VHD). Para obter mais detalhes sobre como utilizar o Hyper-V Manager para instalar uma imagem do sistema operativo, consulte [instalar a Função Hiper-V e configurar uma Máquina Virtual](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

**Notas de instalação DARL**

* O Azure não suporta o formato VHDX. O Azure suporta apenas VHD fixo. Pode utilizar o Hyper-V Manager para converter o disco no formato VHD, ou pode utilizar o cmdlet converte-vhd. Se utilizar a VirtualBox, selecione **tamanho fixo** em oposição ao padrão de opção atribuída dinamicamente quando criar o disco.
* A azure suporta máquinas virtuais Gen1 (BOTA BIOS) & Gen2 (bota UEFI).
* O tamanho máximo permitido para o VHD é de 1.023 GB.
* O Gestor de Volume Lógico (LVM) é suportado e pode ser utilizado no disco de SISTEMA ou discos de dados em máquinas virtuais Azure. No entanto, em geral, recomenda-se a utilização de divisórias padrão no disco de so em vez de LVM. Esta prática evitará conflitos de nome LVM com máquinas virtuais clonadas, especialmente se alguma vez precisar de anexar um disco do sistema operativo a outra máquina virtual idêntica para a resolução de problemas. Consulte também a documentação [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) e [RAID.](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* **É necessário suporte ao Kernel para a montagem de sistemas de ficheiros Universal Disk Format (UDF).** No início da boot on Azure, o mídia formatada pelo UDF que está ligado ao hóspede passa a configuração de provisionamento para a máquina virtual Linux. O Agente Azure Linux deve ser capaz de montar o sistema de ficheiros UDF para ler a sua configuração e providenciar a máquina virtual, sem isso, o provisionamento falhará!
* Não configuure uma partição de troca no disco do sistema operativo. Mais informações sobre isso podem ser encontradas nos seguintes passos.

* Todos os VHDs em Azure devem ter um tamanho virtual alinhado a 1MB. Ao converter de um disco cru para VHD deve certificar-se de que o tamanho do disco bruto é um múltiplo de 1MB antes da conversão. Mais detalhes podem ser encontrados nos degraus abaixo. Consulte também [as notas de instalação do Linux](create-upload-generic.md#general-linux-installation-notes) para obter mais informações.

### <a name="rhel-6-using-hyper-v-manager"></a>RHEL 6 usando Hyper-V Manager

1. No Hyper-V Manager, selecione a máquina virtual.

1. Clique **em Connect** para abrir uma janela de consola para a máquina virtual.

1. No RHEL 6, o NetworkManager pode interferir com o agente Azure Linux. Desinstale este pacote executando o seguinte comando:

    ```console
    # sudo rpm -e --nodeps NetworkManager
    ```

1. Criar ou editar o `/etc/sysconfig/network` ficheiro e adicionar o seguinte texto:

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Criar ou editar o `/etc/sysconfig/network-scripts/ifcfg-eth0` ficheiro e adicionar o seguinte texto:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

1. Mova (ou remova) as regras udev para evitar gerar regras estáticas para a interface Ethernet. Estas regras causam problemas quando clona uma máquina virtual no Microsoft Azure ou Hiper-V:

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

    # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

1. Certifique-se de que o serviço de rede começará no tempo de arranque executando o seguinte comando:

    ```console
    # sudo chkconfig network on
    ```

1. Registe a sua assinatura Red Hat para permitir a instalação de pacotes a partir do repositório RHEL executando o seguinte comando:

    ```console
    # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. O pacote WALinuxAgent, `WALinuxAgent-<version>` foi empurrado para o repositório extras do Chapéu Vermelho. Ativar o repositório extra executando o seguinte comando:

    ```console
    # subscription-manager repos --enable=rhel-6-server-extras-rpms
    ```

1. Modifique a linha de arranque do núcleo na sua configuração de larva para incluir parâmetros adicionais de kernel para Azure. Para fazer esta modificação, abra `/boot/grub/menu.lst` num editor de texto e certifique-se de que o núcleo padrão inclui os seguintes parâmetros:

    ```config-grub
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

    Isto também irá garantir que todas as mensagens de consola são enviadas para a primeira porta em série, que pode ajudar o suporte do Azure com problemas de depuração.
    
    Além disso, recomendamos que remova os seguintes parâmetros:

    ```config-grub
    rhgb quiet crashkernel=auto
    ```
    
    A bota gráfica e silenciosa não é útil num ambiente em nuvem onde queremos que todos os registos sejam enviados para a porta em série.  Pode deixar a `crashkernel` opção configurada se desejar. Note que este parâmetro reduz a quantidade de memória disponível na máquina virtual em 128 MB ou mais. Esta configuração pode ser problemática em tamanhos de máquinas virtuais menores.


1. Certifique-se de que o servidor secure shell (SSH) está instalado e configurado para começar na hora do arranque, que normalmente é o padrão. Modificar /etc/ssh/sshd_config para incluir a seguinte linha:

    ```config
    ClientAliveInterval 180
    ```

1. Instale o Agente Azure Linux executando o seguinte comando:

    ```console
    # sudo yum install WALinuxAgent

    # sudo chkconfig waagent on
    ```

    A instalação do pacote WALinuxAgent remove os pacotes NetworkManager e NetworkManager-gnome se ainda não tiverem sido removidos no passo 3.

1. Não crie espaço de troca no disco do sistema operativo.

    O Agente Azure Linux pode configurar automaticamente o espaço de troca utilizando o disco de recursos local que é ligado à máquina virtual após a aprovisionamento da máquina virtual no Azure. Note que o disco de recursos local é um disco temporário e que pode ser esvaziado se a máquina virtual for desprovisionada. Depois de instalar o Agente Azure Linux no passo anterior, modifique os seguintes parâmetros em /etc/waagent.conf adequadamente:

    ```config-cong
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

1. Desregralar a subscrição (se necessário) executando o seguinte comando:

    ```console
    # sudo subscription-manager unregister
    ```

1. Executar os seguintes comandos para desprovisionar a máquina virtual e prepará-la para provisão em Azure:

    ```console
    # Note: if you are migrating a specific virtual machine and do not wish to create a generalized image,
    # skip the deprovision step
    # sudo waagent -force -deprovision

    # export HISTSIZE=0

    # logout
    ```

1. Clique em **Ação**  >  **Desligada** no Gestor De Hiper-V. O seu VHD Linux está agora pronto para ser enviado para Azure.


### <a name="rhel-7-using-hyper-v-manager"></a>RHEL 7 usando Hyper-V Manager

1. No Hyper-V Manager, selecione a máquina virtual.

1. Clique **em Connect** para abrir uma janela de consola para a máquina virtual.

1. Criar ou editar o `/etc/sysconfig/network` ficheiro e adicionar o seguinte texto:

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Criar ou editar o `/etc/sysconfig/network-scripts/ifcfg-eth0` ficheiro e adicionar o seguinte texto:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    PERSISTENT_DHCLIENT=yes
    NM_CONTROLLED=yes
    ```

1. Certifique-se de que o serviço de rede começará no tempo de arranque executando o seguinte comando:

    ```console
    # sudo systemctl enable network
    ```

1. Registe a sua assinatura Red Hat para permitir a instalação de pacotes a partir do repositório RHEL executando o seguinte comando:

    ```console
    # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Modifique a linha de arranque do núcleo na sua configuração de larva para incluir parâmetros adicionais de kernel para Azure. Para fazer esta modificação, abra `/etc/default/grub` num editor de texto e edite o `GRUB_CMDLINE_LINUX` parâmetro. Por exemplo:

    
    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 earlyprintk=ttyS0 net.ifnames=0"
    GRUB_TERMINAL_OUTPUT="serial console"
    GRUB_SERIAL_COMMAND="serial --speed=115200 --unit=0 --word=8 --parity=no --stop=1
    ```
   
    Isto também irá garantir que todas as mensagens de consola são enviadas para a primeira porta em série e permitir a interação com a consola em série, que pode ajudar o suporte do Azure com problemas de depuração. Esta configuração também desliga as novas convenções de nomeação RHEL 7 para NICs.

    ```config
    rhgb quiet crashkernel=auto
    ```
   
    A bota gráfica e silenciosa não é útil num ambiente em nuvem onde queremos que todos os registos sejam enviados para a porta em série. Pode deixar a `crashkernel` opção configurada se desejar. Note que este parâmetro reduz a quantidade de memória disponível na máquina virtual em 128 MB ou mais, o que pode ser problemático em tamanhos de máquinas virtuais menores.

1. Depois de ter terminado a `/etc/default/grub` edição, executar o seguinte comando para reconstruir a configuração da larva:

    ```console
    # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```
    > [!NOTE]
    > Se o upload de um UEFI ativar vM, o comando para atualizar a comida é `grub2-mkconfig -o /boot/efi/EFI/redhat/grub.cfg` .

1. Certifique-se de que o servidor SSH está instalado e configurado para iniciar na hora do arranque, que normalmente é o padrão. Modificar `/etc/ssh/sshd_config` para incluir a seguinte linha:

    ```config
    ClientAliveInterval 180
    ```

1. O pacote WALinuxAgent, `WALinuxAgent-<version>` foi empurrado para o repositório extras do Chapéu Vermelho. Ativar o repositório extra executando o seguinte comando:

    ```console
    # subscription-manager repos --enable=rhel-7-server-extras-rpms
    ```

1. Instale o Agente Azure Linux, cloud-init e outros utilitários necessários executando o seguinte comando:

    ```console
    # sudo yum install -y WALinuxAgent cloud-init cloud-utils-growpart gdisk hyperv-daemons

    # sudo systemctl enable waagent.service
    # sudo systemctl enable cloud-init.service
    ```

1. Configure a inição de nuvem para lidar com o provisionamento:

    1. Configure waagent para cloud-init:

    ```console
    sed -i 's/Provisioning.Agent=auto/Provisioning.Agent=cloud-init/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
    ```
    > [!NOTE]
    > Se estiver a migrar uma máquina virtual específica e não pretender criar uma imagem generalizada, `Provisioning.Agent=disabled` coloque-a no `/etc/waagent.conf` config.
    
    1. Suportes de configuração:

    ```console
    echo "Adding mounts and disk_setup to init stage"
    sed -i '/ - mounts/d' /etc/cloud/cloud.cfg
    sed -i '/ - disk_setup/d' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - mounts' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - disk_setup' /etc/cloud/cloud.cfg
    ```
    
    1. Configurar fonte de dados Azure:

    ```console
    echo "Allow only Azure datasource, disable fetching network setting via IMDS"
    cat > /etc/cloud/cloud.cfg.d/91-azure_datasource.cfg <<EOF
    datasource_list: [ Azure ]
    datasource:
    Azure:
        apply_network_config: False
    EOF
    ```

    1. Se configurado, remova o ficheiro de swap existente:

    ```console
    if [[ -f /mnt/resource/swapfile ]]; then
    echo "Removing swapfile" #RHEL uses a swapfile by defaul
    swapoff /mnt/resource/swapfile
    rm /mnt/resource/swapfile -f
    fi
    ```
    1. Configure a madeira de nuvem::
    ```console
    echo "Add console log file"
    cat >> /etc/cloud/cloud.cfg.d/05_logging.cfg <<EOF

    # This tells cloud-init to redirect its stdout and stderr to
    # 'tee -a /var/log/cloud-init-output.log' so the user can see output
    # there without needing to look on the console.
    output: {all: '| tee -a /var/log/cloud-init-output.log'}
    EOF

    ```

1. Configuração de troca Não crie espaço de troca no disco do sistema operativo.

    Anteriormente, o Agente Azure Linux foi utilizado automaticamente para configurar o espaço de troca utilizando o disco de recursos local que é ligado à máquina virtual após a colocação da máquina virtual no Azure. No entanto, isto é agora tratado por cloud-init, **não deve** utilizar o Agente Linux para formatar o disco de recursos criar o ficheiro swap, modificar os seguintes parâmetros de `/etc/waagent.conf` forma adequada:

    ```console
    ResourceDisk.Format=n
    ResourceDisk.EnableSwap=n
    ```

    Se quiser montar, formatar e criar troca, pode:
    * Passe isto como um config nuvem-init cada vez que você criar um VM
    * Utilize uma diretiva em nuvem cozida na imagem que o fará sempre que o VM for criado:

        ```console
        cat > /etc/cloud/cloud.cfg.d/00-azure-swap.cfg << EOF
        #cloud-config
        # Generated by Azure cloud image build
        disk_setup:
          ephemeral0:
            table_type: mbr
            layout: [66, [33, 82]]
            overwrite: True
        fs_setup:
          - device: ephemeral0.1
            filesystem: ext4
          - device: ephemeral0.2
            filesystem: swap
        mounts:
          - ["ephemeral0.1", "/mnt"]
          - ["ephemeral0.2", "none", "swap", "sw", "0", "0"]
        EOF
        ```
1. Se pretender não registar a subscrição, execute o seguinte comando:

    ```console
    # sudo subscription-manager unregister
    ```

1. Deprovisionamento

    Executar os seguintes comandos para desprovisionar a máquina virtual e prepará-la para provisão em Azure:

    > [!CAUTION]
    > Se estiver a migrar uma máquina virtual específica e não pretender criar uma imagem generalizada, ignore o passo de desprovisionamento. Executar o comando `waagent -force -deprovision` tornará a máquina de origem inutilizável, este passo destina-se apenas a criar uma imagem generalizada.
    ```console
    # sudo waagent -force -deprovision

    # export HISTSIZE=0

    # logout
    ```
    

1. Clique em **Ação**  >  **Desligada** no Gestor De Hiper-V. O seu VHD Linux está agora pronto para ser enviado para Azure.

### <a name="rhel-8-using-hyper-v-manager"></a>RHEL 8 usando Hyper-V Manager

1. No Hyper-V Manager, selecione a máquina virtual.

1. Clique **em Connect** para abrir uma janela de consola para a máquina virtual.

1. Certifique-se de que o serviço Network Manager começará na hora do arranque executando o seguinte comando:

    ```console
    # sudo systemctl enable NetworkManager.service
    ```

1. Configure a interface de rede para iniciar automaticamente no arranque e utilizar o DHCP:

    ```console
    # nmcli con mod eth0 connection.autoconnect yes ipv4.method auto
    ```


1. Registe a sua assinatura Red Hat para permitir a instalação de pacotes a partir do repositório RHEL executando o seguinte comando:

    ```console
    # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Modifique a linha de arranque do núcleo na sua configuração de larva para incluir parâmetros adicionais de kernel para Azure e ativar a consola em série. 

    1. Remova os parâmetros atuais do GRUB:
    ```console
    # grub2-editenv - unset kernelopts
    ```

    1. Editar `/etc/default/grub` num editor de texto e adicionar os seguintes paramters:

    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 earlyprintk=ttyS0 net.ifnames=0"
    GRUB_TERMINAL_OUTPUT="serial console"
    GRUB_SERIAL_COMMAND="serial --speed=115200 --unit=0 --word=8 --parity=no --stop=1"
    ```
   
   Isto também irá garantir que todas as mensagens de consola são enviadas para a primeira porta em série e permitir a interação com a consola em série, que pode ajudar o suporte do Azure com problemas de depuração. Esta configuração também desliga as novas convenções de nomeação RHEL 7 para NICs.
   
   1. Além disso, recomendamos que remova os seguintes parâmetros:

    ```config
    rhgb quiet crashkernel=auto
    ```
   
    A bota gráfica e silenciosa não é útil num ambiente em nuvem onde queremos que todos os registos sejam enviados para a porta em série. Pode deixar a `crashkernel` opção configurada se desejar. Note que este parâmetro reduz a quantidade de memória disponível na máquina virtual em 128 MB ou mais, o que pode ser problemático em tamanhos de máquinas virtuais menores.

1. Depois de ter terminado a `/etc/default/grub` edição, executar o seguinte comando para reconstruir a configuração da larva:

    ```console
    # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```
    E para um VM ativado pela UEFI, executar o seguinte comando:

    ```console
    # sudo grub2-mkconfig -o /boot/efi/EFI/redhat/grub.cfg
    ```

1. Certifique-se de que o servidor SSH está instalado e configurado para iniciar na hora do arranque, que normalmente é o padrão. Modificar `/etc/ssh/sshd_config` para incluir a seguinte linha:

    ```config
    ClientAliveInterval 180
    ```

1. Instale o Agente Azure Linux, cloud-init e outros utilitários necessários executando o seguinte comando:

    ```console
    # sudo yum install -y WALinuxAgent cloud-init cloud-utils-growpart gdisk hyperv-daemons

    # sudo systemctl enable waagent.service
    # sudo systemctl enable cloud-init.service
    ```

1. Configure a inição de nuvem para lidar com o provisionamento:

    1. Configure waagent para cloud-init:

    ```console
    sed -i 's/Provisioning.Agent=auto/Provisioning.Agent=cloud-init/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
    ```
    > [!NOTE]
    > Se estiver a migrar uma máquina virtual específica e não pretender criar uma imagem generalizada, `Provisioning.Agent=disabled` coloque-a no `/etc/waagent.conf` config.
    
    1. Suportes de configuração:

    ```console
    echo "Adding mounts and disk_setup to init stage"
    sed -i '/ - mounts/d' /etc/cloud/cloud.cfg
    sed -i '/ - disk_setup/d' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - mounts' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - disk_setup' /etc/cloud/cloud.cfg
    ```
    
    1. Configurar fonte de dados Azure:

    ```console
    echo "Allow only Azure datasource, disable fetching network setting via IMDS"
    cat > /etc/cloud/cloud.cfg.d/91-azure_datasource.cfg <<EOF
    datasource_list: [ Azure ]
    datasource:
    Azure:
        apply_network_config: False
    EOF
    ```

    1. Se configurado, remova o ficheiro de swap existente:

    ```console
    if [[ -f /mnt/resource/swapfile ]]; then
    echo "Removing swapfile" #RHEL uses a swapfile by defaul
    swapoff /mnt/resource/swapfile
    rm /mnt/resource/swapfile -f
    fi
    ```
    1. Configure a madeira de nuvem::
    ```console
    echo "Add console log file"
    cat >> /etc/cloud/cloud.cfg.d/05_logging.cfg <<EOF

    # This tells cloud-init to redirect its stdout and stderr to
    # 'tee -a /var/log/cloud-init-output.log' so the user can see output
    # there without needing to look on the console.
    output: {all: '| tee -a /var/log/cloud-init-output.log'}
    EOF

    ```

1. Configuração de troca Não crie espaço de troca no disco do sistema operativo.

    Anteriormente, o Agente Azure Linux foi utilizado automaticamente para configurar o espaço de troca utilizando o disco de recursos local que é ligado à máquina virtual após a colocação da máquina virtual no Azure. No entanto, isto é agora tratado por cloud-init, **não deve** utilizar o Agente Linux para formatar o disco de recursos criar o ficheiro swap, modificar os seguintes parâmetros de `/etc/waagent.conf` forma adequada:

    ```console
    ResourceDisk.Format=n
    ResourceDisk.EnableSwap=n
    ```

    Se quiser montar, formatar e criar troca, pode:
    * Passe isto como um config nuvem-init cada vez que você criar um VM
    * Utilize uma diretiva em nuvem cozida na imagem que o fará sempre que o VM for criado:

        ```console
        cat > /etc/cloud/cloud.cfg.d/00-azure-swap.cfg << EOF
        #cloud-config
        # Generated by Azure cloud image build
        disk_setup:
          ephemeral0:
            table_type: mbr
            layout: [66, [33, 82]]
            overwrite: True
        fs_setup:
          - device: ephemeral0.1
            filesystem: ext4
          - device: ephemeral0.2
            filesystem: swap
        mounts:
          - ["ephemeral0.1", "/mnt"]
          - ["ephemeral0.2", "none", "swap", "sw", "0", "0"]
        EOF
        ```
1. Se pretender não registar a subscrição, execute o seguinte comando:

    ```console
    # sudo subscription-manager unregister
    ```

1. Deprovisionamento

    Executar os seguintes comandos para desprovisionar a máquina virtual e prepará-la para provisão em Azure:

    ```console
    # sudo waagent -force -deprovision

    # export HISTSIZE=0

    # logout
    ```
    > [!CAUTION]
    > Se estiver a migrar uma máquina virtual específica e não pretender criar uma imagem generalizada, ignore o passo de desprovisionamento. Executar o comando `waagent -force -deprovision` tornará a máquina de origem inutilizável, este passo destina-se apenas a criar uma imagem generalizada.


1. Clique em **Ação**  >  **Desligada** no Gestor De Hiper-V. O seu VHD Linux está agora pronto para ser enviado para Azure.


## <a name="kvm"></a>KVM

Esta secção mostra-lhe como utilizar o KVM para preparar um [distro RHEL 6](#rhel-6-using-kvm) ou [RHEL 7](#rhel-7-using-kvm) para fazer o upload para Azure. 

### <a name="rhel-6-using-kvm"></a>RHEL 6 utilizando o KVM

1. Descarregue a imagem KVM do RHEL 6 a partir do site do Red Hat.

1. Desateia uma senha de raiz.

    Gere uma palavra-passe encriptada e copie a saída do comando:

    ```console
    # openssl passwd -1 changeme
    ```

    Desapedaça uma senha de raiz com o peixe-hóspede:

    ```console
    # guestfish --rw -a <image-name>
    > <fs> run
    > <fs> list-filesystems
    > <fs> mount /dev/sda1 /
    > <fs> vi /etc/shadow
    > <fs> exit
    ```

   Mude o segundo campo do utilizador raiz de "!!" para a senha encriptada.

1. Crie uma máquina virtual em KVM a partir da imagem qcow2. Desa estale o tipo de disco para **qcow2**, e desate o modelo de dispositivo de interface de rede virtual para **virtio**. Em seguida, ligue a máquina virtual e inscreva-se como raiz.

1. Criar ou editar o `/etc/sysconfig/network` ficheiro e adicionar o seguinte texto:

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Criar ou editar o `/etc/sysconfig/network-scripts/ifcfg-eth0` ficheiro e adicionar o seguinte texto:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

1. Mova (ou remova) as regras udev para evitar gerar regras estáticas para a interface Ethernet. Estas regras causam problemas quando clona uma máquina virtual em Azure ou Hiper-V:

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

    # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

1. Certifique-se de que o serviço de rede começará no tempo de arranque executando o seguinte comando:

    ```console
    # chkconfig network on
    ```

1. Registe a sua assinatura Red Hat para permitir a instalação de pacotes a partir do repositório RHEL executando o seguinte comando:

    ```console
    # subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Modifique a linha de arranque do núcleo na sua configuração de larva para incluir parâmetros adicionais de kernel para Azure. Para fazer esta configuração, abra `/boot/grub/menu.lst` num editor de texto e certifique-se de que o núcleo padrão inclui os seguintes parâmetros:

    ```config-grub
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

    Isto também irá garantir que todas as mensagens de consola são enviadas para a primeira porta em série, que pode ajudar o suporte do Azure com problemas de depuração.
    
    Além disso, recomendamos que remova os seguintes parâmetros:

    ```config-grub
    rhgb quiet crashkernel=auto
    ```

    A bota gráfica e silenciosa não é útil num ambiente em nuvem onde queremos que todos os registos sejam enviados para a porta em série. Pode deixar a `crashkernel` opção configurada se desejar. Note que este parâmetro reduz a quantidade de memória disponível na máquina virtual em 128 MB ou mais, o que pode ser problemático em tamanhos de máquinas virtuais menores.


1. Adicione módulos Hiper-V a initramfs:  

    Editar `/etc/dracut.conf` , e adicionar o seguinte conteúdo:

    ```config-conf
    add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
    ```

    Reconstruir initramfs:

    ```config-conf
    # dracut -f -v
    ```

1. Desinstalar a nuvem:

    ```console
    # yum remove cloud-init
    ```

1. Certifique-se de que o servidor SSH está instalado e configurado para começar na hora de arranque:

    ```console
    # chkconfig sshd on
    ```

    Modificar /etc/ssh/sshd_config para incluir as seguintes linhas:

    ```config
    PasswordAuthentication yes
    ClientAliveInterval 180
    ```

1. O pacote WALinuxAgent, `WALinuxAgent-<version>` foi empurrado para o repositório extras do Chapéu Vermelho. Ativar o repositório extra executando o seguinte comando:

    ```console
    # subscription-manager repos --enable=rhel-6-server-extras-rpms
    ```

1. Instale o Agente Azure Linux executando o seguinte comando:

    ```console
    # yum install WALinuxAgent

    # chkconfig waagent on
    ```

1. O Agente Azure Linux pode configurar automaticamente o espaço de troca utilizando o disco de recursos local que é ligado à máquina virtual após a aprovisionamento da máquina virtual no Azure. Note que o disco de recursos local é um disco temporário, e pode ser esvaziado se a máquina virtual for desprovisionada. Depois de instalar o Agente Azure Linux no passo anterior, modifique os seguintes parâmetros em **/etc/waagent.conf** adequadamente:

    ```config-conf
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

1. Desregralar a subscrição (se necessário) executando o seguinte comando:

    ```console-conf
    # subscription-manager unregister
    ```

1. Executar os seguintes comandos para desprovisionar a máquina virtual e prepará-la para provisão em Azure:

    ```console
    # Note: if you are migrating a specific virtual machine and do not wish to create a generalized image,
    # skip the deprovision step
    # sudo rm -rf /var/lib/waagent/
    # sudo rm -f /var/log/waagent.log

    # waagent -force -deprovision+user
    # rm -f ~/.bash_history
    

    # export HISTSIZE=0

    # logout
    ```

1. Desligue a máquina virtual na KVM.

1. Converta a imagem qcow2 no formato VHD.

    > [!NOTE]
    > Existe um bug conhecido nas versões qemu-img >=2.2.1 que resulta num VHD inadequadamente formatado. A questão foi corrigida no QEMU 2.6. Recomenda-se a utilização de qemu-img 2.2.0 ou inferior, ou atualizar para 2.6 ou superior. Referência: https://bugs.launchpad.net/qemu/+bug/1490611 .
    >

    Primeiro converta a imagem em formato cru:

    ```console
    # qemu-img convert -f qcow2 -O raw rhel-6.9.qcow2 rhel-6.9.raw
    ```

    Certifique-se de que o tamanho da imagem em bruto está alinhado com 1 MB. Caso contrário, arredondar o tamanho para alinhar com 1 MB:

    ```console
    # MB=$((1024*1024))
    # size=$(qemu-img info -f raw --output json "rhel-6.9.raw" | \
      gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    # rounded_size=$((($size/$MB + 1)*$MB))
    # qemu-img resize rhel-6.9.raw $rounded_size
    ```

    Converter o disco cru num VHD de tamanho fixo:

    ```console
    # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.9.raw rhel-6.9.vhd
    ```

    Ou, com a versão qemu **2.6+** incluir a `force_size` opção:

    ```console
    # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-6.9.raw rhel-6.9.vhd
    ```

        
### <a name="rhel-7-using-kvm"></a>RHEL 7 utilizando kvm

1. Descarregue a imagem KVM do RHEL 7 a partir do site do Red Hat. Este procedimento utiliza o RHEL 7 como exemplo.

1. Desateia uma senha de raiz.

    Gere uma palavra-passe encriptada e copie a saída do comando:

    ```console
    # openssl passwd -1 changeme
    ```

    Desapedaça uma senha de raiz com o peixe-hóspede:

    ```console
    # guestfish --rw -a <image-name>
    > <fs> run
    > <fs> list-filesystems
    > <fs> mount /dev/sda1 /
    > <fs> vi /etc/shadow
    > <fs> exit
    ```

   Mude o segundo campo de utilizadores de raiz de "!!" para a senha encriptada.

1. Crie uma máquina virtual em KVM a partir da imagem qcow2. Desa estale o tipo de disco para **qcow2**, e desate o modelo de dispositivo de interface de rede virtual para **virtio**. Em seguida, ligue a máquina virtual e inscreva-se como raiz.

1. Criar ou editar o `/etc/sysconfig/network` ficheiro e adicionar o seguinte texto:

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Criar ou editar o `/etc/sysconfig/network-scripts/ifcfg-eth0` ficheiro e adicionar o seguinte texto:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    PERSISTENT_DHCLIENT=yes
    NM_CONTROLLED=yes
    ```

1. Certifique-se de que o serviço de rede começará no tempo de arranque executando o seguinte comando:

    ```console
    # sudo systemctl enable network
    ```

1. Registe a sua assinatura Red Hat para permitir a instalação de pacotes a partir do repositório RHEL executando o seguinte comando:

    ```console
    # subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Modifique a linha de arranque do núcleo na sua configuração de larva para incluir parâmetros adicionais de kernel para Azure. Para fazer esta configuração, abra `/etc/default/grub` num editor de texto e edite o `GRUB_CMDLINE_LINUX` parâmetro. Por exemplo:

    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Este comando também garante que todas as mensagens de consola são enviadas para a primeira porta em série, que pode ajudar o suporte do Azure com problemas de depuração. O comando também desliga as novas convenções de nomeação RHEL 7 para NICs. Além disso, recomendamos que remova os seguintes parâmetros:

    ```config-grub
    rhgb quiet crashkernel=auto
    ```

    A bota gráfica e silenciosa não é útil num ambiente em nuvem onde queremos que todos os registos sejam enviados para a porta em série. Pode deixar a `crashkernel` opção configurada se desejar. Note que este parâmetro reduz a quantidade de memória disponível na máquina virtual em 128 MB ou mais, o que pode ser problemático em tamanhos de máquinas virtuais menores.

1. Depois de ter terminado a `/etc/default/grub` edição, executar o seguinte comando para reconstruir a configuração da larva:

    ```console
    # grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. Adicione módulos Hiper-V em initramfs.

    Editar `/etc/dracut.conf` e adicionar conteúdo:

    ```config-conf
    add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
    ```

    Reconstruir initramfs:

    ```config-conf
    # dracut -f -v
    ```

1. Desinstalar a nuvem:

    ```console
    # yum remove cloud-init
    ```

1. Certifique-se de que o servidor SSH está instalado e configurado para começar na hora de arranque:

    ```console
    # systemctl enable sshd
    ```

    Modificar /etc/ssh/sshd_config para incluir as seguintes linhas:

    ```config
    PasswordAuthentication yes
    ClientAliveInterval 180
    ```

1. O pacote WALinuxAgent, `WALinuxAgent-<version>` foi empurrado para o repositório extras do Chapéu Vermelho. Ativar o repositório extra executando o seguinte comando:

    ```config
    # subscription-manager repos --enable=rhel-7-server-extras-rpms
    ```

1. Instale o Agente Azure Linux executando o seguinte comando:

    ```console
    # yum install WALinuxAgent
    ```

    Ativar o serviço waagent:

    ```console
    # systemctl enable waagent.service
    ```

1. Instale cloud-init Siga os passos em 'Prepare uma máquina virtual RHEL 7 do Hyper-V Manager', passo 12, 'Instale cloud-init para lidar com o provisionamento.'

1. Configuração de troca 

    Não crie espaço de troca no disco do sistema operativo.
    Siga os passos em 'Prepare uma máquina virtual RHEL 7 do Hyper-V Manager', passo 13, 'Configuração de troca'


1. Desregralar a subscrição (se necessário) executando o seguinte comando:

    ```console
    # subscription-manager unregister
    ```


1. Deprovisionamento

    Siga os passos em 'Prepare uma máquina virtual RHEL 7 do Hyper-V Manager', passo 15, 'Deprovision'

1. Desligue a máquina virtual na KVM.

1. Converta a imagem qcow2 no formato VHD.

    > [!NOTE]
    > Existe um bug conhecido nas versões qemu-img >=2.2.1 que resulta num VHD inadequadamente formatado. A questão foi corrigida no QEMU 2.6. Recomenda-se a utilização de qemu-img 2.2.0 ou inferior, ou atualizar para 2.6 ou superior. Referência: https://bugs.launchpad.net/qemu/+bug/1490611 .
    >

    Primeiro converta a imagem em formato cru:

    ```console
    # qemu-img convert -f qcow2 -O raw rhel-7.4.qcow2 rhel-7.4.raw
    ```

    Certifique-se de que o tamanho da imagem em bruto está alinhado com 1 MB. Caso contrário, arredondar o tamanho para alinhar com 1 MB:

    ```console
    # MB=$((1024*1024))
    # size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
      gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    # rounded_size=$((($size/$MB + 1)*$MB))
    # qemu-img resize rhel-7.4.raw $rounded_size
    ```

    Converter o disco cru num VHD de tamanho fixo:

    ```console
    # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

    Ou, com a versão qemu **2.6+** incluir a `force_size` opção:

    ```console
    # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

## <a name="vmware"></a>VMware

Esta secção mostra-lhe como preparar um [distro RHEL 6](#rhel-6-using-vmware) ou [RHEL 7](#rhel-6-using-vmware)  da VMware.

### <a name="prerequisites"></a>Pré-requisitos
Esta secção pressupõe que já instalou uma máquina virtual RHEL em VMware. Para obter mais informações sobre como instalar um sistema operativo em VMware, consulte o Guia de [Instalação do Sistema Operativo VMware Guest](https://partnerweb.vmware.com/GOSIG/home.html).

* Quando instala o sistema operativo Linux, recomendamos que utilize divisórias padrão em vez de LVM, que é frequentemente o padrão para muitas instalações. Isto evitará conflitos de nome LVM com máquina virtual clonada, especialmente se um disco do sistema operativo precisar de ser ligado a outra máquina virtual para resolução de problemas. LVM ou RAID podem ser usados em discos de dados, se preferir.
* Não configuure uma partição de troca no disco do sistema operativo. Pode configurar o agente Linux para criar um ficheiro de troca no disco de recursos temporários. Pode encontrar mais informações sobre isso nos passos que se seguem.
* Quando criar o disco rígido virtual, selecione **Guarde o disco virtual como um único ficheiro**.

### <a name="rhel-6-using-vmware"></a>RHEL 6 usando VMware
1. No RHEL 6, o NetworkManager pode interferir com o agente Azure Linux. Desinstale este pacote executando o seguinte comando:

    ```console
    # sudo rpm -e --nodeps NetworkManager
    ```

1. Criar uma **rede** de ficheiros nomeada no /etc/sysconfig/ diretório que contenha o seguinte texto:

    ```console
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Criar ou editar o `/etc/sysconfig/network-scripts/ifcfg-eth0` ficheiro e adicionar o seguinte texto:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

1. Mova (ou remova) as regras udev para evitar gerar regras estáticas para a interface Ethernet. Estas regras causam problemas quando clona uma máquina virtual em Azure ou Hiper-V:

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

    # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

1. Certifique-se de que o serviço de rede começará no tempo de arranque executando o seguinte comando:

    ```console
    # sudo chkconfig network on
    ```

1. Registe a sua assinatura Red Hat para permitir a instalação de pacotes a partir do repositório RHEL executando o seguinte comando:

    ```console
    # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. O pacote WALinuxAgent, `WALinuxAgent-<version>` foi empurrado para o repositório extras do Chapéu Vermelho. Ativar o repositório extra executando o seguinte comando:

    ```console
    # subscription-manager repos --enable=rhel-6-server-extras-rpms
    ```

1. Modifique a linha de arranque do núcleo na sua configuração de larva para incluir parâmetros adicionais de kernel para Azure. Para isso, abra `/etc/default/grub` num editor de texto e edite o `GRUB_CMDLINE_LINUX` parâmetro. Por exemplo:

    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0"
    ```

   Isto também irá garantir que todas as mensagens de consola são enviadas para a primeira porta em série, que pode ajudar o suporte do Azure com problemas de depuração. Além disso, recomendamos que remova os seguintes parâmetros:

    ```config-grub
    rhgb quiet crashkernel=auto
    ```
   
    A bota gráfica e silenciosa não é útil num ambiente em nuvem onde queremos que todos os registos sejam enviados para a porta em série. Pode deixar a `crashkernel` opção configurada se desejar. Note que este parâmetro reduz a quantidade de memória disponível na máquina virtual em 128 MB ou mais, o que pode ser problemático em tamanhos de máquinas virtuais menores.

1. Adicione módulos Hiper-V a initramfs:

    Editar `/etc/dracut.conf` , e adicionar o seguinte conteúdo:

    ```config-conf
    add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
    ```

    Reconstruir initramfs:

    ```config-cong
    # dracut -f -v
    ```

1. Certifique-se de que o servidor SSH está instalado e configurado para iniciar na hora do arranque, que normalmente é o padrão. Modificar `/etc/ssh/sshd_config` para incluir a seguinte linha:

    ```config
    ClientAliveInterval 180
    ```

1. Instale o Agente Azure Linux executando o seguinte comando:

    ```console
    # sudo yum install WALinuxAgent

    # sudo chkconfig waagent on
    ```

1. Não crie espaço de troca no disco do sistema operativo.

    O Agente Azure Linux pode configurar automaticamente o espaço de troca utilizando o disco de recursos local que é ligado à máquina virtual após a aprovisionamento da máquina virtual no Azure. Note que o disco de recursos local é um disco temporário, e pode ser esvaziado se a máquina virtual for desprovisionada. Depois de instalar o Agente Azure Linux no passo anterior, modifique os seguintes parâmetros `/etc/waagent.conf` de forma adequada:

    ```config-conf
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

1. Desregralar a subscrição (se necessário) executando o seguinte comando:

    ```console
    # sudo subscription-manager unregister
    ```

1. Executar os seguintes comandos para desprovisionar a máquina virtual e prepará-la para provisão em Azure:

    ```console
    # Note: if you are migrating a specific virtual machine and do not wish to create a generalized image,
    # skip the deprovision step
    # sudo rm -rf /var/lib/waagent/
    # sudo rm -f /var/log/waagent.log

    # waagent -force -deprovision+user
    # rm -f ~/.bash_history
    

    # export HISTSIZE=0

    # logout
    ```

1. Desligue a máquina virtual e converta o ficheiro VMDK num ficheiro .vhd.

    > [!NOTE]
    > Existe um bug conhecido nas versões qemu-img >=2.2.1 que resulta num VHD inadequadamente formatado. A questão foi corrigida no QEMU 2.6. Recomenda-se a utilização de qemu-img 2.2.0 ou inferior, ou atualizar para 2.6 ou superior. Referência: https://bugs.launchpad.net/qemu/+bug/1490611 .
    >

    Primeiro converta a imagem em formato cru:

    ```console
    # qemu-img convert -f vmdk -O raw rhel-6.9.vmdk rhel-6.9.raw
    ```

    Certifique-se de que o tamanho da imagem em bruto está alinhado com 1 MB. Caso contrário, arredondar o tamanho para alinhar com 1 MB:

    ```console
    # MB=$((1024*1024))
    # size=$(qemu-img info -f raw --output json "rhel-6.9.raw" | \
      gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    # rounded_size=$((($size/$MB + 1)*$MB))
    # qemu-img resize rhel-6.9.raw $rounded_size
    ```

    Converter o disco cru num VHD de tamanho fixo:

    ```console
    # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.9.raw rhel-6.9.vhd
    ```

    Ou, com a versão qemu **2.6+** incluir a `force_size` opção:

    ```console
    # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-6.9.raw rhel-6.9.vhd
    ```

### <a name="rhel-7-using-vmware"></a>RHEL 7 usando VMware
1. Criar ou editar o `/etc/sysconfig/network` ficheiro e adicionar o seguinte texto:

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Criar ou editar o `/etc/sysconfig/network-scripts/ifcfg-eth0` ficheiro e adicionar o seguinte texto:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    PERSISTENT_DHCLIENT=yes
    NM_CONTROLLED=yes
    ```

1. Certifique-se de que o serviço de rede começará no tempo de arranque executando o seguinte comando:

    ```console
    # sudo systemctl enable network
    ```

1. Registe a sua assinatura Red Hat para permitir a instalação de pacotes a partir do repositório RHEL executando o seguinte comando:

    ```console
    # sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Modifique a linha de arranque do núcleo na sua configuração de larva para incluir parâmetros adicionais de kernel para Azure. Para fazer esta modificação, abra `/etc/default/grub` num editor de texto e edite o `GRUB_CMDLINE_LINUX` parâmetro. Por exemplo:

    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Esta configuração também garante que todas as mensagens de consola são enviadas para a primeira porta em série, que pode ajudar o suporte do Azure com problemas de depuração. Também desliga as novas convenções de nomeação RHEL 7 para OS NICs. Além disso, recomendamos que remova os seguintes parâmetros:

    ```config-grub
    rhgb quiet crashkernel=auto
    ```

    A bota gráfica e silenciosa não é útil num ambiente em nuvem onde queremos que todos os registos sejam enviados para a porta em série. Pode deixar a `crashkernel` opção configurada se desejar. Note que este parâmetro reduz a quantidade de memória disponível na máquina virtual em 128 MB ou mais, o que pode ser problemático em tamanhos de máquinas virtuais menores.

1. Depois de ter terminado a `/etc/default/grub` edição, executar o seguinte comando para reconstruir a configuração da larva:

    ```console
    # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. Adicione módulos Hiper-V a initramfs.

    `/etc/dracut.conf`Editar, adicionar conteúdo:

    ```config-conf
    add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
    ```

    Reconstruir initramfs:

    ```console
    # dracut -f -v
    ```

1. Certifique-se de que o servidor SSH está instalado e configurado para começar na hora de arranque. Esta definição é normalmente o padrão. Modificar `/etc/ssh/sshd_config` para incluir a seguinte linha:

    ```config
    ClientAliveInterval 180
    ```

1. O pacote WALinuxAgent, `WALinuxAgent-<version>` foi empurrado para o repositório extras do Chapéu Vermelho. Ativar o repositório extra executando o seguinte comando:

    ```console
    # subscription-manager repos --enable=rhel-7-server-extras-rpms
    ```

1. Instale o Agente Azure Linux executando o seguinte comando:

    ```console
    # sudo yum install WALinuxAgent

    # sudo systemctl enable waagent.service
    ```

1. Instalar cloud-init

    Siga os passos em 'Prepare uma máquina virtual RHEL 7 do Hyper-V Manager', passo 12, 'Instale cloud-init para manusear o provisionamento.'

1. Configuração de troca

    Não crie espaço de troca no disco do sistema operativo.
    Siga os passos em 'Prepare uma máquina virtual RHEL 7 do Hyper-V Manager', passo 13, 'Configuração de troca'

1. Se pretender não registar a subscrição, execute o seguinte comando:

    ```console
    # sudo subscription-manager unregister
    ```

1. Deprovisionamento

    Siga os passos em 'Prepare uma máquina virtual RHEL 7 do Hyper-V Manager', passo 15, 'Deprovision'


1. Desligue a máquina virtual e converta o ficheiro VMDK no formato VHD.

    > [!NOTE]
    > Existe um bug conhecido nas versões qemu-img >=2.2.1 que resulta num VHD inadequadamente formatado. A questão foi corrigida no QEMU 2.6. Recomenda-se a utilização de qemu-img 2.2.0 ou inferior, ou atualizar para 2.6 ou superior. Referência: https://bugs.launchpad.net/qemu/+bug/1490611 .
    >

    Primeiro converta a imagem em formato cru:

    ```console
    # qemu-img convert -f vmdk -O raw rhel-7.4.vmdk rhel-7.4.raw
    ```

    Certifique-se de que o tamanho da imagem em bruto está alinhado com 1 MB. Caso contrário, arredondar o tamanho para alinhar com 1 MB:

    ```console
    # MB=$((1024*1024))
    # size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
      gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    # rounded_size=$((($size/$MB + 1)*$MB))
    # qemu-img resize rhel-7.4.raw $rounded_size
    ```

    Converter o disco cru num VHD de tamanho fixo:

    ```console
    # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

    Ou, com a versão qemu **2.6+** incluir a `force_size` opção:

    ```console
    # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

## <a name="kickstart-file"></a>Arquivo Kickstart

Esta secção mostra-lhe como preparar um distro RHEL 7 de um ISO utilizando um ficheiro kickstart.

### <a name="rhel-7-from-a-kickstart-file"></a>RHEL 7 de um ficheiro de arranque

1.  Crie um ficheiro de arranque que inclua o seguinte conteúdo e guarde o ficheiro. Para mais informações sobre a instalação do kickstart, consulte o [Guia de Instalação kickstart](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html).

    ```text
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

    # Install cloud-init
    yum install -y cloud-init cloud-utils-growpart gdisk hyperv-daemons

    # Configure waagent for cloud-init
    sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
    sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf

    echo "Adding mounts and disk_setup to init stage"
    sed -i '/ - mounts/d' /etc/cloud/cloud.cfg
    sed -i '/ - disk_setup/d' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - mounts' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - disk_setup' /etc/cloud/cloud.cfg

    # Disable the root account
    usermod root -p '!!'

    # Disabke swap in WALinuxAgent
    ResourceDisk.Format=n
    ResourceDisk.EnableSwap=n

    # Configure swap using cloud-init
    cat > /etc/cloud/cloud.cfg.d/00-azure-swap.cfg << EOF
    #cloud-config
    # Generated by Azure cloud image build
    disk_setup:
    ephemeral0:
        table_type: mbr
        layout: [66, [33, 82]]
        overwrite: True
    fs_setup:
    - device: ephemeral0.1
        filesystem: ext4
    - device: ephemeral0.2
        filesystem: swap
    mounts:
    - ["ephemeral0.1", "/mnt"]
    - ["ephemeral0.2", "none", "swap", "sw", "0", "0"]
    EOF

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
    PERSISTENT_DHCLIENT=yes
    NM_CONTROLLED=yes
    EOF

    # Deprovision and prepare for Azure if you are creating a generalized image
    sudo cloud-init clean --logs --seed
    sudo rm -rf /var/lib/cloud/
    sudo rm -rf /var/lib/waagent/
    sudo rm -f /var/log/waagent.log

    sudo waagent -force -deprovision+user
    rm -f ~/.bash_history
    export HISTSIZE=0

    %end
    ```

1. Coloque o ficheiro de arranque onde o sistema de instalação possa aceder-lhe.

1. No Hyper-V Manager, crie uma nova máquina virtual. Na página **'Ligar** o disco rígido virtual', **selecione Fixe um disco rígido virtual mais tarde** e complete o Novo Assistente de Máquina Virtual.

1. Abra as definições da máquina virtual:

    a.  Prenda um novo disco rígido virtual à máquina virtual. Certifique-se de selecionar **o formato VHD** e **o tamanho fixo.**

    b.  Fixe a instalação ISO à unidade de DVD.

    c.  Desapontina o BIOS a partir de CD.

1. Inicie a máquina virtual. Quando o guia de instalação aparecer, prima **o Separador** para configurar as opções de arranque.

1. Introduza `inst.ks=<the location of the kickstart file>` no final das opções de arranque e prima **Enter**.

1. Aguarde que a instalação termine. Quando estiver terminada, a máquina virtual será desligada automaticamente. O seu VHD Linux está agora pronto para ser enviado para Azure.

## <a name="known-issues"></a>Problemas conhecidos
### <a name="the-hyper-v-driver-could-not-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>O controlador Hiper-V não pôde ser incluído no disco RAM inicial quando se utiliza um hipervisor não-Hiper-V

Em alguns casos, os instaladores Linux podem não incluir os controladores de Hiper-V no disco RAM inicial (initrd ou initramfs) a menos que o Linux detete que está a funcionar num ambiente Hiper-V.

Quando estiver a utilizar um sistema de virtualização diferente (isto é, VirtualBox, Xen, etc.) para preparar a sua imagem Linux, poderá ter de reconstruir o initrd para garantir que pelo menos os módulos de kernel hv_vmbus e hv_storvsc estão disponíveis no disco RAM inicial. Esta é uma questão conhecida pelo menos em sistemas que se baseiam na distribuição a montante do Red Hat.

Para resolver este problema, adicione módulos Hyper-V aos initramfs e reconstrua-o:

Editar `/etc/dracut.conf` , e adicionar o seguinte conteúdo:

```config-conf
add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
```

Reconstruir initramfs:

```console
# dracut -f -v
```

Para mais detalhes, consulte as informações sobre [a reconstrução de initramfs.](https://access.redhat.com/solutions/1958)

## <a name="next-steps"></a>Passos seguintes
* Está agora pronto para usar o seu disco rígido virtual Red Hat Enterprise Linux para criar novas máquinas virtuais em Azure. Se esta for a primeira vez que está a enviar o ficheiro .vhd para a Azure, consulte [Create a Linux VM a partir de um disco personalizado](upload-vhd.md#option-1-upload-a-vhd).
* Para mais detalhes sobre os hipervisores certificados para executar Red Hat Enterprise Linux, consulte [o site da Red Hat.](https://access.redhat.com/certified-hypervisors)
* Para saber mais sobre a utilização de imagens RHEL BYOS prontas a produzir, aceda à página de documentação para [BYOS](../workloads/redhat/byos.md).