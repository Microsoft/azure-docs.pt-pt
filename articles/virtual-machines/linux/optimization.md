---
title: Otimizar a VM do Linux no Azure
description: Aprenda algumas dicas de otimização para se certificar de que configura o seu Linux VM para um desempenho ideal no Azure
author: rickstercdn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/06/2016
ms.author: rclaus
ms.subservice: disks
ms.openlocfilehash: eff512c9d050eb293391233848fcece83e845680
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88654196"
---
# <a name="optimize-your-linux-vm-on-azure"></a>Otimizar a VM do Linux no Azure
A criação de uma máquina virtual Linux (VM) é fácil de fazer a partir da linha de comando ou a partir do portal. Este tutorial mostra-lhe como garantir que o configura para otimizar o seu desempenho na plataforma Microsoft Azure. Este tópico utiliza um Ubuntu Server VM, mas também pode criar uma máquina virtual Linux usando [as suas próprias imagens como modelos.](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)  

## <a name="prerequisites"></a>Pré-requisitos
Este tópico pressupõe que já tem uma subscrição Azure em funcionamento ([inscrição gratuita](https://azure.microsoft.com/pricing/free-trial/)de teste ) e já adpôs um VM na sua subscrição Azure. Certifique-se de que tem o mais recente [Azure CLI](/cli/azure/install-az-cli2) instalado e iniciado na sua subscrição Azure com [login az](/cli/azure/reference-index) antes de [criar um VM](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="azure-os-disk"></a>Disco Azure OS
Uma vez que cria um Linux VM em Azure, tem dois discos associados a ele. **/dev/sda** é o seu disco DE, **/dev/sdb** é o seu disco temporário.  Não utilize o disco principal de SO **(/dev/sda)** para nada menos o sistema operativo, uma vez que está otimizado para um tempo de arranque rápido em VM e não proporciona um bom desempenho para as suas cargas de trabalho. Pretende anexar um ou mais discos ao seu VM para obter um armazenamento persistente e otimizado para os seus dados. 

## <a name="adding-disks-for-size-and-performance-targets"></a>Adicionar discos para alvos de tamanho e desempenho
Com base no tamanho do VM, pode anexar até 16 discos adicionais numa Série A, 32 discos numa Série D e 64 discos numa máquina da Série G - cada um com um tamanho até 32 TB. Adiciona discos extras conforme necessário para o seu espaço e requisitos de IOps. Cada disco tem um objetivo de desempenho de 500 IOps para armazenamento padrão e até 20.000 IOps por disco para Armazenamento Premium.

Para obter os IOps mais elevados nos discos de armazenamento Premium onde as suas definições de cache foram definidas para **ReadOnly** ou **None**, deve **desativar barreiras** enquanto monta o sistema de ficheiros no Linux. Não precisa de barreiras porque as gravações para discos com apoio de Armazenamento Premium são duráveis para estas definições de cache.

* Se utilizar **o ReiserFS,** desative as barreiras utilizando a opção de montagem `barrier=none` (Para ativar barreiras, `barrier=flush` utilize)
* Se utilizar **o ext3/ext4,** desative as barreiras utilizando a opção de montagem `barrier=0` (Para ativar barreiras, utilize `barrier=1` -
* Se utilizar **XFS,** desative as barreiras utilizando a opção de montagem `nobarrier` (Para ativar barreiras, utilize a opção `barrier` )

## <a name="unmanaged-storage-account-considerations"></a>Considerações de conta de armazenamento não geridos
A ação predefinida quando cria um VM com o CLI Azure é utilizar discos geridos Azure.  Estes discos são manuseados pela plataforma Azure e não requerem qualquer preparação ou localização para os armazenar.  Os discos não geridos requerem uma conta de armazenamento e têm algumas considerações adicionais de desempenho.  Para mais informações sobre discos geridos, veja [Managed Disks Overview (Descrição geral dos Managed Disks)](../managed-disks-overview.md).  A secção seguinte descreve considerações de desempenho apenas quando utiliza discos não geridos.  Mais uma vez, a solução de armazenamento padrão e recomendada é a utilização de discos geridos.

Se criar um VM com discos não geridos, certifique-se de que anexa discos de contas de armazenamento que residem na mesma região que o seu VM para garantir a proximidade e minimizar a latência da rede.  Cada conta de armazenamento Standard tem um máximo de 20k IOps e uma capacidade de tamanho de 500 TB.  Este limite resulta em aproximadamente 40 discos fortemente utilizados, incluindo o disco SO e quaisquer discos de dados que crie. Para contas de Armazenamento Premium, não existe limite máximo de IOps, mas existe um limite de tamanho de 32 TB. 

Ao lidar com altas cargas de trabalho de IOps e tiver escolhido o Standard Storage para os seus discos, poderá ter de dividir os discos em várias contas de armazenamento para se certificar de que não atingiu o limite de 20.000 IOps para contas de Armazenamento Padrão. O seu VM pode conter uma mistura de discos de diferentes contas de armazenamento e tipos de conta de armazenamento para obter a sua configuração ideal.
 

## <a name="your-vm-temporary-drive"></a>A sua unidade temporária VM
Por predefinição, quando cria um VM, o Azure fornece-lhe um disco DEL **(/dev/sda)** e um disco temporário **(/dev/sdb).**  Todos os discos adicionais que adicionar aparecem como **/dev/sdc,** **/dev/sdd,** **/dev/sde** e assim por diante. Todos os dados do seu disco temporário **(/dev/sdb)** não são duráveis, podendo perder-se se eventos específicos como redimensionamento, redistribuição ou manutenção de VM obrigam ao reinício do seu VM.  O tamanho e o tipo do seu disco temporário estão relacionados com o tamanho VM que escolheu na hora de implantação. Todas as VMs de tamanho premium (sérieS DS, G e DS_V2) a unidade temporária é apoiada por um SSD local para um desempenho adicional de até 48k IOps. 

## <a name="linux-swap-partition"></a>Partição de troca de Linux
Se o seu Azure VM é de uma imagem Ubuntu ou CoreOS, então pode usar o CustomData para enviar um cloud-config para cloud-init. Se [você carregou uma imagem linux personalizada](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) que usa cloud-init, você também configura trocar divisões usando cloud-init.

Nas Imagens cloud Ubuntu, deve utilizar a cloud-init para configurar a partição de troca. Para mais informações, consulte [AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions).

Para imagens sem suporte de nuvem, as imagens VM implementadas a partir do Azure Marketplace têm um Agente VM Linux integrado com o SO. Este agente permite que o VM interaja com vários serviços Azure. Assumindo que implementou uma imagem padrão a partir do Azure Marketplace, teria de fazer o seguinte para configurar corretamente as definições de ficheiros de troca de Linux:

Localize e modifique duas entradas no ficheiro **/etc/waagent.conf.** Controlam a existência de um ficheiro de troca dedicado e o tamanho do ficheiro swap. Os parâmetros que precisa de verificar são `ResourceDisk.EnableSwap` e `ResourceDisk.SwapSizeMB` 

Para ativar um disco devidamente ativado e um ficheiro de troca montado, certifique-se de que os parâmetros têm as seguintes definições:

* ResourceDisk.EnableSwap=Y
* ResourceDisk.SwapSizeMB={size in MB para atender às suas necessidades} 

Depois de ter feito a alteração, tem de reiniciar o waagent ou reiniciar o seu Linux VM para refletir essas alterações.  Sabe que as alterações foram implementadas e foi criado um ficheiro swap quando utiliza o comando para visualizar o `free` espaço livre. O exemplo a seguir tem um ficheiro swap de 512MB criado como resultado da modificação do ficheiro **waagent.conf:**

```bash
azuseruser@myVM:~$ free
            total       used       free     shared    buffers     cached
Mem:       3525156     804168    2720988        408       8428     633192
-/+ buffers/cache:     162548    3362608
Swap:       524284          0     524284
```

## <a name="io-scheduling-algorithm-for-premium-storage"></a>Algoritmo de agendamento de I/O para armazenamento premium
Com o kernel 2.6.18 Linux, o algoritmo de agendamento de I/O padrão foi alterado de Deadline para CFQ (algoritmo de fila completamente justo). Para padrões de E/S de acesso aleatório, há uma diferença negligenciável nas diferenças de desempenho entre o CFQ e o Deadline.  Para discos baseados em SSD onde o padrão de E/S do disco é predominantemente sequencial, voltar para o algoritmo NOOP ou Deadline pode alcançar um melhor desempenho de I/S.

### <a name="view-the-current-io-scheduler"></a>Ver o atual programador de E/S
Utilize o seguinte comando:  

```bash
cat /sys/block/sda/queue/scheduler
```

Vê a saída seguinte, o que indica o programador atual.  

```bash
noop [deadline] cfq
```

### <a name="change-the-current-device-devsda-of-io-scheduling-algorithm"></a>Alterar o dispositivo atual (/dev/sda) do algoritmo de agendamento de E/O
Utilize os seguintes comandos:  

```bash
azureuser@myVM:~$ sudo su -
root@myVM:~# echo "noop" >/sys/block/sda/queue/scheduler
root@myVM:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
root@myVM:~# update-grub
```

> [!NOTE]
> Aplicar esta definição para **/dev/sda** por si só não é útil. Definir em todos os discos de dados onde a E/S sequencial domina o padrão de E/S.  

Deve ver a seguinte saída, indicando que **a grub.cfg** foi reconstruída com sucesso e que o programador predefinido foi atualizado para a NOOP.  

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

Ubuntu 18.04 com o núcleo afinado Azure usa agendadores de I/S multi-fila. Nesse cenário, `none` é a seleção adequada em vez de `noop` . Para mais informações, consulte [os Agendadores Ubuntu I/O](https://wiki.ubuntu.com/Kernel/Reference/IOSchedulers).

## <a name="using-software-raid-to-achieve-higher-iops"></a>Utilização de RAID de Software para atingir i/ops mais elevados
Se as suas cargas de trabalho requerem mais IOps do que um único disco pode fornecer, tem de utilizar uma configuração RAID de software de vários discos. Como o Azure já realiza a resiliência do disco na camada de tecido local, obtém-se o mais alto nível de desempenho a partir de uma configuração de striping RAID-0.  Provisões e criações de discos no ambiente Azure e prenda-os ao seu LM Linux antes de dividir, formatar e montar as unidades.  Mais detalhes sobre a configuração de um RAID de software no seu Linux VM em azul podem ser encontrados no RAID de Software Configurado no documento **[Linux.](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Como alternativa a uma configuração raid tradicional, também pode optar por instalar o Logical Volume Manager (LVM) de forma a configurar uma série de discos físicos num único volume de armazenamento lógico listrado. Nesta configuração, as leituras e as gravações são distribuídas por vários discos contidos no grupo de volume (semelhante ao RAID0). Por razões de desempenho, é provável que queira riscar os seus volumes lógicos para que as leituras e as gravações utilizem todos os seus discos de dados anexados.  Mais detalhes sobre a configuração de um volume lógico listrado no seu Linux VM em Azure podem ser encontrados no **[CONFIGURE LVM num documento Linux VM em Azure.](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

## <a name="next-steps"></a>Passos Seguintes
Lembre-se, como em todas as discussões de otimização, você precisa realizar testes antes e depois de cada mudança para medir o impacto que a mudança tem.  A otimização é um processo passo a passo que tem resultados diferentes em diferentes máquinas do seu ambiente.  O que funciona para uma configuração pode não funcionar para os outros.

Algumas ligações úteis a recursos adicionais:

* [Guia de Utilizador de Agente Azure Linux](../extensions/agent-linux.md)
* [Configure software RAID no Linux](configure-raid.md)
