---
title: Inicialização VM está bloqueada no "obtendo Windows pronto. Não desativar o seu computador"no Azure | Documentos da Microsoft
description: Introduzir os passos para resolver o problema em que o arranque da VM está bloqueada no "obtendo Windows pronto. Não desligue o computador."
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: willchen
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/18/2018
ms.author: delhan
ms.openlocfilehash: c3592529d20680c6920e569887effee4ffe38344
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60443862"
---
# <a name="vm-startup-is-stuck-on-getting-windows-ready-dont-turn-off-your-computer-in-azure"></a>Inicialização VM está bloqueada no "obtendo Windows pronto. Não desativar o seu computador"no Azure

Este artigo ajuda-o a resolver o problema quando está bloqueada a sua máquina virtual (VM) sobre o "introdução Windows pronto. Não, desligar o computador"estágio durante o arranque.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="symptoms"></a>Sintomas

Quando utiliza **diagnósticos de arranque** para obter a captura de ecrã de uma VM, o sistema operativo não totalmente arrancar. A VM apresenta a mensagem "obtendo Windows pronto. Não desligue o computador."

![Exemplo de mensagem para o Windows Server 2012 R2](./media/troubleshoot-vm-configure-update-boot/message1.png)

![Exemplo de mensagem](./media/troubleshoot-vm-configure-update-boot/message2.png)

## <a name="cause"></a>Causa

Geralmente, este problema ocorre quando o servidor está a fazer a reinicialização final após a configuração foi alterada. A alteração de configuração pode ser inicializada por atualizações do Windows ou as alterações na funcionalidade/funções do servidor. Para o Windows Update, se o tamanho das atualizações foi grande, o sistema operativo tem mais tempo para reconfigurar as alterações.

## <a name="back-up-the-os-disk"></a>Criar cópias de segurança do disco do SO

Antes de tentar corrigir o problema, criar cópias de segurança do disco do SO.

### <a name="for-vms-with-an-encrypted-disk-you-must-unlock-the-disks-first"></a>Para VMs com um disco encriptado, tem de desbloquear os discos em primeiro lugar

Siga estes passos para determinar se a VM é uma VM encriptada.

1. No portal do Azure, abra a sua VM e, em seguida, navegue para os discos.

2. Examinar os **Encryption** coluna para ver se a encriptação está ativada.

Se o disco do SO estiver encriptado, desbloquear o disco criptografado. Para desbloquear o disco, siga estes passos.

1. Crie uma VM de recuperação que está localizado no mesmo grupo de recursos, conta de armazenamento e localização da VM afetada.

2. No portal do Azure, elimine a VM afetada e manter o disco.

3. Execute o PowerShell como administrador.

4. Execute o seguinte cmdlet para obter o nome secreto.

    ```Powershell
    Login-AzAccount
 
    $vmName = “VirtualMachineName”
    $vault = “AzureKeyVaultName”
 
    # Get the Secret for the C drive from Azure Key Vault
    Get-AzureKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq $vmName) -and ($_.Tags.VolumeLetter -eq “C:\”) -and ($_.ContentType -eq ‘BEK‘)}

    # OR Use the below command to get BEK keys for all the Volumes
    Get-AzureKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq   $vmName) -and ($_.ContentType -eq ‘BEK’)}
    ```

5. Depois de ter o nome do segredo, execute os seguintes comandos no PowerShell.

    ```Powershell
    $secretName = 'SecretName'
    $keyVaultSecret = Get-AzureKeyVaultSecret -VaultName $vault -Name $secretname
    $bekSecretBase64 = $keyVaultSecret.SecretValueText
    ```

