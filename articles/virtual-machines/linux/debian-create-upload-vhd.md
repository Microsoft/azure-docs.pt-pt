---
title: Prepare um Debian Linux VHD
description: Saiba como criar imagens Debian VHD para implementações em VM em Azure.
author: gbowerman
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 11/13/2018
ms.author: guybo
ms.openlocfilehash: d822aa30926c473d08bc5d785211c82d038ef652
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98203189"
---
# <a name="prepare-a-debian-vhd-for-azure"></a>Prepare um VHD Debian para Azure
## <a name="prerequisites"></a>Pré-requisitos
Esta secção pressupõe que já instalou um sistema operativo Debian Linux a partir de um ficheiro .iso descarregado do [site Debian](https://www.debian.org/distrib/) para um disco rígido virtual. Existem várias ferramentas para criar ficheiros .vhd; Hiper-V é apenas um exemplo. Para obter instruções que utilizem o Hyper-V, consulte [instalar a função Hiper-V e configurar uma máquina virtual](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

## <a name="installation-notes"></a>Notas de instalação
* Consulte também [as notas de instalação do General Linux](create-upload-generic.md#general-linux-installation-notes) para obter mais dicas sobre a preparação do Linux para a Azure.
* O novo formato VHDX não é suportado no Azure. Pode converter o disco em formato VHD utilizando o Hyper-V Manager ou o **cmdlet converte-vhd.**
* Ao instalar o sistema Linux, recomenda-se que utilize divisórias padrão em vez de LVM (muitas vezes o padrão para muitas instalações). Isto evitará conflitos de nome LVM com VMs clonados, especialmente se um disco de SO precisar de ser ligado a outro VM para resolução de problemas. [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) ou [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid) podem ser usados em discos de dados, se preferir.
* Não configuure uma partição de troca no disco OS. O agente Azure Linux pode ser configurado para criar um ficheiro de troca no disco de recursos temporários. Mais informações podem ser encontradas nos degraus abaixo.
* Todos os VHDs em Azure devem ter um tamanho virtual alinhado a 1MB. Ao converter de um disco cru para VHD, deve certificar-se de que o tamanho do disco bruto é um múltiplo de 1MB antes da conversão. Para mais informações, consulte [as Notas de Instalação do Linux.](create-upload-generic.md#general-linux-installation-notes)

## <a name="use-azure-manage-to-create-debian-vhds"></a>Use Azure-Manage para criar VHDs de Debian
Existem ferramentas disponíveis para gerar VHDs Debian para Azure, como os scripts [de gestão de azure](https://github.com/credativ/azure-manage) do [Credativ.](https://www.credativ.com/) Esta é a abordagem recomendada contra a criação de uma imagem do zero. Por exemplo, para criar um Debian 8 VHD executar os seguintes comandos para descarregar o `azure-manage` utilitário (e dependências) e executar o `azure_build_image` script:

```console
# sudo apt-get update
# sudo apt-get install git qemu-utils mbr kpartx debootstrap

# sudo apt-get install python3-pip python3-dateutil python3-cryptography
# sudo pip3 install azure-storage azure-servicemanagement-legacy azure-common pytest pyyaml
# git clone https://github.com/credativ/azure-manage.git
# cd azure-manage
# sudo pip3 install .

# sudo azure_build_image --option release=jessie --option image_size_gb=30 --option image_prefix=debian-jessie-azure section
```


## <a name="manually-prepare-a-debian-vhd"></a>Preparar manualmente um VHD Debian
1. No Hyper-V Manager, selecione a máquina virtual.
2. Clique **em Connect** para abrir uma janela de consola para a máquina virtual.
3. Se instalou o SO usando um ISO, em seguida, comente qualquer linha relacionada com `deb cdrom` " em " em `/etc/apt/source.list` .

4. Edite o `/etc/default/grub` ficheiro e modifique o parâmetro **GRUB_CMDLINE_LINUX** da seguinte forma para incluir parâmetros adicionais de kernel para Azure.

    ```config-grub
    GRUB_CMDLINE_LINUX="console=tty0 console=ttyS0,115200n8 earlyprintk=ttyS0,115200"
    ```

5. Reconstruir a comida e correr:

    ```console
    # sudo update-grub
    ```

6. Adicione os repositórios Azure de Debian a /etc/apt/sources.list para debian 8, 9 ou 10:

    **Debian 8.x "Jessie"**

    ```config-grub
    deb http://debian-archive.trafficmanager.net/debian jessie main
    deb-src http://debian-archive.trafficmanager.net/debian jessie main
    deb http://debian-archive.trafficmanager.net/debian-security jessie/updates main
    deb-src http://debian-archive.trafficmanager.net/debian-security jessie/updates
    deb http://debian-archive.trafficmanager.net/debian jessie-updates main
    deb-src http://debian-archive.trafficmanager.net/debian jessie-updates main
    deb http://debian-archive.trafficmanager.net/debian jessie-backports main
    deb-src http://debian-archive.trafficmanager.net/debian jessie-backports main
    ```

    **Debian 9.x "Alongamento"**

    ```config-grub
    deb http://debian-archive.trafficmanager.net/debian stretch main
    deb-src http://debian-archive.trafficmanager.net/debian stretch main
    deb http://debian-archive.trafficmanager.net/debian-security stretch/updates main
    deb-src http://debian-archive.trafficmanager.net/debian-security stretch/updates main
    deb http://debian-archive.trafficmanager.net/debian stretch-updates main
    deb-src http://debian-archive.trafficmanager.net/debian stretch-updates main
    deb http://debian-archive.trafficmanager.net/debian stretch-backports main
    deb-src http://debian-archive.trafficmanager.net/debian stretch-backports main
    ```
    
    **Debian 10.x "Buster"**
    ```config-grub
    deb http://debian-archive.trafficmanager.net/debian buster main
    deb-src http://debian-archive.trafficmanager.net/debian buster main
    deb http://debian-archive.trafficmanager.net/debian-security buster/updates main
    deb-src http://debian-archive.trafficmanager.net/debian-security buster/updates main
    deb http://debian-archive.trafficmanager.net/debian buster-updates main
    deb-src http://debian-archive.trafficmanager.net/debian buster-updates main
    deb http://debian-archive.trafficmanager.net/debian buster-backports main
    deb-src http://debian-archive.trafficmanager.net/debian buster-backports main
    ```

7. Instale o agente Azure Linux:

    ```console
    # sudo apt-get update
    # sudo apt-get install waagent
    ```

8. Para Debian 9+, recomenda-se a utilização do novo núcleo de Nuvem Debian para utilização com VMs em Azure. Para instalar este novo núcleo, primeiro crie um ficheiro chamado /etc/apt/preferences.d/linux.pref com os seguintes conteúdos:

    ```config-pref
    Package: linux-* initramfs-tools
    Pin: release n=stretch-backports
    Pin-Priority: 500
    ```

    Em seguida, executar "sudo apt-get instalar linux-image-cloud-amd64" para instalar o novo kernel Debian Cloud.

9. Desprovisionar a máquina virtual e prepará-la para provisão no Azure e executar:

    ```console
    # sudo waagent –force -deprovision
    # export HISTSIZE=0
    # logout
    ```

10. Clique em **Ação** -> Desligar em Hyper-V Manager. O seu VHD Linux está agora pronto para ser enviado para Azure.

## <a name="next-steps"></a>Passos seguintes
Está agora pronto para usar o seu disco rígido virtual Debian para criar novas máquinas virtuais em Azure. Se esta for a primeira vez que está a enviar o ficheiro .vhd para a Azure, consulte [Create a Linux VM a partir de um disco personalizado](upload-vhd.md#option-1-upload-a-vhd).