---
title: Preparar um VHD Debian do Linux no Azure | Documentos da Microsoft
description: Saiba como criar imagens de Debian VHD para implementação no Azure.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: a6de7a7c-cc70-44e7-aed0-2ae6884d401a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: szark
ms.openlocfilehash: 6ef0a9f6efbf5f8398ba242150b2eb6102875f7e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58000317"
---
# <a name="prepare-a-debian-vhd-for-azure"></a>Preparar um VHD Debian para o Azure
## <a name="prerequisites"></a>Pré-requisitos
Esta secção assume que já tem instalado um sistema operativo Debian Linux de um arquivo. ISO transferido a partir da [Web site Debian](https://www.debian.org/distrib/) para um disco rígido virtual. Várias ferramentas existem para criar ficheiros. vhd; Hyper-V é apenas um exemplo. Para obter instruções sobre como utilizar o Hyper-V, consulte [instalar a função Hyper-V e configurar uma Máquina Virtual](https://technet.microsoft.com/library/hh846766.aspx).

## <a name="installation-notes"></a>Observações de instalação
* Consulte também [observações de instalação de Linux geral](create-upload-generic.md#general-linux-installation-notes) para obter mais dicas sobre como preparar o Linux para o Azure.
* O formato mais recente do VHDX não é suportado no Azure. Pode converter o disco para o formato VHD utilizando o Gestor de Hyper-V ou o **convert-vhd** cmdlet.
* Ao instalar o sistema Linux, é recomendado que utilize partições padrão em vez de LVM (muitas vezes, o padrão para muitas instalações). Isso evitará LVM nome entra em conflito com VMs Clonadas, especialmente se um disco de SO algum dia precisar ser anexados a outra VM para resolução de problemas. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) pode ser utilizada em discos de dados, se preferirem.
* Não configure uma partição de troca no disco do SO. O agente Linux do Azure pode ser configurado para criar um ficheiro de troca no disco de recursos temporário. Podem encontrar mais informações nos passos abaixo.
* Todos os VHDs no Azure tem de ter um tamanho virtual alinhado para 1MB. Ao converter a partir de um disco não processado para o VHD, certifique-se de que o tamanho de disco bruto é um múltiplo de 1MB antes da conversão. Para obter mais informações, consulte [observações de instalação de Linux](create-upload-generic.md#general-linux-installation-notes).

## <a name="use-azure-manage-to-create-debian-vhds"></a>Utilizar a gerir o Azure para criar o VHD Debian
Existem ferramentas disponíveis para gerar o VHD Debian para o Azure, tais como o [azure-gerenciar](https://github.com/credativ/azure-manage) scripts a partir de [Credativ](https://www.credativ.com/). Esta é a abordagem recomendada versus criar uma imagem a partir do zero. Por exemplo, para criar uma execução de Debian 8 VHD os seguintes comandos para transferir os `azure-manage` utilitário (e as dependências) e execute o `azure_build_image` script:

    # sudo apt-get update
    # sudo apt-get install git qemu-utils mbr kpartx debootstrap

    # sudo apt-get install python3-pip python3-dateutil python3-cryptography
    # sudo pip3 install azure-storage azure-servicemanagement-legacy azure-common pytest pyyaml
    # git clone https://github.com/credativ/azure-manage.git
    # cd azure-manage
    # sudo pip3 install .

    # sudo azure_build_image --option release=jessie --option image_size_gb=30 --option image_prefix=debian-jessie-azure section


## <a name="manually-prepare-a-debian-vhd"></a>Preparar um VHD Debian manualmente
1. No Gestor de Hyper-V, selecione a máquina virtual.
2. Clique em **Connect** para abrir uma janela de consola para a máquina virtual.
3. Se tiver instalado o sistema operacional usando uma imagem ISO, em seguida, comente a qualquer linha relacionados ao "`deb cdrom`" no `/etc/apt/source.list`.

4. Editar a `/etc/default/grub` de ficheiros e modificar o **GRUB_CMDLINE_LINUX** parâmetro da seguinte forma para incluir parâmetros de kernel adicionais para o Azure.
   
        GRUB_CMDLINE_LINUX="console=tty0 console=ttyS0,115200n8 earlyprintk=ttyS0,115200"

5. Recompile o grub e execute:

        # sudo update-grub

6. Adicione repositórios de Azure do Debian a /etc/apt/sources.list para Debian 8 ou 9:

    **Debian 8.x "Jessie"**

        deb http://debian-archive.trafficmanager.net/debian jessie main
        deb-src http://debian-archive.trafficmanager.net/debian jessie main
        deb http://debian-archive.trafficmanager.net/debian-security jessie/updates main
        deb-src http://debian-archive.trafficmanager.net/debian-security jessie/updates
        deb http://debian-archive.trafficmanager.net/debian jessie-updates main
        deb-src http://debian-archive.trafficmanager.net/debian jessie-updates main
        deb http://debian-archive.trafficmanager.net/debian jessie-backports main
        deb-src http://debian-archive.trafficmanager.net/debian jessie-backports main

    **Debian 9.x "Stretch"**

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

8. Para Debian 9 +, recomenda-se para utilizar o novo kernel de Debian Cloud para utilização com as VMs no Azure. Para instalar este novo kernel, primeiro crie um arquivo chamado /etc/apt/preferences.d/linux.pref com o seguinte conteúdo:
   
        Package: linux-* initramfs-tools
        Pin: release n=stretch-backports
        Pin-Priority: 500
   
    Em seguida, executar "sudo apt-get install linux-imagem-cloud-amd64" para instalar o novo kernel de Debian Cloud.

9. Desaprovisionar a máquina virtual e prepará-lo para o aprovisionamento no Azure e execute:
   
        # sudo waagent –force -deprovision
        # export HISTSIZE=0
        # logout

10. Clique em **ação** -> Encerrar para baixo no Gestor de Hyper-V. O VHD do Linux está agora pronto para ser carregado para o Azure.

## <a name="next-steps"></a>Passos Seguintes
Agora, está pronto para utilizar o seu disco rígido virtual Debian para criar novas máquinas virtuais no Azure. Se esta for a primeira vez que está a carregar o ficheiro. vhd para o Azure, veja [criar uma VM do Linux a partir de um disco personalizado](upload-vhd.md#option-1-upload-a-vhd).

