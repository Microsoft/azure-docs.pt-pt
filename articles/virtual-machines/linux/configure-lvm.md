---
title: Configure LVM em uma máquina virtual executando Linux
description: Saiba como configurar lvm em Linux em Azure.
author: mimckitt
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 09/27/2018
ms.author: mimckitt
ms.subservice: disks
ms.openlocfilehash: 781ff1e6bda655ebd60d86e19375dcb6da051039
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2020
ms.locfileid: "78969733"
---
# <a name="configure-lvm-on-a-linux-vm-in-azure"></a>Configure LVM em um Linux VM em Azure
Este documento irá discutir como configurar o Gestor de Volume Lógico (LVM) na sua máquina virtual Azure. O LVM pode ser utilizado no disco OS ou nos discos de dados em VMs Azure, no entanto, por padrão, a maioria das imagens em nuvem não terá LVM configurado no disco OS. Os passos abaixo concentrar-se-ão na configuração do LVM para os seus discos de dados.

## <a name="linear-vs-striped-logical-volumes"></a>Linear vs. volumes lógicos listrados
O LVM pode ser usado para combinar uma série de discos físicos num único volume de armazenamento. Por padrão, o LVM irá geralmente criar volumes lógicos lineares, o que significa que o armazenamento físico é concatenado em conjunto. Neste caso, as operações de leitura/escrita serão normalmente enviadas apenas para um único disco. Em contraste, também podemos criar volumes lógicos listrados onde as leituras e os escritos são distribuídos por vários discos contidos no grupo de volume (semelhante ao RAID0). Por razões de desempenho, é provável que queira riscar os seus volumes lógicos para que as leituras e os escritos utilizem todos os seus discos de dados anexados.

Este documento descreverá como combinar vários discos de dados num único grupo de volume e, em seguida, criar um volume lógico listrado. Os passos abaixo são generalizados para trabalhar com a maioria das distribuições. Na maioria dos casos, os serviços públicos e os fluxos de trabalho para a gestão da LVM em Azure não são fundamentalmente diferentes dos outros ambientes. Como de costume, consulte também o seu fornecedor Linux para obter documentação e boas práticas para utilizar o LVM com a sua distribuição específica.

## <a name="attaching-data-disks"></a>Anexação de discos de dados
Normalmente, um deles vai querer começar com dois ou mais discos de dados vazios quando se utiliza LVM. Com base nas suas necessidades de IO, pode optar por anexar discos que são armazenados no nosso Armazenamento Standard, com até 500 IO/ps por disco ou o nosso armazenamento Premium com até 5000 IO/ps por disco. Este artigo não entrará em detalhes sobre como fornecer e anexar discos de dados a uma máquina virtual Linux. Consulte o artigo do Microsoft Azure [para obter](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) instruções detalhadas sobre como anexar um disco de dados vazio a uma máquina virtual Linux no Azure.

## <a name="install-the-lvm-utilities"></a>Instalar os utilitários LVM
* **Ubuntu**

    ```bash  
    sudo apt-get update
    sudo apt-get install lvm2
    ```

* **RHEL, CentOS e Oracle Linux**

    ```bash   
    sudo yum install lvm2
    ```

* **SLES 12 e abre SUSE**

    ```bash   
    sudo zypper install lvm2
    ```

* **SLES 11**

    ```bash   
    sudo zypper install lvm2
    ```

    No SLES11, deve também editar `/etc/sysconfig/lvm` e definir `LVM_ACTIVATED_ON_DISCOVERED` para "ativar":

    ```sh   
    LVM_ACTIVATED_ON_DISCOVERED="enable" 
    ```

## <a name="configure-lvm"></a>Configurar LVM
Neste guia assumimos que anexou três discos de dados, a que nos referiremos como `/dev/sdc`, `/dev/sdd` e `/dev/sde`. Estes caminhos podem não coincidir com os nomes do caminho do disco no seu VM. Pode executar '`sudo fdisk -l`' ou comando semelhante para listar os seus discos disponíveis.

1. Preparar os volumes físicos:

    ```bash    
    sudo pvcreate /dev/sd[cde]
    Physical volume "/dev/sdc" successfully created
    Physical volume "/dev/sdd" successfully created
    Physical volume "/dev/sde" successfully created
    ```

