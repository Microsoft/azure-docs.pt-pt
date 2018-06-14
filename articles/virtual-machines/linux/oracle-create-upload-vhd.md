---
title: Criar e carregar um VHD do Oracle Linux | Microsoft Docs
description: Saiba como criar e carregar um Azure disco rígido virtual (VHD) que contenha um sistema de operativo Oracle Linux.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: tysonn
tags: azure-service-management,azure-resource-manager
ms.assetid: dd96f771-26eb-4391-9a89-8c8b6d691822
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: szark
ms.openlocfilehash: ca556b493740d66922fbdd96bac45114b1b9b5c0
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2018
ms.locfileid: "30909331"
---
# <a name="prepare-an-oracle-linux-virtual-machine-for-azure"></a>Preparar uma máquina virtual do Oracle Linux para o Azure
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que já tem instalado um sistema de operativo Oracle Linux para um disco rígido virtual. Existem várias ferramentas para criar ficheiros. vhd, por exemplo uma solução de virtualização, tais como o Hyper-V. Para obter instruções, consulte [instalar a função Hyper-V e configurar uma Máquina Virtual](http://technet.microsoft.com/library/hh846766.aspx).

### <a name="oracle-linux-installation-notes"></a>Notas de instalação do Oracle Linux
* Consulte também [geral notas de instalação do Linux](create-upload-generic.md#general-linux-installation-notes) para mais sugestões no preparar Linux para o Azure.
* Kernel compatível do Red Hat do Oracle e os respetivos UEK3 (Unbreakable Enterprise Kernel) são ambas suportadas no Hyper-V e do Azure. Para obter os melhores resultados,. não se esqueça de atualizar para o kernel mais recente ao preparar o VHD do Oracle Linux.
* UEK2 do Oracle não é suportado no Hyper-V e o Azure, não inclua os controladores necessários.
* O formato VHDX não é suportado no Azure, apenas **fixo VHD**.  Pode converter o disco para o formato VHD utilizando o Gestor de Hyper-V ou o cmdlet convert-vhd.
* Ao instalar o sistema Linux é recomendado que utilize partições padrão em vez de LVM (muitas vezes, a predefinição para instalações muitos). Evitará LVM nome entra em conflito com VMs Clonadas, particularmente se um disco de SO alguma vez precisar de ser ligado a outra VM para resolução de problemas. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) pode ser utilizado em discos de dados se preferencial.
* NUMA não é suportada para tamanhos de VM maiores resultam de erros nas versões anteriores de kernel do Linux ao 2.6.37. Este problema afeta principalmente distribuições utilizando o montante kernel do Red Hat 2.6.32. Instalação manual do agente Linux do Azure (waagent) desativará automaticamente na configuração GRUB para o kernel do Linux. Podem encontrar mais informações sobre esta nos passos abaixo.
* Não configure uma partição de comutação no disco do SO. O agente Linux pode ser configurado para criar um ficheiro de comutação no disco de recursos temporário.  Podem encontrar mais informações sobre esta nos passos abaixo.
* Todos os VHDs no Azure tem de ter um tamanho virtual alinhado com 1MB. Ao converter de um disco não processado para o VHD tem de se certificar de que o tamanho do disco não processados é um múltiplo de 1MB antes de conversão. Consulte [Linux instalação notas](create-upload-generic.md#general-linux-installation-notes) para obter mais informações.
* Certifique-se de que o `Addons` repositório está ativado. Edite o ficheiro `/etc/yum.repo.d/public-yum-ol6.repo`(Oracle Linux 6) ou `/etc/yum.repo.d/public-yum-ol7.repo`(Oracle Linux) e altere a linha `enabled=0` para `enabled=1` em **[ol6_addons]** ou **[ol7_addons]** neste ficheiro.

## <a name="oracle-linux-64"></a>Oracle Linux 6.4 +
Tem de concluir os passos de configuração específicos no sistema operativo da máquina virtual ser executada no Azure.

1. No painel central do Gestor de Hyper-V, selecione a máquina virtual.
2. Clique em **Connect** para abrir a janela para a máquina virtual.
3. Desinstale NetworkManager executando o seguinte comando:
   
        # sudo rpm -e --nodeps NetworkManager
   
    **Nota:** se o pacote já não estiver instalado, este comando irá falhar com uma mensagem de erro. Isto é esperado.
4. Crie um ficheiro denominado **rede** no `/etc/sysconfig/` diretório que contém o seguinte texto:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain
5. Crie um ficheiro denominado **ifcfg eth0** no `/etc/sysconfig/network-scripts/` diretório que contém o seguinte texto:
   
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
   
        # chkconfig network on
8. Instale o python pyasn1 executando o seguinte comando:
   
        # sudo yum install python-pyasn1
9. Modifique a linha de arranque de kernel na configuração grub para incluir parâmetros de kernel adicionais para o Azure. Para fazer este open "/ boot/grub/menu.lst" num editor de texto e certifique-se de que o kernel predefinida inclui os seguintes parâmetros:
   
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off
   
   Isto também vai assegurar todas as mensagens de consola são enviadas para a primeira porta série, que pode ajudar a Azure suporte com problemas de depuração. Este procedimento desactivará NUMA devido a um erro no kernel compatível do Red Hat do Oracle.
   
   Para além dos acima, é recomendado *remover* os seguintes parâmetros:
   
        rhgb quiet crashkernel=auto
   
   Arranque gráfica e silenciosos não são úteis num ambiente de nuvem em que pretende é que todos os registos sejam enviados para a porta série.
   
   O `crashkernel` opção pode ser esquerda configurado se assim o desejar, mas tenha em atenção que este parâmetro irá reduzir a quantidade de memória disponível na VM 128 MB ou mais, que pode ser problemático nos tamanhos VM mais pequenos.
10. Certifique-se de que o servidor SSH está instalado e configurado para iniciar no momento do arranque.  Normalmente, esta é a predefinição.
11. Instale o agente Linux do Azure executando o seguinte comando. A versão mais recente é 2.0.15.
    
        # sudo yum install WALinuxAgent
    
    Tenha em atenção que a instalação do pacote de WALinuxAgent removerá o NetworkManager e pacotes NetworkManager gnome se estes não foram já removidas conforme descrito no passo 2.
12. Não crie o espaço de comutação no disco do SO.
    
    O agente Linux do Azure podem configurar automaticamente o espaço de comutação com o disco de recurso local que está ligado à VM após o aprovisionamento no Azure. Tenha em atenção que o disco de recurso local é um *temporário* disco e poderá ser esvaziada quando a VM é desaprovisionada. Depois de instalar o agente Linux do Azure (consulte o passo anterior), modifique os parâmetros seguintes /etc/waagent.conf conforme apropriado:
    
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

- - -
## <a name="oracle-linux-70"></a>Oracle Linux 7.0 +
**Alterações no Oracle Linux 7**

Preparar uma máquina virtual do Oracle Linux 7 para o Azure é muito semelhante à Oracle Linux 6, no entanto, existem várias diferenças importantes vale a pena realçar:

* O kernel do Red Hat compatível e UEK3 do Oracle são suportadas no Azure.  Recomenda-se o kernel UEK3.
* O pacote de NetworkManager já não está em conflito com o agente Linux do Azure. Este pacote é instalado por predefinição e recomendamos que não é removida.
* GRUB2 agora é utilizado como bootloader predefinido, para que o procedimento para editar os parâmetros de kernel foi alterada (ver abaixo).
* XFS agora é o sistema de ficheiros predefinido. O sistema de ficheiros ext4 ainda pode ser utilizado se assim o desejar.

**Passos de configuração**

1. No Gestor de Hyper-V, selecione a máquina virtual.
2. Clique em **Connect** para abrir uma janela da consola da máquina virtual.
3. Crie um ficheiro denominado **rede** no `/etc/sysconfig/` diretório que contém o seguinte texto:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain
4. Crie um ficheiro denominado **ifcfg eth0** no `/etc/sysconfig/network-scripts/` diretório que contém o seguinte texto:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
5. Modificar regras de udev para evitar a geração de regras estáticas para a interfaces Ethernet. Estas regras podem causar problemas ao clonar uma máquina virtual no Microsoft Azure ou o Hyper-v:
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
6. Certifique-se de que o serviço de rede será iniciada no momento do arranque executando o seguinte comando:
   
        # sudo chkconfig network on
7. Instale o pacote do python pyasn1 executando o seguinte comando:
   
        # sudo yum install python-pyasn1
8. Execute o seguinte comando para limpar os metadados de yum atual e instale as atualizações:
   
        # sudo yum clean all
        # sudo yum -y update
9. Modifique a linha de arranque de kernel na configuração grub para incluir parâmetros de kernel adicionais para o Azure. Para fazê-lo aberto "/ predefinido/etc/grub" num editor de texto e editar o `GRUB_CMDLINE_LINUX` parâmetro, por exemplo:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Isto também vai assegurar todas as mensagens de consola são enviadas para a primeira porta série, que pode ajudar a Azure suporte com problemas de depuração. Também desativa as convenções de nomenclatura de OEL 7 novo para NICs. Para além dos acima, é recomendado *remover* os seguintes parâmetros:
   
       rhgb quiet crashkernel=auto
   
   Arranque gráfica e silenciosos não são úteis num ambiente de nuvem em que pretende é que todos os registos sejam enviados para a porta série.
   
   O `crashkernel` opção pode ser esquerda configurado se assim o desejar, mas tenha em atenção que este parâmetro irá reduzir a quantidade de memória disponível na VM 128 MB ou mais, que pode ser problemático nos tamanhos VM mais pequenos.
10. Quando tiver terminado edição "/ predefinido/etc/grub" por acima, execute o seguinte comando para reconstruir a configuração de grub:
    
        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
11. Certifique-se de que o servidor SSH está instalado e configurado para iniciar no momento do arranque.  Normalmente, esta é a predefinição.
12. Instale o agente Linux do Azure executando o seguinte comando:
    
        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent
13. Não crie o espaço de comutação no disco do SO.
    
    O agente Linux do Azure podem configurar automaticamente o espaço de comutação com o disco de recurso local que está ligado à VM após o aprovisionamento no Azure. Tenha em atenção que o disco de recurso local é um *temporário* disco e poderá ser esvaziada quando a VM é desaprovisionada. Depois de instalar o agente Linux do Azure (consulte o passo anterior), modifique os parâmetros seguintes /etc/waagent.conf conforme apropriado:
    
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
14. Execute os seguintes comandos para retirar o aprovisionamento da máquina virtual e prepará-la para o aprovisionamento no Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
15. Clique em **ação -> encerrar baixo** no Gestor de Hyper-V. O VHD de Linux está agora pronto para ser carregado para o Azure.

## <a name="next-steps"></a>Passos Seguintes
Agora, está pronto a utilizar o. vhd Oracle Linux para criar novas máquinas virtuais no Azure. Se esta for a primeira vez que está a carregar o ficheiro. vhd para o Azure, consulte [criar uma VM com Linux a partir de um disco personalizado](upload-vhd.md#option-1-upload-a-vhd).

