---
title: Criar e carregar um VHD do SUSE Linux no Azure
description: Saiba como criar e carregar um VHD (disco rígido virtual) do Azure que contém um sistema operacional SUSE Linux.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: gwallace
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 066d01a6-2a54-4718-bcd0-90fe7a5303a1
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 03/12/2018
ms.author: szark
ms.openlocfilehash: d3241229fcf3ef99f71185c452ae615ec2cfc889
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70091212"
---
# <a name="prepare-a-sles-or-opensuse-virtual-machine-for-azure"></a>Preparar uma máquina virtual SLES ou openSUSE para o Azure
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que você já tenha instalado um sistema operacional Linux SUSE ou openSUSE em um disco rígido virtual. Existem várias ferramentas para criar arquivos. VHD, por exemplo, uma solução de virtualização, como o Hyper-V. Para obter instruções, consulte [instalar a função Hyper-V e configurar uma máquina virtual](https://technet.microsoft.com/library/hh846766.aspx).

### <a name="sles--opensuse-installation-notes"></a>Notas de instalação do SLES/openSUSE
* Consulte também as [notas de instalação gerais do Linux](create-upload-generic.md#general-linux-installation-notes) para obter mais dicas sobre como preparar o Linux para o Azure.
* Não há suporte para o formato VHDX no Azure, somente **VHD fixo**.  Você pode converter o disco para o formato VHD usando o Gerenciador do Hyper-V ou o cmdlet Convert-VHD.
* Ao instalar o sistema Linux, é recomendável que você use partições padrão em vez de LVM (geralmente o padrão para muitas instalações). Isso evitará conflitos de nome LVM com VMs clonadas, especialmente se um disco do sistema operacional precisar ser anexado a outra VM para solução de problemas. O [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou o [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) pode ser usado em discos de dados, se for preferencial.
* Não configure uma partição de permuta no disco do sistema operacional. O agente do Linux pode ser configurado para criar um arquivo de permuta no disco de recursos temporário.  Mais informações sobre isso podem ser encontradas nas etapas abaixo.
* Todos os VHDs no Azure devem ter um tamanho virtual alinhado a 1 MB. Ao converter de um disco bruto para VHD, você deve garantir que o tamanho do disco bruto seja um múltiplo de 1MB antes da conversão. Consulte [notas de instalação do Linux](create-upload-generic.md#general-linux-installation-notes) para obter mais informações.

## <a name="use-suse-studio"></a>Usar o SUSE Studio
O [SuSE Studio](http://www.susestudio.com) pode criar e gerenciar facilmente suas imagens SLES e openSUSE para o Azure e o Hyper-V. Esta é a abordagem recomendada para personalizar suas próprias imagens SLES e openSUSE.

Como uma alternativa para compilar seu próprio VHD, o SUSE também publica imagens BYOS (traga sua própria assinatura) para SLES em [VMDepot](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/04/using-and-contributing-vms-to-vm-depot.pdf).

## <a name="prepare-suse-linux-enterprise-server-11-sp4"></a>Preparar o SUSE Linux Enterprise Server 11 SP4
1. No painel central do Gerenciador do Hyper-V, selecione a máquina virtual.
2. Clique em **conectar** para abrir a janela da máquina virtual.
3. Registre seu sistema do SUSE Linux Enterprise para permitir que ele baixe atualizações e instale pacotes.
4. Atualize o sistema com os patches mais recentes:
   
        # sudo zypper update
5. Instale o agente Linux do Azure do repositório SLES:
   
        # sudo zypper install python-azure-agent
6. Verifique se waagent está definido como "on" em comando chkconfig e, se não estiver, habilite-o para inicialização automática:
   
        # sudo chkconfig waagent on
7. Verifique se o serviço waagent está em execução e, se não estiver, inicie-o: 
   
        # sudo service waagent start
8. Modifique a linha de inicialização do kernel em sua configuração do grub para incluir parâmetros de kernel adicionais para o Azure. Para fazer isso, abra "/boot/grub/menu.lst" em um editor de texto e verifique se o kernel padrão inclui os seguintes parâmetros:
   
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
    Isso garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, o que pode auxiliar o suporte do Azure com problemas de depuração.
9. Confirme se/boot/grub/menu.lst e/etc/fstab fazem referência ao disco usando seu UUID (por UUID) em vez da ID do disco (por ID). 
   
    Obter UUID de disco
   
        # ls /dev/disk/by-uuid/
   
    Se/dev/disk/by-ID/for usado, atualize/boot/grub/menu.lst e/etc/fstab com o valor apropriado de-UUID
   
    Antes da alteração
   
        root=/dev/disk/by-id/SCSI-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx-part1
   
    Após a alteração
   
        root=/dev/disk/by-uuid/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
10. Modifique as regras de udev para evitar a geração de regras estáticas para as interfaces de Ethernet. Essas regras podem causar problemas ao clonar uma máquina virtual no Microsoft Azure ou no Hyper-V:
    
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
11. É recomendável editar o arquivo "/etc/sysconfig/network/DHCP" e alterar o `DHCLIENT_SET_HOSTNAME` parâmetro para o seguinte:
    
     DHCLIENT_SET_HOSTNAME="no"
12. Em "/etc/sudoers", comente ou remova as seguintes linhas, se existirem:
    
     Padrões targetpw # solicitam a senha do usuário de destino, ou seja, raiz ALL todos = (ALL) todos os # WARNING! Use-o apenas junto com ' defaults targetpw '!
13. Verifique se o servidor SSH está instalado e configurado para iniciar no momento da inicialização.  Normalmente, isso é o padrão.
14. Não crie espaço de permuta no disco do sistema operacional.
    
    O agente Linux do Azure pode configurar automaticamente o espaço de permuta usando o disco de recurso local que é anexado à VM após o provisionamento no Azure. Observe que o disco de recurso local é um disco *temporário* e pode ser esvaziado quando a VM é desprovisionada. Depois de instalar o agente Linux do Azure (consulte a etapa anterior), modifique os seguintes parâmetros no/etc/waagent.conf adequadamente:
    
     ResourceDisk. Format = y ResourceDisk. FileSystem = ext4 ResourceDisk. MountPoint =/mnt/Resource ResourceDisk. EnableSwap = y ResourceDisk. SwapSizeMB = 2048 # # Observação: Defina isso para o que for necessário.
15. Execute os seguintes comandos para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
16. Clique em **ação-> desligar** no Gerenciador do Hyper-V. Seu VHD do Linux agora está pronto para ser carregado no Azure.

---
## <a name="prepare-opensuse-131"></a>Preparar openSUSE 13.1 +
1. No painel central do Gerenciador do Hyper-V, selecione a máquina virtual.
2. Clique em **conectar** para abrir a janela da máquina virtual.
3. No Shell, execute o comando '`zypper lr`'. Se esse comando retornar uma saída semelhante à seguinte, os repositórios serão configurados como esperado--nenhum ajuste é necessário (Observe que os números de versão podem variar):
   
        # | Alias                 | Name                  | Enabled | Refresh
        --+-----------------------+-----------------------+---------+--------
        1 | Cloud:Tools_13.1      | Cloud:Tools_13.1      | Yes     | Yes
        2 | openSUSE_13.1_OSS     | openSUSE_13.1_OSS     | Yes     | Yes
        3 | openSUSE_13.1_Updates | openSUSE_13.1_Updates | Yes     | Yes
   
    Se o comando retornar "nenhum repositório definido..." em seguida, use os seguintes comandos para adicionar esses repositórios:
   
        # sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_13.1 Cloud:Tools_13.1
        # sudo zypper ar -f https://download.opensuse.org/distribution/13.1/repo/oss openSUSE_13.1_OSS
        # sudo zypper ar -f http://download.opensuse.org/update/13.1 openSUSE_13.1_Updates
   
    Em seguida, você pode verificar se os repositórios foram adicionados executando o comando '`zypper lr`' novamente. Caso um dos repositórios de atualização relevantes não esteja habilitado, habilite-o com o seguinte comando:
   
        # sudo zypper mr -e [NUMBER OF REPOSITORY]
4. Atualize o kernel para a versão mais recente disponível:
   
        # sudo zypper up kernel-default
   
    Ou para atualizar o sistema com todos os patches mais recentes:
   
        # sudo zypper update
5. Instale o agente Linux do Azure.
   
        # sudo zypper install WALinuxAgent
6. Modifique a linha de inicialização do kernel em sua configuração do grub para incluir parâmetros de kernel adicionais para o Azure. Para fazer isso, abra "/boot/grub/menu.lst" em um editor de texto e verifique se o kernel padrão inclui os seguintes parâmetros:
   
     console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
   Isso garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, o que pode auxiliar o suporte do Azure com problemas de depuração. Além disso, remova os seguintes parâmetros da linha de inicialização do kernel, se existirem:
   
     libata. atapi_enabled = 0 Reserve = 0x1f0, 0x8
7. É recomendável editar o arquivo "/etc/sysconfig/network/DHCP" e alterar o `DHCLIENT_SET_HOSTNAME` parâmetro para o seguinte:
   
     DHCLIENT_SET_HOSTNAME="no"
8. **Importante:** Em "/etc/sudoers", comente ou remova as seguintes linhas, se existirem:
   
     Padrões targetpw # solicitam a senha do usuário de destino, ou seja, raiz ALL todos = (ALL) todos os # WARNING! Use-o apenas junto com ' defaults targetpw '!
9. Verifique se o servidor SSH está instalado e configurado para iniciar no momento da inicialização.  Normalmente, isso é o padrão.
10. Não crie espaço de permuta no disco do sistema operacional.
    
    O agente Linux do Azure pode configurar automaticamente o espaço de permuta usando o disco de recurso local que é anexado à VM após o provisionamento no Azure. Observe que o disco de recurso local é um disco *temporário* e pode ser esvaziado quando a VM é desprovisionada. Depois de instalar o agente Linux do Azure (consulte a etapa anterior), modifique os seguintes parâmetros no/etc/waagent.conf adequadamente:
    
     ResourceDisk. Format = y ResourceDisk. FileSystem = ext4 ResourceDisk. MountPoint =/mnt/Resource ResourceDisk. EnableSwap = y ResourceDisk. SwapSizeMB = 2048 # # Observação: Defina isso para o que for necessário.
11. Execute os seguintes comandos para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
12. Verifique se o agente Linux do Azure é executado na inicialização:
    
        # sudo systemctl enable waagent.service
13. Clique em **ação-> desligar** no Gerenciador do Hyper-V. Seu VHD do Linux agora está pronto para ser carregado no Azure.

## <a name="next-steps"></a>Passos seguintes
Agora você está pronto para usar o disco rígido virtual SUSE Linux para criar novas máquinas virtuais no Azure. Se esta for a primeira vez que você está carregando o arquivo. vhd no Azure, consulte [criar uma VM do Linux a partir de um disco personalizado](upload-vhd.md#option-1-upload-a-vhd).
