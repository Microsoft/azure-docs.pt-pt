---
title: Crie e carregue um Linux VHD baseado no CentOS
description: Aprenda a criar e carregar um disco rígido virtual Azure (VHD) que contenha um sistema operativo Linux baseado no CentOS.
author: gbowerman
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 11/25/2019
ms.author: guybo
ms.openlocfilehash: 8899249fd284f69fa26bab8cd70aaf6a67fbb83c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066788"
---
# <a name="prepare-a-centos-based-virtual-machine-for-azure"></a>Prepare a CentOS-based virtual machine for Azure (Preparar uma máquina virtual baseada em CentOS para o Azure)

Aprenda a criar e carregar um disco rígido virtual Azure (VHD) que contenha um sistema operativo Linux baseado no CentOS.

* [Prepare uma máquina virtual CentOS 6.x para o Azure](#centos-6x)
* [Prepare uma máquina virtual CentOS 7.0+ para o Azure](#centos-70)


## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que já instalou um sistema operativo Linux CentOS (ou derivado semelhante) a um disco rígido virtual. Existem múltiplas ferramentas para criar ficheiros .vhd, por exemplo, uma solução de virtualização como o Hyper-V. Para obter instruções, consulte [Instale a função Hyper-V e configure uma máquina virtual](https://technet.microsoft.com/library/hh846766.aspx).

**Notas de instalação CentOS**

* Consulte também as Notas de [Instalação do General Linux](create-upload-generic.md#general-linux-installation-notes) para obter mais dicas sobre a preparação do Linux para o Azure.
* O formato VHDX não é suportado em Azure, apenas **VHD fixo**.  Pode converter o disco em formato VHD utilizando o Hyper-V Manager ou o cmdlet convert-vhd. Se estiver a utilizar o VirtualBox, isto significa selecionar o **tamanho Fixo** em oposição ao padrão dinamicamente atribuído ao criar o disco.
* Ao instalar o sistema *Linux,* recomenda-se que utilize divisórias padrão em vez de LVM (muitas vezes o padrão para muitas instalações). Isto evitará conflitos de nome lvm com VMs clonados, especialmente se um disco de SO alguma vez precisar de ser ligado a outro VM idêntico para resolução de problemas. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) podem ser utilizados em discos de dados.
* É necessário suporte para a montagem de sistemas de ficheiros UDF. No início do Azure, a configuração de provisionamento é passada para o Linux VM através de meios formados uDF que está ligado ao hóspede. O agente Azure Linux deve ser capaz de montar o sistema de ficheiros UDF para ler a sua configuração e fornecer o VM.
* As versões de kernel Linux abaixo de 2.6.37 não suportam o NUMA em Hyper-V com tamanhos VM maiores. Esta questão tem principal impacto nas distribuições mais antigas utilizando o kernel 2.6.32 do Chapéu Vermelho a montante, e foi fixada em RHEL 6.6 (kernel-2.6.32-504). Os sistemas que executam núcleos personalizados com mais de 2,6.37, ou núcleos à base de RHEL com idade superior a 2.6.32-504, devem definir o parâmetro `numa=off` de arranque na linha de comando do núcleo em grub.conf. Para mais informações consulte Red Hat [KB 436883](https://access.redhat.com/solutions/436883).
* Não configure uma divisória de troca no disco OS. O agente Linux pode ser configurado para criar um ficheiro de troca no disco de recursos temporários.  Mais informações sobre isso podem ser encontradas nos degraus abaixo.
* Todos os VHDs em Azure devem ter um tamanho virtual alinhado a 1MB. Ao converter de um disco cru para VHD, deve certificar-se de que o tamanho do disco bruto é um múltiplo de 1MB antes da conversão. Consulte as Notas de [Instalação do Linux](create-upload-generic.md#general-linux-installation-notes) para obter mais informações.

## <a name="centos-6x"></a>CentOS 6.x

1. No Hyper-V Manager, selecione a máquina virtual.

2. Clique em **Ligar** para abrir uma janela de consola para a máquina virtual.

3. No CentOS 6, o NetworkManager pode interferir com o agente Azure Linux. Desinstale esta embalagem executando o seguinte comando:

    ```bash
    sudo rpm -e --nodeps NetworkManager
    ```

4. Criar ou editar `/etc/sysconfig/network` o ficheiro e adicionar o seguinte texto:

    ```console
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

5. Criar ou editar `/etc/sysconfig/network-scripts/ifcfg-eth0` o ficheiro e adicionar o seguinte texto:

    ```console
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

6. Modificar as regras udev para evitar gerar regras estáticas para a interface Ethernet. Estas regras podem causar problemas ao clonar uma máquina virtual no Microsoft Azure ou Hyper-V:

    ```bash
    sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

7. Certifique-se de que o serviço de rede começará no momento do arranque executando o seguinte comando:

    ```bash
    sudo chkconfig network on
    ```

8. Se quiser utilizar os espelhos OpenLogic que estão alojados nos datacenters do Azure, substitua o `/etc/yum.repos.d/CentOS-Base.repo` ficheiro com os seguintes repositórios.  Isto também adicionará o repositório **[openlogic]** que inclui pacotes adicionais como o agente Azure Linux:

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
    > O resto deste guia assumirá que `[openlogic]` está a utilizar pelo menos o repo, que será utilizado para instalar o agente Azure Linux abaixo.

9. Adicione a seguinte linha a /etc/yum.conf:

    ```console
    http_caching=packages
    ```

10. Executar o seguinte comando para limpar os metadados yum atuais e atualizar o sistema com os pacotes mais recentes:

    ```bash
    yum clean all
    ```

    A menos que esteja a criar uma imagem para uma versão mais antiga do CentOS, recomenda-se atualizar todos os pacotes para as mais recentes:

    ```bash
    sudo yum -y update
    ```

    Pode ser necessário reiniciar após a execução deste comando.

11. (Opcional) Instale os controladores para os Serviços de Integração Linux (LIS).

    > [!IMPORTANT]
    > O passo é **necessário** para centos 6.3 e anterior, e opcional para lançamentos posteriores.

    ```bash
    sudo rpm -e hypervkvpd  ## (may return error if not installed, that's OK)
    sudo yum install microsoft-hyper-v
    ```

    Em alternativa, pode seguir as instruções de instalação manual na página de [descarregamento LIS](https://www.microsoft.com/download/details.aspx?id=51612) para instalar o RPM no seu VM.

12. Instale o Agente Azure Linux e as dependências. Iniciar e ativar o serviço waagent:

    ```bash
    sudo yum install python-pyasn1 WALinuxAgent
    sudo service waagent start
    sudo chkconfig waagent on
    ```


    O pacote WALinuxAgent removerá os pacotes NetworkManager e NetworkManager-gnono se não forem já removidos como descrito no passo 3.

13. Modifique a linha de arranque do núcleo na sua configuração de larva para incluir parâmetros adicionais de kernel para o Azure. Para tal, `/boot/grub/menu.lst` abra num editor de texto e certifique-se de que o núcleo predefinido inclui os seguintes parâmetros:

    ```console
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

    Isto também garantirá que todas as mensagens de consola são enviadas para a primeira porta em série, que pode ajudar o suporte do Azure com problemas de depuração.

    Para além do que precede, recomenda-se *a remoção* dos seguintes parâmetros:

    ```console
    rhgb quiet crashkernel=auto
    ```

    A bota gráfica e tranquila não é útil num ambiente de nuvem onde queremos que todos os troncos sejam enviados para a porta de série.  A `crashkernel` opção pode ser deixada configurada se desejar, mas note que este parâmetro reduzirá a quantidade de memória disponível no VM em 128MB ou mais, o que pode ser problemático nos tamanhos de VM menores.

    > [!Important]
    > CentOS 6.5 e anteriormente devem também `numa=off`definir o parâmetro do núcleo . Ver Chapéu Vermelho [KB 436883](https://access.redhat.com/solutions/436883).

14. Certifique-se de que o servidor SSH está instalado e configurado para iniciar no momento do arranque.  Este é geralmente o padrão.

15. Não crie espaço de troca no disco OS.

    O Agente Azure Linux pode configurar automaticamente o espaço de troca utilizando o disco de recursos locais que está ligado ao VM após o fornecimento no Azure. Note que o disco de recursos locais é um disco *temporário,* e pode ser esvaziado quando o VM é desprovisionado. Depois de instalar o Agente Azure Linux (ver passo `/etc/waagent.conf` anterior), modifique adequadamente os seguintes parâmetros:

    ```console
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048 ## NOTE: set this to whatever you need it to be.
    ```

16. Executar os seguintes comandos para desprovisionar a máquina virtual e prepará-la para o fornecimento em Azure:

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

17. Clique em **Ação -> desligar** em Hiper-V Manager. O seu VHD Linux está agora pronto para ser enviado para o Azure.



## <a name="centos-70"></a>Centos 7.0+

**Alterações no CentOS 7 (e derivados semelhantes)**

Preparar uma máquina virtual CentOS 7 para o Azure é muito semelhante ao CentOS 6, no entanto existem várias diferenças importantes que merecem ser observadas:

* O pacote NetworkManager já não entra em conflito com o agente Azure Linux. Esta embalagem é instalada por defeito e recomendamos que não seja removida.
* O GRUB2 é agora utilizado como bootloader predefinido, pelo que o procedimento de edição dos parâmetros do kernel mudou (ver abaixo).
* XFS é agora o sistema de ficheiros padrão. O sistema de ficheiros ext4 ainda pode ser utilizado se desejar.

**Passos de configuração**

1. No Hyper-V Manager, selecione a máquina virtual.

2. Clique em **Ligar** para abrir uma janela de consola para a máquina virtual.

3. Criar ou editar `/etc/sysconfig/network` o ficheiro e adicionar o seguinte texto:

    ```console
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

4. Criar ou editar `/etc/sysconfig/network-scripts/ifcfg-eth0` o ficheiro e adicionar o seguinte texto:

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

5. Modificar as regras udev para evitar gerar regras estáticas para a interface Ethernet. Estas regras podem causar problemas ao clonar uma máquina virtual no Microsoft Azure ou Hyper-V:

    ```bash
    sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    ```

6. Se quiser utilizar os espelhos OpenLogic que estão alojados nos datacenters do Azure, substitua o `/etc/yum.repos.d/CentOS-Base.repo` ficheiro com os seguintes repositórios.  Isto também irá adicionar o repositório **[openlogic]** que inclui pacotes para o agente Azure Linux:

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
   > O resto deste guia assumirá que `[openlogic]` está a utilizar pelo menos o repo, que será utilizado para instalar o agente Azure Linux abaixo.

7. Executar o seguinte comando para limpar os metadados yum atuais e instalar quaisquer atualizações:

    ```bash
    sudo yum clean all
    ```

    A menos que esteja a criar uma imagem para uma versão mais antiga do CentOS, recomenda-se atualizar todos os pacotes para as mais recentes:

    ```bash
    sudo yum -y update
    ```

    Um reboot talvez necessário depois de dirigir este comando.

8. Modifique a linha de arranque do núcleo na sua configuração de larva para incluir parâmetros adicionais de kernel para o Azure. Para isso, `/etc/default/grub` abra num editor de `GRUB_CMDLINE_LINUX` texto e edite o parâmetro, por exemplo:

    ```console
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Isto também garantirá que todas as mensagens de consola são enviadas para a primeira porta em série, que pode ajudar o suporte do Azure com problemas de depuração. Também desliga as novas convenções de nomeação centos7 para NICs. Para além do que precede, recomenda-se *a remoção* dos seguintes parâmetros:

    ```console
    rhgb quiet crashkernel=auto
    ```

    A bota gráfica e tranquila não é útil num ambiente de nuvem onde queremos que todos os troncos sejam enviados para a porta de série. A `crashkernel` opção pode ser deixada configurada se desejar, mas note que este parâmetro reduzirá a quantidade de memória disponível no VM em 128MB ou mais, o que pode ser problemático nos tamanhos de VM menores.

9. Uma vez terminada `/etc/default/grub` a edição por cima, execute o seguinte comando para reconstruir a configuração da larva:

    ```bash
    sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

10. Se construir a imagem a partir de **VMware, VirtualBox ou KVM:** Certifique-se de que os controladores Hyper-V estão incluídos nos initramfs:

    Editar, `/etc/dracut.conf`adicionar conteúdo:

    ```console
    add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
    ```

    Reconstruir os initramfs:

    ```bash
    sudo dracut -f -v
    ```

11. Instale o Agente Azure Linux e dependências:

    ```bash
    sudo yum install python-pyasn1 WALinuxAgent
    sudo systemctl enable waagent
    ```

12. Não crie espaço de troca no disco OS.

    O Agente Azure Linux pode configurar automaticamente o espaço de troca utilizando o disco de recursos locais que está ligado ao VM após o fornecimento no Azure. Note que o disco de recursos locais é um disco *temporário,* e pode ser esvaziado quando o VM é desprovisionado. Depois de instalar o Agente Azure Linux (ver passo `/etc/waagent.conf` anterior), modifique adequadamente os seguintes parâmetros:

    ```console
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

13. Executar os seguintes comandos para desprovisionar a máquina virtual e prepará-la para o fornecimento em Azure:

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

14. Clique em **Ação -> desligar** em Hiper-V Manager. O seu VHD Linux está agora pronto para ser enviado para o Azure.

## <a name="next-steps"></a>Passos seguintes

Está agora pronto para usar o seu disco rígido virtual CentOS Linux para criar novas máquinas virtuais em Azure. Se esta for a primeira vez que está a enviar o ficheiro .vhd para o Azure, consulte [Create a Linux VM a partir de um disco personalizado](upload-vhd.md#option-1-upload-a-vhd).
