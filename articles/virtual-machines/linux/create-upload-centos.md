---
title: Criar e carregar um VHD do Linux baseado em CentOS no Azure
description: Aprenda a criar e carregar um VHD (disco rígido virtual) do Azure que contém um sistema operacional Linux baseado em CentOS.
services: virtual-machines-linux
documentationcenter: ''
author: MicahMcKittrick-MSFT
manager: gwallace
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 11/25/2019
ms.author: mimckitt
ms.openlocfilehash: a5a6dd7e451d3204c8ab2bd2eae95db93c2f0837
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750237"
---
# <a name="prepare-a-centos-based-virtual-machine-for-azure"></a>Preparar uma máquina virtual baseada em CentOS para o Azure

Aprenda a criar e carregar um VHD (disco rígido virtual) do Azure que contém um sistema operacional Linux baseado em CentOS.

* [Preparar uma máquina virtual CentOS 6. x para o Azure](#centos-6x)
* [Preparar uma máquina virtual CentOS 7.0 + para o Azure](#centos-70)


## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você já tenha instalado um sistema operacional Linux CentOS (ou derivativo semelhante) para um disco rígido virtual. Existem várias ferramentas para criar arquivos. VHD, por exemplo, uma solução de virtualização, como o Hyper-V. Para obter instruções, consulte [instalar a função Hyper-V e configurar uma máquina virtual](https://technet.microsoft.com/library/hh846766.aspx).

**Notas de instalação do CentOS**

* Consulte também as [notas de instalação gerais do Linux](create-upload-generic.md#general-linux-installation-notes) para obter mais dicas sobre como preparar o Linux para o Azure.
* Não há suporte para o formato VHDX no Azure, somente **VHD fixo**.  Você pode converter o disco para o formato VHD usando o Gerenciador do Hyper-V ou o cmdlet Convert-VHD. Se você estiver usando VirtualBox, isso significa selecionar **tamanho fixo** em oposição ao padrão alocado dinamicamente ao criar o disco.
* Ao instalar o sistema Linux, é *recomendável* que você use partições padrão em vez de LVM (geralmente o padrão para muitas instalações). Isso evitará conflitos de nome LVM com VMs clonadas, especialmente se um disco do sistema operacional precisar ser anexado a outra VM idêntica para solução de problemas. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) podem ser usados em discos de dados.
* É necessário suporte de kernel para montar sistemas de arquivos UDF. Na primeira inicialização no Azure, a configuração de provisionamento é passada para a VM do Linux por meio da mídia formatada por UDF que está anexada ao convidado. O agente Linux do Azure deve ser capaz de montar o sistema de arquivos UDF para ler sua configuração e provisionar a VM.
* As versões do kernel do Linux abaixo do 2.6.37 não dão suporte a NUMA no Hyper-V com tamanhos de VM maiores. Esse problema afeta principalmente as distribuições mais antigas usando o kernel upstream Red Hat 2.6.32 e foi corrigido no RHEL 6,6 (kernel-2.6.32-504). Sistemas que executam kernels personalizados com mais de 2.6.37 ou kernels baseados em RHEL mais antigos que 2.6.32-504 devem definir o parâmetro de inicialização `numa=off` na linha de comando do kernel em grub. conf. Para obter mais informações, consulte Red Hat [KB 436883](https://access.redhat.com/solutions/436883).
* Não configure uma partição de permuta no disco do sistema operacional. O agente do Linux pode ser configurado para criar um arquivo de permuta no disco de recursos temporário.  Mais informações sobre isso podem ser encontradas nas etapas abaixo.
* Todos os VHDs no Azure devem ter um tamanho virtual alinhado a 1 MB. Ao converter de um disco bruto para VHD, você deve garantir que o tamanho do disco bruto seja um múltiplo de 1MB antes da conversão. Consulte [notas de instalação do Linux](create-upload-generic.md#general-linux-installation-notes) para obter mais informações.

## <a name="centos-6x"></a>CentOS 6. x

1. No Gerenciador do Hyper-V, selecione a máquina virtual.

2. Clique em **conectar** para abrir uma janela de console para a máquina virtual.

3. No CentOS 6, o NetworkManager pode interferir no agente Linux do Azure. Desinstale este pacote executando o seguinte comando:

    ```bash
    sudo rpm -e --nodeps NetworkManager
    ```

4. Crie ou edite o arquivo `/etc/sysconfig/network` e adicione o seguinte texto:

    ```console
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

5. Crie ou edite o arquivo `/etc/sysconfig/network-scripts/ifcfg-eth0` e adicione o seguinte texto:

    ```console
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

6. Modifique as regras de udev para evitar a geração de regras estáticas para as interfaces de Ethernet. Essas regras podem causar problemas ao clonar uma máquina virtual no Microsoft Azure ou no Hyper-V:

    ```bash
    sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

7. Verifique se o serviço de rede será iniciado no momento da inicialização executando o seguinte comando:

    ```bash
    sudo chkconfig network on
    ```

8. Se você quiser usar os espelhos do OpenLogic que estão hospedados nos data centers do Azure, substitua o arquivo `/etc/yum.repos.d/CentOS-Base.repo` pelos repositórios a seguir.  Isso também adicionará o repositório **[openlogic]** que inclui pacotes adicionais, como o agente Linux do Azure:

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
    > O restante deste guia pressupõe que você esteja usando pelo menos o repositório `[openlogic]`, que será usado para instalar o agente Linux do Azure abaixo.

9. Adicione a seguinte linha a/etc/yum.conf:

    ```console
    http_caching=packages
    ```

10. Execute o comando a seguir para limpar os metadados atuais do yum e atualizar o sistema com os pacotes mais recentes:

    ```bash
    yum clean all
    ```

    A menos que você esteja criando uma imagem para uma versão mais antiga do CentOS, é recomendável atualizar todos os pacotes para o mais recente:

    ```bash
    sudo yum -y update
    ```

    Uma reinicialização pode ser necessária após a execução desse comando.

11. Adicional Instale os drivers para o Integration Services do Linux (LIS).

    > [!IMPORTANT]
    > A etapa é **necessária** para centos 6,3 e anterior e opcional para versões posteriores.

    ```bash
    sudo rpm -e hypervkvpd  ## (may return error if not installed, that's OK)
    sudo yum install microsoft-hyper-v
    ```

    Como alternativa, você pode seguir as instruções de instalação manual na [página de download do LIS](https://www.microsoft.com/download/details.aspx?id=51612) para instalar o RPM em sua VM.

12. Instale o agente Linux do Azure e as dependências. Iniciar e habilitar o serviço waagent:

    ```bash
    sudo yum install python-pyasn1 WALinuxAgent
    sudo service waagent start
    sudo chkconfig waagent on
    ```


    O pacote WALinuxAgent removerá os pacotes NetworkManager e NetworkManager-gnome se eles ainda não tiverem sido removidos, conforme descrito na etapa 3.

13. Modifique a linha de inicialização do kernel em sua configuração do grub para incluir parâmetros de kernel adicionais para o Azure. Para fazer isso, abra `/boot/grub/menu.lst` em um editor de texto e verifique se o kernel padrão inclui os seguintes parâmetros:

    ```console
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

    Isso também garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, o que pode auxiliar o suporte do Azure com problemas de depuração.

    Além do que está acima, é recomendável *remover* os seguintes parâmetros:

    ```console
    rhgb quiet crashkernel=auto
    ```

    A inicialização gráfica e silenciosa não são úteis em um ambiente de nuvem onde queremos que todos os logs sejam enviados para a porta serial.  A opção `crashkernel` pode ser deixada configurada se desejado, mas observe que esse parâmetro reduzirá a quantidade de memória disponível na VM por 128MB ou mais, o que pode ser um problema nos tamanhos de VM menores.

    > [!Important]
    > O CentOS 6,5 e versões anteriores também devem definir o parâmetro de kernel `numa=off`. Consulte Red Hat [KB 436883](https://access.redhat.com/solutions/436883).

14. Verifique se o servidor SSH está instalado e configurado para iniciar no momento da inicialização.  Normalmente, isso é o padrão.

15. Não crie espaço de permuta no disco do sistema operacional.

    O agente Linux do Azure pode configurar automaticamente o espaço de permuta usando o disco de recurso local que é anexado à VM após o provisionamento no Azure. Observe que o disco de recurso local é um disco *temporário* e pode ser esvaziado quando a VM é desprovisionada. Depois de instalar o agente Linux do Azure (consulte a etapa anterior), modifique os seguintes parâmetros no `/etc/waagent.conf` adequadamente:

    ```console
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048 ## NOTE: set this to whatever you need it to be.
    ```

16. Execute os seguintes comandos para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

17. Clique em **ação-> desligar** no Gerenciador do Hyper-V. Seu VHD do Linux agora está pronto para ser carregado no Azure.



## <a name="centos-70"></a>CentOS 7.0 +

**Alterações no CentOS 7 (e derivativos semelhantes)**

Preparar uma máquina virtual CentOS 7 para o Azure é muito semelhante ao CentOS 6, no entanto, há várias diferenças importantes que vale a pena observar:

* O pacote NetworkManager não está mais em conflito com o agente Linux do Azure. Esse pacote é instalado por padrão e recomendamos que ele não seja removido.
* GRUB2 agora é usado como o carregador de estado padrão, portanto, o procedimento para editar parâmetros de kernel foi alterado (veja abaixo).
* XFS agora é o sistema de arquivos padrão. O sistema de arquivos EXT4 ainda pode ser usado se desejado.

**Etapas de configuração**

1. No Gerenciador do Hyper-V, selecione a máquina virtual.

2. Clique em **conectar** para abrir uma janela de console para a máquina virtual.

3. Crie ou edite o arquivo `/etc/sysconfig/network` e adicione o seguinte texto:

    ```console
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

4. Crie ou edite o arquivo `/etc/sysconfig/network-scripts/ifcfg-eth0` e adicione o seguinte texto:

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

5. Modifique as regras de udev para evitar a geração de regras estáticas para as interfaces de Ethernet. Essas regras podem causar problemas ao clonar uma máquina virtual no Microsoft Azure ou no Hyper-V:

    ```bash
    sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    ```

6. Se você quiser usar os espelhos do OpenLogic que estão hospedados nos data centers do Azure, substitua o arquivo `/etc/yum.repos.d/CentOS-Base.repo` pelos repositórios a seguir.  Isso também adicionará o repositório **[openlogic]** , que inclui pacotes para o agente Linux do Azure:

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
   > O restante deste guia pressupõe que você esteja usando pelo menos o repositório `[openlogic]`, que será usado para instalar o agente Linux do Azure abaixo.

7. Execute o comando a seguir para limpar os metadados atuais do yum e instalar as atualizações:

    ```bash
    sudo yum clean all
    ```

    A menos que você esteja criando uma imagem para uma versão mais antiga do CentOS, é recomendável atualizar todos os pacotes para o mais recente:

    ```bash
    sudo yum -y update
    ```

    Uma reinicialização talvez seja necessária após a execução deste comando.

8. Modifique a linha de inicialização do kernel em sua configuração do grub para incluir parâmetros de kernel adicionais para o Azure. Para fazer isso, abra `/etc/default/grub` em um editor de texto e edite o parâmetro `GRUB_CMDLINE_LINUX`, por exemplo:

    ```console
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Isso também garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, o que pode auxiliar o suporte do Azure com problemas de depuração. Ele também desativa as novas convenções de nomenclatura do CentOS 7 para NICs. Além do que está acima, é recomendável *remover* os seguintes parâmetros:

    ```console
    rhgb quiet crashkernel=auto
    ```

    A inicialização gráfica e silenciosa não são úteis em um ambiente de nuvem onde queremos que todos os logs sejam enviados para a porta serial. A opção `crashkernel` pode ser deixada configurada se desejado, mas observe que esse parâmetro reduzirá a quantidade de memória disponível na VM por 128MB ou mais, o que pode ser um problema nos tamanhos de VM menores.

9. Quando terminar de editar `/etc/default/grub` por acima, execute o seguinte comando para recompilar a configuração do grub:

    ```bash
    sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

10. Se estiver criando a imagem do **VMware, VirtualBox ou KVM:** Verifique se os drivers do Hyper-V estão incluídos no initramfs:

    Editar `/etc/dracut.conf`, adicionar conteúdo:

    ```console
    add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
    ```

    Reconstrua o initramfs:

    ```bash
    sudo dracut -f -v
    ```

11. Instale o agente Linux do Azure e as dependências:

    ```bash
    sudo yum install python-pyasn1 WALinuxAgent
    sudo systemctl enable waagent
    ```

12. Não crie espaço de permuta no disco do sistema operacional.

    O agente Linux do Azure pode configurar automaticamente o espaço de permuta usando o disco de recurso local que é anexado à VM após o provisionamento no Azure. Observe que o disco de recurso local é um disco *temporário* e pode ser esvaziado quando a VM é desprovisionada. Depois de instalar o agente Linux do Azure (consulte a etapa anterior), modifique os seguintes parâmetros no `/etc/waagent.conf` adequadamente:

    ```console
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

13. Execute os seguintes comandos para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

14. Clique em **ação-> desligar** no Gerenciador do Hyper-V. Seu VHD do Linux agora está pronto para ser carregado no Azure.

## <a name="next-steps"></a>Passos seguintes

Agora você está pronto para usar seu disco rígido virtual CentOS Linux para criar novas máquinas virtuais no Azure. Se esta for a primeira vez que você está carregando o arquivo. vhd no Azure, consulte [criar uma VM do Linux a partir de um disco personalizado](upload-vhd.md#option-1-upload-a-vhd).
