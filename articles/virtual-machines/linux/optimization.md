---
title: Otimizar a VM do Linux no Azure
description: Conheça algumas dicas de otimização para garantir que você configurou sua VM Linux para obter um desempenho ideal no Azure
keywords: máquina virtual Linux, máquina virtual Linux, máquina virtual Ubuntu
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
ms.topic: article
ms.date: 09/06/2016
ms.author: rclaus
ms.subservice: disks
ms.openlocfilehash: ea0d284b8220e4f8bc7bc1b91684654b32da7065
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035378"
---
# <a name="optimize-your-linux-vm-on-azure"></a>Otimizar a VM do Linux no Azure
Criar uma VM (máquina virtual) do Linux é fácil de fazer na linha de comando ou no Portal. Este tutorial mostra como garantir que você o configurou para otimizar seu desempenho na plataforma Microsoft Azure. Este tópico usa uma VM do servidor Ubuntu, mas você também pode criar uma máquina virtual Linux usando [suas próprias imagens como modelos](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

## <a name="prerequisites"></a>Pré-requisitos
Este tópico pressupõe que você já tenha uma assinatura do Azure em funcionamento ([inscrição de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/)) e já tenha PROVISIONADO uma VM em sua assinatura do Azure. Verifique se você tem as [CLI do Azure](/cli/azure/install-az-cli2) mais recentes instaladas e conectadas à sua assinatura do Azure com [AZ login](/cli/azure/reference-index) antes [de criar uma VM](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="azure-os-disk"></a>Disco do sistema operacional do Azure
Depois de criar uma VM do Linux no Azure, ela tem dois discos associados a ela. **/dev/sda** é o disco do sistema operacional, **/dev/sdb** é o disco temporário.  Não use o disco do sistema operacional principal ( **/dev/sda**) para qualquer coisa, exceto o sistema operacional, pois ele é otimizado para o tempo de inicialização rápido da VM e não fornece um bom desempenho para suas cargas de trabalho. Você deseja anexar um ou mais discos à sua VM para obter armazenamento persistente e otimizado para seus dados. 

## <a name="adding-disks-for-size-and-performance-targets"></a>Adicionando discos para metas de desempenho e tamanho
Com base no tamanho da VM, você pode anexar até 16 discos adicionais em uma série A, 32 discos em uma série D e 64 discos em uma máquina da série G, cada um com até 1 TB de tamanho. Você adiciona discos extras conforme necessário, de acordo com seus requisitos de espaço e IOps. Cada disco tem um destino de desempenho de 500 IOps para armazenamento Standard e até 5000 IOps por disco para armazenamento Premium.

Para obter o IOps mais alto em discos de armazenamento Premium em que suas configurações de cache foram definidas como **ReadOnly** ou **None**, você deve desabilitar as **barreiras** ao montar o sistema de arquivos no Linux. Você não precisa de barreiras porque as gravações para os discos de backup do armazenamento Premium são duráveis para essas configurações de cache.

* Se você usar o **reiserFS**, desabilite as barreiras usando a opção de montagem `barrier=none` (para habilitar as barreiras, use `barrier=flush`)
* Se você usar **ext3/ext4**, desabilite as barreiras usando a opção de montagem `barrier=0` (para habilitar as barreiras, use `barrier=1`)
* Se você usar o **xfs**, desabilite as barreiras usando a opção de montagem `nobarrier` (para habilitar as barreiras, use a opção `barrier`)

## <a name="unmanaged-storage-account-considerations"></a>Considerações sobre a conta de armazenamento não gerenciado
A ação padrão quando você cria uma VM com o CLI do Azure é usar o Managed Disks do Azure.  Esses discos são tratados pela plataforma do Azure e não exigem nenhuma preparação ou local para armazená-los.  Discos não gerenciados exigem uma conta de armazenamento e têm algumas considerações de desempenho adicionais.  Para mais informações sobre discos geridos, veja [Managed Disks Overview (Descrição geral dos Managed Disks)](../windows/managed-disks-overview.md).  A seção a seguir descreve as considerações de desempenho somente quando você usa discos não gerenciados.  Novamente, a solução de armazenamento padrão e recomendada é usar discos gerenciados.

Se você criar uma VM com discos não gerenciados, certifique-se de anexar discos de contas de armazenamento que residem na mesma região da VM para garantir o fechamento da proximidade e minimizar a latência de rede.  Cada conta de armazenamento Standard tem um máximo de IOps de 20 mil e uma capacidade de tamanho de 500 TB.  Esse limite funciona para aproximadamente 40 discos de uso intensivo, incluindo o disco do sistema operacional e os discos de dados que você criar. Para contas de armazenamento Premium, não há nenhum limite máximo de IOps, mas há um limite de tamanho de 32 TB. 

Ao lidar com cargas de trabalho de IOps alta e você escolheu o armazenamento padrão para seus discos, talvez seja necessário dividir os discos em várias contas de armazenamento para verificar se você não atingiu o limite de 20.000 IOps para contas de armazenamento padrão. Sua VM pode conter uma combinação de discos entre diferentes contas de armazenamento e tipos de conta de armazenamento para obter a configuração ideal.
 

## <a name="your-vm-temporary-drive"></a>Sua unidade temporária da VM
Por padrão, quando você cria uma VM, o Azure fornece um disco do sistema operacional ( **/dev/sda**) e um disco temporário ( **/dev/sdb**).  Todos os discos adicionais que você adicionar aparecerão como **/dev/sdc**, **/dev/sdd**, **/dev/SDE** e assim por diante. Todos os dados no disco temporário ( **/dev/sdb**) não são duráveis e podem ser perdidos se eventos específicos, como redimensionamento de VM, reimplantação ou manutenção, forçarem uma reinicialização da VM.  O tamanho e o tipo do disco temporário estão relacionados ao tamanho da VM escolhido no momento da implantação. Todas as VMs de tamanho Premium (série DS, G e DS_V2) a unidade temporária são apoiadas por um SSD local para desempenho adicional de até 48K IOps. 

## <a name="linux-swap-partition"></a>Partição de permuta do Linux
Se sua VM do Azure for de uma imagem Ubuntu ou CoreOS, você poderá usar o CustomData para enviar uma configuração de nuvem para Cloud-init. Se você [carregou uma imagem personalizada do Linux](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) que usa Cloud-init, também configura as partições de permuta usando Cloud-init.

Em imagens de nuvem do Ubuntu, você deve usar Cloud-init para configurar a partição de permuta. Para obter mais informações, consulte [AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions).

Para imagens sem suporte a Cloud-init, as imagens de VM implantadas do Azure Marketplace têm um agente Linux de VM integrado com o sistema operacional. Esse agente permite que a VM interaja com vários serviços do Azure. Supondo que você tenha implantado uma imagem padrão do Azure Marketplace, você precisaria fazer o seguinte para configurar corretamente suas configurações de arquivo de permuta do Linux:

Localize e modifique duas entradas no arquivo **/etc/waagent.conf** . Eles controlam a existência de um arquivo de permuta dedicado e o tamanho do arquivo de permuta. Os parâmetros que você precisa verificar são `ResourceDisk.EnableSwap` e `ResourceDisk.SwapSizeMB` 

Para habilitar um disco habilitado corretamente e o arquivo de permuta montado, verifique se os parâmetros têm as seguintes configurações:

* ResourceDisk.EnableSwap=Y
* ResourceDisk. SwapSizeMB = {tamanho em MB para atender às suas necessidades} 

Depois de fazer a alteração, você precisará reiniciar o waagent ou reiniciar a VM do Linux para refletir essas alterações.  Você sabe que as alterações foram implementadas e um arquivo de permuta foi criado quando você usa o comando `free` para exibir o espaço livre. O exemplo a seguir tem um arquivo de permuta de 512 MB criado como resultado da modificação do arquivo **waagent. conf** :

```bash
azuseruser@myVM:~$ free
            total       used       free     shared    buffers     cached
Mem:       3525156     804168    2720988        408       8428     633192
-/+ buffers/cache:     162548    3362608
Swap:       524284          0     524284
```

## <a name="io-scheduling-algorithm-for-premium-storage"></a>Algoritmo de agendamento de e/s para armazenamento Premium
Com o kernel do Linux 2.6.18, o algoritmo de agendamento de e/s padrão foi alterado de prazo para CFQ (algoritmo de enfileiramento totalmente justo). Para padrões de e/s de acesso aleatório, há uma diferença insignificante nas diferenças de desempenho entre CFQ e prazo.  Para discos baseados em SSD em que o padrão de e/s de disco é predominantemente sequencial, alternar de volta para o algoritmo NOOP ou prazo pode atingir um melhor desempenho de e/s.

### <a name="view-the-current-io-scheduler"></a>Exibir o Agendador de e/s atual
Utilize o seguinte comando:  

```bash
cat /sys/block/sda/queue/scheduler
```

Você verá a saída a seguir, que indica o Agendador atual.  

```bash
noop [deadline] cfq
```

### <a name="change-the-current-device-devsda-of-io-scheduling-algorithm"></a>Alterar o dispositivo atual (/dev/sda) do algoritmo de agendamento de e/s
Use os seguintes comandos:  

```bash
azureuser@myVM:~$ sudo su -
root@myVM:~# echo "noop" >/sys/block/sda/queue/scheduler
root@myVM:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
root@myVM:~# update-grub
```

> [!NOTE]
> Aplicar essa configuração para **/dev/sda** sozinho não é útil. Definido em todos os discos de dados em que a e/s sequencial domina o padrão de e/s.  

Você deve ver a saída a seguir, indicando que o **grub. cfg** foi recriado com êxito e que o agendador padrão foi atualizado para NOOP.  

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

Para a família de distribuição do Red Hat, você só precisa do seguinte comando:   

```bash
echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local
```

## <a name="using-software-raid-to-achieve-higher-iops"></a>Usando o RAID de software para obter e/Ops mais altas
Se suas cargas de trabalho exigirem mais IOps do que um único disco pode fornecer, você precisará usar uma configuração de RAID de software de vários discos. Como o Azure já executa a resiliência de disco na camada de malha local, você obtém o nível mais alto de desempenho de uma configuração de distribuição RAID-0.  Provisione e crie discos no ambiente do Azure e anexe-os à sua VM do Linux antes de particionar, Formatar e montar as unidades.  Mais detalhes sobre como configurar uma instalação de RAID de software em sua VM do Linux no Azure podem ser encontrados no documento **[Configurando o RAID de software no Linux](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)** .

Como alternativa a uma configuração de RAID tradicional, você também pode optar por instalar o LVM (Gerenciador de volumes lógicos) para configurar um número de discos físicos em um único volume de armazenamento lógico distribuído. Nessa configuração, leituras e gravações são distribuídas para vários discos contidos no grupo de volumes (semelhante a RAID0). Por motivos de desempenho, é provável que você queira distribuir seus volumes lógicos para que as leituras e gravações utilizem todos os discos de dados anexados.  Mais detalhes sobre como configurar um volume lógico distribuído em sua VM do Linux no Azure podem ser encontrados no documento **[Configurar o LVM em uma VM do Linux no Azure](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)** .

## <a name="next-steps"></a>Passos Seguintes
Lembre-se, assim como em todas as discussões sobre otimização, você precisa executar testes antes e depois de cada alteração para medir o impacto que a alteração tem.  A otimização é um processo passo a passo que tem resultados diferentes em máquinas diferentes em seu ambiente.  O que funciona para uma configuração pode não funcionar para outros.

Alguns links úteis para recursos adicionais:

* [Guia do usuário do agente Linux do Azure](../extensions/agent-linux.md)
* [Otimizando o desempenho do MySQL em VMs Linux do Azure](classic/optimize-mysql.md)
* [Configurar o RAID de software no Linux](configure-raid.md)
