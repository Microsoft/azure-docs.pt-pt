---
title: Criar e carregar um Oracle Linux VHD
description: Aprenda a criar e carregar um disco rígido virtual Azure (VHD) que contenha um sistema operativo Oracle Linux.
author: gbowerman
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 12/10/2019
ms.author: guybo
ms.openlocfilehash: 4e9f87ec4a0df28b14bca8f5a44dfe9388e8ff03
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500534"
---
# <a name="prepare-an-oracle-linux-virtual-machine-for-azure"></a>Prepare uma máquina virtual Oracle Linux para o Azure

Este artigo pressupõe que já instalou um sistema operativo Oracle Linux num disco rígido virtual. Existem várias ferramentas para criar ficheiros .vhd, por exemplo uma solução de virtualização como o Hyper-V. Para obter instruções, consulte [instalar a função Hiper-V e configurar uma máquina virtual.](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11))

## <a name="oracle-linux-installation-notes"></a>Notas de instalação do Oracle Linux
* Consulte também [as notas de instalação do General Linux](create-upload-generic.md#general-linux-installation-notes) para obter mais dicas sobre a preparação do Linux para o Azure.
* O Hyper-V e o Azure suportam o Oracle Linux com o Kernel da Empresa Inquebrável (UEK) ou com o Kernel Compatível com o Chapéu Vermelho.
* O UEK2 da Oracle não é suportado no Hyper-V e no Azure, uma vez que não inclui os controladores necessários.
* O formato VHDX não é suportado em Azure, apenas **VHD fixo**.  Pode converter o disco em formato VHD utilizando o Hyper-V Manager ou o cmdlet converte-vhd.
* Ao instalar o sistema Linux recomenda-se que utilize divisórias padrão em vez de LVM (muitas vezes o padrão para muitas instalações). Isto evitará conflitos de nome LVM com VMs clonados, especialmente se um disco de SO precisar de ser ligado a outro VM para resolução de problemas. [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) podem ser usados em discos de dados, se preferir.
* As versões de kernel Linux antes de 2.6.37 não suportam UMA em Hiper-V com tamanhos VM maiores. Esta questão afeta principalmente as distribuições mais antigas utilizando o núcleo a montante do chapéu vermelho 2.6.32, e foi fixada no Oracle Linux 6.6 e mais tarde
* Não configuure uma partição de troca no disco OS. O agente Linux pode ser configurado para criar um ficheiro de troca no disco de recursos temporários.  Mais informações sobre isso podem ser encontradas nos degraus abaixo.
* Todos os VHDs em Azure devem ter um tamanho virtual alinhado a 1MB. Ao converter de um disco cru para VHD deve certificar-se de que o tamanho do disco bruto é um múltiplo de 1MB antes da conversão. Consulte [as notas de instalação do Linux](create-upload-generic.md#general-linux-installation-notes) para obter mais informações.
* Certifique-se de que o `Addons` repositório está ativado. Editar o ficheiro `/etc/yum.repos.d/public-yum-ol6.repo` (Oracle Linux 6) ou `/etc/yum.repos.d/public-yum-ol7.repo` (Oracle Linux 7) e alterar a linha `enabled=0` para `enabled=1` **abaixo de [ol6_addons]** ou **[ol7_addons]** neste ficheiro.

## <a name="oracle-linux-64-and-later"></a>Oracle Linux 6.4 e mais tarde
Tem de completar etapas de configuração específicas no sistema operativo para que a máquina virtual funcione em Azure.

1. No painel central do Hyper-V Manager, selecione a máquina virtual.
2. Clique **em Ligar** para abrir a janela para a máquina virtual.
3. Desinstalar o NetworkManager executando o seguinte comando:

    ```console
    # sudo rpm -e --nodeps NetworkManager
    ```

    **Nota:** Se a embalagem ainda não estiver instalada, este comando falhará com uma mensagem de erro. Isto era esperado.
4. Criar uma **rede** de ficheiros nomeada no `/etc/sysconfig/` diretório que contenha o seguinte texto:

    ```config   
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

5. Criar um ficheiro denominado **ifcfg-eth0** no `/etc/sysconfig/network-scripts/` diretório que contenha o seguinte texto:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

6. Modifique as regras udev para evitar gerar regras estáticas para a interface Ethernet. Estas regras podem causar problemas ao clonar uma máquina virtual no Microsoft Azure ou Hiper-V:

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

7. Certifique-se de que o serviço de rede começará no tempo de arranque, executando o seguinte comando:

    ```console
    # chkconfig network on
    ```

8. Instale python-pyasn1 executando o seguinte comando:

    ```console
    # sudo yum install python-pyasn1
    ```

9. Modifique a linha de arranque do núcleo na sua configuração de larva para incluir parâmetros adicionais de kernel para Azure. Para fazer isto abra "/boot/grub/menu.lst" num editor de texto e certifique-se de que o núcleo inclui os seguintes parâmetros:

    ```config-grub
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

   Isto irá garantir que todas as mensagens de consola são enviadas para a primeira porta em série, que pode ajudar o suporte do Azure com problemas de depuração.
   
   Além do acima referido, recomenda-se *a remoção* dos seguintes parâmetros:

    ```config-grub
    rhgb quiet crashkernel=auto
    ```

   A bota gráfica e silenciosa não é útil num ambiente em nuvem onde queremos que todos os registos sejam enviados para a porta em série.
   
   A `crashkernel` opção pode ser deixada configurada se desejar, mas note que este parâmetro reduzirá a quantidade de memória disponível no VM em 128MB ou mais, o que pode ser problemático nos tamanhos de VM menores.
10. Certifique-se de que o servidor SSH está instalado e configurado para começar na hora de arranque.  Este é geralmente o padrão.
11. Instale o Agente Azure Linux executando o seguinte comando. A versão mais recente é 2.0.15.

    ```console
    # sudo yum install WALinuxAgent
    ```

    Note que a instalação do pacote WALinuxAgent removerá os pacotes NetworkManager e NetworkManager-gnome se não forem já removidos como descrito no passo 2.
12. Não crie espaço de troca no disco SO.
    
    O Agente Azure Linux pode configurar automaticamente o espaço de troca utilizando o disco de recursos local que é ligado ao VM após o provisionamento no Azure. Note que o disco de recursos local é um disco *temporário* e pode ser esvaziado quando o VM é desprovisionado. Depois de instalar o Agente Azure Linux (ver passo anterior), modificar os seguintes parâmetros em /etc/waagent.conf adequadamente:

    ```config-conf
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

13. Executar os seguintes comandos para desprovisionar a máquina virtual e prepará-la para provisão em Azure:

    ```console
    # sudo waagent -force -deprovision
    # export HISTSIZE=0
    # logout
    ```

14. Clique em **Ação -> Desligar** em Hyper-V Manager. O seu VHD Linux está agora pronto para ser enviado para Azure.

---
## <a name="oracle-linux-70-and-later"></a>Oracle Linux 7.0 e mais tarde
**Alterações no Oracle Linux 7**

Preparar uma máquina virtual Oracle Linux 7 para Azure é muito semelhante ao Oracle Linux 6, no entanto existem várias diferenças importantes que merecem ser notantes:

* A azure suporta o Oracle Linux com o Kernel da Empresa Inquebrável (UEK) ou o Kernel Compatível com o Chapéu Vermelho. Recomenda-se o Oracle Linux com UEK.
* O pacote NetworkManager já não entra em conflito com o agente Azure Linux. Esta embalagem é instalada por defeito e recomendamos que não seja removida.
* GRUB2 é agora usado como o bootloader padrão, por isso o procedimento de edição dos parâmetros do núcleo mudou (ver abaixo).
* XFS é agora o sistema de ficheiros predefinido. O sistema de ficheiros ext4 ainda pode ser utilizado se desejar.

**Passos de configuração**

1. No Hyper-V Manager, selecione a máquina virtual.
2. Clique **em Connect** para abrir uma janela de consola para a máquina virtual.
3. Criar uma **rede** de ficheiros nomeada no `/etc/sysconfig/` diretório que contenha o seguinte texto:

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

4. Criar um ficheiro denominado **ifcfg-eth0** no `/etc/sysconfig/network-scripts/` diretório que contenha o seguinte texto:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

5. Modifique as regras udev para evitar gerar regras estáticas para a interface Ethernet. Estas regras podem causar problemas ao clonar uma máquina virtual no Microsoft Azure ou Hiper-V:

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    ```

6. Certifique-se de que o serviço de rede começará no tempo de arranque, executando o seguinte comando:

    ```console
    # sudo chkconfig network on
    ```

7. Instale o pacote python-pyasn1 executando o seguinte comando:

    ```console
    # sudo yum install python-pyasn1
    ```

8. Executar o seguinte comando para limpar os metadados yum atuais e instalar quaisquer atualizações:

    ```console 
    # sudo yum clean all
    # sudo yum -y update
    ```

9. Modifique a linha de arranque do núcleo na sua configuração de larva para incluir parâmetros adicionais de kernel para Azure. Para fazer isto abra "/etc/predefinição/comida" num editor de texto e edite o `GRUB_CMDLINE_LINUX` parâmetro, por exemplo:

    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Isto também irá garantir que todas as mensagens de consola são enviadas para a primeira porta em série, que pode ajudar o suporte do Azure com problemas de depuração. Também desliga as convenções de nomeação de NICs em Oracle Linux 7 com o Kernel da Empresa Inquebrável. Além do acima referido, recomenda-se *a remoção* dos seguintes parâmetros:

    ```config-grub
       rhgb quiet crashkernel=auto
    ```
 
   A bota gráfica e silenciosa não é útil num ambiente em nuvem onde queremos que todos os registos sejam enviados para a porta em série.
   
   A `crashkernel` opção pode ser deixada configurada se desejar, mas note que este parâmetro reduzirá a quantidade de memória disponível no VM em 128MB ou mais, o que pode ser problemático nos tamanhos de VM menores.
10. Uma vez terminada a edição "/etc/predefinição/comida" por cima, executar o seguinte comando para reconstruir a configuração da larva:

    ```console
    # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

11. Certifique-se de que o servidor SSH está instalado e configurado para começar na hora de arranque.  Este é geralmente o padrão.
12. Instale o Agente Azure Linux executando o seguinte comando:

    ```console
    # sudo yum install WALinuxAgent
    # sudo systemctl enable waagent
    ```

13. Não crie espaço de troca no disco SO.
    
    O Agente Azure Linux pode configurar automaticamente o espaço de troca utilizando o disco de recursos local que é ligado ao VM após o provisionamento no Azure. Note que o disco de recursos local é um disco *temporário* e pode ser esvaziado quando o VM é desprovisionado. Após a instalação do Agente Azure Linux (ver passo anterior), modificar os seguintes parâmetros em /etc/waagent.conf adequadamente:

    ```config-conf
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

14. Executar os seguintes comandos para desprovisionar a máquina virtual e prepará-la para provisão em Azure:
    
    ```console
    # sudo waagent -force -deprovision
    # export HISTSIZE=0
    # logout
    ```

15. Clique em **Ação -> Desligar** em Hyper-V Manager. O seu VHD Linux está agora pronto para ser enviado para Azure.

## <a name="next-steps"></a>Passos seguintes
Está agora pronto para usar o seu Oracle Linux .vhd para criar novas máquinas virtuais em Azure. Se esta for a primeira vez que está a enviar o ficheiro .vhd para a Azure, consulte [Create a Linux VM a partir de um disco personalizado](upload-vhd.md#option-1-upload-a-vhd).