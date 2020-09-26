---
title: Como redimensionar discos de gestão de volume lógico encriptados com encriptação do disco Azure
description: Este artigo fornece instruções para redimensionar discos encriptados ADE usando gestão lógica de volume
author: jofrance
ms.service: security
ms.topic: article
ms.author: jofrance
ms.date: 09/21/2020
ms.openlocfilehash: ba652b9424b8d5ce1b6a2c5b7d70b8fd9e999323
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91346314"
---
# <a name="how-to-resize-logical-volume-management-devices-encrypted-with-azure-disk-encryption"></a>Como redimensionar dispositivos de gestão de volume lógicos encriptados com encriptação do disco Azure

Este artigo é um processo passo a passo sobre como redimensionar discos de dados encriptados ADE usando ADE Gestão de Volume Lógico (LVM) no Linux, aplicável a vários cenários.

O processo aplica-se aos seguintes ambientes:

- Distribuição linux
    - RHEL 7 e superior
    - Ubuntu 16+
    - SUSE 12+
- Extensão de passe único de encriptação de disco Azure
- Extensão de duplo passe de encriptação de disco Azure

## <a name="considerations"></a>Considerações

Este documento pressupõe que:

1. Há uma configuração LVM existente.
   
   Consulte [a CONFVM configurada num Linux VM](configure-lvm.md) para obter mais informações sobre a configuração do LVM num Linux VM.

2. Os discos já estão encriptados utilizando a encriptação do disco Azure Check [Configure LVM na cript](how-to-configure-lvm-raid-on-crypt.md) para obter informações sobre como configurar LVM-on-Crypt.

3. Tem a experiência necessária para seguir estes exemplos.

4. Entende que a recomendação de utilização de discos de dados no Azure, como mencionado nos problemas com [os nomes](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-device-names-problems)dos dispositivos de resolução de problemas, é utilizar os caminhos /dev/disco/scsi1/.

## <a name="scenarios"></a>Cenários

Os procedimentos deste artigo aplicam-se aos seguintes cenários:

### <a name="for-traditional-lvm-and-lvm-on-crypt-configurations"></a>Para configurações tradicionais de LVM e LVM-On-Crypt

- Ampliação de um volume lógico quando há espaço disponível no VG

### <a name="for-traditional-lvm-encryption-the-logical-volumes-are-encrypted-not-the-whole-disk"></a>Para encriptação tradicional LVM (os volumes lógicos são encriptados, não todo o disco)

- Ampliação de um volume LVM tradicional adicionando um novo PV
- Ampliação de um volume LVM tradicional redimensionando um PV existente

### <a name="for-lvm-on-crypt-recommended-method-the-entire-disk-is-encrypted-not-only-the-logical-volume"></a>Para LVM-on-crypt (método recomendado, todo o disco é encriptado, não apenas o volume lógico)

- Ampliação de um LVM no volume de cripta adicionando um novo PV
- Estendendo um LVM no volume de cripta redimensionando um PV existente

> [!NOTE]
> Não é recomendada a mistura de encriptação LVM tradicional e LVM-on-Crypt no mesmo VM.

> [!NOTE]
> Estes exemplos utilizam os nomes pré-existentes para discos, volumes físicos, grupos de volume, volumes lógicos, sistemas de ficheiros, UUIDs e pontos de montagem, é necessário substituir os valores fornecidos nestes exemplos para se adaptar ao seu ambiente.

#### <a name="extending-a-logical-volume-when-theres-available-space-in-the-vg"></a>Ampliação de um volume lógico quando há espaço disponível no VG

O método tradicional usado para redimensionar volumes lógicos, pode ser aplicado a discos não encriptados, volumes encriptados tradicionais de LVM e configurações LVM-on-Crypt.

1. Verifique o tamanho atual do sistema de ficheiros que queremos aumentar:

    ``` bash
    df -h /mountpoint
    ```

    ![scenarioa-check-fs1](./media/disk-encryption/resize-lvm/001-resize-lvm-scenarioa-check-fs.png)

2. Verifique se o VG tem espaço suficiente para aumentar o LV

    ``` bash
    vgs
    ```

    ![scenarioa-check-vg](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgs.png)

    Também pode usar "vgdisplay"

    ``` bash
    vgdisplay vgname
    ```

    ![scenarioa-check-vgdisplay](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgdisplay.png)

