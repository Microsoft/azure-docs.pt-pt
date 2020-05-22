---
title: Otimizar a VM do Linux no Azure
description: Aprenda algumas dicas de otimização para se certificar de que configura o seu VM Linux para um desempenho ideal no Azure
author: rickstercdn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/06/2016
ms.author: rclaus
ms.subservice: disks
ms.openlocfilehash: bf674170ff49f55fc7997a87d07f9069306fc0cd
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83774159"
---
# <a name="optimize-your-linux-vm-on-azure"></a>Otimizar a VM do Linux no Azure
A criação de uma máquina virtual Linux (VM) é fácil de fazer a partir da linha de comando ou do portal. Este tutorial mostra-lhe como garantir que o configurapara otimizar o seu desempenho na plataforma Microsoft Azure. Este tópico utiliza um Ubuntu Server VM, mas também pode criar uma máquina virtual Linux usando [as suas próprias imagens como modelos](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

## <a name="prerequisites"></a>Pré-requisitos
Este tópico assume que já tem uma subscrição Azure em funcionamento[(inscrição gratuita](https://azure.microsoft.com/pricing/free-trial/)de teste ) e já disponibilizou um VM para a sua subscrição Azure. Certifique-se de que tem o mais recente [Azure CLI](/cli/azure/install-az-cli2) instalado e iniciado na sua subscrição Azure com [login az](/cli/azure/reference-index) antes de [criar um VM](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="azure-os-disk"></a>Disco Os Azure
Uma vez criado um VM Linux em Azure, tem dois discos associados. **/dev/sda** é o seu disco OS, **/dev/sdb** é o seu disco temporário.  Não utilize o disco operativo principal **(/dev/sda)** para qualquer coisa, exceto o sistema operativo, uma vez que está otimizado para um tempo de arranque rápido de VM e não proporciona um bom desempenho para as suas cargas de trabalho. Pretende anexar um ou mais discos ao seu VM para obter armazenamento persistente e otimizado para os seus dados. 

## <a name="adding-disks-for-size-and-performance-targets"></a>Adicionar discos para alvos de tamanho e desempenho
Com base no tamanho vm, pode anexar até 16 discos adicionais numa Série A, 32 discos numa Série D e 64 discos numa máquina da Série G - cada um até 32 TB de tamanho. Adiciona discos extras conforme necessário nos requisitos do seu espaço e IOps. Cada disco tem um alvo de desempenho de 500 IOps para armazenamento padrão e até 20.000 IOps por disco para Armazenamento Premium.

Para obter os iOps mais elevados em discos de armazenamento premium onde as suas definições de cache foram definidas para **ReadOnly** ou **None,** deve **desativar as barreiras** enquanto monta o sistema de ficheiros em Linux. Não precisa de barreiras porque os escritos para discos apoiados por Armazenamento Premium são duráveis para estas definições de cache.

* Se utilizar **reiserFS,** desative barreiras utilizando a opção de montagem `barrier=none` (Para ativar barreiras, `barrier=flush` utilize)
* Se utilizar **ext3/ext4,** desative barreiras utilizando a opção de montagem `barrier=0` (Para permitir barreiras, `barrier=1` utilize)
* Se utilizar **o XFS,** desative as barreiras utilizando a opção de montagem `nobarrier` (Para ativar barreiras, utilize a opção) `barrier`

## <a name="unmanaged-storage-account-considerations"></a>Considerações de conta de armazenamento não geridas
A ação predefinida quando cria um VM com o AZURE CLI é utilizar discos geridos azure.  Estes discos são manuseados pela plataforma Azure e não requerem qualquer preparação ou localização para os armazenar.  Os discos não geridos requerem uma conta de armazenamento e têm algumas considerações adicionais de desempenho.  Para mais informações sobre discos geridos, veja [Managed Disks Overview (Descrição geral dos Managed Disks)](../windows/managed-disks-overview.md).  A secção seguinte descreve considerações de desempenho apenas quando utiliza discos não geridos.  Mais uma vez, a solução de armazenamento predefinido e recomendada é utilizar discos geridos.

Se criar um VM com discos não geridos, certifique-se de que fixa discos a partir de contas de armazenamento residentes na mesma região que o seu VM para garantir a proximidade e minimizar a latência da rede.  Cada conta de armazenamento Standard tem um máximo de 20 k IOps e uma capacidade de tamanho de 500 TB.  Este limite funciona para aproximadamente 40 discos fortemente utilizados, incluindo tanto o disco OS como quaisquer discos de dados que criar. Para as contas de Armazenamento Premium, não existe limite máximo de IOps, mas existe um limite de tamanho de 32 TB. 

Ao lidar com cargas de trabalho de IOps elevadas e escolheu o Armazenamento Padrão para os seus discos, poderá ter de dividir os discos em várias contas de armazenamento para se certificar de que não atingiu o limite de 20.000 IOps para contas standard de armazenamento. O seu VM pode conter uma mistura de discos de diferentes contas de armazenamento e tipos de contas de armazenamento para obter a sua configuração ideal.
 

## <a name="your-vm-temporary-drive"></a>Sua unidade temporária VM
Por predefinição quando cria um VM, o Azure fornece-lhe um disco OS **(/dev/sda)** e um disco temporário **(/dev/sdb).**  Todos os discos adicionais que adiciona aparecem como **/dev/sdc,** **/dev/sdd,** **/dev/sde** e assim por diante. Todos os dados do seu disco temporário **(/dev/sdb)** não são duráveis e podem ser perdidos se eventos específicos como O VM Redimensionar, reimplantação ou manutenção forçam um reinício do seu VM.  O tamanho e o tipo do disco temporário estão relacionados com o tamanho VM que escolheu no momento da implementação. Todos os VMs de tamanho premium (DS, G e série DS_V2) a unidade temporária é apoiada por um SSD local para um desempenho adicional de até 48k IOps. 

## <a name="linux-swap-partition"></a>Partição de troca de Linux
Se o seu Azure VM for proveniente de uma imagem Ubuntu ou CoreOS, então pode utilizar o CustomData para enviar um cloud-config para cloud-init. Se [fez o upload](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) de uma imagem personalizada do Linux que utiliza cloud-init, também configura divisórias de troca usando cloud-init.

No Ubuntu Cloud Images, deve utilizar cloud-init para configurar a divisória de troca. Para mais informações, consulte [As Divisórias AzureSwap](https://wiki.ubuntu.com/AzureSwapPartitions).

Para imagens sem suporte para cloud-init, as imagens VM implantadas do Azure Marketplace têm um Agente VM Linux integrado com o OS. Este agente permite que o VM interaja com vários serviços Azure. Assumindo que implementou uma imagem padrão do Mercado Azure, teria de fazer o seguinte para configurar corretamente as definições de ficheiros de swap do Linux:

Localize e modifique duas entradas no ficheiro **/etc/waagent.conf.** Controlam a existência de um ficheiro de permuta dedicado e o tamanho do ficheiro swap. Os parâmetros que precisa de verificar são `ResourceDisk.EnableSwap` e`ResourceDisk.SwapSizeMB` 

Para ativar um disco devidamente ativado e um ficheiro de troca montado, certifique-se de que os parâmetros têm as seguintes definições:

* Resourcedisk.Enableswap=Y
* ResourceDisk.SwapSizeMB={size in MB para satisfazer as suas necessidades} 

Depois de ter feito a alteração, tem de reiniciar o waagent ou reiniciar o vM Linux para refletir essas alterações.  Sabe que as alterações foram implementadas e foi criado um ficheiro de troca quando utiliza o `free` comando para visualizar o espaço livre. O exemplo seguinte tem um ficheiro de troca de 512MB criado como resultado da modificação do ficheiro **waagent.conf:**

```bash
azuseruser@myVM:~$ free
            total       used       free     shared    buffers     cached
Mem:       3525156     804168    2720988        408       8428     633192
-/+ buffers/cache:     162548    3362608
Swap:       524284          0     524284
```

## <a name="io-scheduling-algorithm-for-premium-storage"></a>Algoritmo de agendamento de I/S para Armazenamento Premium
Com o kernel linux 2.6.18, o algoritmo de agendamento de I/S padrão foi alterado de Deadline para CFQ (algoritmo de fila completamente justo). Para padrões de Acesso Aleatório de I/S, existe uma diferença insignificante nas diferenças de desempenho entre o CFQ e o Deadline.  Para discos baseados em SSD onde o padrão de I/S do disco é predominantemente sequencial, voltar ao algoritmo NOOP ou Deadline pode obter um melhor desempenho em I/S.

### <a name="view-the-current-io-scheduler"></a>Ver o atual programador de I/S
Utilize o seguinte comando:  

```bash
cat /sys/block/sda/queue/scheduler
```

Veja a saída seguinte, o que indica o programador atual.  

```bash
noop [deadline] cfq
```

### <a name="change-the-current-device-devsda-of-io-scheduling-algorithm"></a>Alterar o dispositivo atual (/dev/sda) do algoritmo de agendamento de I/O
Utilize os seguintes comandos:  

```bash
azureuser@myVM:~$ sudo su -
root@myVM:~# echo "noop" >/sys/block/sda/queue/scheduler
root@myVM:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
root@myVM:~# update-grub
```

> [!NOTE]
> Aplicar esta definição para **/dev/sda** por si só não é útil. Coloque em todos os discos de dados onde o I/O sequencial domina o padrão de I/S.  

Deve ver a seguinte saída, indicando que **grub.cfg** foi reconstruído com sucesso e que o programador predefinido foi atualizado para NOOP.  

```bash
Generating grub configuration file ...
Found linux image: /boot/vmlinuz-3.13.0-34-generic
Found initrd image: /boot/initrd.img-3.13.0-34-generic
Found linux image: /boot/vmlinuz-3.13.0-32-generic
Found initrd image: /boot/initrd.img-3.13.0-32-generic
Found memtest86+ image: /memtest86+.elf
Found memtest86+ image: /memtest86+.bin
done
```

Para a família de distribuição do Chapéu Vermelho, só precisa do seguinte comando:   

```bash
echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local
```

Ubuntu 18.04 com o kernel afinado em Azure usa programadores de I/O multi-fila. Neste cenário, `none` está a seleção adequada em vez de `noop` . Para mais informações, consulte [os Programadores Ubuntu I/O](https://wiki.ubuntu.com/Kernel/Reference/IOSchedulers).

## <a name="using-software-raid-to-achieve-higher-iops"></a>Usando o Software RAID para obter i/ops mais elevados
Se as suas cargas de trabalho requerem mais IOps do que um único disco pode fornecer, precisa de utilizar uma configuração RAID de software de vários discos. Como o Azure já realiza a resiliência do disco na camada de tecido local, obtém-se o maior nível de desempenho a partir de uma configuração de striping RAID-0.  Provisão e criação de discos no ambiente Azure e prenda-os ao seu VM Linux antes de dividir, fortar e montar as unidades.  Mais detalhes sobre a configuração de uma configuração RAID de software no seu Linux VM em azure podem ser encontrados no Raid de Software De Configuração no documento **[Linux.](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Como alternativa a uma configuração raid tradicional, também pode optar por instalar o Logical Volume Manager (LVM) de forma a configurar uma série de discos físicos num único volume de armazenamento lógico listrado. Nesta configuração, as leituras e os escritos são distribuídos por vários discos contidos no grupo de volume (semelhante ao RAID0). Por razões de desempenho, é provável que queira riscar os seus volumes lógicos para que as leituras e os escritos utilizem todos os seus discos de dados anexados.  Mais detalhes sobre a configuração de um volume lógico listrado no seu Linux VM em Azure podem ser encontrados no **[Configure LVM em um Documento Linux VM em Azure.](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

## <a name="next-steps"></a>Passos Seguintes
Lembre-se, como em todas as discussões de otimização, precisa realizar testes antes e depois de cada alteração para medir o impacto que a mudança tem.  A otimização é um processo passo a passo que tem resultados diferentes em diferentes máquinas no seu ambiente.  O que funciona para uma configuração pode não funcionar para os outros.

Alguns links úteis para recursos adicionais:

* [Guia de Utilizador de Agente Azure Linux](../extensions/agent-linux.md)
* [Configure Software RAID em Linux](configure-raid.md)
