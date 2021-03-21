---
title: Tutorial - Gerir discos do Azure com a CLI do Azure
description: Neste tutorial, vai aprender a utilizar a CLI do Azure para criar e gerir discos do Azure para máquinas virtuais
author: cynthn
ms.service: virtual-machines
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 08/20/2020
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.subservice: disks
ms.openlocfilehash: 202125dfa1cd2760695672fb948fb47bfc3ca0c9
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102564650"
---
# <a name="tutorial---manage-azure-disks-with-the-azure-cli"></a>Tutorial - Gerir discos do Azure com a CLI do Azure

As máquinas virtuais (VMs) do Azure utilizam discos para armazenar o sistema operativo, as aplicações e os dados. Quando se cria um VM, é importante escolher um tamanho e configuração do disco adequados à carga de trabalho esperada. Este tutorial mostra como implementar e gerir discos de VM. Vai obter informações sobre:

> [!div class="checklist"]
> * Discos de SO e discos temporários
> * Discos de dados
> * Discos Standard e Premium
> * Desempenho do disco
> * Expor e preparar os discos de dados
> * Instantâneos de disco


## <a name="default-azure-disks"></a>Discos do Azure predefinidos

Quando uma máquina virtual do Azure é criada, dois discos são automaticamente expostos à máquina virtual.

**Disco do sistema operativo** – os discos do sistema operativo podem ter o tamanho máximo de 2 TB e alojam o sistema operativo das VMs. O disco do SO está identificado como */dev/sda* por predefinição. A configuração da colocação em cache do disco do SO está otimizada para desempenho do SO. Devido a esta configuração, o disco do SO **não deve** ser utilizado para aplicações nem para dados. Para aplicações e dados, utilize discos de dados, que são descritos posteriormente neste tutorial.

**Disco temporário** - os discos temporários utilizam uma unidade de estado sólido que está localizada no mesmo anfitrião da VM do Azure. Os discos temporários são de elevado desempenho e servem para operações como o processamento de dados temporários. No entanto, se a VM for movida para um novo anfitrião, todos os dados armazenados num disco temporário são removidos. O tamanho do disco temporário é determinado pelo tamanho da VM. Os discos temporários estão identificados como */dev/sdb* e têm um ponto de montagem de */mnt*.

## <a name="azure-data-disks"></a>Discos de dados do Azure

Para instalar aplicações e armazenar dados, podem ser adicionados mais discos de dados. Os discos de dados devem ser utilizados em qualquer situação em que se pretenda armazenamento de dados durável e reativo. O tamanho da máquina virtual determina quantos discos de dados podem ser expostos a uma VM.

## <a name="vm-disk-types"></a>Tipos de disco de VM

O Azure oferece dois tipos de discos.

**Discos Standard** - são apoiados por HDDs e oferecem armazenamento económico, mantendo o desempenho. Os discos Standard são ideais para uma carga de trabalho de desenvolvimento e teste económica.

Discos premium - **apoiados** por disco ssd baseado, de alto desempenho, de baixa latência. São perfeitos para as VMs com carga de trabalho de produção. Tamanhos VM com um  **S** no [nome de tamanho,](../vm-naming-conventions.md)normalmente suportam o Armazenamento Premium. Por exemplo, séries DS, séries DSv2, sérieS GS e VMs da série FS suportam armazenamento premium. Quando seleciona um tamanho de disco, o valor é arredondado para o tipo seguinte. Por exemplo, se o tamanho do disco for superior a 64 GB, mas inferior a 128 GB, o tipo de disco é P10. 

<br>


[!INCLUDE [disk-storage-premium-ssd-sizes](../../../includes/disk-storage-premium-ssd-sizes.md)]

Ao providenciar um disco de armazenamento premium, ao contrário do armazenamento padrão, é-lhe garantida a capacidade, iOPS e a produção desse disco. Por exemplo, se criar um disco P50, o Azure prevê capacidade de armazenamento de 4.095 GB, 7.500 IOPS e 250-MB/s para o disco. A sua aplicação pode utilizar a maior ou parte da capacidade e desempenho. Os discos Premium SSD são projetados para fornecer latências de um dígito baixo e IOPS alvo e produção descrita na tabela anterior 99,9% das vezes.

Enquanto a tabela acima identifica o IOPS máximo por disco, um nível mais elevado de desempenho pode ser alcançado ao repartir vários discos de dados. Por exemplo, podem ser anexados 64 discos de dados à VM Standard_GS5. Se cada um destes discos for dimensionado como um P30, pode ser alcançado um máximo de 80.000 IOPS. Para obter informações detalhadas sobre o IOPS máximo por VM, veja [Tipos e tamanhos de VMs](../sizes.md).

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

Azure Cloud Shell é uma concha interativa gratuita que pode usar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta.

