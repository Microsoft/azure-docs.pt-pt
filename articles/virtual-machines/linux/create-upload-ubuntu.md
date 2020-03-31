---
title: Crie e carregue um Ubuntu Linux VHD em Azure
description: Aprenda a criar e carregar um disco rígido virtual Azure (VHD) que contenha um sistema operativo Ubuntu Linux.
author: gbowerman
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 06/24/2019
ms.author: guybo
ms.openlocfilehash: 5fa3415d8663f358bf0ae48be46ac52b8f8b4b06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066737"
---
# <a name="prepare-an-ubuntu-virtual-machine-for-azure"></a>Prepare an Ubuntu virtual machine for Azure (Preparar uma máquina virtual Ubuntu para o Azure)


Ubuntu agora publica VHDs oficiais [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/)Azure para download em . Se precisar de construir a sua própria imagem ubuntu especializada para o Azure, em vez de utilizar o procedimento manual abaixo, recomenda-se começar com estes VHDs de trabalho conhecidos e personalizar conforme necessário. As mais recentes versões de imagem podem ser sempre encontradas nos seguintes locais:

* Ubuntu 12.04/Precise: [ubuntu-12.04-server-cloudimg-amd64-disk1.vhd.zip](https://cloud-images.ubuntu.com/precise/current/precise-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 14.04/Trusty: [ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip](https://cloud-images.ubuntu.com/releases/trusty/release/ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 16.04/Xenial: [ubuntu-16.04-server-cloudimg-amd64-disk1.vmdk](https://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vmdk)
* Ubuntu 18.04/Bionic: [bionic-server-cloudimg-amd64.vmdk](https://cloud-images.ubuntu.com/bionic/current/bionic-server-cloudimg-amd64.vmdk)
* Ubuntu 18.10/Cósmico: [cosmic-server-cloudimg-amd64.vhd.zip](http://cloud-images.ubuntu.com/releases/cosmic/release/ubuntu-18.10-server-cloudimg-amd64.vhd.zip)

## <a name="prerequisites"></a>Pré-requisitos
Este artigo assume que já instalou um sistema operativo Ubuntu Linux num disco rígido virtual. Existem múltiplas ferramentas para criar ficheiros .vhd, por exemplo, uma solução de virtualização como o Hyper-V. Para obter instruções, consulte [Instale a função Hyper-V e configure uma máquina virtual](https://technet.microsoft.com/library/hh846766.aspx).

**Notas de instalação ubuntu**

* Consulte também as Notas de [Instalação do General Linux](create-upload-generic.md#general-linux-installation-notes) para obter mais dicas sobre a preparação do Linux para o Azure.
* O formato VHDX não é suportado em Azure, apenas **VHD fixo**.  Pode converter o disco em formato VHD utilizando o Hyper-V Manager ou o cmdlet convert-vhd.
* Ao instalar o sistema Linux, recomenda-se que utilize divisórias padrão em vez de LVM (muitas vezes o padrão para muitas instalações). Isto evitará conflitos de nome lvm com VMs clonados, especialmente se um disco de SO alguma vez precisar de ser ligado a outro VM para resolução de problemas. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) podem ser utilizados em discos de dados, se preferir.
* Não configure uma divisória de troca no disco OS. O agente Linux pode ser configurado para criar um ficheiro de troca no disco de recursos temporários.  Mais informações sobre isso podem ser encontradas nos degraus abaixo.
* Todos os VHDs em Azure devem ter um tamanho virtual alinhado a 1MB. Ao converter de um disco cru para VHD, deve certificar-se de que o tamanho do disco bruto é um múltiplo de 1MB antes da conversão. Consulte as Notas de [Instalação do Linux](create-upload-generic.md#general-linux-installation-notes) para obter mais informações.

## <a name="manual-steps"></a>Passos manuais
> [!NOTE]
> Antes de tentar criar a sua própria imagem ubuntu personalizada para o Azure, por favor considere usar as imagens pré-construídas e testadas a partir de [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/) vez disso.
> 
> 

1. No painel central do Hyper-V Manager, selecione a máquina virtual.

2. Clique **em Ligar** para abrir a janela para a máquina virtual.

3. Substitua os repositórios atuais na imagem para utilizar o repositório Azure de Ubuntu. Os passos variam ligeiramente dependendo da versão Ubuntu.
   
    Antes da `/etc/apt/sources.list`edição, recomenda-se fazer uma cópia de segurança:
   
        # sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

    Ubuntu 12.04:
   
        # sudo sed -i 's/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g' /etc/apt/sources.list
        # sudo apt-get update

    Ubuntu 14.04:
   
        # sudo sed -i 's/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g' /etc/apt/sources.list
        # sudo apt-get update

    Ubuntu 16.04:
   
        # sudo sed -i 's/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g' /etc/apt/sources.list
        # sudo apt-get update

4. As imagens Ubuntu Azure estão agora a seguir o núcleo de ativação de *hardware* (HWE). Atualize o sistema operativo para o mais recente núcleo executando os seguintes comandos:

    Ubuntu 12.04:
   
        # sudo apt-get update
        # sudo apt-get install linux-image-generic-lts-trusty linux-cloud-tools-generic-lts-trusty
        # sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade
   
        # sudo reboot
   
    Ubuntu 14.04:
   
        # sudo apt-get update
        # sudo apt-get install linux-image-virtual-lts-vivid linux-lts-vivid-tools-common
        # sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade
   
        # sudo reboot

    Ubuntu 16.04:
   
        # sudo apt-get update
        # sudo apt-get install linux-generic-hwe-16.04 linux-cloud-tools-generic-hwe-16.04
        (recommended) sudo apt-get dist-upgrade

        # sudo reboot
    
    Ubuntu 18.04.04:
    
        # sudo apt-get update
        # sudo apt-get install --install-recommends linux-generic-hwe-18.04 xserver-xorg-hwe-18.04
        # sudo apt-get install --install-recommends linux-cloud-tools-generic-hwe-18.04
        (recommended) sudo apt-get dist-upgrade

        # sudo reboot
    
    **Consulte também:**
    - [https://wiki.ubuntu.com/Kernel/LTSEnablementStack](https://wiki.ubuntu.com/Kernel/LTSEnablementStack)
    - [https://wiki.ubuntu.com/Kernel/RollingLTSEnablementStack](https://wiki.ubuntu.com/Kernel/RollingLTSEnablementStack)


5. Modifique a linha de arranque do núcleo para a Grub para incluir parâmetros adicionais de kernel para o Azure. Para isso `/etc/default/grub` aberto num editor de texto, encontre a variável chamada `GRUB_CMDLINE_LINUX_DEFAULT` (ou adicione-a se necessário) e edite-a para incluir os seguintes parâmetros:
   
        GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300"

    Guarde e feche este `sudo update-grub`ficheiro e, em seguida, executar . Isto garantirá que todas as mensagens de consola são enviadas para a primeira porta em série, que pode ajudar o suporte técnico do Azure com problemas de depuração.

6. Certifique-se de que o servidor SSH está instalado e configurado para iniciar no momento do arranque.  Este é geralmente o padrão.

7. Instale o Agente Azure Linux:
   
        # sudo apt-get update
        # sudo apt-get install walinuxagent

   > [!Note]
   >  A `walinuxagent` embalagem pode `NetworkManager` `NetworkManager-gnome` remover as e as embalagens, se forem instaladas.


1. Executar os seguintes comandos para desprovisionar a máquina virtual e prepará-la para o fornecimento em Azure:
   
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

1. Clique em **Ação -> desligar** em Hiper-V Manager. O seu VHD Linux está agora pronto para ser enviado para o Azure.

## <a name="references"></a>Referências
[Núcleo de capacitação de hardware Ubuntu (HWE)](https://wiki.ubuntu.com/Kernel/LTSEnablementStack)

## <a name="next-steps"></a>Passos seguintes
Está agora pronto para usar o seu disco rígido virtual Ubuntu Linux para criar novas máquinas virtuais em Azure. Se esta for a primeira vez que está a enviar o ficheiro .vhd para o Azure, consulte [Create a Linux VM a partir de um disco personalizado](upload-vhd.md#option-1-upload-a-vhd).

