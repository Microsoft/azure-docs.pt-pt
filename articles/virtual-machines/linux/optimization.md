---
title: Otimizar a sua VM do Linux no Azure | Documentos da Microsoft
description: Saiba mais algumas sugestões de otimização para certificar-se de que configurou a sua VM do Linux para otimizar o desempenho no Azure
keywords: máquina virtual do Linux, máquinas virtuais linux, máquina virtual do ubuntu
services: virtual-machines-linux
documentationcenter: ''
author: rickstercdn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: 8baa30c8-d40e-41ac-93d0-74e96fe18d4c
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/06/2016
ms.author: rclaus
ms.subservice: disks
ms.openlocfilehash: bd59257c1136f52beaf217c1f983c8aeb7bd81d5
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671129"
---
# <a name="optimize-your-linux-vm-on-azure"></a>Otimizar a VM do Linux no Azure
A criação de uma máquina virtual (VM) do Linux é fácil fazê-lo na linha de comando ou a partir do portal. Este tutorial mostra como Certifique-se de que o configurou para otimizar o desempenho na plataforma Microsoft Azure. Este tópico utiliza uma VM do Ubuntu Server, mas também pode criar a máquina virtual do Linux utilizar [suas próprias imagens como modelos](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

## <a name="prerequisites"></a>Pré-requisitos
Este tópico pressupõe que já tem uma subscrição do Azure ([inscrição na avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/)) e já aprovisionou uma VM na sua subscrição do Azure. Certifique-se de que tem a versão mais recente [CLI do Azure](/cli/azure/install-az-cli2) instalado e iniciado sessão sua subscrição do Azure com [início de sessão az](/cli/azure/reference-index) antes de [criar uma VM](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="azure-os-disk"></a>Disco do SO do Azure
Depois de criar uma VM do Linux no Azure, ela tem dois discos associados a ele. **/ desenvolvimento/sda** é o seu disco de SO **/desenvolvimento/sdb** é o seu disco temporário.  Não utilize o disco do SO principal ( **/desenvolvimento/sda**) para qualquer coisa, exceto o sistema operativo como ele está otimizado para rápido tempo de arranque da VM e não oferecer um bom desempenho para cargas de trabalho. Pretende anexar um ou mais discos para a VM para obter persistente e otimizados de armazenamento para os seus dados. 

## <a name="adding-disks-for-size-and-performance-targets"></a>Adicionar discos para destinos do tamanho e o desempenho
Com base no tamanho VM, pode anexar até 16 discos adicionais numa série a, 32 discos numa série de D e a máquina de 64 discos numa série G - cada até 1 TB de tamanho. Adicione discos Extras conforme necessário por seus requisitos de IOps e um espaço. Cada disco tem um destino de desempenho de 500 IOps para armazenamento Standard e até 5000 IOps por disco para armazenamento Premium.

Para atingir o IOps mais alto em discos de armazenamento Premium em que as definições de cache foram definidas para o **só de leitura** ou **None**, tem de desativar **barreiras** ao montagem o sistema de ficheiros no Linux. Não é necessário barreiras, uma vez que as gravações para discos de armazenamento Premium suportado são duráveis para estas definições de cache.

* Se usar **reiserFS**, utilizando a opção de montagem de barreiras de Desativação `barrier=none` (para ativar as barreiras, utilize `barrier=flush`)
* Se usar **ext3/ext4**, utilizando a opção de montagem de barreiras de Desativação `barrier=0` (para ativar as barreiras, utilize `barrier=1`)
* Se usar **XFS**, utilizando a opção de montagem de barreiras de Desativação `nobarrier` (para habilitar as barreiras, utilize a opção `barrier`)

## <a name="unmanaged-storage-account-considerations"></a>Considerações sobre contas de armazenamento não gerida
A ação de predefinição quando cria uma VM com a CLI do Azure é utilizar Managed Disks do Azure.  Estes discos são processados pela plataforma do Azure e não necessitam de qualquer preparação ou localização para armazená-las.  Discos não geridos requerem uma conta de armazenamento e têm algumas considerações de desempenho adicionais.  Para mais informações sobre discos geridos, veja [Managed Disks Overview (Descrição geral dos Managed Disks)](../windows/managed-disks-overview.md).  A secção seguinte descreve as considerações de desempenho apenas quando utilizar discos não geridos.  Novamente, a predefinição e solução de armazenamento recomendada consiste em utilizar discos geridos.

Se criar uma VM com discos não geridos, certifique-se de que anexar discos a partir de contas de armazenamento que residem na mesma região que a VM para assegurar a proximidade fechar e minimizar a latência de rede.  Cada conta de armazenamento Standard tem um máximo de 20 mil IOps e uma capacidade de tamanho de 500 TB.  Este limite funcione para aproximadamente 40 discos muito utilizados, incluindo o disco do SO e qualquer discos de dados que criar. Para contas de armazenamento Premium, não existe nenhum limite de IOps máximo, mas existe um limite de tamanho de 32 TB. 

Ao lidar com alta cargas de trabalho de IOps e tiver escolhido o armazenamento Standard para os discos, poderá ter de dividir os discos em várias contas de armazenamento para se certificar de que não atingiu o limite de IOps 20.000 para contas de armazenamento Standard. A VM pode conter uma mistura de discos de em contas de armazenamento diferentes e tipos de conta de armazenamento para atingir a sua configuração ideal.
 

## <a name="your-vm-temporary-drive"></a>A unidade temporária de VM
Por predefinição quando cria uma VM, o Azure disponibiliza um disco de SO ( **/desenvolvimento/sda**) e um disco temporário ( **/desenvolvimento/sdb**).  Todos os discos adicionais, some show como **/desenvolvimento/sdc**, **/desenvolvimento/sdd**, **/desenvolvimento/sde** e assim por diante. Todos os dados no disco temporário ( **/desenvolvimento/sdb**) não são duráveis e podem ser perdidas se eventos específicos, como redimensionamento de VM, a reimplementação, ou manutenção força um reinício da VM.  O tamanho e tipo de seu disco temporário está relacionado com o tamanho da VM que selecionou no momento da implementação. Todos o premium VMs (série DS, G e DS_V2), o disco temporário são apoiados por um SSD local para um desempenho adicional de até 48k de tamanho IOps. 

## <a name="linux-swap-file"></a>Ficheiro de troca de Linux
Se a sua VM do Azure é a partir de uma imagem de Ubuntu ou CoreOS, em seguida, pode utilizar CustomData para enviar uma configuração de cloud para o cloud-init. Se [carregada uma imagem personalizada do Linux](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) que utiliza o cloud-init, também de configurar as partições de comutação com o cloud-init.

Nas imagens do Ubuntu na Cloud, tem de utilizar o cloud-init para configurar a partição de troca. Para obter mais informações, consulte [AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions).

Para imagens sem o suporte de cloud-init, imagens VM implementadas no Azure Marketplace tem um agente do Linux de VM integrado com o sistema operacional. Este agente permite que a VM interagir com vários serviços do Azure. Partindo do princípio de que implementou uma imagem padrão do Azure Marketplace, precisaria fazer o seguinte para configurar corretamente as definições de ficheiro de troca de Linux:

Localize e modificar duas entradas no **/etc/waagent.Conf.** ficheiro. Elas controlam a existência de um ficheiro de comutação dedicado e o tamanho do ficheiro de troca. Os parâmetros que pretende para modificar são `ResourceDisk.EnableSwap=N` e `ResourceDisk.SwapSizeMB=0` 

Altere os parâmetros para as seguintes definições:

* ResourceDisk.EnableSwap=Y
* ResourceDisk.SwapSizeMB={size em MB para satisfazer as necessidades} 

Depois de efetuar a alteração, terá de reiniciar o waagent ou reinicie a VM do Linux para refletir essas alterações.  Sabe, as alterações foram implementadas e um ficheiro de comutação foi criado quando utiliza o `free` comando para ver o espaço livre. O exemplo seguinte tem um ficheiro de troca de 512MB criado como resultado de modificar os **waagent.conf** ficheiro:

```bash
azuseruser@myVM:~$ free
            total       used       free     shared    buffers     cached
Mem:       3525156     804168    2720988        408       8428     633192
-/+ buffers/cache:     162548    3362608
Swap:       524284          0     524284
```

## <a name="io-scheduling-algorithm-for-premium-storage"></a>Algoritmo de agendamento de e/s para o armazenamento Premium
Com o 2.6.18 kernel do Linux, o padrão e/s de algoritmo de agendamento foi alterado de dentro do prazo para CFQ (algoritmo de colocação em fila completamente justo). Para padrões de e/s de acesso aleatório, há negligenciável diferença nas diferenças de desempenho entre CFQ e dentro do prazo.  Para discos baseado em SSD em que o padrão de e/s de disco é predominantemente sequencial, voltando para o algoritmo NOOP ou prazo pode obter um melhor desempenho de e/s.

### <a name="view-the-current-io-scheduler"></a>Ver o agendador de e/s atual
Utilize o seguinte comando:  

```bash
cat /sys/block/sda/queue/scheduler
```

Verá a seguinte saída, que indica o programador atual.  

```bash
noop [deadline] cfq
```

### <a name="change-the-current-device-devsda-of-io-scheduling-algorithm"></a>Alterar o dispositivo atual (/ desenvolvimento/sda) de e/s de algoritmo de agendamento
Utilize os seguintes comandos:  

```bash
azureuser@myVM:~$ sudo su -
root@myVM:~# echo "noop" >/sys/block/sda/queue/scheduler
root@myVM:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
root@myVM:~# update-grub
```

> [!NOTE]
> Aplicar esta definição para **/desenvolvimento/sda** sozinha não é útil. Definir todos os discos de dados onde a e/s sequenciais domina o padrão de e/s.  

Deverá ver o resultado seguinte, indicando que **grub.cfg** foi reconstruída com êxito e que o agendador padrão foi atualizado para NOOP.  

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

Para a família de distribuição do Red Hat, precisa apenas o seguinte comando:   

```bash
echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local
```

## <a name="using-software-raid-to-achieve-higher-iops"></a>Utilizando o RAID de Software para alcançar maior / Ops
Se as cargas de trabalho exigem mais IOps, que pode fornecer um único disco, terá de utilizar uma configuração de RAID de software de vários discos. Uma vez que já o Azure efetua resiliência de disco na camada de recursos de infraestrutura local, atingir o nível mais elevado de desempenho de uma configuração de repartição de RAID-0.  Aprovisione e criar discos no ambiente Azure e anexe-os a sua VM do Linux antes de criação de partições, formatação e as unidades de montagem.  Mais detalhes sobre como configurar um programa de configuração do RAID de software na sua VM do Linux no azure podem ser encontrados no **[configurar o RAID de Software no Linux](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)** documento.

## <a name="next-steps"></a>Próximos Passos
Lembre-se, como com todas as discussões de otimização, tiver de realizar testes antes e após cada alteração para medir o impacto da alteração.  Otimização é um processo passo a passo com resultados diferentes em diferentes computadores no seu ambiente.  O que funciona para uma configuração pode não funcionar para outras pessoas.

Alguns links úteis para recursos adicionais:

* [Guia de utilizador do agente Linux do Azure](../extensions/agent-linux.md)
* [Otimizar o desempenho do MySQL em VMs do Linux do Azure](classic/optimize-mysql.md)
* [Configurar o RAID de Software no Linux](configure-raid.md)
