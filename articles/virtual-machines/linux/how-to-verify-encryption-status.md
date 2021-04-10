---
title: Verifique o estado de encriptação do Linux - Encriptação do Disco Azure
description: Este artigo fornece instruções para verificar o estado de encriptação dos níveis de plataforma e so.
author: kailashmsft
ms.service: virtual-machines
ms.subservice: disks
ms.topic: how-to
ms.author: kaib
ms.date: 03/11/2020
ms.custom: seodec18, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 04654672cc5806465ec9f75b695772dcb2037eab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102564140"
---
# <a name="verify-encryption-status-for-linux"></a>Verifique o estado de encriptação do Linux 

O âmbito deste artigo é validar o estado de encriptação de uma máquina virtual utilizando métodos diferentes: o portal Azure, o PowerShell, o Azure CLI, ou o sistema operativo da máquina virtual (VM). 

Pode validar o estado de encriptação durante ou após a encriptação, por:

- Verificação dos discos ligados a um VM em particular. 
- Consultando as definições de encriptação em cada disco, quer o disco esteja ligado ou desapegado.

Este cenário aplica-se às extensões de duplo passe e passe de Azure Disk Encryption. As distribuições de Linux são o único ambiente para este cenário.

>[!NOTE] 
>Estamos a usar variáveis ao longo do artigo. Substitua os valores em conformidade.

## <a name="portal"></a>Portal

No portal Azure, dentro da secção **Extensões,** selecione a extensão de encriptação do disco Azure na lista. As informações para **a mensagem de estado** indicam o estado atual da encriptação:

![Verificação do portal com estado, versão e mensagem de estado realçadas](./media/disk-encryption/verify-encryption-linux/portal-check-001.png)

Na lista de extensões, verá a versão correspondente da extensão de encriptação do disco Azure. A versão 0.x corresponde ao passe duplo de encriptação de disco Azure e a versão 1.x corresponde ao passe único de encriptação do disco Azure.

Pode obter mais detalhes selecionando a extensão e, em seguida, selecionando **o estado detalhado do Ver**. O estado detalhado do processo de encriptação aparece no formato JSON.

![Verificação do portal com o link "Ver estado detalhado" realçado](./media/disk-encryption/verify-encryption-linux/portal-check-002.png)

![Estado detalhado no formato JSON](./media/disk-encryption/verify-encryption-linux/portal-check-003.png)

Outra forma de validar o estado de encriptação é olhando para a secção **de definições do Disco.**

![Estado de encriptação para discos de sistema operativo e discos de dados](./media/disk-encryption/verify-encryption-linux/portal-check-004.png)

>[!NOTE] 
> Este estado significa que os discos têm definições de encriptação carimbadas, não que foram realmente encriptados ao nível do SO.
>
> Por design, os discos são carimbados primeiro e encriptados mais tarde. Se o processo de encriptação falhar, os discos podem acabar estampados, mas não encriptados. 
>
> Para confirmar se os discos estão realmente encriptados, pode verificar duas vezes a encriptação de cada disco ao nível de OS.

## <a name="powershell"></a>PowerShell

Pode validar o estado de encriptação *geral* de um VM encriptado utilizando os seguintes comandos PowerShell:

```azurepowershell
   $VMNAME="VMNAME"
   $RGNAME="RGNAME"
   Get-AzVmDiskEncryptionStatus -ResourceGroupName  ${RGNAME} -VMName ${VMNAME}
```
![Estado geral de encriptação em PowerShell](./media/disk-encryption/verify-encryption-linux/verify-status-ps-01.png)

Pode capturar as definições de encriptação de cada disco utilizando os seguintes comandos PowerShell.

### <a name="single-pass"></a>Passe único
Num único passe, as definições de encriptação são carimbadas em cada um dos discos (SO e dados). Pode capturar as definições de encriptação de um disco DE num único passe da seguinte forma:

```powershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$VM = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME}  
 $Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $VM.StorageProfile.OsDisk.Name
 Write-Host "============================================================================================================================================================="
 Write-Host "Encryption Settings:"
 Write-Host "============================================================================================================================================================="
 Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
 Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
 Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
 Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
 Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
 Write-Host "============================================================================================================================================================="
```
![Definições de encriptação para um disco DE](./media/disk-encryption/verify-encryption-linux/verify-os-single-ps-001.png)

