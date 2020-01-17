---
title: Criar e carregar um VHD do Linux no Azure
description: Saiba como criar e carregar um VHD (disco rígido virtual) do Azure que contém um sistema operacional Linux.
services: virtual-machines-linux
documentationcenter: ''
author: MicahMcKittrick-MSFT
manager: gwallace
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: d351396c-95a0-4092-b7bf-c6aae0bbd112
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 10/08/2018
ms.author: mimckitt
ms.openlocfilehash: ffa99c6ba0157eca133dc36ecbbb159b076b8bc0
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76155558"
---
# <a name="information-for-non-endorsed-distributions"></a>Informações para distribuições não endossadas

O SLA da plataforma Azure aplica-se às máquinas virtuais que executam o sistema operacional Linux somente quando uma das [distribuições endossadas](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) é usada. Para essas distribuições endossadas, as imagens pré-configuradas do Linux são fornecidas no Azure Marketplace.

* [Linux no Azure – distribuições endossadas](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Suporte para imagens do Linux no Microsoft Azure](https://support.microsoft.com/kb/2941892)

Todas as distribuições em execução no Azure têm vários pré-requisitos. Este artigo não pode ser abrangente, pois cada distribuição é diferente. Mesmo que você atenda a todos os critérios abaixo, talvez seja necessário ajustar significativamente seu sistema Linux para que ele seja executado corretamente.

Recomendamos que você comece com uma das [distribuições endossadas do Linux no Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Os artigos a seguir mostram como preparar as várias distribuições do Linux endossadas com suporte no Azure:

* **[Distribuições baseadas em CentOS](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES & openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Este artigo se concentra em diretrizes gerais para executar sua distribuição do Linux no Azure.

## <a name="general-linux-installation-notes"></a>Notas de instalação gerais do Linux
* O formato VHDX (disco rígido virtual) do Hyper-V não tem suporte no Azure, somente *VHD fixo*.  Você pode converter o disco para o formato VHD usando o Gerenciador do Hyper-V ou o cmdlet [Convert-VHD](https://docs.microsoft.com/powershell/module/hyper-v/convert-vhd) . Se você estiver usando VirtualBox, selecione **tamanho fixo** em vez de o padrão (alocado dinamicamente) ao criar o disco.
* O Azure dá suporte a máquinas virtuais Gen1 (inicialização do BIOS) & Gen2 (inicialização UEFI).
* O tamanho máximo permitido para o VHD é 1.023 GB.
* Ao instalar o sistema Linux, recomendamos que você use partições padrão, em vez do Gerenciador de volumes lógicos (LVM), que é o padrão para muitas instalações. O uso de partições padrão evitará conflitos de nome LVM com VMs clonadas, especialmente se um disco do sistema operacional já estiver anexado a outra VM idêntica para solução de problemas. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) podem ser usados em discos de dados.
* É necessário suporte de kernel para montar sistemas de arquivos UDF. Na primeira inicialização no Azure, a configuração de provisionamento é passada para a VM do Linux usando a mídia formatada por UDF que está anexada ao convidado. O agente Linux do Azure deve montar o sistema de arquivos UDF para ler sua configuração e provisionar a VM.
* As versões do kernel do Linux anteriores ao 2.6.37 não dão suporte a NUMA no Hyper-V com tamanhos de VM maiores. Esse problema afeta principalmente as distribuições mais antigas usando o kernel upstream Red Hat 2.6.32 e foi corrigido em Red Hat Enterprise Linux (RHEL) 6,6 (kernel-2.6.32-504). Sistemas que executam kernels personalizados com mais de 2.6.37 ou kernels baseados em RHEL mais antigos que 2.6.32-504 devem definir o parâmetro de inicialização `numa=off` na linha de comando do kernel em grub. conf. Para obter mais informações, consulte [Red Hat KB 436883](https://access.redhat.com/solutions/436883).
* Não configure uma partição de permuta no disco do sistema operacional. O agente do Linux pode ser configurado para criar um arquivo de permuta no disco de recursos temporário, conforme descrito nas etapas a seguir.
* Todos os VHDs no Azure devem ter um tamanho virtual alinhado a 1 MB. Ao converter de um disco bruto para VHD, você deve garantir que o tamanho do disco bruto seja um múltiplo de 1 MB antes da conversão, conforme descrito nas etapas a seguir.

### <a name="installing-kernel-modules-without-hyper-v"></a>Instalando módulos de kernel sem Hyper-V
O Azure é executado no hipervisor do Hyper-V, portanto, o Linux requer que determinados módulos de kernel sejam executados no Azure. Se você tiver uma VM que foi criada fora do Hyper-V, os instaladores do Linux podem não incluir os drivers do Hyper-V no ramdisk inicial (initrd ou initramfs), a menos que a VM detecte que está sendo executada em um ambiente Hyper-V. Ao usar um sistema de virtualização diferente (como VirtualBox, KVM e assim por diante) para preparar a imagem do Linux, talvez seja necessário recompilar o initrd para que pelo menos os módulos de kernel hv_vmbus e hv_storvsc estejam disponíveis no ramdisk inicial.  Esse problema conhecido é para sistemas baseados na distribuição Red Hat upstream e, possivelmente, outros.

O mecanismo para a recriação da imagem initrd ou initramfs pode variar dependendo da distribuição. Consulte a documentação de sua distribuição ou suporte para o procedimento adequado.  Aqui está um exemplo para recompilar a initrd usando o utilitário `mkinitrd`:

1. Fazer backup da imagem initrd existente:

    ```
    cd /boot
    sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak
    ```

2. Reconstrua a initrd com os módulos de kernel hv_vmbus e hv_storvsc:

    ```
    sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`
    ```

### <a name="resizing-vhds"></a>Redimensionando VHDs
As imagens VHD no Azure devem ter um tamanho virtual alinhado a 1 MB.  Normalmente, os VHDs criados usando o Hyper-V estão alinhados corretamente.  Se o VHD não estiver alinhado corretamente, você poderá receber uma mensagem de erro semelhante à seguinte quando tentar criar uma imagem do VHD.

* O VHD http:\//\<mystorageaccount >. blob. Core. Windows. net/VHDs/MyLinuxVM. VHD tem um tamanho virtual sem suporte de 21475270656 bytes. O tamanho deve ser um número inteiro (em MBs).

Nesse caso, redimensione a VM usando o console do Gerenciador do Hyper-V ou o cmdlet [redimensionar-VHD](https://technet.microsoft.com/library/hh848535.aspx) do PowerShell.  Se você não estiver executando o em um ambiente do Windows, é recomendável usar `qemu-img` para converter (se necessário) e redimensionar o VHD.

> [!NOTE]
> Há um [bug conhecido nas versões QEMU-img](https://bugs.launchpad.net/qemu/+bug/1490611) > = 2.2.1 que resulta em um VHD formatado incorretamente. O problema foi corrigido no QEMU 2,6. Recomendamos o uso de `qemu-img` 2.2.0 ou inferior, ou 2,6 ou superior.
> 

1. Redimensionar o VHD diretamente usando ferramentas como `qemu-img` ou `vbox-manage` pode resultar em um VHD não inicializável.  É recomendável converter primeiro o VHD em uma imagem de disco bruto.  Se a imagem da VM foi criada como uma imagem de disco bruto (o padrão para alguns hipervisores, como o KVM), você pode ignorar esta etapa.
 
    ```
    qemu-img convert -f vpc -O raw MyLinuxVM.vhd MyLinuxVM.raw
    ```

1. Calcule o tamanho necessário da imagem do disco para que o tamanho virtual seja alinhado a 1 MB.  O script de shell bash a seguir usa `qemu-img info` para determinar o tamanho virtual da imagem de disco e, em seguida, calcula o tamanho para os próximos 1 MB.

    ```bash
    rawdisk="MyLinuxVM.raw"
    vhddisk="MyLinuxVM.vhd"

    MB=$((1024*1024))
    size=$(qemu-img info -f raw --output json "$rawdisk" | \
    gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    rounded_size=$(((($size+$MB-1)/$MB)*$MB))
    
    echo "Rounded Size = $rounded_size"
    ```

3. Redimensione o disco bruto usando `$rounded_size` conforme definido acima.

    ```bash
    qemu-img resize MyLinuxVM.raw $rounded_size
    ```

4. Agora, converta o disco bruto de volta em um VHD de tamanho fixo.

    ```bash
    qemu-img convert -f raw -o subformat=fixed -O vpc MyLinuxVM.raw MyLinuxVM.vhd
    ```

   Ou, com QEMU versão 2.6 +, inclua a opção `force_size`.

    ```bash
    qemu-img convert -f raw -o subformat=fixed,force_size -O vpc MyLinuxVM.raw MyLinuxVM.vhd
    ```

## <a name="linux-kernel-requirements"></a>Requisitos de kernel do Linux

Os drivers LIS (Linux Integration Services) para Hyper-V e Azure são contribuídos diretamente para o kernel upstream do Linux. Muitas distribuições que incluem uma versão recente do kernel do Linux (como 3. x) já têm esses drivers disponíveis ou fornecem versões reportadas desses drivers com seus kernels.  Esses drivers estão constantemente sendo atualizados no kernel upstream com novas correções e recursos. portanto, quando possível, recomendamos a execução de uma [distribuição endossada](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) que inclui essas correções e atualizações.

Se estiver executando uma variante de Red Hat Enterprise Linux versões 6,0 a 6,3, você precisará instalar os [drivers Lis mais recentes para o Hyper-V](https://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409). A partir do RHEL 6.4 + (e derivativos), os drivers LIS já estão incluídos no kernel e, portanto, nenhum pacote de instalação adicional é necessário.

Se for necessário um kernel personalizado, recomendamos uma versão recente do kernel (como 3,8 +). Para distribuições ou fornecedores que mantêm seu próprio kernel, você precisará backport regularmente os drivers de LIS do kernel upstream para o kernel personalizado.  Mesmo que você já esteja executando uma versão de kernel relativamente recente, é altamente recomendável manter o controle de correções upstream nos drivers LIS e backport-los conforme necessário. Os locais dos arquivos de origem do driver LIS são especificados no arquivo de [manutenção](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS) na árvore de origem do kernel do Linux:
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
Os patches a seguir devem ser incluídos no kernel. Esta lista não pode ser concluída para todas as distribuições.

* [ata_piix: adiar discos para os drivers do Hyper-V por padrão](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
* [storvsc: conta para pacotes em trânsito no caminho de redefinição](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)
* [storvsc: evitar o uso de WRITE_SAME](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=3e8f4f4065901c8dfc51407e1984495e1748c090)
* [storvsc: desabilitar gravação idêntica para RAID e drivers de adaptador de host virtual](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=54b2b50c20a61b51199bedb6e5d2f8ec2568fb43)
* [storvsc: correção de desreferência de ponteiro nulo](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=b12bb60d6c350b348a4e1460cd68f97ccae9822e)
* [storvsc: falhas de buffer de anéis podem resultar em congelamento de e/s](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=e86fb5e8ab95f10ec5f2e9430119d5d35020c951)
* [scsi_sysfs: proteger contra a execução dupla de __scsi_remove_device](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/scsi_sysfs.c?id=be821fd8e62765de43cc4f0e2db363d0e30a7e9b)

## <a name="the-azure-linux-agent"></a>O agente Linux do Azure
O [agente Linux do Azure](../extensions/agent-linux.md) `waagent` provisionar uma máquina virtual Linux no Azure. Você pode obter a versão mais recente, os problemas de arquivo ou enviar solicitações de pull no [repositório GitHub do agente do Linux](https://github.com/Azure/WALinuxAgent).

* O agente do Linux é lançado na licença do Apache 2,0. Muitas distribuições já fornecem pacotes RPM ou. deb para o agente, e esses pacotes podem ser facilmente instalados e atualizados.
* O agente Linux do Azure requer Python v 2.6 +.
* O agente também requer o módulo python-pyasn1. A maioria das distribuições fornece esse módulo como um pacote separado a ser instalado.
* Em alguns casos, o agente Linux do Azure pode não ser compatível com o NetworkManager. Muitos dos pacotes RPM/DEB fornecidos pelas distribuições configuram NetworkManager como um conflito para o pacote waagent. Nesses casos, ele desinstalará o NetworkManager quando você instalar o pacote de agente do Linux.
* O agente Linux do Azure deve estar na [versão mínima com suporte](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)ou acima dela.

## <a name="general-linux-system-requirements"></a>Requisitos gerais de sistema do Linux

1. Modifique a linha de inicialização do kernel em GRUB ou GRUB2 para incluir os seguintes parâmetros, para que todas as mensagens do console sejam enviadas para a primeira porta serial. Essas mensagens podem auxiliar o suporte do Azure com a depuração de quaisquer problemas.
    ```  
    console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300
    ```
    Também recomendamos *remover* os parâmetros a seguir, se existirem.
    ```  
    rhgb quiet crashkernel=auto
    ```
    A inicialização gráfica e silenciosa não é útil em um ambiente de nuvem, onde queremos que todos os logs sejam enviados para a porta serial. A opção `crashkernel` pode ser deixada configurada se necessário, mas observe que esse parâmetro reduz a quantidade de memória disponível na VM por pelo menos 128 MB, o que pode ser problemático para tamanhos de VM menores.

1. Instale o agente Linux do Azure.
  
    O agente Linux do Azure é necessário para provisionar uma imagem do Linux no Azure.  Muitas distribuições fornecem o agente como um pacote RPM ou. deb (o pacote é normalmente chamado de WALinuxAgent ou WALinuxAgent).  O agente também pode ser instalado manualmente seguindo as etapas no guia do [agente do Linux](../extensions/agent-linux.md).

1. Verifique se o servidor SSH está instalado e configurado para iniciar no momento da inicialização.  Essa configuração geralmente é o padrão.

1. Não crie espaço de permuta no disco do sistema operacional.
  
    O agente Linux do Azure pode configurar automaticamente o espaço de permuta usando o disco de recurso local que é anexado à VM após o provisionamento no Azure. O disco de recurso local é um disco *temporário* e pode ser esvaziado quando a VM é desprovisionada. Depois de instalar o agente Linux do Azure (etapa 2 acima), modifique os seguintes parâmetros em/etc/waagent.conf, conforme necessário.
    ```  
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: Set this to your desired size.
    ```
1. Execute os comandos a seguir para desprovisionar a máquina virtual.
  
     ```
     sudo waagent -force -deprovision
     export HISTSIZE=0
     logout
     ```  
   > [!NOTE]
   > No VirtualBox, você pode ver o erro a seguir depois de executar `waagent -force -deprovision` que diz `[Errno 5] Input/output error`. Essa mensagem de erro não é crítica e pode ser ignorada.

* Desligue a máquina virtual e carregue o VHD no Azure.

