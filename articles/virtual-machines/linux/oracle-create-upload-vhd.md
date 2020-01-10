---
title: Criar e carregar um VHD de Oracle Linux
description: Saiba como criar e carregar um VHD (disco rígido virtual) do Azure que contém um sistema operacional Oracle Linux.
services: virtual-machines-linux
documentationcenter: ''
author: MicahMcKittrick-MSFT
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
ms.openlocfilehash: e0250737f1f2934548a16ee42e9ff582f2403c48
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75747739"
---
# <a name="prepare-an-oracle-linux-virtual-machine-for-azure"></a>Preparar uma máquina virtual do Oracle Linux para o Azure

Este artigo pressupõe que você já tenha instalado um sistema operacional Oracle Linux em um disco rígido virtual. Existem várias ferramentas para criar arquivos. VHD, por exemplo, uma solução de virtualização, como o Hyper-V. Para obter instruções, consulte [instalar a função Hyper-V e configurar uma máquina virtual](https://technet.microsoft.com/library/hh846766.aspx).

## <a name="oracle-linux-installation-notes"></a>Notas de instalação do Oracle Linux
* Consulte também as [notas de instalação gerais do Linux](create-upload-generic.md#general-linux-installation-notes) para obter mais dicas sobre como preparar o Linux para o Azure.
* O Hyper-V e o suporte do Azure Oracle Linux com o UEK (inbreakable Enterprise kernel) ou o kernel compatível com Red Hat.
* O UEK2 da Oracle não tem suporte no Hyper-V e no Azure, pois não inclui os drivers necessários.
* Não há suporte para o formato VHDX no Azure, somente **VHD fixo**.  Você pode converter o disco para o formato VHD usando o Gerenciador do Hyper-V ou o cmdlet Convert-VHD.
* Ao instalar o sistema Linux, é recomendável que você use partições padrão em vez de LVM (geralmente o padrão para muitas instalações). Isso evitará conflitos de nome LVM com VMs clonadas, especialmente se um disco do sistema operacional precisar ser anexado a outra VM para solução de problemas. O [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou o [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) pode ser usado em discos de dados, se for preferencial.
* As versões do kernel do Linux anteriores ao 2.6.37 não dão suporte a NUMA no Hyper-V com tamanhos de VM maiores. Esse problema afeta principalmente as distribuições mais antigas usando o kernel upstream Red Hat 2.6.32 e foi corrigido no Oracle Linux 6,6 e posterior
* Não configure uma partição de permuta no disco do sistema operacional. O agente do Linux pode ser configurado para criar um arquivo de permuta no disco de recursos temporário.  Mais informações sobre isso podem ser encontradas nas etapas abaixo.
* Todos os VHDs no Azure devem ter um tamanho virtual alinhado a 1 MB. Ao converter de um disco bruto para VHD, você deve garantir que o tamanho do disco bruto seja um múltiplo de 1MB antes da conversão. Consulte [notas de instalação do Linux](create-upload-generic.md#general-linux-installation-notes) para obter mais informações.
* Verifique se o repositório de `Addons` está habilitado. Edite o arquivo `/etc/yum.repos.d/public-yum-ol6.repo`(Oracle Linux 6) ou `/etc/yum.repos.d/public-yum-ol7.repo`(Oracle Linux 7) e altere a `enabled=0` de linha para `enabled=1` em **[ol6_addons]** ou **[ol7_addons]** neste arquivo.

## <a name="oracle-linux-64-and-later"></a>Oracle Linux 6,4 e posterior
Você deve concluir as etapas de configuração específicas no sistema operacional para que a máquina virtual seja executada no Azure.

1. No painel central do Gerenciador do Hyper-V, selecione a máquina virtual.
2. Clique em **conectar** para abrir a janela da máquina virtual.
3. Desinstale o NetworkManager executando o seguinte comando:
   
        # sudo rpm -e --nodeps NetworkManager
   
    **Observação:** Se o pacote ainda não estiver instalado, esse comando falhará com uma mensagem de erro. Isto era esperado.
4. Crie um arquivo chamado **Network** no diretório `/etc/sysconfig/` que contém o seguinte texto:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain
5. Crie um arquivo chamado **ifcfg-eth0** no diretório `/etc/sysconfig/network-scripts/` que contém o seguinte texto:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
6. Modifique as regras de udev para evitar a geração de regras estáticas para as interfaces de Ethernet. Essas regras podem causar problemas ao clonar uma máquina virtual no Microsoft Azure ou no Hyper-V:
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
7. Verifique se o serviço de rede será iniciado no momento da inicialização executando o seguinte comando:
   
        # chkconfig network on
8. Instale o Python-pyasn1 executando o seguinte comando:
   
        # sudo yum install python-pyasn1
9. Modifique a linha de inicialização do kernel em sua configuração do grub para incluir parâmetros de kernel adicionais para o Azure. Para fazer isso, abra "/boot/grub/menu.lst" em um editor de texto e verifique se o kernel inclui os seguintes parâmetros:
   
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
   Isso garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, o que pode auxiliar o suporte do Azure com problemas de depuração.
   
   Além do que está acima, é recomendável *remover* os seguintes parâmetros:
   
        rhgb quiet crashkernel=auto
   
   A inicialização gráfica e silenciosa não são úteis em um ambiente de nuvem onde queremos que todos os logs sejam enviados para a porta serial.
   
   A opção `crashkernel` pode ser deixada configurada se desejado, mas observe que esse parâmetro reduzirá a quantidade de memória disponível na VM por 128MB ou mais, o que pode ser um problema nos tamanhos de VM menores.
10. Verifique se o servidor SSH está instalado e configurado para iniciar no momento da inicialização.  Normalmente, isso é o padrão.
11. Instale o agente Linux do Azure executando o comando a seguir. A versão mais recente é 2.0.15.
    
        # sudo yum install WALinuxAgent
    
    Observe que a instalação do pacote WALinuxAgent removerá os pacotes NetworkManager e NetworkManager-gnome se eles ainda não tiverem sido removidos, conforme descrito na etapa 2.
12. Não crie espaço de permuta no disco do sistema operacional.
    
    O agente Linux do Azure pode configurar automaticamente o espaço de permuta usando o disco de recurso local que é anexado à VM após o provisionamento no Azure. Observe que o disco de recurso local é um disco *temporário* e pode ser esvaziado quando a VM é desprovisionada. Depois de instalar o agente Linux do Azure (consulte a etapa anterior), modifique os seguintes parâmetros no/etc/waagent.conf adequadamente:
    
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
13. Execute os seguintes comandos para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
14. Clique em **ação-> desligar** no Gerenciador do Hyper-V. Seu VHD do Linux agora está pronto para ser carregado no Azure.

---
## <a name="oracle-linux-70-and-later"></a>Oracle Linux 7,0 e posterior
**Alterações no Oracle Linux 7**

Preparar uma máquina virtual Oracle Linux 7 para o Azure é muito semelhante ao Oracle Linux 6, no entanto, há várias diferenças importantes que vale a pena observar:

* O Azure dá suporte a Oracle Linux com o kernel do UEK (inquebrable Enterprise) ou kernel compatível com Red Hat. É recomendável Oracle Linux com UEK.
* O pacote NetworkManager não está mais em conflito com o agente Linux do Azure. Esse pacote é instalado por padrão e recomendamos que ele não seja removido.
* GRUB2 agora é usado como o carregador de estado padrão, portanto, o procedimento para editar parâmetros de kernel foi alterado (veja abaixo).
* XFS agora é o sistema de arquivos padrão. O sistema de arquivos EXT4 ainda pode ser usado se desejado.

**Etapas de configuração**

1. No Gerenciador do Hyper-V, selecione a máquina virtual.
2. Clique em **conectar** para abrir uma janela de console para a máquina virtual.
3. Crie um arquivo chamado **Network** no diretório `/etc/sysconfig/` que contém o seguinte texto:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain
4. Crie um arquivo chamado **ifcfg-eth0** no diretório `/etc/sysconfig/network-scripts/` que contém o seguinte texto:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
5. Modifique as regras de udev para evitar a geração de regras estáticas para as interfaces de Ethernet. Essas regras podem causar problemas ao clonar uma máquina virtual no Microsoft Azure ou no Hyper-V:
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
6. Verifique se o serviço de rede será iniciado no momento da inicialização executando o seguinte comando:
   
        # sudo chkconfig network on
7. Instale o pacote Python-pyasn1 executando o seguinte comando:
   
        # sudo yum install python-pyasn1
8. Execute o comando a seguir para limpar os metadados atuais do yum e instalar as atualizações:
   
        # sudo yum clean all
        # sudo yum -y update
9. Modifique a linha de inicialização do kernel em sua configuração do grub para incluir parâmetros de kernel adicionais para o Azure. Para fazer isso, abra "/etc/default/grub" em um editor de texto e edite o parâmetro `GRUB_CMDLINE_LINUX`, por exemplo:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Isso também garantirá que todas as mensagens do console sejam enviadas para a primeira porta serial, o que pode auxiliar o suporte do Azure com problemas de depuração. Ele também desativa as convenções de nomenclatura para NICs no Oracle Linux 7 com o kernel corporativo inquebrável. Além do que está acima, é recomendável *remover* os seguintes parâmetros:
   
       rhgb quiet crashkernel=auto
   
   A inicialização gráfica e silenciosa não são úteis em um ambiente de nuvem onde queremos que todos os logs sejam enviados para a porta serial.
   
   A opção `crashkernel` pode ser deixada configurada se desejado, mas observe que esse parâmetro reduzirá a quantidade de memória disponível na VM por 128MB ou mais, o que pode ser um problema nos tamanhos de VM menores.
10. Quando terminar de Editar "/etc/default/grub" por acima, execute o seguinte comando para recompilar a configuração do grub:
    
        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
11. Verifique se o servidor SSH está instalado e configurado para iniciar no momento da inicialização.  Normalmente, isso é o padrão.
12. Instale o agente Linux do Azure executando o seguinte comando:
    
        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent
13. Não crie espaço de permuta no disco do sistema operacional.
    
    O agente Linux do Azure pode configurar automaticamente o espaço de permuta usando o disco de recurso local que é anexado à VM após o provisionamento no Azure. Observe que o disco de recurso local é um disco *temporário* e pode ser esvaziado quando a VM é desprovisionada. Depois de instalar o agente Linux do Azure (consulte a etapa anterior), modifique os seguintes parâmetros no/etc/waagent.conf adequadamente:
    
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
14. Execute os seguintes comandos para desprovisionar a máquina virtual e prepará-la para provisionamento no Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
15. Clique em **ação-> desligar** no Gerenciador do Hyper-V. Seu VHD do Linux agora está pronto para ser carregado no Azure.

## <a name="next-steps"></a>Passos seguintes
Agora você está pronto para usar seu Oracle Linux. VHD para criar novas máquinas virtuais no Azure. Se esta for a primeira vez que você está carregando o arquivo. vhd no Azure, consulte [criar uma VM do Linux a partir de um disco personalizado](upload-vhd.md#option-1-upload-a-vhd).

