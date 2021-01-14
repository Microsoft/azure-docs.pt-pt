---
title: Criar e carregar um Linux VHD baseado em CentOS
description: Aprenda a criar e carregar um disco rígido virtual Azure (VHD) que contenha um sistema operativo Linux baseado no CentOS.
author: danielsollondon
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 12/01/2020
ms.author: danis
ms.openlocfilehash: b29b970061e94bca07b4a7b2ba6b3d3ad0a7a2e1
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98203257"
---
# <a name="prepare-a-centos-based-virtual-machine-for-azure"></a>Prepare a CentOS-based virtual machine for Azure (Preparar uma máquina virtual baseada em CentOS para o Azure)

Aprenda a criar e carregar um disco rígido virtual Azure (VHD) que contenha um sistema operativo Linux baseado no CentOS.

* [Prepare uma máquina virtual CentOS 6.x para O Azure](#centos-6x)
* [Prepare uma máquina virtual CentOS 7.0+ para o Azure](#centos-70)


## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que já instalou um sistema operativo Linux CentOS (ou derivado semelhante) num disco rígido virtual. Existem várias ferramentas para criar ficheiros .vhd, por exemplo uma solução de virtualização como o Hyper-V. Para obter instruções, consulte [instalar a função Hiper-V e configurar uma máquina virtual.](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11))

**Notas de instalação centos**

* Consulte também [as notas de instalação do General Linux](create-upload-generic.md#general-linux-installation-notes) para obter mais dicas sobre a preparação do Linux para o Azure.
* O formato VHDX não é suportado em Azure, apenas **VHD fixo**.  Pode converter o disco em formato VHD utilizando o Hyper-V Manager ou o cmdlet converte-vhd. Se estiver a utilizar a VirtualBox, isto significa selecionar **o tamanho fixo** em oposição ao padrão atribuído dinamicamente ao criar o disco.
* Ao instalar o sistema Linux *recomenda-se* que utilize divisórias padrão em vez de LVM (muitas vezes o padrão para muitas instalações). Isto evitará conflitos de nome LVM com VMs clonados, especialmente se um disco de SO precisar de ser ligado a outro VM idêntico para resolução de problemas. [LVM](configure-lvm.md) ou [RAID](configure-raid.md) podem ser usados em discos de dados.
* **É necessário suporte kernel para a montagem de sistemas de ficheiros UDF.** No início da boot on Azure, a configuração de provisionamento é passada para o Linux VM através de meios formatados pela UDF que estão ligados ao hóspede. O agente Azure Linux deve ser capaz de montar o sistema de ficheiros UDF para ler a sua configuração e provisão do VM.
* As versões de kernel Linux abaixo de 2.6.37 não suportam UMA em Hiper-V com tamanhos VM maiores. Esta questão afeta principalmente as distribuições mais antigas utilizando o núcleo a montante do chapéu vermelho 2.6.32, e foi fixada em RHEL 6.6 (kernel-2.6.32-504). Os sistemas que executam núcleos personalizados com mais de 2.6.37, ou núcleos baseados em RHEL com mais de 2.6.32-504 devem definir o parâmetro de arranque `numa=off` na linha de comando do núcleo em grub.conf. Para mais informações consulte Red Hat [KB 436883](https://access.redhat.com/solutions/436883).
* Não configuure uma partição de troca no disco OS. Mais informações sobre isso podem ser encontradas nos degraus abaixo.
* Todos os VHDs em Azure devem ter um tamanho virtual alinhado a 1MB. Ao converter de um disco cru para VHD deve certificar-se de que o tamanho do disco bruto é um múltiplo de 1MB antes da conversão. Consulte [as notas de instalação do Linux](create-upload-generic.md#general-linux-installation-notes) para obter mais informações.

## <a name="centos-6x"></a>CentOS 6.x

1. No Hyper-V Manager, selecione a máquina virtual.

2. Clique **em Connect** para abrir uma janela de consola para a máquina virtual.

3. No CentOS 6, o NetworkManager pode interferir com o agente Azure Linux. Desinstale este pacote executando o seguinte comando:

    ```bash
    sudo rpm -e --nodeps NetworkManager
    ```

4. Criar ou editar o ficheiro `/etc/sysconfig/network` e adicionar o seguinte texto:

    ```console
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

5. Criar ou editar o ficheiro `/etc/sysconfig/network-scripts/ifcfg-eth0` e adicionar o seguinte texto:

    ```console
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

6. Modifique as regras udev para evitar gerar regras estáticas para a interface Ethernet. Estas regras podem causar problemas ao clonar uma máquina virtual no Microsoft Azure ou Hiper-V:

    ```bash
    sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

7. Certifique-se de que o serviço de rede começará no tempo de arranque, executando o seguinte comando:

    ```bash
    sudo chkconfig network on
    ```

8. Se quiser utilizar os espelhos OpenLogic que estão alojados nos centros de dados Azure, em seguida, substitua o `/etc/yum.repos.d/CentOS-Base.repo` ficheiro pelos seguintes repositórios.  Isto também irá adicionar o repositório **[openlogic]** que inclui pacotes adicionais, como o agente Azure Linux:

   ```console
   [openlogic]
   name=CentOS-$releasever - openlogic packages for $basearch
   baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
   enabled=1
   gpgcheck=0

   [base]
   name=CentOS-$releasever - Base
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #released updates
   [updates]
   name=CentOS-$releasever - Updates
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updates&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #additional packages that may be useful
   [extras]
   name=CentOS-$releasever - Extras
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #additional packages that extend functionality of existing packages
   [centosplus]
   name=CentOS-$releasever - Plus
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=centosplus&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
   gpgcheck=1
   enabled=0
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #contrib - packages by Centos Users
   [contrib]
   name=CentOS-$releasever - Contrib
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=contrib&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
   gpgcheck=1
   enabled=0
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
   ```

    > [!Note]
    > O resto deste guia assumirá que está a usar pelo menos o `[openlogic]` repo, que será usado para instalar o agente Azure Linux abaixo.

9. Adicione a seguinte linha a ////yum.conf:

    ```console
    http_caching=packages
    ```

10. Executar o seguinte comando para limpar os metadados yum atuais e atualizar o sistema com os pacotes mais recentes:

    ```bash
    yum clean all
    ```

    A menos que esteja a criar uma imagem para uma versão mais antiga do CentOS, é aconselhável atualizar todos os pacotes para o mais tardar:

    ```bash
    sudo yum -y update
    ```

    Pode ser necessário reiniciar depois de executar este comando.

11. (Opcional) Instale os controladores para os Serviços de Integração Linux (LIS).

    > [!IMPORTANT]
    > O passo é **necessário** para o CentOS 6.3 e mais cedo, e opcional para lançamentos posteriores.

    ```bash
    sudo rpm -e hypervkvpd  ## (may return error if not installed, that's OK)
    sudo yum install microsoft-hyper-v
    ```

    Em alternativa, pode seguir as instruções de instalação manual na [página de descarregamento LIS](https://www.microsoft.com/download/details.aspx?id=55106) para instalar o RPM no seu VM.

12. Instale o Agente Azure Linux e dependências. Iniciar e ativar o serviço waagent:

    ```bash
    sudo yum install python-pyasn1 WALinuxAgent
    sudo service waagent start
    sudo chkconfig waagent on
    ```


    O pacote WALinuxAgent removerá os pacotes NetworkManager e NetworkManager-gnome se não forem já removidos como descrito no passo 3.

13. Modifique a linha de arranque do núcleo na sua configuração de larva para incluir parâmetros adicionais de kernel para Azure. Para isso, abra `/boot/grub/menu.lst` num editor de texto e certifique-se de que o núcleo padrão inclui os seguintes parâmetros:

    ```console
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

    Isto também irá garantir que todas as mensagens de consola são enviadas para a primeira porta em série, que pode ajudar o suporte do Azure com problemas de depuração.

    Além do acima referido, recomenda-se *a remoção* dos seguintes parâmetros:

    ```console
    rhgb quiet crashkernel=auto
    ```

    A bota gráfica e silenciosa não é útil num ambiente em nuvem onde queremos que todos os registos sejam enviados para a porta em série.  A `crashkernel` opção pode ser deixada configurada se desejar, mas note que este parâmetro reduzirá a quantidade de memória disponível no VM em 128MB ou mais, o que pode ser problemático nos tamanhos de VM menores.

    > [!Important]
    > CentOS 6.5 e anterior deve também definir o parâmetro do núcleo `numa=off` . Ver Chapéu Vermelho [KB 436883](https://access.redhat.com/solutions/436883).

14. Certifique-se de que o servidor SSH está instalado e configurado para começar na hora de arranque.  Este é geralmente o padrão.

15. Não crie espaço de troca no disco SO.

    O Agente Azure Linux pode configurar automaticamente o espaço de troca utilizando o disco de recursos local que é ligado ao VM após o provisionamento no Azure. Note que o disco de recursos local é um disco *temporário* e pode ser esvaziado quando o VM é desprovisionado. Depois de instalar o Agente Azure Linux (ver passo anterior), modificar adequadamente os seguintes `/etc/waagent.conf` parâmetros:

    ```console
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048 ## NOTE: set this to whatever you need it to be.
    ```

16. Executar os seguintes comandos para desprovisionar a máquina virtual e prepará-la para provisão em Azure:

    ```bash
    sudo waagent -force -deprovision+user
    export HISTSIZE=0
    logout
    ```

17. Clique em **Ação -> Desligar** em Hyper-V Manager. O seu VHD Linux está agora pronto para ser enviado para Azure.



## <a name="centos-70"></a>CentOS 7.0+

**Alterações no CentOS 7 (e derivados similares)**

Preparar uma máquina virtual CentOS 7 para O Azure é muito semelhante ao CentOS 6, no entanto existem várias diferenças importantes que merecem ser notantes:

* O pacote NetworkManager já não entra em conflito com o agente Azure Linux. Esta embalagem é instalada por defeito e recomendamos que não seja removida.
* GRUB2 é agora usado como o bootloader padrão, por isso o procedimento de edição dos parâmetros do núcleo mudou (ver abaixo).
* XFS é agora o sistema de ficheiros predefinido. O sistema de ficheiros ext4 ainda pode ser utilizado se desejar.

**Passos de configuração**

1. No Hyper-V Manager, selecione a máquina virtual.

2. Clique **em Connect** para abrir uma janela de consola para a máquina virtual.

3. Criar ou editar o ficheiro `/etc/sysconfig/network` e adicionar o seguinte texto:

    ```console
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

4. Criar ou editar o ficheiro `/etc/sysconfig/network-scripts/ifcfg-eth0` e adicionar o seguinte texto:

    ```console
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    ```

5. Modifique as regras udev para evitar gerar regras estáticas para a interface Ethernet. Estas regras podem causar problemas ao clonar uma máquina virtual no Microsoft Azure ou Hiper-V:

    ```bash
    sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    ```

6. Se quiser utilizar os espelhos OpenLogic que estão alojados nos centros de dados Azure, em seguida, substitua o `/etc/yum.repos.d/CentOS-Base.repo` ficheiro pelos seguintes repositórios.  Isto também irá adicionar o repositório **[openlogic]** que inclui pacotes para o agente Azure Linux:

   ```console
   [openlogic]
   name=CentOS-$releasever - openlogic packages for $basearch
   baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
   enabled=1
   gpgcheck=0
    
   [base]
   name=CentOS-$releasever - Base
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
    
   #released updates
   [updates]
   name=CentOS-$releasever - Updates
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updates&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
    
   #additional packages that may be useful
   [extras]
   name=CentOS-$releasever - Extras
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
    
   #additional packages that extend functionality of existing packages
   [centosplus]
   name=CentOS-$releasever - Plus
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=centosplus&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
   gpgcheck=1
   enabled=0
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
   ```
    
   > [!Note]
   > O resto deste guia assumirá que está a usar pelo menos o `[openlogic]` repo, que será usado para instalar o agente Azure Linux abaixo.

7. Executar o seguinte comando para limpar os metadados yum atuais e instalar quaisquer atualizações:

    ```bash
    sudo yum clean all
    ```

    A menos que esteja a criar uma imagem para uma versão mais antiga do CentOS, é aconselhável atualizar todos os pacotes para o mais tardar:

    ```bash
    sudo yum -y update
    ```

    Um reboot talvez necessário depois de executar este comando.

8. Modifique a linha de arranque do núcleo na sua configuração de larva para incluir parâmetros adicionais de kernel para Azure. Para isso, abra `/etc/default/grub` num editor de texto e edite o `GRUB_CMDLINE_LINUX` parâmetro, por exemplo:

    ```console
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Isto também irá garantir que todas as mensagens de consola são enviadas para a primeira porta em série, que pode ajudar o suporte do Azure com problemas de depuração. Também desliga as novas convenções de nomeação CentOS 7 para OS NICs. Além do acima referido, recomenda-se *a remoção* dos seguintes parâmetros:

    ```console
    rhgb quiet crashkernel=auto
    ```

    A bota gráfica e silenciosa não é útil num ambiente em nuvem onde queremos que todos os registos sejam enviados para a porta em série. A `crashkernel` opção pode ser deixada configurada se desejar, mas note que este parâmetro reduzirá a quantidade de memória disponível no VM em 128MB ou mais, o que pode ser problemático nos tamanhos de VM menores.

9. Uma vez terminada a edição `/etc/default/grub` por cima, executar o seguinte comando para reconstruir a configuração da larva:

    ```bash
    sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

10. Se construir a imagem a partir de **VMware, VirtualBox ou KVM:** Certifique-se de que os controladores Hiper-V estão incluídos nos initramfs:

    `/etc/dracut.conf`Editar, adicionar conteúdo:

    ```console
    add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
    ```

    Reconstruir os initramfs:

    ```bash
    sudo dracut -f -v
    ```

11. Instale o agente Azure Linux e dependências para extensões Azure VM:

    ```bash
    sudo yum install python-pyasn1 WALinuxAgent
    sudo systemctl enable waagent
    ```

12. Instale cloud-init para lidar com o provisionamento

    ```console
    yum install -y cloud-init cloud-utils-growpart gdisk hyperv-daemons

    # Configure waagent for cloud-init
    sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
    sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf


    echo "Adding mounts and disk_setup to init stage"
    sed -i '/ - mounts/d' /etc/cloud/cloud.cfg
    sed -i '/ - disk_setup/d' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - mounts' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - disk_setup' /etc/cloud/cloud.cfg

    echo "Allow only Azure datasource, disable fetching network setting via IMDS"
    cat > /etc/cloud/cloud.cfg.d/91-azure_datasource.cfg <<EOF
    datasource_list: [ Azure ]
    datasource:
    Azure:
        apply_network_config: False
    EOF

    if [[ -f /mnt/resource/swapfile ]]; then
    echo Removing swapfile - RHEL uses a swapfile by default
    swapoff /mnt/resource/swapfile
    rm /mnt/resource/swapfile -f
    fi

    echo "Add console log file"
    cat >> /etc/cloud/cloud.cfg.d/05_logging.cfg <<EOF

    # This tells cloud-init to redirect its stdout and stderr to
    # 'tee -a /var/log/cloud-init-output.log' so the user can see output
    # there without needing to look on the console.
    output: {all: '| tee -a /var/log/cloud-init-output.log'}
    EOF

    ```


13. Configuração de troca Não crie espaço de troca no disco do sistema operativo.

    Anteriormente, o Agente Azure Linux foi utilizado automaticamente para configurar o espaço de troca utilizando o disco de recursos local que é ligado à máquina virtual após a colocação da máquina virtual no Azure. No entanto, isto é agora tratado por cloud-init, **não deve** utilizar o Agente Linux para formatar o disco de recursos criar o ficheiro swap, modificar os seguintes parâmetros de `/etc/waagent.conf` forma adequada:

    ```console
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
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

14. Executar os seguintes comandos para desprovisionar a máquina virtual e prepará-la para provisão em Azure:

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

15. Clique em **Ação -> Desligar** em Hyper-V Manager. O seu VHD Linux está agora pronto para ser enviado para Azure.

## <a name="next-steps"></a>Passos seguintes

Está agora pronto a usar o seu disco rígido virtual CentOS Linux para criar novas máquinas virtuais em Azure. Se esta for a primeira vez que está a enviar o ficheiro .vhd para a Azure, consulte [Create a Linux VM a partir de um disco personalizado](upload-vhd.md#option-1-upload-a-vhd).
