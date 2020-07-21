---
title: Anexar um disco de dados a um Linux VM
description: Utilize o portal para anexar um disco de dados novo ou existente a um Linux VM.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 07/12/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: c5be90cfd107dfa66eb31597818d93ef092b5258
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86502865"
---
# <a name="use-the-portal-to-attach-a-data-disk-to-a-linux-vm"></a>Utilizar o portal para anexar um disco de dados a uma VM do Linux 
Este artigo mostra-lhe como ligar discos novos e existentes a uma máquina virtual Linux através do portal Azure. Também pode [anexar um disco de dados a um VM do Windows no portal Azure](../windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Antes de anexar discos ao seu VM, reveja estas dicas:

* O tamanho da máquina virtual controla quantos discos de dados pode anexar. Para mais detalhes, consulte [tamanhos para máquinas virtuais.](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* Os discos ligados a máquinas virtuais são na verdade ficheiros .vhd armazenados em Azure. Para mais detalhes, consulte a nossa [Introdução aos discos geridos.](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* Depois de fixar o disco, é necessário [ligar-se ao Linux VM para montar o novo disco](#connect-to-the-linux-vm-to-mount-the-new-disk).


## <a name="find-the-virtual-machine"></a>Encontre a máquina virtual
1. Vá ao [portal Azure](https://portal.azure.com/) para encontrar o VM. Procure e selecione **máquinas Virtuais.**
2. Escolha o VM da lista.
3. Na barra lateral da página das **máquinas virtuais,** em **Definições,** escolha **Discos**.
   
    ![Abrir definições de disco](./media/attach-disk-portal/find-disk-settings.png)


## <a name="attach-a-new-disk"></a>Anexar um disco novo

1. No painel **discos,** clique **+ Adicione o disco de dados.**
2. Clique no menu drop-down para **nome** e selecione **Criar disco**:

    ![Criar disco gerido Azure](./media/attach-disk-portal/create-new-md.png)

3. Insira um nome para o seu disco gerido. Reveja as definições predefinidos, atualize se necessário e, em seguida, clique em **Criar**.
   
   ![Rever definições de discos](./media/attach-disk-portal/create-new-md-settings.png)

4. Clique em **Guardar** para criar o disco gerido e atualizar a configuração VM:

   ![Salvar novo disco gerido a Azure](./media/attach-disk-portal/confirm-create-new-md.png)

5. Depois de o Azure criar o disco e o ligar à máquina virtual, o novo disco está listado nas definições de disco da máquina virtual em **Discos de Dados**. Como os discos geridos são um recurso de alto nível, o disco aparece na raiz do grupo de recursos:

   ![Disco gerido Azure no grupo de recursos](./media/attach-disk-portal/view-md-resource-group.png)

## <a name="attach-an-existing-disk"></a>Anexar um disco existente
1. No painel **discos,** clique **+ Adicione o disco de dados.**
2. Clique no menu drop-down para **ver** uma lista de discos geridos existentes acessíveis à sua subscrição Azure. Selecione o disco gerido para anexar:

   ![Anexar o disco gerido a azure existente](./media/attach-disk-portal/select-existing-md.png)

3. Clique em **Guardar** para anexar o disco gerido existente e atualizar a configuração VM:
   
   ![Guardar atualizações de disco gerido aZure](./media/attach-disk-portal/confirm-attach-existing-md.png)

4. Depois de a Azure ligar o disco à máquina virtual, está listado nas definições de disco da máquina virtual em **Discos de Dados**.

## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Ligue-se ao Linux VM para montar o novo disco
Para a partilha, formato e montagem do seu novo disco para que o seu VM Linux possa usá-lo, SSH no seu VM. Para obter mais informações, veja [como utilizar SSH com Linux no Azure](mac-create-ssh-keys.md). O exemplo a seguir liga-se a um VM com a entrada pública de DNS de *mypublicdns.westus.cloudapp.azure.com* com o nome de utilizador *azureuser:* 

```bash
ssh azureuser@mypublicdns.westus.cloudapp.azure.com
```

Uma vez ligado ao seu VM, está pronto para anexar um disco. Primeiro, encontre o disco utilizando `dmesg` (o método que utiliza para descobrir o seu novo disco pode variar). O exemplo a seguir utiliza o dmesg para filtrar em discos *SCSI:*

```bash
dmesg | grep SCSI
```

O resultado é semelhante ao seguinte exemplo:

```bash
[    0.294784] SCSI subsystem initialized
[    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
[    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
[    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
[ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
```

Aqui, *o SDC* é o disco que queremos. 

### <a name="partition-a-new-disk"></a>Partição de um novo disco
Se estiver a utilizar um disco existente que contenha dados, salte para a montagem do disco. Se estiver a anexar um disco novo, tem de dividir o disco.

> [!NOTE]
> Recomenda-se que utilize as versões mais recentes de fdisk ou desapartadas que estão disponíveis para o seu distro.

Particione o disco com `fdisk`. Se o tamanho do disco for de 2 tebibytes (TiB) ou maior, então deve utilizar a partição GPT, pode utilizar `parted` para realizar a partição GPT. Se o tamanho do disco for inferior a 2TiB, então pode utilizar a partição MBR ou GPT. Faça-o um disco primário na partição 1 e aceite os outros defeitos. O exemplo a seguir inicia o `fdisk` processo em */dev/sdc*:

```bash
sudo fdisk /dev/sdc
```

Utilize o comando `n` para adicionar uma nova partição. Neste exemplo, também `p` escolhemos uma partição primária e aceitamos o resto dos valores predefinidos. O resultado vai ser semelhante ao exemplo seguinte:

```bash
Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
Building a new DOS disklabel with disk identifier 0x2a59b123.
Changes will remain in memory only, until you decide to write them.
After that, of course, the previous content won't be recoverable.

Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-10485759, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-10485759, default 10485759):
Using default value 10485759
```

Imprima a tabela de partição digitando `p` e, em seguida, use `w` para escrever a tabela para o disco e para a saída. O resultado deverá ter um aspeto semelhante ao seguinte exemplo:

```bash
Command (m for help): p

Disk /dev/sdc: 5368 MB, 5368709120 bytes
255 heads, 63 sectors/track, 652 cylinders, total 10485760 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x2a59b123

   Device Boot      Start         End      Blocks   Id  System
/dev/sdc1            2048    10485759     5241856   83  Linux

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
```

Agora, escreva um sistema de ficheiros para a divisão com o `mkfs` comando. Especifique o seu tipo de sistema de ficheiros e o nome do dispositivo. O exemplo a seguir cria um sistema *de ficheiros ext4* na partição */dev/sdc1* que foi criada nos passos anteriores:

```bash
sudo mkfs -t ext4 /dev/sdc1
```

O resultado é semelhante ao seguinte exemplo:

```bash
mke2fs 1.42.9 (4-Feb-2014)
Discarding device blocks: done
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
327680 inodes, 1310464 blocks
65523 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=1342177280
40 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
    32768, 98304, 163840, 229376, 294912, 819200, 884736
Allocating group tables: done
Writing inode tables: done
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done
```

#### <a name="alternate-method-using-parted"></a>Método alternativo utilizando a parte
O utilitário fdisk precisa de entrada interativa e, portanto, não é ideal para ser usado dentro de scripts de automação. No entanto, a utilidade [de parte](https://www.gnu.org/software/parted/) pode ser escrita e, portanto, presta-se melhor em cenários de automação. O utilitário de separação pode ser usado para dividir e para formatar um disco de dados. Para a passagem abaixo, utilizamos um novo disco de dados /dev/sdc e formato-o utilizando o sistema de [ficheiros XFS.](https://xfs.wiki.kernel.org/)
```bash
sudo parted /dev/sdc --script mklabel gpt mkpart xfspart xfs 0% 100%
sudo mkfs.xfs /dev/sdc1
partprobe /dev/sdc1
```
Como visto acima, usamos o utilitário [partprobe](https://linux.die.net/man/8/partprobe) para garantir que o núcleo está imediatamente ciente da nova partição e sistema de ficheiros. A não utilização do partprobe pode fazer com que os comandos blkid ou lslbk não devolvam imediatamente o UUID para o novo sistema de ficheiros.

### <a name="mount-the-disk"></a>Monte o disco
Crie um diretório para montar o sistema de ficheiros utilizando `mkdir` . O exemplo a seguir cria um diretório em */datadrive:*

```bash
sudo mkdir /datadrive
```

Utilize `mount` para montar o sistema de ficheiros. O exemplo a seguir monta a partição */dev/sdc1* até ao ponto de montagem */datadrive:*

```bash
sudo mount /dev/sdc1 /datadrive
```

Para garantir que a unidade é remontada automaticamente após um reboot, deve ser adicionada ao ficheiro */etc/fstab.* Recomenda-se também que o UUID (IDentifier Universally Unique) seja utilizado em */etc/fstab* para se referir à unidade e não apenas ao nome do dispositivo (como, por exemplo, */dev/sdc1).* Se o SO detetar um erro do disco durante o arranque, ao utilizar o UUID evitará que o disco incorreto seja montado numa determinada localização. Os restantes discos de dados serão, em seguida, atribuídos aos mesmos IDs de dispositivos. Para localizar o UUID da nova unidade, utilize o utilitário `blkid`:

```bash
sudo -i blkid
```

A saída é semelhante ao seguinte exemplo:

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

> [!NOTE]
> A edição indevida do ficheiro **/etc/fstab** pode resultar num sistema inabitável. Se não tiver a certeza, consulte a documentação de distribuição para obter mais informações sobre como editar corretamente este ficheiro. Recomenda-se também que seja criada uma cópia de segurança do ficheiro /etc/fstab antes da edição.

Em seguida, abra o ficheiro */etc/fstab* num editor de texto da seguinte forma:

```bash
sudo vi /etc/fstab
```

Neste exemplo, utilize o valor UUID para o dispositivo */dev/sdc1* criado nos passos anteriores, e o ponto de montagem de */datadrive*. Adicione a seguinte linha à extremidade do ficheiro */etc/fstab:*

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```
Quando terminar, guarde o ficheiro */etc/fstab* e reinicie o sistema.
> [!NOTE]
> Posteriormente, remover um disco de dados sem editar o fstab pode fazer com que o VM não arranque. A maioria das distribuições fornece ou as opções *de nofail* e/ou *nobootwait* fstab. Estas opções permitem o arranque de um sistema mesmo que o disco não consiga montar na hora do arranque. Consulte a documentação da sua distribuição para obter mais informações sobre estes parâmetros.
> 
> A *opção nofalil* garante que o VM começa mesmo que o sistema de ficheiros seja corrupto ou o disco não exista no momento do arranque. Sem esta opção, poderá encontrar comportamento como descrito em [Não SSH a Linux VM devido a erros do FSTAB](/archive/blogs/linuxonazure/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting)

### <a name="trimunmap-support-for-linux-in-azure"></a>Suporte TRIM/UNMAP para Linux em Azure
Alguns núcleos Linux suportam operações TRIM/UNMAP para descartar blocos não reutilizados no disco. Esta funcionalidade é principalmente útil no armazenamento padrão para informar a Azure que as páginas eliminadas já não são válidas e podem ser descartadas, e pode economizar dinheiro se criar ficheiros grandes e depois eliminá-las.

Existem duas formas de ativar o suporte TRIM no seu Linux VM. Como sempre, consulte a sua distribuição para obter a abordagem recomendada:

* Utilize a `discard` opção de montagem em */etc/fstab,* por exemplo:

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2
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

## <a name="next-steps"></a>Próximos passos
Também pode [anexar um disco de dados](add-disk.md) utilizando o Azure CLI.
