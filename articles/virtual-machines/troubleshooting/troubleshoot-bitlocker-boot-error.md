---
title: Resolução de problemas de erros de arranque do BitLocker numa VM do Azure | Documentos da Microsoft
description: Saiba como resolver erros de arranque do BitLocker numa VM do Azure
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: v-jesits
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/31/2018
ms.author: genli
ms.openlocfilehash: a7bd812d3aadfd3c358c8d0aeccf0e92ed474a05
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57993212"
---
# <a name="bitlocker-boot-errors-on-an-azure-vm"></a>Erros de arranque do BitLocker numa VM do Azure

 Este artigo descreve os erros de disco BitLocker que ocorrem quando inicia uma máquina virtual de Windows (VM) no Microsoft Azure.

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="symptom"></a>Sintoma

 Uma VM do Windows não iniciar. Quando dá entrada as capturas de ecrã do [diagnósticos de arranque](../windows/boot-diagnostics.md) janela, verá uma das seguintes mensagens de erro:

- Plug-in do controlador USB com a chave do BitLocker

- Está bloqueada! Introduza a chave de recuperação para começar a utilizar novamente (Layout de teclado: Informações de início de sessão dos Estados Unidos) incorretos foi introduzida demasiadas vezes, para que o seu PC foi bloqueado para proteger a sua privacidade. Para obter a chave de recuperação, vá para https://windows.microsoft.com/recoverykeyfaq de outro PC ou dispositivo móvel. Caso precise dela, a chave de ID é XXXXXXX. Em alternativa, pode repor o seu PC.

- Introduza a palavra-passe para desbloquear este [unidade] prima a tecla de inserir para ver a palavra-passe à medida que escreve.
- Introduza a chave de recuperação carga sua chave de recuperação a partir de um dispositivo USB.

## <a name="cause"></a>Causa

Esse problema pode ocorrer se a VM não é possível localizar o ficheiro de chave de recuperação do BitLocker (BEK) para desencriptar o disco criptografado.

## <a name="solution"></a>Solução

Para resolver este problema, parar e desalocar a VM e, em seguida, reiniciá-lo. Esta operação força a VM para recuperar o arquivo BEK a partir do Azure Key Vault e, em seguida, colocá-la no disco encriptado. 

Se esse método faz não a resolver o problema, siga estes passos para restaurar o ficheiro BEK manualmente:

