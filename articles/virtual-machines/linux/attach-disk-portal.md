---
title: Anexar um disco de dados a um VM Linux
description: Utilize o portal para anexar um disco de dados novo ou existente a um VM Linux.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 07/12/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 746cef8dfe026c731a677cbf77f729d36342f007
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78969361"
---
# <a name="use-the-portal-to-attach-a-data-disk-to-a-linux-vm"></a>Utilizar o portal para anexar um disco de dados a uma VM do Linux 
Este artigo mostra-lhe como anexar discos novos e existentes a uma máquina virtual Linux através do portal Azure. Também pode anexar um disco de [dados a um VM do Windows no portal Azure](../windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Antes de anexar discos ao seu VM, reveja estas dicas:

* O tamanho da máquina virtual controla quantos discos de dados pode anexar. Para mais detalhes, consulte [Tamanhos para máquinas virtuais](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Os discos ligados a máquinas virtuais são na verdade ficheiros .vhd armazenados em Azure. Para mais detalhes, consulte a nossa [Introdução para discos geridos](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Depois de ligar o disco, tem de [se ligar ao VM Linux para montar o novo disco](#connect-to-the-linux-vm-to-mount-the-new-disk).


## <a name="find-the-virtual-machine"></a>Encontre a máquina virtual
1. Vá ao [portal Azure](https://portal.azure.com/) para encontrar o VM. Procure e selecione **máquinas Virtuais**.
2. Escolha o VM da lista.
3. Na barra lateral da página das **máquinas Virtuais,** em **Definições,** escolha **Discos**.
   
    ![Abrir definições de disco](./media/attach-disk-portal/find-disk-settings.png)


## <a name="attach-a-new-disk"></a>Anexar um disco novo

1. No painel **de Discos,** clique **+ Adicione o disco**de dados .
2. Clique no menu drop-down para **nome** e selecione **Criar disco:**

    ![Criar disco gerido pelo Azure](./media/attach-disk-portal/create-new-md.png)

3. Introduza um nome para o seu disco gerido. Reveja as definições predefinidas, atualize conforme necessário e, em seguida, clique em **Criar**.
   
   ![Rever as definições do disco](./media/attach-disk-portal/create-new-md-settings.png)

4. Clique em **Guardar** para criar o disco gerido e atualizar a configuração VM:

   ![Guarde o novo Disco Gerido azure](./media/attach-disk-portal/confirm-create-new-md.png)

5. Depois de o Azure criar o disco e ligá-lo à máquina virtual, o novo disco está listado nas definições do disco da máquina virtual em **disco de Data Disks**. Como os discos geridos são um recurso de alto nível, o disco aparece na raiz do grupo de recursos:

   ![Disco Gerido azure no grupo de recursos](./media/attach-disk-portal/view-md-resource-group.png)

## <a name="attach-an-existing-disk"></a>Anexar um disco existente
1. No painel **de Discos,** clique **+ Adicione o disco**de dados .
2. Clique no menu drop-down para **Nome** para ver uma lista de discos geridos existentes acessíveis à sua subscrição Azure. Selecione o disco gerido para anexar:

   ![Anexar o disco gerido azure existente](./media/attach-disk-portal/select-existing-md.png)

3. Clique em **Guardar** para anexar o disco gerido existente e atualizar a configuração VM:
   
   ![Salvar atualizações do Disco Gerido sintetizável do Azure](./media/attach-disk-portal/confirm-attach-existing-md.png)

4. Depois de o Azure ligar o disco à máquina virtual, está listado nas definições do disco da máquina virtual em discos de **dados**.

## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Ligue-se ao Linux VM para montar o novo disco
Para a partilha, formato e montagem do seu novo disco para que o seu VM Linux possa usá-lo, SSH no seu VM. Para obter mais informações, veja [como utilizar SSH com Linux no Azure](mac-create-ssh-keys.md). O exemplo seguinte liga-se a um VM com a entrada pública de *DNS* de mypublicdns.westus.cloudapp.azure.com com o nome de utilizador *azureuser:* 

```bash
ssh azureuser@mypublicdns.westus.cloudapp.azure.com
```

Uma vez ligado ao seu VM, está pronto para anexar um disco. Primeiro, encontre o `dmesg` disco utilizando (o método que utiliza para descobrir o seu novo disco pode variar). O exemplo seguinte utiliza dmesg para filtrar em discos *SCSI:*

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

Aqui, *sdc* é o disco que queremos. 

### <a name="partition-a-new-disk"></a>Partição de um novo disco
Se estiver a utilizar um disco existente que contenha dados, salte para a montagem do disco. Se estiver a anexar um disco novo, tem de dividir o disco.

> [!NOTE]
> Recomenda-se que utilize as versões mais recentes do disco ou da parte que estão disponíveis para a sua distro.

Particione o disco com `fdisk`. Se o tamanho do disco for de 2 tebibytes (TiB) ou `parted` maior, então deve utilizar a divisão GPT, pode utilizar para executar a divisão de GPT. Se o tamanho do disco for inferior a 2TiB, pode utilizar divisórias MBR ou GPT. Faça-o um disco primário na divisória 1 e aceite os outros incumprimentos. O seguinte exemplo `fdisk` inicia o processo em */dev/sdc:*

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

Imprima a tabela `p` de `w` partição digitando e, em seguida, use para escrever a tabela para o disco e saída. A saída deve ser semelhante ao seguinte exemplo:

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

Escreva um sistema de ficheiros `mkfs` para a partição com o comando. Especifique o seu tipo de sistema de ficheiros e o nome do dispositivo. O exemplo seguinte cria um sistema de *ficheiros ext4* na partição */dev/sdc1* que foi criado nos passos anteriores:

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

#### <a name="alternate-method-using-parted"></a>Método alternativo utilizando separados
O utilitário de disco necessita de entrada interativa e, portanto, não é ideal para ser usado dentro de scripts de automação. No entanto, a utilidade [separada](https://www.gnu.org/software/parted/) pode ser escrita e, portanto, presta-se melhor em cenários de automação. O utilitário separado pode ser usado para a partilha e para formatar um disco de dados. Para a passagem abaixo, utilizamos um novo disco de dados /dev/sdc e formamos-os utilizando o sistema de ficheiros [XFS.](https://xfs.wiki.kernel.org/)
```bash
sudo parted /dev/sdc --script mklabel gpt mkpart xfspart xfs 0% 100%
partprobe /dev/sdc1
```
Como acima visto, utilizamos o utilitário [partprobe](https://linux.die.net/man/8/partprobe) para garantir que o núcleo está imediatamente ciente da nova partilha e sistema de ficheiros. A não utilização da sonda partprobe pode fazer com que os comandos blkid ou lslbk não devolvam imediatamente o UUID para o novo sistema de ficheiros.

### <a name="mount-the-disk"></a>Monte o disco
Crie um diretório para `mkdir`montar o sistema de ficheiros utilizando . O exemplo seguinte cria um diretório no */datadrive:*

```bash
sudo mkdir /datadrive
```

Utilize `mount` para, em seguida, montar o sistema de ficheiros. O exemplo seguinte monta a divisória */dev/sdc1* até ao ponto de montagem */datadrive:*

```bash
sudo mount /dev/sdc1 /datadrive
```

Para garantir que a unidade é montada automaticamente após uma reinicialização, deve ser adicionada ao ficheiro */etc/fstab.* Recomenda-se também que o UUID (Universalunique IDentifier) seja utilizado em */etc/fstab* para se referir à unidade em vez de apenas o nome do dispositivo (como, */dev/sdc1*). Se o SO detetar um erro do disco durante o arranque, ao utilizar o UUID evitará que o disco incorreto seja montado numa determinada localização. Os restantes discos de dados serão, em seguida, atribuídos aos mesmos IDs de dispositivos. Para localizar o UUID da nova unidade, utilize o utilitário `blkid`:

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
> A edição inadequada do ficheiro **/etc/fstab** pode resultar num sistema inabitável. Se não tiver a certeza, consulte a documentação de distribuição para obter mais informações sobre como editar corretamente este ficheiro. Recomenda-se também que seja criada uma cópia de segurança do ficheiro /etc/fstab antes da edição.

Em seguida, abra o ficheiro */etc/fstab* num editor de texto da seguinte forma:

```bash
sudo vi /etc/fstab
```

Neste exemplo, utilize o valor UUID para o dispositivo */dev/sdc1* que foi criado nos passos anteriores, e o ponto de montagem de */datadrive*. Adicione a seguinte linha à extremidade do ficheiro */etc/fstab):*

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```
Quando estiver pronto, guarde o ficheiro */etc/fstab* e reinicie o sistema.
> [!NOTE]
> Posteriormente, a remoção de um disco de dados sem a edição de fstab poderia fazer com que o VM falhasse no arranque. A maioria das distribuições fornece as opções de *nofail* e/ou *nobootwait* fstab. Estas opções permitem que um sistema arranque mesmo que o disco não monte no momento do arranque. Consulte a documentação da sua distribuição para obter mais informações sobre estes parâmetros.
> 
> A opção *nofail* garante que o VM começa mesmo que o sistema de ficheiros esteja corrupto ou o disco não exista no momento do arranque. Sem esta opção, pode encontrar comportamentos descritos em [Can SSH a Linux VM devido a erros fstab](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/)

### <a name="trimunmap-support-for-linux-in-azure"></a>Suporte TRIM/UNMAP para Linux em Azure
Alguns núcleos linux suportam operações TRIM/UNMAP para descartar blocos não utilizados no disco. Esta funcionalidade é principalmente útil no armazenamento padrão para informar o Azure de que as páginas apagadas já não são válidas e podem ser descartadas, e pode economizar dinheiro se criar ficheiros grandes e depois eliminá-los.

Existem duas formas de permitir o suporte trim no seu VM Linux. Como de costume, consulte a sua distribuição para obter a abordagem recomendada:

* Utilize `discard` a opção montagem em */etc/fstab,* por exemplo:

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2
    ```
* Em alguns `discard` casos, a opção pode ter implicações no desempenho. Em alternativa, pode `fstrim` executar o comando manualmente a partir da linha de comando, ou adicioná-lo ao seu crontab para executar regularmente:
  
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

## <a name="next-steps"></a>Passos seguintes
Também pode [anexar um disco](add-disk.md) de dados utilizando o Azure CLI.