3. Identificar que volume lógico precisa de ser redimensionado

    ``` bash
    lsblk
    ```

    ![scenarioa-check-lsblk1](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk1.png)

    Para o LVM-On-Crypt, a diferença está nesta saída, o que mostra que a camada encriptada está na camada encriptada que cobre todo o disco

    ![scenarioa-check-lsblk2](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk2.png)

4. Verifique o tamanho do volume lógico

    ``` bash
    lvdisplay lvname
    ```

    ![scenarioa-check-lvdisplay01](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lvdisplay01.png)

5. Aumente o tamanho do LV, utilizando "-r" para o redimensionar on-line do sistema de ficheiros

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![scenarioa-resize-lv](./media/disk-encryption/resize-lvm/003-resize-lvm-scenarioa-resize-lv.png)

6. Verifique os novos tamanhos para o LV e o sistema de ficheiros

    ``` bash
    df -h /mountpoint
    ```

    ![scenarioa-check-fs](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-fs.png)

    O novo tamanho é refletido, indica um redimensionado bem-sucedido do LV e do sistema de ficheiros

7. Pode verificar novamente as informações do LV para confirmar as alterações ao nível do LV

    ``` bash
    lvdisplay lvname
    ```

    ![scenarioa-check-lvdisplay2](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-lvdisplay2.png)

#### <a name="extending-a-traditional-lvm-volume-adding-a-new-pv"></a>Ampliação de um volume LVM tradicional adicionando um novo PV

Aplicável quando é necessário adicionar um disco novo para aumentar o tamanho do grupo de volume.

1. Verifique o tamanho atual do sistema de ficheiros que queremos aumentar:

    ``` bash
    df -h /mountpoint
    ```

    ![scenariob-check-fs](./media/disk-encryption/resize-lvm/005-resize-lvm-scenariob-check-fs.png)

2. Verifique a configuração atual do Volume Físico

    ``` bash
    pvs
    ```

    ![cenáriob-check-pvs](./media/disk-encryption/resize-lvm/006-resize-lvm-scenariob-check-pvs.png)

3. Verifique as informações atuais do VG

    ``` bash
    vgs
    ```

    ![cenáriob-check-vgs](./media/disk-encryption/resize-lvm/007-resize-lvm-scenariob-check-vgs.png)

4. Verifique a lista de discos atuais

    Os discos de dados devem ser identificados verificando os dispositivos em /dev/disk/azure/scsi1/

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![cenáriob-check-scs1](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-scs1.png)

5. Verifique a saída de Isblk 

    ``` bash
    lsbk
    ```

    ![cenáriob-check-lsblk](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-lsblk.png)

6. Fixe o novo disco ao VM

    Acompanhe o passo 4 do seguinte documento

   - [Anexar um disco a um VM](attach-disk-portal.md)

7. Depois de o disco estar ligado, verifique a lista de discos, note o novo disco

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![scenariob-check-scsi12](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-scsi12.png)

    ``` bash
    lsbk
    ```

    ![scenariob-check-lsblk12](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-lsblk1.png)

8. Criar um novo PV em cima do novo Disco de Dados

    ``` bash
    pvcreate /dev/newdisk
    ```

    ![cenáriob-pvcreate](./media/disk-encryption/resize-lvm/010-resize-lvm-scenariob-pvcreate.png)

    Este método utiliza todo o disco como um PV sem uma divisória, opcionalmente pode usar "fdisk" para criar uma partição e, em seguida, usar essa partição para o "pvcreate".

9. Verifique se o PV foi adicionado à lista de PV.

    ``` bash
    pvs
    ```

    ![cenáriob-check-pvs1](./media/disk-encryption/resize-lvm/011-resize-lvm-scenariob-check-pvs1.png)

10. Estenda o VG adicionando-lhe o novo PV

    ``` bash
    vgextend vgname /dev/newdisk
    ```

    ![cenáriob-vg-extend](./media/disk-encryption/resize-lvm/012-resize-lvm-scenariob-vgextend.png)

11. Verifique o novo tamanho do VG

    ``` bash
    vgs
    ```

    ![cenáriob-check-vgs1](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-vgs1.png)

12. Use o Lsblk para identificar quais LV precisa ser redimensionado

    ``` bash
    lsblk
    ```

    ![scenariob-check-lsblk1](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-lsblk1.png)

13. Alargar o tamanho LV usando "-r" para fazer um aumento on-line do sistema de ficheiros

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![cenáriob-lvextend](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-lvextend.png) 

