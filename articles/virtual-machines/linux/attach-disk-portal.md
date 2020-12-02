---
title: Anexar um disco de dados a um Linux VM
description: Utilize o portal para anexar um disco de dados novo ou existente a um Linux VM.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 08/28/2020
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 8f60c83417e9c614ca30f140e6acbbf08e5643cf
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500653"
---
# <a name="use-the-portal-to-attach-a-data-disk-to-a-linux-vm"></a>Utilizar o portal para anexar um disco de dados a uma VM do Linux 
Este artigo mostra-lhe como ligar discos novos e existentes a uma máquina virtual Linux através do portal Azure. Também pode [anexar um disco de dados a um VM do Windows no portal Azure](../windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Antes de anexar discos ao seu VM, reveja estas dicas:

* O tamanho da máquina virtual controla quantos discos de dados pode anexar. Para mais detalhes, consulte [tamanhos para máquinas virtuais.](../sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* Os discos ligados a máquinas virtuais são na verdade ficheiros .vhd armazenados em Azure. Para mais detalhes, consulte a nossa [Introdução aos discos geridos.](../managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* Depois de fixar o disco, é necessário [ligar-se ao Linux VM para montar o novo disco](#connect-to-the-linux-vm-to-mount-the-new-disk).


## <a name="find-the-virtual-machine"></a>Encontre a máquina virtual
1. Vá ao [portal Azure](https://portal.azure.com/) para encontrar o VM. Procure e selecione **máquinas Virtuais.**
2. Escolha o VM da lista.
3. Na página **de máquinas Virtuais,** em **Definições,** escolha **Discos**.


## <a name="attach-a-new-disk"></a>Anexar um disco novo

1. No painel **discos,** em **discos de dados,** selecione **Criar e prenda um novo disco**.

1. Insira um nome para o seu disco gerido. Reveja as definições predefinidos e atualize o **tipo de armazenamento**, tamanho **(GiB)**, **Encriptação** e **caching do anfitrião,** se necessário.
   
   :::image type="content" source="./media/attach-disk-portal/create-new-md.png" alt-text="Reveja as definições do disco.":::


1. Quando terminar, **selecione Guardar** no topo da página para criar o disco gerido e atualizar a configuração VM.


## <a name="attach-an-existing-disk"></a>Anexar um disco existente
1. No painel **de discos,** em **discos de dados,** selecione  **Fixe os discos existentes**.
1. Clique no menu suspenso para **o nome do disco** e selecione um disco da lista de discos geridos disponíveis. 

1. Clique em **Guardar** para anexar o disco gerido existente e atualizar a configuração VM:
   

## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Ligue-se ao Linux VM para montar o novo disco
Para a partilha, formato e montagem do seu novo disco para que o seu VM Linux possa usá-lo, SSH no seu VM. Para obter mais informações, veja [como utilizar SSH com Linux no Azure](mac-create-ssh-keys.md). O exemplo a seguir liga-se a um VM com o endereço IP público de *10.123.123.25* com o nome de utilizador *azureuser:* 

```bash
ssh azureuser@10.123.123.25
```

## <a name="find-the-disk"></a>Localizar o disco

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
sdc     3:0:0:0       4G
```

Neste exemplo, o disco que adicionei é `sdc` . É um LUN 0 e é de 4GB.

Para um exemplo mais complexo, aqui está o que vários discos de dados parecem no portal:

:::image type="content" source="./media/attach-disk-portal/create-new-md.png" alt-text="Reveja as definições do disco.":::

Na imagem, pode ver que existem 3 discos de dados: 4 GB em LUN 0, 16GB na LUN 1 e 32G na LUN 2.

Aqui está o que pode parecer `lsblk` usar:

```bash
sda     0:0:0:0      30G
├─sda1             29.9G /
├─sda14               4M
└─sda15             106M /boot/efi
sdb     1:0:1:0      14G
└─sdb1               14G /mnt
sdc     3:0:0:0       4G
sdd     3:0:0:1      16G
sde     3:0:0:2      32G
```

Pela saída do you pode ver que o disco de `lsblk` 4GB no LUN 0 é `sdc` , o disco de 16GB no LUN 1 é , e o disco `sdd` 32G em LUN 2 é `sde` .

### <a name="partition-a-new-disk"></a>Partição de um novo disco

Se estiver a utilizar um disco existente que contenha dados, salte para a montagem do disco. Se estiver a anexar um disco novo, tem de dividir o disco.

O `parted` utilitário pode ser usado para a divisão e para formatar um disco de dados.

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

Crie um diretório para montar o sistema de ficheiros utilizando `mkdir` . O exemplo a seguir cria um diretório `/datadrive` em:

```bash
sudo mkdir /datadrive
```

Utilize `mount` para montar o sistema de ficheiros. O exemplo a seguir monta a partição */dev/sdc1* até ao `/datadrive` ponto de montagem:

```bash
sudo mount /dev/sdc1 /datadrive
```

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

Usamos o nano editor, por isso, quando acabares de editar o ficheiro, usa `Ctrl+O` para escrever o ficheiro e sair do `Ctrl+X` editor.

> [!NOTE]
> Posteriormente, remover um disco de dados sem editar o fstab pode fazer com que o VM não arranque. A maioria das distribuições fornece ou as opções *de nofail* e/ou *nobootwait* fstab. Estas opções permitem o arranque de um sistema mesmo que o disco não consiga montar na hora do arranque. Consulte a documentação da sua distribuição para obter mais informações sobre estes parâmetros.
> 
> A *opção nofalil* garante que o VM começa mesmo que o sistema de ficheiros seja corrupto ou o disco não exista no momento do arranque. Sem esta opção, poderá encontrar comportamento como descrito em [Não SSH a Linux VM devido a erros do FSTAB](/archive/blogs/linuxonazure/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting)


## <a name="verify-the-disk"></a>Verifique o disco

Agora pode usar `lsblk` novamente para ver o disco e o ponto de montagem.

```bash
lsblk -o NAME,HCTL,SIZE,MOUNTPOINT | grep -i "sd"
```

A saída será semelhante à seguinte:

```bash
sda     0:0:0:0      30G
├─sda1             29.9G /
├─sda14               4M
└─sda15             106M /boot/efi
sdb     1:0:1:0      14G
└─sdb1               14G /mnt
sdc     3:0:0:0       4G
└─sdc1                4G /datadrive
```

Pode ver que `sdc` agora está montado em `/datadrive` .

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

## <a name="next-steps"></a>Passos seguintes
Também pode [anexar um disco de dados](add-disk.md) utilizando o Azure CLI.