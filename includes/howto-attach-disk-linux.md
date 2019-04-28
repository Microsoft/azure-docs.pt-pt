---
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 11/25/2018
ms.author: cynthn
ms.openlocfilehash: da2605e7d6dc8d46aa5c5cb89ec3e6f46076ffd1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61476288"
---
Para obter mais informações sobre discos, veja [About Disks and VHDs for Virtual Machines (Acerca de Discos e VHDs para Máquinas Virtuais)](../articles/virtual-machines/linux/managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

<a id="attachempty"></a>

## <a name="attach-an-empty-disk"></a>Anexar um disco vazio
1. Abra o CLI clássica do Azure e [ligar à sua subscrição do Azure](/cli/azure/authenticate-azure-cli). Confirme que está no modo Gestão de Serviço do Azure (`azure config mode asm`).
2. Introduza `azure vm disk attach-new` para criar e anexar um disco novo, conforme mostrado no exemplo seguinte. Substitua *myVM* pelo nome da sua Máquina Virtual do Linux e especifique o tamanho do disco em GB, que é *100* GB neste exemplo:

    ```azurecli
    azure vm disk attach-new myVM 100
    ```

3. Depois de ser criado e anexado, o disco de dados é listado nos resultados de `azure vm disk list <virtual-machine-name>`, conforme mostrado no exemplo seguinte:
   
    ```azurecli
    azure vm disk list TestVM
    ```

    O resultado é semelhante ao seguinte exemplo:

    ```bash
    info:    Executing command vm disk list
   
   * Fetching disk images
   * Getting virtual machines
   * Getting VM disks
     data:    Lun  Size(GB)  Blob-Name                         OS
     data:    ---  --------  --------------------------------  -----
     data:         30        myVM-2645b8030676c8f8.vhd  Linux
     data:    0    100       myVM-76f7ee1ef0f6dddc.vhd
     info:    vm disk list command OK
    ```

<a id="attachexisting"></a>

## <a name="attach-an-existing-disk"></a>Anexar um disco existente
Para anexar um disco existente, tem de ter um .vhd disponível numa conta de armazenamento.

1. Abra o CLI clássica do Azure e [ligar à sua subscrição do Azure](/cli/azure/authenticate-azure-cli). Confirme que está no modo Gestão de Serviço do Azure (`azure config mode asm`).
2. Verifique se o VHD que pretende anexar já está carregado para a sua subscrição do Azure:
   
    ```azurecli
    azure vm disk list
    ```

    O resultado é semelhante ao seguinte exemplo:

    ```azurecli
     info:    Executing command vm disk list
   
   * Fetching disk images
     data:    Name                                          OS
     data:    --------------------------------------------  -----
     data:    myTestVhd                                     Linux
     data:    TestVM-ubuntuVMasm-0-201508060029150744  Linux
     data:    TestVM-ubuntuVMasm-0-201508060040530369
     info:    vm disk list command OK
    ```

3. Se não encontrar o disco que quer utilizar, pode carregar um VHD local para a sua subscrição com `azure vm disk create` ou `azure vm disk upload`. Um exemplo de `disk create` seria igual ao seguinte:
   
    ```azurecli
    azure vm disk create myVhd .\TempDisk\test.VHD -l "East US" -o Linux
    ```

    O resultado é semelhante ao seguinte exemplo:

    ```azurecli
    info:    Executing command vm disk create
    + Retrieving storage accounts
    info:    VHD size : 10 GB
    info:    Uploading 10485760.5 KB
    Requested:100.0% Completed:100.0% Running:   0 Time:   25s Speed:    82 KB/s
    info:    Finishing computing MD5 hash, 16% is complete.
    info:    https://mystorageaccount.blob.core.windows.net/disks/myVHD.vhd was
    uploaded successfully
    info:    vm disk create command OK
    ```
   
   Também pode utilizar `azure vm disk upload` para carregar um VHD para uma conta de armazenamento específica. Leia mais sobre os comandos para gerir os discos de dados de máquinas virtuais do Azure [aqui](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).

4. Agora, vai anexar o VHD pretendido para a sua máquina virtual:
   
    ```azurecli
    azure vm disk attach myVM myVhd
    ```
   
   Confirme que substitui *myVM* pelo nome da sua máquina virtual e *myVHD* pelo VHD pretendido.

5. Pode utilizar `azure vm disk list <virtual-machine-name>` para verificar se o disco está anexado à máquina virtual.
   
    ```azurecli
    azure vm disk list myVM
    ```

    O resultado é semelhante ao seguinte exemplo:

    ```azurecli
     info:    Executing command vm disk list
   
   * Fetching disk images
   * Getting virtual machines
   * Getting VM disks
     data:    Lun  Size(GB)  Blob-Name                         OS
     data:    ---  --------  --------------------------------  -----
     data:         30        TestVM-2645b8030676c8f8.vhd  Linux
     data:    1    10        test.VHD
     data:    0    100        TestVM-76f7ee1ef0f6dddc.vhd
     info:    vm disk list command OK
    ```

> [!NOTE]
> Depois de adicionar um disco de dados, terá de iniciar sessão na máquina virtual e inicializar o disco, para que esta possa utilizar o disco para armazenamento (veja os passos seguintes para obter mais informações sobre como inicializar o disco).
> 
> 