1. Tire um instantâneo do disco de sistema da VM afetado como uma cópia de segurança. Para obter mais informações, consulte [instantâneo de um disco](../windows/snapshot-copy-managed-disk.md).
2. [Anexar o disco do sistema para uma VM de recuperação](troubleshoot-recovery-disks-portal-windows.md) que está encriptada por BitLocker. Isto é necessário para executar o [bde gerir](https://docs.microsoft.com/windows-server/administration/windows-commands/manage-bde) comando que está disponível apenas para a VM encriptada por BitLocker.

    Quando anexa um disco gerido, poderá receber uma mensagem de erro "contém as definições de encriptação e, portanto, não pode ser utilizado como um disco de dados". Nesta situação, execute o script seguinte para tentar novamente para anexar o disco:

    ```Powershell
    $rgName = "myResourceGroup"
    $osDiskName = "ProblemOsDisk"

    New-AzDiskUpdateConfig -EncryptionSettingsEnabled $false |Update-AzDisk -diskName $osDiskName -ResourceGroupName $rgName

    $recoveryVMName = "myRecoveryVM" 
    $recoveryVMRG = "RecoveryVMRG" 
    $OSDisk = Get-AzDisk -ResourceGroupName $rgName -DiskName $osDiskName;

    $vm = get-AzVM -ResourceGroupName $recoveryVMRG -Name $recoveryVMName 

    Add-AzVMDataDisk -VM $vm -Name $osDiskName -ManagedDiskId $osDisk.Id -Caching None -Lun 3 -CreateOption Attach 

    Update-AzVM -VM $vm -ResourceGroupName $recoveryVMRG
    ```
     Não é possível anexar um disco gerido a uma VM que foram restaurada a partir de uma imagem de Blobs.

3. Depois do disco está anexado, fazer uma conexão de área de trabalho remota para a VM de recuperação para que possam executar alguns scripts do PowerShell do Azure. Certifique-se de que tem o [a versão mais recente do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) instalado na VM de recuperação.

4. Abra uma sessão elevada do PowerShell do Azure (executar como administrador). Execute os seguintes comandos para iniciar sessão na subscrição do Azure:

    ```Powershell
    Add-AzAccount -SubscriptionID [SubscriptionID]
    ```

5. Execute o seguinte script para verificar o nome do ficheiro BEK:

    ```powershell
    $vmName = "myVM"
    $vault = "myKeyVault"
    Get-AzureKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq $vmName) -and ($_.ContentType -match 'BEK')} `
            | Sort-Object -Property Created `
            | ft  Created, `
                @{Label="Content Type";Expression={$_.ContentType}}, `
                @{Label ="Volume"; Expression = {$_.Tags.VolumeLetter}}, `
                @{Label ="DiskEncryptionKeyFileName"; Expression = {$_.Tags.DiskEncryptionKeyFileName}}
    ```

    Segue-se o exemplo da saída. Localize o nome do ficheiro BEK para o disco anexado. Neste caso, partimos do princípio de que a letra de unidade de disco do anexado é F e o ficheiro BEK é 6-4637-9F13-7F599C12F85C de EF7B2F5A - 50C. BEK.

    ```
    Created             Content Type Volume DiskEncryptionKeyFileName               
    -------             ------------ ------ -------------------------               
    4/5/2018 7:14:59 PM Wrapped BEK  C:\    B4B3E070-836C-4AF5-AC5B-66F6FDE6A971.BEK
    4/7/2018 7:21:16 PM Wrapped BEK  F:\    EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    4/7/2018 7:26:23 PM Wrapped BEK  G:\    70148178-6FAE-41EC-A05B-3431E6252539.BEK
    4/7/2018 7:26:26 PM Wrapped BEK  H:\    5745719F-4886-4940-9B51-C98AFABE5305.BEK
    ```

    Se vir dois volumes duplicados, o volume que contém o período de tempo mais recente é o arquivo BEK atual utilizada pela VM de recuperação.

    Se o **tipo de conteúdo** valor é **encapsulada BEK**, aceda ao [cenários de chave de encriptação de chaves (KEK)](#key-encryption-key-scenario).

    Agora que tem o nome do ficheiro a BEK para a unidade, tem de criar o nome de ficheiro de segredo. Ficheiro BEK para desbloquear a unidade. 

6.  Transfira o ficheiro BEK para o disco de recuperação. O exemplo a seguir salva o arquivo BEK para a pasta C:\BEK. Certifique-se de que o `C:\BEK\` caminho existe antes de executar os scripts.

    ```powershell
    $vault = "myKeyVault"
    $bek = " EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK"
    $keyVaultSecret = Get-AzureKeyVaultSecret -VaultName $vault -Name $bek
    $bekSecretBase64 = $keyVaultSecret.SecretValueText
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = "C:\BEK\DiskEncryptionKeyFileName.BEK"
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7.  Para desbloquear o disco ligado através do ficheiro BEK, execute o seguinte comando:

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    ```
    Neste exemplo, o disco do SO anexado é unidade F. Certifique-se de que use a letra de unidade correta. 

    - Se o disco foi desbloqueado com êxito utilizando a chave BEK. Pode consideramos o problema de BItLocker que seja resolvido. 

    - Se utilizar a chave BEK não desbloquear o disco, pode utilizar suspender a proteção para desativar temporariamente o BitLocker, executando o seguinte comando
    
        ```powershell
        manage-bde -protectors -disable F: -rc 0
        ```      
    - Se pretender recriar a VM com o disco de dytem, totalmente deve descriptografar a unidade. Para tal, execute o seguinte comando:

        ```powershell
        manage-bde -off F:
        ```
8.  Desanexe o disco da VM de recuperação e, em seguida, voltar a anexar o disco à VM afetado como um disco de sistema. Para obter mais informações, consulte [resolver problemas de uma VM do Windows ao anexar o disco do SO a uma VM de recuperação](troubleshoot-recovery-disks-windows.md).

### <a name="key-encryption-key-scenario"></a>Cenário de chave de encriptação de chave

Para um cenário de chave de encriptação de chave, siga estes passos:

1. Certifique-se de que a conta de utilizador com sessão iniciada requer a permissão "não encapsulada" nas políticas de acesso do Cofre de chave no **utilizador | Permissões da chave | Operações de criptografia | Anular a moldagem de chave**.
2. Guardar os scripts seguintes para um. Arquivo PS1:

    ```powershell
    #Set the Parameters for the script
    param (
            [Parameter(Mandatory=$true)]
            [string] 
            $keyVaultName,
            [Parameter(Mandatory=$true)]
            [string] 
            $kekName,
            [Parameter(Mandatory=$true)]
            [string]
            $secretName,
            [Parameter(Mandatory=$true)]
            [string]
            $bekFilePath,
            [Parameter(Mandatory=$true)]
            [string] 
            $adTenant
            )
    # Load ADAL Assemblies
    $adal = "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Services\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms = "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Services\Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll"
    [System.Reflection.Assembly]::LoadFrom($adal)
    [System.Reflection.Assembly]::LoadFrom($adalforms)

    # Set well-known client ID for AzurePowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2" 
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURI = "https://vault.azure.net"
    # Set Authority to Azure AD Tenant
    $authority = "https://login.windows.net/$adtenant"
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $authResult = $authContext.AcquireToken($resourceAppIdURI, $clientId, $redirectUri, "Auto")
    # Generate auth header 
    $authHeader = $authResult.CreateAuthorizationHeader()
    # Set HTTP request headers to include Authorization header
    $headers = @{'x-ms-version'='2014-08-01';"Authorization" = $authHeader}

    ########################################################################################################################
    # 1. Retrieve wrapped BEK
    # 2. Make KeyVault REST API call to unwrap the BEK
    # 3. Convert the Base64Url string returned by KeyVault unwrap to Base64 string 
    # 4. Convert Base64 string to bytes and write to the BEK file
    ########################################################################################################################

    #Get wrapped BEK and place it in JSON object to send to KeyVault REST API
    $keyVaultSecret = Get-AzureKeyVaultSecret -VaultName $keyVaultName -Name $secretName
    $wrappedBekSecretBase64 = $keyVaultSecret.SecretValueText
    $jsonObject = @"
    {
    "alg": "RSA-OAEP",
    "value" : "$wrappedBekSecretBase64"
    }
    "@

    #Get KEK Url
    $kekUrl = (Get-AzureKeyVaultKey -VaultName $keyVaultName -Name $kekName).Key.Kid;
    $unwrapKeyRequestUrl = $kekUrl+ "/unwrapkey?api-version=2015-06-01";

    #Call KeyVault REST API to Unwrap 
    $result = Invoke-RestMethod -Method POST -Uri $unwrapKeyRequestUrl -Headers $headers -Body $jsonObject -ContentType "application/json" -Debug

    #Convert Base64Url string returned by KeyVault unwrap to Base64 string
    $base64UrlBek = $result.value;
    $base64Bek = $base64UrlBek.Replace('-', '+');
    $base64Bek = $base64Bek.Replace('_', '/');
    if($base64Bek.Length %4 -eq 2)
    {
        $base64Bek+= '==';
    }
    elseif($base64Bek.Length %4 -eq 3)
    {
        $base64Bek+= '=';
    }

    #Convert base64 string to bytes and write to BEK file
    $bekFileBytes = [System.Convert]::FromBase64String($base64Bek);
    [System.IO.File]::WriteAllBytes($bekFilePath,$bekFileBytes)
    ```
3. Defina os parâmetros. O script irá processar o segredo KEK para criar a chave BEK e, em seguida, guarde-o para uma pasta local na VM de recuperação.

4. Quando o script começa, verá a seguinte saída:

        GAC    Version        Location                                                                              
        ---    -------        --------                                                                              
        False  v4.0.30319     C:\Program Files\WindowsPowerShell\Modules\AzureRM.profile\4.0.0\Microsoft.Identity...
        False  v4.0.30319     C:\Program Files\WindowsPowerShell\Modules\AzureRM.profile\4.0.0\Microsoft.Identity...

    Quando o script for concluído, verá a seguinte saída:

        VERBOSE: POST https://myvault.vault.azure.net/keys/rondomkey/<KEY-ID>/unwrapkey?api-
        version=2015-06-01 with -1-byte payload
        VERBOSE: received 360-byte response of content type application/json; charset=utf-8


5. Para desbloquear o disco ligado através do ficheiro BEK, execute o seguinte comando:

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    ```
    Neste exemplo, o disco do SO anexado é unidade F. Certifique-se de que use a letra de unidade correta. 

    - Se o disco foi desbloqueado com êxito utilizando a chave BEK. Pode consideramos o problema de BItLocker que seja resolvido. 

    - Se utilizar a chave BEK não desbloquear o disco, pode utilizar suspender a proteção para desativar temporariamente o BitLocker, executando o seguinte comando
    
        ```powershell
        manage-bde -protectors -disable F: -rc 0
        ```      
    - Se pretender recriar a VM com o disco de dytem, totalmente deve descriptografar a unidade. Para tal, execute o seguinte comando:

        ```powershell
        manage-bde -off F:
        ```

6. Desanexe o disco da VM de recuperação e, em seguida, voltar a anexar o disco à VM afetado como um disco de sistema. Para obter mais informações, consulte [resolver problemas de uma VM do Windows ao anexar o disco do SO a uma VM de recuperação](troubleshoot-recovery-disks-windows.md).
