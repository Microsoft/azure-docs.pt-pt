---
title: Solucionando problemas de erros de inicialização do BitLocker em uma VM do Azure | Microsoft Docs
description: Saiba como solucionar problemas de erros de inicialização do BitLocker em uma VM do Azure
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
ms.date: 03/25/2019
ms.author: genli
ms.openlocfilehash: 27a675982711f8d8f0b36ea0cc2600de45e97a6e
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348453"
---
# <a name="bitlocker-boot-errors-on-an-azure-vm"></a>Erros de inicialização do BitLocker em uma VM do Azure

 Este artigo descreve os erros do BitLocker que você pode enfrentar ao iniciar uma VM (máquina virtual) do Windows no Microsoft Azure.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="symptom"></a>Sintoma

 Uma VM do Windows não é iniciada. Ao verificar as capturas de tela na janela de [diagnóstico de inicialização](../windows/boot-diagnostics.md) , você verá uma das seguintes mensagens de erro:

- Conecte o driver USB que tem a chave do BitLocker

- Você está bloqueado! Insira a chave de recuperação para começar novamente (layout do teclado: EUA) as informações de entrada erradas foram inseridas muitas vezes, portanto, seu PC foi bloqueado para proteger sua privacidade. Para recuperar a chave de recuperação, vá https://windows.microsoft.com/recoverykeyfaq para de outro PC ou dispositivo móvel. Caso você precise dela, a ID da chave é XXXXXXX. Ou então, você pode redefinir seu computador.

- Digite a senha para desbloquear esta unidade [] Pressione a tecla Insert para ver a senha ao digitar.
- Insira sua chave de recuperação carregue sua chave de recuperação de um dispositivo USB.

## <a name="cause"></a>Causa

Esse problema pode ocorrer se a VM não puder localizar o arquivo de chave de recuperação do BitLocker (BEK) para descriptografar o disco criptografado.

## <a name="solution"></a>Solução

Para resolver esse problema, pare e desaloque a VM e, em seguida, reinicie-a. Essa operação força a VM a recuperar o arquivo BEK do Azure Key Vault e, em seguida, colocá-lo no disco criptografado. 

Se esse método não resolver o problema, siga estas etapas para restaurar o arquivo BEK manualmente:

