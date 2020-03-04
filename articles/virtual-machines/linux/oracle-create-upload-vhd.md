---
title: Crie e carregue um VHD Oracle Linux
description: Aprenda a criar e carregar um disco rígido virtual Azure (VHD) que contenha um sistema operativo Oracle Linux.
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: tysonn
tags: azure-service-management,azure-resource-manager
ms.assetid: dd96f771-26eb-4391-9a89-8c8b6d691822
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 12/10/2019
ms.author: mimckitt
ms.openlocfilehash: 240333e55f23f2536d3cf14d2bb817e5776c8139
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78251601"
---
# <a name="prepare-an-oracle-linux-virtual-machine-for-azure"></a>Prepare uma máquina virtual Oracle Linux para o Azure

Este artigo assume que já instalou um sistema operativo Oracle Linux num disco rígido virtual. Existem múltiplas ferramentas para criar ficheiros .vhd, por exemplo, uma solução de virtualização como o Hyper-V. Para obter instruções, consulte [Instale a função Hyper-V e configure uma máquina virtual](https://technet.microsoft.com/library/hh846766.aspx).

## <a name="oracle-linux-installation-notes"></a>Notas de instalação oracle Linux
* Consulte também as Notas de [Instalação do General Linux](create-upload-generic.md#general-linux-installation-notes) para obter mais dicas sobre a preparação do Linux para o Azure.
* Hyper-V e Azure suportam oracle Linux com o Unbreakable Enterprise Kernel (UEK) ou o Red Hat Compatible Kernel.
* O UEK2 da Oracle não é suportado em Hyper-V e Azure, uma vez que não inclui os condutores necessários.
* O formato VHDX não é suportado em Azure, apenas **VHD fixo**.  Pode converter o disco em formato VHD utilizando o Hyper-V Manager ou o cmdlet convert-vhd.
* Ao instalar o sistema Linux, recomenda-se que utilize divisórias padrão em vez de LVM (muitas vezes o padrão para muitas instalações). Isto evitará conflitos de nome lvm com VMs clonados, especialmente se um disco de SO alguma vez precisar de ser ligado a outro VM para resolução de problemas. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) podem ser utilizados em discos de dados, se preferir.
* As versões de kernel Linux anteriores a 2.6.37 não suportam o NUMA em Hyper-V com tamanhos VM maiores. Esta questão afeta principalmente as distribuições mais antigas usando o kernel 2.6.32 vermelho a montante, e foi fixado no Oracle Linux 6.6 e mais tarde
* Não configure uma divisória de troca no disco OS. O agente Linux pode ser configurado para criar um ficheiro de troca no disco de recursos temporários.  Mais informações sobre isso podem ser encontradas nos degraus abaixo.
* Todos os VHDs em Azure devem ter um tamanho virtual alinhado a 1MB. Ao converter de um disco cru para VHD, deve certificar-se de que o tamanho do disco bruto é um múltiplo de 1MB antes da conversão. Consulte as Notas de [Instalação do Linux](create-upload-generic.md#general-linux-installation-notes) para obter mais informações.
* Certifique-se de que o repositório `Addons` está ativado. Editar o ficheiro `/etc/yum.repos.d/public-yum-ol6.repo`(Oracle Linux 6) ou `/etc/yum.repos.d/public-yum-ol7.repo`(Oracle Linux 7) e alterar a linha `enabled=0` para `enabled=1` em **[ol6_addons]** ou **[ol7_addons]** neste ficheiro.

## <a name="oracle-linux-64-and-later"></a>Oracle Linux 6.4 e mais tarde
Deve completar etapas de configuração específicas no sistema operativo para que a máquina virtual funcione em Azure.

1. No painel central do Hyper-V Manager, selecione a máquina virtual.
2. Clique **em Ligar** para abrir a janela para a máquina virtual.
3. Desinstale o NetworkManager executando o seguinte comando:
   
        # sudo rpm -e --nodeps NetworkManager
   
    **Nota:** Se a embalagem ainda não estiver instalada, este comando falhará com uma mensagem de erro. Isto era esperado.
4. Crie um ficheiro chamado **rede** no diretório `/etc/sysconfig/` que contenha o seguinte texto:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain
5. Crie um ficheiro chamado **ifcfg-eth0** no `/etc/sysconfig/network-scripts/` diretório que contenha o seguinte texto:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
6. Modificar as regras udev para evitar gerar regras estáticas para a interface Ethernet. Estas regras podem causar problemas ao clonar uma máquina virtual no Microsoft Azure ou Hyper-V:
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
7. Certifique-se de que o serviço de rede começará no momento do arranque executando o seguinte comando:
   
        # chkconfig network on
8. Instale python-pyasn1 executando o seguinte comando:
   
        # sudo yum install python-pyasn1
9. Modifique a linha de arranque do núcleo na sua configuração de larva para incluir parâmetros adicionais de kernel para o Azure. Para fazer isto aberto "/boot/grub/menu.lst" num editor de texto e certifique-se de que o núcleo inclui os seguintes parâmetros:
   
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
   Isto garantirá que todas as mensagens de consola são enviadas para a primeira porta em série, que pode ajudar o suporte do Azure com problemas de depuração.
   
   Para além do que precede, recomenda-se *a remoção* dos seguintes parâmetros:
   
        rhgb quiet crashkernel=auto
   
   A bota gráfica e tranquila não é útil num ambiente de nuvem onde queremos que todos os troncos sejam enviados para a porta de série.
   
   A opção `crashkernel` pode ser deixada configurada se desejar, mas note que este parâmetro reduzirá a quantidade de memória disponível no VM em 128MB ou mais, o que pode ser problemático nos tamanhos de VM menores.
10. Certifique-se de que o servidor SSH está instalado e configurado para iniciar no momento do arranque.  Este é geralmente o padrão.
11. Instale o Agente Azure Linux executando o seguinte comando. A versão mais recente é 2.0.15.
    
        # sudo yum install WALinuxAgent
    
    Note que a instalação do pacote WALinuxAgent removerá os pacotes DeNetworkManager e NetworkManager-gnoção se não forem já removidos como descrito no passo 2.
12. Não crie espaço de troca no disco OS.
    
    O Agente Azure Linux pode configurar automaticamente o espaço de troca utilizando o disco de recursos locais que está ligado ao VM após o fornecimento no Azure. Note que o disco de recursos locais é um disco *temporário,* e pode ser esvaziado quando o VM é desprovisionado. Depois de instalar o Agente Azure Linux (ver passo anterior), modifique adequadamente os seguintes parâmetros em /etc/waagent.conf adequadamente:
    
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
13. Executar os seguintes comandos para desprovisionar a máquina virtual e prepará-la para o fornecimento em Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
14. Clique em **Ação -> Desligue** em Hiper-V Manager. O seu VHD Linux está agora pronto para ser enviado para o Azure.

---
## <a name="oracle-linux-70-and-later"></a>Oracle Linux 7.0 e mais tarde
**Mudanças no Oracle Linux 7**

Preparar uma máquina virtual Oracle Linux 7 para o Azure é muito semelhante ao Oracle Linux 6, no entanto existem várias diferenças importantes que merecem ser observadas:

* Azure suporta o Oracle Linux com o Unbreakable Enterprise Kernel (UEK) ou o Red Hat Compatible Kernel. Recomenda-se o Oracle Linux com UEK.
* O pacote NetworkManager já não entra em conflito com o agente Azure Linux. Esta embalagem é instalada por defeito e recomendamos que não seja removida.
* O GRUB2 é agora utilizado como bootloader predefinido, pelo que o procedimento de edição dos parâmetros do kernel mudou (ver abaixo).
* XFS é agora o sistema de ficheiros padrão. O sistema de ficheiros ext4 ainda pode ser utilizado se desejar.

**Etapas de configuração**

1. No Hyper-V Manager, selecione a máquina virtual.
2. Clique em **Ligar** para abrir uma janela de consola para a máquina virtual.
3. Crie um ficheiro chamado **rede** no diretório `/etc/sysconfig/` que contenha o seguinte texto:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain
4. Crie um ficheiro chamado **ifcfg-eth0** no `/etc/sysconfig/network-scripts/` diretório que contenha o seguinte texto:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
5. Modificar as regras udev para evitar gerar regras estáticas para a interface Ethernet. Estas regras podem causar problemas ao clonar uma máquina virtual no Microsoft Azure ou Hyper-V:
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
6. Certifique-se de que o serviço de rede começará no momento do arranque executando o seguinte comando:
   
        # sudo chkconfig network on
7. Instale a embalagem python-pyasn1 executando o seguinte comando:
   
        # sudo yum install python-pyasn1
8. Executar o seguinte comando para limpar os metadados yum atuais e instalar quaisquer atualizações:
   
        # sudo yum clean all
        # sudo yum -y update
9. Modifique a linha de arranque do núcleo na sua configuração de larva para incluir parâmetros adicionais de kernel para o Azure. Para fazer isto aberto "/etc/predefinido/grub" num editor de texto e editar o parâmetro `GRUB_CMDLINE_LINUX`, por exemplo:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Isto também garantirá que todas as mensagens de consola são enviadas para a primeira porta em série, que pode ajudar o suporte do Azure com problemas de depuração. Também desliga as convenções de nomeação para NICs no Oracle Linux 7 com o Unbreakable Enterprise Kernel. Para além do que precede, recomenda-se *a remoção* dos seguintes parâmetros:
   
       rhgb quiet crashkernel=auto
   
   A bota gráfica e tranquila não é útil num ambiente de nuvem onde queremos que todos os troncos sejam enviados para a porta de série.
   
   A opção `crashkernel` pode ser deixada configurada se desejar, mas note que este parâmetro reduzirá a quantidade de memória disponível no VM em 128MB ou mais, o que pode ser problemático nos tamanhos de VM menores.
10. Uma vez terminada a edição "/etc/predefinido/grub" por acima, execute o seguinte comando para reconstruir a configuração da larva:
    
        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
11. Certifique-se de que o servidor SSH está instalado e configurado para iniciar no momento do arranque.  Este é geralmente o padrão.
12. Instale o Agente Azure Linux executando o seguinte comando:
    
        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent
13. Não crie espaço de troca no disco OS.
    
    O Agente Azure Linux pode configurar automaticamente o espaço de troca utilizando o disco de recursos locais que está ligado ao VM após o fornecimento no Azure. Note que o disco de recursos locais é um disco *temporário,* e pode ser esvaziado quando o VM é desprovisionado. Depois de instalar o Agente Azure Linux (ver o passo anterior), modifique adequadamente os seguintes parâmetros em /etc/waagent.conf adequadamente:
    
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
14. Executar os seguintes comandos para desprovisionar a máquina virtual e prepará-la para o fornecimento em Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
15. Clique em **Ação -> Desligue** em Hiper-V Manager. O seu VHD Linux está agora pronto para ser enviado para o Azure.

## <a name="next-steps"></a>Passos seguintes
Está agora pronto para usar o seu Oracle Linux .vhd para criar novas máquinas virtuais em Azure. Se esta for a primeira vez que está a enviar o ficheiro .vhd para o Azure, consulte [Create a Linux VM a partir de um disco personalizado](upload-vhd.md#option-1-upload-a-vhd).

