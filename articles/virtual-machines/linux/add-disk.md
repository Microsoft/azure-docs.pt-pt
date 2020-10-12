---
title: Adicione um disco de dados ao Linux VM utilizando o Azure CLI
description: Aprenda a adicionar um disco de dados persistente ao seu Linux VM com o Azure CLI
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 08/20/2020
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 7098744fe012c994e311696a376cd7ed0dc9ac53
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89076621"
---
# <a name="add-a-disk-to-a-linux-vm"></a>Adicionar um disco a uma VM com Linux

Este artigo mostra-lhe como anexar um disco persistente ao seu VM para que possa preservar os seus dados - mesmo que o seu VM seja reprovisionado devido à manutenção ou redimensionamento.


## <a name="attach-a-new-disk-to-a-vm"></a>Anexar um disco novo a um VM

Se pretender adicionar um novo disco de dados vazio no seu VM, utilize o comando de anexação do [disco az vm](/cli/azure/vm/disk?view=azure-cli-latest) com o `--new` parâmetro. Se o seu VM estiver numa Zona de Disponibilidade, o disco é automaticamente criado na mesma zona que o VM. Para mais informações, consulte [a Visão Geral das Zonas de Disponibilidade.](../../availability-zones/az-overview.md) O exemplo a seguir cria um disco chamado *myDataDisk* que tem 50 Gb de tamanho:

```azurecli
az vm disk attach \
   -g myResourceGroup \
   --vm-name myVM \
   --name myDataDisk \
   --new \
   --size-gb 50
```

## <a name="attach-an-existing-disk"></a>Anexar um disco existente

Para fixar um disco existente, encontre o ID do disco e passe o ID para o comando de anexação do [disco az vm.](/cli/azure/vm/disk?view=azure-cli-latest) As seguintes consultas de exemplo para um disco chamado *myDataDisk* no *myResourceGroup,* em seguida, anexa-o ao VM chamado *myVM*:

```azurecli
diskId=$(az disk show -g myResourceGroup -n myDataDisk --query 'id' -o tsv)

az vm disk attach -g myResourceGroup --vm-name myVM --name $diskId
```

## <a name="format-and-mount-the-disk"></a>Formato e montagem do disco

Para a partilha, formato e montagem do seu novo disco para que o seu VM Linux possa usá-lo, SSH no seu VM. Para obter mais informações, veja [como utilizar SSH com Linux no Azure](mac-create-ssh-keys.md). O exemplo a seguir liga-se a um VM com o endereço IP público de *10.123.123.25* com o nome de utilizador *azureuser:*

```bash
ssh azureuser@10.123.123.25
```

### <a name="find-the-disk"></a>Localizar o disco

Uma vez ligado ao seu VM, tem de encontrar o disco. Neste exemplo, estamos a usar `lsblk` para listar os discos. 

```bash
lsblk -o NAME,HCTL,SIZE,MOUNTPOINT | grep -i "sd"
```

O resultado é semelhante ao seguinte exemplo:

```bash
sda     0:0:0:0      30G
├─sda1             29.9G /
├─sda14               4M
└─sda15             106M /boot/efi
sdb     1:0:1:0      14G
└─sdb1               14G /mnt
sdc     3:0:0:0      50G
```

Aqui, `sdc` é o disco que queremos, porque é 50G. Se não tiver a certeza de qual disco se baseia apenas no tamanho, pode ir à página VM no portal, selecionar **Discos**e verificar o número LUN para o disco nos **discos de dados**. 


### <a name="format-the-disk"></a>Formato do disco

Em formato o disco com `parted` , se o tamanho do disco for de 2 tebibytes (TiB) ou maior, então deve utilizar a partição GPT, se estiver abaixo de 2TiB, então pode utilizar a partição MBR ou GPT. 

> [!NOTE]
> Recomenda-se que utilize a versão mais recente `parted` que está disponível para o seu distro.
> Se o tamanho do disco for de 2 tebibytes (TiB) ou maiores, deve utilizar a partição GPT. Se o tamanho do disco for inferior a 2 TiB, então pode utilizar a partição MBR ou GPT.  


