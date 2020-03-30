---
title: Como configurar lvm e raid on-cripta em um Linux VM
description: Este artigo fornece instruções sobre a configuração de LVM e RAID na cripta em VMs Linux.
author: jofrance
ms.service: security
ms.topic: article
ms.author: jofrance
ms.date: 03/17/2020
ms.custom: seodec18
ms.openlocfilehash: 78ba47ba887cf7c90adf70d9d444fbd8a3c721cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284913"
---
# <a name="how-to-configure-lvm-and-raid-on-crypt"></a>Como configurar lvm e raid on-cripta

Este documento é um processo passo a passo sobre como executar LVM em criptografia e Raid em configurações de criptografia.

### <a name="environment"></a>Ambiente

- Distribuição linux
    - RHEL 7.6+
    - Ubuntu 18.04+
    - SUSE 12+
- Passe Único DaDE
- Passe Duplo ADE


## <a name="scenarios"></a>Cenários

**Este cenário aplica-se às extensões de dois passes e passes individuais da ADE.**  

- Configure LVM em cima de dispositivos encriptados (LVM-on-Crypt)
- Configure raid em cima de dispositivos encriptados (RAID-on-Crypt)

Uma vez encriptados os dispositivos subjacentes, pode criar as estruturas LVM ou RAID em cima dessa camada encriptada. Os Volumes Físicos (PV) são criados em cima da Camada Encriptada.
Os Volumes Físicos são utilizados para criar o grupo de volume.
Cria os volumes e adiciona as entradas necessárias em /etc/fstab. 

![Verifique os discos ligados powerShell](./media/disk-encryption/lvm-raid-on-crypt/000-lvm-raid-crypt-diagram.png)

Da mesma forma, o dispositivo RAID é criado em cima da camada encriptada nos discos. Um sistema de ficheiros é criado em cima do dispositivo RAID e adicionado a /etc/fstab como um dispositivo regular.

### <a name="considerations"></a>Considerações

O método recomendado para usar é LVM-on-Crypt.

Raid é considerado quando o LVM não pode ser usado devido a limitações específicas de aplicação/ambiente.

Utilizará a opção EncryptFormatAll, a informação https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-linux#use-encryptformatall-feature-for-data-disks-on-linux-vmssobre esta funcionalidade está localizada aqui: .

Embora este método possa ser feito ao encriptar também o SISTEMA, estamos apenas a encriptar unidades de Dados.

Este procedimento pressupõe que já reviu os https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-linux pré-requisitos aqui mencionados: e aqui https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-cli-quickstart.

A versão de passe duplo ADE encontra-se no caminho da depreciação e já não deve ser utilizada em novas encriptações ADE.

### <a name="procedure"></a>Procedimento

Ao utilizar as configurações "on crypt", estará a seguir o processo descrito abaixo:

>[!NOTE] 
>Estamos a usar variáveis em todo o documento, substituindo os valores em conformidade.
## <a name="general-steps"></a>Passos gerais
### <a name="deploy-a-vm"></a>Implementar uma VM 
>[!NOTE] 
>Embora isto seja opcional, recomendamos que aplique isto num VM recém-implantado.

