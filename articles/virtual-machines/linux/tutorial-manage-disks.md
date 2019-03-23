---
title: Tutorial – Gerir discos do Azure com a CLI do Azure | Microsoft Docs
description: Neste tutorial, vai aprender a utilizar a CLI do Azure para criar e gerir discos do Azure para máquinas virtuais
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/14/2018
ms.author: cynthn
ms.custom: mvc
ms.subservice: disks
ms.openlocfilehash: da70b77edeb483cae0e74400e739f018f78d0993
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370810"
---
# <a name="tutorial---manage-azure-disks-with-the-azure-cli"></a>Tutorial - Gerir discos do Azure com a CLI do Azure

As máquinas virtuais (VMs) do Azure utilizam discos para armazenar o sistema operativo, as aplicações e os dados. Quando cria uma VM, é importante escolher um tamanho de disco e a configuração adequados para a carga de trabalho esperada. Este tutorial mostra como implementar e gerir discos de VM. Vai obter informações sobre:

> [!div class="checklist"]
> * Discos de SO e discos temporários
> * Discos de dados
> * Discos Standard e Premium
> * Desempenho do disco
> * Expor e preparar os discos de dados
> * Redimensionar discos
> * Instantâneos de disco


## <a name="default-azure-disks"></a>Discos do Azure predefinidos

Quando uma máquina virtual do Azure é criada, dois discos são automaticamente expostos à máquina virtual.

**Disco do sistema operativo** – os discos do sistema operativo podem ter o tamanho máximo de 2 TB e alojam o sistema operativo das VMs. O disco do SO está identificado como */dev/sda* por predefinição. A configuração da colocação em cache do disco do SO está otimizada para desempenho do SO. Devido a esta configuração, o disco do SO **não deve** ser utilizado para aplicações nem para dados. Para aplicações e dados, utilize discos de dados, que são descritos posteriormente neste tutorial.

**Disco temporário** - os discos temporários utilizam uma unidade de estado sólido que está localizada no mesmo anfitrião da VM do Azure. Os discos temporários são de elevado desempenho e servem para operações como o processamento de dados temporários. No entanto, se a VM for movida para um novo anfitrião, todos os dados armazenados num disco temporário são removidos. O tamanho do disco temporário é determinado pelo tamanho da VM. Os discos temporários estão identificados como */dev/sdb* e têm um ponto de montagem de */mnt*.


## <a name="azure-data-disks"></a>Discos de dados do Azure

Para instalar aplicações e armazenar dados, podem ser adicionados mais discos de dados. Os discos de dados devem ser utilizados em qualquer situação em que se pretenda armazenamento de dados duradouro e reativo. Cada disco de dados tem a capacidade máxima de 4 TB. O tamanho da máquina virtual determina quantos discos de dados podem ser expostos a uma VM. Para cada vCPU de VM, podem ser expostos quatro discos de dados.


## <a name="vm-disk-types"></a>Tipos de disco de VM

O Azure fornece dois tipos de discos, standard e Premium.

### <a name="standard-disk"></a>Disco Standard

O Armazenamento Standard está protegido por HDDs e fornece armazenamento económico, mantendo o desempenho. Os discos Standard são ideais para uma carga de trabalho de desenvolvimento e teste económica.

### <a name="premium-disk"></a>Disco Premium

Os discos Premium são apoiados por um disco de elevado desempenho baseado em SSD e de baixa latência. São perfeitos para as VMs com carga de trabalho de produção. O Armazenamento Premium suporta VMs da série DS, série DSv2, série GS e série FS. Quando selecionar um tamanho de disco, o valor é arredondado para o tipo seguinte. Por exemplo, se o tamanho do disco for inferior a 128 GB, o tipo de disco é P10. Se o tamanho do disco estiver entre 129 GB e 512 GB, o tamanho é P20. Acima de 512 GB, o tamanho é P30.

### <a name="premium-disk-performance"></a>Desempenho do disco Premium
[!INCLUDE [disk-storage-premium-ssd-sizes](../../../includes/disk-storage-premium-ssd-sizes.md)]

