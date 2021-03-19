---
title: Como redimensionar discos encriptados usando encriptação de disco Azure
description: Este artigo fornece instruções para redimensionar discos encriptados ADE utilizando uma gestão lógica do volume.
author: jofrance
ms.service: virtual-machines
ms.subservice: disks
ms.topic: article
ms.author: jofrance
ms.date: 09/21/2020
ms.openlocfilehash: 92e3be9111abe5712549ba85ef4550a9ac1473f9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104596812"
---
# <a name="how-to-resize-logical-volume-management-devices-that-use-azure-disk-encryption"></a>Como redimensionar dispositivos de gestão de volume lógicos que utilizam encriptação do disco Azure

Neste artigo, aprenderá a redimensionar discos de dados que utilizam encriptação do disco Azure. Para redimensionar os discos, utilizará a gestão lógica do volume (LVM) no Linux. Os passos aplicam-se a vários cenários.

Pode utilizar este processo de redimensionamento nos seguintes ambientes:

- Distribuição linux:
    - Red Hat Enterprise Linux (RHEL) 7 ou mais tarde
    - Ubuntu 16 ou mais tarde
    - SUSE 12 ou mais tarde
- Versões de encriptação de disco Azure: 
    - Extensão de passe único
    - Extensão de passe duplo

## <a name="prerequisites"></a>Pré-requisitos

Este artigo assume que tem:

- Uma configuração LVM existente. Para obter mais informações, consulte [Configure LVM num Linux VM](/previous-versions/azure/virtual-machines/linux/configure-lvm).

- Discos que já estão encriptados pela Encriptação do Disco Azure. Para obter mais informações, consulte [Configure LVM e RAID em dispositivos encriptados](how-to-configure-lvm-raid-on-crypt.md).

- Experimente usando Linux e LVM.

