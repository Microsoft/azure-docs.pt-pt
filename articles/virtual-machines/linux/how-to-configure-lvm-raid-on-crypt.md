---
title: Configure LVM e RAID em dispositivos encriptados - Encriptação do Disco Azure
description: Este artigo fornece instruções para configurar LVM e RAID em dispositivos encriptados para Os VMs Linux.
author: jofrance
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: how-to
ms.author: jofrance
ms.date: 03/17/2020
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: bdd897e76df941130e3acdf9c30ea8edd41147e9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104601929"
---
# <a name="configure-lvm-and-raid-on-encrypted-devices"></a>Configure LVM e RAID em dispositivos encriptados

Este artigo é um processo passo a passo para como executar a Gestão lógica do volume (LVM) e RAID em dispositivos encriptados. O processo aplica-se aos seguintes ambientes:

- Distribuição linux
    - RHEL 7.6+
    - Ubuntu 18.04+
    - SUSE 12+
- Extensão de passe único de encriptação de disco Azure
- Extensão de duplo passe de encriptação de disco Azure


## <a name="scenarios"></a>Cenários

Os procedimentos deste artigo apoiam os seguintes cenários:  

- Configure o LVM em cima de dispositivos encriptados (LVM-on-crypt)
- Configure o RAID em cima de dispositivos encriptados (RAID-on-crypt)

Depois de o dispositivo ou dispositivos subjacentes serem encriptados, então pode criar as estruturas LVM ou RAID em cima dessa camada encriptada. 

Os volumes físicos (PVs) são criados em cima da camada encriptada. Os volumes físicos são utilizados para criar o grupo de volume. Cria-se os volumes e adiciona-se as entradas necessárias em /etc/fstab. 

![Diagrama das camadas das estruturas LVM](./media/disk-encryption/lvm-raid-on-crypt/000-lvm-raid-crypt-diagram.png)

Da mesma forma, o dispositivo RAID é criado em cima da camada encriptada nos discos. Um sistema de ficheiros é criado em cima do dispositivo RAID e adicionado a /etc/fstab como um dispositivo regular.

## <a name="considerations"></a>Considerações

Recomendamos que utilize LVM-on-crypt. RAID é uma opção quando o LVM não pode ser usado devido a aplicações específicas ou limitações ambientais.

Utilizará a opção **EncryptFormatAll.** Para obter mais informações sobre esta opção, consulte [a funcionalidade EncryptFormatAll para discos de dados em VMs Linux](./disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms).

Embora possa usar este método quando também está a encriptar o SISTEMA, estamos apenas a encriptar unidades de dados aqui.

Os procedimentos pressupõem que já reviu os pré-requisitos em [cenários de encriptação de disco Azure em VMs Linux](./disk-encryption-linux.md) e em [Quickstart: Criar e encriptar um Linux VM com o Azure CLI](./disk-encryption-cli-quickstart.md).

A versão Azure Disk Encryption dual-pass está num caminho de depreciação e não deve mais ser usada em novas encriptações.

## <a name="general-steps"></a>Etapas gerais

Quando estiver a utilizar as configurações "on-crypt", utilize o processo descrito nos seguintes procedimentos.

>[!NOTE] 
>Estamos a usar variáveis ao longo do artigo. Substitua os valores em conformidade.

### <a name="deploy-a-vm"></a>Implementar uma VM 
Os seguintes comandos são opcionais, mas recomendamos que os aplique numa máquina virtual recém-implantada (VM).

PowerShell:

```powershell
New-AzVm -ResourceGroupName ${RGNAME} `
-Name ${VMNAME} `
-Location ${LOCATION} `
-Size ${VMSIZE} `
-Image ${OSIMAGE} `
-Credential ${creds} `
-Verbose
```
Azure CLI:

```azurecli
az vm create \
-n ${VMNAME} \
-g ${RGNAME} \
--image ${OSIMAGE} \
--admin-username ${username} \
--admin-password ${password} \
-l ${LOCATION} \
--size ${VMSIZE} \
-o table
```
### <a name="attach-disks-to-the-vm"></a>Anexar discos ao VM
Repita os seguintes comandos para `$N` o número de discos novos que pretende anexar ao VM.

