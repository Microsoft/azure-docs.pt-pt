---
title: A inicialização da VM está presa em "preparando o Windows. Não desligar o computador "no Azure | Microsoft Docs
description: Apresente as etapas para solucionar o problema em que a inicialização da VM está presa em "preparando o Windows. Não desligue o computador."
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: willchen
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 09/18/2018
ms.author: delhan
ms.openlocfilehash: 2f3c18ea1887ea5b05bb89f85371139ac83dfe49
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70080169"
---
# <a name="vm-startup-is-stuck-on-getting-windows-ready-dont-turn-off-your-computer-in-azure"></a>A inicialização da VM está presa em "preparando o Windows. Não desligar o computador "no Azure

Este artigo ajuda você a resolver o problema quando sua VM (máquina virtual) está presa no "preparando o Windows. Não desligue o estágio do computador durante a inicialização.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="symptoms"></a>Sintomas

Quando você usa o **diagnóstico de inicialização** para obter a captura de tela de uma VM, o sistema operacional não é totalmente inicializado. A VM exibe a mensagem "preparando o Windows. Não desligue o computador."

![Exemplo de mensagem para o Windows Server 2012 R2](./media/troubleshoot-vm-configure-update-boot/message1.png)

![Exemplo de mensagem](./media/troubleshoot-vm-configure-update-boot/message2.png)

## <a name="cause"></a>Causa

Normalmente, esse problema ocorre quando o servidor está fazendo a reinicialização final após a alteração da configuração. A alteração de configuração pode ser inicializada por atualizações do Windows ou pelas alterações nas funções/recurso do servidor. Por Windows Update, se o tamanho das atualizações for grande, o sistema operacional precisará de mais tempo para reconfigurar as alterações.

## <a name="back-up-the-os-disk"></a>Fazer backup do disco do sistema operacional

Antes de tentar corrigir o problema, faça backup do disco do sistema operacional.

### <a name="for-vms-with-an-encrypted-disk-you-must-unlock-the-disks-first"></a>Para VMs com um disco criptografado, você deve desbloquear os discos primeiro

Siga estas etapas para determinar se a VM é uma VM criptografada.

1. Na portal do Azure, abra sua VM e, em seguida, navegue até os discos.

2. Examine a coluna **criptografia** para ver se a criptografia está habilitada.

Se o disco do sistema operacional for criptografado, desbloqueie o disco criptografado. Para desbloquear o disco, siga estas etapas.

1. Crie uma VM de recuperação que esteja localizada no mesmo grupo de recursos, conta de armazenamento e local que a VM afetada.

2. No portal do Azure, exclua a VM afetada e mantenha o disco.

3. Execute o PowerShell como administrador.

4. Execute o cmdlet a seguir para obter o nome do segredo.

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

6. Converta o valor codificado em base64 em bytes e grave a saída em um arquivo. 

    > [!Note]
    > Se você usar a opção de desbloqueio de USB, o nome do arquivo BEK deverá corresponder ao GUID original do BEK. Crie uma pasta na unidade C denominada "BEK" antes de seguir estas etapas.
    
    ```Powershell
    New-Item -ItemType directory -Path C:\BEK
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = “c:\BEK\$secretName.BEK”
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7. Depois que o arquivo BEK for criado em seu PC, copie o arquivo para a VM de recuperação à qual o disco do sistema operacional bloqueado está conectado. Execute os comandos a seguir usando o local do arquivo BEK.

    ```Powershell
    manage-bde -status F:
    manage-bde -unlock F: -rk C:\BEKFILENAME.BEK
    ```
    **Opcional**: Em alguns cenários, pode ser necessário descriptografar o disco usando esse comando.
   
    ```Powershell
    manage-bde -off F:
    ```

    > [!Note]
    > O comando anterior pressupõe que o disco para criptografar está na letra F.

8. Se você precisar coletar logs, vá para a letra da **unidade do caminho: \ Windows\System32\winevt\Logs**.

9. Desanexe a unidade do computador de recuperação.

### <a name="create-a-snapshot"></a>Criar um instantâneo

Para criar um instantâneo, siga as etapas em fazer o [instantâneo de um disco](../windows/snapshot-copy-managed-disk.md).

## <a name="collect-an-os-memory-dump"></a>Coletar um despejo de memória do so

Use as etapas na seção [coletar despejo de so](troubleshoot-common-blue-screen-error.md#collect-memory-dump-file) para coletar um despejo de sistema operacional quando a VM estiver presa na configuração.

## <a name="contact-microsoft-support"></a>Contacte o Suporte da Microsoft

Depois de coletar o arquivo de despejo, entre em contato com o [suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para analisar a causa raiz.


## <a name="rebuild-the-vm-by-using-powershell"></a>Recompilar a VM usando o PowerShell

Depois de coletar o arquivo de despejo de memória, siga estas etapas para recompilar a VM.

**Para discos não gerenciados**

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

**Para discos gerenciados**

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
