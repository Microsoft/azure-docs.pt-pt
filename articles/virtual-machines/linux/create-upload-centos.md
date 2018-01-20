---
title: Criar e carregar um VHD para baseado em CentOS Linux no Azure
description: "Saiba como criar e carregar um Azure disco rígido virtual (VHD) que contém um sistema operativo baseado em CentOS Linux."
services: virtual-machines-linux
documentationcenter: 
author: szarkos
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 0e518e92-e981-43f4-b12c-9cba1064c4bb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: szark
ms.openlocfilehash: e003d31b42c7a9518668fb8ddfb6accb9c158c90
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2018
---
# <a name="prepare-a-centos-based-virtual-machine-for-azure"></a>Preparar uma máquina virtual baseada em CentOS para o Azure
* [Preparar uma máquina de virtual do CentOS 6. x para o Azure](#centos-6x)
* [Preparar uma máquina virtual do CentOS 7.0 + Azure](#centos-70)

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que já tem instalado um CentOS (ou semelhante derivativo) do sistema operativo Linux um disco rígido virtual. Existem várias ferramentas para criar ficheiros. vhd, por exemplo uma solução de virtualização, tais como o Hyper-V. Para obter instruções, consulte [instalar a função Hyper-V e configurar uma Máquina Virtual](http://technet.microsoft.com/library/hh846766.aspx).

**Notas de instalação do centOS**

* Consulte também [geral notas de instalação do Linux](create-upload-generic.md#general-linux-installation-notes) para mais sugestões no preparar Linux para o Azure.
* O formato VHDX não é suportado no Azure, apenas **fixo VHD**.  Pode converter o disco para o formato VHD utilizando o Gestor de Hyper-V ou o cmdlet convert-vhd. Se estiver a utilizar VirtualBox significa que a seleção **um tamanho fixo** , por oposição a predefinição dinamicamente atribuída ao criar o disco.
* Ao instalar o sistema Linux é *recomendado* que utilize partições padrão em vez de LVM (muitas vezes, a predefinição para instalações muitos). Evitará LVM nome entra em conflito com VMs Clonadas, particularmente se um disco de SO alguma vez precisar de ser ligado a outra VM idêntico para resolução de problemas. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) pode ser utilizado em discos de dados.
* É necessário suporte de kernel para montar a sistemas de ficheiros UDF. No primeiro arranque no Azure a configuração de aprovisionamento é transmitida para a VM com Linux através de multimédia formatado UDF ligada para o convidado. O agente Linux do Azure tem de ser capaz de montar o sistema de ficheiros UDF ler a configuração e aprovisionar a VM.
* Versões de kernel do Linux abaixo 2.6.37 não suportam no Hyper-V com tamanhos de VM maiores. Isto emitir principalmente impactos distribuições mais antigas utilizando o montante kernel do Red Hat 2.6.32 e foi corrigido em RHEL 6.6 (kernel-2.6.32-504). Sistemas com kernels personalizados anteriores 2.6.37 ou baseado em RHEL kernels mais antigos que 2.6.32-504 tem de definir o parâmetro de arranque `numa=off` no kernel da linha de comandos no grub.conf. Para obter mais informações consulte Red Hat [KB 436883](https://access.redhat.com/solutions/436883).
* Não configure uma partição de comutação no disco do SO. O agente Linux pode ser configurado para criar um ficheiro de comutação no disco de recursos temporário.  Podem encontrar mais informações sobre esta nos passos abaixo.
* Todos os VHDs têm de ter tamanhos que estão em múltiplos de 1 MB.

## <a name="centos-6x"></a>CentOS 6.x

1. No Gestor de Hyper-V, selecione a máquina virtual.

2. Clique em **Connect** para abrir uma janela da consola da máquina virtual.

3. No 6 do CentOS, NetworkManager pode interferir com o agente Linux do Azure. Desinstale este pacote, executando o seguinte comando:
   
        # sudo rpm -e --nodeps NetworkManager

4. Criar ou editar o ficheiro `/etc/sysconfig/network` e adicione o seguinte texto:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5. Criar ou editar o ficheiro `/etc/sysconfig/network-scripts/ifcfg-eth0` e adicione o seguinte texto:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6. Modificar regras de udev para evitar a geração de regras estáticas para a interfaces Ethernet. Estas regras podem causar problemas ao clonar uma máquina virtual no Microsoft Azure ou o Hyper-v:
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

7. Certifique-se de que o serviço de rede será iniciada no momento do arranque executando o seguinte comando:
   
        # sudo chkconfig network on

8. Se gostaria de utilizar os espelhos OpenLogic que estão alojados dentro dos centros de dados do Azure, em seguida, substituem o `/etc/yum.repos.d/CentOS-Base.repo` ficheiro com os repositórios do seguintes.  Também irá adicionar o **[openlogic]** repositório inclui pacotes adicionais, tais como o agente Linux do Azure:

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

    >[!Note]
    O resto deste guia irá partem do princípio de que está a utilizar, pelo menos, o `[openlogic]` repositório, que será utilizado para instalar o agente Linux do Azure abaixo.


9. Adicione a seguinte linha para /etc/yum.conf:
    
        http_caching=packages

10. Execute o seguinte comando para limpar os metadados de yum atual e atualizar o sistema com os mais recentes pacotes:
    
        # yum clean all

    A menos que estiver a criar uma imagem para uma versão antiga do CentOS, recomenda-se a todos os pacotes de atualização para o versão mais recente:

        # sudo yum -y update

    Reiniciar o computador poderá ser necessário depois de executar este comando.

11. (Opcional) Instale os controladores de serviços de integração Linux (LIS).
   
    >[!IMPORTANT]
    O passo é **necessário** para CentOS 6.3 e anteriores e opcionais para versões posteriores.

        # sudo rpm -e hypervkvpd  ## (may return error if not installed, that's OK)
        # sudo yum install microsoft-hyper-v

    Em alternativa, pode seguir as instruções de instalação manual no [página de transferência de LIS](https://go.microsoft.com/fwlink/?linkid=403033) para instalar o RPM para a VM.
 
12. Instale o agente Linux do Azure e as dependências:
    
        # sudo yum install python-pyasn1 WALinuxAgent
    
    O pacote de WALinuxAgent removerá o NetworkManager e pacotes de NetworkManager gnome se estes não foram já removidas conforme descrito no passo 3.


13. Modifique a linha de arranque de kernel na configuração grub para incluir parâmetros de kernel adicionais para o Azure. Para tal, abra `/boot/grub/menu.lst` num editor de texto e certifique-se de que o kernel predefinida inclui os seguintes parâmetros:
    
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    
    Isto também vai assegurar todas as mensagens de consola são enviadas para a primeira porta série, que pode ajudar a Azure suporte com problemas de depuração.
    
    Para além dos acima, é recomendado *remover* os seguintes parâmetros:
    
        rhgb quiet crashkernel=auto
    
    Arranque gráfica e silenciosos não são úteis num ambiente de nuvem em que pretende é que todos os registos sejam enviados para a porta série.  O `crashkernel` opção pode ser esquerda configurado se assim o desejar, mas tenha em atenção que este parâmetro irá reduzir a quantidade de memória disponível na VM 128 MB ou mais, que pode ser problemático nos tamanhos VM mais pequenos.

    >[!Important]
    CentOS 6.5 e anteriores também tem de definir o parâmetro de kernel `numa=off`. Consulte Red Hat [KB 436883](https://access.redhat.com/solutions/436883).

14. Certifique-se de que o servidor SSH está instalado e configurado para iniciar no momento do arranque.  Normalmente, esta é a predefinição.

15. Não crie o espaço de comutação no disco do SO.
    
    O agente Linux do Azure podem configurar automaticamente o espaço de comutação com o disco de recurso local que está ligado à VM após o aprovisionamento no Azure. Tenha em atenção que o disco de recurso local é um *temporário* disco e poderá ser esvaziada quando a VM é desaprovisionada. Depois de instalar o agente Linux do Azure (consulte o passo anterior), modifique os parâmetros seguintes na `/etc/waagent.conf` corretamente:
    
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

16. Execute os seguintes comandos para retirar o aprovisionamento da máquina virtual e prepará-la para o aprovisionamento no Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

17. Clique em **ação -> encerrar baixo** no Gestor de Hyper-V. O VHD de Linux está agora pronto para ser carregado para o Azure.


- - -
## <a name="centos-70"></a>CentOS 7.0 +
**Alterações no CentOS 7 (e derivados semelhantes)**

Preparar uma máquina virtual do CentOS 7 para o Azure é muito semelhante à CentOS 6, no entanto, existem várias diferenças importantes vale a pena realçar:

* O pacote de NetworkManager já não está em conflito com o agente Linux do Azure. Este pacote é instalado por predefinição e recomendamos que não é removida.
* GRUB2 agora é utilizado como bootloader predefinido, para que o procedimento para editar os parâmetros de kernel foi alterada (ver abaixo).
* XFS agora é o sistema de ficheiros predefinido. O sistema de ficheiros ext4 ainda pode ser utilizado se assim o desejar.

**Passos de configuração**

1. No Gestor de Hyper-V, selecione a máquina virtual.

2. Clique em **Connect** para abrir uma janela da consola da máquina virtual.

3. Criar ou editar o ficheiro `/etc/sysconfig/network` e adicione o seguinte texto:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

4. Criar ou editar o ficheiro `/etc/sysconfig/network-scripts/ifcfg-eth0` e adicione o seguinte texto:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

5. Modificar regras de udev para evitar a geração de regras estáticas para a interfaces Ethernet. Estas regras podem causar problemas ao clonar uma máquina virtual no Microsoft Azure ou o Hyper-v:
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

6. Se gostaria de utilizar os espelhos OpenLogic que estão alojados dentro dos centros de dados do Azure, em seguida, substituem o `/etc/yum.repos.d/CentOS-Base.repo` ficheiro com os repositórios do seguintes.  Também irá adicionar o **[openlogic]** repositório inclui pacotes para o agente Linux do Azure:
   
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

    >[!Note]
    O resto deste guia irá partem do princípio de que está a utilizar, pelo menos, o `[openlogic]` repositório, que será utilizado para instalar o agente Linux do Azure abaixo.

7. Execute o seguinte comando para limpar os metadados de yum atual e instale as atualizações:
   
        # sudo yum clean all

    A menos que estiver a criar uma imagem para uma versão antiga do CentOS, recomenda-se a todos os pacotes de atualização para o versão mais recente:

        # sudo yum -y update

    Reiniciar o computador pode ser necessário depois de executar este comando.

8. Modifique a linha de arranque de kernel na configuração grub para incluir parâmetros de kernel adicionais para o Azure. Para tal, abra `/etc/default/grub` num editor de texto e editar o `GRUB_CMDLINE_LINUX` parâmetro, por exemplo:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Isto também vai assegurar todas as mensagens de consola são enviadas para a primeira porta série, que pode ajudar a Azure suporte com problemas de depuração. É também desativa as convenções de nomenclatura do CentOS 7 novo para NICs. Para além dos acima, é recomendado *remover* os seguintes parâmetros:
   
        rhgb quiet crashkernel=auto
   
    Arranque gráfica e silenciosos não são úteis num ambiente de nuvem em que pretende é que todos os registos sejam enviados para a porta série. O `crashkernel` opção pode ser esquerda configurado se assim o desejar, mas tenha em atenção que este parâmetro irá reduzir a quantidade de memória disponível na VM 128 MB ou mais, que pode ser problemático nos tamanhos VM mais pequenos.

9. Quando tiver terminado edição `/etc/default/grub` por acima, execute o seguinte comando para reconstruir a configuração de grub:
   
        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

10. Se a criação da imagem de **VMWare, VirtualBox ou KVM:** controladores Certifique-se do Hyper-V estão incluídos no initramfs:
   
   Editar `/etc/dracut.conf`, adicionar conteúdo:
   
        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”
   
   Reconstrua o initramfs:
   
        # sudo dracut –f -v

11. Instale o agente Linux do Azure e as dependências:

        # sudo yum install python-pyasn1 WALinuxAgent
        # sudo systemctl enable waagent

12. Não crie o espaço de comutação no disco do SO.
   
   O agente Linux do Azure podem configurar automaticamente o espaço de comutação com o disco de recurso local que está ligado à VM após o aprovisionamento no Azure. Tenha em atenção que o disco de recurso local é um *temporário* disco e poderá ser esvaziada quando a VM é desaprovisionada. Depois de instalar o agente Linux do Azure (consulte o passo anterior), modifique os parâmetros seguintes na `/etc/waagent.conf` corretamente:
   
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13. Execute os seguintes comandos para retirar o aprovisionamento da máquina virtual e prepará-la para o aprovisionamento no Azure:
   
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

14. Clique em **ação -> encerrar baixo** no Gestor de Hyper-V. O VHD de Linux está agora pronto para ser carregado para o Azure.

## <a name="next-steps"></a>Passos Seguintes
Agora, está pronto a utilizar o seu disco rígido virtual do CentOS Linux para criar novas máquinas virtuais no Azure. Se esta for a primeira vez que está a carregar o ficheiro. vhd para o Azure, consulte [criar e carregar um disco rígido virtual que contém o sistema operativo Linux](upload-vhd.md).