- Experimente a utilização *de /dev/disk/scsi1/* caminhos para discos de dados em Azure. Para obter mais informações, consulte [problemas de nome do dispositivo De resolução de problemas Linux VM](/troubleshoot/azure/virtual-machines/troubleshoot-device-names-problems). 

## <a name="scenarios"></a>Cenários

Os procedimentos deste artigo aplicam-se aos seguintes cenários:

- Configurações tradicionais de LVM e LVM-on-crypt
- Encriptação tradicional do LVM 
- LVM-on-crypt 

### <a name="traditional-lvm-and-lvm-on-crypt-configurations"></a>Configurações tradicionais de LVM e LVM-on-crypt

As configurações tradicionais de LVM e LVM-on-crypt estendem um volume lógico (LV) quando o grupo de volume (VG) tem espaço disponível.

### <a name="traditional-lvm-encryption"></a>Encriptação tradicional do LVM 

Na encriptação tradicional da LVM, os LVs são encriptados. O disco inteiro não está encriptado.

Ao utilizar a encriptação tradicional LVM, pode:

- Estenda o LV quando adicionar um novo volume físico (PV).
- Estenda o LV quando redimensionar um PV existente.

### <a name="lvm-on-crypt"></a>LVM-on-crypt 

O método recomendado para encriptação de disco é LVM-on-crypt. Este método encripta todo o disco, não apenas o LV.

Ao utilizar a LVM-on-crypt, pode: 

- Estenda o LV quando adicionar um novo PV.
- Estenda o LV quando redimensionar um PV existente.

> [!NOTE]
> Não recomendamos misturar encriptação LVM tradicional e cripta LVM no mesmo VM.

As seguintes secções fornecem exemplos de como utilizar LVM e LVM-on-crypt. Os exemplos usam valores pré-existentes para discos, PVs, VGs, LVs, sistemas de ficheiros, identificadores universalmente únicos (UUIDs) e pontos de montagem. Substitua estes valores por seus próprios valores para se adaptar ao seu ambiente.

#### <a name="extend-an-lv-when-the-vg-has-available-space"></a>Estender um LV quando o VG tiver espaço disponível

A forma tradicional de redimensionar os LVs é estender um LV quando o VG tem espaço disponível. Pode utilizar este método para discos não encriptados, volumes encriptados tradicionais de LVM e configurações LVM-on-crypt.

1. Verifique o tamanho atual do sistema de ficheiros que pretende aumentar:

    ``` bash
    df -h /mountpoint
    ```

    ![Screenshot mostrando código que verifica o tamanho do sistema de ficheiros. O comando e o resultado estão em destaque.](./media/disk-encryption/resize-lvm/001-resize-lvm-scenarioa-check-fs.png)

2. Verifique se o VG tem espaço suficiente para aumentar o LV:

    ``` bash
    vgs
    ```

    ![Screenshot mostrando o código que verifica o espaço no VG. O comando e o resultado estão em destaque.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgs.png)

    Também pode `vgdisplay` utilizar:

    ``` bash
    vgdisplay vgname
    ```

    ![Screenshot mostrando o código de exibição V G que verifica o espaço no VG. O comando e o resultado estão em destaque.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgdisplay.png)

3. Identificar quais LV precisa de ser redimensionado:

    ``` bash
    lsblk
    ```

    ![Screenshot mostrando o resultado do comando l s b l k. O comando e o resultado estão em destaque.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk1.png)

    Para a LVM-on-crypt, a diferença é que esta saída mostra que a camada encriptada está ao nível do disco.

    ![Screenshot mostrando o resultado do comando l s b l k. A saída está em destaque. Mostra a camada encriptada.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk2.png)

4. Verifique o tamanho do LV:

    ``` bash
    lvdisplay lvname
    ```

    ![Screenshot mostrando o código que verifica o tamanho do volume lógico. O comando e o resultado estão em destaque.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lvdisplay01.png)

5. Aumente o tamanho do LV utilizando `-r` para redimensionar o sistema de ficheiros online:

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![Screenshot mostrando o código que aumenta o tamanho do volume lógico. O comando e os resultados estão em destaque.](./media/disk-encryption/resize-lvm/003-resize-lvm-scenarioa-resize-lv.png)

6. Verifique os novos tamanhos para o LV e o sistema de ficheiros:

    ``` bash
    df -h /mountpoint
    ```

    ![Screenshot mostrando o código que verifica o tamanho do LV e do sistema de ficheiros. O comando e o resultado estão em destaque.](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-fs.png)

    A saída do tamanho indica que o LV e o sistema de ficheiros foram redimensionados com sucesso.

Pode verificar novamente as informações do LV para confirmar as alterações ao nível do LV:

``` bash
lvdisplay lvname
```

![Screenshot mostrando o código que confirma os novos tamanhos. Os tamanhos estão em destaque.](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-lvdisplay2.png)

#### <a name="extend-a-traditional-lvm-volume-by-adding-a-new-pv"></a>Alargar um volume LVM tradicional adicionando um novo PV

Quando precisar de adicionar um novo disco para aumentar o tamanho do VG, estenda o seu volume tradicional de LVM adicionando um novo PV.

1. Verifique o tamanho atual do sistema de ficheiros que pretende aumentar:

    ``` bash
    df -h /mountpoint
    ```

    ![Screenshot mostrando o código que verifica o tamanho atual de um sistema de ficheiros. O comando e o resultado estão em destaque.](./media/disk-encryption/resize-lvm/005-resize-lvm-scenariob-check-fs.png)

2. Verifique a configuração atual do PV:

    ``` bash
    pvs
    ```

    ![Screenshot mostrando o código que verifica a configuração atual do PV. O comando e o resultado estão em destaque.](./media/disk-encryption/resize-lvm/006-resize-lvm-scenariob-check-pvs.png)

3. Verifique as informações atuais do VG:

    ``` bash
    vgs
    ```

    ![Screenshot mostrando o código que verifica as informações atuais do grupo de volume. O comando e o resultado estão em destaque.](./media/disk-encryption/resize-lvm/007-resize-lvm-scenariob-check-vgs.png)

4. Verifique a lista de discos atuais. Identifique os discos de dados verificando os dispositivos em */dev/disco/azure/scsi1/*.

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![Screenshot mostrando o código que verifica a lista de discos atuais. O comando e os resultados estão em destaque.](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-scs1.png)

5. Verifique a saída `lsblk` de: 

    ``` bash
    lsbk
    ```

    ![Screenshot mostrando o código que verifica a saída de l s b l k. O comando e os resultados estão em destaque.](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-lsblk.png)

6. Fixe o novo disco ao VM seguindo as instruções [do Attach a data disk a um Linux VM](attach-disk-portal.md).

7. Verifique a lista de discos e note o novo disco.

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![Screenshot mostrando o código que verifica a lista de discos. Os resultados estão em destaque.](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-scsi12.png)

    ``` bash
    lsbk
    ```

    ![Screenshot mostrando o código que verifica a lista de discos utilizando l s b l k. O comando e o resultado estão em destaque.](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-lsblk1.png)

8. Criar um novo PV em cima do novo disco de dados:

    ``` bash
    pvcreate /dev/newdisk
    ```

    ![Screenshot mostrando o código que cria um novo PV. O resultado está em destaque.](./media/disk-encryption/resize-lvm/010-resize-lvm-scenariob-pvcreate.png)

    Este método usa todo o disco como um PV sem uma divisória. Em alternativa, pode utilizar `fdisk` para criar uma divisória e, em seguida, usar essa divisória para `pvcreate` .

9. Verifique se o PV foi adicionado à lista de PV:

    ``` bash
    pvs
    ```

    ![Screenshot mostrando o código que mostra a lista de volume físico. O resultado está em destaque.](./media/disk-encryption/resize-lvm/011-resize-lvm-scenariob-check-pvs1.png)

10. Estenda o VG adicionando-lhe o novo PV:

    ``` bash
    vgextend vgname /dev/newdisk
    ```

    ![Screenshot mostrando o código que estende o grupo de volume. O resultado está em destaque.](./media/disk-encryption/resize-lvm/012-resize-lvm-scenariob-vgextend.png)

11. Verifique o novo tamanho do VG:

    ``` bash
    vgs
    ```

    ![Screenshot mostrando o código que verifica o tamanho do grupo de volume. Os resultados estão em destaque.](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-vgs1.png)

12. Utilize `lsblk` para identificar o LV que precisa de ser redimensionado:

    ``` bash
    lsblk
    ```

    ![Screenshot mostrando o código que identifica o volume local que precisa de ser redimensionado. Os resultados estão em destaque.](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-lsblk1.png)

13. Alargar o tamanho do LV utilizando `-r` para aumentar o sistema de ficheiros on-line:

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![Screenshot mostrando código que aumenta o tamanho do sistema de ficheiros on-line. Os resultados estão em destaque.](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-lvextend.png) 

14. Verifique os novos tamanhos do LV e do sistema de ficheiros:

    ``` bash
    df -h /mountpoint
    ```

    ![Screenshot mostrando o código que verifica os tamanhos do volume local e do sistema de ficheiros. O comando e o resultado estão em destaque.](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-fs1.png)

    >[!IMPORTANT]
    >Quando a Encriptação de Dados Azure é usada nas configurações tradicionais de LVM, a camada encriptada é criada ao nível de LV, não ao nível do disco.
    >
    >Neste ponto, a camada encriptada é expandida para o novo disco. O disco de dados real não tem configurações de encriptação ao nível da plataforma, pelo que o seu estado de encriptação não é atualizado.
    >
    >Estas são algumas das razões pelas quais a LVM-on-crypt é a abordagem recomendada. 

15. Consulte as informações de encriptação do portal:

    ![Screenshot mostrando informações de encriptação no portal. O nome do disco e a encriptação estão destacados.](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal1.png)

    Para atualizar as definições de encriptação no disco, adicione um novo LV e ative a extensão no VM.
    
16. Adicione um novo LV, crie um sistema de ficheiros nele, e adicione-o a `/etc/fstab` .

17. Volte a definir a extensão de encriptação. Desta vez, irá carimbar as definições de encriptação no novo disco de dados ao nível da plataforma. Aqui está um exemplo da CLI:

    ``` bash
    az vm encryption enable -g ${RGNAME} --name ${VMNAME} --disk-encryption-keyvault "<your-unique-keyvault-name>"
    ```

18. Consulte as informações de encriptação do portal:

    ![Screenshot mostrando informações de encriptação no portal. O nome do disco e a informação de encriptação são realçados.](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal2.png)

Depois de atualizadas as definições de encriptação, pode eliminar o novo LV. Também elimine a entrada do `/etc/fstab` e `/etc/crypttab` que criou.

![Screenshot mostrando o código que elimina o novo volume lógico. Destacam-se o separador F S e o separador cript apagado.](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-delete-fstab-crypttab.png)

Siga estes passos para terminar a limpeza:

1. Desmonte o LV:

    ``` bash
    umount /mountpoint
    ```

1. Feche a camada encriptada do volume:

    ``` bash
    cryptsetup luksClose /dev/vgname/lvname
    ```

1. Eliminar o LV:

    ``` bash
    lvremove /dev/vgname/lvname
    ```

#### <a name="extend-a-traditional-lvm-volume-by-resizing-an-existing-pv"></a>Prolongar um volume LVM tradicional redimensionando um PV existente

Em alguns cenários, as suas limitações podem exigir que redimensione um disco existente. Eis como:

1. Identifique os seus discos encriptados:

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![Screenshot mostrando o código que identifica discos encriptados. Os resultados estão em destaque.](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-scsi1.png)

    ``` bash
    lsblk -fs
    ```

    ![Screenshot mostrando código alternativo que identifica discos encriptados. Os resultados estão em destaque.](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-lsblk.png)

2. Verifique as informações do PV:

    ``` bash
    pvs
    ```

    ![Screenshot mostrando o código que verifica informações sobre o volume físico. Os resultados estão em destaque.](./media/disk-encryption/resize-lvm/016-resize-lvm-scenarioc-check-pvs.png)

    Os resultados na imagem mostram que todo o espaço em todos os PVs é atualmente usado.

3. Verifique as informações do VG:

    ``` bash
    vgs
    vgdisplay -v vgname
    ```

    ![Screenshot mostrando o código que verifica informações sobre o grupo de volume. Os resultados estão em destaque.](./media/disk-encryption/resize-lvm/017-resize-lvm-scenarioc-check-vgs.png)

4. Verifique os tamanhos do disco. Pode utilizar `fdisk` ou `lsblk` listar os tamanhos de unidade.

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![Screenshot mostrando o código que verifica os tamanhos do disco. Os resultados estão em destaque.](./media/disk-encryption/resize-lvm/018-resize-lvm-scenarioc-check-fdisk.png)

    Aqui identificámos quais os PVs associados aos quais os LVs `lsblk -fs` utilizam. Pode identificar as associações `lvdisplay` correndo.

    ``` bash
    lvdisplay --maps VG/LV
    lvdisplay --maps datavg/datalv1
    ```

    ![Screenshot mostrando uma forma alternativa de identificar associações de volume físico com volumes locais. Os resultados estão em destaque.](./media/disk-encryption/resize-lvm/019-resize-lvm-scenarioc-check-lvdisplay.png)

    Neste caso, todas as quatro unidades de dados fazem parte do mesmo VG e de um único LV. A sua configuração pode diferir.

5. Verifique a utilização do sistema de ficheiros atuais:

    ``` bash
    df -h /datalvm*
    ```

    ![Screenshot mostrando o código que verifica a utilização do sistema de ficheiros. O comando e os resultados estão em destaque.](./media/disk-encryption/resize-lvm/020-resize-lvm-scenarioc-check-df.png)

6. Redimensione os discos de dados seguindo as instruções no [Expande um disco gerido a Azure](expand-disks.md#expand-an-azure-managed-disk). Pode utilizar o portal, o CLI ou o PowerShell.

    >[!IMPORTANT]
    >Não é possível redimensionar discos virtuais enquanto o VM está em funcionamento. Translocar o seu VM para este passo.

7. Inicie o VM e verifique os novos tamanhos utilizando `fdisk` .

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![Screenshot mostrando o código que verifica o tamanho do disco. O resultado está em destaque.](./media/disk-encryption/resize-lvm/021-resize-lvm-scenarioc-check-fdisk1.png)

    Neste caso, `/dev/sdd` foi redimensionado de 5 G para 20 G.

8. Verifique o tamanho atual do PV:

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![Screenshot mostrando o código que verifica o tamanho do P V. O resultado está em destaque.](./media/disk-encryption/resize-lvm/022-resize-lvm-scenarioc-check-pvdisplay.png)
    
    Apesar de o disco ter sido redimensionado, o PV ainda tem o tamanho anterior.

9. Redimensionar o PV:

    ``` bash
    pvresize /dev/resizeddisk
    ```

    ![Screenshot mostrando o código que redimensiona o volume físico. O resultado está em destaque.](./media/disk-encryption/resize-lvm/023-resize-lvm-scenarioc-check-pvresize.png)


10. Verifique o tamanho do PV:

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![Screenshot mostrando o código que verifica o tamanho do volume físico. O resultado está em destaque.](./media/disk-encryption/resize-lvm/024-resize-lvm-scenarioc-check-pvdisplay1.png)

    Aplique o mesmo procedimento para todos os discos que pretende redimensionar.

11. Verifique a informação do VG.

    ``` bash
    vgdisplay vgname
    ```

    ![Screenshot mostrando o código que verifica as informações para o grupo de volume. O resultado está em destaque.](./media/disk-encryption/resize-lvm/025-resize-lvm-scenarioc-check-vgdisplay1.png)

    O VG agora tem espaço suficiente para ser atribuído aos LVs.

12. Redimensionar o LV:

    ``` bash
    lvresize -r -L +5G vgname/lvname
    lvresize -r -l +100%FREE /dev/datavg/datalv01
    ```

    ![Screenshot mostrando o código que redimensiona o L V. Os resultados estão em destaque.](./media/disk-encryption/resize-lvm/031-resize-lvm-scenarioc-check-lvresize1.png)

13. Verifique o tamanho do sistema de ficheiros:

    ``` bash
    df -h /datalvm2
    ```

    ![Screenshot mostrando o código que verifica o tamanho do sistema de ficheiros. O resultado está em destaque.](./media/disk-encryption/resize-lvm/032-resize-lvm-scenarioc-check-df3.png)

#### <a name="extend-an-lvm-on-crypt-volume-by-adding-a-new-pv"></a>Estenda um volume LVM-on-crypt adicionando um novo PV

Também pode estender um volume LVM-on-crypt adicionando um novo PV. Este método segue de perto os passos em [Configure LVM e RAID em dispositivos encriptados](how-to-configure-lvm-raid-on-crypt.md#general-steps). Consulte as secções que explicam como adicionar um novo disco e instalá-lo numa configuração LVM-on-crypt.

Pode utilizar este método para adicionar espaço a um LV existente. Ou pode criar novos VGs ou LVs.

1. Verifique o tamanho atual do seu VG:

    ``` bash
    vgdisplay vgname
    ```

    ![Screenshot mostrando o código que verifica o tamanho do grupo de volume. Os resultados estão em destaque.](./media/disk-encryption/resize-lvm/033-resize-lvm-scenarioe-check-vg01.png)

2. Verifique o tamanho do sistema de ficheiros e LV que pretende expandir:

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![Screenshot mostrando o código que verifica o tamanho do volume local. Os resultados estão em destaque.](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-lv01.png)

    ``` bash
    df -h mountpoint
    ```

    ![Screenshot mostrando o código que verifica o tamanho do sistema de ficheiros. O resultado está em destaque.](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-fs01.png)

3. Adicione um novo disco de dados ao VM e identifique-o.

    Antes de adicionar o novo disco, verifique os discos:

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![Screenshot mostrando o código que verifica o tamanho dos discos. O resultado está em destaque.](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk01.png)

    Aqui está outra forma de verificar os discos antes de adicionar o novo disco:

    ``` bash
    lsblk
    ```

    ![Screenshot mostrando um código alternativo que verifica o tamanho dos discos. Os resultados estão em destaque.](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk02.png)

    Para adicionar o novo disco, pode utilizar o PowerShell, o Azure CLI ou o portal Azure. Para obter mais informações, consulte [anexar um disco de dados a um Linux VM](attach-disk-portal.md).

    O sistema de nomes de núcleo aplica-se ao dispositivo recém-adicionado. Uma nova unidade é normalmente atribuída a próxima carta disponível. Neste caso, o disco adicionado é `sdd` .

4. Verifique os discos para se certificar de que o novo disco foi adicionado:

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![Screenshot mostrando o código que lista os discos. Os resultados estão em destaque.](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk02.png)

    ``` bash
    lsblk
    ```

    ![Screenshot mostrando o disco recém-adicionado na saída.](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk03.png)

5. Crie um sistema de ficheiros em cima do disco recentemente adicionado. Combine o disco com os dispositivos ligados `/dev/disk/azure/scsi1/` .

    ``` bash
    ls -la /dev/disk/azure/scsi1/
    ```

    ![Screenshot mostrando o código que cria um sistema de ficheiros. Os resultados estão em destaque.](./media/disk-encryption/resize-lvm/037-resize-lvm-scenarioe-check-newdisk03.png)

    ``` bash
    mkfs.ext4 /dev/disk/azure/scsi1/${disk}
    ```

    ![Screenshot mostrando código adicional que cria um sistema de ficheiros e corresponde ao disco com os dispositivos ligados. Os resultados estão em destaque.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-mkfs01.png)

6. Crie um ponto de montagem temporário para o novo disco adicionado:

    ``` bash
    newmount=/data4
    mkdir ${newmount}
    ```

7. Adicione o sistema de ficheiros recentemente criado a `/etc/fstab` .

    ``` bash
    blkid /dev/disk/azure/scsi1/lun4| awk -F\" '{print "UUID="$2" '${newmount}' "$4" defaults,nofail 0 0"}' >> /etc/fstab
    ```

8. Monte o sistema de ficheiros recém-criado:

    ``` bash
    mount -a
    ```

9. Verifique se o novo sistema de ficheiros está montado:

    ``` bash
    df -h
    ```

    ![Screenshot mostrando o código que verifica que o sistema de ficheiros está montado. O resultado está em destaque.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df.png)

    ``` bash
    lsblk
    ```

    ![Screenshot mostrando código adicional que verifica que o sistema de ficheiros está montado. O resultado está em destaque.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk.png)

10. Reinicie a encriptação que começou anteriormente para unidades de dados.

    >[!TIP]
    >Para a cripta LVM-on-crypt, recomendamos que utilize `EncryptFormatAll` . Caso contrário, poderá ver uma dupla encriptação enquanto define discos adicionais.
    >
    >Para obter mais informações, consulte [Configure LVM e RAID em dispositivos encriptados](how-to-configure-lvm-raid-on-crypt.md).

    Eis um exemplo:

    ``` bash
    az vm encryption enable \
    --resource-group ${RGNAME} \
    --name ${VMNAME} \
    --disk-encryption-keyvault ${KEYVAULTNAME} \
    --key-encryption-key ${KEYNAME} \
    --key-encryption-keyvault ${KEYVAULTNAME} \
    --volume-type "DATA" \
    --encrypt-format-all \
    -o table
    ```

    Quando a encriptação termina, vê-se uma camada de cripta no disco recém-adicionado:

    ``` bash
    lsblk
    ```

    ![Screenshot mostrando o código que verifica a camada de cripta. O resultado está em destaque.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk2.png)

11. Desmonte a camada encriptada do novo disco:

    ``` bash
    umount ${newmount}
    ```

12. Consulte as informações atuais do PV:

    ``` bash
    pvs
    ```

    ![Screenshot mostrando o código que verifica informações sobre o volume físico. O resultado está em destaque.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-currentpvs.png)

13. Crie um PV em cima da camada encriptada do disco. Pegue o nome do dispositivo do `lsblk` comando anterior. Adicione um `/dev/` mapper na frente do nome do dispositivo para criar o PV:

    ``` bash
    pvcreate /dev/mapper/mapperdevicename
    ```

    ![Screenshot mostrando o código que cria um volume físico na camada encriptada. Os resultados estão em destaque.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-pvcreate.png)

    Vê um aviso sobre limpar a `ext4 fs` assinatura atual. Este aviso é esperado. Responda a esta pergunta `y` com.

14. Verifique se o novo PV foi adicionado à configuração LVM:

    ``` bash
    pvs
    ```

    ![Screenshot mostrando o código que verifica que o volume físico foi adicionado à configuração LVM. O resultado está em destaque.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-newpv.png)

15. Adicione o novo PV ao VG que precisa de aumentar.

    ``` bash
    vgextend vgname /dev/mapper/nameofhenewpv
    ```

    ![Screenshot mostrando o código que adiciona um volume físico a um grupo de volume. Os resultados estão em destaque.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgextent.png)

16. Verifique o novo tamanho e espaço livre do VG:

    ``` bash
    vgdisplay vgname
    ```

    ![Screenshot mostrando o código que verifica o tamanho e o espaço livre do grupo de volume. Os resultados estão em destaque.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgdisplay.png)

    Note o aumento da `Total PE` contagem e do `Free PE / Size` .

17. Aumente o tamanho do LV e do sistema de ficheiros. Utilize a `-r` opção em `lvextend` . Neste exemplo, estamos adicionando o espaço total disponível no VG ao LV dado.

    ``` bash
    lvextend -r -l +100%FREE /dev/vgname/lvname
    ```

    ![Screenshot mostrando o código que aumenta o tamanho do volume local e do sistema de ficheiros. Os resultados estão em destaque.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvextend.png)

Siga os próximos passos para verificar as suas alterações.

1. Verifique o tamanho do LV:

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![Screenshot mostrando o código que verifica o novo tamanho do volume local. Os resultados estão em destaque.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvdisplay.png)

1. Verifique o novo tamanho do sistema de ficheiros:

    ``` bash
    df -h mountpoint
    ```

    ![Screenshot mostrando o código que verifica o novo tamanho do sistema de ficheiros. O resultado está em destaque.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df1.png)

1. Verifique se a camada LVM está em cima da camada encriptada:

    ``` bash
    lsblk
    ```

    ![Screenshot mostrando o código que verifica que a camada LVM está em cima da camada encriptada. O resultado está em destaque.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk3.png)

    Se utilizar `lsblk` sem opções, verá os pontos de montagem várias vezes. O comando classifica por dispositivo e LVs. 

    É melhor `lsblk -fs` usar. Neste comando, `-fs` inverte-se a ordem de classificação de modo a que os pontos de montagem sejam mostrados uma vez. Os discos são mostrados várias vezes.

    ``` bash
    lsblk -fs
    ```

    ![Screenshot mostrando código alternativo que verifica que a camada LVM está em cima da camada encriptada. O resultado está em destaque.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk4.png)

#### <a name="extend-an-lvm-on-a-crypt-volume-by-resizing-an-existing-pv"></a>Estenda um LVM em um volume de cripta redimensionando um PV existente

1. Identifique os seus discos encriptados:

    ``` bash
    lsblk
    ```

    ![Screenshot mostrando o código que identifica os discos encriptados. Os resultados estão em destaque.](./media/disk-encryption/resize-lvm/039-resize-lvm-scenariof-lsblk01.png)

    ``` bash
    lsblk -s
    ```

    ![Screenshot mostrando código alternativo que identifica os discos encriptados. Os resultados estão em destaque.](./media/disk-encryption/resize-lvm/040-resize-lvm-scenariof-lsblk012.png)

2. Consulte as informações do seu PV:

    ``` bash
    pvs
    ```

    ![Screenshot mostrando o código que verifica informações para volumes físicos. Os resultados estão em destaque.](./media/disk-encryption/resize-lvm/041-resize-lvm-scenariof-pvs.png)

3. Consulte as informações do VG:

    ``` bash
    vgs
    ```

    ![Screenshot mostrando o código que verifica informações para grupos de volume. Os resultados estão em destaque.](./media/disk-encryption/resize-lvm/042-resize-lvm-scenariof-vgs.png)

4. Consulte as informações do seu VL:

    ``` bash
    lvs
    ```

    ![Screenshot mostrando o código que verifica as informações para o volume local. O resultado está em destaque.](./media/disk-encryption/resize-lvm/043-resize-lvm-scenariof-lvs.png)

5. Verifique a utilização do sistema de ficheiros:

    ``` bash
    df -h /mountpoint(s)
    ```

    ![Screenshot mostrando o código que verifica a quantidade de sistema de ficheiros que está a ser utilizado. Os resultados estão em destaque.](./media/disk-encryption/resize-lvm/044-resize-lvm-scenariof-fs.png)

6. Verifique os tamanhos dos seus discos:

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![Screenshot mostrando o código que verifica o tamanho dos discos. Os resultados estão em destaque.](./media/disk-encryption/resize-lvm/045-resize-lvm-scenariof-fdisk01.png)

7. Redimensione o disco de dados. Pode utilizar o portal, CLI ou PowerShell. Para obter mais informações, consulte a secção de redimensionação de discos [em Disco Virtual Expandir discos rígidos virtuais num Linux VM](expand-disks.md#expand-an-azure-managed-disk). 

    >[!IMPORTANT]
    >Não é possível redimensionar discos virtuais enquanto o VM está em funcionamento. Translocar o seu VM para este passo.

8. Verifique os tamanhos dos discos:

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![Screenshot mostrando código que verifica os tamanhos do disco. Os resultados estão em destaque.](./media/disk-encryption/resize-lvm/046-resize-lvm-scenariof-fdisk02.png)

    Neste caso, ambos os discos foram redimensionados de 2 GB a 4 GB. Mas o tamanho do sistema de ficheiros, LV e PV permanecem os mesmos.

9. Verifique o tamanho atual do PV. Lembre-se que na LVM-on-crypt, o PV é o `/dev/mapper/` dispositivo, não o `/dev/sd*` dispositivo.

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![Screenshot mostrando o código que verifica o tamanho do volume físico atual. Os resultados estão em destaque.](./media/disk-encryption/resize-lvm/047-resize-lvm-scenariof-pvs.png)

10. Redimensionar o PV:

    ``` bash
    pvresize /dev/mapper/devicemappername
    ```

    ![Screenshot mostrando o código que redimensiona o volume físico. Os resultados estão em destaque.](./media/disk-encryption/resize-lvm/048-resize-lvm-scenariof-resize-pv.png)

11. Verifique o novo tamanho do PV:

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![Screenshot mostrando o código que verifica o tamanho do volume físico. Os resultados estão em destaque.](./media/disk-encryption/resize-lvm/049-resize-lvm-scenariof-pv.png)

12. Redimensionar a camada encriptada no PV:

    ``` bash
    cryptsetup resize /dev/mapper/devicemappername
    ```

    Aplique o mesmo procedimento para todos os discos que pretende redimensionar.

13. Consulte as informações do VG:

    ``` bash
    vgdisplay vgname
    ```

    ![Screenshot mostrando o código que verifica as informações para o grupo de volume. Os resultados estão em destaque.](./media/disk-encryption/resize-lvm/050-resize-lvm-scenariof-vg.png)

    O VG agora tem espaço suficiente para ser atribuído aos LVs.

14. Consulte as informações sobre o LV:

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![Screenshot mostrando o código que verifica as informações para o volume local. Os resultados estão em destaque.](./media/disk-encryption/resize-lvm/051-resize-lvm-scenariof-lv.png)

15. Verifique a utilização do sistema de ficheiros:

    ``` bash
    df -h /mountpoint
    ```

    ![Screenshot mostrando o código que verifica a utilização do sistema de ficheiros. Os resultados estão em destaque.](./media/disk-encryption/resize-lvm/052-resize-lvm-scenariof-fs.png)

16. Redimensionar o LV:

    ``` bash
    lvresize -r -L +2G /dev/vgname/lvname
    ```

    ![Screenshot mostrando o código que redimensiona o volume local. Os resultados estão em destaque.](./media/disk-encryption/resize-lvm/053-resize-lvm-scenariof-lvresize.png)

    Aqui utilizamos a `-r` opção de redimensionar também o sistema de ficheiros.

17. Consulte as informações sobre o LV:

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![Screenshot mostrando o código que obtém informações sobre o volume local. Os resultados estão em destaque.](./media/disk-encryption/resize-lvm/054-resize-lvm-scenariof-lvsize.png)

18. Verifique a utilização do sistema de ficheiros:

    ``` bash
    df -h /mountpoint
    ```

    ![Screenshot mostrando o código que verifica a utilização do sistema de ficheiros. Os resultados estão em destaque.](./media/disk-encryption/resize-lvm/055-resize-lvm-scenariof-fs.png)

Aplique o mesmo procedimento de redimensionamento em qualquer outro LV que o exija.

## <a name="next-steps"></a>Passos seguintes

[Encriptação do disco Azure de resolução de problemas](disk-encryption-troubleshooting.md)