Enquanto a tabela acima identifica o IOPS máximo por disco, um nível mais elevado de desempenho pode ser alcançado ao repartir vários discos de dados. Por exemplo, uma VM Standard_GS5 pode atingir o máximo de 80 000 IOPS. Para obter informações detalhadas sobre o IOPS máximo por VM, consulte [Tamanhos de VM do Linux](sizes.md).

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta.

Para abrir o Cloud Shell, selecione **experimente** do canto superior direito de um bloco de código. Também pode iniciar o Cloud Shell num separador do browser separado ao aceder a [https://shell.azure.com/powershell](https://shell.azure.com/bash). Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e prima Enter para executá-lo.

## <a name="create-and-attach-disks"></a>Criar e anexar discos

Os discos de dados podem ser criados e expostos no momento de criação da VM ou para uma VM existente.

### <a name="attach-disk-at-vm-creation"></a>Expor o disco durante a criação de VM

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az-group-create).

```azurecli-interactive
az group create --name myResourceGroupDisk --location eastus
```

Crie uma VM com o comando [z vm create](/cli/azure/vm#az-vm-create). O seguinte exemplo cria uma VM com o nome *myVM*, adiciona uma conta de utilizador com o nome *azureuser* e gera chaves SSH, caso não existam. O argumento `--datadisk-sizes-gb` é utilizado para especificar que um disco adicional deve ser criado e exposto à máquina virtual. Para criar e expor mais do que um disco, utilize uma lista delimitada por espaços dos valores de tamanho de disco. No exemplo seguinte, é criada uma VM com dois discos de dados, ambos de 128 GB. Porque os tamanhos de disco são de 128 GB, estes discos são configurados como P10s, que fornecem o máximo de 500 IOPS por disco.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupDisk \
  --name myVM \
  --image UbuntuLTS \
  --size Standard_DS2_v2 \
  --generate-ssh-keys \
  --data-disk-sizes-gb 128 128
```

### <a name="attach-disk-to-existing-vm"></a>Expor o disco a uma VM existente

Para criar e expor um disco novo a uma máquina virtual existente, utilize o comando [az vm disk attach](/cli/azure/vm/disk#az-vm-disk-attach). O exemplo seguinte cria um disco premium, de 128 gigabytes de tamanho, e expõe-o à VM criada no último passo.

```azurecli-interactive
az vm disk attach \
    --resource-group myResourceGroupDisk \
    --vm-name myVM \
    --disk myDataDisk \
    --size-gb 128 \
    --sku Premium_LRS \
    --new
```

## <a name="prepare-data-disks"></a>Preparar dados de discos

Depois de um disco ser exposto à máquina virtual, o sistema operativo tem de ser configurado para utilizar o disco. O exemplo seguinte mostra como configurar manualmente um disco. Este processo também pode ser automatizado através da inicialização da cloud, que é abordada num [tutorial posterior](./tutorial-automate-vm-deployment.md).


Crie uma ligação SSH com a máquina virtual. Substitua o endereço IP de exemplo pelo IP público da máquina virtual.

```azurecli-interactive
ssh 10.101.10.10
```

Particione o disco com `fdisk`.

```bash
(echo n; echo p; echo 1; echo ; echo ; echo w) | sudo fdisk /dev/sdc
```

Escreva um sistema de ficheiros na partição com o comando `mkfs`.

```bash
sudo mkfs -t ext4 /dev/sdc1
```

Monte o disco novo para que fique acessível no sistema operativo.

```bash
sudo mkdir /datadrive && sudo mount /dev/sdc1 /datadrive
```

O disco pode agora ser acedido através do ponto de montagem *datadrive*, que pode ser verificado com o comando `df -h`.

```bash
df -h
```

A saída mostra o novo disco montado em */datadrive*.

```bash
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        30G  1.4G   28G   5% /
/dev/sdb1       6.8G   16M  6.4G   1% /mnt
/dev/sdc1        50G   52M   47G   1% /datadrive
```

Para garantir que a unidade é remontada após uma reinicialização, tem de ser adicionada ao ficheiro *etc/fstab*. Para tal, obtenha o UUID do disco com o utilitário `blkid`.

```bash
sudo -i blkid
```

A saída apresenta o UUID da unidade, `/dev/sdc1` neste caso.

```bash
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

Adicione uma linha semelhante à seguinte ao ficheiro *etc/fstab*.

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive  ext4    defaults,nofail   1  2
```

Agora que o disco foi configurado, feche a sessão SSH.

```bash
exit
```

## <a name="take-a-disk-snapshot"></a>Tirar um instantâneo do disco

Quando tira um instantâneo de disco, o Azure cria uma cópia só de leitura de um ponto no tempo do disco. Os instantâneos de VM do Azure são úteis para guardar rapidamente o estado de uma VM antes de efetuar alterações de configuração. Em caso de problema ou erro, VM pode ser restaurada utilizando um instantâneo. Quando uma VM tiver mais do que um disco, é tirado um instantâneo de cada disco independentemente dos outros. Para fazer cópias de segurança da aplicação, considere parar a VM antes de tirar instantâneos do disco. Em alternativa, utilize o [Serviço Azure Backup](/azure/backup/), que permite efetuar cópias de segurança automatizadas enquanto a VM está em execução.

### <a name="create-snapshot"></a>Criar instantâneo

Antes de criar um instantâneo do disco da máquina virtual, é necessário o ID ou o nome do disco. Utilize o comando [az vm show](/cli/azure/vm#az-vm-show) para obter o ID do disco. Neste exemplo, o ID do disco é armazenado numa variável, para que possa ser utilizado num passo posterior.

```azurecli-interactive
osdiskid=$(az vm show \
   -g myResourceGroupDisk \
   -n myVM \
   --query "storageProfile.osDisk.managedDisk.id" \
   -o tsv)
```

Agora que tem o ID do disco da máquina virtual, o comando seguinte cria um instantâneo do disco.

```azurecli-interactive
az snapshot create \
    --resource-group myResourceGroupDisk \
    --source "$osdiskid" \
    --name osDisk-backup
```

### <a name="create-disk-from-snapshot"></a>Criar disco a partir de instantâneo

Este instantâneo pode então ser convertido num disco, que pode ser utilizado para recriar a máquina virtual.

```azurecli-interactive
az disk create \
   --resource-group myResourceGroupDisk \
   --name mySnapshotDisk \
   --source osDisk-backup
```

### <a name="restore-virtual-machine-from-snapshot"></a>Restaurar a máquina virtual a partir de instantâneo

Para demonstrar a recuperação da máquina virtual, elimine a máquina virtual existente.

```azurecli-interactive
az vm delete \
--resource-group myResourceGroupDisk \
--name myVM
```

Crie uma máquina virtual nova a partir do disco do instantâneo.

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupDisk \
    --name myVM \
    --attach-os-disk mySnapshotDisk \
    --os-type linux
```

### <a name="reattach-data-disk"></a>Voltar a expor disco de dados

Todos os discos de dados têm de ser novamente expostos à máquina virtual.

Em primeiro lugar, localize o nome do disco de dados com o comando [az disk list](/cli/azure/disk#az-disk-list). Este exemplo coloca o nome do disco numa variável designada *datadisk*, que é utilizada no próximo passo.

```azurecli-interactive
datadisk=$(az disk list \
   -g myResourceGroupDisk \
   --query "[?contains(name,'myVM')].[id]" \
   -o tsv)
```

Utilize o comando [az vm disk attach](/cli/azure/vm/disk#az-vm-disk-attach) para expor o disco.

```azurecli-interactive
az vm disk attach \
   –g myResourceGroupDisk \
   --vm-name myVM \
   --disk $datadisk
```

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu sobre os tópicos de discos de VM, como:

> [!div class="checklist"]
> * Discos de SO e discos temporários
> * Discos de dados
> * Discos Standard e Premium
> * Desempenho do disco
> * Expor e preparar os discos de dados
> * Redimensionar discos
> * Instantâneos de disco

Avance para o próximo tutorial para saber mais sobre como automatizar a configuração da VM.

> [!div class="nextstepaction"]
> [Automatizar a configuração da VM](./tutorial-automate-vm-deployment.md)