14. Verifique os novos tamanhos do LV e do sistema de ficheiros

    ``` bash
    df -h /mountpoint
    ```

    ![scenariob-check-fs1](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-fs1.png)

    É importante saber que quando o ADE é usado nas configurações tradicionais de LVM, a camada encriptada é criada ao nível de LV, não ao nível do disco.

    Neste ponto, a camada encriptada é expandida para o novo disco.
    O disco de dados real não tem nenhuma configuração de encriptação ao nível da plataforma, pelo que o seu estado de encriptação não é atualizado.

    >[!NOTE]
    >Estas são algumas das razões pelas quais a LVM-on-Crypt é a abordagem recomendada. 

15. Consulte as informações de encriptação do portal:

    ![scenariob-check-portal1](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal1.png)

    É necessário adicionar um novo LV e ativar a extensão no VM para atualizar as definições de encriptação no disco.
    
16. Adicione um novo LV, crie um sistema de ficheiros nele, e adicione-o a /etc/fstab

17. Defina novamente a extensão de encriptação de forma a carimbar as definições de encriptação no novo disco de dados ao nível da plataforma.

    Exemplo:

    CLI

    ``` bash
    az vm encryption enable -g ${RGNAME} --name ${VMNAME} --disk-encryption-keyvault "<your-unique-keyvault-name>"
    ```

18. Consulte as informações de encriptação do portal:

    ![cenáriob-check-portal2](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal2.png)

19. Após a atualização das definições de encriptação, poderá eliminar o novo LV, também terá de eliminar a entrada de /etc/fstab e /etc/crypttab que foram criados para o mesmo.

    ![scenariob-delete-fstab-crypttab](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-delete-fstab-crypttab.png)

20. Desmonte o volume lógico

    ``` bash
    umount /mountpoint
    ```

21. Feche a camada encriptada do volume

    ``` bash
    cryptsetup luksClose /dev/vgname/lvname
    ```

22. Eliminar o LV

    ``` bash
    lvremove /dev/vgname/lvname
    ```

#### <a name="extending-a-traditional-lvm-volume-resizing-an-existing-pv"></a>Ampliação de um volume LVM tradicional redimensionando um PV existente

Certos cenários ou limitações requerem que redimensione um disco existente.

1. Identifique os seus discos encriptados

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![cenárioc-check-scsi1](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-scsi1.png)

    ``` bash
    lsblk -fs
    ```

    ![cenárioc-check-lsblk](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-lsblk.png)

2. Verifique as informações do pv

    ``` bash
    pvs
    ```

    ![cenárioc-check-pvs](./media/disk-encryption/resize-lvm/016-resize-lvm-scenarioc-check-pvs.png)

    Todo o espaço em todos os PVs é atualmente usado

3. Verifique as informações dos VGs

    ``` bash
    vgs
    vgdisplay -v vgname
    ```

    ![cenárioc-check-vgs](./media/disk-encryption/resize-lvm/017-resize-lvm-scenarioc-check-vgs.png)

4. Verifique os tamanhos do disco, pode usar fdisk ou lsblk para listar os tamanhos de unidade

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![cenárioc-check-fdisk](./media/disk-encryption/resize-lvm/018-resize-lvm-scenarioc-check-fdisk.png)

    Identificamos quais os PVs associados aos quais os LVs usando lsblk-fs, você pode identificá-lo também executando "lvdisplay"

    ``` bash
    lvdisplay --maps VG/LV
    lvdisplay --maps datavg/datalv1
    ```

    ![check-lvdisplay](./media/disk-encryption/resize-lvm/019-resize-lvm-scenarioc-check-lvdisplay.png)

    Neste caso em particular, todas as 4 unidades de dados fazem parte do mesmo VG e de um único LV, a sua configuração pode diferir deste exemplo.

5. Verifique a utilização do sistema de ficheiros atual:

    ``` bash
    df -h /datalvm*
    ```

    ![cenárioc-check-df](./media/disk-encryption/resize-lvm/020-resize-lvm-scenarioc-check-df.png)

6. Redimensionar o ou os discos de dados):

    Pode consultar [os discos de expansão Linux (apenas](expand-disks.md) consulte o redimensionado disco), pode utilizar o portal, CLI ou PowerShell para fazer este passo.

    >[!NOTE]
    >Por favor, considere que as operações de redimensionação em discos virtuais não podem ser realizadas com o VM em execução. Você precisará de negociar o seu VM para este passo

