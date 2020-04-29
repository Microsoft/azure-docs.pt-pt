---
title: Como verificar o estado de encriptação do Linux
description: Este artigo fornece instruções sobre a verificação do estado de encriptação a partir da plataforma e do nível de OS.
author: kailashmsft
ms.service: security
ms.topic: article
ms.author: kaib
ms.date: 03/11/2020
ms.custom: seodec18
ms.openlocfilehash: 0aaa32c46d915eafffcfac9d95cfdd3a24d4086d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80123428"
---
# <a name="how-to-verify-encryption-status-for-linux"></a>Como verificar o estado de encriptação do Linux 

**Este cenário aplica-se às extensões de passe duplo e de passe único da ADE.**  
Este âmbito documental é validar o estado de encriptação de uma máquina virtual utilizando métodos diferentes.

### <a name="environment"></a>Ambiente

- Distribuição linux

### <a name="procedure"></a>Procedimento

Uma máquina virtual foi encriptada usando passe duplo ou passe único.

O estado de encriptação pode ser validado durante ou após a encriptação utilizando diferentes métodos.

>[!NOTE] 
>Estamos a usar variáveis em todo o documento, substituindo os valores em conformidade.

### <a name="verification"></a>Verificação

A verificação pode ser feita a partir do Portal, PowerShell, AZ CLI e, ou do lado VM OS. 

Esta verificação pode ser feita verificando os discos ligados a um VM específico. 

Ou consultando as definições de encriptação em cada disco individual, quer o disco esteja ligado ou desconectado.

Abaixo dos diferentes métodos de validação:

## <a name="using-the-portal"></a>Utilização do Portal

Valide o estado de encriptação verificando a secção de extensões no portal Azure.

Dentro da secção **Extensões,** verá a extensão ADE listada. 

Clique nele e dê uma olhada na mensagem de **estado,** indicará o estado de encriptação atual:

![Verificação do portal número 1](./media/disk-encryption/verify-encryption-linux/portal-check-001.png)

Na lista de extensões, verá a versão de extensão ADE correspondente. A versão 0.x corresponde a ADE Dual-Pass e a versão 1.x corresponde a Um Passe Único ADE.

Pode obter mais detalhes clicando na extensão e, em seguida, no *Estado detalhado do View*.

Verá um estado mais detalhado do processo de encriptação em formato Json:

![Verificação do portal número 2](./media/disk-encryption/verify-encryption-linux/portal-check-002.png)

![Verificação do portal número 3](./media/disk-encryption/verify-encryption-linux/portal-check-003.png)

Outra forma de validar o estado de encriptação é dar uma olhada na secção **De Discos.**

![Verificação do portal número 4](./media/disk-encryption/verify-encryption-linux/portal-check-004.png)

>[!NOTE] 
> Este estado significa que os discos têm definições de encriptação carimbadas, mas não que foram realmente encriptadas ao nível de OS. Por design, os discos são carimbados primeiro e encriptados mais tarde. Se o processo de encriptação falhar, os discos podem acabar carimbados, mas não encriptados. Para confirmar se os discos estão verdadeiramente encriptados, pode verificar duas vezes a encriptação de cada disco ao nível de OS.

## <a name="using-powershell"></a>Com o PowerShell

Pode validar o estado de encriptação **geral** de um VM encriptado utilizando os seguintes comandos PowerShell:

```azurepowershell
   $VMNAME="VMNAME"
   $RGNAME="RGNAME"
   Get-AzVmDiskEncryptionStatus -ResourceGroupName  ${RGNAME} -VMName ${VMNAME}
```
![verificar PowerShell 1](./media/disk-encryption/verify-encryption-linux/verify-status-ps-01.png)

Pode capturar as definições de encriptação de cada disco individual utilizando os seguintes comandos PowerShell:

### <a name="single-pass"></a>Passe único
Se passar um único passe, as definições de encriptação são carimbos em cada um dos discos (OS e Dados), pode capturar as definições de encriptação do disco OS num único passe:

