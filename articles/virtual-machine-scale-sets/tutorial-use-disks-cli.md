---
title: Tutorial - Criar e utilizar discos para conjuntos de escala com Azure CLI
description: Saiba como utilizar a CLI do Azure para criar e utilizar Managed Disks com conjuntos de dimensionamento de máquinas virtuais, incluindo como adicionar, preparar, listar e desanexar discos.
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 03/27/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: adaa7d1c2cf4a78a680ef4fbbec06975ceda812b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96433496"
---
# <a name="tutorial-create-and-use-disks-with-virtual-machine-scale-set-with-the-azure-cli"></a>Tutorial: Criar e utilizar discos com um conjunto de dimensionamento de máquinas virtuais com a CLI do Azure
Os conjuntos de dimensionamento de máquinas virtuais utilizam discos para armazenar o sistema operativo, as aplicações e os dados da instância de VM. Ao criar e gerir um conjunto de dimensionamento, é importante escolher um tamanho de disco e a configuração adequados para a carga de trabalho esperada. Este tutorial abrange como criar e gerir discos de VM. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Discos de SO e discos temporários
> * Discos de dados
> * Discos Standard e Premium
> * Desempenho do disco
> * Anexar e preparar discos de dados

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo requer a versão 2.0.29 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="default-azure-disks"></a>Discos do Azure predefinidos
Quando um conjunto de dimensionamento é criado ou dimensionado, são anexados automaticamente dois discos a cada instância de VM.

**Disco do sistema operativo** – os discos do sistema operativo podem ter o tamanho máximo de 2 TB e alojam o sistema operativo da instância de VM. O disco do SO está identificado como */dev/sda* por predefinição. A configuração da colocação em cache do disco do SO está otimizada para desempenho do SO. Devido a esta configuração, o disco do SO **não deve** alojar aplicações nem dados. Para aplicações e dados, utilize discos de dados, que são descritos posteriormente neste artigo.

**Disco temporário** – os discos temporários utilizam uma unidade de estado sólido que está localizada no mesmo anfitrião do Azure da instância de VM. São discos de elevado desempenho e podem ser utilizados para operações como o processamento de dados temporários. No entanto, se a instância de VM for movida para um novo anfitrião, todos os dados armazenados num disco temporário são removidos. O tamanho do disco temporário é determinado pelo tamanho da instância de VM. Os discos temporários estão identificados como */dev/sdb* e têm um ponto de montagem de */mnt*.

### <a name="temporary-disk-sizes"></a>Tamanhos dos discos temporários
| Tipo | Tamanhos comuns | Tamanho máximo de disco temporário (GiB) |
|----|----|----|
| [Fins gerais](../virtual-machines/sizes-general.md) | Séries A, B e D | 1600 |
| [Com otimização de computação](../virtual-machines/sizes-compute.md) | Série F | 576 |
| [Com otimização de memória](../virtual-machines/sizes-memory.md) | Séries D, E, G e M | 6144 |
| [Com otimização de armazenamento](../virtual-machines/sizes-storage.md) | Série L | 5630 |
| [GPU](../virtual-machines/sizes-gpu.md) | Série N | 1440 |
| [Elevado desempenho](../virtual-machines/sizes-hpc.md) | Séries A e H | 2000 |


## <a name="azure-data-disks"></a>Discos de dados do Azure
Podem ser adicionados mais discos de dados se precisar de instalar aplicações e armazenar dados. Os discos de dados devem ser utilizados em qualquer situação em que se pretenda armazenamento de dados durável e reativo. Cada disco de dados tem a capacidade máxima de 4 TB. O tamanho da instância de VM determina quantos discos de dados podem ser anexados. Para cada VM vCPU, dois discos de dados podem ser ligados até um máximo absoluto de 64 discos por máquina virtual.

## <a name="vm-disk-types"></a>Tipos de disco de VM
O Azure oferece dois tipos de disco.

### <a name="standard-disk"></a>Disco Standard
O Armazenamento Standard está protegido por HDDs e fornece armazenamento e desempenho económicos. Os discos Standard são ideais para uma carga de trabalho de desenvolvimento e teste económica.

### <a name="premium-disk"></a>Disco Premium
Os discos Premium são apoiados por um disco de elevado desempenho baseado em SSD e de baixa latência. Estes discos são recomendados para VMs que executam cargas de trabalho de produção. O Armazenamento Premium suporta VMs da série DS, série DSv2, série GS e série FS. Quando seleciona um tamanho de disco, o valor é arredondado para o tipo seguinte. Por exemplo, se o tamanho do disco for inferior a 128 GB, o tipo de disco é P10. Se o tamanho do disco estiver entre 129 GB e 512 GB, o tamanho é P20. Acima de 512 GB, o tamanho é P30.

