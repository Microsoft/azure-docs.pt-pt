---
title: Criar e carregar um VHD do Linux Ubuntu no Azure
description: Saiba como criar e carregar um Azure disco rígido virtual (VHD) que contenha um sistema de operativo Ubuntu Linux.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 3e097959-84fc-4f6a-8cc8-35e087fd1542
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: szark
ms.openlocfilehash: 62d17670a068304e0764c85d49da0aa9a736c477
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57444443"
---
# <a name="prepare-an-ubuntu-virtual-machine-for-azure"></a>Preparar uma máquina virtual do Ubuntu para o Azure
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="official-ubuntu-cloud-images"></a>Imagens de cloud oficiais do Ubuntu
Ubuntu agora publica oficial VHDs do Azure para download em [ http://cloud-images.ubuntu.com/ ](http://cloud-images.ubuntu.com/). Se precisar de criar sua própria imagem de Ubuntu especializada para o Azure, em vez disso, que utilize o procedimento manual abaixo recomenda-se começar com estes conhecido VHDs de funcionar e ser personalizados conforme necessário. As versões de imagem mais recente sempre podem ser encontradas nas seguintes localizações:

* Ubuntu 12.04/Precise: [ubuntu-12.04-server-cloudimg-amd64-disk1.vhd.zip](https://cloud-images.ubuntu.com/precise/current/precise-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 14.04/Trusty: [ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/trusty/release/ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 16.04/Xenial: [ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 18.04/Bionic: [bionic-server-cloudimg-amd64.vhd.zip](http://cloud-images.ubuntu.com/bionic/current/bionic-server-cloudimg-amd64.vhd.zip)
* Ubuntu 18.10/Cosmic: [cosmic-server-cloudimg-amd64.vhd.zip](http://cloud-images.ubuntu.com/cosmic/current/cosmic-server-cloudimg-amd64.vhd.zip)

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que já tem instalado um sistema de operativo Ubuntu Linux para um disco rígido virtual. Existem várias ferramentas para criar ficheiros. vhd, por exemplo uma solução de virtualização, como o Hyper-V. Para obter instruções, consulte [instalar a função Hyper-V e configurar uma Máquina Virtual](https://technet.microsoft.com/library/hh846766.aspx).

**Observações de instalação do Ubuntu**

* Consulte também [observações de instalação de Linux geral](create-upload-generic.md#general-linux-installation-notes) para obter mais dicas sobre como preparar o Linux para o Azure.
* O formato VHDX não é suportado no Azure, apenas **fixo VHD**.  Pode converter o disco para o formato VHD utilizando o Gestor de Hyper-V ou o cmdlet convert-vhd.
* Ao instalar o sistema Linux é recomendado que utilize partições padrão em vez de LVM (muitas vezes, o padrão para muitas instalações). Isso evitará LVM nome entra em conflito com VMs Clonadas, especialmente se um disco de SO algum dia precisar ser anexados a outra VM para resolução de problemas. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) pode ser utilizada em discos de dados, se preferirem.
* Não configure uma partição de troca no disco do SO. O agente Linux pode ser configurado para criar um ficheiro de troca no disco de recursos temporário.  Podem encontrar mais informações sobre este assunto nos passos abaixo.
* Todos os VHDs no Azure tem de ter um tamanho virtual alinhado para 1MB. Ao converter a partir de um disco não processado para o VHD tem de se certificar de que o tamanho de disco bruto é um múltiplo de 1MB antes da conversão. Ver [observações de instalação de Linux](create-upload-generic.md#general-linux-installation-notes) para obter mais informações.

## <a name="manual-steps"></a>Passos manuais
> [!NOTE]
> Antes de tentar criar a sua própria imagem personalizada do Ubuntu para o Azure,. considere utilizar as imagens previamente criadas e testadas partir [ http://cloud-images.ubuntu.com/ ](http://cloud-images.ubuntu.com/) em vez disso.
> 
> 

1. No painel central do Gestor de Hyper-V, selecione a máquina virtual.

2. Clique em **Connect** para abrir a janela para a máquina virtual.

3. Substitua os repositórios atuais na imagem para utilizar transparente de repositórios do Azure do Ubuntu. Os passos são ligeiramente diferentes consoante a versão do Ubuntu.
   
    Antes de editar `/etc/apt/sources.list`, recomenda-se para fazer uma cópia de segurança:
   
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

4. As imagens do Ubuntu Azure estão agora a seguir a *ativação de hardware* kernel (HWE). Atualize o sistema operativo para o kernel mais recente ao executar os comandos seguintes:

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


5. Modificar a linha de arranque de kernel para Grub incluir parâmetros de kernel adicionais para o Azure. Para fazer este open `/etc/default/grub` no editor de texto, encontre a variável chamada `GRUB_CMDLINE_LINUX_DEFAULT` (ou adicioná-lo se necessário) e editá-la para incluir os seguintes parâmetros:
   
        GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300"

    Guardar e fechar este ficheiro e, em seguida, execute `sudo update-grub`. Isto irá garantir que todas as mensagens de consola são enviadas para a primeira porta serial, que pode ajudar o suporte técnico do Azure com a depuração de problemas.

6. Certifique-se de que o servidor SSH está instalado e configurado para iniciar no momento da inicialização.  Normalmente, esta é a predefinição.

7. Instale o agente Linux do Azure:
   
        # sudo apt-get update
        # sudo apt-get install walinuxagent

    >[!Note]
    O `walinuxagent` poderá remover o pacote a `NetworkManager` e `NetworkManager-gnome` pacotes e, se estiverem instalados.

Para Ubuntu 18.04/18.10, atualizar a origem de dados do Azure, editá-lo: /etc/cloud/cloud.cfg.d/90-azure.cfg, adicione este código ao final do ficheiro:

**Importante: O código tem de ser adicionado exatamente como mostrados, incluindo espaços.**

```bash
datasource:
   Azure:
     agent_command: [service, walinuxagent, start]
```

8. Execute os seguintes comandos para desaprovisionar a máquina virtual e prepará-lo para o aprovisionamento no Azure:
   
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

9. Clique em **ação -> encerrar baixo** no Gestor de Hyper-V. O VHD do Linux está agora pronto para ser carregado para o Azure.

## <a name="references"></a>Referências
[Kernel de ativação (HWE) de hardware do Ubuntu](https://wiki.ubuntu.com/Kernel/LTSEnablementStack)

## <a name="next-steps"></a>Passos Seguintes
Agora, está pronto para utilizar o seu disco rígido virtual do Ubuntu Linux para criar novas máquinas virtuais no Azure. Se esta for a primeira vez que está a carregar o ficheiro. vhd para o Azure, veja [criar uma VM do Linux a partir de um disco personalizado](upload-vhd.md#option-1-upload-a-vhd).