Para abrir a Cloud Shell, selecione **Experimente-a** a partir do canto superior direito de um bloco de código. Também pode lançar cloud Shell num separador de navegador indo para [https://shell.azure.com/powershell](https://shell.azure.com/bash) . Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e prima Enter para executá-lo.

## <a name="create-and-attach-disks"></a>Criar e expor discos

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
    --name myDataDisk \
    --size-gb 128 \
    --sku Premium_LRS \
    --new
```

## <a name="prepare-data-disks"></a>Preparar dados de discos

Depois de um disco ser exposto à máquina virtual, o sistema operativo tem de ser configurado para utilizar o disco. O exemplo seguinte mostra como configurar manualmente um disco. Este processo também pode ser automatizado através da inicialização da cloud, que é abordada num [tutorial posterior](./tutorial-automate-vm-deployment.md).


Crie uma ligação SSH com a máquina virtual. Substitua o endereço IP de exemplo pelo IP público da máquina virtual.

```console
ssh 10.101.10.10
```

Particione o disco com `parted`.

```bash
sudo parted /dev/sdc --script mklabel gpt mkpart xfspart xfs 0% 100%
```

Escreva um sistema de ficheiros na partição com o comando `mkfs`. Utilize `partprobe` para sensibilizar o SO para a mudança.

```bash
sudo mkfs.xfs /dev/sdc1
sudo partprobe /dev/sdc1
```

Monte o disco novo para que fique acessível no sistema operativo.

```bash
sudo mkdir /datadrive && sudo mount /dev/sdc1 /datadrive
```

O disco pode agora ser acedido através do `/datadrive` ponto de montagem, o que pode ser verificado executando o `df -h` comando.

```bash
df -h | grep -i "sd"
```

A saída mostra a nova unidade `/datadrive` montada.

```bash
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        29G  2.0G   27G   7% /
/dev/sda15      105M  3.6M  101M   4% /boot/efi
/dev/sdb1        14G   41M   13G   1% /mnt
/dev/sdc1        50G   52M   47G   1% /datadrive
```

Para garantir que a unidade é remontada após uma reinicialização, tem de ser adicionada ao ficheiro *etc/fstab*. Para tal, obtenha o UUID do disco com o utilitário `blkid`.

```bash
sudo -i blkid
```

A saída apresenta o UUID da unidade, `/dev/sdc1` neste caso.

```bash
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="xfs"
```

> [!NOTE]
> A edição indevida do ficheiro **/etc/fstab** pode resultar num sistema inabitável. Se não tiver a certeza, consulte a documentação de distribuição para obter mais informações sobre como editar corretamente este ficheiro. Recomenda-se também que seja criada uma cópia de segurança do ficheiro /etc/fstab antes da edição.

Abra o `/etc/fstab` ficheiro num editor de texto da seguinte forma:

```bash
sudo nano /etc/fstab
```

Adicione uma linha semelhante à seguinte ao ficheiro */etc/fstab,* substituindo o valor UUID pelo seu próprio.

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive  xfs    defaults,nofail   1  2
```

Quando terminar de editar o ficheiro, use `Ctrl+O` para escrever o ficheiro e sair do `Ctrl+X` editor.

Agora que o disco foi configurado, feche a sessão SSH.

```bash
exit
```

## <a name="take-a-disk-snapshot"></a>Tire uma foto do disco

Quando tira um instantâneo de disco, o Azure cria uma cópia só de leitura de um ponto no tempo do disco. Os instantâneos de VM do Azure são úteis para guardar rapidamente o estado de uma VM antes de efetuar alterações de configuração. Em caso de problema ou erro, o VM pode ser restaurado com um instantâneo. Quando uma VM tiver mais do que um disco, é tirado um instantâneo de cada disco independentemente dos outros. Para fazer cópias de segurança da aplicação, considere parar a VM antes de tirar instantâneos do disco. Em alternativa, utilize o [Serviço Azure Backup](../../backup/index.yml), que permite efetuar cópias de segurança automatizadas enquanto a VM está em execução.

### <a name="create-snapshot"></a>Criar instantâneo

Antes de criar uma imagem, precisa da identificação ou nome do disco. Use [o az vm show](/cli/azure/vm#az-vm-show) para filmar a identificação do disco. Neste exemplo, o ID do disco é armazenado numa variável, para que possa ser utilizado num passo posterior.

```azurecli-interactive
osdiskid=$(az vm show \
   -g myResourceGroupDisk \
   -n myVM \
   --query "storageProfile.osDisk.managedDisk.id" \
   -o tsv)
```

Agora que tem o ID, use [az snapshot criar](/cli/azure/snapshot#az-snapshot-create) para criar uma imagem do disco.

```azurecli-interactive
az snapshot create \
    --resource-group myResourceGroupDisk \
    --source "$osdiskid" \
    --name osDisk-backup
```

### <a name="create-disk-from-snapshot"></a>Criar disco a partir de instantâneo

Este instantâneo pode então ser convertido num disco utilizando [o disco az,](/cli/azure/disk#az-disk-create)que pode ser usado para recriar a máquina virtual.

```azurecli-interactive
az disk create \
   --resource-group myResourceGroupDisk \
   --name mySnapshotDisk \
   --source osDisk-backup
```

### <a name="restore-virtual-machine-from-snapshot"></a>Restaurar a máquina virtual a partir de instantâneo

Para demonstrar a recuperação virtual da máquina, elimine a máquina virtual existente utilizando [a az vm delete](/cli/azure/vm#az-vm-delete).

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

Encontre o nome do disco de dados utilizando o comando [da lista de discos az.](/cli/azure/disk#az-disk-list) Este exemplo coloca o nome do disco numa variável chamada `datadisk` , que é usada no passo seguinte.

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
   --name $datadisk
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu sobre os tópicos de discos de VM, como:

> [!div class="checklist"]
> * Discos de SO e discos temporários
> * Discos de dados
> * Discos Standard e Premium
> * Desempenho do disco
> * Expor e preparar os discos de dados
> * Instantâneos de disco

Avance para o próximo tutorial para saber mais sobre como automatizar a configuração da VM.

> [!div class="nextstepaction"]
> [Automatizar a configuração da VM](./tutorial-automate-vm-deployment.md)