### <a name="premium-disk-performance"></a>Desempenho do disco Premium
|Tipo de disco de armazenamento Premium | P4 | P6 | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Tamanho do disco (arredondado) | 32 GB | 64 GB | 128 GB | 512 GB | 1024 GB (1 TB) | 2.048 GB (2 TB) | 4.095 GB (4 TB) |
| IOPs Máx por disco | 120 | 240 | 500 | 2300 | 5000 | 7.500 | 7.500 |
Débito por disco | 25 MB/s | 50 MB/s | 100 MB/s | 150 MB/s | 200 MB/s | 250 MB/s | 250 MB/s |

Enquanto a tabela acima identifica o IOPS máximo por disco, um nível mais elevado de desempenho pode ser alcançado ao repartir vários discos de dados. Por exemplo, uma VM Standard_GS5 pode atingir o máximo de 80 000 IOPS. Para obter informações detalhadas sobre o IOPS máximo por VM, consulte [Tamanhos de VM do Linux](../virtual-machines/sizes.md).


## <a name="create-and-attach-disks"></a>Criar e expor discos
Pode criar e anexar discos quando cria um conjunto de dimensionamento ou com um conjunto de dimensionamento existente.

A partir da versão `2019-07-01` API, pode definir o tamanho do disco DE numa escala de máquina virtual definida com a propriedade [storageProfile.osDisk.diskSizeGb.](/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosdisk) Após o provisionamento, poderá ter de expandir ou repartir o disco para utilizar todo o espaço. Saiba mais sobre [a expansão do disco aqui.](../virtual-machines/windows/expand-os-disk.md#expand-the-volume-within-the-os)

### <a name="attach-disks-at-scale-set-creation"></a>Anexar discos durante a criação do conjunto de dimensionamento
Primeiro, crie um grupo de recursos com o comando [az group create](/cli/azure/group). Neste exemplo, um grupo de recursos chamado *myResourceGroup* é criado na região *leste.*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Crie um conjunto de dimensionamento de máquinas virtuais com o comando [az vmss create](/cli/azure/vmss). O exemplo a seguir cria um conjunto de escala chamado *myScaleSet*, e gera teclas SSH se não existirem. São criados dois discos com o parâmetro `--data-disk-sizes-gb`. O primeiro disco tem *64* GB de tamanho e o segundo disco tem *128* GB:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy automatic \
  --admin-username azureuser \
  --generate-ssh-keys \
  --data-disk-sizes-gb 64 128
```

A criação e configuração de todas as instâncias de VM e recursos do conjunto de dimensionamento demora alguns minutos.

### <a name="attach-a-disk-to-existing-scale-set"></a>Anexar um disco a um conjunto de dimensionamento existente
Também pode anexar discos a um conjunto de dimensionamento existente. Utilize o conjunto de dimensionamento criado no passo anterior para adicionar outro disco com [az vmss disk attach](/cli/azure/vmss/disk). O exemplo seguinte anexa um disco de *128* GB adicional:

```azurecli-interactive
az vmss disk attach \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --size-gb 128
```


## <a name="prepare-the-data-disks"></a>Preparar os discos de dados
Os discos criados e anexados às instâncias de VM do conjunto de dimensionamento são discos não processados. Antes de poder utilizá-los com os seus dados e aplicações, os discos têm de ser preparados. Para preparar os discos, crie uma partição e um sistema de ficheiros, e monte-os.

Para automatizar o processo em múltiplas instâncias de VM num conjunto de dimensionamento, pode utilizar a Extensão de Script Personalizado do Azure. Esta extensão pode executar scripts localmente em cada instância de VM, como preparar discos de dados anexados. Para obter mais informações, veja a [Descrição geral da Extensão de Script Personalizado](../virtual-machines/extensions/custom-script-linux.md).

O exemplo seguinte executa um script a partir de um repositório de exemplo do GitHub em cada instância de VM com [az vmss extension set](/cli/azure/vmss/extension) que prepara todos os discos de dados não processados anexados:

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"],"commandToExecute":"./prepare_vm_disks.sh"}'
```

Para confirmar que os discos foram preparados corretamente, utilize o SSH numa das instâncias de VM. Liste as informações de ligação do seu conjunto de dimensionamento com [az vmss list-instance-connection-info](/cli/azure/vmss):

```azurecli-interactive
az vmss list-instance-connection-info \
  --resource-group myResourceGroup \
  --name myScaleSet
```

Utilize o seu Endereço IP público e o número de porta para ligar à primeira instância de VM, conforme mostrado no exemplo seguinte:

```console
ssh azureuser@52.226.67.166 -p 50001
```

Examine as partições na instância de VM da seguinte forma:

```bash
sudo fdisk -l
```

O resultado de exemplo seguinte mostra que três discos estão anexados à instância de VM - */dev/sdc*, */dev/sdd* e */dev/sde*. Cada um destes discos tem uma única partição que utiliza todo o espaço disponível:

```bash
Disk /dev/sdc: 64 GiB, 68719476736 bytes, 134217728 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xa47874cb

Device     Boot Start       End   Sectors Size Id Type
/dev/sdc1        2048 134217727 134215680  64G 83 Linux

Disk /dev/sdd: 128 GiB, 137438953472 bytes, 268435456 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xd5c95ca0

Device     Boot Start       End   Sectors  Size Id Type
/dev/sdd1        2048 268435455 268433408  128G 83 Linux

Disk /dev/sde: 128 GiB, 137438953472 bytes, 268435456 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x9312fdf5

Device     Boot Start       End   Sectors  Size Id Type
/dev/sde1        2048 268435455 268433408  128G 83 Linux
```

Examine os sistemas de ficheiros e os pontos de montagem na instância de VM da seguinte forma:

```bash
sudo df -h
```

O exemplo de saída seguinte mostra que os três discos têm os respetivos sistemas de ficheiros instalados corretamente, montados em */datadisks*:

```output
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        30G  1.3G   28G   5% /
/dev/sdb1        50G   52M   47G   1% /mnt
/dev/sdc1        63G   52M   60G   1% /datadisks/disk1
/dev/sdd1       126G   60M  120G   1% /datadisks/disk2
/dev/sde1       126G   60M  120G   1% /datadisks/disk3
```

Os discos em cada instância de VM no seu dimensionamento são preparados automaticamente da mesma forma. À medida que o conjunto de dimensionamento aumenta verticalmente, são anexados os discos de dados necessários às novas instâncias de VM. A Extensão de Script Personalizado também é executada para preparar automaticamente os discos.

Feche a ligação SSH à instância de VM:

```bash
exit
```


## <a name="list-attached-disks"></a>Listar os discos anexados
Para ver informações sobre os discos anexados a um conjunto de dimensionamento, utilize [az vmss show](/cli/azure/vmss) e consulte em *virtualMachineProfile.storageProfile.dataDisks*:

```azurecli-interactive
az vmss show \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --query virtualMachineProfile.storageProfile.dataDisks
```

São apresentadas informações sobre o tamanho do disco, a camada de armazenamento e o LUN (Número de Unidade Lógica). O resultado de exemplo seguinte detalha os três discos de dados anexados ao conjunto de dimensionamento:

```json
[
  {
    "additionalProperties": {},
    "caching": "None",
    "createOption": "Empty",
    "diskSizeGb": 64,
    "lun": 0,
    "managedDisk": {
      "additionalProperties": {},
      "storageAccountType": "Standard_LRS"
    },
    "name": null
  },
  {
    "additionalProperties": {},
    "caching": "None",
    "createOption": "Empty",
    "diskSizeGb": 128,
    "lun": 1,
    "managedDisk": {
      "additionalProperties": {},
      "storageAccountType": "Standard_LRS"
    },
    "name": null
  },
  {
    "additionalProperties": {},
    "caching": "None",
    "createOption": "Empty",
    "diskSizeGb": 128,
    "lun": 2,
    "managedDisk": {
      "additionalProperties": {},
      "storageAccountType": "Standard_LRS"
    },
    "name": null
  }
]
```


## <a name="detach-a-disk"></a>Desligar um disco
Quando já não precisar de um determinado disco, pode desanexá-lo do conjunto de dimensionamento. O disco é removido de todas as instâncias de VM no conjunto de dimensionamento. Para desanexar um disco de um conjunto de dimensionamento, utilize [az vmss disk detach](/cli/azure/vmss/disk) e especifique o LUN do disco. Os LUNs são apresentados no resultado de [az vmss show](/cli/azure/vmss) na secção anterior. O exemplo seguinte desanexa o LUN *2* do conjunto de dimensionamento:

```azurecli-interactive
az vmss disk detach \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --lun 2
```


## <a name="clean-up-resources"></a>Limpar os recursos
Para remover o seu conjunto de dimensionamento e os discos, elimine o grupo de recursos e todos os respetivos recursos com [az group delete](/cli/azure/group). O parâmetro `--no-wait` devolve o controlo à linha de comandos, sem aguardar a conclusão da operação. O parâmetro `--yes` confirma que pretende eliminar os recursos sem uma linha de comandos adicional para fazê-lo.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Passos seguintes
Neste tutorial, aprendeu a criar e a utilizar discos com conjuntos de dimensionamento com a CLI do Azure:

> [!div class="checklist"]
> * Discos de SO e discos temporários
> * Discos de dados
> * Discos Standard e Premium
> * Desempenho do disco
> * Anexar e preparar discos de dados

Avance para o próximo tutorial para saber como utilizar uma imagem personalizada para as instâncias de VM do conjunto de dimensionamento.

> [!div class="nextstepaction"]
> [Utilizar uma imagem personalizada para as instâncias de VM do conjunto de dimensionamento](tutorial-use-custom-image-cli.md)