1. Tire um instantâneo do disco do sistema da VM afetada como um backup. Para obter mais informações, consulte [instantâneo de um disco](../windows/snapshot-copy-managed-disk.md).
2. [Anexar o disco do sistema para uma VM de recuperação](troubleshoot-recovery-disks-portal-windows.md). Para executar o comando [Manage-bde](https://docs.microsoft.com/windows-server/administration/windows-commands/manage-bde) na etapa 7, o recurso **criptografia de unidade de disco BitLocker** deve ser habilitado na VM de recuperação.

    Ao anexar um disco gerenciado, você pode receber uma mensagem de erro "contém configurações de criptografia e, portanto, não pode ser usada como um disco de dados". Nessa situação, execute o seguinte script para tentar anexar o disco novamente:

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
     Não é possível anexar um disco gerenciado a uma VM que foi restaurada de uma imagem de BLOB.

3. Depois que o disco for anexado, faça uma conexão de área de trabalho remota para a VM de recuperação para que você possa executar alguns scripts de Azure PowerShell. Verifique se você tem a [versão mais recente do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) instalada na VM de recuperação.

4. Abra uma sessão de Azure PowerShell com privilégios elevados (executar como administrador). Execute os seguintes comandos para entrar na assinatura do Azure:

    ```Powershell
    Add-AzAccount -SubscriptionID [SubscriptionID]
    ```

5. Execute o script a seguir para verificar o nome do arquivo BEK:

    ```powershell
    $vmName = "myVM"
    $vault = "myKeyVault"
    Get-AzKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq $vmName) -and ($_.ContentType -match 'BEK')} `
            | Sort-Object -Property Created `
            | ft  Created, `
                @{Label="Content Type";Expression={$_.ContentType}}, `
                @{Label ="Volume"; Expression = {$_.Tags.VolumeLetter}}, `
                @{Label ="DiskEncryptionKeyFileName"; Expression = {$_.Tags.DiskEncryptionKeyFileName}}
    ```

    A seguir está um exemplo da saída. Localize o nome do arquivo BEK para o disco anexado. Nesse caso, presumimos que a letra da unidade do disco anexado é F e o arquivo BEK é EF7B2F5A-50C6-4637-9F13-7F599C12F85C. Bek.

    ```
    Created             Content Type Volume DiskEncryptionKeyFileName               
    -------             ------------ ------ -------------------------               
    4/5/2018 7:14:59 PM Wrapped BEK  C:\    B4B3E070-836C-4AF5-AC5B-66F6FDE6A971.BEK
    4/7/2018 7:21:16 PM Wrapped BEK  F:\    EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    4/7/2018 7:26:23 PM Wrapped BEK  G:\    70148178-6FAE-41EC-A05B-3431E6252539.BEK
    4/7/2018 7:26:26 PM Wrapped BEK  H:\    5745719F-4886-4940-9B51-C98AFABE5305.BEK
    ```

    Se você vir dois volumes duplicados, o volume que tem o carimbo de data/hora mais recente será o arquivo BEK atual usado pela VM de recuperação.

    Se o valor do **tipo de conteúdo** for **encapsulado Bek**, vá para os cenários de Kek (chave de [criptografia de chave)](#key-encryption-key-scenario).

    Agora que você tem o nome do arquivo BEK para a unidade, você precisa criar o nome de arquivo-segredo. Arquivo BEK para desbloquear a unidade.

6.  Baixe o arquivo BEK no disco de recuperação. O exemplo a seguir salva o arquivo BEK na pasta C:\BEK. Verifique se o `C:\BEK\` caminho existe antes de executar os scripts.

    ```powershell
    $vault = "myKeyVault"
    $bek = " EF7B2F5A-50C6-4637-9F13-7F599C12F85C"
    $keyVaultSecret = Get-AzKeyVaultSecret -VaultName $vault -Name $bek
    $bekSecretBase64 = $keyVaultSecret.SecretValueText
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = "C:\BEK\DiskEncryptionKeyFileName.BEK"
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7.  Para desbloquear o disco anexado usando o arquivo BEK, execute o comando a seguir.

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    ```
    Neste exemplo, o disco do sistema operacional anexado é a unidade F. Certifique-se de usar a letra da unidade correta. 

    - Se o disco foi desbloqueado com êxito usando a chave BEK. Podemos considerar o problema do BitLocker a ser resolvido. 

    - Se usar a chave BEK não desbloquear o disco, você poderá usar suspender a proteção para desativar temporariamente o BitLocker executando o comando a seguir
    
        ```powershell
        manage-bde -protectors -disable F: -rc 0
        ```      
    - Se você pretende recriar a VM usando o disco dytem, deverá descriptografar completamente a unidade. Para tal, execute o seguinte comando:

        ```powershell
        manage-bde -off F:
        ```
8.  Desanexe o disco da VM de recuperação e anexe novamente o disco à VM afetada como um disco do sistema. Para obter mais informações, consulte [solucionar problemas de uma VM do Windows anexando o disco do sistema operacional a uma VM de recuperação](troubleshoot-recovery-disks-windows.md).

### <a name="key-encryption-key-scenario"></a>Cenário de chave de criptografia de chave

Para um cenário de chave de criptografia de chave, siga estas etapas:

1. Verifique se a conta de usuário conectada requer a permissão "desencapsulada" nas políticas de acesso de Key Vault no **usuário | Permissões de chave | Operações criptográficas | Desencapsular chave**.
2. Salve os scripts a seguir em um. Arquivo PS1:

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
    # Load ADAL Assemblies. The following script assumes that the Azure PowerShell version you installed is 1.0.0. 
    $adal = "${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts\1.0.0\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms = "${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts\1.0.0\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.Platform.dll"
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
    $authResult = $authContext.AcquireTokenAsync($resourceAppIdURI, $clientId, $redirectUri, $platformParameters).result
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
3. Defina os parâmetros. O script processará o segredo KEK para criar a chave BEK e, em seguida, o salvará em uma pasta local na VM de recuperação.

4. Você verá a seguinte saída quando o script começar:

        GAC    Version        Location                                                                              
        ---    -------        --------                                                                              
        False  v4.0.30319     C:\Program Files\WindowsPowerShell\Modules\Az.Accounts\...
        False  v4.0.30319     C:\Program Files\WindowsPowerShell\Modules\Az.Accounts\...

    Quando o script for concluído, você verá a seguinte saída:

        VERBOSE: POST https://myvault.vault.azure.net/keys/rondomkey/<KEY-ID>/unwrapkey?api-
        version=2015-06-01 with -1-byte payload
        VERBOSE: received 360-byte response of content type application/json; charset=utf-8


5. Para desbloquear o disco anexado usando o arquivo BEK, execute o seguinte comando:

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    ```
    Neste exemplo, o disco do sistema operacional anexado é a unidade F. Certifique-se de usar a letra da unidade correta. 

    - Se o disco foi desbloqueado com êxito usando a chave BEK. Podemos considerar o problema do BitLocker a ser resolvido. 

    - Se usar a chave BEK não desbloquear o disco, você poderá usar suspender a proteção para desativar temporariamente o BitLocker executando o comando a seguir
    
        ```powershell
        manage-bde -protectors -disable F: -rc 0
        ```      
    - Se você pretende recriar a VM usando o disco dytem, deverá descriptografar completamente a unidade. Para tal, execute o seguinte comando:

        ```powershell
        manage-bde -off F:
        ```

6. Desanexe o disco da VM de recuperação e anexe novamente o disco à VM afetada como um disco do sistema. Para obter mais informações, consulte [solucionar problemas de uma VM do Windows anexando o disco do sistema operacional a uma VM de recuperação](troubleshoot-recovery-disks-windows.md).
