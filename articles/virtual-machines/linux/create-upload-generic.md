---
title: Criar e carregar um VHD do Linux no Azure
description: Saiba como criar e carregar um Azure disco rígido virtual (VHD) que contém um sistema operativo Linux.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: d351396c-95a0-4092-b7bf-c6aae0bbd112
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: szark
ms.openlocfilehash: e032f9a9772232d3a57a9672dc6c601354ecad43
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58105527"
---
# <a name="information-for-non-endorsed-distributions"></a>Informações sobre distribuições não aprovadas
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

A plataforma do Azure SLA se aplica a máquinas virtuais que executem o sistema operacional Linux, apenas quando um do [distribuições aprovadas pelo](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) é utilizado. Para estes distribuições apoiadas, imagens pré-configuradas de Linux são fornecidas no Azure Marketplace.

* [Distribuições aprovadas pelo Linux no Azure-](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Suporte para imagens do Linux no Microsoft Azure](https://support.microsoft.com/kb/2941892)

Todas as distribuições em execução no Azure têm um número de pré-requisitos. Este artigo não pode ser abrangente, pois cada distribuição é diferente. Mesmo que cumpre todos os critérios abaixo, terá de ajustar significativamente o seu sistema de Linux para o mesmo a ser executado corretamente.

Recomendamos que comece com um da [Linux em distribuições apoiadas do Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Os artigos seguintes mostram como preparar as várias distribuições do Linux apoiadas que são suportadas no Azure:

* **[Distribuições baseada em centOS](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES & openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Este artigo se concentra na documentação de orientação geral para a execução de sua distribuição do Linux no Azure.

## <a name="general-linux-installation-notes"></a>Observações de instalação de Linux geral
* O formato de disco rígido virtual (VHDX) do Hyper-V não é suportado no Azure, apenas *fixo VHD*.  Pode converter o disco para o formato VHD utilizando o Gestor de Hyper-V ou o [Convert-VHD](https://docs.microsoft.com/powershell/module/hyper-v/convert-vhd) cmdlet. Se estiver a utilizar o VirtualBox, selecione **fixos de tamanho** em vez da predefinição (atribuída dinamicamente) ao criar o disco.
* O Azure suporta apenas máquinas virtuais de geração 1. Pode converter uma máquina virtual de geração 1 de VHDX para o formato de ficheiro VHD e de expansão dinâmica num disco de tamanho fixo. Não é possível alterar a geração de uma máquina virtual. Para obter mais informações, consulte [deve criar máquinas virtuais de geração 1 ou 2 no Hyper-V?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)
* O tamanho máximo permitido para o VHD é 1,023 GB.
* Ao instalar o sistema Linux, recomendamos que utilize partições padrão, em vez de lógica Volume Manager (LVM) que é o padrão para muitas instalações. Utilizar partições standard evitará LVM nome entra em conflito com VMs Clonadas, especialmente se um disco de SO já está ligado a outra VM idêntico para resolução de problemas. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) pode ser utilizada em discos de dados.
* Suporte a kernel de sistemas de ficheiros do UDF de montagem é necessário. No primeiro arranque no Azure a configuração de aprovisionamento é passada para a VM do Linux usando mídia formatada para UDF, que está ligada para o convidado. O agente Linux do Azure tem de montar o sistema de ficheiros UDF para ler a respetiva configuração e aprovisionar a VM.
* Versões de kernel do Linux anteriormente que 2.6.37 não é suportada no Hyper-V com tamanhos de VM maiores. Isso emitir principalmente impactos distribuições mais antigas usando o montante kernel do Red Hat 2.6.32 e foi corrigido no Red Hat Enterprise Linux (RHEL) 6.6 (kernel-2.6.32-504). Sistemas em execução personalizados kernels anteriores a 2.6.37 ou baseado em RHEL kernels mais antigas do que 2.6.32-504 tem de definir o parâmetro de arranque `numa=off` na linha de comando de kernel no grub.conf. Para obter mais informações, consulte [Red Hat KB 436883](https://access.redhat.com/solutions/436883).
* Não configure uma partição de troca no disco do SO. O agente Linux pode ser configurado para criar um ficheiro de troca no disco de recursos temporário, conforme descrito nos passos seguintes.
* Todos os VHDs no Azure tem de ter um tamanho virtual alinhado para 1 MB. Ao converter a partir de um disco não processado para o VHD tem de garantir que o tamanho de disco bruto é um múltiplo de 1MB antes da conversão, conforme descrito nos passos seguintes.

### <a name="installing-kernel-modules-without-hyper-v"></a>Instalar módulos kernel sem Hyper-V
Azure é executado num hipervisor Hyper-V, para que o Linux requer determinados módulos kernel para executar no Azure. Se tiver uma VM que tenha sido criada fora do Hyper-V, os programas de instalação do Linux não podem incluir os controladores para o Hyper-V no ramdisk inicial (initrd ou initramfs), a menos que a VM Deteta que está em execução num ambiente de Hyper-V. Quando utilizar um sistema de Virtualização diferentes (por exemplo, o Virtualbox, KVM e assim por diante) para preparar sua imagem do Linux, poderá ter de recriar o initrd então que em, pelo menos, os hv_vmbus hv_storvsc kernel módulos e estão disponíveis no ramdisk inicial.  Este problema conhecido destina-se a sistemas com base na distribuição do Red Hat a montante e possivelmente outros.

O mecanismo para recriar a imagem de initrd ou initramfs pode variar consoante a distribuição. Consulte a documentação ou suporte de sua distribuição para o procedimento adequado.  Eis um exemplo para recriar o initrd utilizando o `mkinitrd` utilitário:

1. Criar cópias de segurança a imagem de initrd existente:

    ```
    cd /boot
    sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak
    ```

2. Recrie a initrd com os módulos de kernel hv_vmbus e hv_storvsc:

    ```
    sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`
    ```

### <a name="resizing-vhds"></a>Redimensionar os VHDs
Imagens VHD no Azure tem de ter um tamanho virtual alinhado para 1 MB.  Normalmente, os VHDs criados com o Hyper-V estão alinhados corretamente.  Se o VHD não está alinhado corretamente, poderá receber uma mensagem de erro semelhante ao seguinte ao tentar criar uma imagem a partir do VHD.

* O VHD http://<mystorageaccount>.blob.core.windows.net/vhds/MyLinuxVM.vhd tem um tamanho virtual não suportado de 21475270656 bytes. O tamanho tem de ser um número inteiro (em MB).

Neste caso, redimensione a VM utilizando a consola de Gestor de Hyper-V ou o [VHD de redimensionamento](https://technet.microsoft.com/library/hh848535.aspx) cmdlet do PowerShell.  Se não está a executar num ambiente Windows, recomendamos que utilize `qemu-img` para converter (se necessário) e redimensione o VHD.

> [!NOTE]
> Há uma [erro conhecido no qemu img](https://bugs.launchpad.net/qemu/+bug/1490611) versões > = 2.2.1 do buildship que resulta num VHD formatado incorretamente. Foi corrigido o problema em QEMU 2.6. Recomendamos a utilização `qemu-img` 2.2.0 ou inferior, ou 2.6 ou superior.
> 

1. O VHD diretamente com ferramentas, como o redimensionamento `qemu-img` ou `vbox-manage` pode resultar num VHD não inicializável.  Recomendamos que primeiro converter o VHD para uma imagem de disco bruto.  Se a imagem VM foi criada como uma imagem de disco bruto (o padrão para alguns hipervisores como KVM), em seguida, pode ignorar este passo.
 
    ```
    qemu-img convert -f vpc -O raw MyLinuxVM.vhd MyLinuxVM.raw
    ```

1. Calcule o tamanho necessário da imagem do disco para que o tamanho virtual está alinhado para 1 MB.  As seguintes utilizações de script de shell de bash `qemu-img info` para determinar o tamanho virtual da imagem do disco e, em seguida, calcula o tamanho para a próxima 1 MB.

    ```bash
    rawdisk="MyLinuxVM.raw"
    vhddisk="MyLinuxVM.vhd"

    MB=$((1024*1024))
    size=$(qemu-img info -f raw --output json "$rawdisk" | \
    gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    rounded_size=$((($size/$MB + 1)*$MB))
    
    echo "Rounded Size = $rounded_size"
    ```

3. Redimensionar o disco não processado com `$rounded_size` conforme definido acima.

    ```bash
    qemu-img resize MyLinuxVM.raw $rounded_size
    ```

4. Agora, converta o disco não PROCESSADO para um VHD de tamanho fixo.

    ```bash
    qemu-img convert -f raw -o subformat=fixed -O vpc MyLinuxVM.raw MyLinuxVM.vhd
    ```

   Ou, com a versão de qemu 2.6 +, inclua o `force_size` opção.

    ```bash
    qemu-img convert -f raw -o subformat=fixed,force_size -O vpc MyLinuxVM.raw MyLinuxVM.vhd
    ```

## <a name="linux-kernel-requirements"></a>Requisitos de Kernel do Linux

Os controladores de serviços de integração do Linux (LIS) para o Hyper-V e do Azure são enviados diretamente para o kernel do Linux a montante. Número de distribuições que incluem uma versão de kernel de Linux recente (por exemplo, 3.x) já a tiver estes controladores disponíveis ou caso contrário, fornece versões backported esses Drivers com os kernels.  Estes controladores estão constantemente a ser atualizados no kernel do montante com novas correções e funcionalidades, para que, sempre que possível, recomendamos a execução um [apoiadas pelo distribuição](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) que inclui estas correções e atualizações.

Se estiver a executar uma variante do Red Hat Enterprise Linux versões 6.0 para 6.3, em seguida, terá de instalar o [controladores mais recentes do LIS para Hyper-V](https://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409). A partir do RHEL 6.4 + (e derivados) os controladores LIS já estão incluídos com o kernel e por isso, não existem pacotes de instalação adicionais são necessários.

Se um kernel personalizado for necessário, recomendamos uma versão recente do kernel (como 3.8 +). Para as distribuições ou fornecedores que mantêm os seus próprios kernel, terá backport regularmente os controladores LIS do kernel a montante para o kernel personalizado.  Mesmo se já estiver a executar uma versão de kernel relativamente recente, é altamente recomendável manter o controle de qualquer a montante correções nos controladores LIS e backport-las conforme necessário. As localizações dos ficheiros de origem do driver LIS estão especificadas na [MAINTAINERS](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS) ficheiros na árvore de origem do kernel do Linux:
```
    F:    arch/x86/include/asm/mshyperv.h
    F:    arch/x86/include/uapi/asm/hyperv.h
    F:    arch/x86/kernel/cpu/mshyperv.c
    F:    drivers/hid/hid-hyperv.c
    F:    drivers/hv/
    F:    drivers/input/serio/hyperv-keyboard.c
    F:    drivers/net/hyperv/
    F:    drivers/scsi/storvsc_drv.c
    F:    drivers/video/fbdev/hyperv_fb.c
    F:    include/linux/hyperv.h
    F:    tools/hv/
```
Os patches seguintes têm de ser incluídos no kernel. Esta lista não pode ser concluída para todas as distribuições.

* [ata_piix: diferir discos para os controladores de Hyper-V por predefinição](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
* [storvsc: Conta para os pacotes em trânsito no caminho de reposição](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)
* [storvsc: evitar a utilização de WRITE_SAME](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=3e8f4f4065901c8dfc51407e1984495e1748c090)
* [storvsc: Desativar o mesmo escrever para RAID e drivers de adaptador de anfitrião virtual](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=54b2b50c20a61b51199bedb6e5d2f8ec2568fb43)
* [storvsc: Ponteiro nulo tirarmos a referência de correção](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=b12bb60d6c350b348a4e1460cd68f97ccae9822e)
* [storvsc: falhas de memória intermédia de anel podem resultar em congelamento de e/s](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=e86fb5e8ab95f10ec5f2e9430119d5d35020c951)
* [scsi_sysfs: proteger contra execução dupla de __scsi_remove_device](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/scsi_sysfs.c?id=be821fd8e62765de43cc4f0e2db363d0e30a7e9b)

## <a name="the-azure-linux-agent"></a>O agente Linux do Azure
O [agente Linux do Azure](../extensions/agent-linux.md) `waagent` Aprovisiona uma máquina virtual do Linux no Azure. Pode obter a versão mais recente, os problemas de ficheiros ou submeter pedidos pull no [repositório do GitHub de agente do Linux](https://github.com/Azure/WALinuxAgent).

* O agente do Linux é lançado sob a licença Apache 2.0. Número de distribuições já forneça deb ou RPM pacotes para o agente, e esses pacotes podem facilmente ser instalados e atualizados.
* O agente Linux do Azure requer Python v2.6 +.
* O agente também requer o módulo de python pyasn1. A maioria das distribuições fornecem este módulo como um pacote separado para ser instalado.
* Em alguns casos, o agente Linux do Azure podem não ser compatível com NetworkManager. Muitos dos pacotes RPM/Deb fornecidos pelo distribuições configurar NetworkManager como um conflito ao pacote de waagent. Nestes casos, irá desinstalar NetworkManager quando instala o pacote de agente do Linux.
* O agente Linux do Azure tem de ser igual ou superior a [versão mínima suportada](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

## <a name="general-linux-system-requirements"></a>Requisitos de sistema de Linux geral

1. Modificar a linha de arranque de kernel em GRUB ou GRUB2 para incluir os seguintes parâmetros, para que todas as mensagens de consola são enviadas para a primeira porta serial. Essas mensagens podem ajudá-lo Azure suporte com quaisquer problemas de depuração.
    ```  
    console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300
    ```
    Também recomendamos *remover* os seguintes parâmetros se existirem.
    ```  
    rhgb quiet crashkernel=auto
    ```
    Arranque gráfica e quieto não é útil num ambiente de cloud, onde queremos todos os registos enviados para a porta serial. O `crashkernel` opção pode ser esquerda configurados, se necessário, mas tenha em atenção que este parâmetro reduz a quantidade de memória disponível na VM em, pelo menos, 128 MB, o que pode ser problemático para os tamanhos de VM mais pequenos.

1. Instale o agente Linux do Azure.
  
    O agente Linux do Azure é necessário para o aprovisionamento de uma imagem do Linux no Azure.  Número de distribuições fornece o agente como um pacote Deb ou RPM (o pacote é normalmente chamado WALinuxAgent ou walinuxagent).  O agente também pode ser instalado manualmente, seguindo os passos a [guia de agente do Linux](../extensions/agent-linux.md).

1. Certifique-se de que o servidor SSH está instalado e configurado para iniciar no momento da inicialização.  Esta configuração é, normalmente, a predefinição.

1. Não crie o espaço de comutação no disco do SO.
  
    O agente Linux do Azure podem configurar automaticamente o espaço de comutação com o disco de recurso local que está ligado à VM após o aprovisionamento no Azure. O disco de recurso local é uma *temporária* disco e poderá ser esvaziada quando a VM é desaprovisionada. Depois de instalar o agente do Linux do Azure (passo 2 acima), modificar os seguintes parâmetros em /etc/waagent.Conf. conforme necessário.
    ```  
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: Set this to your desired size.
    ```
1. Execute os seguintes comandos para desaprovisionar a máquina virtual.
  
     ```
     sudo waagent -force -deprovision
     export HISTSIZE=0
     logout
     ```  
   > [!NOTE]
   > Sobre o Virtualbox poderá ver o seguinte erro depois de ser executada `waagent -force -deprovision` que diz `[Errno 5] Input/output error`. Esta mensagem de erro não é crítica e pode ser ignorada.

* Encerre a máquina virtual e carregar o VHD para o Azure.

