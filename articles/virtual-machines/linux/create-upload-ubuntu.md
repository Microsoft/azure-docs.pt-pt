---
title: Criar e carregar um VHD de Ubuntu Linux no Azure
description: Saiba como criar e carregar um VHD (disco rígido virtual) do Azure que contém um sistema operacional Ubuntu Linux.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: gwallace
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 3e097959-84fc-4f6a-8cc8-35e087fd1542
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 06/24/2019
ms.author: szark
ms.openlocfilehash: fe9eed24ef641ef1403aa940b88790e38512e874
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70083537"
---
# <a name="prepare-an-ubuntu-virtual-machine-for-azure"></a>Preparar uma máquina virtual do Ubuntu para o Azure
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="official-ubuntu-cloud-images"></a>Imagens oficiais de nuvem do Ubuntu
O Ubuntu agora publica VHDs oficiais do Azure para download [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/)em. Se você precisar criar sua própria imagem do Ubuntu especializada para o Azure, em vez de usar o procedimento manual abaixo, é recomendável iniciar com esses VHDs de trabalho conhecidos e personalizá-los conforme necessário. As versões mais recentes da imagem sempre podem ser encontradas nos seguintes locais:

* Ubuntu 12.04/Precise: [ubuntu-12.04-server-cloudimg-amd64-disk1.vhd.zip](https://cloud-images.ubuntu.com/precise/current/precise-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 14.04/Trusty: [ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip](https://cloud-images.ubuntu.com/releases/trusty/release/ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 16.04/Xenial: [ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip](https://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 18.04/Bionic: [bionic-server-cloudimg-amd64.vhd.zip](https://cloud-images.ubuntu.com/bionic/current/bionic-server-cloudimg-amd64.vhd.zip)
* Ubuntu 18.10/Cosmic: [cosmic-server-cloudimg-amd64.vhd.zip](http://cloud-images.ubuntu.com/releases/cosmic/release/ubuntu-18.10-server-cloudimg-amd64.vhd.zip)

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que você já tenha instalado um sistema operacional Ubuntu Linux em um disco rígido virtual. Existem várias ferramentas para criar arquivos. VHD, por exemplo, uma solução de virtualização, como o Hyper-V. Para obter instruções, consulte [instalar a função Hyper-V e configurar uma máquina virtual](https://technet.microsoft.com/library/hh846766.aspx).

**Notas de instalação do Ubuntu**

* Consulte também as [notas de instalação gerais do Linux](create-upload-generic.md#general-linux-installation-notes) para obter mais dicas sobre como preparar o Linux para o Azure.
* Não há suporte para o formato VHDX no Azure, somente **VHD fixo**.  Você pode converter o disco para o formato VHD usando o Gerenciador do Hyper-V ou o cmdlet Convert-VHD.
* Ao instalar o sistema Linux, é recomendável que você use partições padrão em vez de LVM (geralmente o padrão para muitas instalações). Isso evitará conflitos de nome LVM com VMs clonadas, especialmente se um disco do sistema operacional precisar ser anexado a outra VM para solução de problemas. O [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou o [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) pode ser usado em discos de dados, se for preferencial.
* Não configure uma partição de permuta no disco do sistema operacional. O agente do Linux pode ser configurado para criar um arquivo de permuta no disco de recursos temporário.  Mais informações sobre isso podem ser encontradas nas etapas abaixo.
* Todos os VHDs no Azure devem ter um tamanho virtual alinhado a 1 MB. Ao converter de um disco bruto para VHD, você deve garantir que o tamanho do disco bruto seja um múltiplo de 1MB antes da conversão. Consulte [notas de instalação do Linux](create-upload-generic.md#general-linux-installation-notes) para obter mais informações.

## <a name="manual-steps"></a>Etapas manuais
> [!NOTE]
> Antes de tentar criar sua própria imagem personalizada do Ubuntu para o Azure, considere usar as imagens [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/) previamente compiladas e testadas em vez disso.
> 
> 

1. No painel central do Gerenciador do Hyper-V, selecione a máquina virtual.

2. Clique em **conectar** para abrir a janela da máquina virtual.

3. Substitua os repositórios atuais na imagem para usar o Azure repositórios do Ubuntu. As etapas variam um pouco dependendo da versão do Ubuntu.
   
    Antes de `/etc/apt/sources.list`editar, é recomendável fazer um backup:
   
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

4. As imagens do Ubuntu Azure agora estão seguindo o kernel de habilitação de *hardware* (Hwe). Atualize o sistema operacional para o kernel mais recente executando os seguintes comandos:

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

    **Consulte também:**
    - [https://wiki.ubuntu.com/Kernel/LTSEnablementStack](https://wiki.ubuntu.com/Kernel/LTSEnablementStack)
    - [https://wiki.ubuntu.com/Kernel/RollingLTSEnablementStack](https://wiki.ubuntu.com/Kernel/RollingLTSEnablementStack)


5. Modifique a linha de inicialização do kernel para grub para incluir parâmetros de kernel adicionais para o Azure. Para fazer isso, `/etc/default/grub` abra em um editor de texto, localize a `GRUB_CMDLINE_LINUX_DEFAULT` variável chamada (ou adicione-a se necessário) e edite-a para incluir os seguintes parâmetros:
   
        GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300"

    Salve e feche esse arquivo e, em seguida `sudo update-grub`, execute. Isso garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, o que pode auxiliar o suporte técnico do Azure com problemas de depuração.

6. Verifique se o servidor SSH está instalado e configurado para iniciar no momento da inicialização.  Normalmente, isso é o padrão.

7. Instale o agente Linux do Azure:
   
        # sudo apt-get update
        # sudo apt-get install walinuxagent

   > [!Note]
   >  O `walinuxagent` pacote pode remover os `NetworkManager` pacotes `NetworkManager-gnome` e, se eles estiverem instalados.


1. Execute os seguintes comandos para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:
   
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

1. Clique em **ação-> desligar** no Gerenciador do Hyper-V. Seu VHD do Linux agora está pronto para ser carregado no Azure.

## <a name="references"></a>Referências
[Kernel de habilitação de hardware do Ubuntu (HWE)](https://wiki.ubuntu.com/Kernel/LTSEnablementStack)

## <a name="next-steps"></a>Passos Seguintes
Agora você está pronto para usar seu Ubuntu Linux disco rígido virtual para criar novas máquinas virtuais no Azure. Se esta for a primeira vez que você está carregando o arquivo. vhd no Azure, consulte [criar uma VM do Linux a partir de um disco personalizado](upload-vhd.md#option-1-upload-a-vhd).

