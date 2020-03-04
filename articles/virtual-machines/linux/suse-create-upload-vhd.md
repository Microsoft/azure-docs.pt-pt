---
title: Crie e carregue um VHD SUSE Linux em Azure
description: Aprenda a criar e carregar um disco rígido virtual Azure (VHD) que contenha um sistema operativo SUSE Linux.
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 066d01a6-2a54-4718-bcd0-90fe7a5303a1
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 03/12/2018
ms.author: mimckitt
ms.openlocfilehash: d64fc55159ddc3ce88397879958a63bf30808ad9
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78251577"
---
# <a name="prepare-a-sles-or-opensuse-virtual-machine-for-azure"></a>Prepare uma SLES ou abra a máquina virtual SUSE para o Azure


Este artigo pressupõe que já instalou um sistema operativo SUSE ou abriu o sistema operativo SUSE Linux para um disco rígido virtual. Existem múltiplas ferramentas para criar ficheiros .vhd, por exemplo, uma solução de virtualização como o Hyper-V. Para obter instruções, consulte [Instale a função Hyper-V e configure uma máquina virtual](https://technet.microsoft.com/library/hh846766.aspx).

## <a name="sles--opensuse-installation-notes"></a>Notas de instalação SLES / openSUSE
* Consulte também as Notas de [Instalação do General Linux](create-upload-generic.md#general-linux-installation-notes) para obter mais dicas sobre a preparação do Linux para o Azure.
* O formato VHDX não é suportado em Azure, apenas **VHD fixo**.  Pode converter o disco em formato VHD utilizando o Hyper-V Manager ou o cmdlet convert-vhd.
* Ao instalar o sistema Linux, recomenda-se que utilize divisórias padrão em vez de LVM (muitas vezes o padrão para muitas instalações). Isto evitará conflitos de nome lvm com VMs clonados, especialmente se um disco de SO alguma vez precisar de ser ligado a outro VM para resolução de problemas. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) podem ser utilizados em discos de dados, se preferir.
* Não configure uma divisória de troca no disco OS. O agente Linux pode ser configurado para criar um ficheiro de troca no disco de recursos temporários.  Mais informações sobre isso podem ser encontradas nos degraus abaixo.
* Todos os VHDs em Azure devem ter um tamanho virtual alinhado a 1MB. Ao converter de um disco cru para VHD, deve certificar-se de que o tamanho do disco bruto é um múltiplo de 1MB antes da conversão. Consulte as Notas de [Instalação do Linux](create-upload-generic.md#general-linux-installation-notes) para obter mais informações.

## <a name="use-suse-studio"></a>Use estúdio SUSE
[O Estúdio SUSE](http://www.susestudio.com) pode facilmente criar e gerir as suas SLES e abrir imagens SUSE para Azure e Hyper-V. Esta é a abordagem recomendada para personalizar as suas próprias SLES e abrir imagens SUSE.

Como alternativa à construção do seu próprio VHD, a SUSE também publica imagens BYOS (Bring Your Own Subscription) para SLES no [VMDepot](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/04/using-and-contributing-vms-to-vm-depot.pdf).

## <a name="prepare-suse-linux-enterprise-server-11-sp4"></a>Prepare o Servidor Empresarial SUSE Linux 11 SP4
1. No painel central do Hyper-V Manager, selecione a máquina virtual.
2. Clique **em Ligar** para abrir a janela para a máquina virtual.
3. Registe o seu sistema SUSE Linux Enterprise para permitir o download de atualizações e instalação de pacotes.
4. Atualize o sistema com as últimas correções:
   
        # sudo zypper update
5. Instale o Agente Azure Linux no repositório SLES:
   
        # sudo zypper install python-azure-agent
6. Verifique se o waagent está definido para "ligar" em chkconfig e, se não, ative-o para o arranque automático:
   
        # sudo chkconfig waagent on
7. Verifique se o serviço de waagent está em funcionamento e, se não, inicie-o: 
   
        # sudo service waagent start
8. Modifique a linha de arranque do núcleo na sua configuração de larva para incluir parâmetros adicionais de kernel para o Azure. Para fazer isto aberto "/boot/grub/menu.lst" num editor de texto e certifique-se de que o núcleo predefinido inclui os seguintes parâmetros:
   
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
    Isto garantirá que todas as mensagens de consola são enviadas para a primeira porta em série, que pode ajudar o suporte do Azure com problemas de depuração.
9. Confirme que/boot/grub/menu.lst e /etc/fstab ambos referenciam o disco utilizando o seu UUID (by-uuid) em vez do ID do disco (by-id). 
   
    Obtenha uUID do disco
   
        # ls /dev/disk/by-uuid/
   
    Se for utilizado /dev/disco/by-id/ atualização tanto /boot/grub/menu.lst e /etc/fstab com o valor uuide adequado
   
    Antes da mudança
   
        root=/dev/disk/by-id/SCSI-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx-part1
   
    Após a mudança
   
        root=/dev/disk/by-uuid/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
10. Modificar as regras udev para evitar gerar regras estáticas para a interface Ethernet. Estas regras podem causar problemas ao clonar uma máquina virtual no Microsoft Azure ou Hyper-V:
    
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
11. Recomenda-se editar o ficheiro "/etc/sysconfig/network/dhcp" e alterar o parâmetro `DHCLIENT_SET_HOSTNAME` para o seguinte:
    
     DHCLIENT_SET_HOSTNAME="no"
12. Em "/etc/sudoers", comentar ou remover as seguintes linhas se existirem:
    
    ```
     Defaults targetpw   # ask for the password of the target user i.e. root
     ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!
     ```
13. Certifique-se de que o servidor SSH está instalado e configurado para iniciar no momento do arranque.  Este é geralmente o padrão.
14. Não crie espaço de troca no disco OS.
    
    O Agente Azure Linux pode configurar automaticamente o espaço de troca utilizando o disco de recursos locais que está ligado ao VM após o fornecimento no Azure. Note que o disco de recursos locais é um disco *temporário,* e pode ser esvaziado quando o VM é desprovisionado. Depois de instalar o Agente Azure Linux (ver passo anterior), modifique adequadamente os seguintes parâmetros em /etc/waagent.conf adequadamente:
    
     ResourceDisk.Format=y ResourceDisk.Filesystem=ext4 ResourceDisk.MountPoint=/mnt/resource ResourceDisk.EnableSwap=y ResourceDisk.SwapSizeMB=2048 ## NOTA: detete isto para o que for necessário.
15. Executar os seguintes comandos para desprovisionar a máquina virtual e prepará-la para o fornecimento em Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
16. Clique em **Ação -> Desligue** em Hiper-V Manager. O seu VHD Linux está agora pronto para ser enviado para o Azure.

---
## <a name="prepare-opensuse-131"></a>Prepare abrir SUSE 13.1+
1. No painel central do Hyper-V Manager, selecione a máquina virtual.
2. Clique **em Ligar** para abrir a janela para a máquina virtual.
3. Na concha, executar o comando`zypper lr`'. Se este comando devolver a saída semelhante à seguinte, então os repositórios são configurados como esperado - não são necessários ajustes (note que os números da versão podem variar):
   
        # | Alias                 | Name                  | Enabled | Refresh
        --+-----------------------+-----------------------+---------+--------
        1 | Cloud:Tools_13.1      | Cloud:Tools_13.1      | Yes     | Yes
        2 | openSUSE_13.1_OSS     | openSUSE_13.1_OSS     | Yes     | Yes
        3 | openSUSE_13.1_Updates | openSUSE_13.1_Updates | Yes     | Yes
   
    Se o comando devolver "Sem repositórios definidos..." em seguida, utilize os seguintes comandos para adicionar estes repos:
   
        # sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_13.1 Cloud:Tools_13.1
        # sudo zypper ar -f https://download.opensuse.org/distribution/13.1/repo/oss openSUSE_13.1_OSS
        # sudo zypper ar -f http://download.opensuse.org/update/13.1 openSUSE_13.1_Updates
   
    Em seguida, pode verificar se os repositórios foram adicionados executando novamente o comando`zypper lr`' ' No caso de um dos repositórios de atualização relevante não estiver ativado, ative-o com o seguinte comando:
   
        # sudo zypper mr -e [NUMBER OF REPOSITORY]
4. Atualize o núcleo para a versão mais recente disponível:
   
        # sudo zypper up kernel-default
   
    Ou atualizar o sistema com todos os patches mais recentes:
   
        # sudo zypper update
5. Instale o Agente Azure Linux.
   
        # sudo zypper install WALinuxAgent
6. Modifique a linha de arranque do núcleo na sua configuração de larva para incluir parâmetros adicionais de kernel para o Azure. Para tal, abra "/boot/grub/menu.lst" num editor de texto e certifique-se de que o núcleo predefinido inclui os seguintes parâmetros:
   
     console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
   Isto garantirá que todas as mensagens de consola são enviadas para a primeira porta em série, que pode ajudar o suporte do Azure com problemas de depuração. Além disso, retire os seguintes parâmetros da linha de arranque do núcleo se existirem:
   
     libata.atapi_enabled=0 reserva=0x1f0,0x8
7. Recomenda-se editar o ficheiro "/etc/sysconfig/network/dhcp" e alterar o parâmetro `DHCLIENT_SET_HOSTNAME` para o seguinte:
   
     DHCLIENT_SET_HOSTNAME="no"
8. **Importante:** Em "/etc/sudoers", comentar ou remover as seguintes linhas se existirem:
     
     ```
     Defaults targetpw   # ask for the password of the target user i.e. root
     ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!
     ```

9. Certifique-se de que o servidor SSH está instalado e configurado para iniciar no momento do arranque.  Este é geralmente o padrão.
10. Não crie espaço de troca no disco OS.
    
    O Agente Azure Linux pode configurar automaticamente o espaço de troca utilizando o disco de recursos locais que está ligado ao VM após o fornecimento no Azure. Note que o disco de recursos locais é um disco *temporário,* e pode ser esvaziado quando o VM é desprovisionado. Depois de instalar o Agente Azure Linux (ver passo anterior), modifique adequadamente os seguintes parâmetros em /etc/waagent.conf adequadamente:
    
     ResourceDisk.Format=y ResourceDisk.Filesystem=ext4 ResourceDisk.MountPoint=/mnt/resource ResourceDisk.EnableSwap=y ResourceDisk.SwapSizeMB=2048 ## NOTA: detete isto para o que for necessário.
11. Executar os seguintes comandos para desprovisionar a máquina virtual e prepará-la para o fornecimento em Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
12. Certifique-se de que o Agente Azure Linux funciona no arranque:
    
        # sudo systemctl enable waagent.service
13. Clique em **Ação -> Desligue** em Hiper-V Manager. O seu VHD Linux está agora pronto para ser enviado para o Azure.

## <a name="next-steps"></a>Passos seguintes
Está agora pronto para usar o seu disco rígido virtual SUSE Linux para criar novas máquinas virtuais em Azure. Se esta for a primeira vez que está a enviar o ficheiro .vhd para o Azure, consulte [Create a Linux VM a partir de um disco personalizado](upload-vhd.md#option-1-upload-a-vhd).
