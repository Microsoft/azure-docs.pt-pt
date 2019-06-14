---
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 11/25/2018
ms.author: cynthn
ms.openlocfilehash: 25e67a5a6c3abebbac2e0489ff636ca1457b62ea
ms.sourcegitcommit: f9448a4d87226362a02b14d88290ad6b1aea9d82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/07/2019
ms.locfileid: "66807725"
---
Quando já não precisar de um disco de dados que esteja ligado a uma máquina virtual (VM), pode desligá-lo facilmente. Quando desligar um disco da VM, o disco não é removido do armazenamento. Se pretender voltar a utilizar os dados existentes no disco, pode voltar a ligá-lo à mesma VM ou a outra.  

> [!NOTE]
> Uma VM no Azure utiliza diferentes tipos de discos - um disco de sistema operativo, um disco local temporário e discos de dados opcionais. Para obter detalhes, veja [Acerca dos Discos e VHDs para Máquinas Virtuais](../articles/virtual-machines/linux/managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Não é possível desligar um disco de sistema operativo, a menos que elimine também a VM.

## <a name="find-the-disk"></a>Localizar o disco
Para poder desligar um disco de uma VM, precisa de saber o número do LUN, que é um identificador para o disco que vai ser ligado. Para tal, siga estes passos:

1. Abra a CLI do Azure e [ligar à sua subscrição do Azure](/cli/azure/authenticate-azure-cli). Confirme que está no modo Gestão de Serviço do Azure (`azure config mode asm`).
2. Descubra que discos estão ligados à sua VM. O exemplo seguinte lista os discos para a VM com o nome `myVM`:

    ```azurecli
    azure vm disk list myVM
    ```

    O resultado é semelhante ao seguinte exemplo:

    ```azurecli
    * Fetching disk images
    * Getting virtual machines
    * Getting VM disks
      data:    Lun  Size(GB)  Blob-Name                         OS
      data:    ---  --------  --------------------------------  -----
      data:         30        ubuntuVM-2645b8030676c8f8.vhd  Linux
      data:    0    30        myDataDisk.vhd
      info:    vm disk list command OK
    ```

3. Tome nota do LUN ou do **número de unidade lógica** para o disco que pretende desligar.

## <a name="remove-operating-system-references-to-the-disk"></a>Remover referências do sistema operativo ao disco
Antes de desligar o disco do convidado Linux, certifique-se de que todas as partições no disco não estão a ser utilizadas. Certifique-se de que o sistema operativo não tenta voltar a montá-las após um reinício. Estes passos anulam a configuração que provavelmente criou quando [ligou](../articles/virtual-machines/linux/classic/attach-disk-classic.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) o disco.

1. Utilize o comando `lsscsi` para detetar o identificador do disco. `lsscsi` pode ser instalado através de `yum install lsscsi` (em distribuições baseadas no Red Hat) ou de `apt-get install lsscsi` (em distribuições baseadas no Debian). Pode localizar o identificador do disco que está a procurar através do número do LUN. O último número na cadeia de identificação em cada linha é o LUN. No exemplo seguinte de `lsscsi`, o LUN 0 é mapeado para */dev/sdc*

    ```bash
    [1:0:0:0]    cd/dvd  Msft     Virtual CD/ROM   1.0   /dev/sr0
    [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
    [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
    [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
    ```

2. Utilize `fdisk -l <disk>` para detetar as partições associadas ao disco que vai ser ligado. O exemplo seguinte mostra a saída de `/dev/sdc`:

    ```bash
    Disk /dev/sdc: 1098.4 GB, 1098437885952 bytes, 2145386496 sectors
    Units = sectors of 1 * 512 = 512 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disk label type: dos
    Disk identifier: 0x5a1d2a1a
    
        Device Boot      Start         End      Blocks   Id  System
    /dev/sdc1            2048  2145386495  1072692224   83  Linux
    ```

3. Desmonte cada uma das partições listadas para o disco. O exemplo seguinte desmonta `/dev/sdc1`:

    ```bash
    sudo umount /dev/sdc1
    ```

4. Utilize o comando `blkid` para detetar os UUIDs para todas as partições. O resultado é semelhante ao seguinte exemplo:

    ```bash
    /dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
    /dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
    /dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
    ```

5. Remova as entradas no ficheiro **/etc/fstab** associado a cada um dos caminhos de dispositivo ou UUIDs para todas as partições do disco que vai ser desligado.  As entradas para este exemplo poderão ser:

    ```sh  
   UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2
   ```

    ou
   
   ```sh   
   /dev/sdc1   /datadrive   ext4   defaults   1   2
   ```

## <a name="detach-the-disk"></a>Desligar o disco
Depois de encontrar o número do LUN do disco e remover as referências de sistema operativo, está pronto para desligá-lo:

1. Desligue o disco selecionado da máquina virtual, executando o comando `azure vm disk detach
   <virtual-machine-name> <LUN>`. O exemplo seguinte desliga o LUN `0` da VM com o nome `myVM`:
   
    ```azurecli
    azure vm disk detach myVM 0
    ```

2. Pode verificar se o disco foi desligado, executando o comando `azure vm disk list` novamente. O exemplo seguinte verifica a VM com o nome `myVM`:
   
    ```azurecli
    azure vm disk list myVM
    ```

    A saída é semelhante ao seguinte exemplo, o qual mostra que o disco de dados já não está ligado:

    ```azurecli
    info:    Executing command vm disk list
   
   * Fetching disk images
   * Getting virtual machines
   * Getting VM disks
     data:    Lun  Size(GB)  Blob-Name                         OS
     data:    ---  --------  --------------------------------  -----
     data:         30        ubuntuVM-2645b8030676c8f8.vhd  Linux
     info:    vm disk list command OK
    ```

O disco desligado permanece no armazenamento, mas já não está ligado a uma máquina virtual.