``` powershell
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
![Verifique o os único passe 01](./media/disk-encryption/verify-encryption-linux/verify-os-single-ps-001.png)

Se o disco não tiver definições de encriptação carimbadas, a saída ficará vazia como mostrado abaixo:

![Definições de encriptação osso 2](./media/disk-encryption/verify-encryption-linux/os-encryption-settings-2.png)

Capturar definições de encriptação de disco de dados:

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
![Verifique os dados individuais do PS 001](./media/disk-encryption/verify-encryption-linux/verify-data-single-ps-001.png)

### <a name="dual-pass"></a>Passe Duplo
Em Dual Pass, as definições de encriptação são carimbadas no modelo VM e não em cada disco individual.

Para verificar se as definições de encriptação foram carimbadas em dois passes, pode utilizar os seguintes comandos:

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
![Verifique o passe duplo PowerShell 1](./media/disk-encryption/verify-encryption-linux/verify-dual-ps-001.png)

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
## <a name="using-az-cli"></a>Usando a Z CLI

Pode validar o estado de encriptação **geral** de um VM encriptado utilizando os seguintes comandos AZ CLI:

```bash
VMNAME="VMNAME"
RGNAME="RGNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"
```
![Verifique geral usando cli ](./media/disk-encryption/verify-encryption-linux/verify-gen-cli.png)

### <a name="single-pass"></a>Passe Único
Pode validar as definições de encriptação de cada disco individual utilizando os seguintes comandos AZ CLI:

```bash
az vm encryption show -g ${RGNAME} -n ${VMNAME} --query "disks[*].[name, statuses[*].displayStatus]"  -o table
```

![Definições de encriptação de dados](./media/disk-encryption/verify-encryption-linux/data-encryption-settings-2.png)

>[!IMPORTANT]
> Caso o disco não tenha definições de encriptação carimbadas, será mostrado como "O disco não está encriptado"

Definições detalhadas de Estado e encriptação:

Disco OS:

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

![OSSingleCLI](./media/disk-encryption/verify-encryption-linux/os-single-cli.png)

Discos de dados:

```bash
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

![ClI simples de dados ](./media/disk-encryption/verify-encryption-linux/data-single-cli.png)

### <a name="dual-pass"></a>Passe Duplo

``` bash
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} -o table
```

![Verifique a dualidade ](./media/disk-encryption/verify-encryption-linux/verify-gen-dual-cli.png) geral utilizando o CLI Pode também verificar as definições de encriptação no perfil de armazenamento do modelo VM do disco OS:

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

![Verifique o perfil vm dual usando cli ](./media/disk-encryption/verify-encryption-linux/verify-vm-profile-dual-cli.png)

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

Os discos não geridos são ficheiros VHD que são armazenados como bolhas de página nas contas de armazenamento do Azure.

Para obter os detalhes de um disco específico, você precisa fornecer:

A identificação da conta de armazenamento que contém o disco.
Uma corda de ligação para aquela conta de armazenamento em particular.
O nome do recipiente que armazena o disco.
O nome do disco.

Este comando lista todas as IDs para todas as suas contas de armazenamento:

```bash
az storage account list --query [].[id] -o tsv
```
As IDs da conta de armazenamento estão listadas no seguinte formulário:

/subscrições/\<id de subscrição\<>/recursosGroups/ nome do grupo de recursos\<>/fornecedores/Microsoft.StorageAccounts/ storageAccounts/ storage account name>

Selecione o ID apropriado e guarde-o numa variável:
```bash
id="/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name>"
```
A corda de ligação.

Este comando obtém a cadeia de ligação para uma determinada conta de armazenamento e armazena-a numa variável:

```bash
ConnectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
```

O nome do contentor.

O seguinte comando lista todos os contentores sob uma conta de armazenamento:
```bash
az storage container list --connection-string $ConnectionString --query [].[name] -o tsv
```
O recipiente utilizado para discos é normalmente chamado de "vhds"

Guarde o nome do recipiente numa variável 
```bash
ContainerName="name of the container"
```

O nome do disco.

Use este comando para listar todas as bolhas num recipiente em particular
```bash 
az storage blob list -c ${ContainerName} --connection-string $ConnectionString --query [].[name] -o tsv
```
Escolha o disco que pretende consultar e armazenar o seu nome numa variável.
```bash
DiskName="diskname.vhd"
```
Consultar as definições de encriptação do disco
```bash
az storage blob show -c ${ContainerName} --connection-string ${ConnectionString} -n ${DiskName} --query metadata.DiskEncryptionSettings
```

## <a name="from-the-os"></a>Do OS
Validar se as divisórias do disco de dados estiverem encriptadas (e o disco OS não estiver)

Quando uma divisória/disco é encriptado é apresentado como tipo **de cripta,** quando não é encriptado é exibido como **tipo de parte/disco**

``` bash
lsblk
```

![Camada de Cripta Os ](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer.png)

Pode obter mais detalhes utilizando a seguinte variante "lsblk". 

Você verá uma camada do tipo **cripta** que é montada pela extensão.

O exemplo que se segue mostra Volumes Lógicos e discos normais com um "**crypto\_LUKS FSTYPE**".

```bash
lsblk -o NAME,TYPE,FSTYPE,LABEL,SIZE,RO,MOUNTPOINT
```
![Camada de cripta Os 2](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer-2.png)

Como um passo extra, também pode validar se o disco de dados tiver alguma chave carregada

``` bash
cryptsetup luksDump /dev/VGNAME/LVNAME
```

``` bash
cryptsetup luksDump /dev/sdd1
```

E quais os dispositivos DM listados como cripta

```bash
dmsetup ls --target crypt
```

## <a name="next-steps"></a>Passos Seguintes

- [Resolução de problemas do Azure Disk Encryption](disk-encryption-troubleshooting.md)
