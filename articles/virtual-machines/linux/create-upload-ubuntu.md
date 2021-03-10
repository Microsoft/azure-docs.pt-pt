---
title: Criar e carregar um Ubuntu Linux VHD em Azure
description: Aprenda a criar e carregar um disco rígido virtual Azure (VHD) que contenha um sistema operativo Ubuntu Linux.
author: danielsollondon
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 06/06/2020
ms.author: danis
ms.openlocfilehash: 92ceecd16a428593764fe5ab6478cc4ea7ab91d7
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102554620"
---
# <a name="prepare-an-ubuntu-virtual-machine-for-azure"></a>Prepare an Ubuntu virtual machine for Azure (Preparar uma máquina virtual Ubuntu para o Azure)


Ubuntu publica agora VHDs oficiais Azure para download em [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/) . Se precisar de construir a sua própria imagem de Ubuntu especializada para o Azure, em vez de utilizar o procedimento manual abaixo, recomenda-se começar com estes VHDs de trabalho conhecidos e personalizar conforme necessário. As mais recentes versões de imagem podem sempre ser encontradas nos seguintes locais:

* Ubuntu 16.04/Xenial: [ubuntu-16.04-server-cloudimg-amd64-disk1.vmdk](https://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vmdk)
* Ubuntu 18.04/Bionic: [bionic-server-cloudimg-amd64.vmdk](https://cloud-images.ubuntu.com/bionic/current/bionic-server-cloudimg-amd64.vmdk)

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que já instalou um sistema operativo Ubuntu Linux num disco rígido virtual. Existem várias ferramentas para criar ficheiros .vhd, por exemplo uma solução de virtualização como o Hyper-V. Para obter instruções, consulte [instalar a função Hiper-V e configurar uma máquina virtual.](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11))

**Notas de instalação Ubuntu**

* Consulte também [as notas de instalação do General Linux](create-upload-generic.md#general-linux-installation-notes) para obter mais dicas sobre a preparação do Linux para o Azure.
* O formato VHDX não é suportado em Azure, apenas **VHD fixo**.  Pode converter o disco em formato VHD utilizando o Hyper-V Manager ou o `Convert-VHD` cmdlet.
* Ao instalar o sistema Linux recomenda-se que utilize divisórias padrão em vez de LVM (muitas vezes o padrão para muitas instalações). Isto evitará conflitos de nome LVM com VMs clonados, especialmente se um disco de SO precisar de ser ligado a outro VM para resolução de problemas. [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) ou [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid) podem ser usados em discos de dados, se preferir.
* Não configuure uma divisória de troca ou um ficheiro de troca no disco OS. O agente de provisionamento de init em nuvem pode ser configurado para criar um ficheiro de troca ou uma divisória de troca no disco de recursos temporários. Mais informações sobre isso podem ser encontradas nos degraus abaixo.
* Todos os VHDs em Azure devem ter um tamanho virtual alinhado a 1MB. Ao converter de um disco cru para VHD deve certificar-se de que o tamanho do disco bruto é um múltiplo de 1MB antes da conversão. Consulte [as notas de instalação do Linux](create-upload-generic.md#general-linux-installation-notes) para obter mais informações.

## <a name="manual-steps"></a>Passos manuais
> [!NOTE]
> Antes de tentar criar a sua própria imagem personalizada de Ubuntu para Azure, considere usar as imagens pré-construídas e testadas a partir de [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/) todo o mundo.
> 
> 

1. No painel central do Hyper-V Manager, selecione a máquina virtual.

2. Clique **em Ligar** para abrir a janela para a máquina virtual.

3. Substitua os repositórios atuais na imagem para utilizar o repositório Azure de Ubuntu.

    Antes de `/etc/apt/sources.list` editar, recomenda-se fazer uma cópia de segurança:

    ```console
    # sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
    ```

    Ubuntu 16.04 e Ubuntu 18.04:

    ```console
    # sudo sed -i 's/http:\/\/archive\.ubuntu\.com\/ubuntu\//http:\/\/azure\.archive\.ubuntu\.com\/ubuntu\//g' /etc/apt/sources.list
    # sudo sed -i 's/http:\/\/[a-z][a-z]\.archive\.ubuntu\.com\/ubuntu\//http:\/\/azure\.archive\.ubuntu\.com\/ubuntu\//g' /etc/apt/sources.list
    # sudo apt-get update
    ```

4. As imagens Ubuntu Azure estão agora a usar o [núcleo à medida do Azure.](https://ubuntu.com/blog/microsoft-and-canonical-increase-velocity-with-azure-tailored-kernel) Atualize o sistema operativo para o mais recente núcleo à medida do Azure e instale ferramentas Azure Linux (incluindo dependências de Hiper-V) executando os seguintes comandos:

    Ubuntu 16.04 e Ubuntu 18.04:

    ```console
    # sudo apt update
    # sudo apt install linux-azure linux-image-azure linux-headers-azure linux-tools-common linux-cloud-tools-common linux-tools-azure linux-cloud-tools-azure
    (recommended) # sudo apt full-upgrade

    # sudo reboot
    ```

5. Modifique a linha de arranque do núcleo para grub para incluir parâmetros adicionais de kernel para Azure. Para o fazer aberto `/etc/default/grub` num editor de texto, encontre a variável chamada `GRUB_CMDLINE_LINUX_DEFAULT` (ou adicione-a se necessário) e edite-a para incluir os seguintes parâmetros:

    ```text
    GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300 quiet splash"
    ```

    Guarde e feche este ficheiro e, em seguida, `sudo update-grub` corra. Isto irá garantir que todas as mensagens de consola são enviadas para a primeira porta em série, que pode ajudar o suporte técnico do Azure com problemas de depuração.

6. Certifique-se de que o servidor SSH está instalado e configurado para começar na hora de arranque.  Este é geralmente o padrão.

7. Instale cloud-init (o agente de provisionamento) e o Agente Azure Linux (o manipulador de extensões de hóspedes). O Cloud-init utiliza o netplan para configurar a configuração da rede do sistema durante o fornecimento e cada arranque subsequente.

    ```console
    # sudo apt update
    # sudo apt install cloud-init netplan.io walinuxagent && systemctl stop walinuxagent
    ```

   > [!Note]
   >  A `walinuxagent` embalagem pode remover as `NetworkManager` embalagens e as `NetworkManager-gnome` embalagens, se forem instaladas.

8. Remova os configs padrão de nuvem e restos de artefactos de planos que podem entrar em conflito com o fornecimento de inícono em Azure:

    ```console
    # rm -f /etc/cloud/cloud.cfg.d/50-curtin-networking.cfg /etc/cloud/cloud.cfg.d/curtin-preserve-sources.cfg
    # rm -f /etc/cloud/ds-identify.cfg
    # rm -f /etc/netplan/*.yaml
    ```

9. Configure a inição de nuvem para o fornecimento do sistema utilizando o recurso de dados Azure:

    ```console
    # cat > /etc/cloud/cloud.cfg.d/90_dpkg.cfg << EOF
    datasource_list: [ Azure ]
    EOF

    # cat > /etc/cloud/cloud.cfg.d/90-azure.cfg << EOF
    system_info:
       package_mirrors:
         - arches: [i386, amd64]
           failsafe:
             primary: http://archive.ubuntu.com/ubuntu
             security: http://security.ubuntu.com/ubuntu
           search:
             primary:
               - http://azure.archive.ubuntu.com/ubuntu/
             security: []
         - arches: [armhf, armel, default]
           failsafe:
             primary: http://ports.ubuntu.com/ubuntu-ports
             security: http://ports.ubuntu.com/ubuntu-ports
    EOF

    # cat > /etc/cloud/cloud.cfg.d/10-azure-kvp.cfg << EOF
    reporting:
      logging:
        type: log
      telemetry:
        type: hyperv
    EOF
    ```

10. Configure o agente Azure Linux para confiar na nuvem para executar o fornecimento. Veja o [projeto WALinuxAgent](https://github.com/Azure/WALinuxAgent) para obter mais informações sobre estas opções.

    ```console
    sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
    sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf

    cat >> /etc/waagent.conf << EOF
    # For Azure Linux agent version >= 2.2.45, this is the option to configure,
    # enable, or disable the provisioning behavior of the Linux agent.
    # Accepted values are auto (default), waagent, cloud-init, or disabled.
    # A value of auto means that the agent will rely on cloud-init to handle
    # provisioning if it is installed and enabled, which in this case it will.
    Provisioning.Agent=auto
    EOF
    ```

11. Limpe os artefactos e registos de runtime do agente Azure Linux:

    ```console
    # sudo cloud-init clean --logs --seed
    # sudo rm -rf /var/lib/cloud/
    # sudo systemctl stop walinuxagent.service
    # sudo rm -rf /var/lib/waagent/
    # sudo rm -f /var/log/waagent.log
    ```

12. Executar os seguintes comandos para desprovisionar a máquina virtual e prepará-la para provisão em Azure:

    > [!NOTE]
    > O `sudo waagent -force -deprovision+user` comando tentará limpar o sistema e torná-lo adequado para realojamento. A `+user` opção elimina a última conta de utilizador a provisionada e os dados associados.

    > [!WARNING]
    > A desprovisionamento utilizando o comando acima não garante que a imagem seja limpa de todas as informações sensíveis e seja adequada para redistribuição.

    ```console
    # sudo waagent -force -deprovision+user
    # rm -f ~/.bash_history
    # export HISTSIZE=0
    # logout
    ```

13. Clique em **Ação -> Desligar** em Hyper-V Manager.

14. O Azure só aceita VHDs de tamanho fixo. Se o disco de oss do VM não for um VHD de tamanho fixo, utilize o `Convert-VHD` cmdlet PowerShell e especifique a `-VHDType Fixed` opção. Por favor, dê uma olhada nos docs para `Convert-VHD` aqui: [Converte-VHD](/powershell/module/hyper-v/convert-vhd).


## <a name="next-steps"></a>Passos seguintes
Está agora pronto para usar o seu disco rígido virtual Ubuntu Linux para criar novas máquinas virtuais em Azure. Se esta for a primeira vez que está a enviar o ficheiro .vhd para a Azure, consulte [Create a Linux VM a partir de um disco personalizado](upload-vhd.md#option-1-upload-a-vhd).