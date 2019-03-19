---
title: Criar e carregar um VHD do SUSE Linux no Azure
description: Saiba como criar e carregar um Azure disco rígido virtual (VHD) que contém um sistema operativo SUSE Linux.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 066d01a6-2a54-4718-bcd0-90fe7a5303a1
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: szark
ms.openlocfilehash: 2b0c01ee4b1d1bc5ce83fc0afc309abfcf25f33e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57996686"
---
# <a name="prepare-a-sles-or-opensuse-virtual-machine-for-azure"></a>Preparar uma máquina virtual SLES ou openSUSE para o Azure
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que já instalou uma SUSE ou openSUSE sistema operativo Linux para um disco rígido virtual. Existem várias ferramentas para criar ficheiros. vhd, por exemplo uma solução de virtualização, como o Hyper-V. Para obter instruções, consulte [instalar a função Hyper-V e configurar uma Máquina Virtual](https://technet.microsoft.com/library/hh846766.aspx).

### <a name="sles--opensuse-installation-notes"></a>SLES / observações de instalação do openSUSE
* Consulte também [observações de instalação de Linux geral](create-upload-generic.md#general-linux-installation-notes) para obter mais dicas sobre como preparar o Linux para o Azure.
* O formato VHDX não é suportado no Azure, apenas **fixo VHD**.  Pode converter o disco para o formato VHD utilizando o Gestor de Hyper-V ou o cmdlet convert-vhd.
* Ao instalar o sistema Linux é recomendado que utilize partições padrão em vez de LVM (muitas vezes, o padrão para muitas instalações). Isso evitará LVM nome entra em conflito com VMs Clonadas, especialmente se um disco de SO algum dia precisar ser anexados a outra VM para resolução de problemas. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) pode ser utilizada em discos de dados, se preferirem.
* Não configure uma partição de troca no disco do SO. O agente Linux pode ser configurado para criar um ficheiro de troca no disco de recursos temporário.  Podem encontrar mais informações sobre este assunto nos passos abaixo.
* Todos os VHDs no Azure tem de ter um tamanho virtual alinhado para 1MB. Ao converter a partir de um disco não processado para o VHD tem de se certificar de que o tamanho de disco bruto é um múltiplo de 1MB antes da conversão. Ver [observações de instalação de Linux](create-upload-generic.md#general-linux-installation-notes) para obter mais informações.

## <a name="use-suse-studio"></a>Utilize o SUSE Studio
[SUSE Studio](http://www.susestudio.com) pode facilmente criar e gerir as imagens SLES e openSUSE para o Azure e Hyper-V. Esta é a abordagem recomendada para personalizar as suas próprias imagens SLES e openSUSE.

Como alternativa à criação de seu próprio VHD, SUSE também as publica imagens BYOS (traga a sua subscrição próprio) para SLES na [VMDepot](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/04/using-and-contributing-vms-to-vm-depot.pdf).

## <a name="prepare-suse-linux-enterprise-server-11-sp4"></a>Prepare SUSE Linux Enterprise Server 11 SP4
1. No painel central do Gestor de Hyper-V, selecione a máquina virtual.
2. Clique em **Connect** para abrir a janela para a máquina virtual.
3. Registe o seu sistema de SUSE Linux Enterprise para permiti-lo transferir as atualizações e instalar pacotes.
4. Atualize o sistema com os patches mais recentes:
   
        # sudo zypper update
5. Instale o agente Linux do Azure a partir do repositório do SLES:
   
        # sudo zypper install python-azure-agent
6. Verifique se waagent estiver definido como "ativado" no chkconfig e, se não estiver, ativá-lo para inicialização automática:
   
        # sudo chkconfig waagent on
7. Verifique se o serviço de waagent está em execução e se não, inicie-o: 
   
        # sudo service waagent start
8. Modificar a linha de arranque de kernel em sua configuração de grub para incluir parâmetros de kernel adicionais para o Azure. Para fazer este aberto "/ boot/grub/menu.lst" num editor de texto e certifique-se de que o kernel padrão inclui os seguintes parâmetros:
   
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
    Isto garante que todas as mensagens de consola são enviadas para a primeira porta serial, que pode ajudá-lo Azure suporte com a depuração de problemas.
9. Certifique-se que /boot/grub/menu.lst e /etc/fstab. ambas referenciam o disco com o UUID (ao uuid) em vez do ID de disco (pelo id). 
   
    Obter disco de UUID
   
        # ls /dev/disk/by-uuid/
   
    Se /dev/disk/by-id / é utilizado, atualizar /boot/grub/menu.lst tanto/etc/fstab com o valor adequado ao uuid
   
    Antes de alteração
   
        root=/dev/disk/by-id/SCSI-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx-part1
   
    Após a alteração
   
        root=/dev/disk/by-uuid/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
10. Modificar regras de udev para evitar a geração de regras de estáticas para a interface de Ethernet (s). Estas regras podem causar problemas ao clonar uma máquina virtual no Microsoft Azure ou o Hyper-v:
    
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
11. É recomendado editar o ficheiro "/ etc/sysconfig/rede/dhcp" e altere o `DHCLIENT_SET_HOSTNAME` parâmetro para o seguinte:
    
     DHCLIENT_SET_HOSTNAME="no"
12. Em "/ etc/sudoers", comenta ou remover as seguintes linhas, caso existam:
    
     Predefinições targetpw # solicitar a palavra-passe de utilizador de destino raiz, ou seja, todos os todos os ALL=(ALL) # aviso! Só utilize esta opção juntamente com 'Predefinições targetpw'!
13. Certifique-se de que o servidor SSH está instalado e configurado para iniciar no momento da inicialização.  Normalmente, esta é a predefinição.
14. Não é crie o espaço de comutação no disco do SO.
    
    O agente Linux do Azure podem configurar automaticamente o espaço de comutação com o disco de recurso local que está ligado à VM após o aprovisionamento no Azure. Tenha em atenção que o disco de recurso local é uma *temporária* disco e poderá ser esvaziada quando a VM é desaprovisionada. Depois de instalar o agente Linux do Azure (consulte o passo anterior), modifique os parâmetros seguintes em /etc/waagent.Conf. adequadamente:
    
     ResourceDisk.Format=y ResourceDisk.Filesystem=ext4 ResourceDisk.MountPoint=/mnt/resource ResourceDisk.EnableSwap=y ResourceDisk.SwapSizeMB=2048 # # Nota: defina esta opção para tudo o que precisar que ele seja.
15. Execute os seguintes comandos para desaprovisionar a máquina virtual e prepará-lo para o aprovisionamento no Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
16. Clique em **ação -> encerrar baixo** no Gestor de Hyper-V. O VHD do Linux está agora pronto para ser carregado para o Azure.

- - -
## <a name="prepare-opensuse-131"></a>Preparar o openSUSE 13.1 +
1. No painel central do Gestor de Hyper-V, selecione a máquina virtual.
2. Clique em **Connect** para abrir a janela para a máquina virtual.
3. No shell, execute o comando "`zypper lr`'. Se este comando devolve o resultado semelhante ao seguinte, em seguida, os repositórios estão configurados como esperado – sem ajustes são necessários (Observe que os números de versão podem variar):
   
        # | Alias                 | Name                  | Enabled | Refresh
        --+-----------------------+-----------------------+---------+--------
        1 | Cloud:Tools_13.1      | Cloud:Tools_13.1      | Yes     | Yes
        2 | openSUSE_13.1_OSS     | openSUSE_13.1_OSS     | Yes     | Yes
        3 | openSUSE_13.1_Updates | openSUSE_13.1_Updates | Yes     | Yes
   
    Se o comando não devolve "Nenhum repositório definido...", em seguida, utilize os seguintes comandos para adicionar estes repositórios:
   
        # sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_13.1 Cloud:Tools_13.1
        # sudo zypper ar -f https://download.opensuse.org/distribution/13.1/repo/oss openSUSE_13.1_OSS
        # sudo zypper ar -f http://download.opensuse.org/update/13.1 openSUSE_13.1_Updates
   
    Em seguida, pode verificar os repositórios foram adicionados ao executar o comando "`zypper lr`" novamente. No caso de um dos repositórios do atualização relevantes não estiver ativado, ative-o com o seguinte comando:
   
        # sudo zypper mr -e [NUMBER OF REPOSITORY]
4. O kernel de atualização para a versão mais recente disponível:
   
        # sudo zypper up kernel-default
   
    Ou para atualizar o sistema com todos os patches mais recentes:
   
        # sudo zypper update
5. Instale o agente Linux do Azure.
   
        # sudo zypper install WALinuxAgent
6. Modificar a linha de arranque de kernel em sua configuração de grub para incluir parâmetros de kernel adicionais para o Azure. Para tal, abra "/ boot/grub/menu.lst" num editor de texto e certifique-se de que o kernel padrão inclui os seguintes parâmetros:
   
     console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
   Isto garante que todas as mensagens de consola são enviadas para a primeira porta serial, que pode ajudá-lo Azure suporte com a depuração de problemas. Além disso, remova os seguintes parâmetros da linha de arranque de kernel, caso existam:
   
     libata.atapi_enabled=0 reserve=0x1f0,0x8
7. É recomendado editar o ficheiro "/ etc/sysconfig/rede/dhcp" e altere o `DHCLIENT_SET_HOSTNAME` parâmetro para o seguinte:
   
     DHCLIENT_SET_HOSTNAME="no"
8. **Importante:** Em "/ etc/sudoers", comenta ou remover as seguintes linhas, caso existam:
   
     Predefinições targetpw # solicitar a palavra-passe de utilizador de destino raiz, ou seja, todos os todos os ALL=(ALL) # aviso! Só utilize esta opção juntamente com 'Predefinições targetpw'!
9. Certifique-se de que o servidor SSH está instalado e configurado para iniciar no momento da inicialização.  Normalmente, esta é a predefinição.
10. Não é crie o espaço de comutação no disco do SO.
    
    O agente Linux do Azure podem configurar automaticamente o espaço de comutação com o disco de recurso local que está ligado à VM após o aprovisionamento no Azure. Tenha em atenção que o disco de recurso local é uma *temporária* disco e poderá ser esvaziada quando a VM é desaprovisionada. Depois de instalar o agente Linux do Azure (consulte o passo anterior), modifique os parâmetros seguintes em /etc/waagent.Conf. adequadamente:
    
     ResourceDisk.Format=y ResourceDisk.Filesystem=ext4 ResourceDisk.MountPoint=/mnt/resource ResourceDisk.EnableSwap=y ResourceDisk.SwapSizeMB=2048 # # Nota: defina esta opção para tudo o que precisar que ele seja.
11. Execute os seguintes comandos para desaprovisionar a máquina virtual e prepará-lo para o aprovisionamento no Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
12. Certifique-se de que o agente Linux do Azure é executado na inicialização:
    
        # sudo systemctl enable waagent.service
13. Clique em **ação -> encerrar baixo** no Gestor de Hyper-V. O VHD do Linux está agora pronto para ser carregado para o Azure.

## <a name="next-steps"></a>Passos Seguintes
Agora, está pronto para utilizar o seu disco rígido virtual do SUSE Linux para criar novas máquinas virtuais no Azure. Se esta for a primeira vez que está a carregar o ficheiro. vhd para o Azure, veja [criar uma VM do Linux a partir de um disco personalizado](upload-vhd.md#option-1-upload-a-vhd).
