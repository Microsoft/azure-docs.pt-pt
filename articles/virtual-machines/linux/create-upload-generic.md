---
title: Criar e carregar um Linux VHD
description: Aprenda a criar e carregar um disco rígido virtual Azure (VHD) que contenha um sistema operativo Linux.
author: gbowerman
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 10/08/2018
ms.author: guybo
ms.openlocfilehash: 9888cde8bca9fb0646dbdc8bb601b0887908ad1d
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98203240"
---
# <a name="information-for-non-endorsed-distributions"></a>Informação para distribuições não endossadas

A plataforma Azure SLA aplica-se a máquinas virtuais que executam o Sistema Linux apenas quando uma das [distribuições endossadas](endorsed-distros.md) é utilizada. Para estas distribuições endossadas, as imagens Linux pré-configuradas são fornecidas no Mercado Azure.

* [Linux on Azure - Distribuição endossada](endorsed-distros.md)
* [Suporte para imagens Linux no Microsoft Azure](https://support.microsoft.com/kb/2941892)

Todas as distribuições em execução em Azure têm uma série de pré-requisitos. Este artigo não pode ser abrangente, pois cada distribuição é diferente. Mesmo que cumpra todos os critérios abaixo, poderá ter de ajustar significativamente o seu sistema Linux para que este possa funcionar corretamente.

Recomendamos que comece com um dos [Linux em Azure Endossado Distribuições.](endorsed-distros.md) Os seguintes artigos mostram-lhe como preparar as várias distribuições de Linux endossadas que são suportadas no Azure:

- [Distribuições baseadas em CentOS](create-upload-centos.md)
- [Debian Linux](debian-create-upload-vhd.md)
- [Flatcar Container Linux](flatcar-create-upload-vhd.md)
- [Oracle Linux](oracle-create-upload-vhd.md)
- [Red Hat Enterprise Linux](redhat-create-upload-vhd.md)
- [SLES e openSUSE](suse-create-upload-vhd.md)
- [Ubuntu](create-upload-ubuntu.md)

Este artigo centra-se na orientação geral para executar a sua distribuição Linux no Azure.

## <a name="general-linux-installation-notes"></a>Notas de instalação gerais do Linux
* O formato de disco rígido virtual Hyper-V (VHDX) não é suportado em Azure, apenas *VHD fixo*.  Pode converter o disco em formato VHD utilizando o Hyper-V Manager ou o [cmdlet Converte-VHD.](/powershell/module/hyper-v/convert-vhd) Se estiver a utilizar a VirtualBox, selecione **tamanho fixo** em vez do padrão (atribuído dinamicamente) ao criar o disco.
* A azure suporta máquinas virtuais Gen1 (BOTA BIOS) & Gen2 (bota UEFI).
* O tamanho máximo permitido para o VHD é de 1.023 GB.
* Ao instalar o sistema Linux, recomendamos que utilize divisórias padrão, em vez de Logical Volume Manager (LVM), que é o padrão para muitas instalações. A utilização de divisórias padrão evitará conflitos de nome LVM com VMs clonados, especialmente se um disco de SO for alguma vez ligado a outro VM idêntico para resolução de problemas. [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) ou [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid) podem ser usados em discos de dados.
* É necessário suporte kernel para a montagem de sistemas de ficheiros UDF. No início, a configuração de provisionamento é passada para o Linux VM utilizando meios formatados por UDF que estão ligados ao hóspede. O agente Azure Linux deve montar o sistema de ficheiros UDF para ler a sua configuração e a provisionar o VM.
* As versões de kernel Linux antes de 2.6.37 não suportam UMA em Hiper-V com tamanhos VM maiores. Esta questão afeta principalmente as distribuições mais antigas utilizando o núcleo a montante do chapéu vermelho 2.6.32, e foi fixada em Red Hat Enterprise Linux (RHEL) 6.6 (kernel-2.6.32-504). Os sistemas que executam núcleos personalizados com mais de 2.6.37, ou núcleos baseados em RHEL com mais de 2.6.32-504 devem definir o parâmetro de arranque `numa=off` na linha de comando do núcleo em grub.conf. Para mais informações, consulte [Red Hat KB 436883](https://access.redhat.com/solutions/436883).
* Não configuure uma partição de troca no disco SO. O agente Linux pode ser configurado para criar um ficheiro de troca no disco de recursos temporários, conforme descrito nos passos seguintes.
* Todos os VHDs em Azure devem ter um tamanho virtual alinhado a 1 MB. Ao converter de um disco cru para VHD deve certificar-se de que o tamanho do disco bruto é um múltiplo de 1 MB antes da conversão, conforme descrito nos passos seguintes.

### <a name="installing-kernel-modules-without-hyper-v"></a>Instalação de módulos de núcleo sem Hiper-V
O Azure funciona no hipervisor Hiper-V, por isso o Linux requer certos módulos de núcleo para funcionar em Azure. Se tiver um VM que foi criado fora do Hyper-V, os instaladores Linux podem não incluir os controladores de Hiper-V no ramdisk inicial (initrd ou initramfs), a menos que o VM detete que está a funcionar num ambiente Hiper-V. Ao utilizar um sistema de virtualização diferente (como VirtualBox, KVM, e assim por diante) para preparar a sua imagem Linux, poderá ter de reconstruir o initrd para que pelo menos os módulos de kernel hv_vmbus e hv_storvsc estejam disponíveis no ramdisco inicial.  Esta questão conhecida é para sistemas baseados na distribuição a montante do Red Hat, e possivelmente outros.

O mecanismo de reconstrução da imagem initrd ou initramfs pode variar dependendo da distribuição. Consulte a documentação ou suporte da sua distribuição para o procedimento adequado.  Aqui está um exemplo para a reconstrução do initrd utilizando o `mkinitrd` utilitário:

1. Apoie a imagem initrd existente:

    ```
    cd /boot
    sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak
    ```

2. Reconstruir o initrd com os módulos de hv_vmbus e hv_storvsc do núcleo:

    ```
    sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`
    ```

### <a name="resizing-vhds"></a>Redimensionamento VHDs
As imagens VHD em Azure devem ter um tamanho virtual alinhado a 1 MB.  Normalmente, os VHDs criados com Hiper-V estão alinhados corretamente.  Se o VHD não estiver corretamente alinhado, poderá receber uma mensagem de erro semelhante à seguinte quando tentar criar uma imagem a partir do seu VHD.

* O VHD http: \/ / \<mystorageaccount> .blob.core.windows.net/vhds/MyLinuxVM.vhd tem um tamanho virtual não suportado de 21475270656 bytes. O tamanho deve ser um número inteiro (em MBs).

Neste caso, redimensione o VM utilizando a consola Hyper-V Manager ou o [cmdlet Resize-VHD](/powershell/module/hyper-v/resize-vhd) PowerShell.  Se não estiver a funcionar num ambiente Windows, recomendamos que se `qemu-img` converta (se necessário) e redimensione o VHD.

> [!NOTE]
> Existe um bug conhecido nas versões [qemu-img](https://bugs.launchpad.net/qemu/+bug/1490611) >=2.2.1 que resulta num VHD inadequadamente formatado. A questão foi corrigida no QEMU 2.6. Recomendamos a utilização de `qemu-img` 2.2.0 ou inferior, ou 2.6 ou superior.
> 

1. Redimensionar o VHD diretamente utilizando ferramentas como `qemu-img` ou podem resultar num `vbox-manage` VHD insaciável.  Recomendamos primeiro a conversão do VHD para uma imagem de disco RAW.  Se a imagem VM foi criada como uma imagem de disco RAW (o padrão para alguns hipervisores como o KVM), então pode saltar este passo.
 
    ```
    qemu-img convert -f vpc -O raw MyLinuxVM.vhd MyLinuxVM.raw
    ```

1. Calcular o tamanho necessário da imagem do disco de modo a que o tamanho virtual esteja alinhado a 1 MB.  O seguinte script de concha de bash usa `qemu-img info` para determinar o tamanho virtual da imagem do disco e, em seguida, calcula o tamanho para o próximo 1 MB.

    ```bash
    rawdisk="MyLinuxVM.raw"
    vhddisk="MyLinuxVM.vhd"

    MB=$((1024*1024))
    size=$(qemu-img info -f raw --output json "$rawdisk" | \
    gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    rounded_size=$(((($size+$MB-1)/$MB)*$MB))
    
    echo "Rounded Size = $rounded_size"
    ```

3. Redimensione o disco cru utilizando `$rounded_size` como definido acima.

    ```bash
    qemu-img resize MyLinuxVM.raw $rounded_size
    ```

4. Agora, converta o disco RAW de volta para um VHD de tamanho fixo.

    ```bash
    qemu-img convert -f raw -o subformat=fixed -O vpc MyLinuxVM.raw MyLinuxVM.vhd
    ```

   Ou, com a versão qemu 2.6+, incluir a `force_size` opção.

    ```bash
    qemu-img convert -f raw -o subformat=fixed,force_size -O vpc MyLinuxVM.raw MyLinuxVM.vhd
    ```

## <a name="linux-kernel-requirements"></a>Requisitos de Kernel Linux

Os controladores linux integration services (LIS) para Hyper-V e Azure são contribuídos diretamente para o kernel linux a montante. Muitas distribuições que incluem uma versão recente do kernel do Linux (como 3.x) já têm estes controladores disponíveis, ou de outra forma fornecem versões backported destes condutores com os seus núcleos.  Estes controladores estão constantemente a ser atualizados no núcleo a montante com novas correções e funcionalidades, pelo que, quando possível, recomendamos a execução de uma [distribuição endossada](endorsed-distros.md) que inclua estas correções e atualizações.

Se estiver a executar uma variante das versões Red Hat Enterprise Linux 6.0 a 6.3, então terá de instalar os [mais recentes controladores LIS para Hyper-V](https://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409). A partir do RHEL 6.4+ (e derivados), os condutores lis já estão incluídos com o núcleo e, por isso, não são necessários pacotes de instalação adicionais.

Se for necessário um núcleo personalizado, recomendamos uma versão recente do kernel (como 3.8+). Para distribuições ou fornecedores que mantenham o seu próprio núcleo, você precisará de apoiar regularmente os condutores LIS do núcleo a montante para o seu núcleo personalizado.  Mesmo que já esteja a executar uma versão de kernel relativamente recente, recomendamos vivamente que se mantenham atentos a quaisquer correções a montante nos condutores lis e os apoiem conforme necessário. As localizações dos ficheiros de origem do condutor LIS estão especificadas no ficheiro [KEEPERS](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS) na árvore de origem linux:
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
As seguintes manchas devem ser incluídas no núcleo. Esta lista não pode estar completa para todas as distribuições.

* [ata_piix: adiar os discos para os controladores Hiper-V por padrão](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
* [storvsc: Conta para pacotes em trânsito na rota RESET](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)
* [storvsc: evitar o uso de WRITE_SAME](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=3e8f4f4065901c8dfc51407e1984495e1748c090)
* [storvsc: Desative escrever o MESMO para os controladores raid e adaptadores de hospedeiros virtuais](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=54b2b50c20a61b51199bedb6e5d2f8ec2568fb43)
* [storvsc: Correção de dereferência de ponteiro NULO](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=b12bb60d6c350b348a4e1460cd68f97ccae9822e)
* [storvsc: falhas no tampão do anel podem resultar em congelamento de E/S](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=e86fb5e8ab95f10ec5f2e9430119d5d35020c951)
* [scsi_sysfs: proteger contra a dupla execução de __scsi_remove_device](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/scsi_sysfs.c?id=be821fd8e62765de43cc4f0e2db363d0e30a7e9b)

## <a name="the-azure-linux-agent"></a>O Agente Azure Linux
O [Agente Azure Linux](../extensions/agent-linux.md) `waagent` fornece uma máquina virtual Linux em Azure. Pode obter a versão mais recente, problemas de ficheiros ou submeter pedidos de pull no [repo do Agente Linux GitHub](https://github.com/Azure/WALinuxAgent).

* O agente Linux é libertado sob a licença Apache 2.0. Muitas distribuições já fornecem pacotes de RPM ou .deb para o agente, e estes pacotes podem ser facilmente instalados e atualizados.
* O agente Azure Linux requer Python v2.6+.
* O agente também requer o módulo python-pyasn1. A maioria das distribuições fornece este módulo como um pacote separado para ser instalado.
* Em alguns casos, o Agente Azure Linux pode não ser compatível com o NetworkManager. Muitos dos pacotes RPM/deb fornecidos pelas distribuições configuram o NetworkManager como um conflito para o pacote waagent. Nestes casos, irá desinstalar o NetworkManager quando instalar o pacote de agente Linux.
* O Agente Azure Linux deve estar na [versão mínima suportada](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

## <a name="general-linux-system-requirements"></a>Requisitos gerais do sistema Linux

1. Modifique a linha de arranque do núcleo em GRUB ou GRUB2 para incluir os seguintes parâmetros, de modo a que todas as mensagens de consola sejam enviadas para a primeira porta em série. Estas mensagens podem ajudar o suporte do Azure a depurar quaisquer problemas.
    ```  
    console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300
    ```
    Recomendamos também *a remoção* dos seguintes parâmetros, se existirem.
    ```  
    rhgb quiet crashkernel=auto
    ```
    A bota gráfica e silenciosa não é útil num ambiente em nuvem, onde queremos que todos os registos enviados para a porta em série. A `crashkernel` opção pode ser deixada configurada se necessário, mas note que este parâmetro reduz a quantidade de memória disponível no VM em pelo menos 128 MB, o que pode ser problemático para tamanhos de VM menores.

1. Instale o Agente Azure Linux.
  
    O agente Azure Linux é necessário para o fornecimento de uma imagem Linux em Azure.  Muitas distribuições fornecem o agente como um pacote RPM ou .deb (o pacote é tipicamente chamado WALinuxAgent ou walinuxagent).  O agente também pode ser instalado manualmente seguindo os passos no Guia do [Agente Linux](../extensions/agent-linux.md).

1. Certifique-se de que o servidor SSH está instalado e configurado para começar na hora do arranque.  Esta configuração é geralmente o padrão.

1. Não crie espaço de troca no disco so.
  
    O Agente Azure Linux pode configurar automaticamente o espaço de troca utilizando o disco de recursos local que é ligado ao VM após o provisionamento no Azure. O disco de recursos local é um disco *temporário* e pode ser esvaziado quando o VM é desprovisionado. Depois de instalar o Agente Azure Linux (passo 2 acima), modifique os seguintes parâmetros em /etc/waagent.conf conforme necessário.
    ```  
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: Set this to your desired size.
    ```
1. Executar os seguintes comandos para desprovisionar a máquina virtual.
  
     ```
     sudo waagent -force -deprovision
     export HISTSIZE=0
     logout
     ```  
   > [!NOTE]
   > Na Virtualbox pode ver o seguinte erro depois de executar `waagent -force -deprovision` que diz `[Errno 5] Input/output error` . Esta mensagem de erro não é crítica e pode ser ignorada.

* Desligue a máquina virtual e carreque o VHD para Azure.