Se o disco não tiver definições de encriptação carimbadas, a saída estará vazia:

![Saída vazia](./media/disk-encryption/verify-encryption-linux/os-encryption-settings-2.png)

Utilize os seguintes comandos para capturar definições de encriptação para discos de dados:

```azurepowershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$VM = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME}
 clear
 foreach ($i in $VM.StorageProfile.DataDisks|ForEach-Object{$_.Name})
 {
 Write-Host "============================================================================================================================================================="
 Write-Host "Encryption Settings:"
 Write-Host "============================================================================================================================================================="
 Write-Host "Checking Disk:" $i
 $Disk=(Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $i)
 Write-Host "Encryption Enable: " $Sourcedisk.EncryptionSettingsCollection.Enabled
 Write-Host "Encryption KeyEncryptionKey: " $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl;
 Write-Host "Encryption DiskEncryptionKey: " $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl;
 Write-Host "============================================================================================================================================================="
 }
```
![Definições de encriptação para discos de dados](./media/disk-encryption/verify-encryption-linux/verify-data-single-ps-001.png)

### <a name="dual-pass"></a>Passe duplo
Num passe duplo, as definições de encriptação são carimbadas no modelo VM e não em cada disco individual.

Para verificar se as definições de encriptação foram carimbadas num passe duplo, utilize os seguintes comandos:

```azurepowershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$vm = Get-AzVm -ResourceGroupName ${RGNAME} -Name ${VMNAME};
$Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $VM.StorageProfile.OsDisk.Name
clear
Write-Host "============================================================================================================================================================="
Write-Host "Encryption Settings:"
Write-Host "============================================================================================================================================================="
Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
Write-Host "============================================================================================================================================================="
```
![Definições de encriptação num passe duplo](./media/disk-encryption/verify-encryption-linux/verify-dual-ps-001.png)

### <a name="unattached-disks"></a>Discos não ligados

Verifique as definições de encriptação de discos que não estejam ligados a um VM.

### <a name="managed-disks"></a>Managed disks
```powershell
$Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName ${TARGETDISKNAME}
Write-Host "============================================================================================================================================================="
Write-Host "Encryption Settings:"
Write-Host "============================================================================================================================================================="
Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
Write-Host "============================================================================================================================================================="
```
## <a name="azure-cli"></a>CLI do Azure

Pode validar o estado de encriptação *geral* de um VM encriptado utilizando os seguintes comandos Azure CLI:

```azurecli
VMNAME="VMNAME"
RGNAME="RGNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"
```
![Estado geral de encriptação do CLI Azure ](./media/disk-encryption/verify-encryption-linux/verify-gen-cli.png)

### <a name="single-pass"></a>Passe único
Pode validar as definições de encriptação de cada disco utilizando os seguintes comandos Azure CLI:

```azurecli
az vm encryption show -g ${RGNAME} -n ${VMNAME} --query "disks[*].[name, statuses[*].displayStatus]"  -o table
```

![Definições de encriptação de dados](./media/disk-encryption/verify-encryption-linux/data-encryption-settings-2.png)

>[!IMPORTANT]
> Se o disco não tiver definições de encriptação carimbadas, verá que o disco de texto **não está encriptado**.

Utilize os seguintes comandos para obter definições detalhadas de estado e encriptação.

Disco de osa:

```bash
RGNAME="RGNAME"
VMNAME="VNAME"

disk=`az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.osDisk.name -o tsv`
for disk in $disk; do \
echo "============================================================================================================================================================="
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![Definições detalhadas de estado e encriptação para o disco OS](./media/disk-encryption/verify-encryption-linux/os-single-cli.png)

Discos de dados:

```azurecli
RGNAME="RGNAME"
VMNAME="VMNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"

for disk in `az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks[].name -o tsv`; do \
echo "============================================================================================================================================================="; \
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![Definições detalhadas de estado e encriptação para os discos de dados](./media/disk-encryption/verify-encryption-linux/data-single-cli.png)