2. Criar um grupo de volume. Neste exemplo, chamamos o grupo de volume `data-vg01`:

    ```bash    
    sudo vgcreate data-vg01 /dev/sd[cde]
    Volume group "data-vg01" successfully created
    ```

3. Criar os volumes lógicos. O comando abaixo criaremos um único volume lógico chamado `data-lv01` abranger todo o grupo de volume, mas note que também é viável criar múltiplos volumes lógicos no grupo de volume.

    ```bash   
    sudo lvcreate --extents 100%FREE --stripes 3 --name data-lv01 data-vg01
    Logical volume "data-lv01" created.
    ```

4. Formatar o volume lógico

    ```bash  
    sudo mkfs -t ext4 /dev/data-vg01/data-lv01
    ```
   
   > [!NOTE]
   > Com o SLES11 use `-t ext3` em vez de ext4. O SLES11 apenas suporta o acesso apenas a sistemas de ficheiros ext4.

## <a name="add-the-new-file-system-to-etcfstab"></a>Adicione o novo sistema de ficheiros a /etc/fstab
> [!IMPORTANT]
> A edição indevida do ficheiro `/etc/fstab` pode resultar num sistema inabitável. Se não tiver a certeza, consulte a documentação da distribuição para obter informações sobre como editar corretamente este ficheiro. Recomenda-se também que seja criada uma cópia de segurança do ficheiro `/etc/fstab` antes da edição.

1. Crie o ponto de montagem desejado para o seu novo sistema de ficheiros, por exemplo:

    ```bash  
    sudo mkdir /data
    ```

2. Localizar o caminho lógico do volume

    ```bash    
    lvdisplay
    --- Logical volume ---
    LV Path                /dev/data-vg01/data-lv01
    ....
    ```

3. Abra `/etc/fstab` num editor de texto e adicione uma entrada para o novo sistema de ficheiros, por exemplo:

    ```bash    
    /dev/data-vg01/data-lv01  /data  ext4  defaults  0  2
    ```   
    Então, salve e feche `/etc/fstab`.

4. Teste se a entrada `/etc/fstab` está correta:

    ```bash    
    sudo mount -a
    ```

    Se este comando resultar numa mensagem de erro, verifique a sintaxe no ficheiro `/etc/fstab`.
   
    Em seguida, executar o comando `mount` para garantir que o sistema de ficheiros está montado:

    ```bash    
    mount
    ......
    /dev/mapper/data--vg01-data--lv01 on /data type ext4 (rw)
    ```

5. (Opcional) Parâmetros de arranque de segurança em `/etc/fstab`
   
    Muitas distribuições incluem os parâmetros de montagem `nobootwait` ou `nofail` que podem ser adicionados ao ficheiro `/etc/fstab`. Estes parâmetros permitem falhas na montagem de um determinado sistema de ficheiros e permitem que o sistema Linux continue a arrancar mesmo que não seja capaz de montar corretamente o sistema de ficheiros RAID. Consulte a documentação da sua distribuição para obter mais informações sobre estes parâmetros.
   
    Exemplo (Ubuntu):

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,nobootwait  0  2
    ```

## <a name="trimunmap-support"></a>Suporte TRIM/UNMAP
Alguns núcleos linux suportam operações TRIM/UNMAP para descartar blocos não utilizados no disco. Estas operações são principalmente úteis no armazenamento padrão para informar o Azure de que as páginas apagadas já não são válidas e podem ser descartadas. As páginas de devoluções podem economizar custos se criar ficheiros grandes e depois eliminá-las.

Existem duas formas de permitir o suporte trim no seu VM Linux. Como de costume, consulte a sua distribuição para obter a abordagem recomendada:

- Utilize a opção `discard` montagem em `/etc/fstab`, por exemplo:

    ```bash 
    /dev/data-vg01/data-lv01  /data  ext4  defaults,discard  0  2
    ```

- Em alguns casos, a opção `discard` pode ter implicações no desempenho. Em alternativa, pode executar o comando `fstrim` manualmente a partir da linha de comando, ou adicioná-lo ao seu crontab para executar regularmente:

    **Ubuntu**

    ```bash 
    # sudo apt-get install util-linux
    # sudo fstrim /datadrive
    ```

    **RHEL, CentOS e Oracle Linux**

    ```bash 
    # sudo yum install util-linux
    # sudo fstrim /datadrive
    ```
