---
title: Preparar um VHD do Debian Linux no Azure | Microsoft Docs
description: Saiba como criar imagens de VHD Debian para implantação no Azure.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: a6de7a7c-cc70-44e7-aed0-2ae6884d401a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 11/13/2018
ms.author: szark
ms.openlocfilehash: 009918a95ca1ff6189553d502fd06773fcd0d402
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70083459"
---
# <a name="prepare-a-debian-vhd-for-azure"></a>Preparar um VHD Debian para o Azure
## <a name="prerequisites"></a>Pré-requisitos
Esta seção pressupõe que você já tenha instalado um sistema operacional Linux Debian a partir de um arquivo. ISO baixado do [site Debian](https://www.debian.org/distrib/) para um disco rígido virtual. Existem várias ferramentas para criar arquivos. vhd; O Hyper-V é apenas um exemplo. Para obter instruções sobre como usar o Hyper-V, consulte [instalar a função Hyper-v e configurar uma máquina virtual](https://technet.microsoft.com/library/hh846766.aspx).

## <a name="installation-notes"></a>Notas de instalação
* Confira também [notas de instalação gerais do Linux](create-upload-generic.md#general-linux-installation-notes) para obter mais dicas sobre como preparar o Linux para o Azure.
* Não há suporte para o formato VHDX mais recente no Azure. Você pode converter o disco para o formato VHD usando o Gerenciador do Hyper-V ou o cmdlet **Convert-VHD** .
* Ao instalar o sistema Linux, é recomendável que você use partições padrão em vez de LVM (geralmente o padrão para muitas instalações). Isso evitará conflitos de nome LVM com VMs clonadas, especialmente se um disco do sistema operacional precisar ser anexado a outra VM para solução de problemas. O [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou o [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) pode ser usado em discos de dados, se for preferencial.
* Não configure uma partição de permuta no disco do sistema operacional. O agente Linux do Azure pode ser configurado para criar um arquivo de permuta no disco de recursos temporário. Mais informações podem ser encontradas nas etapas abaixo.
* Todos os VHDs no Azure devem ter um tamanho virtual alinhado a 1 MB. Ao converter de um disco bruto para VHD, você deve garantir que o tamanho do disco bruto seja um múltiplo de 1 MB antes da conversão. Para obter mais informações, consulte [notas de instalação do Linux](create-upload-generic.md#general-linux-installation-notes).

## <a name="use-azure-manage-to-create-debian-vhds"></a>Usar o Azure-Manage para criar VHDs Debian
Há ferramentas disponíveis para gerar VHDs de Debian para o Azure, como os scripts do [Azure-Manage](https://github.com/credativ/azure-manage) de [credativ](https://www.credativ.com/). Essa é a abordagem recomendada em relação à criação de uma imagem do zero. Por exemplo, para criar um VHD Debian 8, execute os seguintes comandos para baixar `azure-manage` o utilitário (e as dependências) `azure_build_image` e executar o script:

    # sudo apt-get update
    # sudo apt-get install git qemu-utils mbr kpartx debootstrap

    # sudo apt-get install python3-pip python3-dateutil python3-cryptography
    # sudo pip3 install azure-storage azure-servicemanagement-legacy azure-common pytest pyyaml
    # git clone https://github.com/credativ/azure-manage.git
    # cd azure-manage
    # sudo pip3 install .

    # sudo azure_build_image --option release=jessie --option image_size_gb=30 --option image_prefix=debian-jessie-azure section


## <a name="manually-prepare-a-debian-vhd"></a>Preparar manualmente um VHD Debian
1. No Gerenciador do Hyper-V, selecione a máquina virtual.
2. Clique em **conectar** para abrir uma janela de console para a máquina virtual.
3. Se você instalou o sistema operacional usando um ISO, comente qualquer linha relacionada a "`deb cdrom`" em. `/etc/apt/source.list`

4. Edite `/etc/default/grub` o arquivo e modifique o parâmetro **GRUB_CMDLINE_LINUX** da seguinte maneira para incluir parâmetros de kernel adicionais para o Azure.
   
        GRUB_CMDLINE_LINUX="console=tty0 console=ttyS0,115200n8 earlyprintk=ttyS0,115200"

5. Recompile o grub e execute:

        # sudo update-grub

6. Adicione repositórios do Azure de Debian ao/etc/apt/sources.List para o Debian 8 ou 9:

    **Debian 8. x "Jessie"**

        deb http://debian-archive.trafficmanager.net/debian jessie main
        deb-src http://debian-archive.trafficmanager.net/debian jessie main
        deb http://debian-archive.trafficmanager.net/debian-security jessie/updates main
        deb-src http://debian-archive.trafficmanager.net/debian-security jessie/updates
        deb http://debian-archive.trafficmanager.net/debian jessie-updates main
        deb-src http://debian-archive.trafficmanager.net/debian jessie-updates main
        deb http://debian-archive.trafficmanager.net/debian jessie-backports main
        deb-src http://debian-archive.trafficmanager.net/debian jessie-backports main

    **"Stretch" do Debian 9. x**

        deb http://debian-archive.trafficmanager.net/debian stretch main
        deb-src http://debian-archive.trafficmanager.net/debian stretch main
        deb http://debian-archive.trafficmanager.net/debian-security stretch/updates main
        deb-src http://debian-archive.trafficmanager.net/debian-security stretch/updates main
        deb http://debian-archive.trafficmanager.net/debian stretch-updates main
        deb-src http://debian-archive.trafficmanager.net/debian stretch-updates main
        deb http://debian-archive.trafficmanager.net/debian stretch-backports main
        deb-src http://debian-archive.trafficmanager.net/debian stretch-backports main


7. Instale o agente Linux do Azure:
   
        # sudo apt-get update
        # sudo apt-get install waagent

8. Para Debian 9 +, é recomendável usar o novo kernel de nuvem Debian para uso com VMs no Azure. Para instalar esse novo kernel, primeiro crie um arquivo chamado/etc/apt/preferences.d/Linux.pref com o seguinte conteúdo:
   
        Package: linux-* initramfs-tools
        Pin: release n=stretch-backports
        Pin-Priority: 500
   
    Em seguida, execute "sudo apt-obter instalar Linux-Image-Cloud-amd64" para instalar o novo kernel de nuvem Debian.

9. Desprovisione a máquina virtual e prepare-a para provisionamento no Azure e execute:
   
        # sudo waagent –force -deprovision
        # export HISTSIZE=0
        # logout

10. Clique em **ação** -> desligar no Gerenciador do Hyper-V. Seu VHD do Linux agora está pronto para ser carregado no Azure.

## <a name="next-steps"></a>Passos Seguintes
Agora você está pronto para usar o disco rígido virtual Debian para criar novas máquinas virtuais no Azure. Se esta for a primeira vez que você está carregando o arquivo. vhd no Azure, consulte [criar uma VM do Linux a partir de um disco personalizado](upload-vhd.md#option-1-upload-a-vhd).