7. Quando os discos forem redimensionados para o valor necessário, inicie o VM e verifique os novos tamanhos utilizando fdisk

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![cenárioc-check-fdisk1](./media/disk-encryption/resize-lvm/021-resize-lvm-scenarioc-check-fdisk1.png)

    Neste caso particular/dev/sdd foi redimensionado de 5G para 20G

8. Verifique o tamanho atual do PV

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![cenárioc-check-pvdisplay](./media/disk-encryption/resize-lvm/022-resize-lvm-scenarioc-check-pvdisplay.png)
    
    Mesmo que o disco tenha sido redimensionado, o pv ainda tem o tamanho anterior.

9. Redimensionar o PV

    ``` bash
    pvresize /dev/resizeddisk
    ```

    ![cenárioc-check-pvresize](./media/disk-encryption/resize-lvm/023-resize-lvm-scenarioc-check-pvresize.png)


10. Verifique o tamanho do PV

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![cenárioc-check-pvdisplay1](./media/disk-encryption/resize-lvm/024-resize-lvm-scenarioc-check-pvdisplay1.png)

    Aplique o mesmo procedimento para todos os discos que pretende redimensionar.

11. Verifique as informações do VG

    ``` bash
    vgdisplay vgname
    ```

    ![scenarioc-check-vgdisplay1](./media/disk-encryption/resize-lvm/025-resize-lvm-scenarioc-check-vgdisplay1.png)

    O VG agora tem espaço a ser atribuído aos LVs

12. Redimensionar o LV

    ``` bash
    lvresize -r -L +5G vgname/lvname
    lvresize -r -l +100%FREE /dev/datavg/datalv01
    ```

    ![cenárioc-check-lvresize1](./media/disk-encryption/resize-lvm/031-resize-lvm-scenarioc-check-lvresize1.png)

13. Verifique o tamanho do FS

    ``` bash
    df -h /datalvm2
    ```

    ![cenárioc-check-df3](./media/disk-encryption/resize-lvm/032-resize-lvm-scenarioc-check-df3.png)

#### <a name="extending-an-lvm-on-crypt-volume-adding-a-new-pv"></a>Ampliação de um volume LVM-on-Crypt adicionando um novo PV

Este método segue de perto os passos da [Configure LVM na cripta](how-to-configure-lvm-raid-on-crypt.md) para adicionar um novo disco e configurá-lo sob uma configuração LVM-on-crypt.

Pode utilizar este método para adicionar espaço a um LV já existente ou, em vez disso, pode criar novos VGs ou LVs.

1. Verifique o tamanho atual do seu VG

    ``` bash
    vgdisplay vgname
    ```

    ![cenário-check-vg01](./media/disk-encryption/resize-lvm/033-resize-lvm-scenarioe-check-vg01.png)

2. Verifique o tamanho dos fs e lv que deseja aumentar

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![cenário-check-lv01](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-lv01.png)

    ``` bash
    df -h mountpoint
    ```

    ![cenário-check-fs01](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-fs01.png)

3. Adicione um novo disco de dados ao VM e identifique-o.

    Verifique os discos antes de adicionar o disco

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![cenário-check-newdisk01](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk01.png)

    Verifique os discos antes de adicionar o novo disco

    ``` bash
    lsblk
    ```

    ![cenário-check-newdisk002](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk02.png)

    Adicione um novo disco com PowerShell, o Azure CLI ou o portal Azure. Verifique como [anexar um disco](attach-disk-portal.md) para referência na adição de discos a um VM.

    Seguindo o esquema de nome de núcleo para os dispositivos, a nova unidade normalmente será atribuída à próxima carta disponível, neste caso em particular o novo disco adicionado é sdd.

4. Verifique os discos depois de adicionar o novo disco

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![cenário-check-newdisk02](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk02.png)-

    ``` bash
    lsblk
    ```

    ![cenário-check-newdisk003](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk03.png)

5. Criar um sistema de ficheiros em cima do disco recentemente adicionado

    Combine o disco recentemente adicionado aos dispositivos ligados em /dev/disco/azure/scsi1/

    ``` bash
    ls -la /dev/disk/azure/scsi1/
    ```

    ![cenário-check-newdisk03](./media/disk-encryption/resize-lvm/037-resize-lvm-scenarioe-check-newdisk03.png)

    ``` bash
    mkfs.ext4 /dev/disk/azure/scsi1/${disk}
    ```

    ![cenário-mkfs01](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-mkfs01.png)