PowerShell:

```powershell
$storageType = 'Standard_LRS'
$dataDiskName = ${VMNAME} + '_datadisk0'
$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $LOCATION -CreateOption Empty -DiskSizeGB 5
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName ${RGNAME}
$vm = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME} 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 0
Update-AzVM -VM ${VM} -ResourceGroupName ${RGNAME}
```

Azure CLI:

```azurecli
az vm disk attach \
-g ${RGNAME} \
--vm-name ${VMNAME} \
--name ${VMNAME}datadisk1 \
--size-gb 5 \
--new \
-o table
```

### <a name="verify-that-the-disks-are-attached-to-the-vm"></a>Verifique se os discos estão ligados ao VM
PowerShell:
```powershell
$VM = Get-AzVM -ResourceGroupName ${RGNAME} -Name ${VMNAME}
$VM.StorageProfile.DataDisks | Select-Object Lun,Name,DiskSizeGB
```
![Lista de discos anexados em PowerShell](./media/disk-encryption/lvm-raid-on-crypt/001-lvm-raid-check-disks-powershell.png)

Azure CLI:

```azurecli
az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks -o table
```
![Lista de discos anexados no Azure CLI](./media/disk-encryption/lvm-raid-on-crypt/002-lvm-raid-check-disks-cli.png)

Portal:

![Lista de discos anexos no portal](./media/disk-encryption/lvm-raid-on-crypt/003-lvm-raid-check-disks-portal.png)

SO:

```bash
lsblk 
```
![Lista de discos anexados no SISTEMA](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="configure-the-disks-to-be-encrypted"></a>Configure os discos a encriptar
Esta configuração é feita ao nível do sistema operativo. Os discos correspondentes são configurados para uma encriptação tradicional através da encriptação do disco Azure:

- Os sistemas de ficheiros são criados em cima dos discos.
- São criados pontos de montagem temporários para montar os sistemas de ficheiros.
- Os sistemas de ficheiros são configurados em /etc/fstab para serem montados na hora do arranque.

Verifique a carta do dispositivo atribuída aos novos discos. Neste exemplo, estamos a usar quatro discos de dados.

```bash
lsblk 
```
![Discos de dados ligados ao SISTEMA](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="create-a-file-system-on-top-of-each-disk"></a>Criar um sistema de ficheiros em cima de cada disco
Este comando imitiza a criação de um sistema de ficheiros ext4 em cada disco definido na parte "in" do ciclo "para".

```bash
for disk in c d e f; do echo mkfs.ext4 -F /dev/sd${disk}; done |bash
```
![Criação de um sistema de ficheiros ext4](./media/disk-encryption/lvm-raid-on-crypt/005-lvm-raid-create-temp-fs.png)

Encontre o identificador universalmente único (UUID) dos sistemas de ficheiros que criou recentemente, crie uma pasta temporária, adicione as entradas correspondentes em /etc/fstab e monte todos os sistemas de ficheiros.

Este comando também itera em cada disco definido na parte "in" do ciclo "para":

```bash
for disk in c d e f; do diskuuid="$(blkid -s UUID -o value /dev/sd${disk})"; \
mkdir /tempdata${disk}; \
echo "UUID=${diskuuid} /tempdata${disk} ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
``` 

### <a name="verify-that-the-disks-are-mounted-properly"></a>Verifique se os discos estão montados corretamente
```bash
lsblk
```
![Lista de sistemas de ficheiros temporários montados](./media/disk-encryption/lvm-raid-on-crypt/006-lvm-raid-verify-temp-fs.png)

Verifique também se os discos estão configurados:

```bash
cat /etc/fstab
```
![Informação de configuração via fstab](./media/disk-encryption/lvm-raid-on-crypt/007-lvm-raid-verify-temp-fstab.png)

### <a name="encrypt-the-data-disks"></a>Criptografe os discos de dados
PowerShell utilizando uma chave de encriptação chave (KEK):

```powershell
$sequenceVersion = [Guid]::NewGuid() 
Set-AzVMDiskEncryptionExtension -ResourceGroupName $RGNAME `
-VMName ${VMNAME} `
-DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
-DiskEncryptionKeyVaultId $KeyVaultResourceId `
-KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
-KeyEncryptionKeyVaultId $KeyVaultResourceId `
-VolumeType 'DATA' `
-EncryptFormatAll `
-SequenceVersion $sequenceVersion `
-skipVmBackup;
```

Azure CLI usando um KEK:

```azurecli
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
### <a name="verify-the-encryption-status"></a>Verifique o estado de encriptação

Continue até ao próximo passo apenas quando todos os discos estiverem encriptados.

PowerShell:

```powershell
Get-AzVmDiskEncryptionStatus -ResourceGroupName ${RGNAME} -VMName ${VMNAME}
```
![Estado de encriptação em PowerShell](./media/disk-encryption/lvm-raid-on-crypt/008-lvm-raid-verify-encryption-status-ps.png)

Azure CLI:

```azurecli
az vm encryption show -n ${VMNAME} -g ${RGNAME} -o table
```
![Estado de encriptação no CLI Azure](./media/disk-encryption/lvm-raid-on-crypt/009-lvm-raid-verify-encryption-status-cli.png)

Portal:

![Estado de encriptação no portal](./media/disk-encryption/lvm-raid-on-crypt/010-lvm-raid-verify-encryption-status-portal.png)

Nível de OS:

```bash
lsblk
```
![Estado de encriptação no SISTEMA](./media/disk-encryption/lvm-raid-on-crypt/011-lvm-raid-verify-encryption-status-os.png)

A extensão adicionará os sistemas de ficheiros a /var/lib/azure_disk_encryption_config/azure_crypt_mount (uma encriptação antiga) ou a /etc/crypttab (novas encriptações).

>[!NOTE] 
>Não modifique nenhum destes ficheiros.

Este ficheiro cuidará da ativação destes discos durante o processo de arranque para que o LVM ou o RAID possam usá-los mais tarde. 

Não se preocupe com os pontos de montagem deste ficheiro. A Azure Disk Encryption perderá a capacidade de montar os discos como um sistema de ficheiros normal depois de criarmos um volume físico ou um dispositivo RAID em cima desses dispositivos encriptados. (Isto removerá o formato do sistema de ficheiros que utilizamos durante o processo de preparação.)

### <a name="remove-the-temporary-folders-and-temporary-fstab-entries"></a>Remova as pastas temporárias e as entradas temporárias de fstab
Desmonte os sistemas de ficheiros nos discos que serão utilizados como parte da LVM.

```bash
for disk in c d e f; do umount /tempdata${disk}; done
```
E remover as entradas /etc/fstab:

```bash
vi /etc/fstab
```
### <a name="verify-that-the-disks-are-not-mounted-and-that-the-entries-on-etcfstab-were-removed"></a>Verifique se os discos não estão montados e que as entradas em /etc/fstab foram removidas

```bash
lsblk
```
![Verificação de que os sistemas de ficheiros temporários estão desmontados](./media/disk-encryption/lvm-raid-on-crypt/012-lvm-raid-verify-disks-not-mounted.png)

E verifique se os discos estão configurados:
```bash
cat /etc/fstab
```
![Verificação de que as entradas temporárias fstab são removidas](./media/disk-encryption/lvm-raid-on-crypt/013-lvm-raid-verify-fstab-temp-removed.png)

## <a name="steps-for-lvm-on-crypt"></a>Passos para LVM-on-crypt
Agora que os discos subjacentes estão encriptados, pode criar as estruturas LVM.

Em vez de utilizar o nome do dispositivo, utilize os caminhos /dev/mapper para cada um dos discos para criar um volume físico (na camada de cripta em cima do disco, não no próprio disco).

### <a name="configure-lvm-on-top-of-the-encrypted-layers"></a>Configure o LVM em cima das camadas encriptadas
#### <a name="create-the-physical-volumes"></a>Criar os volumes físicos
Receberá um aviso que pergunta se não há problema em apagar a assinatura do sistema de ficheiros. Continue entrando **em y,** ou use **eco "y"** como mostrado:

```bash
echo "y" | pvcreate /dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052
echo "y" | pvcreate /dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1
echo "y" | pvcreate /dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a
echo "y" | pvcreate /dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![Verificação de que um volume físico foi criado](./media/disk-encryption/lvm-raid-on-crypt/014-lvm-raid-pvcreate.png)

>[!NOTE] 
>Os nomes /dev/mapper/dispositivo aqui precisam de ser substituídos pelos seus valores reais com base na saída de **Isblk**.

#### <a name="verify-the-information-for-physical-volumes"></a>Verificar as informações sobre volumes físicos
```bash
pvs
```

![Informação para volumes físicos](./media/disk-encryption/lvm-raid-on-crypt/015-lvm-raid-pvs.png)

#### <a name="create-the-volume-group"></a>Criar o grupo de volume
Crie o grupo de volume utilizando os mesmos dispositivos já inicializados:

```bash
vgcreate vgdata /dev/mapper/
```

### <a name="check-the-information-for-the-volume-group"></a>Verifique as informações sobre o grupo de volume

```bash
vgdisplay -v vgdata
```
```bash
pvs
```
![Informação para o grupo de volume](./media/disk-encryption/lvm-raid-on-crypt/016-lvm-raid-pvs-on-vg.png)

#### <a name="create-logical-volumes"></a>Criar volumes lógicos

```bash
lvcreate -L 10G -n lvdata1 vgdata
lvcreate -L 7G -n lvdata2 vgdata
``` 

#### <a name="check-the-created-logical-volumes"></a>Verifique os volumes lógicos criados

```bash
lvdisplay
lvdisplay vgdata/lvdata1
lvdisplay vgdata/lvdata2
```
![Informação para volumes lógicos](./media/disk-encryption/lvm-raid-on-crypt/017-lvm-raid-lvs.png)

#### <a name="create-file-systems-on-top-of-the-structures-for-logical-volumes"></a>Criar sistemas de ficheiros em cima das estruturas para volumes lógicos

```bash
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata1
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata2
```

#### <a name="create-the-mount-points-for-the-new-file-systems"></a>Criar os pontos de montagem para os novos sistemas de ficheiros

```bash
mkdir /data0
mkdir /data1
```

#### <a name="add-the-new-file-systems-to-etcfstab-and-mount-them"></a>Adicione os novos sistemas de ficheiros a /etc/fstab e monte-os

```bash
echo "/dev/mapper/vgdata-lvdata1 /data0 ext4 defaults,nofail 0 0" >>/etc/fstab
echo "/dev/mapper/vgdata-lvdata2 /data1 ext4 defaults,nofail 0 0" >>/etc/fstab
mount -a
```

#### <a name="verify-that-the-new-file-systems-are-mounted"></a>Verifique se os novos sistemas de ficheiros estão montados

```bash
lsblk -fs
df -h
```
![A screenshot mostra uma janela de consola com sistemas de ficheiros montados como dados0 e dados1.](./media/disk-encryption/lvm-raid-on-crypt/018-lvm-raid-lsblk-after-lvm.png)

Nesta variação de **Isblk,** estamos a listar os dispositivos que mostram as dependências em ordem inversa. Esta opção ajuda a identificar os dispositivos agrupados pelo volume lógico em vez dos nomes originais do dispositivo /dev/sd[disco].

É importante certificar-se de que a opção **nofalil** é adicionada às opções de ponto de montagem dos volumes LVM criados em cima de um dispositivo encriptado através da Encriptação do Disco Azure. Evita que o SO fique preso durante o processo de arranque (ou em modo de manutenção).

Se não utilizar a opção **nofalil:**

- O SISTEMA nunca entrará no estágio em que a Encriptação do Disco Azure é iniciada e os discos de dados são desbloqueados e montados. 
- Os discos encriptados serão desbloqueados no final do processo de arranque. Os volumes e sistemas de ficheiros LVM serão automaticamente montados até que a Encriptação do Disco Azure os desbloqueie. 

Pode testar reiniciar o VM e validar que os sistemas de ficheiros também estão automaticamente a ser montados após o tempo de arranque. Este processo pode demorar vários minutos, dependendo do número e tamanhos dos sistemas de ficheiros.

#### <a name="reboot-the-vm-and-verify-after-reboot"></a>Reinicie o VM e verifique depois do reboot

```bash
shutdown -r now
```
```bash
lsblk
df -h
```
## <a name="steps-for-raid-on-crypt"></a>Passos para RAID-on-cript
Agora que os discos subjacentes estão encriptados, pode continuar a criar as estruturas RAID. O processo é o mesmo que o de LVM, mas em vez de usar o nome do dispositivo, use os caminhos /dev/mapper para cada disco.

#### <a name="configure-raid-on-top-of-the-encrypted-layer-of-the-disks"></a>Configure RAID em cima da camada encriptada dos discos
```bash
mdadm --create /dev/md10 \
--level 0 \
--raid-devices=4 \
/dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052 \
/dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1 \
/dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a \
/dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![Informações para RAID configurado através do comando mdadm](./media/disk-encryption/lvm-raid-on-crypt/019-lvm-raid-md-creation.png)

>[!NOTE] 
>Os nomes /dev/mapper/dispositivo aqui precisam de ser substituídos pelos seus valores reais, com base na saída de **Isblk**.

### <a name="checkmonitor-raid-creation"></a>Criar RAID de verificação/monitor
```bash
watch -n1 cat /proc/mdstat
mdadm --examine /dev/mapper/[]
mdadm --detail /dev/md10
```
![Estado do RAID](./media/disk-encryption/lvm-raid-on-crypt/020-lvm-raid-md-details.png)

### <a name="create-a-file-system-on-top-of-the-new-raid-device"></a>Criar um sistema de ficheiros em cima do novo dispositivo RAID
```bash
mkfs.ext4 /dev/md10
```

Crie um novo ponto de montagem para o sistema de ficheiros, adicione o novo sistema de ficheiros a /etc/fstab e monte-o:

>[!NOTE] 
>Este ciclo itera apenas num dispositivo para este exemplo em particular, é construído desta forma para ser usado para vários dispositivos md, se necessário.

```bash
for device in md10; do diskuuid="$(blkid -s UUID -o value /dev/${device})"; \
mkdir /raiddata; \
echo "UUID=${diskuuid} /raiddata ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
```

Verifique se o novo sistema de ficheiros está montado:

```bash
lsblk -fs
df -h
```
![A screenshot mostra uma janela da consola com um sistema de ficheiros montado como raiddata.](./media/disk-encryption/lvm-raid-on-crypt/021-lvm-raid-lsblk-md-details.png)

É importante certificar-se de que a opção **nofalil** é adicionada às opções de ponto de montagem dos volumes RAID criados em cima de um dispositivo encriptado através da Encriptação do Disco Azure. Evita que o SO fique preso durante o processo de arranque (ou em modo de manutenção).

Se não utilizar a opção **nofalil:**

- O SISTEMA nunca entrará no estágio em que a Encriptação do Disco Azure é iniciada e os discos de dados são desbloqueados e montados.
- Os discos encriptados serão desbloqueados no final do processo de arranque. Os volumes raid e os sistemas de ficheiros serão automaticamente montados até que a Encriptação do Disco Azure os desbloqueie.

Pode testar reiniciar o VM e validar que os sistemas de ficheiros também estão automaticamente a ser montados após o tempo de arranque. Este processo pode demorar vários minutos, dependendo do número e tamanhos dos sistemas de ficheiros.

```bash
shutdown -r now
```

E quando puder fazer login:

```bash
lsblk
df -h
```
## <a name="next-steps"></a>Passos seguintes

- [Redimensionar dispositivos de gestão de volume lógicos encriptados com encriptação do disco Azure](how-to-resize-encrypted-lvm.md)
- [Resolução de problemas do Azure Disk Encryption](disk-encryption-troubleshooting.md)