PowerShell
```powershell
New-AzVm -ResourceGroupName ${RGNAME} `
-Name ${VMNAME} `
-Location ${LOCATION} `
-Size ${VMSIZE} `
-Image ${OSIMAGE} `
-Credential ${creds} `
-Verbose
```
CLI:
```bash
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
### <a name="attach-disks-to-the-vm"></a>Fixe os discos ao vm:
Repita para $N número de discos novos que pretende anexar ao VM PowerShell
```powershell
$storageType = 'Standard_LRS'
$dataDiskName = ${VMNAME} + '_datadisk0'
$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $LOCATION -CreateOption Empty -DiskSizeGB 5
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName ${RGNAME}
$vm = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME} 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 0
Update-AzVM -VM ${VM} -ResourceGroupName ${RGNAME}
```
CLI:
```bash
az vm disk attach \
-g ${RGNAME} \
--vm-name ${VMNAME} \
--name ${VMNAME}datadisk1 \
--size-gb 5 \
--new \
-o table
```
### <a name="verify-the-disks-are-attached-to-the-vm"></a>Verifique se os discos estão ligados ao VM:
PowerShell:
```powershell
$VM = Get-AzVM -ResourceGroupName ${RGNAME} -Name ${VMNAME}
$VM.StorageProfile.DataDisks | Select-Object Lun,Name,DiskSizeGB
```
![Verifique os discos](./media/disk-encryption/lvm-raid-on-crypt/001-lvm-raid-check-disks-powershell.png) ligados ao PowerShell CLI:
```bash
az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks -o table
```
![Verifique os discos](./media/disk-encryption/lvm-raid-on-crypt/002-lvm-raid-check-disks-cli.png) ligados ![ao Portal CLI: Verifique os discos ligados ao CLI](./media/disk-encryption/lvm-raid-on-crypt/003-lvm-raid-check-disks-portal.png) OS:
```bash
lsblk 
```
![Verifique o portal anexo dos discos](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)
### <a name="configure-the-disks-to-be-encrypted"></a>Configure os discos a encriptar
Esta configuração é feita que o nível do sistema operativo, os discos correspondentes são configurados para uma encriptação Tradicional ADE:

Os sistemas de ficheiros são criados em cima dos discos.

São criados pontos de montagem temporários para montar os sistemas de ficheiros.

Os Sistemas de Ficheiros estão configurados em /etc/fstab a montar no momento do arranque.

Verifique a carta do dispositivo atribuída aos novos discos, neste exemplo estamos a usar quatro discos de dados

```bash
lsblk 
```
![Verifique os discos ligados a os](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="create-a-filesystem-on-top-of-each-disk"></a>Crie um sistema de ficheiros em cima de cada disco.
Este comando itera uma criação de sistema de ficheiros ext4 em cada disco definido na parte "in" do ciclo "para".
```bash
for disk in c d e f; do echo mkfs.ext4 -F /dev/sd${disk}; done |bash
```
![Verifique os discos](./media/disk-encryption/lvm-raid-on-crypt/005-lvm-raid-create-temp-fs.png) ligados ao os Encontre o UUID dos sistemas de ficheiros recentemente criados, crie uma pasta temporária para montá-lo, adicione as entradas correspondentes em /etc/fstab e monte todos os sistemas de ficheiros.

Este comando também iterates em cada disco definido na parte "in" do ciclo "para":
```bash
for disk in c d e f; do diskuuid="$(blkid -s UUID -o value /dev/sd${disk})"; \
mkdir /tempdata${disk}; \
echo "UUID=${diskuuid} /tempdata${disk} ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
``` 
### <a name="verify-the-disks-are-mounted-properly"></a>Verifique se os discos estão montados corretamente:
```bash
lsblk
```
![Verifique os sistemas](./media/disk-encryption/lvm-raid-on-crypt/006-lvm-raid-verify-temp-fs.png) de ficheiros temporários montados e configurados:
```bash
cat /etc/fstab
```
![Verifique fstab](./media/disk-encryption/lvm-raid-on-crypt/007-lvm-raid-verify-temp-fstab.png)
### <a name="encrypt-the-data-disks"></a>Criptografe os discos de dados:
PowerShell usando KEK:
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
CLI usando KEK:
```bash
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
### <a name="verify-the-encryption-status"></a>Verifique o estado da encriptação

Continue até ao próximo passo apenas quando todos os discos estiverem encriptados.

PowerShell:
```powershell
Get-AzVmDiskEncryptionStatus -ResourceGroupName ${RGNAME} -VMName ${VMNAME}
```
![Verifique a](./media/disk-encryption/lvm-raid-on-crypt/008-lvm-raid-verify-encryption-status-ps.png) encriptação do CLI:
```bash
az vm encryption show -n ${VMNAME} -g ${RGNAME} -o table
```
![Verifique a encriptação](./media/disk-encryption/lvm-raid-on-crypt/009-lvm-raid-verify-encryption-status-cli.png) ![clI Portal: Verifique a encriptação do](./media/disk-encryption/lvm-raid-on-crypt/010-lvm-raid-verify-encryption-status-portal.png) os os os o nível:
```bash
lsblk
```
![Verifique a encriptação CLI](./media/disk-encryption/lvm-raid-on-crypt/011-lvm-raid-verify-encryption-status-os.png)

A extensão adicionará os sistemas de ficheiros a "/var/lib/azure_disk_encryption_config/azure_crypt_mount" (uma encriptação antiga) ou adicionado a "/etc/crypttab" (novas encriptações).

Não modifique nenhum destes ficheiros.

Este ficheiro vai estar a tratar da ativação destes discos durante o processo de arranque para que possam ser posteriormente utilizados pela LVM ou RAID. 