6. Criar um novo ponto de montagem temporário para o novo disco adicionado

    ``` bash
    newmount=/data4
    mkdir ${newmount}
    ```

7. Adicione o sistema de ficheiros recentemente criado a /etc/fstab

    ``` bash
    blkid /dev/disk/azure/scsi1/lun4| awk -F\" '{print "UUID="$2" '${newmount}' "$4" defaults,nofail 0 0"}' >> /etc/fstab
    ```

8. Monte os novos fs criados usando o monte -a

    ``` bash
    mount -a
    ```

9. Verifique se o novo FS adicionado está montado

    ``` bash
    df -h
    ```

    ![resize-lvm-scenarioe-df](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df.png)

    ``` bash
    lsblk
    ```

    ![resize-lvm-scenarioe-lsblk](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk.png)

10. Relançar a encriptação previamente iniciada para unidades de dados

    Para o LVM-On-Crypt, a recomendação é utilizar o EncryptFormatAll caso contrário, uma dupla encriptação pode acontecer durante a definição de discos adicionais.

    Para obter informações sobre a utilização, consulte [Configure LVM na cript](how-to-configure-lvm-raid-on-crypt.md).

    Exemplo:

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

    Quando a encriptação estiver concluída, verá uma camada de cripta no disco recém-adicionado

    ``` bash
    lsblk
    ```

    ![cenário-lsblk2](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk2.png)

11. Desmonte a camada encriptada do novo disco

    ``` bash
    umount ${newmount}
    ```

12. Verifique as informações dos pvs atuais

    ``` bash
    pvs
    ```

    ![cenário-correntepvs](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-currentpvs.png)

13. Criar um PV em cima da camada encriptada do disco

    Seque o nome do dispositivo do comando lsblk anterior e adicione /dev/mapper na frente do nome do dispositivo para criar o pv

    ``` bash
    pvcreate /dev/mapper/mapperdevicename
    ```

    ![cenário-pvcreate](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-pvcreate.png)

    Você verá um aviso sobre limpar a assinatura ext4 fs atual, isto é esperado, responder a esta questão

14. Verifique se o novo PV foi adicionado à configuração lvm

    ``` bash
    pvs
    ```

    ![cenário-newpv](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-newpv.png)

15. Adicione o novo PV ao VG que precisa de aumentar

    ``` bash
    vgextend vgname /dev/mapper/nameofhenewpv
    ```

    ![cenário-vgextent](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgextent.png)

16. Verifique o novo tamanho e espaço livre do VG

    ``` bash
    vgdisplay vgname
    ```

    ![cenário-vgdisplay](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgdisplay.png)

    Note o aumento na contagem total de PE e no PE/tamanho gratuito

17. Aumente o tamanho do lv e do sistema de ficheiros utilizando a opção -r em lvextend (neste exemplo estamos a aproveitar o espaço total disponível no VG e a adicioná-lo ao volume lógico dado)

    ``` bash
    lvextend -r -l +100%FREE /dev/vgname/lvname
    ```

    ![cenário-lvextend](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvextend.png)

18. Verifique o tamanho do LV

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![cenário-lvdisplay](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvdisplay.png)

19. Verifique o tamanho do sistema de ficheiros que acabou de redimensionar

    ``` bash
    df -h mountpoint
    ```

    ![cenário-df1](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df1.png)

20. Verifique se a camada LVM é criada em cima da camada encriptada

    ``` bash
    lsblk
    ```

    ![cenário-lsblk3](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk3.png)

    A utilização de isblk sem opções mostrará os pontos de montagem várias vezes, uma vez que se classifica por dispositivo e volumes lógicos, é possível que queira utilizar lsblk -fs, -s inverte o tipo de modo que os pontos de montagem são mostrados uma vez, então os discos serão mostrados várias vezes.

    ``` bash
    lsblk -fs
    ```

    ![cenário-lsblk4](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk4.png)

#### <a name="extending-an-lvm-on-crypt-volume-resizing-an-existing-pv"></a>Estendendo um LVM no volume de cripta redimensionando um PV existente

1. Identifique os seus discos encriptados

    ``` bash
    lsblk
    ```

    ![cenáriof-lsblk01](./media/disk-encryption/resize-lvm/039-resize-lvm-scenariof-lsblk01.png)

    ``` bash
    lsblk -s
    ```

    ![cenáriof-lsblk012](./media/disk-encryption/resize-lvm/040-resize-lvm-scenariof-lsblk012.png)