### <a name="dual-pass"></a>Passe duplo

```azurecli
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} -o table
```

![Definições gerais de encriptação para passagem dupla através do CLI Azure](./media/disk-encryption/verify-encryption-linux/verify-gen-dual-cli.png)

Também pode verificar as definições de encriptação no perfil de armazenamento do modelo VM do disco OS:

```bash
disk=`az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.osDisk.name -o tsv`
for disk in $disk; do \
echo "============================================================================================================================================================="; \
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![Perfil VM para passe duplo através do CLI Azure](./media/disk-encryption/verify-encryption-linux/verify-vm-profile-dual-cli.png)

### <a name="unattached-disks"></a>Discos não ligados

Verifique as definições de encriptação de discos que não estejam ligados a um VM.

### <a name="managed-disks"></a>Managed disks

```bash
RGNAME="RGNAME"
TARGETDISKNAME="DISKNAME"
echo "============================================================================================================================================================="
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
```
### <a name="unmanaged-disks"></a>Discos não geridos

Os discos não geridos são ficheiros VHD que são armazenados como bolhas de página nas contas de armazenamento Azure.

Para obter os detalhes de um disco específico, você precisa fornecer:

- A identificação da conta de armazenamento que contém o disco.
- Uma cadeia de ligação para essa conta de armazenamento em particular.
- O nome do contentor que armazena o disco.
- O nome do disco.

Este comando lista todos os IDs para todas as suas contas de armazenamento:

```azurecli
az storage account list --query [].[id] -o tsv
```
Os IDs da conta de armazenamento são listados no seguinte formulário:

/subscrições/ \<subscription id> /resourceGroups/ \<resource group name> /providers/Microsoft.Storage/storageAccounts/\<storage account name>

Selecione o ID apropriado e guarde-o numa variável:
```bash
id="/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name>"
```

Este comando obtém a cadeia de ligação de uma conta de armazenamento em particular e armazena-a numa variável:

```bash
ConnectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
```

O seguinte comando lista todos os contentores numa conta de armazenamento:
```azurecli
az storage container list --connection-string $ConnectionString --query [].[name] -o tsv
```
O recipiente usado para discos é normalmente chamado de "vhds".

Guarde o nome do recipiente numa variável: 
```bash
ContainerName="name of the container"
```

Utilize este comando para listar todas as bolhas num determinado recipiente:
```azurecli 
az storage blob list -c ${ContainerName} --connection-string $ConnectionString --query [].[name] -o tsv
```
Escolha o disco que pretende consultar e guarde o seu nome numa variável:
```bash
DiskName="diskname.vhd"
```
Consultar as definições de encriptação do disco:
```azurecli
az storage blob show -c ${ContainerName} --connection-string ${ConnectionString} -n ${DiskName} --query metadata.DiskEncryptionSettings
```

## <a name="operating-system"></a>Sistema operativo
Validar se as divisórias do disco de dados estiverem encriptadas (e o disco DE não estiver).

Quando uma divisória ou disco é encriptado, é exibido como um tipo **de cripta.** Quando não está encriptado, é exibido como um tipo **de peça/disco.**

```bash
lsblk
```

![Camada de cripta de SO para uma partição](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer.png)

Pode obter mais detalhes utilizando a seguinte variante **de Isblk.** 

Você verá uma camada do tipo **cripta** que é montada pela extensão. O exemplo a seguir mostra volumes lógicos e discos normais com **cripto \_ LUKS FSTYPE**.

```bash
lsblk -o NAME,TYPE,FSTYPE,LABEL,SIZE,RO,MOUNTPOINT
```
![Camada de cripta de SO para volumes lógicos e discos normais](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer-2.png)

Como um passo extra, pode validar se o disco de dados tiver alguma tecla carregada:

```bash
cryptsetup luksDump /dev/VGNAME/LVNAME
```

```bash
cryptsetup luksDump /dev/sdd1
```

E pode verificar quais os dispositivos **dm** listados como **cripta:**

```bash
dmsetup ls --target crypt
```

## <a name="next-steps"></a>Passos seguintes

- [Resolução de problemas do Azure Disk Encryption](disk-encryption-troubleshooting.md)