6. Converter o valor codificado pela Base64 para bytes e escrever a saída para um ficheiro. 

    > [!Note]
    > Se usar o USB desbloquear opção, o nome do ficheiro BEK tem de corresponder o GUID de BEK original. Crie uma pasta na unidade C com o nome "BEK" antes de seguir estes passos.
    
    ```Powershell
    New-Item -ItemType directory -Path C:\BEK
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = “c:\BEK\$secretName.BEK”
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7. Depois do ficheiro BEK é criado no seu PC, copie o ficheiro para a VM tem o disco do SO bloqueado anexado a de recuperação. Execute os seguintes comandos com a localização do ficheiro BEK.

    ```Powershell
    manage-bde -status F:
    manage-bde -unlock F: -rk C:\BEKFILENAME.BEK
    ```
    **Opcional**: Em alguns cenários, poderá ser necessário desencriptar o disco ao utilizar este comando.
   
    ```Powershell
    manage-bde -off F:
    ```

    > [!Note]
    > O comando anterior pressupõe que o disco a encriptação está em letra F.

8. Se pretender recolher registos, vá para o caminho **letra de unidade: \Windows\System32\winevt\Logs**.

9. Desligar a unidade a partir da máquina de recuperação.

### <a name="create-a-snapshot"></a>Criar um instantâneo

Para criar um instantâneo, siga os passos em [instantâneo de um disco](../windows/snapshot-copy-managed-disk.md).

## <a name="collect-an-os-memory-dump"></a>Recolher um despejo de memória do sistema operacional

Utilize os passos no [despejo de recolher os](troubleshoot-common-blue-screen-error.md#collect-memory-dump-file) secção para recolher um despejo do sistema operacional quando a VM está bloqueada nos configuração.

## <a name="contact-microsoft-support"></a>Contacte o Suporte da Microsoft

Depois de recolher o ficheiro de informação, contacte [suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para analisar a causa raiz.


## <a name="rebuild-the-vm-by-using-powershell"></a>Recriar a VM com o PowerShell

Depois de recolher o arquivo de despejo de memória, siga estes passos para recriar a VM.

**Para discos não geridos**

```powershell
# To log in to Azure Resource Manager
Login-AzAccount

# To view all subscriptions for your account
Get-AzSubscription

# To select a default subscription for your current session
Get-AzSubscription –SubscriptionID “SubscriptionID” | Select-AzSubscription

$rgname = "RGname"
$loc = "Location"
$vmsize = "VmSize"
$vmname = "VmName"
$vm = New-AzVMConfig -VMName $vmname -VMSize $vmsize;

$nic = Get-AzNetworkInterface -Name ("NicName") -ResourceGroupName $rgname;
$nicId = $nic.Id;

$vm = Add-AzVMNetworkInterface -VM $vm -Id $nicId;

$osDiskName = "OSdiskName"
$osDiskVhdUri = "OSdiskURI"

$vm = Set-AzVMOSDisk -VM $vm -VhdUri $osDiskVhdUri -name $osDiskName -CreateOption attach -Windows

New-AzVM -ResourceGroupName $rgname -Location $loc -VM $vm -Verbose
```

**Para discos geridos**

```powershell
# To log in to Azure Resource Manager
Login-AzAccount

# To view all subscriptions for your account
Get-AzSubscription

# To select a default subscription for your current session
Get-AzSubscription –SubscriptionID "SubscriptionID" | Select-AzSubscription

#Fill in all variables
$subid = "SubscriptionID"
$rgName = "ResourceGroupName";
$loc = "Location";
$vmSize = "VmSize";
$vmName = "VmName";
$nic1Name = "FirstNetworkInterfaceName";
#$nic2Name = "SecondNetworkInterfaceName";
$avName = "AvailabilitySetName";
$osDiskName = "OsDiskName";
$DataDiskName = "DataDiskName"

#This can be found by selecting the Managed Disks you wish you use in the Azure portal if the format below doesn't match
$osDiskResourceId = "/subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/disks/$osDiskName";
$dataDiskResourceId = "/subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/disks/$DataDiskName";

$vm = New-AzVMConfig -VMName $vmName -VMSize $vmSize;

#Uncomment to add Availability Set
#$avSet = Get-AzAvailabilitySet –Name $avName –ResourceGroupName $rgName;
#$vm = New-AzVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avSet.Id;

#Get NIC Resource Id and add
$nic1 = Get-AzNetworkInterface -Name $nic1Name -ResourceGroupName $rgName;
$vm = Add-AzVMNetworkInterface -VM $vm -Id $nic1.Id -Primary;

#Uncomment to add a secondary NIC
#$nic2 = Get-AzNetworkInterface -Name $nic2Name -ResourceGroupName $rgName;
#$vm = Add-AzVMNetworkInterface -VM $vm -Id $nic2.Id;

#Windows VM
$vm = Set-AzVMOSDisk -VM $vm -ManagedDiskId $osDiskResourceId -name $osDiskName -CreateOption Attach -Windows;

#Linux VM
#$vm = Set-AzVMOSDisk -VM $vm -ManagedDiskId $osDiskResourceId -name $osDiskName -CreateOption Attach -Linux;

#Uncomment to add additional Data Disk
#Add-AzVMDataDisk -VM $vm -ManagedDiskId $dataDiskResourceId -Name $dataDiskName -Caching None -DiskSizeInGB 1024 -Lun 0 -CreateOption Attach;

New-AzVM -ResourceGroupName $rgName -Location $loc -VM $vm;
```
