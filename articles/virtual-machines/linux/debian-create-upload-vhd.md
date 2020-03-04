---
title: Prepare um VHD Linux Debian em Azure
description: Aprenda a criar imagens VHD de Debian para implantação em Azure.
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: a6de7a7c-cc70-44e7-aed0-2ae6884d401a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 11/13/2018
ms.author: mimckitt
ms.openlocfilehash: 579704ad663e20fdbb59b94d1d4c5ea831d3a68a
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78251690"
---
# <a name="prepare-a-debian-vhd-for-azure"></a>Prepare um VHD dedebiano para o Azure
## <a name="prerequisites"></a>Pré-requisitos
Esta secção pressupõe que já instalou um sistema operativo Debian Linux a partir de um ficheiro .iso descarregado do [website da Debian](https://www.debian.org/distrib/) para um disco rígido virtual. Existem múltiplas ferramentas para criar ficheiros .vhd; Hyper-V é apenas um exemplo. Para obter instruções utilizando o Hyper-V, consulte [Instale a função Hyper-V e configure uma máquina virtual](https://technet.microsoft.com/library/hh846766.aspx).

## <a name="installation-notes"></a>Notas de instalação
* Consulte também as Notas de [Instalação do General Linux](create-upload-generic.md#general-linux-installation-notes) para obter mais dicas sobre a preparação do Linux para o Azure.
* O novo formato VHDX não é suportado no Azure. Pode converter o disco em formato VHD utilizando o Hyper-V Manager ou o cmdlet **convert-vhd.**
* Ao instalar o sistema Linux, recomenda-se que utilize divisórias padrão em vez de LVM (muitas vezes o padrão para muitas instalações). Isto evitará conflitos de nome lvm com VMs clonados, especialmente se um disco de SO alguma vez precisar de ser ligado a outro VM para resolução de problemas. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) podem ser utilizados em discos de dados, se preferir.
* Não configure uma divisória de troca no disco OS. O agente Azure Linux pode ser configurado para criar um ficheiro de troca no disco de recursos temporários. Mais informações podem ser encontradas nos degraus abaixo.
* Todos os VHDs em Azure devem ter um tamanho virtual alinhado a 1MB. Ao converter de um disco cru para VHD, deve certificar-se de que o tamanho do disco bruto é um múltiplo de 1MB antes da conversão. Para mais informações, consulte as Notas de [Instalação do Linux](create-upload-generic.md#general-linux-installation-notes).

## <a name="use-azure-manage-to-create-debian-vhds"></a>Use Azure-Manage para criar VHDs debianos
Existem ferramentas disponíveis para gerar VHDs dedebianos para o Azure, como os scripts [de gestão azul](https://github.com/credativ/azure-manage) da [Credativ.](https://www.credativ.com/) Esta é a abordagem recomendada versus criar uma imagem do zero. Por exemplo, para criar um Debian 8 VHD executar os seguintes comandos para descarregar o utilitário `azure-manage` (e dependências) e executar o `azure_build_image` script:

    # sudo apt-get update
    # sudo apt-get install git qemu-utils mbr kpartx debootstrap

    # sudo apt-get install python3-pip python3-dateutil python3-cryptography
    # sudo pip3 install azure-storage azure-servicemanagement-legacy azure-common pytest pyyaml
    # git clone https://github.com/credativ/azure-manage.git
    # cd azure-manage
    # sudo pip3 install .

    # sudo azure_build_image --option release=jessie --option image_size_gb=30 --option image_prefix=debian-jessie-azure section


## <a name="manually-prepare-a-debian-vhd"></a>Prepare manualmente um VHD dedesano
1. No Hyper-V Manager, selecione a máquina virtual.
2. Clique em **Ligar** para abrir uma janela de consola para a máquina virtual.
3. Se instalou o SISTEMA utilizando uma ISO, então comente qualquer linha relacionada com "`deb cdrom`" em `/etc/apt/source.list`.

4. Editar o ficheiro `/etc/default/grub` e modificar o parâmetro **GRUB_CMDLINE_LINUX** seguinte para incluir parâmetros adicionais de kernel para o Azure.
   
        GRUB_CMDLINE_LINUX="console=tty0 console=ttyS0,115200n8 earlyprintk=ttyS0,115200"

5. Reconstruir a comida e correr:

        # sudo update-grub

6. Adicione os repositórios Azure da Debian a /etc/apt/sources.list para Debian 8 ou 9:

    **Debian 8.x "Jessie"**

        deb http://debian-archive.trafficmanager.net/debian jessie main
        deb-src http://debian-archive.trafficmanager.net/debian jessie main
        deb http://debian-archive.trafficmanager.net/debian-security jessie/updates main
        deb-src http://debian-archive.trafficmanager.net/debian-security jessie/updates
        deb http://debian-archive.trafficmanager.net/debian jessie-updates main
        deb-src http://debian-archive.trafficmanager.net/debian jessie-updates main
        deb http://debian-archive.trafficmanager.net/debian jessie-backports main
        deb-src http://debian-archive.trafficmanager.net/debian jessie-backports main

    **Debian 9.x "Esticar"**

        deb http://debian-archive.trafficmanager.net/debian stretch main
        deb-src http://debian-archive.trafficmanager.net/debian stretch main
        deb http://debian-archive.trafficmanager.net/debian-security stretch/updates main
        deb-src http://debian-archive.trafficmanager.net/debian-security stretch/updates main
        deb http://debian-archive.trafficmanager.net/debian stretch-updates main
        deb-src http://debian-archive.trafficmanager.net/debian stretch-updates main
        deb http://debian-archive.trafficmanager.net/debian stretch-backports main
        deb-src http://debian-archive.trafficmanager.net/debian stretch-backports main


7. Instale o Agente Azure Linux:
   
        # sudo apt-get update
        # sudo apt-get install waagent

8. Para debian 9+, recomenda-se a utilização do novo núcleo de nuvem de debian para utilização com VMs em Azure. Para instalar este novo núcleo, crie primeiro um ficheiro chamado /etc/apt/preferences.d/linux.pref com o seguinte conteúdo:
   
        Package: linux-* initramfs-tools
        Pin: release n=stretch-backports
        Pin-Priority: 500
   
    Em seguida, executar "sudo apt-get instalar linux-image-cloud-amd64" para instalar o novo kernel Debian Cloud.

9. Desprovisionie a máquina virtual e prepare-a para o fornecimento em Azure e executar:
   
        # sudo waagent –force -deprovision
        # export HISTSIZE=0
        # logout

10. Clique em **Ação** -> Desligue em Hiper-V Manager. O seu VHD Linux está agora pronto para ser enviado para o Azure.

## <a name="next-steps"></a>Passos seguintes
Está agora pronto para usar o seu disco rígido virtual Debian para criar novas máquinas virtuais em Azure. Se esta for a primeira vez que está a enviar o ficheiro .vhd para o Azure, consulte [Create a Linux VM a partir de um disco personalizado](upload-vhd.md#option-1-upload-a-vhd).