O exemplo a seguir é `parted` em , que é onde o primeiro disco de `/dev/sdc` dados normalmente estará na maioria dos VMs. `sdc`Substitua-a pela opção correta para o seu disco. Também estamos a formatar o sistema de ficheiros [XFS.](https://xfs.wiki.kernel.org/)

```bash
sudo parted /dev/sdc --script mklabel gpt mkpart xfspart xfs 0% 100%
sudo mkfs.xfs /dev/sdc1
sudo partprobe /dev/sdc1
```

Utilize o [`partprobe`](https://linux.die.net/man/8/partprobe) utilitário para se certificar de que o núcleo está ciente da nova partição e do sistema de ficheiros. A não utilização `partprobe` pode fazer com que os comandos blkid ou lslbk não devolvam imediatamente o UUID para o novo sistema de ficheiros.


### <a name="mount-the-disk"></a>Monte o disco

Agora, crie um diretório para montar o sistema de ficheiros utilizando `mkdir` . O exemplo a seguir cria um diretório `/datadrive` em:

```bash
sudo mkdir /datadrive
```

Utilize `mount` para montar o sistema de ficheiros. O exemplo a seguir monta a `/dev/sdc1` partição até ao `/datadrive` ponto de montagem:

```bash
sudo mount /dev/sdc1 /datadrive
```

### <a name="persist-the-mount"></a>Persistir o monte

Para garantir que a unidade é remontada automaticamente após um reboot, deve ser adicionada ao ficheiro */etc/fstab.* Recomenda-se também que o UUID (Identificador Universalmente Único) seja utilizado em */etc/fstab* para se referir à unidade e não apenas ao nome do dispositivo (como, por exemplo, */dev/sdc1).* Se o SO detetar um erro do disco durante o arranque, ao utilizar o UUID evitará que o disco incorreto seja montado numa determinada localização. Os restantes discos de dados serão, em seguida, atribuídos aos mesmos IDs de dispositivos. Para localizar o UUID da nova unidade, utilize o utilitário `blkid`:

```bash
sudo blkid
```

A saída é semelhante ao seguinte exemplo:

```bash
/dev/sda1: LABEL="cloudimg-rootfs" UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4" PARTUUID="1a1b1c1d-11aa-1234-1a1a1a1a1a1a"
/dev/sda15: LABEL="UEFI" UUID="BCD7-96A6" TYPE="vfat" PARTUUID="1e1g1cg1h-11aa-1234-1u1u1a1a1u1u"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4" TYPE="ext4" PARTUUID="1a2b3c4d-01"
/dev/sda14: PARTUUID="2e2g2cg2h-11aa-1234-1u1u1a1a1u1u"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="xfs" PARTLABEL="xfspart" PARTUUID="c1c2c3c4-1234-cdef-asdf3456ghjk"
```

> [!NOTE]
> A edição indevida do ficheiro **/etc/fstab** pode resultar num sistema inabitável. Se não tiver a certeza, consulte a documentação de distribuição para obter mais informações sobre como editar corretamente este ficheiro. Recomenda-se também que seja criada uma cópia de segurança do ficheiro /etc/fstab antes da edição.

Em seguida, abra o ficheiro */etc/fstab* num editor de texto da seguinte forma:

```bash
sudo nano /etc/fstab
```

Neste exemplo, utilize o valor UUID para o `/dev/sdc1` dispositivo criado nos degraus anteriores e o ponto de montagem de `/datadrive` . Adicione a seguinte linha à extremidade do `/etc/fstab` ficheiro:

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   xfs   defaults,nofail   1   2
```

Neste exemplo, estamos a usar o nano editor, por isso, quando terminar de editar o ficheiro, use `Ctrl+O` para escrever o ficheiro e sair do `Ctrl+X` editor.

> [!NOTE]
> Posteriormente, remover um disco de dados sem editar o fstab pode fazer com que o VM não arranque. A maioria das distribuições fornece ou as opções *de nofail* e/ou *nobootwait* fstab. Estas opções permitem o arranque de um sistema mesmo que o disco não consiga montar na hora do arranque. Consulte a documentação da sua distribuição para obter mais informações sobre estes parâmetros.
>
> A *opção nofalil* garante que o VM começa mesmo que o sistema de ficheiros seja corrupto ou o disco não exista no momento do arranque. Sem esta opção, poderá encontrar comportamento como descrito em [Não SSH a Linux VM devido a erros do FSTAB](/archive/blogs/linuxonazure/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting)
>
> A Consola Em Série Azure VM pode ser utilizada para o acesso à consola ao seu VM se modificar o fstab resultou numa falha no arranque. Mais detalhes estão disponíveis na [documentação da Consola Em Série.](../troubleshooting/serial-console-linux.md)

### <a name="trimunmap-support-for-linux-in-azure"></a>Suporte TRIM/UNMAP para Linux em Azure
Alguns núcleos Linux suportam operações TRIM/UNMAP para descartar blocos não reutilizados no disco. Esta funcionalidade é principalmente útil no armazenamento padrão para informar a Azure que as páginas eliminadas já não são válidas e podem ser descartadas, e pode economizar dinheiro se criar ficheiros grandes e depois eliminá-las.

Existem duas formas de ativar o suporte TRIM no seu Linux VM. Como sempre, consulte a sua distribuição para obter a abordagem recomendada:

* Utilize a `discard` opção de montagem em */etc/fstab,* por exemplo:

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   xfs   defaults,discard   1   2
    ```
* Em alguns casos, a `discard` opção pode ter implicações no desempenho. Em alternativa, pode executar o `fstrim` comando manualmente a partir da linha de comando ou adicioná-lo ao crontab para executar regularmente:

    **Ubuntu**

    ```bash
    sudo apt-get install util-linux
    sudo fstrim /datadrive
    ```

    **RHEL/CentOS**

    ```bash
    sudo yum install util-linux
    sudo fstrim /datadrive
    ```

## <a name="troubleshooting"></a>Resolução de problemas

[!INCLUDE [virtual-machines-linux-lunzero](../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>Passos seguintes

* Para garantir que o seu VM Linux está configurado corretamente, reveja as recomendações de desempenho da [máquina Linux.](optimization.md)
* Expanda a sua capacidade de armazenamento adicionando discos adicionais e [configurar o RAID](configure-raid.md) para um desempenho adicional.