Não se preocupe com os pontos de montagem deste ficheiro, uma vez que a ADE perderá a capacidade de montar os discos como um sistema normal de ficheiros depois de criarmos um volume físico ou um dispositivo de raid em cima desses dispositivos encriptados (que se livrarão do formato do sistema de ficheiros que usamos durante o processo de preparação).
### <a name="remove-the-temp-folders-and-temp-fstab-entries"></a>Remova as pastas temporárias e as entradas temporárias de fstab
Desmonta os sistemas de ficheiros nos discos que serão usados como parte do LVM
```bash
for disk in c d e f; do umount /tempdata${disk}; done
```
E remova as entradas /etc/fstab:
```bash
vi /etc/fstab
```
### <a name="verify-that-the-disks-are-not-mounted-and-that-the-entries-on-etcfstab-were-removed"></a>Verifique se os discos não estão montados e que as entradas em /etc/fstab foram removidas
```bash
lsblk
```
![Verifique os sistemas de](./media/disk-encryption/lvm-raid-on-crypt/012-lvm-raid-verify-disks-not-mounted.png) ficheiros temporários desmontados e configurados:
```bash
cat /etc/fstab
```
![Verifique se as entradas de fstab temporário são removidas](./media/disk-encryption/lvm-raid-on-crypt/013-lvm-raid-verify-fstab-temp-removed.png)
## <a name="for-lvm-on-crypt"></a>Para LVM-on-cripto
Agora que os discos subjacentes estão encriptados, pode proceder à criação das estruturas LVM.

Em vez de utilizar o nome do dispositivo, utilize os caminhos /dev/mapper para cada um dos discos para criar um volume físico (na camada da cripta em cima do disco não no próprio disco).
### <a name="configure-lvm-on-top-of-the-encrypted-layers"></a>Configure LVM em cima das camadas encriptadas
#### <a name="create-the-physical-volumes"></a>Criar os volumes físicos
Vai receber um aviso perguntando se não há problema em apagar a assinatura do sistema de ficheiros. 