2. Verifique as informações do seu pv

    ``` bash
    pvs
    ```

    ![cenáriof-pvs1](./media/disk-encryption/resize-lvm/041-resize-lvm-scenariof-pvs.png)

3. Verifique as suas informações vg

    ``` bash
    vgs
    ```

    ![cenáriof-vgs](./media/disk-encryption/resize-lvm/042-resize-lvm-scenariof-vgs.png)

4. Verifique as informações do seu lv

    ``` bash
    lvs
    ```

    ![cenáriof-lvs](./media/disk-encryption/resize-lvm/043-resize-lvm-scenariof-lvs.png)

5. Verifique a utilização do sistema de ficheiros

    ``` bash
    df -h /mountpoint(s)
    ```

    ![lvm-scenariof-fs](./media/disk-encryption/resize-lvm/044-resize-lvm-scenariof-fs.png)

6. Verifique os tamanhos dos seus discos

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![cenáriof-fdisk01](./media/disk-encryption/resize-lvm/045-resize-lvm-scenariof-fdisk01.png)

7. Redimensionar o disco de dados

    Pode consultar [os discos de expansão Linux](expand-disks.md) (apenas consulte o redimensionado disco), pode utilizar o portal, OCli ou o PowerShell para executar este passo.

    >[!NOTE]
    >Por favor, considere que as operações de redimensionação em discos virtuais não podem ser realizadas com o VM em execução. Você precisará de negociar o seu VM para este passo

8. Verifique os tamanhos dos seus discos

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![cenáriof-fdisk02](./media/disk-encryption/resize-lvm/046-resize-lvm-scenariof-fdisk02.png)

    Note que (neste caso) ambos os discos foram redimensionados de 2GB a 4GB, no entanto o tamanho FS, LV e PV permanece o mesmo.

9. Verifique o tamanho do carrinho de estar atual

    Lembre-se que no LVM-on-Crypt, o pv é o dispositivo /dev/mapper/ não o dispositivo /dev/sd*

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![cenários de pvs](./media/disk-encryption/resize-lvm/047-resize-lvm-scenariof-pvs.png)

10. Redimensionar o pv

    ``` bash
    pvresize /dev/mapper/devicemappername
    ```

    ![cenáriof-resize-pv](./media/disk-encryption/resize-lvm/048-resize-lvm-scenariof-resize-pv.png)

11. Verifique o tamanho do pv após a redimensionamento

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![cenáriof-pv](./media/disk-encryption/resize-lvm/049-resize-lvm-scenariof-pv.png)

12. Redimensione a camada encriptada no PV

    ``` bash
    cryptsetup resize /dev/mapper/devicemappername
    ```

    Aplique o mesmo procedimento para todos os discos que pretende redimensionar.

13. Verifique as suas informações vg

    ``` bash
    vgdisplay vgname
    ```

    ![cenáriof-vg](./media/disk-encryption/resize-lvm/050-resize-lvm-scenariof-vg.png)

    O VG agora tem espaço a ser atribuído aos LVs

14. Verifique as informações de lv

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![cenáriof-lv](./media/disk-encryption/resize-lvm/051-resize-lvm-scenariof-lv.png)

15. Verifique a utilização de fs

    ``` bash
    df -h /mountpoint
    ```

    ![cenáriof-fs](./media/disk-encryption/resize-lvm/052-resize-lvm-scenariof-fs.png)

16. Redimensionar o lv

    ``` bash
    lvresize -r -L +2G /dev/vgname/lvname
    ```

    ![cenáriof-lvresize](./media/disk-encryption/resize-lvm/053-resize-lvm-scenariof-lvresize.png)

    Estamos usando a opção -r para também executar o redimensionar FS

17. Verifique as informações de lv

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![cenáriof-lvsize](./media/disk-encryption/resize-lvm/054-resize-lvm-scenariof-lvsize.png)

18. Verifique a utilização do sistema de ficheiros

    ``` bash
    df -h /mountpoint
    ```

    ![criar sistema de ficheiros](./media/disk-encryption/resize-lvm/055-resize-lvm-scenariof-fs.png)

    Aplique o mesmo procedimento de redimensionamento a qualquer lv adicional que o exija

## <a name="next-steps"></a>Passos seguintes

- [Resolução de problemas do Azure Disk Encryption](disk-encryption-troubleshooting.md)