Pode continuar entrando em 'y' ou utilizar o eco "y" como mostrado:
```bash
echo "y" | pvcreate /dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052
echo "y" | pvcreate /dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1
echo "y" | pvcreate /dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a
echo "y" | pvcreate /dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![pvcriar](./media/disk-encryption/lvm-raid-on-crypt/014-lvm-raid-pvcreate.png)
>[!NOTE] 
>Os nomes /dev/mapper/dispositivo aqui precisam de ser substituídos pelos seus valores reais com base na saída do Lsblk.
#### <a name="verify-the-physical-volumes-information"></a>Verifique as informações sobre volumes físicos
```bash
pvs
```
![verificar volumes físicos 1](./media/disk-encryption/lvm-raid-on-crypt/015-lvm-raid-pvs.png)
#### <a name="create-the-volume-group"></a>Criar o grupo de volume
Criar o VG utilizando os mesmos dispositivos já inicializados
```bash
vgcreate vgdata /dev/mapper/
```
### <a name="check-the-volume-group-information"></a>Consulte as informações do grupo de volume
```bash
vgdisplay -v vgdata
```
```bash
pvs
```
![verificar volumes físicos 2](./media/disk-encryption/lvm-raid-on-crypt/016-lvm-raid-pvs-on-vg.png)
#### <a name="create-logical-volumes"></a>Criar volumes lógicos
```bash
lvcreate -L 10G -n lvdata1 vgdata
lvcreate -L 7G -n lvdata2 vgdata
``` 
#### <a name="check-the-logical-volumes-created"></a>Verifique os volumes lógicos criados
```bash
lvdisplay
lvdisplay vgdata/lvdata1
lvdisplay vgdata/lvdata2
```
![verificar lvs](./media/disk-encryption/lvm-raid-on-crypt/017-lvm-raid-lvs.png)
#### <a name="create-filesystems-on-top-of-the-logical-volumes-structures"></a>Criar sistemas de ficheiros em cima da(s) estrutura lógica do volume(s)
```bash
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata1
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata2
```
#### <a name="create-the-mount-points-for-the-new-filesystems"></a>Crie os pontos de montagem para os novos sistemas de ficheiros
```bash
mkdir /data0
mkdir /data1
```
#### <a name="add-the-new-file-systems-to-etcfstab-and-mount-them"></a>Adicione os novos sistemas de ficheiros para /etc/fstab e monte-os
```bash
echo "/dev/mapper/vgdata-lvdata1 /data0 ext4 defaults,nofail 0 0" >>/etc/fstab
echo "/dev/mapper/vgdata-lvdata2 /data1 ext4 defaults,nofail 0 0" >>/etc/fstab
mount -a
```
#### <a name="verify-that-the-new-filesystems-are-mounted"></a>Verifique se os novos sistemas de ficheiros estão montados
```bash
lsblk -fs
df -h
```
![verificar volumes](./media/disk-encryption/lvm-raid-on-crypt/018-lvm-raid-lsblk-after-lvm.png) lógicos Nesta variação de lsblk, estamos listando os dispositivos que mostram as dependências da ordem inversa, esta opção ajuda a identificar os dispositivos agrupados pelo volume lógico em vez dos nomes originais do dispositivo /dev/sd[disco].

Importante: Certifique-se de que a opção "nofail" é adicionada às opções de ponto de montagem dos volumes LVM criados em cima de um dispositivo encriptado ADE. É importante evitar que o SO se aguente durante o processo de arranque (ou no modo de manutenção). 

O disco encriptado está desbloqueado no final do processo de arranque, os volumes LVM e os sistemas de ficheiros serão automaticamente montados.

Se a opção nofail não for utilizada, o SISTEMA nunca entrará na fase em que a ADE é iniciada e o disco de dados(s) é desbloqueado e montado.

Pode testar a reinicialização do VM e a validação dos sistemas de ficheiros também estão automaticamente a ser montados após o tempo de arranque. 

Tenha em consideração que este processo pode demorar vários minutos dependendo do número de sistemas de ficheiros e dos tamanhos
#### <a name="reboot-the-vm-and-verify-after-reboot"></a>Reiniciar o VM e verificar após o reboot
```bash
shutdown -r now
```
```bash
lsblk
df -h
```
## <a name="for-raid-on-crypt"></a>Para RAID-on-Crypt
Agora os discos subjacentes são encriptados pode continuar a criar as estruturas RAID, tal como o LVM, em vez de utilizar o nome do dispositivo, utilizar os caminhos /dev/mapper para cada um dos discos.

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
![mdadm criar](./media/disk-encryption/lvm-raid-on-crypt/019-lvm-raid-md-creation.png)
>[!NOTE] 
>Os nomes /dev/mapper/dispositivo aqui precisam de ser substituídos pelos seus valores reais com base na saída do Lsblk.
#### <a name="checkmonitor-the-raid-creation"></a>Verifique/monitorize a criação RAID:
```bash
watch -n1 cat /proc/mdstat
mdadm --examine /dev/mapper/[]
mdadm --detail /dev/md10
```
![verificar mdadm](./media/disk-encryption/lvm-raid-on-crypt/020-lvm-raid-md-details.png)
#### <a name="create-a-filesystem-on-top-of-the-new-raid-device"></a>Criar um sistema de ficheiros em cima do novo dispositivo Raid:
```bash
mkfs.ext4 /dev/md10
```
Crie um novo ponto de montagem para o sistema de ficheiros, adicione o novo sistema de ficheiros a /etc/fstab, e monte-o
```bash
for device in md10; do diskuuid="$(blkid -s UUID -o value /dev/${device})"; \
mkdir /raiddata; \
echo "UUID=${diskuuid} /raiddata ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
```
Verifique se os novos sistemas de ficheiros estão montados
```bash
lsblk -fs
df -h
```
![verificar mdadm](./media/disk-encryption/lvm-raid-on-crypt/021-lvm-raid-lsblk-md-details.png)

Importante: Certifique-se de que a opção "nofail" é adicionada às opções de ponto de montagem dos volumes RAID criados em cima de um dispositivo encriptado ADE. 

É muito importante evitar que o SO se aguente durante o processo de arranque (ou no modo de manutenção). 

O disco encriptado será desbloqueado no final do processo de arranque e os volumes RAID e sistemas de ficheiros serão automaticamente montados até que sejam desbloqueados pela ADE, caso a opção nofail não seja utilizada.

O SISTEMA nunca entrará na fase em que a ADE é iniciada, e os discos de dados são desbloqueados e montados.

Pode testar a reinicialização do VM e a validação dos sistemas de ficheiros também estão automaticamente a ser montados após o tempo de arranque. Tenha em consideração que este processo pode demorar vários minutos dependendo do número de sistemas de ficheiros e dos tamanhos
```bash
shutdown -r now
```
E quando puder fazer login:
```bash
lsblk
df -h
```
## <a name="next-steps"></a>Passos seguintes

- [Resolução de problemas do Azure Disk Encryption](disk-encryption-troubleshooting.md)

